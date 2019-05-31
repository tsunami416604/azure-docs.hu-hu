---
title: fájl belefoglalása
description: fájl belefoglalása
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: e3e7044148e262e6977ae3be96f7cb61218114a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388743"
---
## <a name="business-disaster-recovery"></a>Üzleti katasztrófa utáni helyreállítás

Ez a szakasz ismerteti, így az alkalmazások és szolgáltatások fut, akkor is, ha katasztrófa történik az Azure Time Series Insights funkcióit. A fogalom üzleti vész-helyreállítási néven ismert.

### <a name="high-availability"></a>Magas rendelkezésre állás

Azure-szolgáltatások, mint a Time Series Insights lehetővé teszi bizonyos magas rendelkezésre állású szolgáltatások használatával redundanciát az Azure-régió szintjén. Például a Microsoft Azure támogatja a vész-helyreállítási lehetőségei az Azure-régiók rendelkezésre állás szolgáltatással.

Azure, és hogy keresztül elérhető további magas rendelkezésre állású funkciókat is bármely Time Series Insights-példány számára elérhető, például:

* **Feladatátvétel**: Az Azure biztosít [georeplikációja és a terheléselosztás](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
* **Adatok helyreállítása** és **tárolási helyreállítási**: Az Azure biztosít [adatokat helyreállítani, valamint számos](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
* **Site recovery**: Keresztül elérhető szolgáltatások [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Ellenőrizze, hogy a globális, a régiók közötti magas rendelkezésre állás biztosításához az eszközök és felhasználók Azure funkciók engedélyezéséhez.

> [!NOTE]
> Ha az Azure-régiók rendelkezésre állás engedélyezéséhez van konfigurálva, nincs további régiók rendelkezésre állási konfiguráció nem szükséges az Azure Time Series Insights belül.

### <a name="iot-and-event-hubs"></a>IoT- és event hubs

Egyes Azure-IoT-szolgáltatások beépített üzleti vész helyreállítási funkciókat is tartalmazzák:

* [Az Azure IoT Hub magas rendelkezésre állású vész-helyreállítási](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), mely intra-régió redundancia tartalmazza.
* [Az Azure Event Hubs-házirendek](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
* [Az Azure Storage-redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

Más szolgáltatások integrálása a Time Series Insights további vész helyreállítási lehetőségeket biztosít. Például az eseményközpontnak küldött telemetriát előfordulhat, hogy az Azure Blob storage-adatbázis biztonsági megőrzésére.

### <a name="time-series-insights"></a>Time Series Insights

Többféleképpen is lehet a Time Series Insights adatait, az alkalmazások és a szolgáltatásait, még akkor is, ha azok még szakadhat meg. Dönthet úgy is, hogy egy teljes biztonsági másolat az Azure Time Series-környezet szükség:

* Egy Time Series Insights-specifikus feladatátvételi példányt átirányíthatja az adatokat, és a forgalmat.
* Naplózás és az adatok megőrzését céljából.

Ismétlődő egy Time Series Insights-környezet a legjobb módszer általában egy második Time Series Insights-környezet létrehozása a biztonsági másolat az Azure-régióban. Események az elsődleges esemény forrásból is küldése a másodlagos ebben a környezetben. Győződjön meg arról, hogy a második dedikált fogyasztói csoportot és kövesse a forrást üzleti vész helyreállítási, korábban biztosított útmutatásokat.

Pontosabban a duplikált környezetet hozhat létre:

1. Hozzon létre egy környezetet egy második régióba. Útmutatásért lásd: [egy új Time Series Insights-környezet létrehozása az Azure Portalon](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Hozzon létre az eseményforrás második dedikált fogyasztói csoportot.
1. Az esemény-adatforráshoz csatlakozhat az új környezet. Győződjön meg arról, hogy a második dedikált fogyasztói csoportot kijelölje.
1. Tekintse át a Time Series Insights [az IoT hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) és [eseményközpont](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentációját.

A végső lépés:

1. Ha az elsődleges régióban egy vészhelyreállítási incidens van hatással, irányítsa át a műveletek a biztonsági mentési Time Series Insights-környezetbe.
1. A második régiót segítségével biztonsági mentése és helyreállítása minden Time Series Insights telemetriai és a lekérdezés adatait.

> [!IMPORTANT]
> * Vegye figyelembe, hogy a késés előfordulhat, hogy észlelt feladatátvétel esetén.
> * Feladatátvételi pillanatnyi ugrásszerű az üzenet feldolgozását műveletek foglaltsága okozhat.
> * További információkért lásd: [a Time Series Insightsban késés csökkentése](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).
