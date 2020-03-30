---
title: Az Azure Monitor tárolókhoz való áttekintése | Microsoft dokumentumok
description: Ez a cikk ismerteti az Azure Monitor tárolók, amelyek figyelik az AKS Container Insights-megoldás és az általa az Azure-ban az AKS-fürtök és a tárolópéldányok állapotának figyelése által biztosított értéket.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275229"
---
# <a name="azure-monitor-for-containers-overview"></a>A tárolókhoz készült Azure Monitor áttekintése

Az Azure Monitor tárolók egy olyan szolgáltatás, amelynek célja, hogy figyelemmel kíséri a tároló számítási feladatok üzembe helyezett:

- Az [Azure Kubernetes szolgáltatásban (AKS)](../../aks/intro-kubernetes.md) üzemeltetett felügyelt Kubernetes-fürtök
- Az Azure-ban az [AKS-motor](https://github.com/Azure/aks-engine) használatával üzemeltetett, saját irányítású Kubernetes-fürtök
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Az [Azure Stackben](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) vagy a helyszíni létesítményekben üzemeltetett, saját felügyelt Kubernetes-fürtök
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Az Azure Monitor a tárolók támogatja a Linux és a Windows Server 2019 operációs rendszert futtató fürtök. 

A tárolók figyelése kritikus fontosságú, különösen akkor, ha egy éles fürt ötös méretekben, több alkalmazással fut.

Az Azure Monitor tárolók hoz a teljesítmény láthatóságát a memória és a processzor metrikák a vezérlők, csomópontok és tárolók, amelyek a Kubernetes a Metrikák API-n keresztül érhetők el. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a figyelést a Kubernetes-fürtökből, a metrikák és a naplók automatikusan összegyűjtésre kerülnek a Linux-naplóügynök tárolós verzióján keresztül. Metrikák vannak írva a metrikák tárolására és a naplóadatok a [Log Analytics-munkaterülethez](../log-query/log-query-overview.md) társított naplók tárolójába. 

![Azure Monitor tárolók architektúrájának](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Mit biztosít az Azure Monitor a tárolókhoz?

Az Azure Monitor tárolók hoz átfogó figyelési élményt nyújt az Azure Monitor különböző funkcióinak használatával. Ezek a szolgáltatások lehetővé teszik a Linux és Windows Server 2019 operációs rendszert futtató Kubernetes-fürt, valamint a tárolók munkaterhelésének ismertetése. Az Azure Monitor tárolók hoz:

* Azonosítsa a csomóponton, valamint azok átlagos processzor- és memóriakihasználtságát futtató AKS-tárolókat. Ez a tudás segíthet azonosítani az erőforrások szűk keresztmetszeteit.
* Azonosítsa a tárolócsoportok és az Azure Container Instances-ben üzemeltetett tárolók processzor- és memóriakihasználtságát.  
* Azonosítsa, hogy a tároló hol található egy vezérlőben vagy egy podban. Ez a tudás segíthet a vezérlő vagy a pod általános teljesítményének megtekintésében. 
* Tekintse át az erőforrás-kihasználtsága a gazdagépen futó számítási feladatok, amelyek nem kapcsolódnak a pod ot támogató szabványos folyamatok.
* Ismerje meg a fürt viselkedését az átlagos és a legnehezebb terhelések alatt. Ez a tudás segíthet a kapacitásigények azonosításában és a fürt által fenntartható maximális terhelés meghatározásában. 
* Konfigurálja a riasztásokat, hogy proaktív módon értesítse Önt, vagy rögzítse, ha a processzor és a memória kihasználtsága csomópontokon vagy tárolókon meghaladja a küszöbértékeket, vagy ha az infrastruktúra vagy a csomópontok állapotának összesítése állapotváltozástörténik a fürtben.
* A [Prometheus](https://prometheus.io/docs/introduction/overview/) alkalmazás- és számításifeladatok-metrikák megtekintéséhez a csomópontokról és a Kubernetes-lekérdezések [segítségével](container-insights-log-search.md) egyéni riasztásokat, irányítópultokat és részletes elemzéseket hozhat létre.
* Figyelheti a helyszíni [AKS Engine](https://github.com/Azure/aks-engine) és az [Azure Stack AKS Engine üzembe](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)helyezett tárolószámítási feladatokat.
* Figyelheti [az Azure Red Hat OpenShift szolgáltatásban üzembe helyezett](../../openshift/intro-openshift.md)tárolók számítási feladatait.

    >[!NOTE]
    >Az Azure Red Hat OpenShift támogatása jelenleg nyilvános előzetes verzióban érhető el.
    >

A Windows Server-fürt linuxos fürthöz viszonyított figyelésének fő különbségei a következők:

- Memória RSS-metrika nem érhető el a Windows-csomópont és a tárolók.
- A Lemeztároló kapacitás adatai nem érhetők el a Windows-csomópontokhoz.
- A tárolónaplók nem érhetők el a Windows-csomópontokon futó tárolók hoz.
- A Live Data (előzetes verzió) szolgáltatás támogatása a Windows tárolónaplók kivételével érhető el.
- Csak pod környezetek figyelt, nem Docker-környezetek.
- Az előzetes verzióban legfeljebb 30 Windows Server-tároló támogatott. Ez a korlátozás nem vonatkozik a Linux-tárolókra. 

Tekintse meg az alábbi videót, amely egy köztes szintű mélymerülés, hogy segítsen megismerni az AKS-fürt az Azure Monitor tárolók.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hogyan érhetem el ezt a funkciót?

Az Azure Monitor a tárolók két módon, az Azure Monitor vagy közvetlenül a kiválasztott AKS-fürtből érheti el az Azure Monitort. Az Azure Monitor, rendelkezik egy globális perspektívát az összes telepített tárolók, amelyek figyelik, és amelyek nem, amely lehetővé teszi, hogy keressen és szűrhet az előfizetések és erőforráscsoportok között, majd részletezze az Azure Monitor tárolók a kiválasztott tárolót.  Ellenkező esetben a szolgáltatás közvetlenül a kijelölt AKS-tárolóból érhető el az AKS-lapról.  

![Az Azure Monitor tárolókhoz való hozzáférésének módszereinek áttekintése](./media/container-insights-overview/azmon-containers-experience.png)

Ha érdekli a Figyelés és a Windows-tárológazdak az AKS-en kívül futó figyelése és kezelése a konfiguráció, a naplózás és az erőforrás-használat megtekintéséhez, olvassa el a [Tárolófigyelés i.](../../azure-monitor/insights/containers.md)

## <a name="next-steps"></a>További lépések

A Kubernetes-fürt figyelésének megkezdéséhez tekintse át [a Hogyan engedélyezheti az Azure Monitor tárolók](container-insights-onboard.md) a követelmények és a rendelkezésre álló módszerek a figyelés engedélyezéséhez. 
