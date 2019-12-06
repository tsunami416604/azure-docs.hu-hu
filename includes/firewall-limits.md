---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/06/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a7b6867033e750f476b3d995926f0b670965a5d7
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875522"
---
| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Adatátviteli sebesség |30 GB/s<sup>1</sup> |
|Szabályok|10 000. Minden szabálytípus kombinálva.|
|DNAT-szabályok/nyilvános IP-címek|299|
|Minimális AzureFirewallSubnet-méret |/26|
|Porttartomány a hálózat és az alkalmazás szabályaiban|0 – 64000. A művelet elvégzése folyamatban van.|
|Nyilvános IP-címek|100 maximális (jelenleg a SNAT portok csak az első öt nyilvános IP-címhez lesznek hozzáadva.)|
|Útválasztási táblázat|Alapértelmezés szerint a AzureFirewallSubnet 0.0.0.0/0 útvonalon van, és az NextHopType érték van beállítva az **internethez**.<br><br>Azure Firewall közvetlen internetkapcsolattal kell rendelkeznie. Ha a AzureFirewallSubnet a BGP-n keresztül tanulja meg a helyszíni hálózat alapértelmezett útvonalát, akkor a közvetlen internetkapcsolat **fenntartása érdekében egy** 0.0.0.0/0 UDR kell megadnia a **NextHopType** értékkel. Alapértelmezés szerint a Azure Firewall nem támogatja a kényszerített bújtatást egy helyszíni hálózatra.<br><br>Ha azonban a konfiguráció kényszerített bújtatást igényel egy helyszíni hálózathoz, a Microsoft eseti alapon fogja támogatni azt. Forduljon az ügyfélszolgálathoz, és tekintse át az esetet. Ha elfogadják, engedélyezzük az előfizetését, és gondoskodni kell a tűzfal internetkapcsolatának fenntartásáról.|

<sup>1</sup> Ha meg kell emelnie ezeket a korlátokat, forduljon az Azure ügyfélszolgálatához.
