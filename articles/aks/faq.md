---
title: Gyakori kérdések az Azure Kubernetes Service (AKS)
description: Válaszok az Azure Kubernetes Service (AKS) kapcsolatos gyakori kérdésekre.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.openlocfilehash: 1cc03cbcffc5253e8b357b6702cd21c45740ff81
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514490"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>– Azure Kubernetes Service (AKS) kapcsolatos gyakori kérdések

Ez a cikk címek gyakori kérdések az Azure Kubernetes Service (AKS) kapcsolatos.

## <a name="which-azure-regions-currently-provide-aks"></a>Mely Azure-régiók jelenleg elérhető AKS?

Az elérhető régiók teljes listáját lásd: [AKS régiók és rendelkezésre állás][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Az AKS támogatja az automatikus skálázás csomópont?

Igen, az automatikus skálázás keresztül érhető el a [Kubernetes méretező] [ auto-scaler] Kubernetes 1.10 kezdődően. A manuális konfigurálása és a fürt automatikus méretező használatához további információkért lásd: [fürt az automatikus skálázás AKS][aks-cluster-autoscale].

Csomópontok szerepkörtípusok (jelenleg előzetes verzióban érhető el az aks-ben) is használhatja a beépített fürt méretező. További információkért lásd: [automatikus méretezése egy fürtöt az aks-ben alkalmazás figyelembevételével][aks-cluster-autoscaler].

## <a name="does-aks-support-kubernetes-rbac"></a>Támogatja az AKS Kubernetes RBAC?

Igen, Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) is alapértelmezés szerint a engedélyezve van, amikor az Azure CLI-vel való létrehozása. RBAC használata az Azure portal vagy a sablonok által létrehozott fürtök esetén engedélyezhető.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Üzembe helyezhetem az AKS a meglévő virtuális hálózatban?

Igen, telepíthet egy AKS-fürtöt egy meglévő virtuális hálózatban használatával a [speciális hálózatkezelési funkció][aks-advanced-networking].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Tehetők a Kubernetes API-kiszolgálóhoz elérhető csak a saját virtuális hálózaton belül?

Jelenleg nem. A Kubernetes API-kiszolgálóhoz van közzétéve, mert egy nyilvános teljesen minősített tartományneve (FQDN). Szabályozhatja a hozzáférést a fürthöz való használatával [Kubernetes rbac-RÓL és az Azure Active Directory (Azure AD)][aks-rbac-aad].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>AKS-ügyfélcsomóponttal rendelkezik érvényes biztonsági frissítések?

Az Azure automatikusan alkalmazza a Linux-csomópontok a fürtben éjszakai ütemezett biztonsági javítások. Azonban Ön felelős azért, hogy szükséges-e ezen csomópontok újraindulásakor, Linux. A csomópont újraindítása számos lehetősége van:

