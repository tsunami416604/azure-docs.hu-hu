---
title: Gyakori kérdések az Azure Kubernetes Service (AKS)
description: Az Azure Kubernetes Service (AKS) kapcsolatos gyakori kérdésekre adott válaszokat biztosít.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: f365fcd61944fbae131ab79a1c3660aaf02fa8d7
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073931"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>– Azure Kubernetes Service (AKS) kapcsolatos gyakori kérdések

Ez a cikk címek gyakori kérdések az Azure Kubernetes Service (AKS) kapcsolatos.

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Mely Azure-régiók az Azure Kubernetes Service (AKS) jelenleg elérhető?

Az elérhető régiók teljes listáját lásd: [AKS régiók és rendelkezésre állás][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Az AKS támogatja az automatikus skálázás csomópont?

Igen, az automatikus skálázás keresztül érhető el a [Kubernetes méretező] [ auto-scaler] Kubernetes 1.10 kezdődően. A manuális konfigurálása és a fürt automatikus méretező használatához további információkért lásd: [fürt az automatikus skálázás AKS][aks-cluster-autoscale].

Csomópontok szerepkörtípusok (jelenleg előzetes verzióban érhető el az aks-ben) is használhatja a beépített fürt méretező. További információkért lásd: [automatikus méretezése egy fürtöt az aks-ben alkalmazás figyelembevételével][aks-cluster-autoscaler].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Támogatja az AKS Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC)?

Igen, Kubernetes RBAC is alapértelmezés szerint a engedélyezve van, amikor az Azure CLI-vel való létrehozása. Az RBAC az Azure portal vagy a sablonok használatával létrehozott fürtök esetén engedélyezhető.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Üzembe helyezhetem az AKS a meglévő virtuális hálózatban?

