---
title: Azure Application Insights | Microsoft dokumentumok
description: Az Application Insights adatainak összekötése más adatkészletekkel, például adatgazdagítási vagy -kereshető táblákkal, nem Application Insights-adatforrásokkal és egyéni adatokkal.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082761"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Az Application Insights-adatok és az egyéni adatforrások korrelációja

Az Application Insights több különböző adattípust gyűjt: kivételeket, nyomkövetéseket, oldalnézeteket és másokat. Bár ez gyakran elegendő az alkalmazás teljesítményének, megbízhatóságának és használatának vizsgálatához, vannak olyan esetek, amikor hasznos az Application Insightsban tárolt adatok és más teljesen egyéni adatkészletek korrelációja.

Néhány olyan helyzet, amikor egyéni adatokat szeretne:

- Adatbővítési vagy -kereshető táblák: például kiegészítheti a kiszolgáló nevét a kiszolgáló tulajdonosával és azzal a laborhellyel, ahol megtalálható 
- Korreláció az Application Insights-adatoktól nem rendelkező adatforrásokkal: például korrelála egy webáruházban történő vásárlással kapcsolatos adatok és a beszerzési teljesítési szolgáltatás adatai között annak meghatározásához, hogy mennyire voltak pontosak a szállítási időbecslések. 
- Teljesen egyéni adatok: sok ügyfelünk szereti az Azure Monitor naplózási platform lekérdezési nyelvét és teljesítményét, amely támogatja az Application Insightsot, és olyan adatok lekérdezésére szeretné használni, amelyek egyáltalán nem kapcsolódnak az Application Insightshoz. Például, hogy nyomon kövesse a napelem teljesítményét részeként egy intelligens otthoni telepítés [itt](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)vázolt .

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Egyéni adatok és Application Insights-adatok korrelációja 

Mivel az Application Insights a hatékony Azure Monitor-naplóplatform ot támogatja, az Azure Monitor teljes erejét kihasználhatjuk az adatok betöltéséhez. Ezután a "join" operátor használatával lekérdezéseket írunk, amelyek korrelálják ezeket az egyéni adatokat az Azure Monitor-naplókban elérhető adatokkal. 

## <a name="ingesting-data"></a>Az adatok betöltése

Ebben a szakaszban áttekintjük, hogyan juthat el az adatokat az Azure Monitor naplóiba.

Ha még nem rendelkezik ilyen, hozzon létre egy új Log Analytics-munkaterületet [az alábbi utasításokat](../learn/quick-collect-azurevm.md) követve, és beleértve a "munkaterület létrehozása" lépést.

Naplóadatok küldésének megkezdése az Azure Monitorba. Számos lehetőség létezik:

- Egy szinkron mechanizmus esetén közvetlenül meghívhatja az [adatgyűjtő API-t,](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) vagy használhatja a Logic App-összekötőt – egyszerűen keresse meg az "Azure Log Analytics" kifejezést, és válassza az "Adatok küldése" lehetőséget:

  ![Képernyőkép kiválasztása és művelet](./media/custom-data-correlation/01-logic-app-connector.png)  

- Aszinkron beállítás esetén használja a Data Collector API-t egy feldolgozási folyamat létrehozásához. A részleteket ebben a [cikkben](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) találja.

## <a name="correlating-data"></a>Az adatok korrelálása

Az Application Insights az Azure Monitor naplóplatformján alapul. Ezért az [erőforrások közötti illesztések](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) segítségével az Azure Monitorba bevitt adatokat összevethetjük az Application Insights-adatokkal.

Például a laborleltárt és a helyeket egy "LabLocations_CL" nevű táblába vihetjük egy "myLA" nevű Log Analytics-munkaterületen. Ha ezután át akartuk tekinteni a "myAI" nevű Application Insights-alkalmazásban nyomon követett kéréseinket, és korrelálni szeretnénk a kéréseket a korábban említett egyéni táblában tárolt gépek helyeire, a következő lekérdezést futtathatjuk a következő az Application Insights vagy az Azure Monitor környezetben:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [Data Collector API referencia.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)
- További információ az [erőforrás-közi illesztések](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
