# MasjidTime - Configuration & User Guide

**Version:** 1.4.2 | **Platforms:** Windows and Linux  
**Cloud S/ntax** | Support: [support@cloudsyntax.co.uk](mailto:support@cloudsyntax.co.uk)

> This guide was checked against the v1.4.2 Python development source. It describes controls and behaviours implemented in that version. The Quick Start is intentionally short enough to occupy approximately one printed page; Markdown itself does not guarantee page breaks.

## Part 1 - Quick Start (one-page reference)

1. **Install and activate.** Follow the platform installation instructions and import the device-specific `.lic` file when prompted. For a licence, email [support@cloudsyntax.co.uk](mailto:support@cloudsyntax.co.uk?subject=MasjidTime%20Licence%20Request) with the Device ID shown in the app.
2. **Open Settings.** Select the settings cog. Open **Location & Calculation**.
3. **Add your masjid.** Edit the **Primary** masjid, enter its name and optional city/location, and select an **Iqamah timetable** source: **Local file**, **Remote URL**, or **Jumu'ah only (no timetable)**. Use **Manage Masjid Library…** to store additional masajid.
4. **Set your location.** Select **Advanced…** to configure latitude, longitude, timezone and elevation. Use its test/preview tools to check prayer entry times.
5. **Choose a calculation method.** Select the appropriate method under **Location & Calculation**. If using **Custom**, configure **Custom Entry Times** and select per-masjid entry files or a shared entry CSV.
6. **Set Iqamah and Jumu'ah.** Supply an Iqamah CSV for each applicable masjid. Set optional **Weekly Jumu'ah** fallback times. Use **Exceptions…** for one-off date-specific changes.
7. **Choose your display.** Under **General** and **Display Settings**, select theme, colour palette, fonts, panes and startup preferences. Under **Content**, manage notices and export a CSV template.
8. **Save and verify.** Save settings and confirm the displayed masjid, prayer entry times, Iqamah times, Jumu'ah slots and date. If a timetable is unavailable, check the footer and timetable source.

**Important:** Calculated *entry times* and scheduled *Iqamah times* are separate. A prayer calculation method does not automatically create congregation times.

---

## Part 2 - Full User Manual

### 1. Settings and navigation

Open **Settings** using the cog icon. The settings dialog has these tabs: **General**, **Display Settings**, **Location & Calculation**, **Content** and **About**. Individual masjid editors and nested screens include **Save and Previous** and **Save and Exit Settings** options. The application stores its configuration in `config/settings.json` in the application data layout; do not manually edit this file unless directed by support.

![General settings](../screenshots/Settings%20-%20General.png)

### 2. Masjid setup and library

Open **Location & Calculation** and select the **Primary**, **Secondary** or **Tertiary** masjid. The Primary slot is the principal displayed masjid. Additional masajid can be kept in **Manage Masjid Library…** independently of the three display slots, then assigned to a display position.

Each masjid editor provides **Masjid name**, optional **City / location**, **Iqamah timetable** source, and **Weekly Jumu'ah (optional)** times. For the timetable source choose:

- **Local file** - browse to an Iqamah CSV stored on the computer or a locally synced folder.
- **Remote URL** - enter a downloadable CSV URL, select a refresh interval (5-1440 minutes) and use **Validate Remote**.
- **Jumu'ah only (no timetable)** - for a masjid where only the weekly Jumu'ah schedule is required.

Remote downloads are validated before replacing the last-known-good cached timetable. A failed refresh does not automatically erase a previously valid cache.

![Location and masajid](../screenshots/Settings%20-%20Location%20Calculation%20and%20Masajid.png)

### 3. Location and prayer calculations

Under **Location & Calculation**, select **Advanced…** to configure or search for geographic coordinates, timezone and other location details. The calculation method controls **prayer entry times**, not Iqamah schedules.

