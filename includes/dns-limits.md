---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334594"
---
**Nyilvános DNS-zónák**

| Erőforrás | Korlát |
| --- | --- |
| Nyilvános DNS-zónák/előfizetés |250 <sup>1</sup> |
| Rekordhalmazok nyilvános DNS-zónában |10 000 <sup>1</sup> |
| Rekord/rekordhalmaz a nyilvános DNS-zónában |20 |
| Egyetlen Azure-erőforráshoz tartozó alias-rekordok száma |20|
| saját DNS zónák/előfizetés |1000|
| Rekordhalmazok egy saját DNS-zónában |25000|
| Rekord/rekordhalmaz a magánhálózati DNS-zónákhoz |20|
| Privát DNS-zóna Virtual Network hivatkozásai |1000|
| A virtuális hálózatok privát DNS-zónákon keresztül, automatikus regisztráció engedélyezve |100|
| Azon privát DNS-zónák száma, amelyekhez a virtuális hálózat elérhetővé válik, és engedélyezve van az automatikus regisztráció |1|
| Saját DNS-zónák száma, amelyekhez a virtuális hálózat kapcsolódhat |1000|
| A virtuális gép által elküldhető DNS-lekérdezések száma másodpercenként Azure DNS feloldó számára |500 <sup>2</sup> |
| A várólistára helyezett DNS-lekérdezések maximális száma (Válasz függőben) virtuális gépenként |200 <sup>2</sup> |

<sup>1</sup> Ha meg kell emelnie ezeket a korlátokat, forduljon az Azure ügyfélszolgálatához.

<sup>2</sup> Ezeket a korlátokat minden egyes virtuális gépre alkalmazza a rendszer, nem pedig a virtuális hálózat szintjén. A határértékeket meghaladó DNS-lekérdezések el lettek dobva.