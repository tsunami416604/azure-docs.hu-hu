---
title: Gyakran ismételt kérdések az Azure dev Spaces-ről
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Válaszok az Azure dev Spaces használatával kapcsolatos gyakori kérdésekre
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: d5ab56edfe4799d51fb7f08642aad9e2ee01db05
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044963"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Gyakran ismételt kérdések az Azure dev Spaces-ről

Ez az Azure dev Spaces-re vonatkozó gyakori kérdéseket tárgyalja.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Mely Azure-régiók biztosítanak Azure dev Spaces-helyeket?

Az elérhető régiók teljes listájáért tekintse meg a [támogatott régiók][supported-regions] című témakört.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Használhatom nyilvános IP-cím nélkül az Azure dev Spaces szolgáltatást?

Nem, nyilvános IP-cím nélkül nem lehet Azure dev-helyeket kiépíteni egy AK-fürtön. Az [Azure dev Spaces for Routing][dev-spaces-routing]szolgáltatáshoz nyilvános IP-cím szükséges.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Használhatom a saját beáramlást az Azure dev Spaces szolgáltatással?

Igen, beállíthatja saját bemenő adatait az egy Azure dev Spaces létrehozása mellett. Használhatja például a [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Használhatom a HTTPS-t az Azure dev Spaces használatával?

Igen, a [traefik][ingress-https-traefik]használatával saját bejövő adatait is konfigurálhatja HTTPS-kapcsolattal.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Használhatom az Azure dev Spaces szolgáltatást olyan fürtön, amely nem kubenet, hanem CNI használ? 

Igen, használhatja az Azure dev Spaces szolgáltatást egy olyan AK-fürtön, amely CNI használ a hálózatkezeléshez. Használhatja például az Azure dev Spaces szolgáltatást egy AK-fürtön [meglévő Windows-tárolókkal][windows-containers], amelyek hálózati CNI használnak. A CNI és az Azure dev Spaces közötti hálózatkezeléssel kapcsolatos további információk [itt](configure-networking.md#using-azure-cni)érhetők el.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Használhatom az Azure dev Spaces szolgáltatást Windows-tárolókkal?

Jelenleg az Azure dev Spaces kizárólag Linux-hüvelyeken és-csomópontokon fut, de az Azure fejlesztői tárhelyeit egy AK-fürtön futtathatja [meglévő Windows-tárolók][windows-containers]használatával.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Használhatok Azure dev Spaces-et az AK-fürtökön, ha engedélyezve vannak az API Server által engedélyezett IP-címtartományok?

Igen, az Azure dev-szóközöket az AK-fürtökön használhatja az [API-kiszolgáló által engedélyezett IP-címtartományok][aks-auth-range] engedélyezésével. További információ az Azure dev Spaces szolgáltatással rendelkező, az API Server által engedélyezett IP-címtartományok használatával rendelkező AK-fürtök használatáról [itt](configure-networking.md#using-api-server-authorized-ip-ranges)található.

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Használhatom az Azure dev Spaces-t az AK-fürtökön a fürtcsomópontok korlátozott kimenő forgalmával?

Igen, a megfelelő teljes tartománynevek engedélyezése után használhatja az Azure dev-szóközöket az AK-fürtökön a [korlátozott kimenő forgalommal rendelkező fürtcsomópontok esetében][aks-restrict-egress-traffic] . [Itt](configure-networking.md#ingress-and-egress-network-traffic-requirements)talál további információt arról, hogyan használhatók a korlátozott kimenő forgalommal rendelkező AK-fürtök az Azure dev Spaces szolgáltatással engedélyezett fürtcsomópontok esetében.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md