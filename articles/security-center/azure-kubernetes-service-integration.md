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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 7f43f50fc35b5ecbc9720224036ea13e2ba753f5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894935"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Az Azure Kubernetes Services és a Security Center integrációja

Az Azure Kubernetes Service (ak) a Microsoft által felügyelt szolgáltatás a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez. 

Az Azure **Defender for Kubernetes** lehetővé teszi az AK-csomópontok, a Felhőbeli forgalom és a biztonsági vezérlők mélyebb láthatóságát.

A Security Center és az AK együttese együtt a legjobb Felhőbeli natív Kubernetes biztonsági ajánlatot alkotja.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Mik a Security Center Kubernetes-védelmének összetevői?

A Kubernetes Security Center védelmét két elem kombinációjával biztosítjuk:

- **Azure Security Center a veszélyforrások elleni védelem a virtuális gépek számára** – ugyanazzal a log Analytics ügynökkel, amelyet más virtuális gépeken is Security Center használni, Security Center megmutathatja az AK-csomópontokon előforduló biztonsági problémákat. Az ügynök a Container-specifikus elemzéseket is figyeli.

- **Azure Security Center opcionális Azure Defender for Kubernetes-csomag** – a Kubernetes-csomag az AK szolgáltatáson keresztül fogadja a Kubernetes alrendszer naplóit és információit. Ezek a naplók már elérhetők az Azure-ban az AK szolgáltatáson keresztül. Ha engedélyezi az Azure Defender for Kubernetes, Security Center hozzáférést biztosít a naplókhoz. Így Security Center biztonsági előnyöket biztosít az AK-fürtök számára az AK főcsomópontja által már összegyűjtött adatok használatával. A Kubernetes-környezet Azure Security Center által beolvasott adatok némelyike bizalmas adatokat is tartalmazhat.


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:

* [Azure Security Center és tárolók biztonsága](container-security.md)

* [Integráció az Azure Container Registryvel](azure-container-registry-integration.md)

* [Adatkezelés a Microsoftnál](https://www.microsoft.com/trust-center/privacy/data-management) – ismerteti a Microsoft-szolgáltatások (beleértve az Azure-t, az Intune-t és a Microsoft 365) adatszabályzatait, a Microsoft adatkezelésének részleteit és az adatokat érintő adatmegőrzési szabályzatokat
