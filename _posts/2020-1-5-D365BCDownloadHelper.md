---
layout: post
title: Download Helper for Dynamics 365 Business Central
comments: true
---
![Sample output with Verbose-switch](/images/posts/2020-1-5-D365BCDownloadHelper_01-small)

We all know these situations: you have a simple requirement and wonder anyway why you can't find an easy-to-use solution to solve it. That's why I created a little PowerShell-module which I'll describe here.

## Requirement
I needed a simple way to retrieve the download link for a Dynamics 365 Business Central DVD ZIP file from Microsoft (like these [here](https://www.microsoft.com/en-us/download/details.aspx?id=58318)). I would have preferred an easy-to-use approach (similar to specifying `mcr.microsoft.com/businesscentral/onprem:cu3-nl` for a Docker image).

Maybe I was to impatient during my search or looked for the wrong keywords. But anyway I couldn't find anything. That's why I started with a little script, which became a slightly bigger script, which ultimately became the module I'm writing about here.

## Solution
Well, the solution is quite easy now 😊 Just install the module **D365BCDownloadHelper** in PowerShell like this:
```
Install-Module D365BCDownloadHelper
```

and then import it like this:
```
Install-Module D365BCDownloadHelper
```
and you're good to go 👍

The available CmdLets are the following:

`Get-BusinessCentralDownloadURL`: Returns the URI (string) for the desired version
* Parameters
  * Version (`integer`): defaults to "14"; possible values 13, 14, 15
  * CumulativeUpdate (`string`): defaults to "CU01"
  * Language (`string`): defaults to "W1"; values like Microsoft lists them (AT,AU,BE,CH,CZ,DE,DK,...)
* Output
  * URI  (`string`): the download link for the ZIP file

`Receive-BusinessCentralDVD`: Downloads the ZIP file for the desired version
* Parameters
  * Version (`integer`): defaults to "14"; possible values 13, 14, 15
  * CumulativeUpdate (`string`): defaults to "CU01"
  * Language (`string`): defaults to "W1"; values like Microsoft lists them (AT,AU,BE,CH,CZ,DE,DK,...)
  * DownloadDirectory (`string`): defaults to "C:\Install"
* Output
  * URI  (`string`): the FullPath for the downloaded file

`Expand-BusinessCentralDVD`: Extracts the ZIP file
* Parameters
  * ZipFilename (`string`): FullPath of the ZIP file
  * TargetDirectoryParent (`string`): e.g. "C:\Install"
  * TargetDirectoryName  (`string`): defaults to "DVD"  
* Output
  * none
* Remarks
  Some archives contain another ZIP file; this CmdLet will check if that's the case and also extract the inner ZIP (actually if that's the case, this one will be the only content remaining, the content from the parent ZIP will be deleted)

`Get-ReceiveAndExpandBusinessCentralDVD`: I think you can guess 😅 Not sure if I like the naming though 😉
* Parameters
  * Version (`integer`): defaults to "14"; possible values 13, 14, 15
  * CumulativeUpdate (`string`): defaults to "CU01"
  * Language (`string`): defaults to "W1"; values like Microsoft lists them (AT,AU,BE,CH,CZ,DE,DK,...)
  * DownloadDirectory (`string`): defaults to "C:\Install"
  * TargetDirectoryParent (`string`): e.g. "C:\Install"
  * TargetDirectoryName  (`string`): defaults to "DVD"  
* Output
  * none

## Remarks or: How does it work internally
Well, maybe it's a somewhat naive approach, but it is how it is and it's described here:
* Create a search query string based on the given information (e.g. `Get-BusinessCentralDownloadURL -Version 14 -CumulativeUpdate "CU01" -Language "W1"` will create the query `CU 01 Dynamics 365 BC Spring 2019 Update On Premise.zip site:microsoft.com`)
* Do a Google-search with the generated query string (using `Invoke-WebRequest`)
* Find a link to `https://www.microsoft.com/en-us/download/` in the results
* Get the download page and find the URL for the specific ZIP file on it

## Feedback
I tested it (manually) with a couple of different parameters and it worked out in these cases. If you know a better, more robust, approach I'm happy when you leave me a comment here (or a Pull Request on [GitHub](https://github.com/SimonOfHH/D365BCDownloadHelper)). Of course I'm also happy about any other feedback 😊

Cheers, Simon