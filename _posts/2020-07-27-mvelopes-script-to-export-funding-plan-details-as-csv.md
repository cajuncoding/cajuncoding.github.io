---
published: true
layout: post
title: How to Export your Monthly Funding Plan details as CSV from the Mvelopes 5 UI
subtitle: Addressing Mvelopes 5 issues with some JS hackery...
cover-img: /assets/img/crawfish-banner.jpg
thumbnail-img: >-
  /assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/mvelopes-to-ynab-thumbnail.png
share-img: >-
  /assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/mvelopes-to-ynab-thumbnail.png
tags:
  - mvelopes
  - finances
  - budget
  - hackery
  - javascript
---
One of the many-many frustrating issues with the Mvelopes 5 budgeting platform has been how the Funding Plan screen is plagued with bugs and useability issues...

## Monthly Funding Plan bugs/issues:
- The **Planning -> Income** panel has forgotten/lost my income amounts and payment dates on multiple occasions.
- Making a change to any income amount (**Planning -> Income**) causes ALL related funding plan details, from that income, to be deleted without warning; move over to the Monthly Funding tab and lots of data is just gone.
- There's no way to export or back-up this data in Mvelopes (nor in Mvelopes v4 either).
- Theres soo much white-space waste, and the UI uses only part of the screen real estate available; causing incessant scrolling.

## My time is more valuable, to me, than this tool thinks...
Isn't the purpose of paying for Mvelopes (or software in general) to minimize the headache and time I spend mucking with my budget info.?

None-the-less, these issues have meant that I've spend inordinate amounts of time doing one or more of the following:
1. Figuring out what I want my budget to be all-over-again.
2. Or, if I only changed one income, I can re-enter what the newly zeroed-out funding plan items should be by matching the *Remaining Column* which does the math for me (Thanks, that's sorta helpful).
3. Maintain all updates in duplication in a separate spreadsheet/file so I always have a backup...
   - *I admit that this just stopped getting done a while back...*

## A better way to back-up the Mvelopes Monthly Funding Plan details...
I should have been asking myself, "Ok, how can I waste less time on the *Mvelopes Monthly Funding Plan* in the future?" Well, for some reason that thought just occurred to me now since I'm going to try migrating over to YNAB.

As part of the migration, I need to ensure I've got an updated backup of the *Monthly Funding Plan* details, and the thought of manually writing down my full plan (which is probably unnecessarily detailed; but it works for me) made me cringe. That's when the thought of fixing this with some JS hackery occurred! 

This blog post is because just-maybe one other person might stumble upon this and find it helpful.

So here's a simple JS script that will parse the screen and export the key data from the Mvelopes 5 *Monthly Funding Plan* screen into CSV format that can be saved/imported into Excel:

```javascript
var rows = document.querySelectorAll("form div.table-row");
var category = rows[0].parentElement.querySelector("div.table-caption").innerText.trim();

var csvLines = Array.from(rows).map(row => {
	var fields = Array.from(row.querySelectorAll("div.text")).map(field => {
		var inputFields = field.getElementsByTagName("input");
		return '"' + (inputFields.length > 0 ? inputFields[0].value : field.innerText).trim() + '"';
	});
	return fields.join(',');
});
var csv = csvLines.join("\n");
console.log("CSV CONTENT is Below, Use the Copy Link to bring it into Excel!");
console.log(csv);
```
#### A thought on Browser Support for this JS snippet -- just one browser will be tested:
**NOTE:** This has only been tested in the latest version of **Chrome Browser**, it will likely work in others but may need modification to do so.  This was intended to be a stop-gap solution and since I only use the latest Chrome browser, that's all I plan on testing with.

### Running the script...
If you know your way around Chrome Browser and/or a Console window then skip the rest, otherwise here's how to run it in Chrome Browser:

1. First open Mvelopes in Chrome and navigate to the Monthly Funding Plan window
2. Open up the Chrome Console window by:
   - Pressing **F12**
   - Or, by pressing **Ctrl + Shift + I**
   - Or, by selecting **More Tools -> Developer Tools** from the menu
3. Go to the Console Window
4. Paste in the Script from above, and you're ready to run it:  
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/script-executed-with-csv-results.png " class="fullsize" data-zoomable />
5. Now you can scroll down, and use the handy-dandy Copy function that Chrome browser offers (or whatever other copy mechanism other browsers provide/allow):
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/script-executed-with-csv-results.png " class="fullsize" data-zoomable />

### Seeing the results:
1. **Once run, the results will be written to the Console Log as a single CSV output:**
  
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/script-executed-with-csv-results.png " class="fullsize" data-zoomable />  

2. Finally scroll down to the bottom and use the **Copy** link available in Chrome to copy the text...
   
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/script-executed-copy-results.png " class="fullsize" data-zoomable />  

3. The CSV data is now on the Clipboard... you can paste directly into [Excel (via Text Import Wizard)](#excel-text-import-wizard) or into [a CSV file](#notepad-csv-file) and then open in Excel; or save however you like for use in other applications.

<a name="excel-text-import-wizard">
### Paste directly into Excel via *Text Import Wizard*:
1. Open a new Workbook in Excel...
2. Use the Copy link in Chrome to copy the CSV results
3. Now you can use the **Import Text Wizard** to paste the value right into excel:
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/excel-paste-special-import-text-wizard.png " class="fullsize" data-zoomable />
   - **NOTE:** If you don't see this option, then re-copy the value from Chrome and then immediately click Paste Special in Excel and it should find the value on the clipboard and show the import wizard option.  

4. Choose **Delimited** for the type of data and click **Next**:
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/excel-import-wizard-delimited-selection.png " class="fullsize" data-zoomable />  

5. Select **Comma** as the delimiter, and click **Finish**:
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/excel-import-wizard-comma-delimiter-selection.png " class="fullsize" data-zoomable />  

6. You've now dynamically imported the data into Excel (which should *auto-magically* detect the numeric fields):
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/excel-import-wizard-results.png " class="fullsize" data-zoomable />

<a name="notepad-csv-file">
### Alternatively, Save Results to CSV File:
1. Paste the data into Notepad (use any raw text editor you like, but notepad works just fine)
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/paste-content-into-notepad.png " class="fullsize" data-zoomable />  

2. Save as a '**csv**' file
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/notepad-save-as-csv-file-dialog.png " class="fullsize" data-zoomable />  

3. Now you may open this CSV file directly up in Excel:
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/csv-file-open-in-excel.png " class="fullsize" data-zoomable />

4. **Format, add headers, etc. as desired...**

**Congrats, You now have a full backup of the details taken directly from the Mvelopes 5 UI/screen via some JS hackery!**
