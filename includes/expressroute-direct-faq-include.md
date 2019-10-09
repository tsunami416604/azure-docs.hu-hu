---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/07/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 9c53aac47e965a6eba681008d6b6476142190ef8
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72023083"
---
### <a name="what-is-expressroute-direct"></a>Mi az az ExpressRoute Direct?

Az ExpressRoute Direct közvetlen csatlakozást tesz lehetővé az ügyfelek számára a Microsoft globális hálózatához a világszerte stratégiai pontokon elhelyezett társviszony-létesítési helyszíneken át. A ExpressRoute Direct kettős 100 vagy 10 GB/s kapcsolatot biztosít, amely támogatja az aktív/aktív kapcsolatot a skálán. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Hogyan csatlakozhatnak az ügyfelek a ExpressRoute Directhez? 

Az ügyfeleknek a helyi szolgáltatók és a közös telephelyek használatával kell dolgozniuk a ExpressRoute-útválasztókkal való kapcsolattal, hogy kihasználhassa a ExpressRoute Direct szolgáltatás előnyeit.

### <a name="what-locations-currently-support-expressroute-direct"></a>Milyen helyszínek támogatják a ExpressRoute Direct szolgáltatást? 

A rendelkezésre álló portok dinamikusak lesznek, és a PowerShell elérhetővé válik a kapacitás megtekintéséhez. A helyek közé tartoznak a következők, és a *rendelkezésre állás alapján változhatnak*:

* Amszterdam
* Amszterdam2
* Auckland 
* Chicago
* Dallas
* Dublin
* Hongkong (KKT)
* London
* London2
* Melbourne
* New York City
* Perth
* San Antonio
* Seattle
* Szöul
* Szilícium-völgy
* Szingapúr2 
* Sydney
* Tajpej
* Tokió
* Toronto
* Washington, D.C.
* Washington DC2

### <a name="what-is-the-sla-for-expressroute-direct"></a>Mi a ExpressRoute Direct SLA-ja?

A közvetlen ExpressRoute ugyanazt a [nagyvállalati szintű ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)fogja használni. 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Milyen forgatókönyveket érdemes figyelembe venni az ügyfelek a ExpressRoute Directtel?  

A ExpressRoute Direct 100-es vagy 10 GB/s-os portszámú ügyfeleket biztosít a Microsoft globális gerincének. Az ügyfelek számára a legjobb előnyökkel járó forgatókönyvek a következők: Nagy mennyiségű adat betöltése, fizikai elkülönítés a szabályozott piacokon, valamint dedikált kapacitás a burst forgatókönyvhöz, például a rendereléshez. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Mi a ExpressRoute Direct számlázási modellje? 

A ExpressRoute Direct szolgáltatás a port párja után rögzített összegű díjat számít fel. A standard szintű áramköröket további órák nélkül, a prémium pedig kisebb kiegészítő díjat számítunk fel. A kimenő forgalom számlázása a társítási hely zónája alapján, áramkör alapján történik.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Mikor kezdődik a ExpressRoute Direct port-párok számlázása?

A 45 közvetlen ExpressRoute a ExpressRoute közvetlen erőforrásának létrehozásakor, vagy ha 1 vagy mindkét hivatkozás engedélyezve van, akkor az előbbi értéknek kell lennie. A 45 napos türelmi időszak lehetővé teszi, hogy az ügyfelek a közös elhelyezésű szolgáltatóval is elvégezzék a kapcsolatok közötti folyamatot.
