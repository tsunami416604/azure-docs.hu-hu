---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429980"
---
| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Feldolgozott adatok |1000 TB/tűzfal/hó <sup>1</sup> |
|Szabályok|10k - az összes szabály típusa kombinált|
|Globális társviszony-létesítés|Nem támogatott. Rendelkeznie kell legalább egy tűzfal központi telepítési régiónként.|
|Az egyetlen hálózati szabály maximális portok|15<br>Egy porttartományt (például: 2 – 10) számítani két.
|Minimális AzureFirewallSubnet mérete |/26|
|A hálózati és az szabályokban porttartomány|0 – 64 000. Ez a korlátozás enyhítése folyamatban van.|
|


<sup>1</sup> forduljon az Azure támogatási esetben ezek a korlátok növelni szeretné.
