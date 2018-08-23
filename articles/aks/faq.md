---
title: Gyakori kérdések az Azure Kubernetes Service (AKS)
description: Az Azure Kubernetes Service (AKS) kapcsolatos gyakori kérdésekre adott válaszokat biztosít.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.openlocfilehash: b910b6cdf55ae7c2a220543bdb555d8e9bff59a0
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42055340"
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

## <a name="why-are-two-resource-groups-created-with-aks"></a>Két erőforráscsoport miért jönnek létre az aks-sel?

Minden egyes az AKS üzembe helyezési kiterjedő két erőforráscsoport:

- Az első erőforráscsoport jön létre, és csak a Kubernetes szolgáltatás-erőforrást tartalmaz. Az AKS erőforrás-szolgáltató automatikusan létrehozza a központi telepítése során a másodikat például *MC_myResourceGroup_myAKSCluster_eastus*.
- A második erőforráscsoportja, mint például *MC_myResourceGroup_myAKSCluster_eastus*, a fürthöz társított infrastruktúrát erőforrásokat tartalmaz. Ilyen erőforrások többek között a Kubernetes csomópont virtuális gépek, virtuális hálózati és storage. Erőforrás-karbantartása leegyszerűsítése érdekében külön ebben az erőforráscsoportban jön létre.

Az AKS-fürt használatra erőforrásokat hoz létre, ha például a storage-fiókok vagy fenntartott nyilvános IP-címeket elhelyezése egy az automatikusan létrehozott erőforráscsoportot.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Címkék és egyéb tulajdonságait az AKS a MC_ * erőforráscsoportban lévő erőforrásokat is módosíthatja?

Módosítása és törlése a címkék és egyéb erőforrások tulajdonságait a *MC_** erőforráscsoportban például a méretezés és a hibák frissítése váratlan eredményekhez vezethet. Az erőforrások alapján módosítja a *MC_** az aks-ben a fürt a szolgáltatási szint Célkitűzésének működésképtelenné válik.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Milyen Kubernetes már a betegfelvétel tartományvezérlők támogatja az AKS? Is már a betegfelvétel vezérlők hozzáadásának vagy eltávolításának?

Az AKS támogatja a következő [már a betegfelvétel tartományvezérlők][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *Szolgáltatásfiók*
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

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./networking-overview.md#advanced-networking
[aks-rbac-aad]: ./aad-integration.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
