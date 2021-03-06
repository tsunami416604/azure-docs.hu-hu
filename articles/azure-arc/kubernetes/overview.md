---
title: Az Azure arc-kompatibilis Kubernetes áttekintése
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Ez a cikk az Azure arc-kompatibilis Kubernetes áttekintését tartalmazza.
keywords: Kubernetes, arc, Azure, tárolók
ms.custom: references_regions
ms.openlocfilehash: 7e48ebf98f12e79cb154fb50d8e6dbdfaea1cd95
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371307"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Mi az Azure Arc-kompatibilis Kubernetes előzetes verziója?

Az Azure arc-kompatibilis Kubernetes előzetes verziójának használatával csatlakoztathat és konfigurálhat Kubernetes-fürtöket az Azure-on belül vagy kívül is. Ha egy Kubernetes-fürt csatlakozik az Azure arc-hoz, akkor az megjelenik a Azure Portalban. Azure Resource Manager azonosítója és felügyelt identitása lesz. A fürtök szabványos Azure-előfizetésekhez vannak csatolva, és egy erőforráscsoport alatt találhatók, és ugyanúgy fogadhatnak címkéket, mint bármely más Azure-erőforrást. 

Kubernetes-fürt Azure-hoz való összekapcsolásához a fürt rendszergazdájának ügynököket kell telepítenie. Ezek az ügynökök egy nevű Kubernetes-névtérben futnak `azure-arc` , és szabványos Kubernetes üzemelő példányok. Az ügynökök felelősek az Azure-hoz való csatlakozásért, az Azure arc-naplók és-metrikák összegyűjtéséhez, valamint a konfigurációs kérések figyeléséhez. 

Az Azure arc-kompatibilis Kubernetes támogatja az iparági szabványnak megfelelő SSL-t az adatátvitel biztonságossá tételéhez. Az adatok titkosságának biztosítása érdekében a rendszer a Azure Cosmos DB adatbázisában tárolja az adatok titkosítását is.
 
> [!NOTE]
> Az Azure arc-kompatibilis Kubernetes előzetes verzióban érhető el. Nem ajánlott éles környezetben üzemelő számítási feladatokhoz.

## <a name="supported-kubernetes-distributions"></a>Támogatott Kubernetes-disztribúciók

Az Azure arc-kompatibilis Kubernetes bármilyen Felhőbeli natív számítástechnikai alaprendszer (CNCF) tanúsítvánnyal rendelkező Kubernetes-fürttel működik, mint például az AK-motor az Azure-ban, az AK-motor Azure Stack hub, a GKE, a EKS és a VMware vSphere-fürthöz.

Az Azure arc-kompatibilis Kubernetes funkcióit az arc csapata tesztelte a következő disztribúciókban:
* RedHat OpenShift 4,3
* Farmer RKE 1.0.8
* Canonical Charmed Kubernetes 1,18
* AK-motor
* AK-motor Azure Stack hub-on
* AK Azure Stack HCI-ben
* Azure-beli fürt API-szolgáltatója

## <a name="supported-scenarios"></a>Támogatott esetek 

Az Azure arc-kompatibilis Kubernetes az alábbi forgatókönyveket támogatja: 

* Az Azure-on kívül futó Kubernetes-ket a leltár, csoportosítás és címkézés céljából is összekapcsolhatjuk.

* Alkalmazások telepítése és konfiguráció alkalmazása a GitOps-alapú konfiguráció-kezelés használatával. 

* A fürtök megtekintéséhez és figyeléséhez használjon Azure Monitor tárolókat. 

* Házirendek alkalmazása a Kubernetes Azure Policy használatával. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Támogatott régiók 

Az Azure arc-kompatibilis Kubernetes jelenleg a következő régiókban támogatott: 

* USA keleti régiója 
* Nyugat-Európa

## <a name="frequently-asked-questions"></a>Gyakori kérdések

* Mi a különbség az Azure arc-kompatibilis Kubernetes és az Azure Kubernetes Service (ak) között?

    Az Azure Kubernetes szolgáltatás (ak) az Azure által felügyelt Kubernetes-ajánlat. Az AK segítségével egyszerűen üzembe helyezhet egy felügyelt Kubernetes-fürtöt az Azure-ban. Az AKS egyszerűsíti a Kubernetes kezelését és csökkenti annak működési munkaterhelését azáltal, hogy a felelősség nagy részét az Azure-ra helyezi át. A Kubernetes fő csomópontokat az Azure felügyeli. Ön csak az ügynökcsomópontokat felügyeli és tartja karban.

    Az Azure arc-kompatibilis Kubernetes lehetővé teszi a Kubernetes-fürtök összekapcsolását az Azure-ban az Azure felügyeleti képességeinek, például a Azure Monitor és Azure Policy kibővítéséhez. Maga a mögöttes Kubernetes-fürt karbantartását végzi.

* Kell-e csatlakozni az Azure-ban futó Azure Kubernetes Service-fürtöket az Azure arc szolgáltatáshoz?

    Nem. Az Azure arc-kompatibilis Kubernetes (például Azure Monitor, Azure Policy (forgalomirányító) összes funkciója natív módon elérhető az AK-val, amely már rendelkezik erőforrás-ábrázolással az Azure-ban.
    
* Össze kell kapcsolni az AK-fürtöt Azure Stack HCI-re az Azure arc-ra? Mi a helyzet a Azure Stack hub vagy a Azure Stack Edge rendszerű Kubernetes-fürtökkel?

    Igen, a fürtök az Azure arc-hoz való csatlakoztatása előnyökkel jár. Erőforrás-ábrázolást biztosít ezen Kubernetes-fürtökhöz Azure Resource Managerban. Ezen erőforrás-ábrázolások, például a fürtkonfiguráció, Azure Monitor, Azure Policy (forgalomirányító) használatával bővíthetők ezekhez a Kubernetes-fürtökhöz

## <a name="next-steps"></a>Következő lépések

* [Fürt összekötése](./connect-cluster.md)
