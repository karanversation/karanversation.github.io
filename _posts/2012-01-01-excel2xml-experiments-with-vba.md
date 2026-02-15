---
layout: post
title: "Excel2XML - Experiments with VBA"
categories: [programming]
date: 2012-01-01
---

Working with VBA for transferring data from Excel sheets onto XML files, was Part-1 of my project concerning generation of apps for mobile database implementation.

![Excel2XML Main](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgXx9R1-Oc3SkpgW5MduMZHMHztvvRFQ3tNIKaoKOo98Cj34KjrDpow0S_EA-oswL-aEon8RwAMFX8S_Z-c5tM-GMQ5XEBWh_WZBZmooNwsHQgyAa3-6Guc6YiX_u94Ce_4L7I2_vFsfBhT/s1600/Untitled.png)

![Developer Tools](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3B6QMESLJhspy4owiLt9aVA7_yNBF6yS9ndvpYLAC5OyL9LtpVQ4Ig_QEzbiDJiA6P0tTA2sKKuKefM2sMQ3ejDMnIexl1mRH8kL6awAd5IiUnj18SeWJtBN5bhWJn49iX0_nNfHJBlHL/s320/Capture.JPG)

For this purpose, I utilized the Developer tools of Microsoft Excel.

To enable the developer tools, you can follows the steps mentioned [here](http://office.microsoft.com/en-us/excel-help/show-the-developer-tab-or-run-in-developer-mode-HA010173052.aspx).

Once, the developer tab appears on your screen, you can begin with the coding work!

My program code would work successfully with data in multiple TABs on the worksheet.

![Worksheet Tabs](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjIpfnBv66P0dZTOCgTzoB4__7AYEdCO3NIPa4KmgH36cb0nGC3hit636LT2aSQriRT0FGFgT260gjT1EU5K2PyA2Wtv4ZFCvXeLR2_todnjSPac-q1g4QufmPXoOrZhAVg7hbaNCFs5sFP/s1600/Capture.JPG)

Now from the developer tab, you need to select the **Command Button**, under **Insert** option.

![Command Button](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjlNOa_3bWmSxB2r5rfLnSXhIje70sIPUg4qk2bZkc42Cp91Zc9Ckq9yiqF-QV-jgfIQ-2-qpW5cUdOGUnfuNsGaJh1llG1n4tdFl35_3WA5mBRVqsjt4QEXvwkQMK51jgB9rxNviET3CGy/s1600/Capture.JPG)

Command Button is inserted. You can now change its properties as follows -

![Button Properties](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEht71K0jFndkl9rg1T4c3-5YNKcdp_QSeoA_UoYravv2ykPOHb7vBy6n6Yy28ZD4zdM3IZFB1lrL80-siuUC9Q6m8TSC3d9GFvvnHuqpFZmx5T2nMlnCGm4Me26Ttng8L6DDZHpZlh_K4Pd/s1600/Capture.JPG)

Once the necessary changes are done, do ensure that the **Design Mode** button is selected, and then double click on the Button you earlier inserted to get into the VBA development area!

![VBA Development Area](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvgYTS8qzmw6iuGxmUpHTRO85ALy7417_IlQu9FPle7nmwQylW3AsvL538G__ZRON8k-rl-9kLGhT2GYo1KmSkXILg8EN6MCG3klIwqXi8pQ-JYQqNU1s_loabTl1EezkJm537XcEB168W/s640/Capture.JPG)

This is where my code comes into play.

```vba
Dim x As Integer
For x = 1 To ThisWorkbook.Worksheets.Count
    ThisWorkbook.Worksheets(x).Select
    
    Dim strXML As String
    If Application.Count(Selection) = 0 Then
        FindUsedRange
    End If
    
    Set objXMLDoc = GenerateXMLDOM(Selection, "data")
    
    Dim path As String
    path = ThisWorkbook.path & "\" & ActiveSheet.Name & " (" & ThisWorkbook.Name & ")" & ".xml"
    
    objXMLDoc.Save (path)
    
Next x
    
MsgBox ("Operation Complete!!!")
```

Paste this code inside the **sub** function of the command button.

Now you need to IMPORT the .bas file of the script that I have created.

![Import BAS File](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgAFZUKaKpFvFKJkBXpGNVOozeRG7umYuAIxVilvcvI-6twwPGfmntKaHM50i0IC4yfvGVl487OpBdJw7kUZz_B4WGvrpjg7UlJkZSodoa1Zq7Qseaj5G2wtncksKD61oXv63sQR2vAizQe/s1600/Capture.JPG)

This is where you need to MODIFY the code, according to the fields of your excel sheet and the required XML tags. For this you need to know basics of VBA coding.

In my case, when the Import is done, and the code runs without error, on clicking the Command Button earlier inserted, the required XML files are generated and stored in the same directory and a confirmation message is shown -

![Operation Complete](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVgfxOcx1x-reQinIbAJy6w0IvFaHD5a0XfISVeI9JjHxSuLP3zWyExlfZ_nqzbke_bLqh7dn__nT8ttNdE1nzaEWIPoP-3o56OnhHpO1FbAj8zNXbP_z7HGhVQuNHTAU1CNQ-yw9kTSHY/s1600/Capture.JPG)

![XML Files Generated](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNZVRHr8uosCt_vLew7FMDE8h0lwmVUgBOTfUbl-mOyN9fO1LtAZXNj51ABvGOQgilK6OX2b9zjT5m5pXujgk2bV-JeCSTDhSQ7l7PcMDZD1hwS8JSAWgJCttVtefZVnyfRSljPU-zTu4q/s1600/Capture.JPG)

You can download my .bas file from [here](http://www.mediafire.com/?ip6xpkr8ept8ne8).

If you wish to view the complete SRS, you can download the pdf from [here](http://www.mediafire.com/?2s7m8m5cw29boux).

Mission Successful! :)

*Original post: [https://karanversation.blogspot.com/2012/01/excel2xml-experiments-with-vba.html](https://karanversation.blogspot.com/2012/01/excel2xml-experiments-with-vba.html)*
