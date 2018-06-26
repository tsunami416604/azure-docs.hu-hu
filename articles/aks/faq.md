---
title: Gyakori kérdések az Azure Kubernetes szolgáltatás
description: A témakör válaszokat ad Azure Kubernetes szolgáltatással kapcsolatos gyakori kérdésekre.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/25/2018
ms.author: nepeters
ms.openlocfilehash: 5155d0c85e5b3698b0a13d2d5256a235858f0e82
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938419"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Gyakori kérdések kapcsolatos Azure Kubernetes szolgáltatás (AKS)

Ez a cikk címek gyakran használják az Azure Kubernetes szolgáltatás (AKS) kapcsolatos kérdésekre.

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Mely Azure-régiók ma biztosít az Azure Kubernetes szolgáltatás (AKS)?

- Kelet-Ausztrália
- Közép-Kanada
- Kelet-Kanada
- USA középső régiója
- USA keleti régiója
- Kelet-US2
- Észak-Európa
- Az Egyesült Királyság déli régiója
- Nyugat-Európa
- USA nyugati régiója
- USA nyugati régiója, 2.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Biztonsági frissítések AKS ügynök csomópontok vonatkoznak?

Azure automatikusan alkalmazza a biztonsági javítások egy éjszakánként ütemezés szerint a fürt csomópontjaihoz. Azonban az Ön felelőssége annak biztosítására, hogy a csomópont újraindítása van szükség szerint. A csomópont újraindul végrehajtásához több lehetőség közül választhat:

- Manuálisan keresztül az Azure-portálon vagy az Azure parancssori felület.
- A AKS fürt frissítésével. A fürt frissítések automatikusan [fekvő terület és üríti ki a csomópontok](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), majd hálózatra kell kapcsolni azokat készítsen biztonsági másolatot a legújabb Ubuntu lemezképpel. Operációsrendszer-lemezképet a csomóponton frissítse a fürt verziószámának megadásával Kubernetes verziók módosítása nélkül `az aks upgrade`.
- Használatával [Kured](https://github.com/weaveworks/kured), egy nyílt forráskódú újraindítás démont Kubernetes. Kured fut, mint egy [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) és a fájl, amely jelzi, hogy újraindításra szükség minden egyes csomópont figyeli. A fürtön, az azonos fekvő terület és a korábban leírt kiürítési folyamat a következő vesznek majd koordinálja.

## <a name="does-aks-support-node-autoscaling"></a>Támogatja a AKS csomópont automatikus skálázás?

Igen, automatikus skálázás megtalálható a [Kubernetes autoscaler] [ auto-scaler] Kubernetes 1.10-től.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Támogatja a AKS Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC)?

Igen, az RBAC is engedélyezhető, ha az Azure CLI vagy Azure Resource Manager sablon AKS fürt telepítése. Ez a funkció hamarosan határozza meg az Azure portálon.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Milyen Kubernetes beléptetésre vezérlők nem támogatják a AKS? Ez konfigurálható?

AKS támogatja a következő [beléptetésre tartományvezérlők][admission-controllers]:

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

Nincs jelenleg lehetőség módosíthatja AKS beléptetésre tartományvezérlőinek listáját.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Is AKS is üzembe helyezés a már meglévő virtuális hálózatot?

Igen, egy AKS fürtök egy meglévő virtuális hálózat használatával történő telepítése a [speciális hálózati szolgáltatás](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS szolgáltatással integrált Azure Key Vault?

AKS nem natív módon integrálva van az Azure Key Vault jelenleg. Van azonban közösségi megoldások, például [az acs-keyvault-ügynököt a Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Futtatható Windows Server-tárolók AKS?

Futtassa a Windows Server-tárolók, Windows Server-alapú csomópontok futtatnia kell. Windows Server-alapú csomópontok nem érhetők el a AKS most. Ha a Windows Server-tárolók futtatnak az Azure-ban Kubernetes van szüksége, tekintse át a [acs-motor dokumentációja](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Két erőforráscsoport miért jönnek létre az AKS?

Minden egyes AKS központi telepítés két erőforráscsoport is. Az első hozta létre, és csak a Kubernetes szolgáltatás-erőforrást tartalmaz. A AKS erőforrás-szolgáltató automatikusan hoz létre központi telepítése során a második érték egy nevet, például a *MC_myResourceGroup_myAKSCluster_eastus*. A második erőforráscsoport az összes az infrastruktúrához kapcsolódó erőforrások a fürt, például a virtuális gépekhez társított tartalmazza a hálózati és adattárolási. Erőforrás-karbantartása leegyszerűsítése érdekében a rendszer létrehozza.

A AKS-fürthöz, például a storage-fiókok vagy fenntartott nyilvános IP-cím használható erőforrások létrehozásakor meg kell helyezze el őket az automatikusan létrehozott erőforráscsoportot.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS nyújtja a szolgáltatásiszint-szerződések?

A szolgáltatásiszint-szerződések (SLA) a szolgáltató vállalja, hogy úgy az ügyfél a szolgáltatás költségét kell a közzétett szolgáltatási szint nem teljesíthető. Mivel maga AKS szabad, nincs elérhető úgy költség nélkül, és így nem hivatalos SLA-t. Azonban azt pozícionálni legalább 99,5 %-a Kubernetes API-kiszolgáló rendelkezésre állását fenntartásához.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
