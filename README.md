# DayBoard

An open-source daily and weekly task planner for Windows 10 and 11. Out of the box it stays simple: Day and Week views, an inbox, Stats and a Focus timer. Everything else (Kanban, an Eisenhower matrix, a weekly review, a daily journal, colour themes) is an optional plugin, so you only get what you use. It's fully local: a native WPF app (C#, .NET 10, WPF-UI) with a SQLite database on your PC. It has no web UI, needs no account and doesn't use the cloud.

## Run it

1. Before unzipping, right-click `DayBoard-win-x64.zip`, choose **Properties**, tick **Unblock** at the bottom of the General tab, and click **OK**. This stops the *"Windows protected your PC"* warning (see below).
2. Unzip it to a folder you'll keep, for example `C:\Apps\DayBoard`. Don't run it from inside the zip or from Downloads if you tidy that folder.
3. Double-click `DayBoard.exe`. Nothing needs installing, because the .NET runtime is built into the exe.

The zip also has a `Plugins` folder with the optional extras: the Kanban board, the Eisenhower matrix, the weekly review, the daily check-in and five colour themes. Add the ones you want under **Settings › Plugins** (see [Plugins](#plugins)).

On first start DayBoard adds itself to the Start menu, so Windows search finds it. You can turn that off under **Settings › Windows integration**. If you move the exe, the shortcut follows the next time you start it.

**Windows SmartScreen:** Windows marks files downloaded from the internet, and SmartScreen warns about marked programs it hasn't seen many times before. DayBoard isn't code-signed and isn't widely downloaded, so it gets the *"Windows protected your PC"* warning. Unblocking the zip before you unzip it removes the mark, so the warning doesn't appear. If you already unzipped it, unblock `DayBoard.exe` the same way, or click **More info** › **Run anyway** once. A copy you build yourself with `build.ps1` is never marked.

The first start takes a few seconds longer, because the single-file exe unpacks its native libraries into a cache folder.

## Where your data lives

Everything is stored in `%LOCALAPPDATA%\DayBoard\` (usually `C:\Users\<you>\AppData\Local\DayBoard\`):

| Path | What |
|---|---|
| `dayboard.db` | The SQLite database: tasks, recurring series, categories, history, settings, and what plugins save (such as daily check-ins) |
| `backups\dayboard-YYYY-MM-DD.db` | Automatic daily backups. The newest 14 are kept |
| `calendars\*.ics` | Cached copies of subscribed calendars, so they show offline |
| `logs\` | Error logs |
| `plugins\` | Installed plugins, one folder each |
| `plugin-data\` | Files that code plugins keep, if any (most keep their data in `dayboard.db`) |

You can make a backup or restore one under **Settings › Data**. A restore restarts the app, and a copy of the data you replaced is kept in `backups\`. To start fresh, quit DayBoard (tray icon › Exit) and delete the folder.

## Features at a glance

- **Day view:** a timeline with 30-minute slots (hours are configurable), an "unscheduled" list, overdue tasks and the day's capacity bar. Drag a task onto a slot to time-block it. Drag a block's bottom edge to change its estimate. Double-click an empty slot to add a task there.
- **Week view:** seven columns (week start is configurable), each with a load bar and read-only calendar events.
- **Inbox:** a sidebar in the day and week views for tasks without a date.
- **Drag and drop:** between days, into time slots, and to and from the inbox (and, with their plugins, between matrix quadrants and Kanban columns). A highlighted zone plus an insertion line or time ghost shows where the task will land.
- **Rollover:** at startup and at midnight, unfinished tasks from past days move to today and their rollover count goes up. In Settings you can switch to flagging them as overdue instead.
- **Recurring tasks:** daily, weekdays, weekly on chosen days, every N weeks, monthly on day X, or monthly on the first/second/third/fourth/last weekday. When you edit or delete one, DayBoard asks whether to change this occurrence or the whole series.
- **Priority** is shown as a coloured card outline *and* a `!` / `!!` / `!!!` badge, so it never relies on colour alone. **Category** is shown as the coloured left border plus a tag chip with an icon.
- **Stats:** completion rate by priority and by category, and GitHub-style completion heatmaps for the month and year.
- **Focus:** a Pomodoro timer (25/5 by default, configurable) that logs the time to the task and shows estimate against actual. It's a built-in plugin, so you can untick it if you don't use it.
- **Windows integration:**
  - Global quick-add shortcut, Ctrl+Alt+Space by default and configurable.
  - Tray icon showing how many tasks are left today, inside the logo's arc.
  - Always-on-top mini view.
  - Start with Windows.
  - Taskbar jump list with *New task* and *Today*.
  - Single instance: launching DayBoard again brings up the running copy.
  - Start menu shortcut, so Windows search finds DayBoard.
- **Data:**
  - Undo/redo for every change (Ctrl+Z / Ctrl+Y).
  - JSON export and import, CSV export, and .ics export.
  - iCal subscriptions such as Google or Outlook secret addresses.
  - Microsoft To Do import.
- **Plugins:** optional extras you add yourself: a Kanban board, an Eisenhower matrix, a weekly review, a daily check-in with a journal, and colour themes. DayBoard looks and works the same without them.

### Keyboard

| Keys | Action |
|---|---|
| Ctrl+Z / Ctrl+Y (or Ctrl+Shift+Z) | Undo / redo |
| Ctrl+N / Ctrl+Shift+N | New task / quick add |
| Ctrl+Left, Ctrl+Right (or PageUp, PageDown) | Previous / next day or week |
| Ctrl+T | Today |
| Ctrl+1, Ctrl+2, Ctrl+3, Ctrl+, | Day, Week, Stats, Settings |
| Ctrl+4 … Ctrl+9 | Plugin pages in sidebar order: Focus, then the ones you add, such as Matrix, Kanban, Review and the Journal |
| Ctrl+I / Ctrl+M / F5 | Toggle inbox / mini view / refresh calendars |
| On a focused card: Enter, Space, Delete, 0–3, U, Ctrl+D | Edit, done, delete, priority, urgent, duplicate |
| Ctrl+Alt+Space (anywhere in Windows) | Quick add |

In quick add, ending the title with `!`, `!!` or `!!!` sets the priority, and `#work` picks the matching category.

### Calendar subscriptions

Under **Settings › Calendar subscriptions**, paste a secret iCal address:

- **Google Calendar:** Settings › *your calendar* › *Secret address in iCal format*.
- **Outlook.com / Microsoft 365:** Settings › Calendar › Shared calendars › *Publish a calendar* › ICS link.

Events show read-only in the day and week views and refresh every 30 minutes (configurable). Recurring events, exceptions, moved instances and time zones are handled, including IANA and Windows zone names and zones defined inside the feed itself.

### Microsoft To Do import

You need your own (free) Azure app registration:

1. In the Azure portal, go to **Microsoft Entra ID › App registrations › New registration**. For personal accounts, choose *"Accounts in any organizational directory and personal Microsoft accounts"*.
2. Under **Authentication**, add the platform **Mobile and desktop applications** with the redirect URI `http://localhost`.
3. Under **API permissions**, add **Microsoft Graph › Delegated › Tasks.Read**.
4. Copy the **Application (client) ID** into DayBoard under **Settings › Import from Microsoft To Do**, then click *Import*. Sign-in opens your web browser.

Lists become categories and checklist items become subtasks. Tasks that were already imported are skipped, and the whole import can be undone with Ctrl+Z.

## Plugins

DayBoard stays plain on purpose: out of the box it has the Day and Week views, the inbox, Stats and Focus. Everything else comes as plugins: single `.dayboard-plugin` files you add once. New plugins don't need a new version of DayBoard. Focus is a plugin too, but it comes with DayBoard.

These come in the `Plugins` folder of the download:

| Plugin file | What it adds |
|---|---|
| `Kanban.dayboard-plugin` | A **Kanban** page: To do, Doing and Done columns. |
| `Matrix.dayboard-plugin` | A **Matrix** page: the Eisenhower matrix. |
| `WeeklyReview.dayboard-plugin` | A **Review** page: the weekly review. |
| `DailyCheckIn.dayboard-plugin` | A check-in panel in the Day view and a **Journal** page. |
| `FadedCherry`, `Terracotta`, `Honey`, `Sage`, `Classic` | Colour themes. |

- **Add one:** go to **Settings › Plugins**, click **Add plugin…** and pick the file (or several at once: the dialog opens on the `Plugins` folder when DayBoard is next to it). It's ticked and used straight away. Adding a newer version of the same plugin updates it, even while it's running.
- **Turn one on or off:** plugins are listed as a checklist under **Settings › Plugins**, starting with the ones marked **Built in** that come with DayBoard. Untick a theme to go back to the stock look. Only one colour theme can be ticked at a time, so ticking another one swaps to it. Other plugins are ticked independently.
- **Delete one:** quit DayBoard and delete the plugin's folder in `%LOCALAPPDATA%\DayBoard\plugins\`. Built-in plugins can't be deleted, only unticked.

There are two kinds of plugin:

- **Colour themes** only contain colours. They can't run code or read your tasks.
- **Code plugins** add features, such as the Kanban board or the daily check-in. They run their own code inside DayBoard, with the same rights as DayBoard itself, so DayBoard asks before adding one and marks it **Runs code** in the list. Only add code plugins from people you trust.

A file that isn't a valid plugin is refused with a message saying what's wrong. If a code plugin runs into an error, DayBoard turns it off, says so, and carries on. Settings › Plugins then shows what went wrong. If DayBoard ever closes while a code plugin is starting, it turns that plugin off the next time it starts. To start DayBoard without the code plugins you added, run `DayBoard.exe --safe-mode`. Built-in plugins still run then, because they're part of DayBoard.

### Focus

Focus comes with DayBoard and is ticked from the start. It's built as a plugin, so if you don't use it, untick **Focus** under **Settings › Plugins**: its page and the *Focus on this* item in task menus go away. Tick it again to bring them back.

- **Start a session:** open **Focus** in the sidebar (Ctrl+4), or right-click a task and choose **Focus on this**. Pick the task, then **Start**. The ring shows how much of the session is left.
- **Breaks:** after each focus session comes a short break, and a long one after every few sessions. DayBoard plays a sound and shows a notification when a session or break ends. The break can start by itself.
- **Time logging:** work time is added to the task's actual time when a session ends, when you reset or skip, when you switch tasks, and when DayBoard closes. Stats and the weekly review plugin count it as focus time. Logging can be undone with Ctrl+Z.
- **On the right:** the task's estimate against the time logged, its checklist (tick items off as you go), **Log time now** and **Mark done**.
- **Timer settings:** the session and break lengths are on the Focus page itself, under **Timer**. (They used to be under Settings. Your settings from 1.3 carry over.)

### Colour themes

DayBoard's own look is **Dusty Plum**, to match the logo: oat-coloured paper in light mode, warm charcoal in dark mode, a dusty plum accent, and muted category, priority and calendar colours. (Until 1.4 it was cool grey with an indigo accent; that look is now the Classic theme below.)

Five theme plugins come in the `Plugins` folder of the download:

| Plugin file | Accent | Adjusted so nothing clashes with the accent |
|---|---|---|
| `FadedCherry.dayboard-plugin` | Muted cherry red | High priority is dusty plum; over capacity and the current-time line are a deeper brick. |
| `Terracotta.dayboard-plugin` | Soft terracotta orange | High priority is dusty plum. |
| `Honey.dayboard-plugin` | Golden honey | Medium priority and the nearly-full capacity bar are muted orange. |
| `Sage.dayboard-plugin` | Soft sage green | The on-track capacity bar is dusty blue. |
| `Classic.dayboard-plugin` | Indigo | DayBoard's look before 1.5: cool grey, with the bright category and priority colours. |

The first four keep DayBoard's warm base and change the accent, tinting the completion heatmap in it. Your stored colours never change: a theme only changes how they're shown, so unticking it brings DayBoard's own look back. If you had the Dusty Plum theme from an earlier version, it now looks the same as DayBoard without a theme, so you can untick it.

To make your own theme or code plugin, see [`plugins/README.md`](plugins/README.md).

### Kanban, Eisenhower matrix and weekly review

These were views of DayBoard until 1.5. Since 1.6 each is a code plugin, so DayBoard stays lean for those who don't use them. They need DayBoard 1.6.0 or later, and they work as before:

- **Kanban** (`Kanban.dayboard-plugin`): To do, Doing and Done columns for today, this week or every open task. Overdue tasks always show. Drag a card to another column to move it; Done marks it done.
- **Matrix** (`Matrix.dayboard-plugin`): open tasks sorted into Do first, Schedule, Delegate and Eliminate. Medium and High priority count as important. Dragging a card to another quadrant sets the task's urgency, and its priority when it has to change (to Medium for important, Low for not).
- **Review** (`WeeklyReview.dayboard-plugin`): what you completed each day of the week, what slipped (rolled over, skipped or overdue), the tasks that keep rolling over, how many tasks were planned, the completion rate, and the focus time logged. The ‹ Today › buttons step through the weeks.

The cards on these pages are DayBoard's own: tick them done, double-click to edit, right-click for the menu, or drag them anywhere, and every change can be undone. The Today / This week / All open choice is remembered. The plugins only see your tasks through DayBoard's plugin API, and they save nothing but that choice.

**Coming from DayBoard 1.5:** add the ones you use from the `Plugins` folder. DayBoard says so once after the update. If one of them was open when you closed 1.5, its page opens again once its plugin is added.

### Daily check-in

`DailyCheckIn.dayboard-plugin` (version 2.0) adds a daily check-in and a journal. It's a code plugin and needs DayBoard 1.3.0 or later.

- **Check in:** a panel at the top of the Day view asks *How's today?* Pick one of five faces for how you feel (very low, low, okay, good, great) and a colour for the day, tick a few tags, and write a note if you like. Everything saves as you go; the note saves a moment after you stop typing. Click a chosen face or colour again to clear it. Type a new tag and press Enter to add your own.
- **Past days:** go back a day in the Day view to fill in a day you missed. Future days have no check-in.
- **Fold it away:** once you've checked in, the arrow folds the panel into one line. The next time you open that day it starts folded; click **Edit** to change it.
- **Journal:** a **Journal** page appears in the sidebar. It shows the month with each day's face and colour, the whole year in colour, and the month's notes. Click any day to read or edit it on the right. **Export journal…** saves every check-in and note as a Markdown file.
- **Privacy:** check-ins are stored in your local database only, so they're included in the daily backups. They aren't part of the JSON or CSV exports, and the mini view never shows them.
- **Turning it off:** untick it under **Settings › Plugins** to remove the panel and the Journal. Your entries are kept and come back when you tick it again, even if you delete and re-add the plugin.

**Coming from DayBoard 1.2:** 1.2 had the check-in built in, switched on by a settings-only plugin. In 1.3 the check-in is the plugin itself, so add the new `DailyCheckIn.dayboard-plugin` once under **Settings › Plugins**. Until you do, the old one is listed with a note saying it needs its new version. All your check-ins carry over: DayBoard 1.3 copies them into the plugin's storage the first time it starts.

Its source is in [`plugins-src/DailyCheckIn`](plugins-src/DailyCheckIn), and it's the example to start from for your own code plugin. The Kanban, matrix and review plugins are in `plugins-src` too: they show how to use DayBoard's task cards and drag and drop.

## Rebuild from source

Requirements: the [.NET 10 SDK](https://dotnet.microsoft.com/download/dotnet/10.0) on Windows 10 or 11. Visual Studio isn't needed.

```powershell
# from the folder with DayBoard.sln
dotnet build DayBoard.sln -c Release
dotnet run --project tests/DayBoard.Core.Tests -c Release   # unit tests (no test framework needed)
dotnet test tests/DayBoard.Tests.xUnit -c Release           # the same tests under xUnit (downloads xUnit from nuget.org)

# self-contained single-file build (the DayBoard.exe in DayBoard-win-x64.zip)
dotnet publish src/DayBoard/DayBoard.csproj -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true -p:IncludeNativeLibrariesForSelfExtract=true -o publish
```

The `publish\DayBoard.exe` file is the whole app.

To do all of that in one step, run `powershell -ExecutionPolicy Bypass -File .\build.ps1`. It builds, runs the tests and publishes. It packs each theme folder in `plugins\` into a `.dayboard-plugin` file in `artifacts\`, and copies the code plugins from `plugins-src\` there too. (A Release build of a code plugin packs its own `.dayboard-plugin` file next to its dll.) Then it writes `artifacts\DayBoard-win-x64.zip` with `DayBoard.exe` and a `Plugins` folder holding those files. Add `-SelfTest` to also run the UI self-test on the published exe.

`EnableWindowsTargeting` is set in the WPF project, so it also compiles and publishes from Linux or macOS with the .NET 10 SDK.

### UI self-test

`DayBoard.exe --selftest <folder>` starts the app on throwaway sample data in a temporary folder, so your real data isn't touched. It opens every view and window in the light and dark themes, saves a PNG of each into `<folder>`, writes `selftest.log`, and exits with code 0 when nothing failed. Add `--plugin <file>` (more than once if you like) to test plugins as well: the first theme is used, and the code plugin checks run for the Daily check-in and for the Kanban, matrix and review plugins when they're passed. `build.ps1 -SelfTest` passes all of them. Without a theme, a small built-in test theme is used.

### Project layout

| Project | Contents |
|---|---|
| `src/DayBoard.Core` | Domain model, RRULE recurrence engine, planner operations as undoable change sets (command pattern), rollover, recurring-series generation, iCal parser and writer, JSON/CSV import and export, stats. No dependencies. |
| `src/DayBoard.Data` | SQLite storage (Microsoft.Data.Sqlite), settings, daily backups, and the plugin file format, plugin folder and plugin storage (`Plugins/`). |
| `src/DayBoard.Plugins` | The plugin API that code plugins build against: `IDayBoardPlugin`, `IDayBoardHost`, pages, Day view panels, task menu items, task cards and drags, and storage. Small on purpose, and kept compatible. |
| `src/DayBoard.Focus` | Focus, the built-in plugin. It only uses the plugin API, like any other plugin, but it's part of `DayBoard.exe`. |
| `src/DayBoard` | WPF app (MVVM with CommunityToolkit.Mvvm, WPF-UI). It contains the views, the drag-and-drop and timeline controls, the tray (H.NotifyIcon), the global hotkey (RegisterHotKey), the jump list, the single-instance pipe, iCal subscriptions, the Microsoft To Do import (MSAL + Microsoft Graph), and the plugin host (`Hosting/`: the built-in plugins, one load context per added plugin, start/stop, the error guard). |
| `tests/DayBoard.Core.Tests` | Console test runner covering recurrence (checked against python-dateutil), undo/redo, series edits, rollover, iCal, export, stats, SQLite, plugins, and the models of the Daily check-in, Kanban, matrix and review plugins. |
| `tests/DayBoard.Tests.xUnit` | The same test files run under xUnit (`dotnet test`), each test an xUnit fact. It needs nuget.org for the xUnit packages, so it's kept out of `DayBoard.sln` and the solution still builds offline. |
| `plugins/` | Source files of the five theme plugins, one folder each, plus a guide to making your own themes and code plugins. |
| `plugins-src/` | Code plugins: `DailyCheckIn` (the daily check-in and Journal), `Kanban`, `Matrix` and `WeeklyReview`, and `Shared` (board code compiled into the Kanban and matrix plugins). |
| `branding/` | The logo (the open "focus arc") as SVG and PNG, and `make_logo.py`, which draws it and writes the app's `DayBoard.ico` and `DayBoard.png`. |

## Notes and limits

- Rollover is a system action, so it isn't on the undo stack. Neither is the background generation of upcoming recurring occurrences.
- Dragging an occurrence of a recurring task moves only that occurrence, without asking. Editing or deleting one asks this-occurrence-or-whole-series.
- "Monthly on day 31" lands on the last day of shorter months. The rule is stored as standard RRULE (`BYMONTHDAY=28,29,30,31;BYSETPOS=-1`), so the .ics export stays compatible.
- Capacity counts the estimates of all tasks planned that day, done or not. Calendar events don't count toward it.
- The undo history lasts until you quit the app.
- Check-ins aren't on the undo stack. Click a chosen face or colour again to clear it, or empty the note.

## How this build was tested

This release was built and tested on Linux, not on Windows. The published `DayBoard.exe` is the one from the command above.

- **Unit tests:** 74 of 74 pass in the console runner. The xUnit project compiles the same 74 tests, but it couldn't be run here: the build machine can't reach nuget.org to download xUnit. Run `dotnet test tests/DayBoard.Tests.xUnit` on your PC to see them under xUnit. They cover recurrence, undo/redo, series edits, rollover, iCal parsing and writing, import/export, stats, SQLite and plugins. The plugin tests check that bad files are refused with a readable message, that nothing is written outside the plugin's own folder, and that the theme slots match the app's palette files. For code plugins they check the manifest rules, that only real plugin assemblies are accepted (by reading their metadata, without running them), plugin storage, the database upgrade that moves 1.2's check-ins into the plugin's storage, the Daily check-in's own saving and Markdown export, and Focus taking its timer settings along from 1.3. For the Kanban, matrix and review plugins they check the board scopes, the columns and quadrants, the weekly review worked out from the task history (against the same week the old built-in review was tested with), their plugin files, and the one-time note about the move.
- **Plugins:** each of the five themes, and DayBoard's own look, is checked for completeness and readable text contrast in both modes, and that high priority doesn't look like its accent. Each one was also run through the UI self-test with its own screenshots.
- **Plugins in the UI self-test:** refusing a file that isn't a plugin, adding a theme, every main view in its light and dark versions, category, priority and calendar colours following the theme, unticking it and ticking it again, only one theme ticked at a time, adding it again as an update, and removing its folder.
- **Focus in the UI self-test:** listed as built in and ticked, Ctrl+4, *Focus on this* from a task's menu, starting, pausing and resetting the timer, ticking a checklist item and marking the task done (and undoing both), a whole one-minute session that logs its minute to the task and starts the break by itself (and undoing the logged time), changing the focus length, unticking it (its page and menu item go) and ticking it again, and refusing a plugin file that tries to replace it.
- **The Daily check-in code plugin in the UI self-test:** the self-test presses its buttons and types into its boxes the way a screen reader would. It covers the 1.2 plugin asking for its new version, the confirmation dialog (Cancel, then Add), logging today from the Day view panel, folding and opening the panel, a note saved when you move to another day straight after typing, the Journal page (Ctrl+5) and its month buttons, editing a past day there, future days being read-only, the Markdown export through DayBoard's save dialog, unticking (removed, data kept) and ticking again, updating the plugin while it runs, and a plugin error being caught: the plugin is turned off and DayBoard carries on.
- **The Kanban, matrix and review plugins in the UI self-test:** the removed views are gone from the sidebar and Ctrl+3 is Stats; a view left open in 1.5 opens the Day view until its plugin is added, and its page once it is; adding all three (sidebar order and Ctrl+5 to Ctrl+7); every page in light and dark; dragging a card to the Eliminate quadrant and to the Doing column (the card moves, the highlight clears, the scope choice is kept); ticking a card done on a plugin page and undoing it; the weekly review's numbers against the database, and stepping to last week and back; undoing the drags; and unticking one plugin while the others stay.
- **Plugins built for older versions still work:** the self-test was run again with the `DailyCheckIn.dayboard-plugin` file built for DayBoard 1.5 (against plugin API 2), and all its checks passed. (In 1.4 the same was done with the file built for 1.3, on plugin API 1.)
- **UI self-test under Wine:** all 78 checks pass. It also saves the tray icon at each tray size (16, 20, 24 and 32 px) with and without a count, and the taskbar badge, so they can be looked over. The test opens every view and window in both themes and in compact density, and simulates drags to each drop target (timeline slot, unscheduled list, week column, inbox, and with the plugins a matrix quadrant and a Kanban column) followed by undos. It also covers navigation, undo/redo, quick add, the mini view, registering a global hotkey, JSON/CSV/.ics export and re-import, backups, and subscribing to an iCal feed over HTTP.
- **Single instance:** under Wine, a second launch with `--today` or `--quick-add` passed its arguments to the running instance and then exited.
- **Not tested yet on real Windows:** code plugins on Windows itself (they were tested under Wine), the tray icon (Wine can't create one), Mica, the jump list, the Start with Windows registry entry, the Start menu shortcut appearing in Windows search (Wine only checked that the shortcut file is written correctly), a hotkey that clashes with another app, real mouse drag gestures, Windows fonts and high-DPI scaling, and Microsoft To Do sign-in, which needs your own Azure client ID.

To run the unit tests and the UI self-test on your own PC, use `.\build.ps1 -SelfTest`.

## License

DayBoard is open source under the [MIT License](LICENSE). The libraries it's built with are listed, with their licences, in [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md).
