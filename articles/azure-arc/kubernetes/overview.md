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
ms.openlocfilehash: 61317f7f5f2bf17c88fc019294574993c1854e59
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540642"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Mi az az Azure arc-kompatibilis Kubernetes előzetes verziója?

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

    Nem. Az Azure arc-kompatibilis Kubernetes (például Azure Monitor, Azure Policy (forgalomirányító) összes funkciója natív módon elérhető az AK-val, amely már rendelkezik erőforrás-ábrázolással az Azure-ban. A fürtkonfiguráció (GitOps) natív módon is elérhető az AK-on, és jelenleg privát előzetes verzióban érhető el. Ezt a [regisztrációs űrlapot](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5acO18Lmx5Bk_qao2CrOcFUQ0UyRllDR1BEV1BPNENYRERYN1pFWTQ4WC4u) használhatja a funkció eléréséhez.
    
* Össze kell kapcsolni az AK-fürtöt Azure Stack HCI-re az Azure arc-ra? Mi a helyzet a Azure Stack hub vagy Azure Stack motoron futó Kubernetes-fürtökkel?

    Igen, a fürtök az Azure arc-hoz való csatlakoztatása előnyökkel jár. Erőforrás-ábrázolást biztosít ezen Kubernetes-fürtökhöz Azure Resource Managerban. Ezen erőforrás-ábrázolások, például a fürtkonfiguráció, Azure Monitor, Azure Policy (forgalomirányító) használatával bővíthetők ezekhez a Kubernetes-fürtökhöz

## <a name="next-steps"></a>További lépések

* [Fürt összekötése](./connect-cluster.md)
