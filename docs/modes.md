# Launch modes in detail

The menu and the flags in the [README](../README.md) start six kinds of session.
A plain course launch just runs that workspace's Lern-Loop. The other five are
described here.

## Begleiter

The Begleiter is the default persona and the menu's top row as soon as one
course is registered. Picking it (or running `studyclaude --begleiter`) launches
one session at the courses' common parent folder, like the Gärtner, with every
course's dossier, your upcoming exams, the Wochenplan and the current time. It
works from the clock:

1. It reads your profile: the tab „Profil“ on a Tutor Board named „studyclaude“.
   The profile says who you are (programme, semester), where your timetable is
   (a calendar, a file, another board's tab) and where the semester's material
   lies. It is free text. The repo holds no personal data, so the profile lives
   on your board. Without a board, or without the tab, the session asks you for
   these facts in chat and offers to create the tab.
2. It checks the timetable the profile names: what runs now, what just ended,
   what comes later today, and deadlines until tomorrow.
3. It picks what is due: following a lecture that is running, preparing one that
   starts within the hour, reviewing one that just ended, a deadline, or — in free
   time — a course by Wochenplan, exams and dossiers, and the next Häppchen from
   its Lern-Loop.
4. It tells you in at most three lines what is on now, what comes later and what
   it suggests, and then starts.

It is the automatic default whenever you never chose one, and `d` on its row or
`studyclaude --set-default begleiter` sets it explicitly. The board „studyclaude“
holds only the profile; Häppchen go on each course's own board.

## Tutors Choice

With two or more courses registered, the menu's top row is **Tutors Choice**.
Picking it (or running `studyclaude --tutor`) launches one interactive session,
exactly like starting a course — except its opening message carries a compact,
mechanically extracted dossier per course (progress line, days since last
activity, Themenkarte size, created/reviewed Häppchen counts, the dominant
Fehlermuster, the newest todo.md entry) plus your upcoming exams, and tells it
to read the strongest candidates' `todo.md` before deciding. The session weighs
exam proximity against neglect and progress, tells you in one sentence which
course it picks and why, then reads that course's `CLAUDE.md` and runs its
Lern-Loop itself. The chooser and the tutor writing your Häppchen are the same
session — no hidden pre-pass, no wait at the menu. It starts at the courses'
common parent folder so it can work across all of them.

Tutors Choice can itself be the default: press `d` on its row, or run
`studyclaude --set-default tutor` — the 10s autostart then runs the tutor pick.
(Scripted flags like `--dry-run` need a concrete folder and fall back to the
first course.)

## Quickie

The menu's very top row (shown as soon as one course exists) is **⚡ Quickie**
— one short Häppchen, about five minutes, nothing else. It is the low-threshold
entry for days when a full session feels like too much: pick it (or run
`studyclaude --quickie`) and the session greets you in one sentence, picks a course
in half a sentence when there are several (no file reading first — the dossiers
suffice), and opens one small, self-contained, deliberately *winnable* task in
the active medium, following that course's `CLAUDE.md` mechanics scaled down to
a single Häppchen. After your answer it corrects briefly, names what you got
right, and asks „Noch eins?“ the way the active medium does it (a button on the
board, a line in the chat) — with the next Quickie already in mind. If you
stop, it says goodbye in a sentence; no lecture. A Quickie still counts in the
`Fortschritt:` line and is noted with date and topic in `todo.md`.

The launcher keeps a streak in the registry (`quickies`: last date, days in a
row, total) and shows it on the row — `· Serie: 3 Tage` while the streak is
alive (a Quickie today or yesterday), `· bisher 12` otherwise. The session gets
the numbers to mention in its greeting. A launch counts as a Quickie; the
launcher never judges whether you finished. `studyclaude --set-default quickie`
(or `d` on the row) makes it the 10s autostart target.

The Quickie never builds a course overview.

## Wochenplan

Tutors Choice and the Quickie pick a course on their own. To steer that pick by
weekday, write a plain-text rule into `data/wochenplan.md`, next to the registry
(with `STUDYCLAUDE_REGISTRY` set, next to that file). For example: "Monday to
Friday the university courses, on weekends the languages; an exam within 7 days
always comes first." Copy
[`templates/wochenplan.example.md`](../templates/wochenplan.example.md) as a
starting point and name courses by their folder name.

The launcher does not parse the rule. It passes the text, together with today's
weekday and date, into the opening message, and the session applies it when it
picks. Without the file, or with an empty one, nothing changes. The file is
personal and gitignored, like the registry. A Quickie with only one course
registered has no pick to make and ignores it.

## Meta-Häppchen

A Meta-Häppchen is one short Häppchen in a **target** course, written with the
other selected **source** courses in view: the session reads each source's
`fehlermuster.md` in full and the last ten or so dated lines of its `todo.md`,
then builds a single winnable task in the target's own material that provokes
the sources' error patterns or reuses what they just practised. Which of the
two dominates is the session's call. It names in half a sentence which source
shaped the task, reviews briefly, and asks „Noch eins?“ with the same
selection. Only the target's files change (its `Fortschritt:` line and a dated
`todo.md` note naming the sources); the sources are read-only. A launch counts
as a Quickie day too, so the streak survives a Meta-only day.

