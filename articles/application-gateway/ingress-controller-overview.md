---
title: Mi az Azure Application Gateway inbehatolás vezérlő?
description: Ez a cikk bemutatja, hogy milyen Application Gateway beléptetési vezérlő.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 838145f8573e11deff8566c932a9c73c6f59f03b
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561660"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Mi az Application Gateway beáramló vezérlő?
A Application Gateway beáramlási vezérlő (AGIC) egy Kubernetes-alkalmazás, amely lehetővé teszi, hogy az [Azure Kubernetes Service (ak)](https://azure.microsoft.com/services/kubernetes-service/) ügyfelei kihasználják az azure natív [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 Load-balancert a felhőalapú szoftverek interneten való elérhetővé tételéhez. A AGIC figyeli az üzemeltetett Kubernetes-fürtöt, és folyamatosan frissíti egy Application Gateway, hogy a kiválasztott szolgáltatások elérhetők legyenek az internethez.

A bejövő vezérlő a saját Pod-on fut az ügyfél AK-ban. A AGIC a Kubernetes-erőforrások egy részhalmazát figyeli a változásokhoz. Az AK-fürt állapota Application Gateway meghatározott konfigurációra van lefordítva, és a [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)alkalmazásra lett alkalmazva.

## <a name="benefits-of-application-gateway-ingress-controller"></a>A Application Gateway beáramlási vezérlő előnyei
A AGIC lehetővé teszi, hogy az üzembe helyezés több AK-alapú fürt vezérlését egyetlen Application Gateway bejövő adatkezelővel. A AGIC azt is lehetővé teszi, hogy egy másik terheléselosztó/nyilvános IP-cím legyen az AK-fürt előtt, és elkerüli a több ugrást a DataPath, mielőtt a kérelmek elérnék az AK-fürtöt. A Application Gateway a saját saját IP-címével beszél a hüvelyekről, és nem igényli a NodePort vagy a KubeProxy szolgáltatás használatát. Ez az üzembe helyezések jobb teljesítményét is lehetővé teszi.

A beáramló vezérlőt kizárólag Standard_v2 és WAF_v2 SKU támogatja, ami az automatikus skálázási előnyökkel is jár. Application Gateway reagálhat a forgalom terhelésének növekedésére vagy csökkenésére, és ennek megfelelően méretezhető, anélkül, hogy az AK-fürtből erőforrásokat kellene használnia.

A AGIC mellett a Application Gateway használatával a TLS-házirend és a webalkalmazási tűzfal (WAF) funkciójának segítségével is megvédheti az AK-fürtöt.

![Azure Application Gateway + AK](./media/application-gateway-ingress-controller-overview/architecture.png)

A AGIC a Kubernetes beáramlási [erőforrással](https://kubernetes.io/docs/user-guide/ingress/), valamint a szolgáltatás és az üzembe helyezések/hüvelyek használatával konfigurálható. Számos funkciót kínál, az Azure natív Application Gateway L7 terheléselosztó használatával. Csak néhányat említsünk:
  - URL-útválasztás
  - Cookie-alapú affinitás
  - SSL leállítása
  - Végpontok közötti SSL
  - Nyilvános, privát és hibrid webhelyek támogatása
  - Integrált webalkalmazási tűzfalszolgáltatás

A AGIC több névteret képes kezelni, és ProhibitedTargets rendelkezik, ami azt jelenti, hogy a AGIC a többi meglévő háttérrendszer módosítása nélkül is konfigurálhatja a Application Gatewayt az AK-fürtökhöz. 

## <a name="next-steps"></a>Következő lépések

- [**Zöldmezős üzembe helyezés**](ingress-controller-install-new.md): a AGIC, az AK és a Application Gateway telepítésére vonatkozó utasítások az üres-pala infrastruktúrán.
- [**Rozsdaövezetek rehabilitálása-telepítés**](ingress-controller-install-existing.md): telepítse a AGIC-t egy meglévő AK-ra és Application Gatewayra.

