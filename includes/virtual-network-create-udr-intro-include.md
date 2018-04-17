---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 10f723d5298e745520c4db41b994bd2b97aaa365
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
Bár a rendszerútvonalak használata automatikusan segíti az üzemelő példány forgalmát, vannak esetek, amikor a csomagok útválasztását érdemesebb egy virtuális készüléken keresztül irányítani. Ezt úgy teheti meg, hogy felhasználó által megadott útvonalakat hoz létre, amelyek úgy határozzák meg a következő ugrást az adott alhálózatra áramló csomagok számára, hogy azok a virtuális készülékre érkezzenek, valamint engedélyezi az IP-továbbítást a virtuális készülékként futó virtuális gép számára.

A virtuális készülékek helyének esetek a következők:

* Behatolás-észlelés rendszerrel (Azonosítók) forgalom figyelése
* A tűzfal forgalom vezérlése

További információt a UDR és IP-továbbítás [felhasználó által megadott útvonalak és IP-továbbítás](../articles/virtual-network/virtual-networks-udr-overview.md).

