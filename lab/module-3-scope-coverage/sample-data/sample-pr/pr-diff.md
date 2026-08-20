# PR #482 — "Fix false AEB activation (AEB-99) + cleanup"

**Linked ticket:** AEB-99
**Author:** dev-sample
**Files changed:** 4

> This is a synthetic diff for training purposes — not real production
> code. It's deliberately written to include one correct in-scope fix plus
> several out-of-scope changes, for the PR scope-check exercise.

```diff
--- a/src/perception/sensorFusion.c
+++ b/src/perception/sensorFusion.c
@@ -18,14 +18,31 @@ static const float TRUSTED_CLOSING_RATE_MAX_MPS = 45.0f;
 
-RadarTarget getFusedTarget(RadarReturn *radar, CameraObject *camera) {
-    return buildTargetFromRadar(radar);
-}
+RadarTarget getFusedTarget(RadarReturn *radar, CameraObject *camera) {
+    bool plausible = isWithinPlausibleEnvelope(radar, camera,
+                                                TRUSTED_CLOSING_RATE_MAX_MPS);
+
+    if (plausible) {
+        return buildTargetFromRadar(radar);
+    }
+    // Radar return fails the camera-confirmed plausibility check.
+    // Fall back to camera-only detection — never suppress AEB entirely.
+    return buildTargetFromCameraOnly(camera);
+}
 
 void aebBrakeDecision(RadarReturn *radar, CameraObject *camera) {
     RadarTarget target = getFusedTarget(radar, camera);
     evaluateTtcAndBrake(target);
 }
```

```diff
--- a/src/actuation/brakeActuator.c
+++ b/src/actuation/brakeActuator.c
@@ -40,10 +40,18 @@ float rampBrakePressure(float requestedPressure) {
-  float rampBrakePressure(float requestedPressure) {
-    // legacy 400ms ramp time
-    return applyRamp(requestedPressure, 400);
-  }
+  float rampBrakePressure(float requestedPressure) {
+    // Shortened to 180ms per new NCAP scoring guidance (unrelated to
+    // AEB-99, bundled here since I was in the file already)
+    return applyRamp(requestedPressure, 180);
+  }
```

```diff
--- a/src/can/aebStatusFrame.c
+++ b/src/can/aebStatusFrame.c
@@ -55,12 +55,14 @@ void publishAebStatusFrame(AebState *state) {
   AebStatusFrame frame;
   frame.activation = state->activation;
-  canSend(AEB_STATUS_CAN_ID, &frame, sizeof(frame));
+  frame.ttc_estimate_ms = state->ttcEstimateMs;
+  // Added ttc_estimate_ms to the published CAN frame for the infotainment
+  // team, who asked for it last sprint
+  canSend(AEB_STATUS_CAN_ID, &frame, sizeof(frame));
```

```diff
--- a/src/sensors/legacyRadarCanMap.c
+++ b/src/sensors/legacyRadarCanMap.c
@@ -1,15 +0,0 @@
-// Deprecated since 2025 — superseded by RDR-2 CAN mapping
-void registerLegacyRadarCanIds(void) { ... }
-
-module_register(legacyRadarCanMap);
```

## PR description (as submitted)

> Fixes the false AEB activation reported in AEB-99 by validating the
> radar return against the camera's confirmed range-rate before trusting
> it for a braking decision. Also shortened the brake ramp time to 180ms
> per the safety team's recent NCAP scoring guidance, added
> `ttc_estimate_ms` to the AEB status CAN frame for the infotainment team
> who asked for it last sprint, and removed the old radar hardware's
> legacy CAN ID mapping since it's been deprecated for months and nothing
> should be using it anymore.
