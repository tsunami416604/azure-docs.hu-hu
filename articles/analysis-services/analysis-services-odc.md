---
title: "Hozzon létre egy Azure Analysis Services-kiszolgálóhoz való kapcsolódáshoz .odc fájl |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy Office Data Connection fájlt adatokat lekérni az Azure-ban egy Analysis Services-kiszolgálóhoz való csatlakozáshoz."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 128a25a3d68c036a0fc165899a53d975e7a774d8
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="create-an-office-data-connection-file"></a>Office Data Connection-fájl létrehozása

A cikkben szereplő információkat ismerteti, hogyan hozhat létre az Office Data Connection fájl Excel 2016 verziószáma 16.0.7369.2117 egy Azure Analysis Services-kiszolgálóhoz való kapcsolódáshoz és a korábbi verziók, vagy az Excel 2013. Egy frissített [MSOLAP.7 szolgáltató](analysis-services-data-providers.md) is szükség.


1. Másolja az alábbi minta kapcsolat fájl, és illessze be a szövegszerkesztőben. 

2. A `odc:ConnectionString`, módosítsa a következő tulajdonságokkal:

    *   A `Data Source=asazure://<region>.asazure.windows.net/<servername>;` módosítása `<region>` a két régiónak az Analysis Services-kiszolgáló és `<servername>` a kiszolgáló nevét.

    *   A `Initial Catalog=<database>;` módosítása `<database>` az adatbázis nevét.

3. A `<odc:CommandText>Model</odc:CommandText>` módosítása `Model` a modell vagy perspektíva nevét. 

4. Mentse a fájlt egy `.odc` a C:\Users bővítmény\\*felhasználónév*\Documents\My adatforrások mappát.

5. Kattintson jobb gombbal a fájlra, és kattintson **Megnyitás az Excelben**. Vagy az Excel programban a a **adatok** menüszalag, kattintson a **meglévő kapcsolatok**, válassza ki a fájlt, és kattintson a **nyitott**.



**A minta kapcsolatfájl**
```
<html xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns="http://www.w3.org/TR/REC-html40">

<head>
<meta http-equiv=Content-Type content="text/x-ms-odc; charset=utf-8">
<meta name=ProgId content=ODC.Cube>
<meta name=SourceType content=OLEDB>
<meta name=Catalog content="Database">
<meta name=Table content=Model>
<title>AzureAnalysisServicesConnection</title>
<xml id=docprops><o:DocumentProperties
  xmlns:o="urn:schemas-microsoft-com:office:office"
  xmlns="http://www.w3.org/TR/REC-html40">
  <o:Name>SampleAzureAnalysisServices</o:Name>
 </o:DocumentProperties>
</xml><xml id=msodc><odc:OfficeDataConnection
  xmlns:odc="urn:schemas-microsoft-com:office:odc"
  xmlns="http://www.w3.org/TR/REC-html40">
  <odc:Connection odc:Type="OLEDB">
   <odc:ConnectionString>Provider=MSOLAP.7;Data Source=asazure://<region>.asazure.windows.net/<servername>;Initial Catalog=<database>;</odc:ConnectionString>
   <odc:CommandType>Cube</odc:CommandType>
   <odc:CommandText>Model</odc:CommandText>
  </odc:Connection>
 </odc:OfficeDataConnection>
</xml>
<style>
<!--
    .ODCDataSource
    {
    behavior: url(dataconn.htc);
    }
-->
</style>
 
</head>

<body onload='init()' scroll=no leftmargin=0 topmargin=0 rightmargin=0 style='border: 0px'>
<table style='border: solid 1px threedface; height: 100%; width: 100%' cellpadding=0 cellspacing=0 width='100%'> 
  <tr> 
    <td id=tdName style='font-family:arial; font-size:medium; padding: 3px; background-color: threedface'> 
      &nbsp; 
    </td> 
     <td id=tdTableDropdown style='padding: 3px; background-color: threedface; vertical-align: top; padding-bottom: 3px'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td id=tdDesc colspan='2' style='border-bottom: 1px threedshadow solid; font-family: Arial; font-size: 1pt; padding: 2px; background-color: threedface'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td colspan='2' style='height: 100%; padding-bottom: 4px; border-top: 1px threedhighlight solid;'> 
      <div id='pt' style='height: 100%' class='ODCDataSource'></div> 
    </td> 
  </tr> 
</table> 

  
<script language='javascript'> 

function init() { 
  var sName, sDescription; 
  var i, j; 
  
  try { 
    sName = unescape(location.href) 
  
    i = sName.lastIndexOf(".") 
    if (i>=0) { sName = sName.substring(1, i); } 
  
    i = sName.lastIndexOf("/") 
    if (i>=0) { sName = sName.substring(i+1, sName.length); } 

    document.title = sName; 
    document.getElementById("tdName").innerText = sName; 

    sDescription = document.getElementById("docprops").innerHTML; 
  
    i = sDescription.indexOf("escription>") 
    if (i>=0) { j = sDescription.indexOf("escription>", i + 11); } 

    if (i>=0 && j >= 0) { 
      j = sDescription.lastIndexOf("</", j); 

      if (j>=0) { 
          sDescription = sDescription.substring(i+11, j); 
        if (sDescription != "") { 
            document.getElementById("tdDesc").style.fontSize="x-small"; 
          document.getElementById("tdDesc").innerHTML = sDescription; 
          } 
        } 
      } 
    } 
  catch(e) { 

    } 
  } 
</script> 

</body> 
 
</html>

```



