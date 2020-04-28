---
title: Azure Service Fabric monitorozás – ajánlott eljárások
description: Ajánlott eljárások és tervezési szempontok a fürtök és alkalmazások Azure Service Fabric használatával történő figyeléséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: af03223e8b007cbd2a00d54c3076056cd110ecc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75551816"
---
# <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

A [monitorozás és a diagnosztika](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) kritikus fontosságú a számítási feladatok bármilyen felhőalapú környezetben való fejlesztéséhez, teszteléséhez és üzembe helyezéséhez. Például nyomon követheti az alkalmazások felhasználásának módját, a Service Fabric platform által végrehajtott műveleteket, az erőforrások kihasználtságát a teljesítményszámlálók segítségével, valamint a fürt általános állapotát. Ezeket az információkat felhasználhatja a problémák diagnosztizálásához és kijavításához, és megakadályozhatja, hogy a későbbiekben is megtörténjenek.

## <a name="application-monitoring"></a>Alkalmazások monitorozása

Az alkalmazás figyelése nyomon követi, hogy az alkalmazás mely szolgáltatásait és összetevőit használja. Figyelje az alkalmazásokat, és győződjön meg arról, hogy a felhasználók által érintett problémák merülnek fel. Az alkalmazások figyelése az alkalmazás és a szolgáltatásai fejlesztésének felelőssége, mivel az alkalmazás üzleti logikája egyedi. Javasoljuk, hogy az alkalmazás figyelését [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), az Azure alkalmazás-figyelési eszközével állítsa be.

## <a name="cluster-monitoring"></a>Fürtmonitorozás

A Service Fabric egyik célja, hogy az alkalmazások a hardverhiba miatt rugalmasak legyenek. Ez a cél a platform rendszerszolgáltatásai által a fürt más csomópontjaira történő észlelési képességgel és a gyors feladatátvételsel kapcsolatos lehetőségekkel érhető el. Mi a teendő, ha a rendszerszolgáltatások problémái vannak? Vagy ha a számítási feladatok üzembe helyezésére vagy áthelyezésére tett kísérlet során megsértették a szolgáltatások elhelyezésére vonatkozó szabályokat? A Service Fabric diagnosztikát biztosít ezekhez és egyéb problémákhoz, hogy megbizonyosodjon arról, hogy a Service Fabric platform hogyan kommunikál az alkalmazásokkal, szolgáltatásokkal, tárolókkal és csomópontokkal.

Windows-fürtök esetén ajánlott beállítani a fürt figyelését [diagnosztikai ügynökkel](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) és [Azure monitor naplókkal](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Linux-fürtök esetében az Azure platform és az infrastruktúra figyelésére szolgáló Azure Monitor naplók is az ajánlott eszköz. A Linux platform diagnosztika különböző konfigurációt igényel, mint [Service Fabric Linux-fürt eseményei a syslog-ben](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Infrastruktúrák monitorozása

A fürt szintű események figyeléséhez [Azure monitor naplók](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) használata javasolt. Miután konfigurálta a Log Analytics-ügynököt a munkaterülettel az előző hivatkozásban leírtak szerint, olyan teljesítménymutatókat gyűjthet, mint például a CPU-kihasználtság, a .NET-teljesítményszámlálók, például a processzor kihasználtsági szintje, Service Fabric a teljesítményszámlálók, például a megbízható szolgáltatástól származó kivételek száma, valamint a tároló metrikái, például a CPU-kihasználtság.  Az stdout-ba vagy a stderr-be kell írnia a tároló naplóit, hogy azok elérhetők legyenek Azure Monitor naplókban.

## <a name="watchdogs"></a>Házőrző

A watchdog általában egy különálló szolgáltatás, amely figyeli az állapotot és a terhelést a szolgáltatások között, Pingeli a végpontokat, és nem várt állapotú eseményeket jelez a fürtben. Ez segít megakadályozni, hogy az észlelt hibák csak egyetlen szolgáltatás teljesítményén alapulnak. A watchdog is jó hely a kód futtatására, amely olyan javító műveleteket hajt végre, amelyek nem igényelnek felhasználói beavatkozást, például a naplófájlok bizonyos időközönkénti törlését. A syslog-ben [Service Fabric Linux-fürt eseményeiben](https://github.com/Azure-Samples/service-fabric-watchdog-service)tekintse meg a minta watchdog szolgáltatás implementációját.

## <a name="next-steps"></a>További lépések

* Az alkalmazások kialakításának első lépései: az [alkalmazás szintű események és a naplók generálása](service-fabric-diagnostics-event-generation-app.md).
* Hajtsa végre az alkalmazáshoz való Application Insights beállításának lépéseit, [és figyelje meg, hogyan diagnosztizálhatja a ASP.net Core alkalmazást a Service Fabricon](service-fabric-tutorial-monitoring-aspnet.md).
* További információ a platform figyeléséről és a Service Fabric által szolgáltatott eseményekről: [platform szintű esemény és napló létrehozása](service-fabric-diagnostics-event-generation-infra.md).
* Azure Monitor logs-integráció konfigurálása a Service Fabric: [fürt Azure monitor naplóinak beállítása](service-fabric-diagnostics-oms-setup.md)
* Megtudhatja, hogyan állíthat be Azure Monitor naplókat figyelési tárolók számára: [figyelés és diagnosztika Windows-tárolók számára az Azure Service Fabricban](service-fabric-tutorial-monitoring-wincontainers.md).
* Példa diagnosztikai problémákra és megoldásokra a Service Fabric: [gyakori forgatókönyvek diagnosztizálása](service-fabric-diagnostics-common-scenarios.md)
* Ismerje meg az Azure-erőforrásokra vonatkozó általános monitorozási javaslatokat: [ajánlott eljárások – monitorozás és diagnosztika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).