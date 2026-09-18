# Job Applyr Lite — What The App Actually Does

**GENERATED FILE. Do not hand-edit — your edit will be overwritten and, worse, believed.**

Every line below was read out of the shipped build by `behaviour-map.js`.
This file answers *what the app does*. `JA-LITE-DECISIONS.md` answers *why*, and
`JA-LITE-STATE.md` answers *what is left to do*. When those two disagree with this
one, **this one wins** — it is the only one read from the code.

| | |
|---|---|
| Source | `index.html` |
| `APP_VERSION` | **28** |
| Size | 2,991 lines, 403,003 bytes |

**To regenerate:** `node behaviour-map.js ~/Desktop/job-applyr-lite/index.html`

---

## Buckets

A job lives in exactly one bucket. These are the tabs on the list screen.

| Key | Shown as |
|---|---|
| `ready` | Ready to Apply |
| `flagged` | Flagged |
| `rejected` | Rejected |
| `applied` | Applied |
| `unassigned` | Unassigned |

**A grader verdict maps to a bucket like this:**

| Verdict | Bucket |
|---|---|
| `APPLY` | `ready` |
| `FLAG` | `flagged` |
| `REJECT` | `rejected` |

Anything not in that table falls to `unassigned`.

---

## The Applied tab is grouped. This is the part that gets forgotten.

The Applied bucket does **not** render a flat list. It renders these groups, in this
order, each under its own heading:

| Order | Heading | Rows shown |
|---|---|---|
| 1 | **Interview scheduled** | `response_status === 'interview'` |
| 2 | **No response** | `response_status === 'none'` |
| 3 | **Rejected by employer** | `response_status === 'rejected'` |

Every applied row carries a dropdown writing `response_status`:

- `none` — "No response"
- `rejected` — "Rejected by employer"
- `interview` — "Interview"

- Interview rows sort by `interview_date`, soonest first: **yes**
- An inline date picker appears on Interview rows: **yes**
- Switching away from Interview clears `interview_date`: **yes**

**An applied job never leaves the Applied bucket because of its outcome.** An employer
rejection is a `response_status`, which groups the row to the bottom of Applied. The
`rejected` *bucket* means the user rejected the job. Two different facts, two different
places. See D-21.

---

## A job detail view

Tabs, in render order:

1. **Analysis** (`analysis`)
2. **Notes & Questions** (`notes`)
3. **Resume** (`resume`)
4. **Cover Letter** (`coverletter`)
5. **Portfolio Guidance** (`portfolio`)

**Fit sections** render `qualItems` / `envItems` as bullets. An item may be an object
`{tone, text}` or a plain string.

- A plain string gets `tone: 'none'` and a grey 8px dot: **yes**
- `green` and `red` tones get a 12px coloured dot, because a toned dot carries meaning.
- **So imported prose needs no tone.** It renders correctly untoned.

`job.parsingError` swaps the score for a warning. It is read in 3 place(s) and
**is never set anywhere — it is scaffolding waiting for the import.**

---

## The stored record

`normalizeJob()` builds every job from these defaults. **Nothing else is a field.**

| Field | Default |
|---|---|
| `company` | `'Unknown'` |
| `title` | `'Untitled role'` |
| `location` | `''` |
| `salary` | `'Not listed'` |
| `bucket` | `'unassigned'` |
| `verdict` | `''` |
| `qualFit` | `0` |
| `envFit` | `0` |
| `flagCount` | `0` |
| `notes` | `''` |
| `flagReason` | `''` |
| `qualItems` | `[]` |
| `envItems` | `[]` |
| `fullPosting` | `''` |
| `resume` | `''` |
| `coverletter` | `''` |
| `openQuestions` | `''` |
| `portfolioGuidance` | `[]` |
| `generating` | `false` |
| `materialsError` | `''` |
| `sourceUrl` | `''` |
| `appliedDate` | `''` |
| `rejectedDate` | `''` |
| `response_status` | `'none'` |
| `interview_date` | `''` |

Derived on write if absent: `addedDate`, `postedDateIso`, `postedDate`, `storageKey`.

---

## Where data is kept

Browser storage on the live origin. Nothing is sent anywhere but Anthropic.

| Constant | Key |
|---|---|
| `PROFILE_DB` | `ja-lite` |
| `PROFILE_STORE` | `handles` |
| `PROFILE_HANDLE_KEY` | `profileFolder` |
| `PROFILE_CACHE_KEY` | `profile:cache` |
| `API_KEY_STORAGE` | `cfg:apiKey` |
| `JOB_PREFIX` | `job:` |
| `PENDING_PREFIX` | `pending:` |
| `UI_STATE_KEY` | `ui:state` |
| `MIGRATION_KEY` | `meta:migrations` |
| `BACKUP_META_KEY` | `meta:backup` |

One-time migrations recorded under the migrations key: `duplicateCheckV2`.

