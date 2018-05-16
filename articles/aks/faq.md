---
title: Gyakori kérdések az Azure Kubernetes szolgáltatás
description: A témakör válaszokat ad Azure Kubernetes szolgáltatással kapcsolatos gyakori kérdésekre.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/09/2018
ms.author: nepeters
ms.openlocfilehash: 3152dc69bc8fb9a94111f85976e5d999c4b18261
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Gyakori kérdések kapcsolatos Azure Kubernetes szolgáltatás (AKS)

Ez a cikk címek gyakran használják az Azure Kubernetes szolgáltatás (AKS) kapcsolatos kérdésekre.

> [!IMPORTANT]
> Az Azure Kubernetes Service (AKS) jelenleg **előzetes verzióban** érhető el. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Mely Azure-régiók ma biztosít az Azure Kubernetes szolgáltatás (AKS)?

- Közép-Kanada
- Kelet-Kanada
- Egyesült Államok, középső régió
- Egyesült Államok, keleti régió
- Nyugat-Európa

## <a name="when-will-additional-regions-be-added"></a>Ha további régiókban megkapja?

További régiókban támasztott igény növekedésével hozzá szeretné adni.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Biztonsági frissítések AKS ügynök csomópontok vonatkoznak?

Azure automatikusan alkalmazza a biztonsági javítások egy éjszakánként ütemezés szerint a fürt csomópontjaihoz. Azonban az Ön felelőssége annak biztosítására, hogy a csomópont újraindítása van szükség szerint. A csomópont újraindul végrehajtásához több lehetőség közül választhat:

- Manuálisan keresztül az Azure-portálon vagy az Azure parancssori felület.
- A AKS fürt frissítésével. A fürt frissítések automatikusan [fekvő terület és üríti ki a csomópontok](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), majd hálózatra kell kapcsolni azokat készítsen biztonsági másolatot a legújabb Ubuntu lemezképpel. Operációsrendszer-lemezképet a csomóponton frissítse a fürt verziószámának megadásával Kubernetes verziók módosítása nélkül `az aks upgrade`.
- Használatával [Kured](https://github.com/weaveworks/kured), egy nyílt forráskódú újraindítás démont Kubernetes. Kured fut, mint egy [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) és a fájl, amely jelzi, hogy újraindításra szükség minden egyes csomópont figyeli. A fürtön, az azonos fekvő terület és a korábban leírt kiürítési folyamat ezen vesznek majd koordinálja.

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>Tegye javasoljuk az ügyfelek használata ACS vagy AKS?

AKS preview marad, de javasolt az ACS-Kubernetes üzemi-fürtök létrehozása vagy [acs-motor](https://github.com/azure/acs-engine). A koncepció igazolása központi telepítések és a fejlesztési és tesztelési célú környezetek AKS használhat.

## <a name="when-will-acs-be-deprecated"></a>Ha ACS elavulttá válik?

ACS AKS válik GA környékén elavulttá válik Fürtök áttelepítéséhez AKS 12 hónapon lesz. 12 hónap során az összes ACS-műveleteket is futtathatja.

## <a name="does-aks-support-node-autoscaling"></a>Támogatja a AKS csomópont automatikus skálázás?

Csomópont automatikus skálázás nem támogatott, de a programba. Vessen egy pillantást a nyitott forrása kívánt [automatikus skálázás megvalósítási][auto-scaler].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Támogatja a AKS Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC)?

Nem, RBAC AKS jelenleg nem támogatott, de hamarosan elérhető lesz.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Is AKS is üzembe helyezés a már meglévő virtuális hálózatot?

Igen, ez támogatott keresztül a [speciális hálózati szolgáltatás](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS szolgáltatással integrált Azure Key Vault?

Nem, nem, de ez az integráció tervezett. Addig is, próbálja ki a következő megoldást [Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Futtatható Windows Server-tárolók AKS?

Futtassa a Windows Server-tárolók, Windows Server-alapú csomópontok futtatnia kell. A jelenleg a rendszer Windows Server-alapú csomópontok [private Preview verziójára](https://azure.microsoft.com/en-us/blog/kubernetes-on-azure/). Ha a Windows Server-tárolók futtatnak az Azure-ban Kubernetes kívül az előzetes van szüksége, tekintse át a [acs-motor dokumentációja](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Két erőforráscsoport miért jönnek létre az AKS?

Minden egyes AKS központi telepítés két erőforráscsoport is. Az első hozta létre, és csak a AKS erőforrást tartalmaz. A AKS erőforrás-szolgáltató automatikusan hoz létre központi telepítése során a második érték egy nevet, például a *MC_myResourceGRoup_myAKSCluster_eastus*. A második erőforráscsoport az összes az infrastruktúrához kapcsolódó erőforrások a fürt, például a virtuális gépekhez társított tartalmazza a hálózati és adattárolási. Erőforrás-karbantartása leegyszerűsítése érdekében a rendszer létrehozza.

A AKS-fürthöz, például a storage-fiókok vagy fenntartott nyilvános IP-cím használható erőforrások létrehozásakor meg kell helyezze el őket az automatikusan létrehozott erőforráscsoportot.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
