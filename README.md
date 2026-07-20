  # pyswisseph Android wheel builder

Cross-compiles a `pyswisseph` Android wheel (arm64-v8a + x86_64) on GitHub's
Linux runners via cibuildwheel, so the KP Stellar Astrology Android app can run
the Swiss Ephemeris engine fully offline on-device (Chaquopy).

## How to use

1. Create a new GitHub repo (e.g. `pyswisseph-android-wheel`).
2. Put this folder's contents in it (keep the `.github/workflows/` path).
3. Push to GitHub.
4. On GitHub: **Actions** tab → **"Build pyswisseph Android wheel"** → **Run workflow**.
5. When it finishes (green check), open the run → **Artifacts** →
   download **`pyswisseph-android-wheels`** (a zip of the `.whl` files).

## Then (in the Android app)

- Unzip the wheels into `app/wheels/` in the Android project.
- In `app/build.gradle.kts`, the `chaquopy` block becomes:

  ```kotlin
  chaquopy {
      defaultConfig {
          version = "3.13"     // must match the wheel's cp313 tag
          pip {
              options("--find-links", "wheels")
              install("pyswisseph==2.10.3.2")
          }
      }
  }
  ```

## Notes / tuning

- If the first run builds only one architecture, we add the Android arch
  selector (arm64-v8a + x86_64) to the workflow env and re-run.
- If cibuildwheel needs the NDK/SDK path explicitly, we set `ANDROID_HOME` in
  the workflow (the runner has the SDK; we point cibuildwheel at it).
- Re-run this workflow whenever pyswisseph or the Python version changes.