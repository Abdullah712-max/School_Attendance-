# School Attendance (Android, Kotlin + Jetpack Compose + Room)

Offline school attendance manager: students, daily attendance, absence tracking,
dashboard, PDF/CSV reports, backup/restore, secure admin login.

> STATUS: the source was written without access to an Android SDK, so it has NOT been
> compiled or run yet. The first build may show a small compile error that needs a
> one-line fix. Send me the error text and I will fix it.

---------------------------------------------------------------------------
## 1. Build the APK (pick ONE)

### Option A: Android Studio (most reliable)
1. Install Android Studio (free): https://developer.android.com/studio
2. Unzip this project. In Android Studio choose File > Open and select the `SchoolAttendance` folder.
3. Wait for "Gradle sync" to finish (first time downloads about 1 GB; internet needed).
   If asked to install Android SDK Platform 34, click Install/Accept.
4. Menu: Build > Build Bundle(s) / APK(s) > Build APK(s).
5. When the popup appears click "locate". The file is:
   `app/build/outputs/apk/debug/app-debug.apk`

### Option B: No installation (GitHub builds it for you)
1. Create a free GitHub account, create a new repository, upload ALL files of this
   project (including the hidden `.github` folder).
2. Open the repository > Actions tab > "Build APK" > Run workflow.
3. When it finishes (about 5 minutes) open the run and download the artifact
   `SchoolAttendance-APK` (a zip containing `app-debug.apk`).

### Option C: Command line (needs JDK 17 + Android SDK + Gradle 8.9)
```
gradle assembleDebug
```
Output: `app/build/outputs/apk/debug/app-debug.apk`

---------------------------------------------------------------------------
## 2. Install on your Android phone
1. Copy `app-debug.apk` to the phone (USB cable, Google Drive, WhatsApp to yourself, email).
2. Open the file on the phone (Files app > tap the APK).
3. Android asks to allow "Install unknown apps" for the app you opened it from.
   Tap Settings, switch Allow on, go back, tap Install.
4. Open "School Attendance". On first launch you create the admin username and
   password. There is NO default password, and no recovery, so write it down.

---------------------------------------------------------------------------
## 3. Using the app
* Students tab: + to add; pencil to edit; bin to delete; search by name or roll number.
  Tap a student for full history, totals, percentage, absent streaks and CSV/PDF export.
* Attendance tab: choose class, section, date; tap Present or Absent per student; Save.
  Re-open the same class and date to correct it; saving updates the records (never duplicates).
  Future dates are blocked.
* Home tab: totals, present/absent today, overall %, students with most absences,
  and frequent absentees (below 75% attendance with at least 5 recorded days, or 3+ absences in a row).
* Reports tab: Daily / Weekly (Mon-Sun) / Monthly / Full history, for all students,
  one class-section, or one student. Export CSV or PDF, then choose where to save it.
* "Absent in a row" counts consecutive recorded school days (days with no attendance
  recorded, such as weekends, are skipped).

---------------------------------------------------------------------------
## 4. Backup and restore
Data lives only on the phone. Back up regularly, and ALWAYS before updating,
reinstalling, or changing phones.

Backup: Settings > Create backup > pick a place (Downloads, Google Drive, USB) > save.
Restore: Settings > Restore from backup > pick the .json file > confirm.
         Restore REPLACES all current data. The password is not part of the backup.
Moving to a new phone: install the APK, create an admin account, then Restore.
Backup files contain student and parent details. Keep them private.

---------------------------------------------------------------------------
## 5. Building a new APK after changes
Edit the code (or ask me), then repeat Option A, B or C. To release an update:
raise `versionCode` (+1) and `versionName` in `app/build.gradle.kts`.

IMPORTANT: an update installs over the old app and keeps data ONLY if both APKs are
signed with the same key. Debug APKs use a key that is auto-created on each computer,
so APKs built on a different computer (or GitHub) cannot update each other: Android
would force an uninstall, which deletes the data. Back up first, or use your own key:

### Signing with your own key (recommended for real use)
1. Create a key once (keep the file and passwords safe, and never lose them):
   `keytool -genkey -v -keystore school.jks -keyalg RSA -keysize 2048 -validity 10000 -alias school`
2. Create `keystore.properties` in the project root:
   ```
   storeFile=school.jks
   storePassword=YOUR_STORE_PASSWORD
   keyAlias=school
   keyPassword=YOUR_KEY_PASSWORD
   ```
3. Build: `gradle assembleRelease`  ->  `app/build/outputs/apk/release/app-release.apk`
   (signed, installable). Android Studio: Build > Generate Signed App Bundle / APK > APK.
(`keystore.properties` and `*.jks` are git-ignored; do not upload them to GitHub.)

---------------------------------------------------------------------------
## 6. Security notes
* Admin credentials: salted PBKDF2-HMAC-SHA256 (120,000 iterations); no hardcoded passwords.
  5 wrong logins locks sign-in for 30 seconds. The app asks for login on every fresh start.
* Android's own cloud auto-backup is disabled; use the in-app backup instead.
* The attendance database itself is not encrypted on disk (it is protected by Android's
  app sandbox and your phone's screen lock). Use a phone lock screen.
* Forgot the password? It cannot be recovered. Clearing the app data resets it,
  but also erases all records, so restore from a backup afterwards.

## 7. Project layout
```
app/src/main/java/com/school/attendance/
  MainActivity.kt, AppViewModel.kt
  data/      Room entities, DAOs, database
  logic/     statistics, report builder, CSV/PDF exporter
  security/  admin login (hashing, lockout)
  ui/        Compose screens (dashboard, students, attendance, reports, settings)
```
Requirements: Android 8.0 (API 26) or newer. Phones and tablets (tablet gets a side navigation rail).
