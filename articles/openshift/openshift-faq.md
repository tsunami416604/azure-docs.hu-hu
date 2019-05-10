---
title: Gyakori kérdések az Azure Red Hat OpenShift |} A Microsoft Docs
description: Válaszok a Microsoft Azure Red Hat OpenShift kapcsolatos gyakori kérdésekre
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 881734caf855ccfc4f001693fe261b8448b49bc4
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466200"
---
# <a name="azure-red-hat-openshift-faq"></a>Az Azure Red Hat OpenShift – gyakori kérdések

Ez a cikk foglalkozik – gyakori kérdések (GYIK) a Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Mely Azure-régiók támogatottak?

Lásd: [támogatott erőforrások](supported-resources.md#azure-regions) globális régióban, ahol támogatott az Azure Red Hat OpenShift listáját.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Meglévő virtuális hálózatban fürtöt is telepíthet?

Nem. De az Azure Red Hat OpenShift fürt kapcsolódhat egy meglévő virtuális hálózatok közötti társviszony-létesítés keresztül. Lásd: [egy fürt virtuális hálózat csatlakoztatása egy meglévő virtuális hálózatot ](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) részleteiről.

## <a name="what-cluster-operations-are-available"></a>Milyen fürt műveletek érhetők el?

Csak méretezhetők felfelé és lefelé a számítási csomópontok számát. Egyéb módosítása nem engedélyezett a `Microsoft.ContainerService/openShiftManagedClusters` erőforrás létrehozása után. A számítási csomópontok maximális száma 20-ra korlátozódik.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Milyen virtuálisgép-méretek használhatok?

Lásd: [Azure Red Hat OpenShift virtuálisgép-méretek](supported-resources.md#virtual-machine-sizes) Azure Red Hat OpenShift-fürttel használható virtuálisgép-méretek listáját.

## <a name="is-data-on-my-cluster-encrypted"></a>Az adatok titkosítása a fürtön?

Alapértelmezés szerint titkosítás inaktív állapotban van. Az Azure Storage-platform automatikusan titkosítja az adatokat, mielőtt megőrzése és dekódolja az adatokat lekérés előtt. Lásd: [Azure Storage Service Encryption az inaktív adatok](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) részleteiről.

## <a name="can-i-use-prometheusgrafana-to-monitor-containers-and-manage-capacity"></a>Használhatok Prometheus/Grafana tárolók monitorozását és kezelését a kapacitást?

Nem, nem az aktuális időpontban.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>A Docker-beállításjegyzék kívülről, így az eszközök, például a Jenkins használható érhetők el?

A Docker-beállításjegyzék érhető el `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` azonban egy erős storage tartóssági garancia nem áll rendelkezésre. Is [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Kereszt-névtér hálózatkezelés támogatott?

Ügyfél és a projektben egyes rendszergazdák cross-névtér hálózati (beleértve a megtagadása,) szabhatja egy projekt száma alapján használatával `NetworkPolicy` objektumokat.

## <a name="can-an-admin-manage-users-and-quotas"></a>Kezelheti egy rendszergazda felhasználók és a kvóták?

Igen. Azure Red Hat OpenShift rendszergazda felhasználók és a kvóták minden felhasználó által létrehozott a projektek kívül is kezelheti.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Korlátozhatom a fürt egyes Azure AD-felhasználók?

Igen. Korlátozhatja, hogy melyik Azure AD-felhasználók bejelentkezhet egy fürtöt az Azure AD-alkalmazás konfigurálásával. További információkért lásd: [hogyan: Az alkalmazás felhasználók korlátozása](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>A fürt is rendelkezik a számítási csomópontok több Azure-régióban?

Nem. Az Azure Red Hat OpenShift fürt összes csomópontja az Azure-régióban kell származniuk.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Fő-és infrastruktúra emeli ki nyelvet a lenti listában azok az Azure Kubernetes Service (AKS)?

Nem. Minden erőforrás, beleértve a fürt fő futtassa az ügyfél-előfizetés. Az ilyen típusú erőforrások kerüljenek a csak olvasható erőforráscsoportban.
