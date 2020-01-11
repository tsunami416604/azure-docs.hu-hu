---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 12/06/2019
ms.openlocfilehash: dec7f022a73c5eb47c99b2d2d38e1a3258bcf14e
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861462"
---
## <a name="business-disaster-recovery"></a>Üzleti katasztrófák helyreállítása

Ez a szakasz a Azure Time Series Insights azon funkcióit ismerteti, amelyek az alkalmazások és szolgáltatások futását biztosítják, még akkor is, ha vészhelyzet történik (más néven az *üzleti katasztrófák helyreállítása*).

### <a name="high-availability"></a>Magas rendelkezésre állás

Azure-szolgáltatásként a Time Series Insights bizonyos *magas rendelkezésre állású* funkciókat biztosít az Azure-régió szintjén elérhető redundanciák használatával. Az Azure például támogatja a vész-helyreállítási lehetőségeket az Azure *régiók közötti rendelkezésre állási* funkciójával.

Az Azure-on keresztül elérhető további magas rendelkezésre állási funkciók (és bármely Time Series Insights-példány számára elérhetők) a következők:

- **Feladatátvétel**: az Azure [geo-replikációt és terheléselosztást](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)biztosít.
- **Adatok helyreállítása** és **tárolása – helyreállítás**: [Az Azure számos lehetőséget kínál az adatok megőrzésére és helyreállítására](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Azure site Recovery**: az Azure a site Recovery szolgáltatásait [Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/)használatával biztosítja.
- **Azure Backup**: a [Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) az Azure-beli virtuális gépek helyszíni és felhőalapú biztonsági mentését is támogatja.

Győződjön meg arról, hogy a megfelelő Azure-funkciók lehetővé teszik, hogy globális, régiók közötti magas rendelkezésre állást biztosítson az eszközök és a felhasználók számára.

> [!NOTE]
> Ha az Azure úgy van beállítva, hogy engedélyezze a régiók közötti rendelkezésre állást, a Azure Time Series Insights nem szükséges további régiók közötti rendelkezésre állási konfiguráció.

### <a name="iot-and-event-hubs"></a>IoT és Event hubok

Néhány Azure IoT-szolgáltatás beépített üzleti katasztrófa-helyreállítási funkciókat is tartalmaz:

- [Azure IoT hub magas rendelkezésre állású vész-helyreállítás](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), amely a régión belüli redundanciát is magában foglalja
- [Azure Event Hubs házirendek](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure Storage-redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

A Time Series Insights más szolgáltatásokkal való integrálása további vész-helyreállítási lehetőségeket biztosít. Előfordulhat például, hogy az telemetria eljuttatott értesítéseket a Backup Azure Blob Storage-adatbázisa őrzi meg.

### <a name="time-series-insights"></a>Time Series Insights

Több módon is megtarthatja a Time Series Insights-adatait,-alkalmazásait és-szolgáltatásait, még akkor is, ha azok megszakadnak. 

Azonban előfordulhat, hogy az Azure Time Series-környezet teljes biztonsági másolatát is meg kell határoznia az alábbi célokból:

- Az adatok és a forgalom átirányításához a Time Series Insights kifejezetten *feladatátvételi példányként*
- Az adatok és a naplózási információk megőrzése

Általánosságban elmondható, hogy a Time Series Insights-környezet duplikálása a legjobb módszer, ha egy második Time Series Insights-környezetet hoz létre egy biztonsági mentési Azure-régióban. Az eseményeket az elsődleges esemény forrásaként is elküldi erre a másodlagos környezetre. Győződjön meg arról, hogy egy második dedikált fogyasztói csoportot használ. Kövesse a forrás üzleti katasztrófák helyreállítási irányelveit a korábban leírtak szerint.

Ismétlődő környezet létrehozásához:

1. Hozzon létre egy környezetet egy második régióban. További információért olvassa el [a Azure Portal új Time Series Insights környezet létrehozása](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)című témakört.
1. Hozzon létre egy második dedikált fogyasztói csoportot az eseményforrás számára.
1. Az eseményforrás összekötése az új környezettel. Győződjön meg arról, hogy a második dedikált fogyasztói csoportot jelölte ki.
1. Tekintse át a Time Series Insights [IoT hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) és [Event Hubs](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentációját.

Ha egy esemény következik be:

1. Ha az elsődleges régiót a vész-incidensek befolyásolják, átirányítja a műveleteket a biztonsági mentési Time Series Insights környezetbe.
1. A második régió használatával biztonsági mentést készíthet, és helyreállíthatja az összes Time Series Insights telemetria és a lekérdezési adatokat.

> [!IMPORTANT]
> Feladatátvétel esetén:
> 
> * Késés is előfordulhat.
> * Előfordulhat, hogy a rendszer a műveletek átirányítása közben az üzenet feldolgozásának pillanatnyi csúcsát is felhasználja.
> 
> További információért olvassa el a [Time Series Insights késleltetésének enyhítését](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)ismertető témakört.

