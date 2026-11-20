# Low Attendance WhatsApp Alert (n8n)

Google Sheet se students ki attendance padhkar, jinki attendance threshold (default 50%) se kam hai, unke parents ko WhatsApp Business Cloud API (Meta) se Hindi template message bhejta hai. Har message ke baad sheet mein status likhta hai.

## Workflow

`workflows/low_attendance_alert_production.n8n.json`

```
Daily 6 PM / Manual Test
  -> Config
  -> Read Sheet
  -> Filter Low Attendance (P/A count, % nikalna, repeat gap check, phone saaf karna)
  -> Valid number?
       |- yes -> Send WhatsApp -> Result -> Sent OK? -> Mark Sent / Mark Failed
       |- no  -> Mark Invalid Number
```

## Setup

1. n8n mein **Import from File** se workflow JSON import karo.
2. **Config** node mein bharo:
   - `sheetId`: Google Sheet ID
   - `phoneNumberId`: Meta WhatsApp Phone Number ID
   - `templateName` / `languageCode`: approved template (default `low_attendance_alert_hi`, `hi`)
3. Credentials banao (secrets repo mein kabhi commit na karein):
   - **Google Sheets OAuth2**
   - **Header Auth** (Name: `Authorization`, Value: `Bearer <PERMANENT TOKEN>`)
4. Sheet ke end mein do plain text columns banao: `Last Alert Date`, `Alert Status`.
5. **Manual Test** se check karo, phir workflow ko Publish karo.

## Sheet format

- `Name` aur `Contact` columns (naam mein "name" aur "contact" hona chahiye).
- Har date ka ek column, value `P` (present) ya `A` (absent).
- Naya date column add karte hi count apne aap badh jata hai.

## Config options

| Key | Matlab |
|---|---|
| `threshold` | Is % se kam attendance par alert |
| `minDays` | Kam se kam itne din ka data ho tabhi alert |
| `repeatEveryDays` | Ek parent ko kitne din mein ek baar alert |
| `mode` | `template` (live) ya `text` (sirf test) |

## Notes

- Timezone: Asia/Kolkata.
- Phone numbers `91XXXXXXXXXX` format mein convert hote hain.
- Failed status mein Meta error code aur hint likha jata hai (jaise 190 = token expire).
- Is repo mein koi token ya credential ID nahi hai.
