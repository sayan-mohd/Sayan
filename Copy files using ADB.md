# How to copy files using `adb` (Android Debug Bridge)

Let me start with the problem I had and why I chose `adb`. I have over 4000 images in my mobile SD card and some of it got corrupted. 
If I try to copy 100 images, at random, and if one of the file is corrupted then the copy process will be stopped. I have to find out which image is causing this problem
and manually delete it.
I could have used a SD card adapter to copy files directly from SD card to my backup disk using [xcopy](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/xcopy)
but I chose `adb` for convenience.

This article assumes `adb` is already installed in WIndows 10.

### Overview
1. [Enable Developer mode](https://developer.android.com/studio/debug/dev-options)
2. Connect mobile to PC and accept permissions
3. Change the below script as necessary and execute it!

````powershell
$SuccessCount=0; $ErrorCount=0 #Variables to show status of copy
$file = Get-Content -Path ImagesToCopy.txt #Name of all files to be copied from SD Card. This gives us greater control to audit

foreach ($f in $file){ #Loop through each file from the list of files to be copied
    try {
      $op = adb.exe pull "/storage/5687-6987/DCIM/Camera/$f" "CopiedImages"
      if ($op -match "error:") {  #If the file cannot be copied then log it
          $ErrorCount = $ErrorCount+1
          $f | Out-File -Append errors.txt
    } 
      else { 
          $SuccessCount = $SuccessCount+1
          $f | Out-File -Append success.txt #Log all successful copies
      }
    #Show Status of the copy process  
    Write-Host ($SuccessCount+$ErrorCount) " file(s) Completed. Remaining:" ($file.Count-($SuccessCount+$ErrorCount)) "( Success:" $SuccessCount "; Errors:" $ErrorCount " )"
    } 
    catch {$f | Out-File -Append errors.txt}
}
````
Hope this will be useful to people facing similar issues like I did! 
