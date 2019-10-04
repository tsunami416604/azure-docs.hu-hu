---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/08/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8f8d366961049deb3eda193718ccb553aac930e3
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666376"
---
Két dologért fizet: a virtuális hálózati átjáró óránkénti számítási költségeiért és a virtuális hálózati átjáró kimenő adatátviteléért. A díjakról a [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway) oldalon tájékozódhat. Örökölt gateway Termékváltozat díjszabása, lásd: a [díjszabását ismertető lapon ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) , és görgessen a **virtuális hálózati átjárók** szakaszban.

**A virtuális hálózati átjáró számítási költségei**<br>Minden virtuális hálózati átjáró óránkénti számítási költségekkel rendelkezik. Az ár a virtuális hálózati átjáró létrehozáskor megadott termékváltozatán alapul. A költség magára az átjáróra vonatkozik, és az átjárón áthaladó adatátvitelen felül értendő. Egy aktív-aktív telepítő költsége megegyezik a aktív-passzív.

**Adatátviteli költségek**<br>Az adatátviteli költségek kiszámítása a forrás virtuális hálózati átjáróról származó kimenő forgalmon alapul.

* Ha forgalmat küld helyszíni VPN-eszközére, a költségek az internetes kimenő adatátviteli tarifa alapján lesznek megállapítva.
* Ha különböző régiókban található virtuális hálózatok között küld forgalmat, a díjszabás a régióhoz igazodik.
* Ha csak azonos régióban található virtuális hálózatok között küld forgalmat, nincsenek adatátviteli költségek. Az azonos régióban található virtuális hálózatok közötti forgalom ingyenes.
