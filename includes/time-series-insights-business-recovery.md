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
ms.openlocfilehash: cf07d19966c08a63b9aa50475622aa0a1e5e1600
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236506"
---
## <a name="business-disaster-recovery"></a>Üzleti katasztrófa utáni helyreállítás

Ez a szakasz ismerteti, így az alkalmazások és szolgáltatások fut, akkor is, ha katasztrófa történik az Azure Time Series Insights funkcióit (**üzleti vész-helyreállítási**).

### <a name="high-availability"></a>Magas rendelkezésre állás

Azure-szolgáltatások, mint a Time Series Insights biztosít bizonyos **magas rendelkezésre állású** funkciók redundanciát használatával az Azure-régió szintjén. Például a Microsoft Azure támogatja a vész-helyreállítási lehetőségei az Azure **régiók rendelkezésre állása** funkció.

Az Azure (és bármely Time Series Insights-példány számára is elérhető) keresztül elérhető további magas rendelkezésre állású szolgáltatások a következők:

1. **Feladatátvétel**: Az Azure biztosít [georeplikációja és a terheléselosztás betöltése](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
1. **Adatok helyreállítása** és **tárolási helyreállítási**: Az Azure biztosít [adatokat helyreállítani, valamint számos](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
1. **Site Recovery** funkciói révén [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Ellenőrizze, hogy a globális, régiók, magas rendelkezésre állás biztosításához az eszközök és felhasználók Azure funkciók engedélyezéséhez.

> [!NOTE]
> Ha Azure van konfigurálva ahhoz, hogy **régiók rendelkezésre állása**, nincs további régiók rendelkezésre állási konfiguráció nem szükséges az Azure Time Series Insights belül.

### <a name="iot-and-event-hubs"></a>IoT- és Event hubs

Egyes Azure-IoT-szolgáltatások beépített üzleti vész helyreállítási funkciókat is tartalmazzák:

1. [Az IoT Hub magas rendelkezésre állású vész-helyreállítási](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) többek között a redundancia intra-régió.
1. [Eseményközpont-szabályzatok](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
1. [Az Azure Storage-redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

Más szolgáltatások integrálása a Time Series Insights további vész helyreállítási lehetőségeket biztosít. Például az eseményközpontnak küldött telemetriát előfordulhat, hogy az Azure Blob Storage-adatbázis biztonsági megőrzésére.

### <a name="time-series-insights"></a>Time Series Insights

Többféleképpen is lehet a Time Series Insights adatait, az alkalmazások és a szolgáltatásait, még akkor is, ha azok még szakadhat meg. Dönthet úgy is, hogy az Azure Time Series-környezet teljes körű, ismétlődő, biztonsági mentési másolatot szükség:

1. A TSI-specifikus, **Feladatátvevőfürt-példány** átirányíthatja az adatokat, és a forgalmat.
1. Naplózás és az adatok megőrzését céljából.

Ismétlődő a TSI-környezet a legjobb módszer általában egy második TSI-környezet létrehozása a biztonsági másolat az Azure-régióban. Események az elsődleges esemény forrásból is küldése a másodlagos ebben a környezetben. Győződjön meg arról, hogy egy második, dedikált és fogyasztói csoportot használja, és hajtsa végre az adott forrás üzleti vész helyreállítási irányelveinek (fent).

Pontosabban a duplikált környezetet hozhat létre:

1. Létrehoz egy környezetet a egy második régióba ([egy új Time Series Insights-környezet létrehozása az Azure Portalon](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)).
1. Hozzon létre az eseményforrás második dedikált fogyasztói csoportot.
1. Az új környezet gondoskodik róla, hogy a második, dedikált fogyasztói csoportot kijelölje az esemény-adatforráshoz csatlakozni.
1. Tekintse át a Time Series Insights [az IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) és [eseményközpont](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentációját.

A végső lépés:

* Ha az elsődleges régióban egy vészhelyreállítási incidens van hatással, irányítsa át a műveletek a biztonsági mentési Time Series Insights-környezetbe.
* Biztonsági mentése és helyreállítása minden TSI telemetriai és adatok lekérdezése a második régiót használni.

> [!IMPORTANT]
> * Vegye figyelembe, hogy a késés előfordulhat, hogy észlelt feladatátvétel esetén.
> * Feladatátvételi pillanatnyi ugrásszerű az üzenet feldolgozását műveletek foglaltsága okozhat.
> * További információkért lásd: [a Time Series Insightsban késés csökkentése](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).