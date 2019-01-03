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
ms.openlocfilehash: ae36adc78d8c87d85c64fd61cb3a50dfcae60b85
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995712"
---
Két dologért fizet: a virtuális hálózati átjáró óránkénti számítási költségeiért és a virtuális hálózati átjáró kimenő adatátviteléért. A díjakról a [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway) oldalon tájékozódhat.

**A virtuális hálózati átjáró számítási költségei**<br>Minden virtuális hálózati átjáró óránkénti számítási költségekkel rendelkezik. Az ár a virtuális hálózati átjáró létrehozáskor megadott termékváltozatán alapul. A költség magára az átjáróra vonatkozik, és az átjárón áthaladó adatátvitelen felül értendő. Egy aktív-aktív telepítő költsége megegyezik a aktív-passzív.

**Adatátviteli költségek**<br>Az adatátviteli költségek kiszámítása a forrás virtuális hálózati átjáróról származó kimenő forgalmon alapul.

* Ha forgalmat küld helyszíni VPN-eszközére, a költségek az internetes kimenő adatátviteli tarifa alapján lesznek megállapítva.
* Ha különböző régiókban található virtuális hálózatok között küld forgalmat, a díjszabás a régióhoz igazodik.
* Ha csak azonos régióban található virtuális hálózatok között küld forgalmat, nincsenek adatátviteli költségek. Az azonos régióban található virtuális hálózatok közötti forgalom ingyenes.