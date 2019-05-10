---
title: Gyakori kérdések az Azure Kubernetes Service (AKS)
description: Az Azure Kubernetes Service (AKS) kapcsolatos gyakori kérdésekre adott válaszokat biztosít.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/25/2019
ms.author: iainfou
ms.openlocfilehash: 17bc1d2b7a08314f19f1bf8f87d0c774afc37500
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508181"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>– Azure Kubernetes Service (AKS) kapcsolatos gyakori kérdések

Ez a cikk címek gyakori kérdések az Azure Kubernetes Service (AKS) kapcsolatos.

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Mely Azure-régiók az Azure Kubernetes Service (AKS) jelenleg elérhető?

Az elérhető régiók teljes listáját lásd: [AKS régiók és rendelkezésre állás][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Az AKS támogatja az automatikus skálázás csomópont?

Igen, az automatikus skálázás keresztül érhető el a [Kubernetes méretező] [ auto-scaler] Kubernetes 1.10 kezdődően. Konfigurálása és a fürt automatikus méretező használatához további információkért lásd: [fürt az automatikus skálázás AKS][aks-cluster-autoscale].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Támogatja az AKS Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC)?

Igen, Kubernetes RBAC is alapértelmezés szerint a engedélyezve van, amikor az Azure CLI-vel való létrehozása. Az RBAC az Azure portal vagy a sablonok használatával létrehozott fürtök esetén engedélyezhető.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Üzembe helyezhetem az AKS a meglévő virtuális hálózatban?

Igen, telepíthet egy AKS-fürtöt egy meglévő virtuális hálózat használatával, a [speciális hálózatkezelési funkció][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Korlátozhatja a csak a saját virtuális hálózaton belül elérhetők lesznek a Kubernetes API-t kiszolgáló?

Jelenleg nem. A Kubernetes API-kiszolgálóhoz van közzétéve, mert egy nyilvános teljesen minősített tartományneve (FQDN). A fürt használatával szabályozhatja a hozzáférést [Kubernetes szerepköralapú hozzáférés-vezérlési (RBAC) és az Azure Active Directory (AAD)][aks-rbac-aad]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>AKS-ügyfélcsomóponttal rendelkezik érvényes biztonsági frissítések?

Igen, az Azure automatikusan alkalmazza a biztonsági javítások éjszakai ütemezés szerint a fürt csomópontjaihoz. Azonban Ön felelős azért, hogy csomópontok indulnak újra lesz szükség szerint. A csomópont újraindul végrehajtása több lehetősége van:

- Manuálisan keresztül az Azure portal vagy az Azure parancssori felület.
- Az AKS-fürt frissítésével. Fürt frissítése automatikusan [Ez a három csomópontunk, és a csomópont kiürítési][cordon-drain], majd hálózatra a legújabb Ubuntu-rendszerképet és a egy új biztonsági javítást vagy egy Kubernetes-alverzió készítsen biztonsági másolatot minden csomóponton. További információkért lásd: [AKS-fürt frissítése][aks-upgrade].
- Használatával [Kured](https://github.com/weaveworks/kured), egy nyílt forráskódú újraindítás démon a Kubernetes esetében. Kured fut, mint egy [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) , és figyeli a-fájljának jelenlétét, arról, hogy a számítógép újraindítása nem szükséges minden egyes csomópont. Az operációs rendszer-újraindítások felügyelt ugyanazzal a fürtön [Ez a három csomópontunk, és a kiürítési folyamat] [ cordon-drain] mint a fürtfrissítések.

Kured használatával kapcsolatos további információkért lásd: [biztonsági és a kernel-frissítések alkalmazása a csomópontok az aks-ben][node-updates-kured].

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

A Windows Server-tárolókat futtathat, Windows Server-alapú csomópontokon futtatni kell. A Windows Server-alapú csomópontokat jelenleg nem állnak rendelkezésre az aks-ben. Azonban segítségével Virtual Kubelet Windows-tárolókhoz az Azure Container Instances ütemezheti és kezelheti azokat az AKS-fürt részeként. További információkért lásd: [Virtual Kubelet használata az aks-sel][virtual-kubelet].

## <a name="does-aks-offer-a-service-level-agreement"></a>Az AKS kínál egy szolgáltatói szerződést?

A egy szolgáltatásiszint-szerződés (SLA) a szolgáltató vállalja, hogy megtéríti a szolgáltatás díja a vásárlónak, ha a közzétett szolgáltatási szint nem teljesül. Mivel az AKS maga ingyenes, nincs költség nélkül érhető el, hogy megtéríti, és így nem hivatalos tartozik vállalt SZOLGÁLTATÁSSZINT. Azonban az AKS célja, hogy a Kubernetes API-kiszolgálóhoz legalább 99,5 %-os rendelkezésre állását.

## <a name="why-can-i-not-set-maxpods-below-30"></a>Miért nem állítható `maxPods` 30 alatt?

Az AKS beállítást támogatja a `maxPods` értéke a fürt létrehozáskor az Azure parancssori felület és Azure Resource Manager-sablonok használatával. Van azonban egy *minimális érték* (Létrehozás ideje ellenőrizni) Kubenet és Azure CNI, az alább látható:

| Hálózat | Minimum | Maximum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Mivel az AKS egy felügyelt szolgáltatás, bővítmények és a podok, hogy üzembe helyezése és kezelése a fürt részeként biztosítunk. A múltban felhasználók megadására a `maxPods` érték kisebb, mint a felügyelt podok futtatásához szükséges értéket (Példa: 30), az AKS most számítja ki a podok keresztül minimális száma: ((maxPods vagy (maxPods * vm_count)) > felügyelt bővítmény podok minimális.

Felhasználók nem írhatja felül a minimális `maxPods` érvényesítése.

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

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
