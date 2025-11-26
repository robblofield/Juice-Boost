# Juice Boost

## Expressive Motion: Procedural Squash and Stretch for Characters

---

# Series Mission Statement

Juice Boost delivers short, practical sessions under one hour, each focused on adding high-impact polish to any prototype.
Every workshop introduces a standalone, modular technique that improves game feel without complicated systems or animation pipelines.

---

# Session Mission Statement

In this session, we will implement procedural squash and stretch to add expressive motion to a character without using animation clips.
Using transform manipulation and simple sine wave functions, we will prototype:

* Idle breathing motion
* Movement wobble
* Bounce and tilt effects

By the end, you will have a reusable component that can be dropped into any prototype to enhance character personality.

---

# Project Setup

### 1. New Project

Use a 3D URP template.

### 2. Download Starter Package

Teaching_CorePlayer_3D
Available on GitHub or your Moodle session page.

### 3. Install the Package

Drag the .unitypackage into Unity
or
Use Assets > Import Package > Custom Package.

The package includes:

* Basic PlayerMovement script using the New Input System
* A simple test character mesh
* A pre-made scene with input bindings

---

# 1. Create the Script and Attach It

### Goal

Attach the new procedural motion script to the player.

### Instructions

* Create file: PlayerBouncy.cs
* Add it to the same GameObject that has PlayerMovement

Hierarchy example:

```
Player
    PlayerMovement
    PlayerBouncy
```

---

# 2. Add Comment-Based Planning (Scaffolding)

Add this structure before writing logic.
This helps plan the behaviour and makes the code easier to follow.

```csharp
using UnityEngine;

public class PlayerBouncy : MonoBehaviour
{
    // 1. Idle Stretch (Breathing)
    //    - Use Mathf.Sin to generate a repeating motion.
    //    - Apply this to character scale along the Y axis.

    // 2. Moving Shuffle (Side-to-Side Wobble)
    //    - Detect player movement using PlayerMovement.
    //    - Use a faster sine wave for tilt and bounce.

    // 3. State Switching
    //    - If idle, run idle breathing.
    //    - If moving, run moving wobble.

    // 4. Expose inspector values for tuning.

    void Start()
    {
        // TODO: Cache original scale.
    }

    void Update()
    {
        // TODO: Check if idle or moving.
        // TODO: Run appropriate behaviour.
    }
}
```

---

# 3. Proof of Concept: Sine Wave Test

Before changing mesh scale, confirm the sine wave works.

### Add these fields:

```csharp
[Header("Idle Stretch (Breathing Effect)")]
public float idleBounceSpeed = 0.5f;
public float idleBounceHeight = 0.02f;

private Vector3 originalScale;
```

### Add this inside Update():

```csharp
void Update()
{
    // A sine wave between -1 and 1
    float raw = Mathf.Sin(Time.time * idleBounceSpeed);

    // Scaled to a small offset
    float bounce = raw * idleBounceHeight;

    // Proof of concept
    Debug.Log("Idle bounce value: " + bounce);
}
```

Expected result:
Console shows values smoothly oscillating between positive and negative.

---

# 4. Apply Sine Wave to Idle Breathing

Replace the logging with actual scale manipulation.

### In Start():

```csharp
void Start()
{
    originalScale = transform.localScale;
}
```

### In Update():

```csharp
void Update()
{
    float bounce = Mathf.Sin(Time.time * idleBounceSpeed) * idleBounceHeight;

    transform.localScale = new Vector3(
        originalScale.x,
        originalScale.y + bounce,
        originalScale.z
    );
}
```

Expected result:
Your character now has a gentle breathing effect.

---

# 5. Tune Idle Values in Inspector

No code here.
Students should experiment with the parameters.

Suggested values:

* idleBounceSpeed: 0.2 for slow breathing, 1.0 for fast wobble
* idleBounceHeight: 0.01 for subtle, 0.1 for exaggerated

Goal: understand how tuning small values can drastically change feel.

---

# 6. Detect Player Movement

Now integrate with PlayerMovement so we can switch states.

### Add reference at top:

```csharp
[Header("References")]
public PlayerMovement playerMovement;
```

### Replace Update() with state detection scaffold:

```csharp
void Update()
{
    if (playerMovement == null)
    {
        Debug.LogWarning("PlayerMovement reference missing.");
        RunIdleBreathing();
        return;
    }

    bool isMoving = playerMovement.CurrentMoveInput.sqrMagnitude > 0.01f;

    if (!isMoving)
    {
        RunIdleBreathing();
    }
    else
    {
        Debug.Log("Player is moving (shuffle incoming).");
        RunIdleBreathing(); // placeholder for testing
    }
}
```

### Add idle helper:

```csharp
private void RunIdleBreathing()
{
    float bounce = Mathf.Sin(Time.time * idleBounceSpeed) * idleBounceHeight;

    transform.localScale = new Vector3(
        originalScale.x,
        originalScale.y + bounce,
        originalScale.z
    );
}
```

Expected result:

* Logs appear when moving.
* Idle breathing still works.

---

# 7. Implement Moving Shuffle (Tilt and Bounce)

### Add moving parameters:

```csharp
[Header("Moving Shuffle (Cartoon Hop Side-to-Side)")]
public float moveShuffleSpeed = 6f;
public float moveShuffleAngle = 10f;
public float moveBounceHeight = 0.05f;
```

### Update the state-switch logic:

```csharp
void Update()
{
    bool isMoving = playerMovement.CurrentMoveInput.sqrMagnitude > 0.01f;

    if (!isMoving)
    {
        RunIdleBreathing();

        // Reset side tilt but keep current facing direction
        transform.rotation = Quaternion.Euler(
            0,
            transform.rotation.eulerAngles.y,
            0
        );
    }
    else
    {
        RunMovingShuffle();
    }
}
```

### Add moving shuffle method:

```csharp
private void RunMovingShuffle()
{
    float shuffle = Mathf.Sin(Time.time * moveShuffleSpeed) * moveShuffleAngle;
    float bounce = Mathf.Sin(Time.time * moveShuffleSpeed) * moveBounceHeight;

    // Bounce in scale
    transform.localScale = new Vector3(
        originalScale.x,
        originalScale.y + bounce,
        originalScale.z
    );

    // Side-to-side tilt (Z axis)
    transform.rotation = Quaternion.Euler(
        0,
        transform.rotation.eulerAngles.y,
        shuffle
    );
}
```

Expected outcome:

* Character tilts left and right while moving
* Character bounces vertically
* Idle breathing remains smooth when not moving

---

# 8. Optional Part 2 (Further Enhancements)

This session can be extended in a second part if desired.

Possible additions:

* Scale wobble intensity based on movement magnitude
* Add landing squash using grounded detection
* Use Animation Curves for custom motion shapes
* Introduce bobbing movement and positional offsets
* Create unique wobble presets for different characters or enemies

---

# End of Session

You now have a procedural squash and stretch system that:

* Requires no animations
* Works with any mesh or prototype
* Is fully tunable through the Inspector
* Adds personality instantly with minimal code
