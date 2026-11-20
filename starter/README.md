# Workout Routine starter kit

Your own copy of the setup: a workout app on your phone, a Google Sheet that holds your program and your logs, and (optional) a Sunday task in Claude that plans the week ahead.

- **The app** shows today's workout with loads worked out from your training maxes. It runs your rest and WOD timers, logs every set, and keeps your history and estimated maxes.
- **The Sheet** is where you write the program (weeks, lifts, exercises, loads). The app reads it every time you open it and writes what you log back to it.
- **The Sunday task** reads last week's log, fills in next week's target weights, puts your lifts on your calendar and sends you a short summary.

Setup takes about 30 minutes. You need a Google account, a free GitHub account and a phone. The Sunday task needs Claude with the Google Calendar and Google Sheets connectors.

What's in this folder:

| File | What it's for |
|---|---|
| `app/index.html` | The app. One file, with your settings at the top |
| `app/lifter-icon-v2.png` | The Home Screen icon |
| `workout-sync.gs` | The script that connects your Sheet to the app |
| `sunday-task.txt` | The Sunday scheduled task, with blanks to fill in |

## 1. Copy the Sheet

Open the template link you were sent (it ends in `/copy`) and click **Make a copy**. Rename it if you like. Its **Start here** tab repeats steps 2 and 3.

## 2. Add the sync script

1. In your copy: **File → Settings → Time zone**, pick yours, **Save settings**.
2. **Extensions → Apps Script**. Delete everything in the editor and paste in all of [`workout-sync.gs`](workout-sync.gs).
3. Change the passcode line to a word only you know, keeping the quotes:
   ```js
   const PASSCODE = 'your-own-passcode';
   ```
   The script refuses to connect until you change it from `change-this-passcode`.
4. Click **Save** (the disk icon).

## 3. Deploy it

1. **Deploy → New deployment**. Click the gear next to "Select type" and choose **Web app**.
2. Execute as: **Me**. Who has access: **Anyone**. Click **Deploy**.
3. Google asks you to authorize. Pick your account. On "Google hasn't verified this app", click **Advanced → Go to (your project) (unsafe)**, then **Allow**. It's your own script, so this is expected.
4. Copy the **Web app URL**. It ends in `/exec`.

To check it, open that URL in a browser: it should say `{"ok":true,"app":"Workout Routine sync"}`.

If you ever change the script: **Deploy → Manage deployments → pencil icon → Version: New version → Deploy**. The URL stays the same.

## 4. Put the app on GitHub

1. Download the two files in the [`app`](app) folder: open each one and click the download icon (**Download raw file**).
2. On GitHub, create a new repository (**+ → New repository**). Name it anything, for example `workout`. Leave it **Public**; free GitHub Pages needs that, and your passcode never goes in the files. Click **Create repository**.
3. On the new repo's page, click **uploading an existing file**, drag in both files and click **Commit changes**.
4. **Settings → Pages**. Under "Build and deployment", Source: **Deploy from a branch**, Branch: **main**, folder **/ (root)**. Click **Save**.
5. After a minute or two your app is at `https://<your-github-username>.github.io/<repo-name>/`.

## 5. Put in your settings

In your repo, open `index.html` and click the pencil icon to edit it. At the very top is a block marked **YOUR SETTINGS**. It's the only part you need to change:

```js
window.APP_SETTINGS = {
  appName: "Strong",                       // the name under the Home Screen icon
  syncUrl: "https://script.google.com/macros/s/AKfy.../exec",   // your Web app URL from step 3
  gym: "Iron Works",                       // travel days say "Away from Iron Works"
  classes: [                               // classes you take every week, or []
    { day: 2, name: "Spin", time: "6:00 PM", where: "Iron Works" }
  ],                                       // day: 0 = Mon, 1 = Tue ... 6 = Sun
  restDay: 6,                              // your day off (6 = Sunday), or null
  weighInNote: "Same scale, same morning.",
  trainingMax: { squat: 275, bench: 205, trap: 365, press: 135, clean: 165 },
  oneRepMax:   { squat: 300, bench: 225, trap: 405, press: 150, clean: 185 }
};
```

- `trainingMax` drives every % load in the program: back squat, bench press, trap bar deadlift, strict press and power clean, in lb. A good training max is about 90% of a 1-rep max you could hit today. If you don't know your maxes, guess low; the test week resets them.
- `oneRepMax` is for reference on the Maxes tab.
- You can change training maxes later on the app's **Maxes** tab; those edits are saved on your phone.

Click **Commit changes**. Never put your passcode in this file. The app asks for it on your phone instead.

## 6. Add it to your phone

- iPhone: open your app's address in **Safari**, tap **Share → Add to Home Screen**.
- Android: open it in **Chrome**, tap **⋮ → Add to Home screen**.

Open it from the Home Screen icon. It already works: it comes with the same 4-week starter program as the Sheet.

## 7. Connect the Sheet

Tap **Connect Sheet** at the top and type your passcode. From then on, the app takes its program from the Sheet and sends everything you log to it, including anything you logged before connecting.

## 8. Make the program yours

Everything lives on the Sheet. Hover over any column header there to see what goes in it.

- **Program · Weeks**: set **Starts** on week 1 to the Monday you begin. Later weeks follow on their own. The week whose **Phase** says "Test" is the test week.
- **Program · Lifts**: your four lifts, the day each one usually falls on, the time and the warm-up.
- **Program**: every exercise, week by week. Change exercises, sets, reps and loads freely. When you only tweak an exercise, keep its **ID** so its history carries over.

The app checks the Sheet every time you open it, so your edits show up on your phone right away.

**Next block:** when this block ends, replace the rows on Program · Weeks and Program with the new block's. Give it a new name in **Block** (for example "Winter"). The app files its log under the new name and starts fresh; the old logs stay in the Sheet.

## 9. The Sunday task (optional)

1. Open [`sunday-task.txt`](sunday-task.txt) and fill in the **YOUR DETAILS** block at the top: your name, time zone, app address, repo, Sheet ID (the long code in the Sheet's address, between `/d/` and `/edit`), calendars, gym and away dates.
2. In Claude, create a scheduled task that runs every Sunday evening, with the Google Calendar and Google Sheets connectors turned on, and paste in the prompt.
3. Run it once by hand to check it.

It only ever fills in empty Load cells for next week and adds a row to the **Runs** tab. Anything else it would change comes as a numbered suggestion in its summary, and nothing changes until you say so.

## Changing the app

It's your copy, so change anything you like: edit `index.html` in your repo, or ask Claude to. When you change it, raise the number in `var APP_VERSION = "28 · ..."` (search for `APP_VERSION`). The app on your phone checks that number when you open it and reloads itself when it goes up.

## If something's off

- **"Wrong passcode · tap to fix"**: the passcode you typed must match `PASSCODE` in the script exactly. If you changed the script, deploy a new version (end of step 3).
- **No Connect Sheet button**: `syncUrl` in your settings is empty or wasn't saved. It should be the Web app URL, ending in `/exec`.
- **An edit to the Sheet doesn't show up**: close the app fully and reopen it. Make sure the Week and Lift columns are filled in where a new workout starts.
- **A lift is on the wrong day**: change **Usual day** on Program · Lifts. To move just one week, set **Day** on that workout's first row on Program.
- **Logs aren't reaching the Sheet**: the app keeps everything on the phone and retries. If something's wrong, a "Not syncing · tap to retry" note appears at the top. The Sessions, Sets and Bodyweight tabs fill in on their own; don't edit them.
