---
title: Az Azure Application Insights |} A Microsoft Docs
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: fbeece7bf9e460453bc3c1c6a9df1f19f767ce48
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725029"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Vlastní zdroje dat az Application Insights-adatok korrelálásához

Az Application Insights összegyűjti a számos különböző adattípusok: kivételeket, nyomkövetéseket, lapmegtekintés és mások. Bár ez gyakran elegendő az alkalmazás teljesítmény, a megbízhatóság és a használati vizsgálatára, nincsenek esetekben hasznos lehet más teljesen egyéni adatkészletekhez az Application insights szolgáltatásban tárolt adatok korrelálásához.

Bizonyos esetekben érdemes lehet egyéni adatok a következők:

- Adatbővítés vagy -keresési adattáblák: például kiegészítik a tulajdonos, a kiszolgáló és a tesztkörnyezet helyét, amelyben megtalálhatók a kiszolgáló neve 
- Az Application Insights-adatforrások korrelációs: például egy webes áruházi meghatározni, hogy pontos becslése a szállítási időt a beszerzési-teljesítési service adataival a vásárlás összevetését adatait is 
- Teljesen egyéni adatok: számos ügyfelünk kedvelt a lekérdezési nyelvet és a Log Analytics data platform, amely az Application Insights teljesítményét, és azt szeretné használni, amely egyáltalán nem kapcsolódik az Application Insights adatokat lekérdezni. Ha például egy intelligens otthoni telepítés részeként a napkollektorok teljesítményének nyomon követéséhez vázolt [Itt]( http://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Hogyan lehet egyéni adatait az Application Insights-adatok 

Az Application Insights alapját a Log Analytics hatékony adatplatform, mivel tudjuk használja a teljes Log Analytics az adatok betöltését. Ezt követően írunk a lekérdezéseket a "Csatlakozás" operátor ezt az egyéni adatot, a rendelkezésre álló velünk a kapcsolatot a Log Analytics adatokat fog kapcsolható. 

## <a name="ingesting-data"></a>Adatok feldolgozása

Ebben a szakaszban át fogjuk tekinteni az adatok lekérése a Log analyticsbe.

Ha még nem rendelkezik egy, üzembe helyezése egy új Log Analytics-munkaterület az alábbi [ezek az utasítások]( https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm) keresztül és a "munkaterület létrehozása" lépésben.

Hogy adatokat küldjön a Log analyticsbe. Több lehetőség is létezik:

- A szinkron mechanizmust, vagy közvetlenül meghívhatja a [adatgyűjtő API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) vagy a logikai alkalmazás-összekötő használata – egyszerűen csak "Az Azure Log Analytics" keresse meg és válassza ki az "Adatok küldése" lehetőséget:

 ![Képernyőkép – válassza ki, és a művelet](./media/app-insights-custom-data-correlation/01-logic-app-connector.png)  

- Egy aszinkron lehetőséggel az adatgyűjtő API használatával hozhat létre egy feldolgozási folyamat. Lásd: [Ez a cikk](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) részleteiről.

## <a name="correlating-data"></a>Az adatok korrelálása

Az Application Insights a Log Analytics data platform alapul. Ezért használhatunk [erőforrások közötti illesztések](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) korrelációját, hogy betöltődnek a Log Analytics az Application Insights-adatok az adatokat.

Például hogy betöltheti az a labor-készlet és a helyek "LabLocations_CL" nevű "myLA" nevű Log Analytics-munkaterületet a táblába. Ha ezután tekintse át a kérelmek nyomon követése "myAI" nevű Application Insights-alkalmazás, és vesse össze a számítógépek nevével, amely a kérelmeket a helyekre, ezek a gépek a korábban említett egyéni táblában tárolt kiszolgált szerettünk volna, futtassa a következő lekérdezést a az Application Insights vagy a Log Analytics-kontextus:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>További lépések

- Tekintse meg a [adatgyűjtő API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) hivatkozást.
- További információ a [erőforrások közötti illesztések](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
