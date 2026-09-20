# Study Schedule

A private, responsive 87-study-day calendar that synchronizes progress through
an existing Supabase project. The frontend is a single static file built with
HTML, CSS, vanilla JavaScript, and Supabase JavaScript v2 from a CDN.

## Project files

- `index.html` - complete application, styling, authentication, and sync logic
- `README.md` - usage and GitHub Pages deployment instructions

There is no npm install, build command, or backend server.

## Existing Supabase setup

`index.html` is already configured with the supplied Supabase Project URL and
publishable key. It uses the existing `study_schedule_state` table and existing
Row Level Security policies. It does not create, alter, or drop database
objects.

The application provides **Sign In only**. Create and manage the permitted user
directly in the Supabase dashboard under **Authentication > Users**. There is no
public registration screen in the website.

The publishable key is intended to be visible in browser source. The existing
RLS policies must restrict each authenticated user to their own row. Never put a
`service_role` key or any Supabase secret key in `index.html` or GitHub; those
keys can bypass RLS.

## Run locally

You can open `index.html` directly in a modern browser. For behavior closer to
GitHub Pages, serve the repository with any simple static-file server and open
the generated local URL. No compilation is needed.

Sign in with the email and password of the existing Supabase Auth user. The
Supabase client persists the session, so a returning user normally does not
need to sign in again.

## Deploy to GitHub Pages

1. Create a GitHub repository.
2. Add `index.html` and `README.md` to the repository root.
3. Push the files to the `main` branch.
4. Open the repository's **Settings > Pages**.
5. Under **Build and deployment**, choose **Deploy from a branch**.
6. Select branch **main** and folder **/ (root)**.
7. Click **Save**.
8. Open the generated URL, such as
   `https://USERNAME.github.io/study-schedule/`.

Everything is embedded in `index.html`, and the app uses no root-relative asset
paths, so it works from a GitHub Pages repository subdirectory.

## Supabase URL configuration

In Supabase, open **Authentication > URL Configuration** and set the Site URL to
the real GitHub Pages address, for example:

`https://USERNAME.github.io/study-schedule/`

Add the same address to the Redirect URLs list. Replace the example username
and repository name with the deployed values.

## How synchronization works

- The browser restores the authenticated user's local cache for a fast render.
- Supabase is then queried for that user's `study_schedule_state` row.
- The newest valid state is used based on `updated_at`.
- Online changes are written to Supabase immediately.
- Offline changes are saved locally with a pending-sync flag.
- On reconnect, the cloud row is fetched and compared before an upload.
- The app refreshes cloud state when it becomes visible or focused, with a
  request guard to avoid excessive queries.

Local cache keys are scoped by authenticated user ID, preventing one user's
cached schedule from appearing for another account on the same browser.

## Schedule behavior

- The fixed study plan contains 87 Study Days: 51 lecture days and 36 old-question days.
- Each chapter's old-question days begin immediately after its lecture days.
- Skipping a calendar date does not consume a Study Day number.
- Every later Study Day shifts forward when a date is skipped.
- Completed status is stored by Study Day number, not calendar date.
- Undoing a skip regenerates the complete schedule.
- Changing the start date retains completed Study Day numbers and removes
  skipped dates that no longer fall within the generated schedule.
- Reset clears skipped dates and completed days without deleting the account or
  database row.

The sync indicator reports **Synced**, **Syncing...**, **Offline**, or
**Sync Error**.
