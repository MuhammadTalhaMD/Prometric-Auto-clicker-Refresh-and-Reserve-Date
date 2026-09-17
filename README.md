# Prometric Appointment Auto Checker

A simple browser-console JavaScript tool that repeatedly checks the Prometric appointment search page for availability.

It is intended to be easy to use even if you have no programming experience.

## What it does

- Automatically clicks the Prometric **Search** button every 2 seconds.
- Checks whether Prometric is showing **Sorry No Availability Found**.
- When an appointment becomes available, it selects the first available date.
- Selects the first available appointment time.
- Clicks **Next** to continue to the next Prometric page.
- Plays 8 beeps to alert you that availability was found.
- Stops automatically after selecting the appointment and clicking Next.

> **Important:** The script itself does not create a 15-minute reservation timer. If Prometric temporarily holds an appointment after you proceed, that hold and its duration are controlled by Prometric.

## Files

### `prometric-checker.js`
This is the main script. This is the code you copy and paste into your browser Console to start checking.

### `stop-checker.js`
This contains a one-line command that stops the automatic checker manually.

## How to use it — no programming knowledge required

### 1. Open Prometric

Go through the Prometric scheduling process normally until you reach the appointment search page where you can choose your search criteria and press the **Search** button.

Set the location, date range, or other search options you want **before starting the script**.

### 2. Open `prometric-checker.js` on GitHub

In this repository, click the file named:

`prometric-checker.js`

Copy **all** of the code inside the file.

### 3. Open your browser Console

Keep the Prometric appointment-search page open.

In Google Chrome or Microsoft Edge on Windows, press:

`Ctrl + Shift + J`

You can also press `F12` and then click the **Console** tab.

### 4. Paste the script

Click inside the Console, paste the entire `prometric-checker.js` code, and press **Enter**.

If your browser displays a warning about pasting code into Developer Tools, read the browser warning carefully and follow its instructions only if you understand and trust the code you are pasting.

### 5. Confirm that it started

You should see a message similar to:

`Prometric checker started. Searching every 2 seconds.`

Leave the Prometric tab open.

The script will repeatedly press Search for you and check the results.

### 6. When an appointment is found

When the script detects availability, it will:

1. Play a series of beeps.
2. Select the first available date shown.
3. Select the first available time shown.
4. Click **Next**.
5. Stop the automatic checker.

At this point, return to the Prometric page and review the appointment information yourself before completing any remaining steps.

## How to stop the checker manually

If you want to stop it before an appointment is found, paste this into the Console and press **Enter**:

```javascript
clearInterval(window.prometricChecker);
```

This is also stored in `stop-checker.js`.

## If you accidentally run the main script twice

The script first stops an older checker before starting a new one, so you should not normally end up with multiple copies of the checker running in the same page.

## Important notes

- Keep the Prometric page open while the checker is running.
- Do not close or refresh the page unless you want to stop the current script session.
- The script selects the **first available date and first available time** it detects. It does not currently choose a specific preferred date or time.
- Prometric can change its website at any time. If the page layout, buttons, or labels change, the script may stop working and need to be updated.
- Automated or frequent requests may be restricted by a website's terms, rate limits, or anti-bot systems. Use this tool responsibly and check Prometric's current rules before using automation.
- Never paste browser-console code from a source you do not trust. Console scripts run inside the website currently open in your browser.

## Browser compatibility

The script is designed for modern desktop browsers such as Google Chrome and Microsoft Edge. Other browsers may behave differently.

## Disclaimer

This is an independent personal utility and is not affiliated with, endorsed by, or supported by Prometric. Availability, appointment holds, reservation duration, scheduling rules, and final booking confirmation are controlled entirely by Prometric.