**A migration flag is checked once and never re-run.** Records written before a flag was
set keep whatever answer they were given at the time.

**Also written outside the browser, into the profile folder** — only once the user has
turned on automatic backup and Chrome has allowed writing:

| Constant | Value |
|---|---|
| `AUTO_BACKUP_DIR` | `ja-lite-backups` |
| `AUTO_BACKUP_FILE` | `job-applyr-backup.json` |
| `AUTO_BACKUP_PREVIOUS_FILE` | `job-applyr-backup-previous.json` |
| `AUTO_BACKUP_TEMP_FILE` | `job-applyr-backup.json.tmp` |
| `AUTO_BACKUP_PAUSE_MS` | `2000` |

The profile folder handle lives in IndexedDB, not local storage — a directory handle
cannot be serialised into local storage.

---

## Tabs, and what the app needs before it changes anything

Navigation, left to right: **Upload**, **Review**, **Unassigned**, **Settings**.
The app opens on `review`. **Unassigned is shown only when something is in it.**

Setup lives on Settings. Until every requirement is met, every other tab shows one box
headed **To use Job Applyr:** with one line per missing item:

| Missing | Line shown |
|---|---|
| `storage` | open Job Applyr in a normal Chrome window, not a private one. |
| `profileNone` | choose your profile folder on the *Settings tab* (a link) |
| `profileLost` | reconnect your profile folder on the *Settings tab* (a link) |
| `apiKey` | enter your API key on the *Settings tab* (a link) |
| `backupOff` | turn on automatic backup on the *Settings tab* (a link) |
| `backupFailed` | fix automatic backup on the *Settings tab* (a link) |

**While any line shows, these refuse and their controls are greyed out:** `deleteJob()`, `generateMaterials()`, `handleSave()`, `moveJob()`, `openReasonPanel()`, `regenerateDoc()`, `runAnalysis()`, `saveDoc()`, `saveNotes()`, `setInterviewDate()`, `setResponseStatus()`, `startEditing()`, `updateJobField()`.
Viewing stays open — switching buckets, sorting, opening a job, switching its tabs, exporting.

---

## Everything the user can trigger

Every `app.*` handler reachable from the interface:

- `changeApiKey()` — clears the stored key so the entry box comes back — the only in-app way to replace a rejected key
- `chooseBackupFile()` — opens the file picker for restoring from a backup file
- `chooseProfileFolder()` — opens the folder picker for the five profile files
- `deleteJob()` — removes a record — offered on duplicate rows only, and it confirms first
- `exportDoc()` — downloads the open document as PDF or Word
- `generateMaterials()` — drafts résumé and cover letter, skipping any field that already has content
- `handleSave()` — saves the open editor
- `moveJob()` — moves a job to another bucket, stamping `rejectedDate` on the way into Rejected
- `openJob()` — opens the detail view
- `openReasonPanel()` — opens the flag or reject reason panel, reading the reason off the record
- `regenerateDoc()` — clears one document then redrafts it — confirms first, and hand edits do not survive
- `reloadProfile()` — re-reads the profile folder now
- `restoreBackupFile()` — reads a backup file and merges it in — records in the file win, nothing is wiped
- `restoreFromFolderBackup()` — restores from the automatic copy in the profile folder — offered only when the browser holds no jobs; also asks for write permission and turns automatic backup back on
- `runAnalysis()` — grades the pending postings
- `saveApiKey()` — stores the Anthropic key in this browser
- `saveBackupFile()` — writes every record to one backup file and downloads it
- `saveDoc()` — writes the open document back to the record
- `saveNotes()` — writes the notes field back to the record
- `setBucket()` — switches the visible bucket tab
- `setInterviewDate()` — writes `interview_date` from the inline picker
- `setResponseStatus()` — writes `response_status`, which is what regroups an applied row
- `setState()` — generic interface state change
- `showSettings()` — opens the Settings tab and clears its old messages — also what every "Settings tab" link in the requirements box does
- `sortBy()` — sorts the list by a column
- `startEditing()` — opens the rich-text editor on a document
- `turnOnAutoBackup()` — asks Chrome for permission to write to the profile folder; granted, it writes a first copy and every later change is copied there; refused, the screen says so
- `updateJobField()` — writes one field on one record

---

## Calls to Anthropic

| | |
|---|---|
| Endpoint | `https://api.anthropic.com/v1/messages` |
| Model | `claude-opus-5` |
| Grader output ceiling | `8000` tokens |

The user's own key is sent from the browser with the direct-browser-access header.
Grading and each document are separate calls.

**The grader is asked to return exactly these fields:**

`company`, `title`, `location`, `salary`, `verdict`, `qualFit`, `envFit`, `qualItems`, `envItems`, `flagCount`, `flagReason`, `openQuestions`, `portfolioGuidance`.

A field not in that list is not something the grader produces.

---

## Gaps in this generation

None. Every section above was extracted successfully.
