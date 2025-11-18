---
mode: agent
---
## Update Android versionCode & versionName in build.gradle

When releasing a new Android build, update the module-level `build.gradle` (or `build.gradle.kts`) to increment the following properties:

- `versionCode` (integer)
- `versionName` (string)

Steps:

1. Open your module-level `build.gradle` (usually `app/build.gradle`).
2. Locate `versionCode` and `versionName` inside the `android` -> `defaultConfig` block.
3. Increment `versionCode` by the desired amount (must be an integer and greater than the previous value).
4. Update `versionName` to the new human-readable version string.
5. Sync Gradle / rebuild the project.

Example

Before:

```
versionCode 5360
versionName '5.36'
```

After:

```
versionCode 5370
versionName '5.37'
```

Notes

- `versionCode` must always increase for Play Store uploads.
- `versionName` is for display and can follow your team's formatting (e.g., `5.37`, `v5.37`, etc.).
- If you use CI or a release script, consider automating these bumps to avoid manual mistakes.

— End of note —
