---
title: Azure arc-kompatibilis Kubernetes – áttekintés
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, arc, Azure, tárolók
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666257"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Mi az Azure arc-kompatibilis Kubernetes (előzetes verzió)

Az Azure-on belüli vagy kívüli Kubernetes-fürtöket az Azure arc-kompatibilis Kubernetes (előzetes verzió) segítségével csatlakoztathatja és konfigurálhatja. Ha egy Kubernetes-fürt csatlakozik az Azure arc-hoz, az az Azure Portalon fog megjelenni, Azure Resource Manager-AZONOSÍTÓval és egy felügyelt identitással. A fürtök szabványos Azure-előfizetésekhez vannak csatolva, és egy erőforráscsoporthoz vannak csatlakoztatva, és ugyanúgy fogadhatnak címkéket, mint bármely más Azure-erőforrást. 


A Kubernetes-fürtök Azure-hoz való csatlakoztatásához a fürt rendszergazdájának kell telepítenie az ügynököket. Ezek az ügynökök egy nevű Kubernetes-névtérben futnak `azure-arc` , és szabványos Kubernetes üzemelő példányok. Az ügynökök felelősek az Azure-hoz való csatlakozásért, az Azure arc-naplók és-metrikák összegyűjtéséhez, valamint a konfigurációs kérések figyeléséhez.  
 
 > [!NOTE]
> Az Azure arc-kompatibilis Kubernetes előzetes verzióban érhető el, és éles számítási feladatokhoz nem ajánlott. 


## <a name="supported-scenarios"></a>Támogatott helyzetek 

Az Azure arc-kompatibilis Kubernetes a következő forgatókönyveket támogatja: 

* Az Azure-on kívül futó Kubernetes összekapcsolása leltár, csoportosítás és címkézés céljából 

* Alkalmazások telepítése és konfiguráció alkalmazása a GitOps-alapú konfiguráció-kezelés használatával 

* Tárolók Azure Monitor használata a fürtök megtekintéséhez és figyeléséhez 

* Házirendek alkalmazása a Kubernetes Azure Policy használatával 

 
## <a name="supported-regions"></a>Támogatott régiók 

Az Azure arc-kompatibilis Kubernetes jelenleg a következő régiókban támogatott: 

* USA keleti régiója 
* Nyugat-Európa 


## <a name="next-steps"></a>Következő lépések

* [Fürt összekötése](./connect-cluster.md)
