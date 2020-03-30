---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013880"
---
## <a name="business-disaster-recovery"></a>Üzleti katasztrófa utáni helyreállítás

Ez a szakasz az Azure Time Series Insights azon funkcióit ismerteti, amelyek az alkalmazásokat és szolgáltatásokat futtatják, még akkor is, ha katasztrófa történik *(üzleti vészhelyreállítás).*

### <a name="high-availability"></a>Magas rendelkezésre állás

Azure-szolgáltatásként a Time Series Insights bizonyos *magas rendelkezésre állású* funkciókat biztosít az Azure régió szintjén redundancia használatával. Az Azure például támogatja a vész-helyreállítási képességeket az Azure *régiók közötti rendelkezésre állási* szolgáltatásán keresztül.

Az Azure-on keresztül biztosított további magas rendelkezésre állású funkciók (és bármely Time Series Insights-példány számára elérhetők) a következők:

- **Feladatátvétel**: Az Azure [georeplikációt és terheléselosztást](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)biztosít.
- **Adatok visszaállítása** és **tárolásának helyreállítása:** Az Azure számos lehetőséget kínál [az adatok megőrzésére és helyreállítására](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Azure Site Recovery**: Az Azure webhely-helyreállítási funkciókat biztosít az [Azure Site Recovery szolgáltatáson](https://docs.microsoft.com/azure/site-recovery/)keresztül.
- **Azure Backup:** [Az Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) támogatja mind a helyszíni, mind az Azure virtuális gépek felhőbeli biztonsági mentését.

Győződjön meg arról, hogy engedélyezi a megfelelő Azure-funkciókat, hogy globális, régiók közötti, magas rendelkezésre állást biztosítson az eszközök és a felhasználók számára.

> [!NOTE]
> Ha az Azure úgy van beállítva, hogy engedélyezze a régiók közötti rendelkezésre állást, nincs szükség további régiók közötti rendelkezésre állási konfigurációra az Azure Time Series Insightsban.

### <a name="iot-and-event-hubs"></a>IoT- és eseményközpontok

Egyes Azure IoT-szolgáltatások beépített üzleti vész-helyreállítási funkciókat is tartalmaznak:

- [Az Azure IoT Hub magas rendelkezésre állású vész-helyreállítási ,](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)amely magában foglalja a régión belüli redundancia
- [Az Azure Event Hubs irányelvei](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Az Azure Storage redundanciája](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

A Time Series Insights integrálása a többi szolgáltatással további vész-helyreállítási lehetőségeket biztosít. Például az eseményközpontba küldött telemetriai adatok megőrződhetnek egy biztonsági mentési Azure Blob storage-adatbázisban.

### <a name="time-series-insights"></a>Time Series Insights

A Time Series Insights-adatok, -alkalmazások és -szolgáltatások számos módon futtathatják a versenyeket, még akkor is, ha azok megszakadnak. 

Azonban előfordulhat, hogy az Azure Time Series környezet teljes biztonsági másolata is szükséges a következő célokra:

- *Feladatátvételi példányként* kifejezetten a Time Series Insights számára az adatok és a forgalom
- Az adatok megőrzése és az adatok naplózása

Általában a Time Series Insights-környezet duplikálásának legjobb módja egy második Time Series Insights-környezet létrehozása egy biztonsági mentési Azure-régióban. Az események et is elküldi a másodlagos környezetaz elsődleges eseményforrásból. Győződjön meg arról, hogy egy második dedikált fogyasztói csoportot használ. Kövesse a forrás üzleti vész-helyreállítási irányelveit, ahogy azt korábban leírtuk.

Ismétlődő környezet létrehozása:

1. Hozzon létre egy környezetet egy második régióban. További információért olvassa el [Az új Time Series Insights-környezet létrehozása az Azure Portalon](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)című olvasni.
1. Hozzon létre egy második dedikált fogyasztói csoportot az eseményforráshoz.
1. Csatlakoztassa az eseményforrást az új környezethez. Győződjön meg arról, hogy a második dedikált fogyasztói csoportot jelölte ki.
1. Tekintse át a Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) és [az Event Hubs dokumentációját.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)

Esemény bekövetkezése esetén:

1. Ha az elsődleges régió tegy katasztrófa esemény, átirányítja a műveleteket a biztonsági mentési Time Series Insights környezetben.
1. A második régió használatával biztonsági másolatot készíthet és helyreállíthatja az összes Time Series Insights telemetriai és lekérdezési adatot.

> [!IMPORTANT]
> Feladatátvétel esetén:
> 
> * Késés is előfordulhat.
> * Az üzenetek feldolgozásának pillanatnyi kiugrása fordulhat elő, a műveletek átirányítása kor.
> 
> További információkért olvassa el [a Késés csökkentése a Time Series Insights alkalmazásban](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)című részt.

