---
title: Az Azure Security Center és az Azure Kubernetes szolgáltatás
description: Ismerje meg az Azure Security Center és az Azure Kubernetes-szolgáltatások integrációját
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
ms.openlocfilehash: d1cd4691586b27282d221a19c5fb7a1af034ed6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125161"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Az Azure Kubernetes-szolgáltatások integrációja a Biztonsági központtal

Az Azure Kubernetes-szolgáltatás (AKS) a Microsoft felügyelt szolgáltatása a tárolóba helyezett alkalmazások fejlesztéséhez, telepítéséhez és kezeléséhez. 

Az AKS-t az Azure Security Center standard csomagjával (lásd: [díjszabás)](security-center-pricing.md)együtt használhatja az AKS-csomópontok, a felhőbeli forgalom és a biztonsági vezérlők mélyebb láthatóságának megszerzéséhez.

A Security Center biztonsági előnyökkel jár az AKS-fürtök számára az AKS főcsomópont által már összegyűjtött adatok használatával. 

![Az Azure Security Center és az Azure Kubernetes-szolgáltatás (AKS) magas szintű áttekintése](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Ez a két eszköz együttesen alkotja a legjobb felhőalapú Kubernetes biztonsági kínálatot. 

## <a name="benefits-of-integration"></a>Az integráció előnyei

A két szolgáltatás együttes használata a következőket biztosítja:

* **Biztonsági javaslatok** – A Security Center azonosítja az AKS-erőforrásokat, és kategorizálja őket: a fürtöktől az egyes virtuális gépekig. Ezután megtekintheti az erőforrásonkénti biztonsági javaslatokat. További információt a tárolók ajánlásai ban, az [ajánlások hivatkozási listájában talál.](recommendations-reference.md#recs-computeapp) 

* **Környezetedzés** – A Security Center folyamatosan figyeli a Kubernetes-fürtök és a Docker-konfigurációk konfigurációját. Ezután olyan biztonsági javaslatokat hoz létre, amelyek tükrözik az iparági szabványokat.

* **Futásidejű védelem** – A Következő AKS-források folyamatos elemzésével a Security Center figyelmezteti a gazdagép *en és* az AKS-fürt szintjén észlelt fenyegetésekre és rosszindulatú tevékenységekre:
    * Nyers biztonsági események, például hálózati adatok és folyamatok létrehozása
    * A Kubernetes naplója

    További információ: [Fenyegetésvédelem az Azure-tárolókhoz](threat-protection.md#azure-containers)

    A lehetséges riasztások listáját a riasztások hivatkozási táblázata: [AKS-fürtszintű riasztások](alerts-reference.md#alerts-akscluster) és [a tárolóállomás-szintű riasztások](alerts-reference.md#alerts-containerhost)című témakörben található ezeket a szakaszokat tartalmazza.  

![Az Azure Security Center és az Azure Kubernetes-szolgáltatás (AKS) részletesebben](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Az Azure Security Center által a Kubernetes-környezetből beolvasott adatok egy része bizalmas adatokat tartalmazhat.


## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a Security Center tárolóbiztonsági szolgáltatásairól, olvassa el az:

* [Az Azure Security Center és a tárolóbiztonság](container-security.md)

* [Integráció az Azure Container Registryvel](azure-container-registry-integration.md)

* [Adatkezelés a Microsoftnál](https://www.microsoft.com/trust-center/privacy/data-management) – A Microsoft-szolgáltatások (beleértve az Azure, az Intune és az Office 365 adatházirendjeit), a Microsoft adatkezelésének részleteit és az adatokat érintő adatmegőrzési irányelveket ismerteti