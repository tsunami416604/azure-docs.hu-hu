---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 205c67377e4bff66c02e3ee508c0f6f4e2823608
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
Két dologért fizet: a virtuális hálózati átjáró óránkénti számítási költségeiért és a virtuális hálózati átjáró kimenő adatátviteléért. A díjakról a [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway) oldalon tájékozódhat.

**A virtuális hálózati átjáró számítási költségei**<br>Minden virtuális hálózati átjáró óránkénti számítási költségekkel rendelkezik. Az ár a virtuális hálózati átjáró létrehozáskor megadott termékváltozatán alapul. A költség magára az átjáróra vonatkozik, és az átjárón áthaladó adatátvitelen felül értendő.

**Adatátviteli költségek**<br>Az adatátviteli költségek kiszámítása a forrás virtuális hálózati átjáróról származó kimenő forgalmon alapul.

* Ha forgalmat küld helyszíni VPN-eszközére, a költségek az internetes kimenő adatátviteli tarifa alapján lesznek megállapítva.
* Ha különböző régiókban található virtuális hálózatok között küld forgalmat, a díjszabás a régióhoz igazodik.
* Ha csak azonos régióban található virtuális hálózatok között küld forgalmat, nincsenek adatátviteli költségek. Az azonos régióban található virtuális hálózatok közötti forgalom ingyenes.