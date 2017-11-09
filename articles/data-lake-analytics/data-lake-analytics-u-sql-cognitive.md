---
title: "Az Azure Data Lake Analytics U-SQL kognitív képességek segítségével |} Microsoft Docs"
description: "Az eszközintelligencia kognitív képességek használata U-SQL-ben"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: ec48a07af0aba78f2e508bad232f34102f0c2073
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Oktatóanyag: Ismerkedés a U-SQL kognitív lehetőségeinek

## <a name="overview"></a>Áttekintés
A fejlesztők kognitív képességet biztosít a U-SQL helyezze az eszközintelligencia a big Data típusú adatok programok telepítése és használata. 

A következő kognitív lehetőségek érhetők el:
* Képkezelő: Lapok észlelése
* Képkezelő: Érzelemfelismerési észlelése
* Képkezelő: (Címkézés) objektumok észlelése
* Képkezelő: OCR optikai karakter használata)
* Szöveg: Kulcs kifejezés kivonása
* Szöveg: Véleményeket elemzés

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>A U-SQL parancsfájl Cognitive használata

A teljes folyamat felettébb egyszerű:

* A referencia szerelvény utasítás használható a U-SQL parancsfájl kognitív funkciók lehetővé tétele
* A FOLYAMATOT használja egy kognitív UDO segítségével egy kimenete egy bemeneti sorkészlet sorhalmaz

### <a name="detecting-objects-in-images"></a>A képek objektumok észlelése

A következő példa bemutatja, hogyan objektumok azonosíthatók a képek a kognitív képességek használatára.

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputters.Csv();
```
További példákért tekintse meg a **U-SQL/Cognitive minták** a a **további lépések** szakasz.

## <a name="next-steps"></a>Következő lépések
* [U-SQL/kognitív minták](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL ablak függvények használata az Azure Data Lake Analytics-feladatok](data-lake-analytics-use-window-functions.md)