In the menu, `Space` on any row opens the multiselect. The first course you
check is the target (`◉`), further checks are sources (`☑`), `z` moves the
target to the cursor row, `Enter` launches with a target and at least one
source, `Esc` goes back. The selection is remembered in the registry (`meta`:
target, sources, total), and the multiselect opens pre-checked with it next
time. Once something is remembered, a row `⇄ Meta-Häppchen — Mathe + Spanisch
→ Physik` appears under Tutors Choice: `Enter` repeats the combination, `d`
makes it the 10s autostart target (`studyclaude --set-default meta` does the same),
and it disappears while one of its courses is unregistered.

From a script: `studyclaude --meta ZIEL QUELLE [QUELLE…]` launches and remembers an
explicit combination (unregistered paths get registered, like `--set-default`);
bare `studyclaude --meta` reuses the remembered one; `studyclaude --print-prompt --meta …`
shows the system prompt without launching or remembering.

## Gärtner

The Gärtner is a maintenance session, not a study session: no Häppchen, no
tutoring. Press `g` in the menu (or run `studyclaude --gaertner`) and tell it in chat
what to do with your courses — clean one up, merge two into one, revise a
course's `CLAUDE.md` against the current template. It gets every course's
dossier and your upcoming exams, greets you, names up to three things it notices
(two courses on the same subject, a long-idle course, a missing overview) and
asks what you want to tackle. It starts at the courses' common parent folder.

Courses checked in the multiselect (`Space`) when you press `g` are named as
the focus; the others are there for comparison and change only when you say so.
From a script, `studyclaude --gaertner PFAD…` sets the focus; a path that is not
registered is shown but not registered.

Before anything that moves, deletes or merges files, or changes the course list,
it shows a short plan and waits for your OK. It changes the course list only
through `studyclaude --register` / `--unregister`, never by editing the registry file,
and moves what should go into `_archiv/` inside the course instead of deleting
it, unless you ask for deletion. After a merge it re-estimates the `Fortschritt:`
line and sets `Übersicht: fehlt`, so the next study session builds a new overview.
The Gärtner keeps no state and does not count towards the Quickie streak.

## The progress line

Each course row in the menu shows its progress, e.g. `· 7/24 Häppchen` (and
`✓ bereit` once the target is reached), and `· ohne Übersicht` while the course
has no confirmed overview yet. The numbers come from a single line the study
session itself maintains in the workspace's `todo.md`:

```
Fortschritt: 7/24 Häppchen
```

`x` counts reviewed Häppchen; `y` is the session's *living estimate* of how many
it will take until you are exam-ready, re-judged after every review. The
launcher only reads the line — the judgment stays in the workspace. No line, no
display.
