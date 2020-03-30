---
title: Az Azure Service Fabric figyelése ajánlott eljárások
description: Ajánlott eljárások és tervezési szempontok a fürtök és alkalmazások figyeléséhez az Azure Service Fabric használatával.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: af03223e8b007cbd2a00d54c3076056cd110ecc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551816"
---
# <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

[A figyelés és a diagnosztika](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) kritikus fontosságú a számítási feladatok fejlesztése, tesztelése és üzembe helyezése szempontjából bármilyen felhőalapú környezetben. Például nyomon követheti, hogyan használják az alkalmazásokat, a Service Fabric platform által végrehajtott műveleteket, az erőforrás-kihasználtságot a teljesítményszámlálókkal és a fürt általános állapotát. Ezeket az információkat a problémák diagnosztizálására és javítására használhatja, és megakadályozhatja azok jövőbeni előfordulását.

## <a name="application-monitoring"></a>Alkalmazások monitorozása

Az alkalmazásfigyelés nyomon követi az alkalmazás funkcióinak és összetevőinek használatos módját. Figyelheti alkalmazásait, és meggyőződhet arról, hogy a felhasználókat érintő problémák kivannak kapva. Az alkalmazásfigyelés az alkalmazás és a szolgáltatások fejlesztéséért felelős, mivel az az alkalmazás üzleti logikája szempontjából egyedülálló. Javasoljuk, hogy állítsa be az application monitoring [az Application Insights,](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet)az Azure alkalmazásfigyelési eszköz.

## <a name="cluster-monitoring"></a>Fürtmonitorozás

A Service Fabric egyik célja, hogy az alkalmazások rugalmas a hardverhibák. Ez a cél a platform rendszerszolgáltatásainak az infrastruktúra-problémák észlelési képességével és a fürt más csomópontjainak gyors feladatátvételi munkaterhelésekkel való észlelésével érhető el. De mi van, ha a rendszer szolgáltatások maguk is kérdések? Vagy ha egy számítási feladat üzembe helyezésének vagy áthelyezésének megkísérlése során megsértik a szolgáltatások elhelyezésére vonatkozó szabályokat? A Service Fabric diagnosztikát biztosít ezekhez és más problémákhoz, hogy biztosan értesüljön arról, hogy a Service Fabric platform hogyan kommunikál az alkalmazásokkal, a szolgáltatásokkal, a tárolókkal és a csomópontokkal.

Windows-fürtök esetén ajánlott a fürtfigyelés beállítása [a Diagnosztikai ügynök](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) és az Azure Monitor [naplóival.](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup)

Linux-fürtök esetén az Azure Monitor naplók is ajánlott eszköz az Azure platform és infrastruktúra figyelése. A Linux platform diagnosztikája eltérő konfigurációt igényel, ahogy azt a [Service Fabric Linux fürtesemények syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Infrastruktúrák monitorozása

[Az Azure Monitor naplók](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) ajánlott a fürtszintű események figyelése. Miután konfigurálta a Log Analytics-ügynököt a munkaterülettel az előző kapcsolatban leírtak szerint, képes lesz olyan teljesítménymutatókat gyűjteni, mint például a CPU-kihasználtság, a .NET teljesítményszámlálók, például a folyamatszintű CPU-kihasználtság, a Service Fabric teljesítményszámlálók, például a megbízható szolgáltatás ból való kivételek # és a tároló metrikák, például a CPU-kihasználtság.  Meg kell írnia a tárolónaplókat az stdout vagy stderr, hogy azok elérhetők lesznek az Azure Monitor naplók.

## <a name="watchdogs"></a>Házőrzők

Általában egy figyelő egy külön szolgáltatás, amely figyeli az állapotát és a terhelést a szolgáltatások között, pingvégpontok ping, és a fürt váratlan állapotesemények jelentése. Ez segíthet megelőzni azolyan hibákat, amelyek nem észlelhetők egyetlen szolgáltatás teljesítménye alapján. Watchdogs is egy jó hely a fogadó kódot, amely végrehajtja a javítási műveletet, amely nem igényel felhasználói beavatkozást, mint például a naplófájlok tisztítása a tárolóban bizonyos időközönként. Tekintse meg a mintafigyelő szolgáltatás implementációját a [Service Fabric Linux-fürtesemények ben syslog.](https://github.com/Azure-Samples/service-fabric-watchdog-service)

## <a name="next-steps"></a>További lépések

* Első lépések az alkalmazások programozása: [Alkalmazásszintű esemény és naplógenerálás.](service-fabric-diagnostics-event-generation-app.md)
* A lépéseket, hogy az Application Insights az alkalmazás hoz létre a [figyelő vel, és diagnosztizálni egy ASP.NET Core alkalmazás Service Fabric.](service-fabric-tutorial-monitoring-aspnet.md)
* További információ a platform figyeléséről és a Service Fabric által biztosított eseményekről: [Platformszintű esemény és naplógenerálás.](service-fabric-diagnostics-event-generation-infra.md)
* Az Azure Monitor naplóinak konfigurálása a Service Fabric szolgáltatással: [Az Azure Monitor-naplók beállítása fürthöz](service-fabric-diagnostics-oms-setup.md)
* Megtudhatja, hogyan állíthatja be az Azure Monitor naplók figyelési tárolók: [figyelése és diagnosztika a Windows-tárolók az Azure Service Fabric.](service-fabric-tutorial-monitoring-wincontainers.md)
* Tekintse meg a diagnosztikai problémákat és megoldásokat a Service Fabric szolgáltatással: [gyakori forgatókönyvek diagnosztizálása](service-fabric-diagnostics-common-scenarios.md)
* Ismerje meg az Azure-erőforrásokkal kapcsolatos általános [figyelési javaslatokat: Gyakorlati tanácsok – Figyelés és diagnosztika.](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)