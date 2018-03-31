---
title: Tesztelés Azure Stream Analytics lekérdezési |} Microsoft Docs
description: Meghatározhatja problémák elhárításakor Stream Analytics-feladatok.
keywords: bemeneti, bemeneti mintavételi hibaelhárítása
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 99c9f7f2dc84f53d09b7aee1916c5532427de692
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="azure-stream-analytics-query-testing-and-input-stream-sampling"></a>Az Azure Stream Analytics lekérdezési tesztelése és az adatfolyam-bemenet mintavétel

Azure Stream Analytics segítségével fájlból származnak, és tesztelje a lekérdezések a portálon anélkül, hogy indítása vagy leállítása feladat bemeneti események segítségével mintavételi.

## <a name="testing-your-query"></a>A lekérdezés tesztelése

A Stream Analytics-részleteket tartalmazó ablaktáblán nyissa meg a **Lekérdezésszerkesztő** panel alatt lekérdezés nevére kattintanak **lekérdezés**. (A példaforgatókönyvben, mert nem a lekérdezés még jött létre, kattintson a **< >** helyőrző.)

![A Stream Analytics query editor](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

A lekérdezés létrehozásához gazdag szerkesztő panel jelenik meg, mint az előző verzióra. Most egy új bal oldali panelen, amely a be- és kimenetekkel, a lekérdezés által használt és a feladat meghatározott frissült a panelt.

![A Stream Analytics query editor a bemeneti és kimeneti listák](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

Egy további bemeneti és kimeneti, amelyek nem definiált is megjelennek a rendszer. Az új lekérdezés sablont, amely a kiindulási pont származnak. Módosíthatja, vagy akár eltűnnek a teljes sorozatnak így szerkessze a lekérdezést. Nyugodtan figyelmen kívül hagyhatja őket most.

A minta bemeneti adatok teszteléséhez kattintson a jobb gombbal bármelyik bemenet, és válassza **tölthet fel fájlból adatot**.

![A Stream Analytics lekérdezési szerkesztő feltöltés mintaadatok fájlból parancs](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

A feltöltés befejeződése után kattintson **tesztelése** tesztelése a most megadott mintaadatokat irányuló lekérdezés.

![A Stream Analytics query editor teszt gombra](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Ha azt szeretné, a vizsgálat kimeneti későbbi használatra menti, a lekérdezés kimenetét megjelenik egy hivatkozás a letöltés eredményeit a webböngésző. Mostantól egyszerűen és ismételt módosítsa a lekérdezést és tesztelik azt hogy ismételten hogyan a kimeneti változik.

![Stream Analytics query editor minta kimenet](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

Az előző ábrán egy második kimenet hozzáadva, úgynevezett **HighAvgTempOutput**.

Ha több kimenet a lekérdezésben, külön-külön minden kimeneti eredmények megjelenítéséhez, és könnyen váltása.

Miután elégedett az eredményeket, mentheti a lekérdezés, indítsa el a feladat, elhelyezkedik vissza, és tekintse meg a Stream Analytics magic.

## <a name="get-help"></a>Segítség kérése

Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