- Manuálisan keresztül az Azure portal vagy az Azure parancssori felület.
- Az AKS-fürt frissítésével. A fürtfrissítések [Ez a három csomópontunk, és a csomópont kiürítési] [ cordon-drain] automatikusan és a egy új csomópontot online a legújabb Ubuntu-rendszerképet és a egy új biztonsági javítást vagy egy Kubernetes-alverzió. További információkért lásd: [AKS-fürt frissítése][aks-upgrade].
- Használatával [Kured](https://github.com/weaveworks/kured), egy nyílt forráskódú újraindítás démon a Kubernetes esetében. Kured fut, mint egy [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) , és a egy fájlt, amely azt jelzi, hogy újraindítás szükséges meglétének minden csomópont figyeli. A fürtön, az operációs rendszer-újraindítások azonos által kezelt [Ez a három csomópontunk, és a kiürítési folyamat] [ cordon-drain] mint a fürtfrissítések.

Kured használatával kapcsolatos további információkért lásd: [biztonsági és a kernel-frissítések alkalmazása a csomópontok az aks-ben][node-updates-kured].

### <a name="windows-server-nodes"></a>A Windows Server-csomópontok

A Windows Server-csomópontok (jelenleg előzetes verzióban érhető el az aks-ben) a Windows Update futtatása és automatikusan alkalmazza a legújabb frissítéseket. A Windows Update kiadási ciklus, és a saját érvényesítési folyamat rendszeres időközönként meg kell hajtsa végre a Windows Server-csomóponton vagy tárolókészleteit a frissítés az AKS-fürt. A frissítés során hoz létre a csomópontokat, amelyeken a legújabb Windows Server-rendszerképet és azok javításait, majd eltávolítja a régebbi csomópontok. Ezen folyamatról további információkért lásd: [frissítése az aks-ben csomópontkészletek][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Két erőforráscsoport miért jönnek létre az aks-sel?

Minden egyes az AKS üzembe helyezési kiterjedő két erőforráscsoport:

1. Az első erőforráscsoport létrehozásához. Csak a Kubernetes szolgáltatás-erőforrást tartalmazó csoport. Az AKS erőforrás-szolgáltató üzembe helyezése során automatikusan hoz létre a második erőforráscsoport. A második erőforráscsoport például *MC_myResourceGroup_myAKSCluster_eastus*. Az erőforráscsoport nevét, a második megadására vonatkozó további információkért lásd: a következő szakaszban.
1. A második erőforráscsoportja, mint például *MC_myResourceGroup_myAKSCluster_eastus*, a fürthöz társított infrastruktúrát erőforrásokat tartalmaz. Ilyen erőforrások többek között a Kubernetes csomópont virtuális gépek, virtuális hálózati és storage. Ez az erőforráscsoport célja erőforrás karbantartása leegyszerűsítése érdekében.

Ha hoz létre az AKS-fürt a használni kívánt erőforrások, például a storage-fiókok vagy fenntartott nyilvános IP-címeket elhelyezése egy az automatikusan létrehozott erőforráscsoportot.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>Biztosíthat a saját nevét az AKS-infrastruktúra erőforráscsoport?

Igen. Alapértelmezés szerint az AKS erőforrás-szolgáltató automatikusan létrehoz egy másodlagos erőforráscsoportot (például *MC_myResourceGroup_myAKSCluster_eastus*) üzembe helyezése során. Ahhoz, hogy megfeleljenek a vállalati házirenddel, adja meg a saját felügyelt fürt nevét (*MC_* ) erőforráscsoportot.

Adja meg a saját erőforráscsoport neve, telepítse a [aks előzetes] [ aks-preview-cli] Azure CLI-bővítmény verziója *0.3.2-es verzióra* vagy újabb. Az AKS-fürt létrehozásakor a [az aks létrehozása] [ az-aks-create] parancsot, használja a *– csomópont-resource-group* paramétert, és adja meg az erőforráscsoport nevét. Ha Ön [egy Azure Resource Manager-sablonnal] [ aks-rm-template] AKS-fürt üzembe helyezéséhez használatával adhatja meg az erőforráscsoport neve a *nodeResourceGroup* tulajdonság.

* A másodlagos erőforráscsoport automatikusan létrejön a Azure-erőforrás-szolgáltató saját előfizetésében.
* Egy egyéni erőforráscsoport-nevet is megadhat, csak a fürt létrehozásakor.

Végzett munka során a *MC_* erőforráscsoportot, ne feledje, hogy nem lehet:

* Adjon meg egy meglévő erőforráscsoportot a *MC_* csoport.
* Adjon meg egy másik előfizetéshez tartozó a *MC_* erőforráscsoportot.
* Módosítsa a *MC_* erőforráscsoport-nevet a fürt létrehozása után.
* Belül a felügyelt erőforrások nevét adja meg a *MC_* erőforráscsoportot.
* Módosítja vagy törli a felügyelt erőforrások címkék a *MC_* erőforráscsoportot. (Lásd a következő szakaszban további információt.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Címkék és egyéb tulajdonságok MC_ erőforráscsoporthoz tartozik, az AKS-erőforrások is módosíthatja?

Ha módosítja vagy törli az Azure által létrehozott címkék és egyéb erőforrás-tulajdonságok a *MC_* erőforráscsoport, a, például a méretezés és a hibák frissítése nem várt eredmények kérhetők. Az AKS létrehozásához és módosításához az egyéni címkéket teszi lehetővé. Érdemes létrehozni vagy módosítani a egyéni címkéket, például, egy üzleti egységet vagy költség center hozzárendelni. Az erőforrások alapján módosításával a *MC_* az AKS-fürtöt, az megszakítja a szolgáltatásiszint-célkitűzés (SLO). További információkért lásd: [Does AKS kínál egy szolgáltatásiszint-szerződést?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Milyen Kubernetes már a betegfelvétel tartományvezérlők támogatja az AKS? Is már a betegfelvétel vezérlők hozzáadásának vagy eltávolításának?

Az AKS támogatja a következő [már a betegfelvétel tartományvezérlők][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Jelenleg nem módosítható az aks-ben már a betegfelvétel tartományvezérlők listáját.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Az aks-sel integrált Azure Key Vault?

Az AKS jelenleg a natív módon az Azure Key Vault nem integrált. Azonban a [Azure Key Vault FlexVolume Kubernetes projekt] [ keyvault-flexvolume] lehetővé teszi, hogy közvetlen integráció a Kubernetes-podok Key Vault titkos kódokhoz való.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Futtatható Windows Server-tárolók az aks-en?

Igen, a Windows Server-tárolók előzetes verziójában érhető el. Az aks-ben a Windows Server-tárolók futtatásához, a vendég operációs rendszer Windows Server rendszert futtató csomópontot készletet hoz létre. A Windows Server-tárolók segítségével csak Windows Server 2019. Első lépésként lásd [AKS-fürt létrehozása a Windows Server csomópontkészletek][aks-windows-cli].

Csomópontkészlet ablak kiszolgáló támogatás magában foglalja a Kubernetes-projektben a felsőbb rétegbeli Windows Server részét képező bizonyos korlátozások. Ezek a korlátozások további információkért lásd: [AKS korlátozásai a Windows Server-tárolók][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Az AKS kínál egy szolgáltatásiszint-szerződést?

A szolgáltatásiszint-szerződés (SLA) a szolgáltató vállalja, hogy megtéríti a szolgáltatás díja a vásárlónak, ha a közzétett szolgáltatási szint nem teljesül. AKS díjmentes, mert nem érhető el úgy, hogy így AKS nem tartozik formális SLA. Azonban az AKS célja, hogy a Kubernetes API-kiszolgálóhoz legalább 99,5 %-os rendelkezésre állását.

## <a name="why-cant-i-set-maxpods-below-30"></a>Miért nem állítható be alább 30 maxPods?

Az aks-ben, beállíthatja a `maxPods` értéke, ha a fürt létrehozása az Azure CLI-vel és az Azure Resource Manager-sablonok használatával. Azonban Kubenet és Azure CNI is szükség van egy *minimális érték* (érvényesíteni a Létrehozás időpontja):

| Hálózat | Minimális | Maximum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Az AKS egy olyan felügyelt szolgáltatás, hogy üzembe helyezheti és felügyelheti a bővítmények és a podok a fürt részeként. A múltban felhasználók megadására a `maxPods` kisebb az érték, amely a felügyelt podok (például: 30) futtatásához szükséges. AKS most kiszámítja a podok minimális számát a képlettel: ((maxPods vagy (maxPods * vm_count)) > felügyelt bővítmény podok minimális.

Felhasználók nem bírálhatják felül a minimális `maxPods` érvényesítése.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Használhatom-e a foglalást az Azure-kedvezmények az AKS-ügyfélcsomóponttal rendelkezik?

AKS ügynökcsomópontok számlázzuk, a standard szintű Azure-beli virtuális gépek, így ha korábban már vásárolt [Azure foglalások] [ reservation-discounts] Virtuálisgép-méret az aks-ben használt, a rendszer automatikusan alkalmazza ezeket a kedvezményeket jelentenek.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /rest/api/aks/managedclusters/createorupdate#managedcluster
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
