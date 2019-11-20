---
title: Tárolók az Azure Monitor szolgáltatás áttekintése |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Monitor-tárolókhoz, amely figyeli az AKS Container Insights megoldás és az érték azt tesz lehetővé, hogy az AKS-fürt és az Azure Container Instances állapotának figyelése.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 97aad6f3853de07a3c4d2fc694c1dcefbd0e82a4
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195118"
---
# <a name="azure-monitor-for-containers-overview"></a>Az Azure Monitor tárolók áttekintése

A tárolók Azure Monitor a-ben üzembe helyezett tároló-munkaterhelések teljesítményének figyelésére szolgáló szolgáltatás:

- Az Azure Kubernetes szolgáltatásban (ak) üzemeltetett fürtök Managed Kubernetes
- Azure Container Instances
- Azure Stack vagy helyszíni környezetben üzemeltetett, önállóan felügyelt Kubernetes-fürtök
- Azure Red Hat OpenShift

A tárolók alapvető fontosságú, különösen akkor, ha nagy mennyiségű, több alkalmazással rendelkező egy éles fürtöt futtat.

Tárolók az Azure Monitor lehetővé teszi gyűjtését memória és a mérőszámok a processzor teljesítménye látható-e a tartományvezérlők, a csomópontok és a Kubernetes, a metrikák API-n keresztül a rendelkezésre álló tárolók. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a Kubernetes-fürtök figyelését, a rendszer automatikusan összegyűjti a metrikákat és a naplókat a linuxos Log Analytics-ügynök egy tárolós verziójában. A metrikák a metrikák tárolójába íródnak, és a rendszer a napló adatait a [log Analytics](../log-query/log-query-overview.md) munkaterülethez társított naplók tárolójába írja. 

![Azure Monitor a tárolók architektúrához](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Az Azure Monitor-tárolók biztosítják a funkciója?

A Azure Monitor a tárolók számára átfogó figyelési élményt nyújt Azure Monitor különböző funkciói segítségével, így megismerheti a Kubernetes-fürt teljesítményét és állapotát, valamint a tároló munkaterheléseit. A tárolók Azure Monitor a következőket teheti:

* AKS-tárolók, a csomópont és a processzor és memória átlagos kihasználtság futó azonosításához. A Tudásbázis segítségével erőforrás szűk keresztmetszetek azonosítása.
* Azonosítsa a tárolócsoportok és a tárolók az Azure Container Instances szolgáltatásban üzemeltetett processzor és memória felhasználását.  
* Annak meghatározása, hogy a tároló hol található egy vezérlőben vagy egy Pod-ban. A Tudásbázis segítségével megtekintheti a vezérlő vagy a pod általános teljesítménye. 
* Tekintse át az erőforrás-használat, a gazdagépen futó számítási feladatok, amely a szabványos folyamatokat, amelyek támogatják a pod kapcsolódnak.
* Ismerje meg a fürt átlagos és a leggyakrabban használt terhelések viselkedését. Ennek segítségével azonosíthatja a kapacitásbeli szükségleteket, és döntse el, a legnagyobb terhelést, amely képes elviselni a fürt. 
* Riasztásokat állíthat be, amelyekkel proaktívan értesítheti Önt, vagy rögzítheti azt, ha a csomópontokon vagy tárolókban a CPU és a memória kihasználtsága túllépi a küszöbértékeket, vagy ha az infrastruktúra vagy a csomópontok állapota összesítéssel a fürt állapota megváltozik.
* A [Prometheus](https://prometheus.io/docs/introduction/overview/) -nal integrálva megtekintheti a csomópontokból és Kubernetes származó alkalmazások és munkaterhelések mérőszámait, és [lekérdezésekkel](container-insights-log-search.md) egyéni riasztásokat, irányítópultokat és részletes elemzéseket készíthet.

    >[!NOTE]
    >A Prometheus támogatása jelenleg a nyilvános előzetes verzióban érhető el.
    >

* Figyelheti a tároló munkaterheléseit [, amelyek](https://github.com/microsoft/OMS-docker/tree/aks-engine) a helyszíni és az [AK](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)-os motoron üzemelnek Azure stack.
* [Az Azure Red Hat OpenShift üzembe helyezett tároló-](../../openshift/intro-openshift.md)munkaterhelések figyelése.

Tekintse meg az alábbi videót, amely egy köztes szintű részletes bemutatót biztosít, amely segít megismerni az AK-fürt Azure Monitor for containers szolgáltatással való figyelését.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hogyan érhetem el ezt a szolgáltatást?

Az Azure Monitor-tárolókhoz az Azure Monitor vagy közvetlenül a kijelölt AKS-fürt két módon érheti el. A Azure Monitor globális perspektívát biztosít az összes üzembe helyezett tárolóról, amelyek figyelése és nem, így lehetővé teszi az előfizetések és az erőforráscsoportok keresését és szűrését, majd a tárolók Azure Monitor való részletezését kiválasztott tároló.  Ellenkező esetben közvetlenül egy kiválasztott AK-tárolóból is elérheti a szolgáltatást az AK-ból származó lapról.  

![Hozzáférés az Azure Monitor-tárolók módszereinek áttekintése](./media/container-insights-overview/azmon-containers-experience.png)

Ha az AK-n kívül futó Docker-és Windows-tároló gazdagépek figyelését és felügyeletét érdekli, tekintse meg a [tároló-figyelési megoldást](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Következő lépések

A Kubernetes-fürt figyelésének megkezdéséhez tekintse át, [hogyan engedélyezheti a tárolók Azure monitor](container-insights-onboard.md) a figyelés engedélyezéséhez szükséges követelmények és a rendelkezésre álló módszerek megismerése érdekében. 
