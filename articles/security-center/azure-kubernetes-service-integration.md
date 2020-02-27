---
title: Azure Security Center és az Azure Kubernetes szolgáltatás
description: Ismerkedjen meg Azure Security Center Azure Kubernetes-szolgáltatásokkal való integrálásával
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 0743499b019bd1c7b985636e886eee9352284a55
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616070"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Az Azure Kubernetes Services integrációja Security Center (előzetes verzió)
Az Azure Kubernetes Service (ak) a Microsoft által felügyelt szolgáltatás a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez. 

Az AK-k együttes használata a Azure Security Center Standard szintjével (lásd a [díjszabást](security-center-pricing.md)) az AK-csomópontok, a Felhőbeli forgalom és a biztonsági vezérlők mélyebb megismeréséhez.

A Security Center biztonsági előnyöket biztosít az AK-fürtök számára az AK fő csomópontja által már összegyűjtött adatok használatával. 

![A Azure Security Center és az Azure Kubernetes Service (ak) magas szintű áttekintése](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Ez a két eszköz együttesen a legjobb Felhőbeli natív Kubernetes biztonsági ajánlatot alkotja. 

## <a name="benefits-of-integration"></a>Az integráció előnyei

A két szolgáltatás együttes használata a következőket biztosítja:

* **Biztonsági javaslatok** – Security Center AZONOSÍTJA az AK-erőforrásokat, és kategorizálja őket: a fürtöktől az egyes virtuális gépekig. Ezután megtekintheti az egyes erőforrásokra vonatkozó biztonsági javaslatokat. További információkért tekintse meg a tárolók javaslatait a [javaslatok hivatkozási listájában](recommendations-reference.md#recs-computeapp). 

    > [!NOTE]
    > Ha egy Security Center javaslat neve "(előzetes verzió)" címkével végződik, a javaslat előnézeti természetére hivatkozik, nem a szolgáltatásra.

* **Környezet megerősítése** – Security Center folyamatosan figyeli a Kubernetes-fürtök és a Docker-konfigurációk konfigurációját. Ezután biztonsági javaslatokat hoz létre az iparági szabványoknak megfelelően.

* **Futásidejű védelem** – a következő AK-források folyamatos elemzése révén Security Center riasztást küld, és a gazdagépen és az AK *-* beli fürt szintjén észlelt kártékony tevékenységekre figyelmeztet:
    * Nyers biztonsági események, például a hálózati adatfeldolgozás és a folyamat létrehozása
    * A Kubernetes naplója

    További információ: [veszélyforrások elleni védelem Azure-tárolók](threat-protection.md#azure-containers) esetén

    A lehetséges riasztások listájáért tekintse meg a riasztások hivatkozási táblázatának következő részeit: [AK-fürt szintű riasztások](alerts-reference.md#alerts-akscluster) és [tároló-gazdagép szintű riasztások](alerts-reference.md#alerts-containerhost).  

![Azure Security Center és az Azure Kubernetes szolgáltatás (ak) részletesebben](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> A Kubernetes-környezet Azure Security Center által beolvasott adatok némelyike bizalmas adatokat is tartalmazhat.


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:

* [Azure Security Center és tárolók biztonsága](container-security.md)

* [Integráció a Azure Container Registry](azure-container-registry-integration.md)

* [Adatkezelés a Microsoftnál](https://www.microsoft.com/trust-center/privacy/data-management) – ismerteti a Microsoft-szolgáltatások (például az Azure, az Intune és az Office 365) adatszabályzatait, a Microsoft adatkezelésének részleteit és az adatokat érintő adatmegőrzési házirendeket.