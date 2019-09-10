---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/16/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a42284765a46f4a000dc5b7fcf2867ef17d69570
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "68229244"
---
| Resource | Alapértelmezett korlát |
| --- | --- |
| Adatátviteli sebesség |30 GB/s<sup>1</sup> |
|Szabályok|10 000, az összes szabálytípus kombinálva.|
|Minimális AzureFirewallSubnet-méret |/26|
|Porttartomány a hálózat és az alkalmazás szabályaiban|0 – 64000. A művelet elvégzése folyamatban van.|
|Nyilvános IP-címek|100 maximális|
|Útválasztási táblázat|Alapértelmezés szerint a AzureFirewallSubnet 0.0.0.0/0 útvonalon van, és az NextHopType érték van beállítva az **internethez**.<br><br>Azure Firewall közvetlen internetkapcsolattal kell rendelkeznie. Ha a AzureFirewallSubnet a BGP-n keresztül tanulja meg a helyszíni hálózat alapértelmezett útvonalát, akkor a közvetlen internetkapcsolat fenntartása érdekében egy 0.0.0.0/0 UDR kell megadnia a **NextHopType** értékkel. Alapértelmezés szerint a Azure Firewall nem támogatja a kényszerített bújtatást egy helyszíni hálózatra.<br><br>Ha azonban a konfiguráció kényszerített bújtatást igényel egy helyszíni hálózathoz, a Microsoft eseti alapon fogja támogatni azt. Forduljon az ügyfélszolgálathoz, és tekintse át az esetet. Ha elfogadják, engedélyezzük az előfizetését, és gondoskodni kell a tűzfal internetkapcsolatának fenntartásáról.|

<sup>1</sup> Ha meg kell emelnie ezeket a korlátokat, forduljon az Azure ügyfélszolgálatához.
