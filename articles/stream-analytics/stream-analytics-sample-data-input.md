---
title: Mintaadatokat használja az Azure Stream Analytics lekérdezés tesztelése
description: Ez a cikk ismerteti, hogyan néhány minta bemeneti adatok használatának Azure Stream Analytics lekérdezés tesztelése.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 305b767ee86de6c8b04fed17514a9092afc2d735
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2018
ms.locfileid: "30961747"
---
# <a name="test-a-query-and-sample-input-in-azure-stream-analytics"></a>A lekérdezés és az Azure Stream Analytics bemeneti minta tesztelése 

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

Után elégedett az eredményeket, a lekérdezés mentéséhez, a feladat elindítása, elhelyezkedik vissza, és tekintse meg a Stream Analytics magic.

## <a name="get-help"></a>Segítségkérés

Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
