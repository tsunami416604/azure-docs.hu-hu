---
title: Mi a metrikai tanácsadó szolgáltatás?
titleSuffix: Azure Cognitive Services
description: Mi az a Metrics Advisor?
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 408bdd948977218d9b39a39bf97391a4141e545c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947125"
---
# <a name="what-is-metrics-advisor-preview"></a>Mi a mérőszámok tanácsadója (előzetes verzió)? 

A metrikai tanácsadó az Azure Cognitive Services része, amely a mesterséges intelligencia-adatelemzést és a anomáliák észlelését használja az idősorozat-adatokban. A szolgáltatás automatizálja a modellek alkalmazásának folyamatát, és az API-k webes munkaterületét biztosítja az adatfeldolgozás, a rendellenességek észlelése és a diagnosztika számára – anélkül, hogy ismernie kellene a gépi tanulást. Metrikai tanácsadó használata a következőhöz:

* Több dimenziós adatok elemzése több adatforrásból 
* Rendellenességek azonosítása és korrelációja
* Az adatain használt anomália-észlelési modell konfigurálása és finomhangolása
* Rendellenességek diagnosztizálása és Súgó a kiváltó okok elemzéséhez. 

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="A metrikai tanácsadó áttekintése":::

## <a name="connect-to-a-variety-of-data-sources"></a>Kapcsolódás különféle adatforrásokhoz

A metrikai tanácsadó képes a több adattárolóból származó [többdimenziós metrikai](how-tos/onboard-your-data.md) adatokhoz való kapcsolódásra, többek között a következőkre: SQL Server, Azure Blob Storage, MongoDB stb. 

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Könnyen használható és testreszabható anomáliák észlelése

* A metrikai tanácsadó automatikusan kiválasztja a legjobb modellt az adatokhoz, anélkül, hogy ismernie kellene a gépi tanulást. 
* A [többdimenziós mérőszámokban](glossary.md#multi-dimensional-metric)lévő minden idősorozat automatikus figyelése.
* A [Paraméterek finomhangolása](how-tos/configure-metrics.md) és az [interaktív visszajelzések](how-tos/anomaly-feedback.md) segítségével testre szabhatja az adatain alkalmazott modellt, valamint a jövőbeli anomáliák észlelésének eredményét.


## <a name="real-time-alerts-through-multiple-channels"></a>Valós idejű riasztások több csatornán keresztül

Ha rendellenességek észlelhetők, a metrikai tanácsadó több csatornán keresztül tud [valós idejű riasztásokat küldeni](how-tos/alerts.md) , például: e-mail-hookok, webhookok és az Azure DevOps hookok. A rugalmas riasztási szabályok lehetővé teszik a riasztások küldését, és hol.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Intelligens diagnosztikai adatok rendellenességek elemzésével

Elemezheti a többdimenziós metrikákban észlelt rendellenességeket, és [intelligens diagnosztikai](how-tos/diagnose-incident.md) elemzéseket készíthet, beleértve a legvalószínűbb kiváltó okot, a diagnosztikai fákat, a metrikus fúrást és egyebeket. A [metrikák diagramjának](how-tos/metrics-graph.md)konfigurálásával a kereszthivatkozások elemzése lehetővé teszi az incidensek megjelenítésének megkönnyítését.


## <a name="typical-workflow"></a>Jellemző munkafolyamat

A munkafolyamat egyszerű: az adatfeldolgozás után finomíthatja a rendellenességek észlelését, és létrehozhat konfigurációkat, hogy illeszkedjenek a forgatókönyvhöz.

1. [Hozzon létre egy Azure-erőforrást](../cognitive-services-apis-create-account.md) a metrikák tanácsadójának. 
2. Próbálja ki a bemutató webhelyet, és tekintse meg az előre konfigurált mintaadatok használatával kapcsolatos példát. 
3. Hozza létre első figyelőjét a webes portál használatával.
    1. Az adatok előkészítése
    2. Az anomáliák finomhangolása
    3. Feliratkozás riasztásokra
    4. Diagnosztikai ismeretek megtekintése
1. A példány testreszabásához használja a REST API.

## <a name="next-steps"></a>További lépések

* Próbálja ki a [bemutató webhelyet](quickstarts/explore-demo.md).
* Ismerkedjen meg a gyors útmutatóval: [Figyelje meg az első mérőszámot a weben](quickstarts/web-portal.md).
* Ismerkedés a gyors üzembe helyezéssel: [a REST API-k segítségével testre szabhatja a megoldást](quickstarts/rest-api.md).
