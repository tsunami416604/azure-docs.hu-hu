---
title: Eseményösszesítés linuxos Azure-diagnosztikával
description: Ismerje meg az események összesítését és gyűjtését a LAD használatával az Azure Service Fabric-fürtök figyeléséhez és diagnosztikájához.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: fdb78498d33416ef21b2e2b0f498e7afa6a58d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609961"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Eseményösszesítés és -gyűjtés linuxos Azure-diagnosztika használatával
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Ha egy Azure Service Fabric-fürtöt futtat, célszerű a naplókat egy központi helyen lévő összes csomópontról gyűjteni. A naplók központi helyen történő elhelyezése segít a fürtben, illetve az adott fürtben futó alkalmazásokban és szolgáltatásokban felmerülő problémák elemzésében és elhárításában.

A naplók feltöltésének és gyűjtésének egyik módja a Linux Azure Diagnostics (LAD) bővítmény használata, amely feltölti a naplókat az Azure Storage-ba, és rendelkezik a naplók küldésére az Azure Application Insights vagy az Event Hubs számára. Egy külső folyamat is használhatja az eseményeket a tárolóból, és helyezze el őket egy elemzési platform termék, például [az Azure Monitor naplók](../log-analytics/log-analytics-service-fabric.md) vagy más log-elemzési megoldás.

## <a name="log-and-event-sources"></a>Napló- és eseményforrások

### <a name="service-fabric-platform-events"></a>Service Fabric platformesemények
A Service Fabric néhány beépített naplót bocsát ki az [LTTng-en](https://lttng.org)keresztül, beleértve az operatív eseményeket vagy a futásidejű eseményeket. Ezek a naplók a fürt Erőforrás-kezelő sablonja által megadott helyen tárolódnak. A tárfiók részleteinek beszerzéséhez vagy beállításához keresse meg az **AzureTableWinFabETWQueryable** címkét, és keresse meg a **StoreConnectionString**kifejezést.

### <a name="application-events"></a>Alkalmazásesemények
 Az alkalmazások és szolgáltatások kódjából kibocsátott események, ahogy azt Ön a szoftver műszerezése során meghatározta. Bármilyen naplózási megoldást használhat, amely szövegalapú naplófájlokat ír – például LTTng. További információt az LTTng dokumentációjában talál az alkalmazás nyomon követéséről.

[A szolgáltatások figyelése és diagnosztizálása a helyi gépfejlesztési beállításokban.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## <a name="deploy-the-diagnostics-extension"></a>A Diagnosztika bővítmény telepítése
A naplók gyűjtésének első lépése a diagnosztikai bővítmény üzembe helyezése a Service Fabric-fürt minden egyes virtuális gépén. A Diagnosztikai bővítmény minden virtuális gép naplóit gyűjti, és feltölti őket a megadott tárfiókba. 

Ha a fürt létrehozása részeként telepíti a diagnosztikai bővítményt a fürt ben lévő virtuális gépekre, állítsa **a Diagnosztika** **beállítását Be állapotra.** A fürt létrehozása után ezt a beállítást nem módosíthatja a portál használatával, ezért el kell végrehajtania a megfelelő módosításokat az Erőforrás-kezelő sablonban.

Ez úgy konfigurálja a LAD-ügynököt, hogy a megadott naplófájlokat figyelje. Amikor új sort fűz a fájlhoz, létrehoz egy syslog bejegyzést, amelyet a rendszer a megadott tárolóba (táblába) küld.


## <a name="next-steps"></a>További lépések

1. Ha részletesebben szeretné megtudni, hogy milyen eseményeket kell megvizsgálnia a hibaelhárítás során, olvassa el az [LTTng dokumentációját](https://lttng.org/docs) és [a LAD használata című témakört.](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux)
2. [Állítsa be a Log Analytics-ügynököt a](service-fabric-diagnostics-event-analysis-oms.md) metrikák összegyűjtéséhez, a fürtön üzembe helyezett tárolók figyeléséhez és a naplók megjelenítéséhez 
