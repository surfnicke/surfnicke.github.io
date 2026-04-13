# Google Apps Script Setup (No Backend)

Use this to append each watering log entry to a Google Sheet (a file in Google Drive).

## 1) Create a Google Sheet

1. Create a new Google Sheet.
2. Name the first sheet `Logs`.
3. Add header row in row 1:
   - `timestamp`
   - `name`

## 2) Create Apps Script

1. In the sheet, open `Extensions > Apps Script`.
2. Replace code with:

```javascript
function doPost(e) {
  try {
    var payload = JSON.parse(e.postData.contents || '{}');
    var timestamp = payload.timestamp || new Date().toISOString();
    var name = payload.name || '';

    if (!name) {
      return ContentService
        .createTextOutput(JSON.stringify({ ok: false, error: 'Name is required' }))
        .setMimeType(ContentService.MimeType.JSON);
    }

    var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Logs');
    if (!sheet) {
      sheet = SpreadsheetApp.getActiveSpreadsheet().insertSheet('Logs');
      sheet.appendRow(['timestamp', 'name']);
    }

    sheet.appendRow([timestamp, name]);

    return ContentService
      .createTextOutput(JSON.stringify({ ok: true }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ ok: false, error: String(err) }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

## 3) Deploy Web App

1. Click `Deploy > New deployment`.
2. Type: `Web app`.
3. Execute as: `Me`.
4. Who has access: `Anyone`.
5. Deploy and copy the web app URL.

## 4) Add URL in the site

1. Open `index.html`.
2. Find `GOOGLE_SCRIPT_URL`.
3. Replace placeholder with your deployed Apps Script URL.

## 5) Use the page

- New log entries are still saved locally in browser storage.
- Each new log is also sent to your Google Sheet via Apps Script.
