---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/03/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 4972a80ec7c481ac7bc8860c005c8576ce37a090
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758656"
---
| Erőforrás | Korlát |
| --- | --- |
| Adatátviteli sebesség |30 Gbps<sup>1</sup> |
|Szabályok|10,000. Minden szabálytípus kombinálva.|
|Maximális DNST-szabályok|298<br>Ha egy szabály protokollja tcp-hez és UDP-hez is konfigurálva van, az két szabálynak számít.|
|Minimális AzureFirewallSubnet-méret |/26|
|Porttartomány a hálózati és alkalmazási szabályokban|0-64,000. A munka folyamatban van, hogy lazítson ezt a korlátozást.|
|Nyilvános IP-címek|Legfeljebb 100 (jelenleg az SNAT-portok csak az első öt nyilvános IP-címhez adnak hozzá.)|
|IP-csoportok IP-címei|50 IP-csoport vagy kevesebb: tűzfalpéldányonként legfeljebb 5000 egyedi IP-cím.<br>51 - 100 IP-csoport: tűzfalpéldányonként 500 egyedi IP-cím.<br><br>További információ: [IP-csoportok (előzetes verzió) az Azure Firewall](../articles/firewall/ip-groups.md#ip-address-limits)
|Útválasztási táblázat|Alapértelmezés szerint az AzureFirewallSubnet 0.0.0.0/0 útvonallal rendelkezik, amelynek NextHopType értéke **internetre**van állítva.<br><br>Az Azure tűzfalnak közvetlen internetkapcsolattal kell rendelkeznie. Ha az AzureFirewallSubnet megtanulja az alapértelmezett útvonalat a helyszíni hálózatra a BGP-n keresztül, felül kell írnia, hogy a 0.0.0.0/0 UDR a **NextHopType** érték **beállítása internet a** közvetlen internetkapcsolat fenntartásához. Alapértelmezés szerint az Azure Firewall nem támogatja a kényszerített bújtatás egy helyszíni hálózatra.<br><br>Ha azonban a konfiguráció kényszerített bújtatást igényel egy helyszíni hálózathoz, a Microsoft eseti alapon támogatja azt. Lépjen kapcsolatba az ügyfélszolgálattal, hogy áttekinthessük az ügyét. Ha elfogadják, engedélyezzük az előfizetést, és biztosítjuk a szükséges tűzfal internetkapcsolat fenntartását.|

<sup>1 1</sup> Ha növelnie kell ezeket a korlátokat, forduljon az Azure-támogatáshoz.
