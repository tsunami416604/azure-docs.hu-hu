---
title: A tárolók Azure Monitor áttekintése | Microsoft Docs
description: Ez a cikk azokat a tárolókat ismerteti Azure Monitor, amelyek az AK-tárolók bevezetési megoldását figyelik, valamint a általa nyújtott értéket az AK-fürtök és az Azure-Container Instances állapotának figyelésével.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 341dd28f6c1523e4b4c06da30a0a8ffc61b1c6f4
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75730736"
---
# <a name="azure-monitor-for-containers-overview"></a>A tárolók Azure Monitor áttekintése

A tárolók Azure Monitor a-ben üzembe helyezett tároló-munkaterhelések teljesítményének figyelésére szolgáló szolgáltatás:

- Az Azure Kubernetes szolgáltatásban (ak) üzemeltetett fürtök Managed Kubernetes
- Azure Container Instances
- Azure Stack vagy helyszíni környezetben üzemeltetett, önállóan felügyelt Kubernetes-fürtök
- Azure Red Hat OpenShift

A tárolók Azure Monitor támogatja a Linux és a Windows Server 2019 operációs rendszert futtató fürtöket. 

A tárolók monitorozása kritikus fontosságú, különösen akkor, ha éles fürtöt futtat, több alkalmazással.

A tárolók Azure Monitor a teljesítmény láthatóságát azáltal, hogy a mérőszámok API-n keresztül Kubernetes elérhető vezérlőket, csomópontokat és tárolókat gyűjt a memória és a processzor metrikáinak összegyűjtéséhez. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a Kubernetes-fürtök figyelését, a rendszer automatikusan összegyűjti a metrikákat és a naplókat a linuxos Log Analytics-ügynök egy tárolós verziójában. A metrikák a metrikák tárolójába íródnak, és a rendszer a napló adatait a [log Analytics](../log-query/log-query-overview.md) munkaterülethez társított naplók tárolójába írja. 

![Azure Monitor a tárolók architektúrához](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Mit nyújt a tárolók Azure Monitor?

A tárolók Azure Monitor a Azure Monitor különböző funkcióit használó átfogó figyelési élményt nyújt. Ezek a funkciók lehetővé teszik a Linux és a Windows Server 2019 operációs rendszert futtató Kubernetes-fürt teljesítményének és állapotának, valamint a tároló munkaterhelésének megismerését. A tárolók Azure Monitor a következőket teheti:

* Azonosítsa a csomóponton futó AK-tárolókat, valamint a processzor és a memória átlagos kihasználtságát. Ez az információ segít azonosítani az erőforrás szűk keresztmetszeteit.
* Azonosítsa a tároló-csoportok és a Azure Container Instancesban üzemeltetett tárolók processzorát és memóriájának kihasználtságát.  
* Annak meghatározása, hogy a tároló hol található egy vezérlőben vagy egy Pod-ban. Ez a tudás segítséget nyújt a vezérlő vagy a pod teljes teljesítményének megtekintéséhez. 
* Tekintse át a gazdagépen futó számítási feladatok erőforrás-kihasználtságát, amelyek nem kapcsolódnak a pod-t támogató szabványos folyamatokhoz.
* Ismerje meg a fürt viselkedését az átlag és a legnagyobb terhelések alatt. Ez a tudás segíthet a kapacitási igények azonosításában és a fürt által fenntartott maximális terhelés meghatározásában. 
* Riasztásokat állíthat be, amelyekkel proaktívan értesítheti Önt, vagy rögzítheti azt, ha a csomópontokon vagy tárolókban a CPU és a memória kihasználtsága túllépi a küszöbértékeket, vagy ha az infrastruktúra vagy a csomópontok állapota összesítéssel a fürt állapota megváltozik.
* A [Prometheus](https://prometheus.io/docs/introduction/overview/) -nal integrálva megtekintheti a csomópontokból és Kubernetes származó alkalmazások és munkaterhelések mérőszámait, és [lekérdezésekkel](container-insights-log-search.md) egyéni riasztásokat, irányítópultokat és részletes elemzéseket készíthet.
* Figyelheti a tároló munkaterheléseit [, amelyek](https://github.com/microsoft/OMS-docker/tree/aks-engine) a helyszíni és az [AK](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)-os motoron üzemelnek Azure stack.
* [Az Azure Red Hat OpenShift üzembe helyezett tároló-](../../openshift/intro-openshift.md)munkaterhelések figyelése.

    >[!NOTE]
    >Az Azure Red Hat OpenShift támogatása jelenleg nyilvános előzetes verzióban érhető el.
    >

A Windows Server-fürtök Linux-fürthöz képest történő figyelésének fő eltérései a következők:

- A memóriabeli RSS-metrika nem érhető el a Windows-csomópontok és-tárolók esetében.
- A lemezes tárolás kapacitására vonatkozó információk nem érhetők el Windows-csomópontok esetén.
- A tároló-naplók nem érhetők el Windows-csomópontokon futó tárolók esetén.
- Az élő adatszolgáltatások (előzetes verzió) funkcióinak támogatása a Windows-tároló naplói kivételével érhető el.
- Csak a pod környezetek figyelhetők meg, nem pedig a Docker-környezetek.
- Az előzetes kiadásban legfeljebb 30 Windows Server-tároló támogatott. Ez a korlátozás nem vonatkozik a Linux-tárolók esetében. 

Tekintse meg az alábbi videót, amely egy köztes szintű részletes bemutatót biztosít, amely segít megismerni az AK-fürt Azure Monitor for containers szolgáltatással való figyelését.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hogyan hozzáférni a szolgáltatáshoz?

A tárolók Azure Monitor kétféleképpen, Azure Monitor vagy közvetlenül a kiválasztott AK-fürtből érheti el. A Azure Monitor globális perspektívát biztosít az összes üzembe helyezett tárolóról, amelyek figyelése és nem, így lehetővé teszi az előfizetések és az erőforráscsoportok keresését és szűrését, majd a tárolók Azure Monitor való részletezését kiválasztott tároló.  Ellenkező esetben közvetlenül egy kiválasztott AK-tárolóból is elérheti a szolgáltatást az AK-ból származó lapról.  

![A tárolók Azure Monitor elérési módszereinek áttekintése](./media/container-insights-overview/azmon-containers-experience.png)

Ha az AK-n kívül futó Docker-és Windows-tároló gazdagépek figyelését és felügyeletét érdekli, tekintse meg a [tároló-figyelési megoldást](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Következő lépések

A Kubernetes-fürt figyelésének megkezdéséhez tekintse át, [hogyan engedélyezheti a tárolók Azure monitor](container-insights-onboard.md) a figyelés engedélyezéséhez szükséges követelmények és a rendelkezésre álló módszerek megismerése érdekében. 
