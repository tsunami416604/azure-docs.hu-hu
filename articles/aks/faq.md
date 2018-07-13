---
title: Gyakori kérdések az Azure Kubernetes Service-ben
description: Azure Kubernetes Service-ről a gyakori kérdésekre adott válaszokat biztosít.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/11/2018
ms.author: iainfou
ms.openlocfilehash: 915f74df69596b1677a0e03770e076ae50efc609
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001245"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>– Azure Kubernetes Service (AKS) kapcsolatos gyakori kérdések

Ez a cikk címek gyakori kérdések az Azure Kubernetes Service (AKS) kapcsolatos.

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Mely Azure-régiók az Azure Kubernetes Service (AKS) jelenleg elérhető?

Tekintse meg az Azure Kubernetes Service [régiók és rendelkezésre állás] [ aks-regions] dokumentáció teljes listáját.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>AKS-ügyfélcsomóponttal rendelkezik érvényes biztonsági frissítések?

Az Azure automatikusan alkalmazza a biztonsági javítások éjszakai ütemezés szerint a fürt csomópontjaihoz. Azonban Ön felelős azért, hogy csomópontok indulnak újra lesz szükség szerint. A csomópont újraindul végrehajtása több lehetősége van:

- Manuálisan keresztül az Azure portal vagy az Azure parancssori felület.
- Az AKS-fürt frissítésével. Fürt frissítése automatikusan [Ez a három csomópontunk, és a csomópont kiürítési](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), majd vonásához biztonsági mentése a legújabb Ubuntu-lemezképpel. Az operációsrendszer-képet a csomópontokon frissítéséhez adja meg az aktuális fürt verziója a Kubernetes-verzió módosítása nélkül `az aks upgrade`.
- Használatával [Kured](https://github.com/weaveworks/kured), egy nyílt forráskódú újraindítás démon a Kubernetes esetében. Kured fut, mint egy [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) , és figyeli a-fájljának jelenlétét, arról, hogy a számítógép újraindítása nem szükséges minden egyes csomópont. A fürtön, az azonos fekvő terület és a korábban leírt kiürítési folyamat a következő újraindítások majd koordinálja.

## <a name="does-aks-support-node-autoscaling"></a>Az AKS támogatja az automatikus skálázás csomópont?

Igen, az automatikus skálázás keresztül érhető el a [Kubernetes méretező] [ auto-scaler] Kubernetes 1.10 kezdődően.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Támogatja az AKS Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC)?

Igen, engedélyezhető az RBAC az Azure CLI-vel vagy Azure Resource Manager-sablon az AKS-fürt üzembe helyezésekor. Ezt a funkciót hamarosan az Azure Portalra fognak érkezni.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Milyen Kubernetes már a betegfelvétel tartományvezérlők támogatja az AKS? Ez konfigurálható?

Az AKS támogatja a következő [már a betegfelvétel tartományvezérlők][admission-controllers]:

* NamespaceLifecycle
* LimitRanger
* Szolgáltatásfiók
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

Nincs jelenleg lehetséges módosítani az aks-ben már a betegfelvétel tartományvezérlők listáját.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Üzembe helyezhetem az AKS a meglévő virtuális hálózatban?

Igen, telepíthet egy AKS-fürtöt egy meglévő virtuális hálózat használatával, a [speciális hálózatkezelési funkció](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Az aks-sel integrált Azure Key Vault?

Az AKS nem natív módon integrált az Azure Key Vault jelenleg. Előfordulhatnak azonban olyan megoldások, mint a közösségi [az acs-keyvault-ügynököt a Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Futtatható Windows Server-tárolók az aks-en?

A Windows Server-tárolókat futtathat, Windows Server-alapú csomópontokon futtatni kell. A Windows Server-alapú csomópontokat jelenleg nem állnak rendelkezésre az aks-ben. Azonban segítségével Virtual Kubelet Windows-tárolókhoz az Azure Container Instances ütemezheti és kezelheti azokat az AKS-fürt részeként. További információkért lásd: [Virtual Kubelet használata az aks-sel][virtual-kubelet].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Két erőforráscsoport miért jönnek létre az aks-sel?

Minden egyes az AKS üzembe helyezési kiterjedő két erőforráscsoport. Az első Ön által létrehozott, és csak a Kubernetes szolgáltatás-erőforrást tartalmaz. Az AKS erőforrás-szolgáltató automatikusan hoz létre központi telepítése során a másodikat vagy hasonló néven *MC_myResourceGroup_myAKSCluster_eastus*. A második erőforráscsoport tartalmazza az összes, az infrastruktúra-erőforrások, például a virtuális gépek, a fürthöz társított hálózati és tárolási. Erőforrás-karbantartása leegyszerűsítése érdekében a rendszer létrehozza.

Az AKS-fürt, például a storage-fiókok vagy fenntartott nyilvános IP-címet, a használt erőforrások létrehozásakor el az automatikusan létrehozott erőforráscsoportban kell helyezni.

## <a name="does-aks-offer-a-service-level-agreement"></a>Az AKS kínál egy szolgáltatói szerződést?

A egy szolgáltatásiszint-szerződés (SLA) a szolgáltató vállalja, hogy megtéríti a szolgáltatás járó költségeket az ügyfél a közzétett szolgáltatási szint nem kell kielégíteni. Mivel az AKS maga ingyenes, nincs költség nélkül érhető el, hogy megtéríti, és így nem hivatalos tartozik vállalt SZOLGÁLTATÁSSZINT. Azonban azt keresik a Kubernetes API-kiszolgálóhoz legalább 99,5 %-os rendelkezésre állását.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md
[virtual-kubelet]: virtual-kubelet.md

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