The available methods in v1.4.2 are **MWL**, **ISNA**, **Egypt**, **Karachi**, **UmmAlQura**, **Moonsighting**, **Wifaqul Ulama**, **Wifaqul Ulama (Custom)** and **Custom**. Asr madhhab choices are **Shafi**, **Hanafi** and **Hanafi & Shafi**. The dual option displays both Asr entry times while retaining one congregation time.

The **High latitude rule** options depend on the calculation method. For generic methods they are **None**, **Middle of Night**, **Seventh of Night** and **Twilight Angle**. Wifaqul Ulama methods instead offer **Nisful-Layl (Middle of Night)** and **Aqrabul-Ayyam (Last Valid 18°)**, alongside relevant Wifaqul Ulama Isha/Haraj and Maghrib controls.

**Custom entry times:** Choosing **Custom** enables **Custom Entry Times**. With **Use entry times per Masjid** selected, each masjid uses its own custom entry timetable; otherwise select a **Shared entry CSV**. **Repeat entry timetable annually** allows the dates to recur by day/month. Custom entry times are daily lookups: a missing date or blank entry does **not** inherit yesterday's entry time, and the display uses `--:--` for missing values. An exact date match takes precedence over another year's same month/day when annual repetition is enabled.

### 4. Iqamah CSV timetables

An Iqamah timetable is configured **per masjid**, in the masjid editor, not globally on the Content tab. Under **Content**, **Export CSV Timetable Template...** generates a dated template (v1.4.2 uses a 2028 leap-year template with 366 dates).

![Example Iqamah CSV](../screenshots/Excel%20Iqamah%20Times.png)

The timetable requires a `date` header. Supported dates include `YYYY-MM-DD` and UK-style `DD/MM/YYYY` (also `DD-MM-YYYY` and `YYYY/MM/DD`). US `MM/DD/YYYY` is not intentionally interpreted. CSV header matching is case-insensitive.

For **Fajr, Dhuhr, Asr and Isha**, a dated row changes the effective Iqamah time; a blank value can retain the most recent earlier non-blank value for that prayer. These dated changes do **not** automatically repeat annually. **Maghrib Iqamah** is calculated from Maghrib entry plus the configured `maghrib_offset` (or `maghrib_offset_min`). Jumu'ah values have separate rules described below.

**Do not confuse the two timetable types:** a Custom *entry-time* CSV uses exact daily values (or annual day/month matching); an *Iqamah* CSV uses dated change-points and selective inheritance.

### 5. One-off Iqamah exceptions

In the relevant masjid editor, select **Exceptions…** to manage date-specific overrides. Exceptions belong to the individual masjid. They can override **Fajr, Dhuhr, Asr and Isha** Iqamah times and **Maghrib offset** on a selected date. These are one-off changes, not general changes to the base timetable.

![Adding exceptions](../screenshots/Settings%20-%20Adding%20Exceptions.png)

### 6. Jumu'ah configuration

In the masjid editor, **Weekly Jumu'ah (optional)** accepts up to three 24-hour `HH:MM` times. Blank fields mean no manual fallback for those slots. The display resolves Jumu'ah slots in this order: **date-specific exception**, then **dated timetable value**, then **manual weekly fallback**. Jumu'ah CSV values are taken from the row dated for the current or upcoming Friday; they do not inherit from an earlier Friday's row. Jumu'ah exceptions apply to Friday schedules.

When Friday's services are approaching or finishing, the display can alternate between the current and following Friday's complete schedules. It does not mix individual slots from different Fridays.

### 7. Display, appearance and layout

Under **General**, choose **Theme** (LIGHT, MEDIUM or DARK), a **Colour palette** (including Custom), background image opacity, nearby-masajid size, font sizes and font families. Font changes can preview immediately; **Save** retains them and **Cancel** reverts the preview.

The **Display panes** selector has three miniature controls: **Compact**, **Expanded left** and **Expanded right**. Each can show **Prayer Times**, **Masjid Explorer** or **Promotions**; the expanded right pane also supports **None**. Click a miniature pane to edit its content; clicking it again closes the editor.

