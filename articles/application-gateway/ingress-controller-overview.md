---
title: Mi az Azure Application Gateway ingress controller?
description: Ez a cikk bemutatja, hogy mi az Application Gateway ingress controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335803"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Mi az Application Gateway ingress controller?
Az Application Gateway Ingress Controller (AGIC) egy Kubernetes-alkalmazás, amely lehetővé teszi az [Azure Kubernetes-szolgáltatás (AKS)](https://azure.microsoft.com/services/kubernetes-service/) ügyfelei számára, hogy kihasználják az Azure natív [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 terheléselosztóját a felhőalapú szoftverek internethez való elérhetővé tévává. Az AGIC figyeli azt a Kubernetes-fürtöt, amelyen az alkalmazásátjáró található, és folyamatosan frissíti az Alkalmazásátjárót, hogy a kiválasztott szolgáltatások elérhetők legyenek az interneten.

A bejövő kapcsolatvezérlő saját podjában fut az ügyfél AKS-én. Az AGIC a Kubernetes-erőforrások egy részhalmazát figyeli a változások szempontjából. Az AKS-fürt állapota az Application Gateway-specifikus konfigurációra lesz lefordítva, és az [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)alkalmazásra kerül.

## <a name="benefits-of-application-gateway-ingress-controller"></a>Az Application Gateway be- és átjáróvezérlőjének előnyei
Az AGIC lehetővé teszi, hogy a központi telepítés több AKS-fürtöt vezéreljen egyetlen Application Gateway Ingress Controller rel. Az AGIC segít kiküszöbölni egy másik terheléselosztó/nyilvános IP-cím szükségességét az AKS-fürt előtt, és elkerüli a több ugrást a datapath-ban, mielőtt a kérelmek elérnék az AKS-fürtöt. Az Application Gateway közvetlenül a privát IP-címüket használó podokat beszéli, és nem igényel NodePort vagy KubeProxy szolgáltatásokat. Ez is hozza a jobb teljesítményt a központi telepítések.

A rendszerszámos adatkezelőt kizárólag Standard_v2 és WAF_v2 suk támogatja, ami automatikus skálázási előnyökkel is jár. Az Application Gateway reagálhat a forgalom terhelésének növekedésére vagy csökkenésére, és ennek megfelelően skálázhatja, anélkül, hogy az AKS-fürtből származó erőforrásokat fellenne szabadítva.

Az Alkalmazásátjáró használata az AGIC mellett a TLS-házirend és a WEBApplication Firewall (WAF) funkcióbiztosításával is segít megvédeni az AKS-fürtöt.

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

Az AGIC a Kubernetes [ingress erőforráson](https://kubernetes.io/docs/user-guide/ingress/)keresztül van konfigurálva, valamint a Service and Deployments/Pods. Számos funkciót kínál, kihasználva az Azure natív Application Gateway L7 terheléselosztóját. Hogy csak néhányat említsünk:
  - URL-útválasztás
  - Cookie-alapú affinitás
  - A TLS megszüntetése
  - Végpontok között TLS
  - Állami, magán- és hibrid webhelyek támogatása
  - Integrált webes alkalmazás tűzfal

Az AGIC több névteret is képes kezelni, és tiltott célokat is tartalmazó, ami azt jelenti, hogy az AGIC az Alkalmazásátjárót kifejezetten az AKS-fürtökhöz konfigurálhatja anélkül, hogy más meglévő háttérrendszereket befolyásolna. 

## <a name="next-steps"></a>Következő lépések

- [**Zöldmezős telepítés:**](ingress-controller-install-new.md)Az AGIC, az AKS és az Application Gateway üres lappal történő infrastruktúrára történő telepítésével kapcsolatos utasítások.
- [**Brownfield Deployment**](ingress-controller-install-existing.md): Telepítse az AGIC-et egy meglévő AKS- és alkalmazásátjáróra.

