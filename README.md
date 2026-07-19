# Gesture Vault (Android)

Draw a custom shape on screen → the matching app launches. Each installed app can
have its own gesture, recorded and recognized using Android's built-in gesture API
(`android.gesture`).

## How it works

- **MainActivity** — lists your installed apps, lets you record a gesture per app.
- **RecordGestureActivity** — full-screen drawing surface; draw the same shape
  3–4 times for better accuracy, then tap Done. Gestures are saved to a private
  file (`GestureLibraries.fromPrivateFile`) inside the app's own storage —
  nothing leaves the device.
- **TriggerService** — a foreground service that shows a small floating bubble
  (like Messenger chat heads) on top of everything. Tap the bubble, draw your
  sign anywhere on screen, and it recognizes which app you meant and launches
  it directly via `Intent`.
- The bubble can be dragged around; tapping (not dragging) opens the gesture
  capture layer.
- "Hide App Icon" disables the launcher icon via an `activity-alias`, while the
  real activity stays intact — the bubble is then the only way back in.

## Setup

1. Open this folder in **Android Studio** (Giraffe or newer). It will
   auto-generate the Gradle wrapper on first sync — if it doesn't, run
   `gradle wrapper` once with a local Gradle install, or let Android Studio's
   "Sync Now" prompt handle it.
2. Connect a real device or start an emulator (**API 26+**).
3. Run the app.
4. In-app: tap **Overlay Permission** → grant "Display over other apps" for
   Gesture Vault in the system settings screen that opens.
5. Record a gesture for a couple of apps from the list.
6. Tap **Start Bubble** — a small circle appears on your home screen.
7. Tap the bubble anywhere and draw one of your saved signs — the target app
   opens.

## Known limitations / things to be aware of

- **Overlay permission (`SYSTEM_ALERT_WINDOW`) is a "special" permission** —
  Android won't show a normal runtime dialog for it; the user has to flip it
  on manually in Settings, which the app deep-links to.
- **Foreground service notification is required** to keep the bubble alive —
  Android will kill background services otherwise. The notification is set to
  minimum importance but can't be fully hidden; this is an OS requirement, not
  a bug.
- **Play Store policy**: apps that primarily exist to hide their own icon and
  run persistent overlays get extra scrutiny during review (this pattern is
  associated with stalkerware). If you ever plan to publish this, be ready to
  justify the use case (personal privacy / productivity) in the listing, and
  expect Google to ask questions. For personal use / sideloading, none of this
  matters.
- **Accuracy** improves a lot if you record each gesture 3–4 times with some
  natural variation, and if the shapes you choose are clearly different from
  each other (a circle vs a zigzag works much better than two similar squiggles).
- `matchThreshold` in `TriggerService.kt` (currently `2.2`) controls how strict
  matching is — lower it if real gestures keep getting rejected, raise it if
  wrong apps keep opening.
