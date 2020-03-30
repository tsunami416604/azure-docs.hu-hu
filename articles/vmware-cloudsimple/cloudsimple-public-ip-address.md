---
title: Azure VMware-megoldás a CloudSimple által – nyilvános IP-cím
description: Ismerje meg a nyilvános IP-címeket és azok előnyeit a CloudSimple Azure VMware-megoldásában
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024976"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple nyilvános IP-cím – áttekintés

A nyilvános IP-cím lehetővé teszi, hogy az internetes erőforrások privát IP-címen kommunikáljanak a privát felhőerőforrásaiba. A privát IP-cím egy virtuális gép vagy egy szoftver terheléselosztó a private cloud vCenter. A nyilvános IP-cím lehetővé teszi, hogy a magánfelhőn futó szolgáltatásokat elérhetővé tegye az interneten.

A nyilvános IP-cím a privát IP-címhez van rendelve, amíg vissza nem rendeli. Nyilvános IP-cím csak egy privát IP-címhez rendelhető hozzá.

A nyilvános IP-címhez társított erőforrás mindig a nyilvános IP-címet használja az internet-hozzáféréshez. Alapértelmezés szerint nyilvános IP-címen csak kimenő internet-hozzáférés engedélyezett.  A nyilvános IP-címen bejövő forgalom megtagadva.  A bejövő forgalom engedélyezéséhez hozzon létre egy tűzfalszabályt az adott port nyilvános IP-címéhez.

## <a name="benefits"></a>Előnyök

Nyilvános IP-cím használata a bejövő kommunikációhoz a következőket biztosítja:

* Elosztott szolgáltatásmegtagadási (DDoS) támadásmegelőzés. Ez a védelem automatikusan engedélyezve van a nyilvános IP-címhez.
* Mindig forgalomfigyelés és a gyakori hálózati szintű támadások valós idejű csökkentése. Ezek a védelem ugyanazok a védelem által használt Microsoft online szolgáltatások.
* Az Azure globális hálózatának teljes skálája. A hálózat a támadások közötti forgalom régiók közötti elosztására és csökkentésére használható.  

## <a name="next-steps"></a>További lépések

* Nyilvános [IP-cím lefoglalásának elsajátítása](public-ips.md)