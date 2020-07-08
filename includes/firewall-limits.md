---
title: fájlbefoglalás
description: fájlbefoglalás
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 06/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b317c2a43352a750d4700fad56d5f7d741b2cc7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805703"
---
| Erőforrás | Korlát |
| --- | --- |
| Adatátviteli sebesség |30 GB/s<sup>1</sup> |
|Szabályok|10 000. Minden szabálytípus kombinálva.|
|Maximális DNAT-szabályok|298 egyetlen nyilvános IP-címhez.<br>Minden további nyilvános IP-cím a rendelkezésre álló SNAT-portokhoz járul hozzá, de csökkentheti az elérhető DNAT-szabályok számát. Például két nyilvános IP-cím lehetővé teszi a 297 DNAT-szabályok használatát. Ha a szabály protokollja a TCP és az UDP használatára van konfigurálva, két szabálynak számít.|
|Minimális AzureFirewallSubnet-méret |/26|
|Porttartomány a hálózat és az alkalmazás szabályaiban|1 - 65535|
|Nyilvános IP-címek|250 maximális érték. Az összes nyilvános IP-cím használható DNAT-szabályokban, és mind hozzájárulnak az elérhető SNAT-portokhoz.|
|IP-címek az IP-csoportokban|Tűzfalon legfeljebb 100 IP-csoport adható meg.<br>Az egyes IP-csoportokban legfeljebb 5000 egyedi IP-cím vagy IP-előtag adható meg.<br><br>További információ: [IP-csoportok Azure Firewall](../articles/firewall/ip-groups.md#ip-address-limits).
|Útválasztási táblázat|Alapértelmezés szerint a AzureFirewallSubnet 0.0.0.0/0 útvonalon van, és az NextHopType érték van beállítva az **internethez**.<br><br>Azure Firewall közvetlen internetkapcsolattal kell rendelkeznie. Ha a AzureFirewallSubnet a BGP-n keresztül tanulja meg a helyszíni hálózat alapértelmezett útvonalát, akkor a közvetlen internetkapcsolat **fenntartása érdekében egy** 0.0.0.0/0 UDR kell megadnia a **NextHopType** értékkel. Alapértelmezés szerint a Azure Firewall nem támogatja a kényszerített bújtatást egy helyszíni hálózatra.<br><br>Ha azonban a konfiguráció kényszerített bújtatást igényel egy helyszíni hálózathoz, a Microsoft eseti alapon fogja támogatni azt. Forduljon az ügyfélszolgálathoz, és tekintse át az esetet. Ha elfogadják, engedélyezzük az előfizetését, és gondoskodni kell a tűzfal internetkapcsolatának fenntartásáról.|

<sup>1</sup> Ha meg kell emelnie ezeket a korlátokat, forduljon az Azure ügyfélszolgálatához.
