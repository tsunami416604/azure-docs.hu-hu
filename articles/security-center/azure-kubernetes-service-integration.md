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
ms.openlocfilehash: 3f58afa41a27427f8deabb945261d96763edb4bc
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126178"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Az Azure Kubernetes Services és a Security Center integrációja

Az Azure Kubernetes Service (ak) a Microsoft által felügyelt szolgáltatás a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez. 

Az AK-k együttes használata a Azure Security Center Standard szintjével (lásd a [díjszabást](security-center-pricing.md)) az AK-csomópontok, a Felhőbeli forgalom és a biztonsági vezérlők mélyebb megismeréséhez.

A Security Center biztonsági előnyöket biztosít az AK-fürtök számára az AK fő csomópontja által már összegyűjtött adatok használatával. 

![A Azure Security Center és az Azure Kubernetes Service (ak) magas szintű áttekintése](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Ez a két eszköz együttesen a legjobb Felhőbeli natív Kubernetes biztonsági ajánlatot alkotja. 

## <a name="benefits-of-integration"></a>Az integráció előnyei

A két szolgáltatás együttes használata a következőket biztosítja:

* **Biztonsági javaslatok** – Security Center AZONOSÍTJA az AK-erőforrásokat, és kategorizálja őket: a fürtöktől az egyes virtuális gépekig. Ezután megtekintheti az egyes erőforrásokra vonatkozó biztonsági javaslatokat. További információkért tekintse meg a tárolók javaslatait a [javaslatok hivatkozási listájában](recommendations-reference.md#recs-containers). 

* **Környezet megerősítése** – Security Center folyamatosan figyeli a Kubernetes-fürtök és a Docker-konfigurációk konfigurációját. Ezután biztonsági javaslatokat hoz létre az iparági szabványoknak megfelelően.

* **Futásidejű védelem** – a következő AK-források folyamatos elemzése révén Security Center riasztást küld, és a gazdagépen és az AK *-* beli fürt szintjén észlelt kártékony tevékenységekre figyelmeztet:
    * Nyers biztonsági események, például a hálózati adatfeldolgozás és a folyamat létrehozása
    * A Kubernetes naplója

    További információ: [veszélyforrások elleni védelem Azure-tárolók](threat-protection.md#azure-containers) esetén

    A lehetséges riasztások listájáért tekintse meg a riasztások hivatkozási táblázatának következő részeit: [AK-fürt szintű riasztások](alerts-reference.md#alerts-akscluster) és [tároló-gazdagép szintű riasztások](alerts-reference.md#alerts-containerhost).  

![Azure Security Center és az Azure Kubernetes szolgáltatás (ak) részletesebben](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> A Kubernetes-környezet Azure Security Center által beolvasott adatok némelyike bizalmas adatokat is tartalmazhat.


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:

* [Azure Security Center és tárolók biztonsága](container-security.md)

* [Integráció az Azure Container Registryvel](azure-container-registry-integration.md)

* [Adatkezelés a Microsoftnál](https://www.microsoft.com/trust-center/privacy/data-management) – ismerteti a Microsoft-szolgáltatások (beleértve az Azure-t, az Intune-t és a Microsoft 365) adatszabályzatait, a Microsoft adatkezelésének részleteit és az adatokat érintő adatmegőrzési szabályzatokat
