---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0467359cd9d6a067e519a62532f00459bc5f68cb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804179"
---
| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Átviteli adatok |30 GB/s<sup>1</sup> |
|Szabályok|10 000-et, az összes szabály típusa kombinált.|
|Minimális AzureFirewallSubnet mérete |/26|
|A hálózati és az szabályokban porttartomány|0-64,000. Ez a korlátozás enyhítése folyamatban van.|
|Útválasztási táblázat|Alapértelmezés szerint AzureFirewallSubnet egy 0.0.0.0/0 útvonalat rendelkezik a nexthoptype elem érték **Internet**.<br><br>Ha engedélyezi a kényszerített bújtatás ExpressRoute vagy VPN-átjáró használatával helyszíni, szükség lehet explicit módon egy 0.0.0.0/0 felhasználó által megadott útvonal (UDR) konfigurálja a nexthoptype elem értéke csoportot, az Internet, és társíthatja azt a AzureFirewallSubnet. Ez a művelet lehetséges alapértelmezett átjárót a helyszíni hálózat vissza a BGP hirdetésből. Ha a szervezet megköveteli, hogy az Azure-tűzfal alapértelmezett átjáró forgalmat a helyszíni hálózaton keresztül visszairányító kényszerített bújtatás, forduljon az ügyfélszolgálathoz. Azt is változatlan marad az előfizetés annak biztosítása érdekében az internetkapcsolat szükséges tűzfal engedélyezési listáján.|

<sup>1</sup>Ha ezek a korlátok növeléséhez van szüksége, forduljon az Azure ügyfélszolgálatához.
