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

# Quick Start — Copy This Code

If you just want to use the checker, you do **not** need to download anything.

1. Open Prometric and go to the appointment search page.
2. Choose your location/date search options normally.
3. Press **Ctrl + Shift + J** in Chrome or Microsoft Edge to open the browser Console. You can also press **F12** and click **Console**.
4. Click the **Copy** button in the top-right corner of the code box below to copy the entire script.
5. Paste it into the Console and press **Enter**.
6. Leave the Prometric tab open.

```javascript
(() => {
    const SEARCH_INTERVAL = 2000; // Search every 2 seconds
    const RESULTS_WAIT = 1000;    // Wait 1 second for results to update

    let busy = false;
    let finished = false;

    const sleep = ms => new Promise(resolve => setTimeout(resolve, ms));

    function playAlarm() {
        try {
            const AudioContextClass = window.AudioContext || window.webkitAudioContext;
            const audioContext = new AudioContextClass();
            if (audioContext.state === "suspended") audioContext.resume();

            // Play 8 short beeps
            for (let i = 0; i < 8; i++) {
                setTimeout(() => {
                    const oscillator = audioContext.createOscillator();
                    const gain = audioContext.createGain();
                    oscillator.type = "sine";
                    oscillator.frequency.value = 1000;
                    gain.gain.setValueAtTime(0.35, audioContext.currentTime);
                    oscillator.connect(gain);
                    gain.connect(audioContext.destination);
                    oscillator.start();
                    oscillator.stop(audioContext.currentTime + 0.25);
                }, i * 350);
            }
        } catch (error) {
            console.error("Could not play alarm:", error);
        }
    }

    function noAvailabilityBoxExists() {
        return [...document.querySelectorAll('[role="alert"] h2')]
            .some(element => element.textContent.trim() === "Sorry No Availability Found");
    }

    async function waitForElement(getElement, timeout = 10000) {
        const startTime = Date.now();
        while (Date.now() - startTime < timeout) {
            const element = getElement();
            if (element) return element;
            await sleep(250);
        }
        return null;
    }

    function findFirstDateCard() {
        return document.querySelector('[role="radio"].date-card');
    }

    function findFirstTimeButton() {
        const timePattern = /\b(?:0?[1-9]|1[0-2]):[0-5]\d\s*(?:AM|PM)\b/i;
        const timeSection = document.querySelector("app-slot-card-detail");
        if (!timeSection) return null;

        const possibleElements = [...timeSection.querySelectorAll(
            'button, [role="button"], [role="radio"], .btn, [tabindex]'
        )];

        return possibleElements.find(element => {
            const text = element.textContent.trim();
            const visible = element.offsetParent !== null;
            const enabled = !element.disabled && element.getAttribute("aria-disabled") !== "true";
            return visible && enabled && timePattern.test(text);
        });
    }

    function findNextButton() {
        const button = document.querySelector(
            'button.tempSucBtn.tempSucBtn-nbme[aria-label="Continue to next page"]'
        );
        if (button && !button.disabled && button.getAttribute("aria-disabled") !== "true") {
            return button;
        }
        return null;
    }

    async function selectAvailableAppointment() {
        console.log("Availability detected.");
        playAlarm();

        const dateCard = await waitForElement(findFirstDateCard, 10000);
        if (!dateCard) {
            console.log("Date card was not found.");
            return false;
        }
        dateCard.click();
        console.log("Clicked date:", dateCard.getAttribute("aria-label") || dateCard.textContent.trim());

        const timeButton = await waitForElement(findFirstTimeButton, 10000);
        if (!timeButton) {
            console.log("Time button was not found.");
            return false;
        }
        timeButton.click();
        console.log("Clicked time:", timeButton.textContent.trim());

        const nextButton = await waitForElement(findNextButton, 10000);
        if (!nextButton) {
            console.log("Enabled Next button was not found.");
            return false;
        }
        nextButton.click();
        console.log("Clicked Next.");

        finished = true;
        clearInterval(window.prometricChecker);
        console.log("Appointment selected. Automation stopped.");
        return true;
    }

    async function runCheck() {
        if (busy || finished) return;
        busy = true;

        try {
            const searchButton = document.getElementById("searchBtn");
            if (!searchButton || searchButton.disabled || searchButton.getAttribute("aria-disabled") === "true") {
                console.log("Search button is unavailable.");
                return;
            }

            searchButton.click();
            console.log("Search clicked:", new Date().toLocaleTimeString());
            await sleep(RESULTS_WAIT);

            if (noAvailabilityBoxExists()) {
                console.log("No availability.");
                return;
            }

            await selectAvailableAppointment();
        } catch (error) {
            console.error("Automation error:", error);
        } finally {
            busy = false;
        }
    }

    // Stop any older checker using the same variable
    clearInterval(window.prometricChecker);

    window.prometricChecker = setInterval(runCheck, SEARCH_INTERVAL);
    console.log("Prometric checker started. Searching every 2 seconds.");
})();
```

After pressing Enter, you should see:

`Prometric checker started. Searching every 2 seconds.`

If your browser warns you about pasting code into Developer Tools, read the warning carefully. Only paste code that you understand and trust.

## Stop the checker

To stop it manually at any time, copy this line, paste it into the same Console, and press **Enter**:

```javascript
clearInterval(window.prometricChecker);
```

# Detailed instructions for beginners

## 1. Open Prometric

Go through the Prometric scheduling process normally until you reach the appointment search page where you can choose your search criteria and press the **Search** button.

Set the location, date range, or other search options you want **before starting the script**.

## 2. Open the browser Console

Keep the Prometric appointment-search page open.

In Google Chrome or Microsoft Edge on Windows, press:

`Ctrl + Shift + J`

Alternatively, press `F12`, then select the **Console** tab at the top of Developer Tools.

## 3. Copy the checker

The easiest method is to use the **Quick Start** code box near the top of this README. GitHub displays a copy button in the top-right corner of code blocks.

You can also open the separate `prometric-checker.js` file from the repository file list. Click the filename, then use GitHub's **Copy raw file** button near the top-right of the file viewer to copy the complete script.

## 4. Paste and start

Return to the Prometric tab. Click inside the Console, paste the code, and press **Enter**.

You should see:

`Prometric checker started. Searching every 2 seconds.`

Leave the Prometric tab open. The script will repeatedly press Search for you and check the results.

## 5. When an appointment is found

When the script detects availability, it will:

1. Play a series of beeps.
2. Select the first available date shown.
3. Select the first available time shown.
4. Click **Next**.
5. Stop the automatic checker.

Return to the Prometric page and review the appointment information yourself before completing any remaining steps.

## Files

### `prometric-checker.js`
The main source-code file. It contains the same checker shown in the Quick Start section above.

### `stop-checker.js`
Contains the one-line command used to stop the automatic checker manually.

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
