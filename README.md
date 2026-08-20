# Job Applyr Lite

A single-page tool that grades job postings against your own criteria, then drafts a tailored résumé and cover letter for the ones worth applying to.

**Open it here: https://1benbcraig.github.io/job-applyr/**

Nothing to install. It runs entirely in your browser.

---

## What it does

1. Paste job postings in.
2. Each one is graded against your own criteria — qualification fit, environment fit, and a verdict with reasoning.
3. Jobs sort into **Ready to Apply**, **Flagged**, and **Rejected**.
4. Jobs you pursue get a tailored résumé, a cover letter in your own voice, and portfolio case-study suggestions.
5. Edit any document in place and export it to PDF or Word.
6. Track what you applied to, and whether you heard back — no response, interview scheduled, or rejected.

---

## Setup — once, about five minutes

### 1. Make your profile folder

Create a folder anywhere on your computer — the Desktop is fine — holding five markdown files:

| File | What goes in it |
|---|---|
| `candidate_profile.md` | Your criteria: salary floor, remote requirements, seniority you will and won't take, what you want and what you don't. **This is the rule set the grader reasons over.** |
| `master_resume.md` | Your full résumé in markdown. Tailored versions are drawn from it. |
| `voice_samples.md` | A few paragraphs you actually wrote, so cover letters sound like you rather than like a machine. |
| `ai_experience.md` | What you can honestly claim about AI tools — no more, no less. |
| `portfolio_case_studies.md` | One line per case study: `- **Name** — one-sentence description`. A short index, not the full write-up. |

Only `candidate_profile.md` is required to start. The rest can come later; the app will name any that are missing.

**Keep this folder out of any folder you publish or sync publicly.** Its contents never leave your machine.

### 2. Add an Anthropic API key

Open the app, go to the **Upload** tab, and follow **"One-time setup: add an API key."** You can create a key at [console.anthropic.com](https://console.anthropic.com).

The key is stored in your browser only. It is sent to Anthropic's API and nowhere else. You pay Anthropic directly for what you use — grading a posting costs a few cents, and a full package with both documents is around fifteen cents.

### 3. Point the app at your profile folder

On the **Upload** tab, click **Choose folder** and select the folder from step 1. Your browser will ask permission once.

After that the app re-reads those files every time it launches, so editing your criteria in a text editor changes the very next grading. You never paste your profile into the app.

You will see a line reading `Profile: your-folder-name`. If it ever reads **"— using the last copy read, not the folder itself,"** the folder permission has lapsed and the app is running on its cached copy. Click **Reload** to fix it.

---

## Your data

- **Your profile, postings, documents, and API key stay in your browser and on your machine.** There is no server and no account.
- **This repository contains the app and nothing else.** No personal data of any kind is in it.
- Postings you grade are sent to Anthropic's API to be graded, and nowhere else.

---

## Using it for yourself

The app carries no personal information — every candidate-specific value is read from your profile folder at launch. To use it as your own, open the link and do the three setup steps above. To run your own copy, take `index.html`, publish it anywhere that serves a normal web page, and point it at your own profile folder.

**A note on where you open it from.** Open the app from a real web address, not by double-clicking a downloaded copy. A page opened as a local file cannot hold on to folder permission between launches, so it will ask again on every visit.

---

## Requirements

A Chromium-based browser — Chrome or Edge. Reading the profile folder relies on the File System Access API, which those browsers support. Everything else works anywhere.
