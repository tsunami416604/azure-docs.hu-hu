---
title: VMware-megoldás CloudSimple szerint – Azure nyilvános IP-cím
description: Ismerje meg a nyilvános IP-címeket, valamint a VMware-megoldás előnyeit a CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 29d2bdb75a7f4409e6b3ffee2608cfe7adde6b1f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812522"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple nyilvános IP-cím áttekintése

A nyilvános IP-cím lehetővé teszi, hogy az internetes erőforrások a privát IP-címeken keresztül kommunikáljanak a belső felhőalapú erőforrásokkal. A magánhálózati IP-cím vagy egy virtuális gép, vagy egy szoftveres terheléselosztó. A magánhálózati IP-cím a saját felhőalapú vCenter van. A nyilvános IP-cím lehetővé teszi, hogy elérhetővé tegye a saját felhőben futó szolgáltatásokat az interneten.

A nyilvános IP-cím a magánhálózati IP-címhez van hozzárendelve, amíg meg nem rendeli a hozzárendelést. Egy nyilvános IP-cím csak egy magánhálózati IP-címhez rendelhető hozzá.

Egy nyilvános IP-címhez társított erőforrás mindig a nyilvános IP-címet használja az internet-hozzáféréshez. Alapértelmezés szerint csak a kimenő internet-hozzáférés engedélyezett a nyilvános IP-címen.  A nyilvános IP-cím bejövő forgalma megtagadva.  A bejövő forgalom engedélyezéséhez hozzon létre egy tűzfalszabályot a nyilvános IP-címhez az adott porton.

## <a name="benefits"></a>Előnyök

Nyilvános IP-cím használata a bejövő kommunikációhoz:

* Elosztott szolgáltatásmegtagadási (DDoS) támadások megelőzése. Ez a védelem automatikusan engedélyezve van a nyilvános IP-címhez.
* A forgalom figyelése és a gyakori hálózati szintű támadások valós idejű enyhítése. Ezek a védelem a Microsoft online szolgáltatások által használt védelem.
* Az Azure globális hálózatának teljes skálája. A hálózat használható a különböző régiók közötti támadási forgalom elosztására és enyhítésére.  

## <a name="next-steps"></a>További lépések

* Útmutató [nyilvános IP-cím](https://docs.azure.cloudsimple.com/public-ips/) lefoglalásához
