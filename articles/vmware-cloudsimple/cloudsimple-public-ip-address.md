---
title: Azure VMware-megoldások (AVS) – nyilvános IP-cím
description: Ismerje meg a nyilvános IP-címeket és azok előnyeit az Azure VMware Solutions (AVS) megoldásokban
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cb9d0e33da4447760ae0be216c1dd9868c498bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024976"
---
# <a name="avs-public-ip-address-overview"></a>AVS nyilvános IP-cím – áttekintés

A nyilvános IP-cím lehetővé teszi, hogy az internetes erőforrások egy magánhálózati IP-címen keresztül kommunikáljanak az AVS Private Cloud-erőforrásokkal. A magánhálózati IP-cím egy virtuális gép vagy egy szoftveres terheléselosztó az AVS Private Cloud vCenter. A nyilvános IP-cím lehetővé teszi, hogy az AVS Private Cloud-on futó szolgáltatásokat az interneten keresztül tegye elérhetővé.

A nyilvános IP-cím a magánhálózati IP-címhez van hozzárendelve, amíg meg nem rendeli a hozzárendelést. Egy nyilvános IP-cím csak egy magánhálózati IP-címhez rendelhető hozzá.

Egy nyilvános IP-címhez társított erőforrás mindig a nyilvános IP-címet használja az internet-hozzáféréshez. Alapértelmezés szerint csak a kimenő internet-hozzáférés engedélyezett a nyilvános IP-címen.  A nyilvános IP-cím bejövő forgalma megtagadva.  A bejövő forgalom engedélyezéséhez hozzon létre egy tűzfalszabályot a nyilvános IP-címhez az adott porton.

## <a name="benefits"></a>Előnyök

Nyilvános IP-cím használata a bejövő kommunikációhoz:

* Elosztott szolgáltatásmegtagadási (DDoS) támadások megelőzése. Ez a védelem automatikusan engedélyezve van a nyilvános IP-címhez.
* A forgalom figyelése és a gyakori hálózati szintű támadások valós idejű enyhítése. Ezek a védelem a Microsoft online szolgáltatások által használt védelem.
* Az Azure globális hálózatának teljes skálája. A hálózat használható a különböző régiók közötti támadási forgalom elosztására és enyhítésére.  

## <a name="next-steps"></a>Következő lépések

* Útmutató [nyilvános IP-cím lefoglalásához](public-ips.md)