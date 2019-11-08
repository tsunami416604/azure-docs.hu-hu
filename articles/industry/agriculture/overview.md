---
title: Az Azure FarmBeats áttekintése
description: Áttekintést nyújt az Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12882860b8a9beadd4e56cbb151d670fac4da43b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798696"
---
# <a name="overview-of-azure-farmbeats"></a>Az Azure FarmBeats áttekintése

Az Azure FarmBeats olyan Azure-szolgáltatások és-képességek gyűjteménye, amelyek segítségével gyorsan készíthet intelligens adatvezérelt megoldásokat a mezőgazdaságban. Az Azure FarmBeats egy Azure Marketplace-ajánlat, amely lehetővé teszi, hogy a mezőgazdasággal kapcsolatos adatokat különböző forrásokból, például érzékelőkből, herékből, kamerákból, műholdból szerezzen be, összesítse és dolgozza fel.

> [!NOTE]
> Az Azure FarmBeats jelenleg nyilvános előzetes verzióban érhető el. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Az Azure-FarmBeats szolgáltatói szerződés nélkül biztosítjuk. Támogatásért használja az [Azure FarmBeats fórumot](https://aka.ms/FarmBeatsMSDN ) .

Az Azure FarmBeats különböző forrásokból (például érzékelőkből, műholdból, herékből) származó adatokhoz juthat, amelyek mindegyike egy Farm (földrajzi terület) kontextusában van.

A következőket teheti:

- Különböző szolgáltatók összesített mezőgazdasági adatkészletei
- Mesterséges intelligencia/gépi nyelvi (AI/ML) modellek gyors kiépítése különböző adatkészletek elutasításával

Az Azure FarmBeats robusztus és egyszerű módszert kínál a következők elvégzésére:

- Hozzon létre egy térképet a farmhoz a GeoJSON fájl használatával.
- A farm állapotának felmérése a vegetációs index és a vízindex használatával, a műholdképek alapján.
- Javaslatokat kaphat a használatban lévő érzékelők számáról és helyéről.
- Nyomon követheti a farm feltételeit, ha a különböző érzékelők gyártói által gyűjtött adatokat jeleníti meg.
- A talaj nedvességtartalmának leképezése a műhold és az érzékelő adategyesítése alapján.
- Az összesített adathalmazok alapján az AI/ML modellek kiépítése révén hasznosítható elemzéseket nyerhet.
- A farm Health Advisor szolgáltatásával kiépítheti vagy kiegészítheti a digitális mezőgazdaság megoldását.

Az Azure FarmBeat-összetevőket a jelen cikk következő részeiben tárgyaljuk.

## <a name="data-hub"></a>Adatközpont

Egy API-réteg, amely lehetővé teszi különböző mezőgazdasági adatkészletek összesítését, normalizálása és contextualization a szolgáltatók között. Ebben az előzetes verzióban a következő két érzékelő szolgáltatója is kihasználható: [Davis-eszközök](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), egy szatellit képszolgáltató [Sentinel-2](https://sentinel.esa.int/web/sentinel/home)és két drone képszolgáltató [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/). Az adatközpont API-platformként lett kialakítva, és számos további szolgáltatóval dolgozunk az Azure FarmBeats való integráláshoz, így a megoldás létrehozásakor több lehetőség is van.

## <a name="accelerator"></a>Accelerator

Az adatközpontra épülő minta megoldás, amely a felhasználói felületet és a modell fejlesztését szorgalmazza. Ez a webalkalmazás kihasználja az API-kat, hogy bemutassa a betöltött érzékelői adatokat diagramokként és a modell kimenetének megjelenítésekor. A Gyorssegéd használatával például gyorsan létrehozhat egy farmot, és egyszerűen beszerezhet egy vegetációs index-térképet vagy az adott farmhoz tartozó érzékelő elhelyezési térképet.

## <a name="resources"></a>Erőforrások

Látogasson el a FarmBeats- [blogra](https://aka.ms/AzureFarmBeats) és a [fórumokra](https://aka.ms/FarmBeatsMSDN).

## <a name="next-steps"></a>További lépések

Az Azure FarmBeats megismeréséhez látogasson el az [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) -re az üzembe helyezéshez.
