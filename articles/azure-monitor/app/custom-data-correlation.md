---
title: Azure-Application Insights | Microsoft Docs
description: ''
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 03f802297c2ecaac0c126ed3de6401b40e1f804b
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678159"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Egyéni adatforrásokkal való korreláció Application Insights

Application Insights több különböző adattípust gyűjt: kivételek, Nyomkövetések, oldalmegtekintések és egyebek. Habár ez gyakran elegendő az alkalmazás teljesítményének, megbízhatóságának és használatának vizsgálatához, vannak olyan esetek, amikor hasznos lehet az Application Insights tárolt adatok más, teljesen egyéni adatkészletekbe való összekapcsolása.

Bizonyos esetekben előfordulhat, hogy az egyéni adatértékek a következők:

- Adatgazdagítás vagy keresési táblák: például kiegészít egy kiszolgálónevet a kiszolgáló tulajdonosával és a tesztkörnyezet helyével, ahol megtalálható 
- Korreláció a nem Application Insights adatforrásokkal: például egy webáruházban lévő vásárlással kapcsolatos adatok korrelálása a vásárlást beteljesítő szolgáltatásból származó információkkal, hogy meghatározza a szállítási idő becslésének pontos módját 
- Teljes mértékben egyéni adat: sok ügyfelünk szereti a Azure Monitor log platform lekérdezési nyelvét és teljesítményét, amely a Application Insightst támogatja, és azt szeretné használni, hogy lekérdezze az összes Application Insightshoz nem kapcsolódó adat lekérdezését. Például a napelemek teljesítményének a Smart Home-telepítés részeként való nyomon követéséhez, az [itt](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)ismertetett módon.

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Egyéni adatértékek korrelációja Application Insights adattal 

Mivel Application Insights a hatékony Azure Monitor log platformmal rendelkezik, a Azure Monitor teljes erejét használhatja az adat betöltéséhez. Ezután a "Join" operátor használatával írunk le lekérdezéseket, amelyek összekapcsolják ezeket az egyéni adataikat, és a számunkra rendelkezésre álló, Azure Monitor naplókban elérhető adataikat. 

## <a name="ingesting-data"></a>Adatfeldolgozás

Ebben a szakaszban áttekintjük, hogyan kérheti le az adatait Azure Monitor naplókba.

Ha még nem rendelkezik ilyennel, hozzon létre egy új Log Analytics munkaterületet a következő [utasításokat](../learn/quick-collect-azurevm.md) követve, beleértve a "Munkaterület létrehozása" lépést.

Naplózási adatok küldésének megkezdése Azure Monitorba. Több lehetőség is létezik:

- Egy szinkron mechanizmus esetében közvetlenül hívhatja meg az adatgyűjtő [API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) -t, vagy használhatja a Logic app Connectort – egyszerűen keresse meg az "Azure log Analytics" lehetőséget, és válassza az "adatküldés" beállítást:

  ![Képernyőkép választása és művelet](./media/custom-data-correlation/01-logic-app-connector.png)  

- Aszinkron beállításhoz használja a adatgyűjtő API-t egy feldolgozási folyamat létrehozásához. További részletekért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) .

## <a name="correlating-data"></a>Az adatok korrelálása

A Application Insights a Azure Monitor log platformon alapul. Ezért az [erőforrás](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) -összekapcsolással összekapcsolhatja a Azure monitorba betöltött összes adatot a Application Insights-adatként.

Egy "LabLocations_CL" nevű táblába például betöltheti a labor-leltárt és a telephelyeket egy "faLog Analytics" nevű munkaterületen. Ha ezt követően áttekintjük a "myAI" nevű Application Insights alkalmazásban követett kéréseket, és összekapcsoljuk azokat a számítógépneveket, amelyek a kérelmeket a korábban említett egyéni táblázatban tárolt gépek helyeire kézbesítik, a következő lekérdezést futtathatja vagy a Application Insights vagy Azure Monitor kontextus:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [adatgyűjtő API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) -referenciát.
- További információ az [erőforrások közötti illesztésekről](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