Igen, telepíthet egy AKS-fürtöt egy meglévő virtuális hálózat használatával, a [speciális hálózatkezelési funkció][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Korlátozhatja a csak a saját virtuális hálózaton belül elérhetők lesznek a Kubernetes API-t kiszolgáló?

Jelenleg nem. A Kubernetes API-kiszolgálóhoz van közzétéve, mert egy nyilvános teljesen minősített tartományneve (FQDN). A fürt használatával szabályozhatja a hozzáférést [Kubernetes szerepköralapú hozzáférés-vezérlési (RBAC) és az Azure Active Directory (AAD)][aks-rbac-aad]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>AKS-ügyfélcsomóponttal rendelkezik érvényes biztonsági frissítések?

Az Azure automatikusan alkalmazza a Linux-csomópontok a fürtben éjszakai ütemezett biztonsági javítások. Azonban Ön felelős azért, hogy szükséges-e ezen csomópontok újraindulásakor, Linux. A csomópont újraindul végrehajtása több lehetősége van:

- Manuálisan keresztül az Azure portal vagy az Azure parancssori felület.
- Az AKS-fürt frissítésével. Fürt frissítése automatikusan [Ez a három csomópontunk, és a csomópont kiürítési][cordon-drain], majd hálózatra a legújabb Ubuntu-rendszerképet és a egy új biztonsági javítást vagy egy Kubernetes-alverzió készítsen biztonsági másolatot minden csomóponton. További információkért lásd: [AKS-fürt frissítése][aks-upgrade].
- Használatával [Kured](https://github.com/weaveworks/kured), egy nyílt forráskódú újraindítás démon a Kubernetes esetében. Kured fut, mint egy [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) , és figyeli a-fájljának jelenlétét, arról, hogy a számítógép újraindítása nem szükséges minden egyes csomópont. Az operációs rendszer-újraindítások felügyelt ugyanazzal a fürtön [Ez a három csomópontunk, és a kiürítési folyamat] [ cordon-drain] mint a fürtfrissítések.

Kured használatával kapcsolatos további információkért lásd: [biztonsági és a kernel-frissítések alkalmazása a csomópontok az aks-ben][node-updates-kured].

### <a name="windows-server-nodes"></a>A Windows Server-csomópontok

A Windows Server-csomópontok (jelenleg előzetes verzióban érhető el az aks-ben) a Windows Update futtatása és automatikusan alkalmazza a legújabb frissítéseket. A Windows Update kiadási ciklus, és a saját érvényesítési folyamat rendszeres időközönként meg kell hajtsa végre a Windows Server-csomóponton vagy tárolókészleteit a frissítés az AKS-fürt. A frissítés során hoz létre a csomópontokat, amelyeken a legújabb Windows Server-rendszerképet és azok javításait, majd eltávolítja a régebbi csomópontok. Ezen folyamatról további információkért lásd: [frissítése az aks-ben csomópontkészletek][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Két erőforráscsoport miért jönnek létre az aks-sel?

Minden egyes az AKS üzembe helyezési kiterjedő két erőforráscsoport:

- Az első erőforráscsoport jön létre, és csak a Kubernetes szolgáltatás-erőforrást tartalmaz. Az AKS erőforrás-szolgáltató automatikusan létrehozza a központi telepítése során a másodikat például *MC_myResourceGroup_myAKSCluster_eastus*. Az erőforráscsoport nevét, a második adhatja meg a további információkért lásd: a következő szakaszban.
- A második erőforráscsoportja, mint például *MC_myResourceGroup_myAKSCluster_eastus*, a fürthöz társított infrastruktúrát erőforrásokat tartalmaz. Ilyen erőforrások többek között a Kubernetes csomópont virtuális gépek, virtuális hálózati és storage. Erőforrás-karbantartása leegyszerűsítése érdekében külön ebben az erőforráscsoportban jön létre.

Az AKS-fürt használatra erőforrásokat hoz létre, ha például a storage-fiókok vagy fenntartott nyilvános IP-címeket elhelyezése egy az automatikusan létrehozott erőforráscsoportot.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>Biztosíthat a saját nevét az AKS-infrastruktúra erőforráscsoport?

Igen. Alapértelmezés szerint az AKS erőforrás-szolgáltató automatikusan létrehoz egy másodlagos erőforráscsoport üzembe helyezése során például *MC_myResourceGroup_myAKSCluster_eastus*. Ahhoz, hogy megfeleljenek a vállalati házirenddel, adja meg a saját felügyelt fürt nevét (*MC_*) erőforráscsoportot.

Adja meg a saját erőforráscsoport neve, telepítse a [aks előzetes] [ aks-preview-cli] Azure CLI-bővítmény verziója *0.3.2-es verzióra* vagy újabb. Létrehozásakor egy AKS fürt használatával a [az aks létrehozása] [ az-aks-create] parancsot, használja a *– csomópont-resource-group* paraméter, és adja meg az erőforráscsoport nevét. Ha Ön [egy Azure Resource Manager-sablonnal] [ aks-rm-template] AKS-fürt üzembe helyezéséhez határozhatja meg az erőforrás csoport nevének használatával a *nodeResourceGroup* tulajdonság.

* Ez az erőforráscsoport automatikusan létrejön a Azure-erőforrás-szolgáltató saját előfizetésében.
* Csak adhat egy egyéni erőforráscsoport-nevet, amikor létrehozza a fürtöt.

A következő helyzetek nem támogatottak:

* Nem adhat meg egy meglévő erőforráscsoportot, a *MC_* csoport.
* Nem adhat meg egy másik előfizetéshez tartozó a *MC_* erőforráscsoportot.
* Nem módosítható a *MC_* erőforráscsoport-nevet a fürt létrehozása után.
* Nem adható meg belül a felügyelt erőforrások nevét a *MC_* erőforráscsoportot.
* Nem lehet módosítani vagy törölni a felügyelt erőforrások címkék a *MC_* resource-group (lásd a további információkat a következő szakaszban).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Címkék és egyéb tulajdonságait az AKS a MC_ * erőforráscsoportban lévő erőforrásokat is módosíthatja?

Módosítása és törlése az Azure által létrehozott címkék és egyéb erőforrások tulajdonságait a *MC_** erőforráscsoportban például a méretezés és a hibák frissítése váratlan eredményekhez vezethet. Létrehozásához és módosításához a további egyéni címkéket, mint például egy üzleti egységet vagy költség center hozzárendelése támogatott. Az erőforrások alapján módosítja a *MC_** az aks-ben a fürt működésképtelenné válik a szolgáltatási szint célkitűzés (SLO). További információkért lásd: [Does AKS kínál egy szolgáltatói szerződést?](#does-aks-offer-a-service-level-agreement)

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

Nincs jelenleg lehetséges módosítani az aks-ben már a betegfelvétel tartományvezérlők listáját.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Az aks-sel integrált Azure Key Vault?

Az AKS nem natív módon jelenleg az Azure Key Vault integrált. Azonban a [Azure Key Vault FlexVolume Kubernetes projekt] [ keyvault-flexvolume] lehetővé teszi, hogy közvetlen integráció a Kubernetes-podok KeyVault titkos kódokhoz való.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Futtatható Windows Server-tárolók az aks-en?

Igen, a Windows Server-tárolók előzetes verziójában érhető el. Az aks-ben a Windows Server-tárolók futtatásához, a vendég operációs rendszer Windows Server rendszert futtató csomópontot készletet hoz létre. A Windows Server-tárolókat használja a Windows Server 2019. Első lépésként [AKS-fürt létrehozása a Windows Server csomópontkészletek][aks-windows-cli].

Ablak kiszolgáló csomópont készlet támogatás magában foglalja a Kubernetes-projektben a felsőbb rétegbeli Windows Server részét képező bizonyos korlátozások. Ezek a korlátozások további információkért lásd: [AKS korlátozásai a Windows Server-tárolók][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Az AKS kínál egy szolgáltatói szerződést?

A egy szolgáltatásiszint-szerződés (SLA) a szolgáltató vállalja, hogy megtéríti a szolgáltatás díja a vásárlónak, ha a közzétett szolgáltatási szint nem teljesül. Mivel az AKS maga ingyenes, nincs költség nélkül érhető el, hogy megtéríti, és így nem hivatalos tartozik vállalt SZOLGÁLTATÁSSZINT. Azonban az AKS célja, hogy a Kubernetes API-kiszolgálóhoz legalább 99,5 %-os rendelkezésre állását.

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

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
