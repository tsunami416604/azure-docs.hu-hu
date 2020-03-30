---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 01/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0df38533afe97f010d1050c3ee2a4a69a54d4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335103"
---
| Erőforrás | Korlát |
| --- | --- |
| Adatátviteli sebesség |30 Gbps<sup>1</sup> |
|Szabályok|10,000. Minden szabálytípus kombinálva.|
|Maximális DNST-szabályok|299|
|Minimális AzureFirewallSubnet-méret |/26|
|Porttartomány a hálózati és alkalmazási szabályokban|0-64,000. A munka folyamatban van, hogy lazítson ezt a korlátozást.|
|Nyilvános IP-címek|Legfeljebb 100 (jelenleg az SNAT-portok csak az első öt nyilvános IP-címhez adnak hozzá.)|
|Útválasztási táblázat|Alapértelmezés szerint az AzureFirewallSubnet 0.0.0.0/0 útvonallal rendelkezik, amelynek NextHopType értéke **internetre**van állítva.<br><br>Az Azure tűzfalnak közvetlen internetkapcsolattal kell rendelkeznie. Ha az AzureFirewallSubnet megtanulja az alapértelmezett útvonalat a helyszíni hálózatra a BGP-n keresztül, felül kell írnia, hogy a 0.0.0.0/0 UDR a **NextHopType** érték **beállítása internet a** közvetlen internetkapcsolat fenntartásához. Alapértelmezés szerint az Azure Firewall nem támogatja a kényszerített bújtatás egy helyszíni hálózatra.<br><br>Ha azonban a konfiguráció kényszerített bújtatást igényel egy helyszíni hálózathoz, a Microsoft eseti alapon támogatja azt. Lépjen kapcsolatba az ügyfélszolgálattal, hogy áttekinthessük az ügyét. Ha elfogadják, engedélyezzük az előfizetést, és biztosítjuk a szükséges tűzfal internetkapcsolat fenntartását.|

<sup>1 1</sup> Ha növelnie kell ezeket a korlátokat, forduljon az Azure-támogatáshoz.
