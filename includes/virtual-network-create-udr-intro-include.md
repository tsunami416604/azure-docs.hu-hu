---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 482241deb1081ac8a5265a076eabbdc3fb6d659e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170871"
---
Bár a rendszerútvonalak használata automatikusan segíti az üzemelő példány forgalmát, vannak esetek, amikor a csomagok útválasztását érdemesebb egy virtuális készüléken keresztül irányítani. Ezt úgy teheti meg, hogy felhasználó által megadott útvonalakat hoz létre, amelyek úgy határozzák meg a következő ugrást az adott alhálózatra áramló csomagok számára, hogy azok a virtuális készülékre érkezzenek, valamint engedélyezi az IP-továbbítást a virtuális készülékként futó virtuális gép számára.

A virtuális készülékek helyének esetek a következők:

* Forgalom behatolás-észlelési rendszerrel (Azonosítók) figyelése
* A tűzfal-forgalom szabályozása

További információ az udr-t és az IP-továbbítást, látogasson el a [felhasználó által megadott útvonalak és IP-továbbítás](../articles/virtual-network/virtual-networks-udr-overview.md).

