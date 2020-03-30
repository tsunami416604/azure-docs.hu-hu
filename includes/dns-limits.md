---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334594"
---
**Nyilvános DNS-zónák**

| Erőforrás | Korlát |
| --- | --- |
| Nyilvános DNS-zónák előfizetésenként |250 <sup>1</sup> |
| Rekordhalmazok nyilvános DNS-zónánként |10 000 <sup>1</sup> |
| Rekordok rekordkészletenként a nyilvános DNS-zónában |20 |
| Egyetlen Azure-erőforrás aliasrekordjainak száma |20|
| Privát DNS-zónák előfizetésenként |1000|
| Rekordhalmazok magánDNS-zónánként |25000|
| Rekordok rekordkészletenként a magán DNS-zónákhoz |20|
| Virtuális hálózati kapcsolatok magán DNS-zónánként |1000|
| Virtuális hálózatok Kapcsolatok magánDNS-zónákonként, ha az automatikus regisztráció engedélyezve van |100|
| Azon magánhálózati DNS-zónák száma, amelyekhez a virtuális hálózat automatikus regisztrációval kapcsolható be |1|
| A virtuális hálózat által összekapcsolható magánDNS-zónák száma |1000|
| A virtuális gép által az Azure DNS-feloldónak küldhető DNS-lekérdezések száma másodpercenként |500 <sup>2</sup> |
| A várólistára helyezett DNS-lekérdezések maximális száma (függőben lévő válasz) virtuális gépenként |200 <sup>22.</sup> |

<sup>1 1</sup> Ha növelnie kell ezeket a korlátokat, forduljon az Azure-támogatáshoz.

<sup>2.</sup> Ezek a korlátok minden egyes virtuális gépre vonatkoznak, és nem a virtuális hálózat szintjén. Az e korlátokat meghaladó DNS-lekérdezéseket a rendszer elejti.