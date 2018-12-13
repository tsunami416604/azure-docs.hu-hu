---
title: Ajánlott eljárások az Azure Kubernetes Service (AKS)
description: A fürt operátor és a fejlesztői gyakorlati tanácsok az Azure Kubernetes Service (AKS) alkalmazások elkészítésében és kezelésében gyűjteménye
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 12/07/18
ms.author: iainfou
ms.openlocfilehash: ef7dabc8bf792893380b80458d5fb20aa15cd909
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256468"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Fürt operátor és a fejlesztői ajánlott eljárások az Azure Kubernetes Service (AKS) az alkalmazások létrehozása és felügyelete

Hozhat létre és futtathat alkalmazásokat sikeresen Azure Kubernetes Service (AKS), a rendszer néhány fő szempontot értelmezhető és megvalósítható. Ezek a területek a következők: több-bérlős és ütemezési funkciókat, fürt és a pod biztonsági, vagy üzletmenet-folytonossági és vész-helyreállítási. Az alábbi gyakorlati tanácsokra a kezelők számára a fürt vannak csoportosítva, és a fejlesztők a következő területeken kapcsolatos szempontok megismerése és a valósít meg a megfelelő funkciókat.

Ezek ajánlott eljárások és elméleti cikkek írása történt az AKS termékcsoport, mérnöki csapat, és többek között a globális fekete övek (GBBs) mező csapatok együtt.

## <a name="cluster-operator-best-practices"></a>Fürt operátor ajánlott eljárások

A fürt felelősnek együttműködjenek alkalmazástulajdonosok és a fejlesztők igényeiknek ismertetése. Ezután használhatja a következő gyakorlati tanácsok az AKS-fürtök konfigurálásához szükség szerint.

**Több-bérlős rendszer**

* [Fürt elkülönítési ajánlott eljárásai](operator-best-practices-cluster-isolation.md)
    * Több-bérlős és logikai elkülönítése, a névterek tartalmaz.
* [Alapszintű ütemezési funkciókat ajánlott eljárásai](operator-best-practices-scheduler.md)
    * Tartalmazza a erőforráskvóták és pod megszakítás költségvetése használatával.
* [A scheduler speciális funkciók ajánlott eljárásai](operator-best-practices-advanced-scheduler.md)
    * Tartalmazza a elkerülésére és tolerations, csomópont választók és -affinitást, és a pod közötti kapcsolat és affinitást.
* [Ajánlott eljárások a hitelesítéshez és engedélyezéshez](operator-best-practices-identity.md)
    * Magában foglalja az Azure Active Directory szerepköralapú hozzáférés-vezérlést (RBAC), és a pod identitások használatával.

**Biztonság**

* [Ajánlott eljárások Fürtbiztonság és frissítések](operator-best-practices-cluster-security.md)
    * Magában foglalja a biztonságos hozzáférés az API-kiszolgálóhoz, a tároló hozzáférésének korlátozása és kezelése, frissítése és a csomópont újraindul.
* [Rendszerkép kezelése és biztonsági védelmének bevált gyakorlata](operator-best-practices-container-image-management.md)
    * Tartalmazza a lemezkép és a modulok, megbízható beállításjegyzékek és automatizált buildekig használatával alaplemezkép frissítések védelme...
* [Podok biztonsági védelmének bevált gyakorlata](developer-best-practices-pod-security.md)
    * Erőforrásokhoz való hozzáférés biztonságossá tétele, a hitelesítő adatok felfedésével korlátozása és a pod identitások és a digitális kulcstartók tartalmazza.

**Hálózati és tárolási**

* [Ajánlott eljárások a hálózati kapcsolat](operator-best-practices-network.md)
    * Magában foglalja a különböző hálózati modellek esetében bejövő és a webes alkalmazás tűzfalak (WAF) használ, és a csomópont SSH-hozzáférés biztonságossá tétele.
* [Tárolási és biztonsági védelmének bevált gyakorlata](operator-best-practices-storage.md)
    * Tartalmazza a megfelelő tárolási típusa és a csomópont méretét, dinamikus kiépítése, köteteket és biztonsági mentések kiválasztása.

**Nagyvállalati szintű számítási feladatok futtatása**

* [Ajánlott eljárások az üzleti folytonossági és vészhelyreállítási helyreállítási](operator-best-practices-multi-region.md)
    * Tartalmazza az Azure Traffic Manager és a tárolórendszerképek georeplikációs régiópárok, több fürt használatával.

## <a name="developer-best-practices"></a>Fejlesztői ajánlott eljárások

A fejlesztők vagy az alkalmazás tulajdonosa, a fejlesztői élmény egyszerűsítése érdekében, és adja meg kell application teljesítményigényeken.

* [Ajánlott eljárások az alkalmazásfejlesztők számára az erőforrások kezelése](developer-best-practices-resource-management.md)
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