The **Startup** controls include **Start MasjidTime automatically at login** and **Start app in full screen**. **F11** toggles full screen and **Esc** exits full screen.

![Display settings](../screenshots/Settings%20-%20Display.png)

![Landscape promotions](../screenshots/Landscape%20-%20Promotions.png)

![Landscape explorer](../screenshots/Landscape%20-%20Explorer.png)

![Portrait display](../screenshots/Portrait%20-%20Default.png)

### 8. Hijri date and prayer advisories

Under **General > Date Settings**, select **Show Hijri date**, set **Adjust Hijri (days)** to align with the local calendar, and choose **Hijri date rollover**: **At Maghrib** or **At Midnight**. The rollover preference changes the Hijri date display, not prayer/Iqamah rollover.

Under **Prayer warnings**, enable or disable prayer-time advisories and configure their timing. These advisories are distinct from the prayer-row highlighting.

### 9. Tomorrow's Iqamah preview

**Finalise tomorrow's Iqamah after** controls how long after today's Iqamah a changed time remains in its preview/transition state. The default is **5 minutes**, and the setting allows **0-60 minutes**. A changed tomorrow Iqamah time can preview starting **20 minutes before today's Iqamah**. This is a display transition and does not itself change the underlying prayer entry calculation.

### 10. Content, notices and promotions

Under **Content**, select **Notices…** to manage notices. The **CSV template** button exports the timetable template. The Iqamah timetable source remains in the individual masjid editor.

Promotions folder and page-duration controls are displayed in settings only when promotions configuration is enabled for that settings dialog; their absence does not mean the Promotions pane is unavailable.

![Content and notices](../screenshots/Settings%20-%20Content%20and%20Notices.png)

![Notices](../screenshots/Direct%20Notices.png)

### 11. Saving, checking and troubleshooting

After configuration, verify the correct masjid is displayed, the timezone and calculated entry times are correct, and the congregation timetable is available. If the timetable cannot be loaded, MasjidTime can still show calculated entry times while displaying **Timetable unavailable** in the footer; hover the footer for the underlying error.

If a remote timetable is used, check its URL with **Validate Remote** and confirm the refresh setting. If Custom entries show `--:--`, check the date row, required headers and whether annual repetition is enabled. For a one-day Iqamah difference, check **Exceptions…** before editing the base CSV.

**Restore Delivered Defaults…** is available under General > Recovery. Because recovery can replace configuration, review the selected recovery scope and back up important files before confirming. **Enable Debug Mode** displays diagnostics and detailed logs for troubleshooting; disable it when no longer required.

### 12. Support and licensing

For installation, activation or configuration help, contact **[support@cloudsyntax.co.uk](mailto:support@cloudsyntax.co.uk)**. Include the application version, Windows/Linux platform and a description of the issue. For licence requests, provide the **Device ID** shown on the activation screen. Do not publicly post licence files or private credentials.

---

## Appendix A - CSV timetable reference

MasjidTime supports two different timetable purposes. **Custom entry-time CSVs** supply the prayer entry times when the **Custom** calculation method is selected. **Iqamah CSVs** supply congregation times for a masjid, including when prayer entry times are calculated automatically. Both formats use header names rather than fixed column positions. Headers are case-insensitive; `date` is required.

### A1. Custom calculation method - entry-time CSV

The application's combined Custom CSV template exports the following headers, in this order:

```csv
date,fajr_entry,sunrise,dhuhr_entry,asr_entry,maghrib_entry,isha_entry,day_type,fajr_iqamah,dhuhr_iqamah,asr_iqamah,maghrib_offset,isha_iqamah,jummah_1,jummah_2,jummah_3,Day,Notes
```

Example of the entry-time portion:

```csv
date,fajr_entry,sunrise,dhuhr_entry,asr_entry,maghrib_entry,isha_entry
2028-01-01,06:10,08:15,12:20,14:15,16:05,18:00
```

