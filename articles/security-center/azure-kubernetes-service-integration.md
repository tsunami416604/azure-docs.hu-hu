---
title: Azure Security Center és az Azure Kubernetes Service | Microsoft Docs
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
ms.openlocfilehash: 9bc01e0e703ea9f98d877be39011dcca7c7b284a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521760"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Az Azure Kubernetes Services integrációja Security Center (előzetes verzió)
Az Azure Kubernetes Service (ak) a Microsoft által felügyelt szolgáltatás a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez. 

Az AK-k együttes használata a Azure Security Center Standard szintjével (lásd a [díjszabást](security-center-pricing.md)) az AK-csomópontok, a Felhőbeli forgalom és a biztonsági vezérlők mélyebb megismeréséhez.

A Security Center biztonsági előnyöket biztosít az AK-fürtök számára az AK fő csomópontja által már összegyűjtött adatok használatával. 

![A Azure Security Center és az Azure Kubernetes Service (ak) magas szintű áttekintése](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Ez a két eszköz együttesen a legjobb Felhőbeli natív Kubernetes biztonsági ajánlatot alkotja. 

## <a name="benefits-of-integration"></a>Az integráció előnyei

A két szolgáltatás együttes használata a következőket biztosítja:

* **Biztonsági javaslatok** – Security Center AZONOSÍTJA az AK-erőforrásokat, és kategorizálja őket: a fürtöktől az egyes virtuális gépekig. Ezután megtekintheti az egyes erőforrásokra vonatkozó biztonsági javaslatokat. További információ: [a biztonsági javaslatok implementálása](security-center-recommendations.md). 

    > [!NOTE]
    > Ha egy Security Center javaslat neve "(előzetes verzió)" címkével végződik, a javaslat előnézeti természetére hivatkozik; nem a funkció.

* **Környezet megerősítése** – Security Center folyamatosan figyeli a Kubernetes-fürtök konfigurációját, és biztonsági javaslatokat hoz létre az iparági szabványoknak megfelelően.

* **Futásidejű védelem** – a következő AK-források folyamatos elemzése révén Security Center riasztásokat jelenít meg a gazdagépen és az AK *-* beli fürt szintjén észlelt fenyegetésekkel és rosszindulatú tevékenységgel kapcsolatban (További információ: [Azure Container Service ](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-container-service-)):
    * Nyers biztonsági események, például a hálózati adatfeldolgozás és a folyamat létrehozása
    * A Kubernetes naplója

![Azure Security Center és az Azure Kubernetes szolgáltatás (ak) részletesebben](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> A Kubernetes-környezet Azure Security Center által beolvasott adatok némelyike bizalmas adatokat is tartalmazhat.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:

* [Azure Security Center és tárolók biztonsága](container-security.md)

* [Integráció a Azure Container Registry](azure-container-registry-integration.md)

* [Virtuális gépek védelme](security-center-virtual-machine-protection.md) – a Security Center javaslatainak ismertetése

* [Adatkezelés a Microsoftnál](https://www.microsoft.com/trust-center/privacy/data-management) – ismerteti a Microsoft-szolgáltatások (például az Azure, az Intune és az Office 365) adatszabályzatait, a Microsoft adatkezelésének részleteit és az adatokat érintő adatmegőrzési házirendeket.