# Balance – personal nutrition, activity and progress tracker

Native Android app (Kotlin, Jetpack Compose, Room) for a Samsung Galaxy phone.
This is **Stage 1 of 5**. Everything is stored on the phone; there is no account and no server.

## What Stage 1 contains

- **Today** dashboard: calorie balance, food / total burned / active, protein, fibre, carbs, fat,
  steps, workouts, this week's balance chart, current weight and 7-day trend.
- **Food diary**: add, edit, delete; portion and unit; four meals; date and time; "My foods";
  saved meals; copy a previous day's meals.
- **Activities**: all twelve categories, several per day, duration, optional calories, notes.
- **Calories burned and steps**: manual entry of total burned, active calories and steps per day.
- **Calendar**: week (Mon–Sun) and month views with workout badges, food calories, balance,
  protein and fibre; tap a date to open its diary; week and month summaries.
- **Settings**: editable targets (protein 155 g, carbs 185 g, fat 60 g, fibre 30 g to start;
  calories and steps blank until you set them), optional separate rest-day targets, change history.

## The calorie rules the app enforces

- Daily balance = **total calories burned − food calories**. Positive = deficit, negative = surplus.
- Active calories are shown separately and are **never added** to the total.
- If total burned is missing the balance is **"not available"**; active calories are never used instead.
- A day with no food logged, or no burn figure, is **incomplete** and is left out of weekly and
  monthly totals and averages. It is never counted as zero.
- A manual value overrides a synced one, but the synced figure is kept alongside it.

These rules live in the `domain` module and are covered by unit tests
(`domain/src/test/.../CalculationsTest.kt`).

## Project layout

```
domain/   Pure Kotlin. Calculations, models, calendar helpers. No Android code.
app/      Android app.
  data/     Room database, DAO, Repository (the single source of truth)
  ui/       Compose screens
  health/   (Stage 3) Health Connect and Samsung Health adapters
  widget/   (Stage 4) Home-screen widgets
```

## How to get the app onto your phone

### Option A – no computer setup: build in the cloud with GitHub

1. Create a free GitHub account and a new **private** repository.
2. Upload the contents of this folder to it (keep the `.github` folder).
3. Open the repository's **Actions** tab. The "Build debug APK" job runs automatically (about 5 minutes).
4. Open the finished run and download **balance-debug-apk**. Unzip it to get `app-debug.apk`.
5. Send the APK to your phone (Drive, email or USB) and tap it. Allow "Install unknown apps" when
   Android asks. Play Protect may warn that the developer is unknown; that is expected for a
   personal build.

### Option B – Android Studio

1. Install Android Studio (Narwhal 2025.1 or newer).
2. **File > Open** and choose this folder. Let Gradle sync finish.
3. On the phone: Settings > About phone > Software information > tap **Build number** seven times,
   then Settings > Developer options > turn on **USB debugging**.
4. Connect the phone by USB and press **Run**.

Command line: `./gradlew :domain:test :app:assembleDebug`
(APK: `app/build/outputs/apk/debug/app-debug.apk`). Requires JDK 17.

## What has and has not been tested

| Item | Status |
|---|---|
| Calculation rules (`domain`) | **Compiled and unit-tested: 13 tests pass.** |
| Database and repository code | Type-checked against stand-in Room definitions. Not run on a device. |
| Screens (Compose UI) | Checked for syntax and imports only. **Not compiled against the Android SDK and not run.** |
| Full Gradle build / APK | **Not built.** The environment it was written in cannot reach Google's Android servers. |
| Samsung phone / One UI | **Not tested.** |

Expect the first real build to surface a few compile errors in the screen code. Send the error
text back and they can be fixed quickly.

## Samsung Health and Health Connect (Stage 3) – what will be needed

Checked against the official documentation in November 2026:

- The old "Samsung Health SDK for Android" is deprecated and is not used.
- **Health Connect** (the default route): no registration needed for a personal app. In Samsung
  Health, turn on Settings > Health Connect and allow sharing. Samsung documents steps, exercise
  sessions and exercise calories as synced; a whole-day total-burn figure is not listed, so it
  may not be available by this route.
- **Samsung Health Data SDK** (optional route for the whole-day "calories burned" and "active
  calories" figures, via its Activity Summary data type):
  - Download the SDK `.aar` from developer.samsung.com (free Samsung account).
  - Turn on Samsung Health's developer mode: Samsung Health > Settings > About Samsung Health >
    tap the version line 10 times > Developer mode (Samsung Health Data SDK) > Developer Mode for Data Read.
  - Reading in developer mode needs **no partner approval**. Publishing the app to other people,
    or writing data to Samsung Health, does.
- Neither route will be described as working until it has been run on your phone.

## Roadmap

- Stage 2: weekly and monthly reports, weight history and charts, CSV and PDF export.
- Stage 3: Health Connect and Samsung Health sync, permissions screen, Sync now, background sync.
- Stage 4: three home-screen widgets (week, month, today).
- Stage 5: backup and restore, testing and fixes.
