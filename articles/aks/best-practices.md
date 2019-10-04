---
title: Ajánlott eljárások az Azure Kubernetes Service (AKS)
description: A fürt operátor és a fejlesztői gyakorlati tanácsok az Azure Kubernetes Service (AKS) alkalmazások elkészítésében és kezelésében gyűjteménye
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/07/2018
ms.author: mlearned
ms.openlocfilehash: 7127894b364ac8f0fe1d87e13150d5522f5473e2
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615964"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Fürt operátor és a fejlesztői ajánlott eljárások az Azure Kubernetes Service (AKS) az alkalmazások létrehozása és felügyelete

Hozhat létre és futtathat alkalmazásokat sikeresen Azure Kubernetes Service (AKS), a rendszer néhány fő szempontot értelmezhető és megvalósítható. Ezek a területek a következők: több-bérlős és ütemezési funkciókat, fürt és a pod biztonsági, vagy üzletmenet-folytonossági és vész-helyreállítási. Az alábbi gyakorlati tanácsokra a kezelők számára a fürt vannak csoportosítva, és a fejlesztők a következő területeken kapcsolatos szempontok megismerése és a valósít meg a megfelelő funkciókat.

Ezek ajánlott eljárások és elméleti cikkek írása történt az AKS termékcsoport, mérnöki csapat, és többek között a globális fekete övek (GBBs) mező csapatok együtt.

## <a name="cluster-operator-best-practices"></a>Fürt operátor ajánlott eljárások

A fürt felelősnek együttműködjenek alkalmazástulajdonosok és a fejlesztők igényeiknek ismertetése. Ezután használhatja a következő gyakorlati tanácsok az AKS-fürtök konfigurálásához szükség szerint.

**Több-bérlős rendszer**

* [Ajánlott fürtelkülönítési eljárások](operator-best-practices-cluster-isolation.md)
    * Több-bérlős és logikai elkülönítése, a névterek tartalmaz.
* [Ajánlott eljárások a Scheduler alapszintű funkcióihoz](operator-best-practices-scheduler.md)
    * Tartalmazza a erőforráskvóták és pod megszakítás költségvetése használatával.
* [Ajánlott eljárások a Scheduler speciális funkcióihoz](operator-best-practices-advanced-scheduler.md)
    * Tartalmazza a elkerülésére és tolerations, csomópont választók és -affinitást, és a pod közötti kapcsolat és affinitást.
* [Ajánlott hitelesítési és engedélyezési eljárások](operator-best-practices-identity.md)
    * Magában foglalja az Azure Active Directory szerepköralapú hozzáférés-vezérlést (RBAC), és a pod identitások használatával.

**Biztonság**

* [A fürtbiztonság és a frissítések ajánlott eljárásai](operator-best-practices-cluster-security.md)
    * Magában foglalja a biztonságos hozzáférés az API-kiszolgálóhoz, a tároló hozzáférésének korlátozása és kezelése, frissítése és a csomópont újraindul.
* [Rendszerkép kezelése és biztonsági védelmének bevált gyakorlata](operator-best-practices-container-image-management.md)
    * Tartalmazza az lemezkép és a modulok és automatizált buildekig alaplemezkép frissítések biztonságossá tétele.
* [Podok biztonsági védelmének bevált gyakorlata](developer-best-practices-pod-security.md)
    * Erőforrásokhoz való hozzáférés biztonságossá tétele, a hitelesítő adatok felfedésével korlátozása és a pod identitások és a digitális kulcstartók tartalmazza.

**Hálózati és tárolási**

* [Ajánlott eljárások a hálózati kapcsolat](operator-best-practices-network.md)
    * Magában foglalja a különböző hálózati modellek esetében bejövő és a webes alkalmazás tűzfalak (WAF) használ, és a csomópont SSH-hozzáférés biztonságossá tétele.
* [A tárolás és a biztonsági mentés ajánlott eljárásai](operator-best-practices-storage.md)
    * Tartalmazza a megfelelő tárolási típusa és a csomópont méretét, dinamikus kiépítése, köteteket és biztonsági mentések kiválasztása.

**Nagyvállalati szintű számítási feladatok futtatása**

* [Ajánlott eljárások az üzleti folytonossági és vészhelyreállítási helyreállítási](operator-best-practices-multi-region.md)
    * Tartalmazza az Azure Traffic Manager és a tárolórendszerképek georeplikációs régiópárok, több fürt használatával.

## <a name="developer-best-practices"></a>Fejlesztői ajánlott eljárások

A fejlesztők vagy az alkalmazás tulajdonosa, a fejlesztői élmény egyszerűsítése érdekében, és adja meg kell application teljesítményigényeken.

* [Ajánlott erőforrás-kezelési eljárások alkalmazásfejlesztők számára](developer-best-practices-resource-management.md)
    * Magában foglalja a pod erőforrás-kérelmek és-korlátok, a fejlesztői eszközök és alkalmazások hibáinak keresése.
* [Podok biztonsági védelmének bevált gyakorlata](developer-best-practices-pod-security.md)
    * Erőforrásokhoz való hozzáférés biztonságossá tétele, a hitelesítő adatok felfedésével korlátozása és a pod identitások és a digitális kulcstartók tartalmazza.

## <a name="kubernetes--aks-concepts"></a>Kubernetes és az AKS-fogalmak

Az egyes szolgáltatásait és összetevőit az ajánlott eljárások megismerése érdekében fürtök az Azure Kubernetes Service (AKS) a következő elméleti cikkeket is látható:

* [Kubernetes alapfogalmak](concepts-clusters-workloads.md)
* [Hozzáférési és azonosító](concepts-identity.md)
* [Biztonsági alapelvek](concepts-security.md)
* [Hálózati fogalmak](concepts-network.md)
* [Tárolási lehetőségek](concepts-storage.md)
* [Méretezési beállítások](concepts-scale.md)

## <a name="next-steps"></a>További lépések

Ha AKS használatának megkezdéséhez szüksége, kövesse az a rövid útmutatók való üzembe helyezése az Azure Kubernetes Service (AKS) fürt segítségével a [Azure CLI-vel](kubernetes-walkthrough.md) vagy [az Azure portal](kubernetes-walkthrough-portal.md).
