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
ms.openlocfilehash: 92c2e79910e40721a0ef62d44825bd1f3e19fc79
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548209"
---
| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Adatátviteli sebesség |30 GB/s<sup>1</sup> |
|Szabályok|10 000. Minden szabálytípus kombinálva.|
|Maximális DNAT-szabályok|299|
|Minimális AzureFirewallSubnet-méret |/26|
|Porttartomány a hálózat és az alkalmazás szabályaiban|0 – 64000. A művelet elvégzése folyamatban van.|
|Nyilvános IP-címek|100 maximális (jelenleg a SNAT portok csak az első öt nyilvános IP-címhez lesznek hozzáadva.)|
|Útválasztási táblázat|Alapértelmezés szerint a AzureFirewallSubnet 0.0.0.0/0 útvonalon van, és az NextHopType érték van beállítva az **internethez**.<br><br>Azure Firewall közvetlen internetkapcsolattal kell rendelkeznie. Ha a AzureFirewallSubnet a BGP-n keresztül tanulja meg a helyszíni hálózat alapértelmezett útvonalát, akkor a közvetlen internetkapcsolat **fenntartása érdekében egy** 0.0.0.0/0 UDR kell megadnia a **NextHopType** értékkel. Alapértelmezés szerint a Azure Firewall nem támogatja a kényszerített bújtatást egy helyszíni hálózatra.<br><br>Ha azonban a konfiguráció kényszerített bújtatást igényel egy helyszíni hálózathoz, a Microsoft eseti alapon fogja támogatni azt. Forduljon az ügyfélszolgálathoz, és tekintse át az esetet. Ha elfogadják, engedélyezzük az előfizetését, és gondoskodni kell a tűzfal internetkapcsolatának fenntartásáról.|

<sup>1</sup> Ha meg kell emelnie ezeket a korlátokat, forduljon az Azure ügyfélszolgálatához.
