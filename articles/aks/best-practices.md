---
title: Ajánlott eljárások az Azure Kubernetes szolgáltatás (AKS)
description: A fürtüzemeltető és a fejlesztői gyakorlati tanácsok gyűjteménye az Azure Kubernetes-szolgáltatásban (AKS) alkalmazások létrehozásához és kezeléséhez
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 627eeffd18a479486e5a682da06bf89cd5f8f0e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596317"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) alkalmazásainak létrehozásához és kezeléséhez a fürtüzemeltető és a fejlesztői gyakorlati tanácsok

Az Azure Kubernetes-szolgáltatásban (AKS) sikeresen hozhat létre és futtatható alkalmazásokhoz néhány kulcsfontosságú szempontot kell megérteni és megvalósítani. Ezek a területek közé tartozik a több-bérlős és ütemező funkciók, fürt és pod biztonság, vagy az üzletmenet folytonossága és a vész-helyreállítási. Az alábbi ajánlott eljárások csoportosítva segítenek a fürtüzemeltetőknek és a fejlesztőknek megérteni az egyes területek szempontjait, és megvalósítani a megfelelő szolgáltatásokat.

Ezeket az ajánlott eljárásokat és koncepcionális cikkeket az AKS termékcsoporttal, a mérnöki csapatokkal és a helyszíni csapatokkal, köztük a globális feketeövekkel (GBB) együtt írták.

## <a name="cluster-operator-best-practices"></a>A fürtkezelővel kapcsolatos gyakorlati tanácsok

Fürtoperátorként működjön együtt az alkalmazástulajdonosokkal és a fejlesztőkkel az igényeik megismerése érdekében. Ezután az alábbi gyakorlati tanácsok segítségével konfigurálhatja az AKS-fürtök, ha szükséges.

**Több-bérlős rendszer**

* [Ajánlott fürtelkülönítési eljárások](operator-best-practices-cluster-isolation.md)
    * Több-bérlős alapvető összetevőket és logikai elkülönítést tartalmaz a névterekkel.
* [Ajánlott eljárások a Scheduler alapszintű funkcióihoz](operator-best-practices-scheduler.md)
    * Erőforráskvóták és pod megszakítási költségvetések használatával.
* [Ajánlott eljárások a Scheduler speciális funkcióihoz](operator-best-practices-advanced-scheduler.md)
    * Magában foglalja a taints és tolerations, csomópont választók és affinitás, valamint a podok közötti affinitás és anti-affinitás.
* [Ajánlott hitelesítési és engedélyezési eljárások](operator-best-practices-identity.md)
    * Tartalmazza az Azure Active Directoryval való integrációt, szerepköralapú hozzáférés-vezérlés (RBAC) és pod identitások használatával.

**Biztonság**

* [A fürtbiztonság és a frissítések ajánlott eljárásai](operator-best-practices-cluster-security.md)
    * Magában foglalja az API-kiszolgálóhoz való hozzáférés biztosítását, a tárolók elérésének korlátozását, valamint a frissítések és a csomópont-újraindítások kezelését.
* [Gyakorlati tanácsok a tárolórendszerkép-kezeléshez és -biztonsághoz](operator-best-practices-container-image-management.md)
    * Magában foglalja a lemezkép és a futásidők, valamint az alaplemezképek frissítéseire épülő automatikus buildek biztosítását.
* [Gyakorlati tanácsok a pod biztonságához](developer-best-practices-pod-security.md)
    * Az erőforrásokhoz való hozzáférés biztonságossá tétele, a hitelesítő adatok kitettségének korlátozása, valamint a pod identitások és a digitális kulcstartók használata.

**Hálózat és tárolás**

* [Gyakorlati tanácsok a hálózati kapcsolathoz](operator-best-practices-network.md)
    * Különböző hálózati modelleket, bejárási és webalkalmazás-tűzfalak (WAF) használatával, valamint az SSH-csomópont-hozzáférés biztosításával.
* [A tárolás és a biztonsági mentés ajánlott eljárásai](operator-best-practices-storage.md)
    * Magában foglalja a megfelelő tárolási típus és csomópont méretének kiválasztását, a dinamikus üzembe építési köteteket és az adatbiztonsági mentéseket.

**Vállalati használatra kész munkaterhelések futtatása**

* [Bevált gyakorlatok az üzletmenet folytonosságához és a katasztrófa utáni helyreállításhoz](operator-best-practices-multi-region.md)
    * Magában foglalja a régiópárok, több fürt az Azure Traffic Manager és a tárolólemezképek georeplikációját.

## <a name="developer-best-practices"></a>A fejlesztőknek ajánlott eljárások

Fejlesztőként vagy alkalmazástulajdonosként egyszerűsítheti a fejlesztési élményt, és meghatározhatja az alkalmazás teljesítményigényeinek meghatározását.

* [Ajánlott erőforrás-kezelési eljárások alkalmazásfejlesztők számára](developer-best-practices-resource-management.md)
    * Pod erőforrás-kérelmek és korlátok definiálását, fejlesztői eszközök konfigurálását és az alkalmazásproblémák ellenőrzését tartalmazza.
* [Gyakorlati tanácsok a pod biztonságához](developer-best-practices-pod-security.md)
    * Az erőforrásokhoz való hozzáférés biztonságossá tétele, a hitelesítő adatok kitettségének korlátozása, valamint a pod identitások és a digitális kulcstartók használata.

## <a name="kubernetes--aks-concepts"></a>Kubernetes / AKS fogalmak

Az ajánlott eljárások egyes funkcióinak és összetevőinek megértéséhez tekintse meg az alábbi általános cikkeket az Azure Kubernetes-szolgáltatás (AKS) fürtjeiről:

* [Kubernetes alapfogalmak](concepts-clusters-workloads.md)
* [Hozzáférés és identitás](concepts-identity.md)
* [Biztonsági alapelvek](concepts-security.md)
* [Hálózati fogalmak](concepts-network.md)
* [Tárolási lehetőségek](concepts-storage.md)
* [Méretezési beállítások](concepts-scale.md)

## <a name="next-steps"></a>További lépések

Ha el kell kezdeni e-t az AKS-sel, kövesse az egyik rövid útmutatót egy Azure Kubernetes-szolgáltatás (AKS) fürt üzembe helyezéséhez az [Azure CLI](kubernetes-walkthrough.md) vagy az [Azure Portal](kubernetes-walkthrough-portal.md)használatával.
