---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238421"
---
| Resource | Alapértelmezett korlát |
| --- | --- |
| Átviteli adatok |30 GB/s<sup>1</sup> |
|Szabályok|10 000-et, az összes szabály típusa kombinált.|
|Minimális AzureFirewallSubnet mérete |/26|
|A hálózati és az szabályokban porttartomány|0-64,000. Ez a korlátozás enyhítése folyamatban van.|
|Útválasztási táblázat|Alapértelmezés szerint AzureFirewallSubnet egy 0.0.0.0/0 útvonalat rendelkezik a nexthoptype elem érték **Internet**.<br><br>Az Azure tűzfal közvetlen internetkapcsolattal kell rendelkeznie. A AzureFirewallSubnet megtanulja az alapértelmezett útvonalat a BGP-n keresztül a helyszíni hálózathoz, ha meg kell felülbírálja ezt az egy 0.0.0.0/0 UDR-a **NextHopType** értéket állítja be **Internet** közvetlen fenntartása Internetkapcsolat. Alapértelmezés szerint az Azure-tűzfal nem támogatja a kényszerített bújtatás a helyszíni hálózathoz.<br><br>Azonban ha a konfigurációt igényel a helyszíni hálózathoz a kényszerített bújtatás, a Microsoft támogatja eseti alapon. Úgy, hogy az eset, forduljon az ügyfélszolgálathoz. Ha elfogadja, hogy lesz engedélyezett az előfizetés, és biztosítják a szükséges internetkapcsolattal.|

<sup>1</sup>Ha ezek a korlátok növeléséhez van szüksége, forduljon az Azure ügyfélszolgálatához.
