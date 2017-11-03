---
title: "Bevezetés az Azure hálózati figyelőt a következő ugrás |} Microsoft Docs"
description: "Ezen a lapon a hálózati figyelőt áttekintést nyújt következő ugrás funkció"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bb2ca0486b3b3d27a77b70927cb3cbfbeac12c7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-next-hop-in-azure-network-watcher"></a>Bevezetés az Azure hálózati figyelőt a következő ugrás

VM forgalmat elküldi a hatékony társított hálózati útvonalak alapján célhelyét. Következő ugrás lekérése a következő ugrás típusa és a csomagok IP-cím egy adott virtuális gép és a hálózati adaptert. Ez segít meghatározni, ha a csomag van irányítja a cél, vagy éppen fekete forgalom furatos van. Az útvonalak a felhasználó, ahol a forgalom irányítja a rendszer egy helyi helyre, vagy a virtuális készülék, egy nem megfelelő konfigurációs problémák vezethet. Következő ugrás is a következő ugrás hozzárendelt útválasztási táblázatot ad vissza. Lekérdezésekor a következő ugrás, ha egy felhasználó által megadott útvonalat az útvonal van meghatározva, az adott útvonal eredmény. Egyéb esetben a következő ugrás "Rendszerútvonal" ad vissza.

![a következő ugrás – áttekintés][1]

A következő ugrás Típuslista, amely a következő ugrás lekérdezésekor adhatók vissza a következő:

* Internet
* VirtualAppliance
* Pedig
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

### <a name="next-steps"></a>Következő lépések

Következő Ugrás használata a problémák keresése a hálózati kapcsolat hibája ellátogatva [ellenőrizze a következő ugrás a virtuális gép](network-watcher-check-next-hop-portal.md)

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png













