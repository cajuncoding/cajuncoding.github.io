---
published: true
layout: post
title: How to Export your Funding Plan Details as CSV from the Mvelopes 5 UI
subtitle: >-
  Addressing Mvelopes 5 issues with some JS hackery...
cover-img: /assets/img/homeassistant-banner.png
thumbnail-img: /assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/mvelops-to-ynab-thumbnail.png
share-img: /assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/mvelops-to-ynab-thumbnail.png
tags:
  - mvelopes
  - finances
  - budget
  - hackery
---
One of the many-many frustrating issues with Mvelopes 5 budgeting platform has been how the Funding Plan screen is plagued with bugs...

<img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/mvelops-to-ynab-thumbnail.png " class="fullsize" data-zoomable />

## Monthly Funding Plan bugs:
- The **Planning -> Income** panel has forgotten/lost my income amounts and payment dates on multiple occassions.
- Making a change to any income amount (**Planning -> Income**) causes ALL related funding plan details, from that income, to be deleted without warning; move over to the Monthly Funding tab and lots of data is just gone.
- There's no way to export or back-up this data in Mvelopes (nor in Mvelopes v4 either).

## What a waste of my valuable time...
I mean isn't the purpose of paying for Mvelopes (or software in general) to minimize the headache and time I spend mucking with my budget info.?

None-the-less, these issues have meant that I've spend inordinate amounts of time doing one or more of the following:
1. Figuring out what I want my budget to be all-over-aagain
2. Or, if I only changed one income, I can re-enter what the newly zeroed-out funding plan items should be by matching the *Remaining Column* which does the math for me (Thanks, that's sorta helpful).
3. Maintain all updates in duplication in a separate spreadsheet/file so I always have a backup...
   - *I admit that this just stopped getting done a while back...*

## A better way to back-up the Mvelopes Montly Funding Plan details...
You might be asking "Ok, how can I waste less on the Mvelopes Monthly Funding Plan time in the future?"

Well, for some reason the thought just occurred to me now that I'm going to try migrating over to YNAB. I realized that I really want an updated backup of this and the thought of crawing my full plan (which is probably unnecessarily detailed; but it works for me) made me cringe. Followed, quickly by the though of fixing this with some JS hackery! 

And this blog is because just-maybe one other person might stumble upon this and find it helpful.

So here's a simple JS script that will parse the screen and export the key data from the Mvelopes 5 monthly funding plan screen into CSV format that can be saved/imported into Excel:

```javacript
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

### Running the script...
If you know your way around Chrome Browser and/or a Console window then skip the rest, otherwise here's how to run it in Chrome Browser:

1. First open Mvelopes in Chrome and navigate to the Monthly Funding Plan window
2. Open up the Chrome Console window by:
   - Pressing **F12**
   - Or, by pressing **Ctrl + Shift + I**
   - Or, by selecting **More Tools -> Developer Tools** from the menu
3. Go to the Console Window
4. Paste in the Script from above, and you're ready to run it:  
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/paste-script-ready-to-run.png " class="fullsize" data-zoomable />  

### Paste directly into Excel via *Text Import Wizard*:
1. Open a new Workbook in Excel...
2. Use the Copy link in Chrome to copy the CSV results
3. Now you can use the **Import Text Wizard** to paste the value right into excel:
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/excel-paste-special-import-text-wizard.png " class="fullsize" data-zoomable />
   - **NOTE:** If you don't see this option, then re-copy the vlaue from Chrome and then immediately click Paste Special in Excel and it shoudl find the value on the clipboard and show the import wizard option.  

4. Choose **Delimited** for the type of data and click **Next**:
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/excel-import-wizard-delimited-selection.png " class="fullsize" data-zoomable />  

5. Select **Comma** as the delimiter, and click **Finish**:
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/excel-import-wizard-comma-delimiter-selection.png " class="fullsize" data-zoomable />  

6. You've now dynamically imported the data into Excel (which should automagically detect the numeric fields):
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/excel-import-wizard-results.png " class="fullsize" data-zoomable />

### Alternatively, Save Results to CSV File:
1. **Once run, the results will be written to the Console Log as a single CSV output:**
  
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/script-executed-with-csv-results.png " class="fullsize" data-zoomable />  

2. Finally scroll down to the bottom and use the **Copy** link availabe in Chrome to copy the text...
   
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/script-executed-copy-results.png " class="fullsize" data-zoomable />  

3. Paste the data into Notepad (use any raw text editor you like, but notepad works just fine)
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/paste-content-into-notepad.png " class="fullsize" data-zoomable />  

4. Save as a '**csv**' file
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/notepad-save-as-csv-file-dialog.png " class="fullsize" data-zoomable />  

5. Now you may open this CSV file directly up in Excel:
   - <img src="../assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/csv-file-open-in-excel.png " class="fullsize" data-zoomable />

6. **Format, add headers, etc. as desired...**

**Congrats, You now have a full bakckup of the details taken directly from the Mvelopes 5 UI/screen via some JS hackery!**