---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431045"
---
## <a name="business-disaster-recovery"></a>Üzleti katasztrófa utáni helyreállítás

Ez a szakasz ismerteti az Azure Time Series Insights, hogy az alkalmazások és szolgáltatások futnak, még akkor is, ha katasztrófa történik funkcióját (más néven *üzleti vész-helyreállítási*).

### <a name="high-availability"></a>Magas rendelkezésre állás

Azure-szolgáltatások, mint a Time Series Insights biztosít bizonyos *magas rendelkezésre állású* funkciók az Azure-régió szintjén redundanciát használatával. Ha például az Azure támogatja a vész-helyreállítási lehetőségei az Azure *régiók rendelkezésre állása* funkció.

Az Azure (és bármely Time Series Insights-példány számára is elérhető) keresztül elérhető további magas rendelkezésre állású szolgáltatások a következők:

- **Feladatátvétel**: Az Azure biztosít [georeplikációja és a terheléselosztás](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Adatok helyreállítása** és **tárolási helyreállítási**: Az Azure biztosít [adatokat helyreállítani, valamint számos](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Site recovery**: Az Azure site recovery szolgáltatásainak biztosít [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Ellenőrizze, hogy a megfelelő Azure-szolgáltatások globális, a régiók közötti magas rendelkezésre állást biztosít az eszközök és felhasználók számára engedélyezi.

> [!NOTE]
> Ha az Azure-régiók rendelkezésre állás engedélyezéséhez van konfigurálva, nincs további régiók rendelkezésre állási konfiguráció szükséges az Azure Time Series Insightsban.

### <a name="iot-and-event-hubs"></a>IoT- és event hubs

Egyes Azure-IoT-szolgáltatások beépített üzleti vész helyreállítási funkciókat is tartalmazzák:

- [Az IoT Hub magas rendelkezésre állású vész-helyreállítási](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), beleértve a redundancia intra-régió
- [Eseményközpont-szabályzat](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Az Azure Storage-redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

A más szolgáltatások integrálása a Time Series Insights további vész helyreállítási lehetőségeket biztosít. Például az eseményközpontnak küldött telemetriát előfordulhat, hogy az Azure Blob storage-adatbázis biztonsági megőrzésére.

### <a name="time-series-insights"></a>Time Series Insights

Többféleképpen is, hogy a Time Series Insights-adatok, alkalmazások és szolgáltatások futnak, még akkor is, ha azok még szakadhat meg. 

Azonban előfordulhat, hogy meghatározni, hogy az Azure Time Series-környezet teljes biztonsági másolatot is szükséges, az alábbi célokra:

- Mint egy *Feladatátvevőfürt-példány* kifejezetten a Time Series Insights átirányíthatja az adatokat, és a forgalmat
- Adatok és a naplózási adatok megőrzése

Ismétlődő egy Time Series Insights-környezet a legjobb módszer általában egy második Time Series Insights-környezet létrehozása a biztonsági másolat az Azure-régióban. Események az elsődleges esemény forrásból is küldése a másodlagos ebben a környezetben. Győződjön meg arról, hogy egy második, dedikált fogyasztói csoportot használja. A forrást üzleti katasztrófa utáni helyreállítás irányelvek, hajtsa végre a korábban leírtaknak megfelelően.

Egy duplikált környezet létrehozásához:

1. Hozzon létre egy környezetet egy második régióba. További információkért lásd: [egy új Time Series Insights-környezet létrehozása az Azure Portalon](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Hozzon létre az eseményforrás második dedikált fogyasztói csoportot.
1. Az esemény-adatforráshoz csatlakozhat az új környezet. Győződjön meg arról, hogy a második, dedikált fogyasztói csoportot jelöljön ki.
1. Tekintse át a Time Series Insights [az IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) és [az Event Hubs](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentációját.

Ha egy esemény történik:

1. Ha az elsődleges régióban egy vészhelyreállítási incidens van hatással, irányítsa át a műveletek a biztonsági mentési Time Series Insights-környezetbe.
1. A második régiót segítségével biztonsági mentése és helyreállítása minden Time Series Insights telemetriai és a lekérdezés adatait.

> [!IMPORTANT]
> Ha a feladatátvételt hajt végre:
> 
> * Emellett előfordulhat némi késleltetés.
> * Üzenetfeldolgozás pillanatnyi ugrásszerű fordulhatnak elő, módon vannak átirányítva a műveleteket.
> 
> További információkért lásd: [a Time Series Insightsban késés csökkentése](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

