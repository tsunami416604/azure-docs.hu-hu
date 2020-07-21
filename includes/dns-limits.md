---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 0f7187300ec96ce417866c4fb8fa02783c1da63a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515870"
---
**Nyilvános DNS-zónák**

| Erőforrás | Korlát |
| --- | --- |
| Nyilvános DNS-zónák/előfizetés |250 <sup>1</sup> |
| Rekordhalmazok nyilvános DNS-zónában |10 000 <sup>1</sup> |
| Rekord/rekordhalmaz a nyilvános DNS-zónában |20 |
| Egyetlen Azure-erőforráshoz tartozó alias-rekordok száma |20|

<sup>1</sup> Ha meg kell emelnie ezeket a korlátokat, forduljon az Azure ügyfélszolgálatához.

**saját DNS zónák**

| Erőforrás | Korlát |
| --- | --- |
| saját DNS zónák/előfizetés |1000|
| Rekordhalmazok egy saját DNS-zónában |25000|
| Rekord/rekordhalmaz a magánhálózati DNS-zónákhoz |20|
| Privát DNS-zóna Virtual Network hivatkozásai |1000|
| A virtuális hálózatok privát DNS-zónákon keresztül, automatikus regisztráció engedélyezve |100|
| Azon privát DNS-zónák száma, amelyekhez a virtuális hálózat elérhetővé válik, és engedélyezve van az automatikus regisztráció |1|
| Saját DNS-zónák száma, amelyekhez a virtuális hálózat kapcsolódhat |1000|
| A virtuális gép által elküldhető DNS-lekérdezések száma másodpercenként Azure DNS feloldó számára |500 <sup>1</sup> |
| A várólistára helyezett DNS-lekérdezések maximális száma (Válasz függőben) virtuális gépenként |200 <sup>1</sup> |

<sup>1</sup> Ezeket a korlátokat minden egyes virtuális gépre alkalmazza a rendszer, nem pedig a virtuális hálózat szintjén. A határértékeket meghaladó DNS-lekérdezések el lettek dobva.
