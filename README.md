# Low Attendance WhatsApp Alert (n8n)

An n8n workflow that reads student attendance from a Google Sheet and sends a WhatsApp template message (Meta WhatsApp Business Cloud API) to the parents of students whose attendance is below a threshold (default 50%). After each message it writes the result back to the sheet.

## Workflow

`workflows/low_attendance_alert_production.n8n.json`

```
Daily 6 PM / Manual Test
  -> Config
  -> Read Sheet
  -> Filter Low Attendance (count P/A, calculate %, repeat-gap check, clean phone number)
  -> Valid number?
       |- yes -> Send WhatsApp -> Result -> Sent OK? -> Mark Sent / Mark Failed
       |- no  -> Mark Invalid Number
```

## Setup

1. In n8n, use **Import from File** and select the workflow JSON.
2. Fill in the **Config** node:
   - `sheetId`: Google Sheet ID
   - `phoneNumberId`: Meta WhatsApp Phone Number ID
   - `templateName` / `languageCode`: your approved template (default `low_attendance_alert_hi`, `hi`)
3. Create credentials in n8n (never commit them to this repo):
   - **Google Sheets OAuth2**
   - **Header Auth** (Name: `Authorization`, Value: `Bearer <PERMANENT TOKEN>`)
4. Add two plain-text columns at the end of the sheet: `Last Alert Date` and `Alert Status`.
5. Run **Manual Test** to verify, then publish the workflow.

## Sheet format

- A `Name` column and a `Contact` column (the header must contain "name" and "contact").
- One column per date, with the value `P` (present) or `A` (absent).
- Adding a new date column is picked up automatically.

## Config options

| Key | Meaning |
|---|---|
| `threshold` | Alert when attendance is below this % |
| `minDays` | Minimum days of data required before alerting |
| `repeatEveryDays` | How often (in days) the same parent can be alerted |
| `mode` | `template` (live) or `text` (testing only) |

## Notes

- Timezone: Asia/Kolkata.
- Phone numbers are converted to `91XXXXXXXXXX` format.
- On failure, the sheet's `Alert Status` shows the Meta error code with a short hint (for example, 190 = token expired).
- This repo contains no tokens or credential IDs.