- **Required header:** `date`. To display a complete daily entry timetable, populate `fajr_entry`, `sunrise`, `dhuhr_entry`, `asr_entry`, `maghrib_entry` and `isha_entry`.
- **Time format:** `HH:MM` in 24-hour time, such as `06:10` or `18:00`.
- **Dates:** Use `YYYY-MM-DD` (recommended), or supported UK-style dates.
- **Missing values:** A missing date or blank entry time displays `--:--`; entry times do **not** carry forward from a previous day.
- **Annual repetition:** When enabled in settings, the same month/day may be reused in later years. An exact matching date takes priority. A 29 February entry has no matching day in non-leap years.
- **Important:** Although the exported combined template also includes Iqamah and Jumu'ah columns, the Custom **entry-time loader** reads the six entry/sunrise columns. Configure the masjid's Iqamah timetable separately to supply congregation times.

### A2. Non-Custom calculation methods - Iqamah CSV

For MWL, ISNA, Egypt, Karachi, UmmAlQura, Moonsighting and Wifaqul Ulama methods, MasjidTime calculates the prayer entry times. The masjid's Iqamah CSV supplies congregation times. A clear recommended header set is:

```csv
date,day_type,fajr_iqamah,dhuhr_iqamah,asr_iqamah,maghrib_offset,isha_iqamah,jummah_1,jummah_2,jummah_3
```

Example:

```csv
date,day_type,fajr_iqamah,dhuhr_iqamah,asr_iqamah,maghrib_offset,isha_iqamah,jummah_1,jummah_2,jummah_3
2028-01-01,All,07:00,13:00,14:45,5,19:00,,,
```

- **Required header:** `date`; the file must contain at least one dated timetable row. Other columns are optional, but provide the prayer fields you intend to display.
- **Fajr, Dhuhr, Asr and Isha:** Enter 24-hour `HH:MM` Iqamah times. Each prayer has its own dated change history; a later blank cell can leave an earlier non-blank time effective.
- **Maghrib:** `maghrib_offset` is an integer number of minutes added to calculated Maghrib entry time. The loader also accepts `maghrib_offset_min`.
- **Jumu'ah:** `jummah_1`, `jummah_2` and `jummah_3` provide Friday service times. Unlike ordinary Iqamah times, Jumu'ah CSV values are read from the row dated for the relevant Friday and do not inherit from a previous Friday.
- **Date handling:** Iqamah change-points remain effective into subsequent dates and years until replaced; they do **not** repeat as an annual month/day cycle.

This Iqamah format can also be used alongside **Custom** entry times: Custom determines the entries; the separate Iqamah timetable determines congregation times.

### A3. Shared column reference

| Column | Purpose | Notes |
|---|---|---|
| `date` | Date of entry or Iqamah change | Required in both formats; `YYYY-MM-DD` recommended |
| `fajr_entry`, `sunrise`, `dhuhr_entry`, `asr_entry`, `maghrib_entry`, `isha_entry` | Custom prayer entry times | Used by Custom entry-time loader; `HH:MM` |
| `day_type` | Applicable day category for Iqamah rows | Optional; blank defaults to `All` |
| `fajr_iqamah`, `dhuhr_iqamah`, `asr_iqamah`, `isha_iqamah` | Congregation times | Optional `HH:MM` values, independently carried forward |
| `maghrib_offset` or `maghrib_offset_min` | Minutes after Maghrib entry | Integer offset; alternative accepted header names |
| `jummah_1`, `jummah_2`, `jummah_3` | Friday service times | Optional; no inheritance from earlier Friday rows |
| `Day`, `Notes` | Spreadsheet reference information | Optional informational columns in the exported combined template |

**Spreadsheet tip:** Keep the header row intact, save the file as CSV, and verify that date and time values have not been reformatted unexpectedly by your spreadsheet application.
