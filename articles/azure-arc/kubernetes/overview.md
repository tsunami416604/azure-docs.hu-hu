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
ms.openlocfilehash: 3960a0f0b6866a0a5d3476e841d29c6e5a89a238
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309934"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Mi az Azure arc-kompatibilis Kubernetes előzetes verziója?

Az Azure arc-kompatibilis Kubernetes előzetes verziójának használatával csatlakoztathat és konfigurálhat Kubernetes-fürtöket az Azure-on belül és kívül is. Ha egy Kubernetes-fürt csatlakozik az Azure arc-hoz, akkor az megjelenik a Azure Portalban. Azure Resource Manager azonosítója és felügyelt identitása lesz. A fürtök szabványos Azure-előfizetésekhez vannak csatolva, és egy erőforráscsoport alatt találhatók, és ugyanúgy fogadhatnak címkéket, mint bármely más Azure-erőforrást. 

Kubernetes-fürt Azure-hoz való összekapcsolásához a fürt rendszergazdájának ügynököket kell telepítenie. Ezek az ügynökök egy nevű Kubernetes-névtérben futnak `azure-arc` , és szabványos Kubernetes üzemelő példányok. Az ügynökök felelősek az Azure-hoz való csatlakozásért, az Azure arc-naplók és-metrikák összegyűjtéséhez, valamint a konfigurációs kérések figyeléséhez. 

Az Azure arc-kompatibilis Kubernetes támogatja az iparági szabványnak megfelelő SSL-t az adatátvitel biztonságossá tételéhez. Az adatok titkosságának biztosítása érdekében a rendszer a Azure Cosmos DB adatbázisában tárolja az adatok titkosítását is.
 
 > [!NOTE]
> Az Azure arc-kompatibilis Kubernetes előzetes verzióban érhető el. Nem ajánlott éles környezetben üzemelő számítási feladatokhoz. 


## <a name="supported-scenarios"></a>Támogatott esetek 

Az Azure arc-kompatibilis Kubernetes az alábbi forgatókönyveket támogatja: 

* Az Azure-on kívül futó Kubernetes-ket a leltár, csoportosítás és címkézés céljából is összekapcsolhatjuk.

* Alkalmazások telepítése és konfiguráció alkalmazása a GitOps-alapú konfiguráció-kezelés használatával. 

* A fürtök megtekintéséhez és figyeléséhez használjon Azure Monitor tárolókat. 

* Házirendek alkalmazása a Kubernetes Azure Policy használatával. 

 
## <a name="supported-regions"></a>Támogatott régiók 

Az Azure arc-kompatibilis Kubernetes jelenleg a következő régiókban támogatott: 

* USA keleti régiója 
* Nyugat-Európa 

## <a name="next-steps"></a>További lépések

* [Fürt összekötése](./connect-cluster.md)
