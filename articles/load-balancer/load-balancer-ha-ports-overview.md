---
title: Magas rendelkezésre állású portok – áttekintés az Azure-ban |} Microsoft Docs
description: További információk a magas rendelkezésre állású portok terheléselosztási egy belső terheléselosztón.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
ms.openlocfilehash: 09c51441d393de5d801e7a4c259b711a527349d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="high-availability-ports-overview"></a>Magas rendelkezésre állású portok áttekintése

Azure Standard terheléselosztó segítséget nyújt egy belső terheléselosztó használata esetén egy időben, betöltése a egyenleg TCP és UDP-forgalom összes porton. 

Egy magas rendelkezésre ÁLLÁSÚ portok szabály variant egy terheléselosztási szabályt egy belső szabványos terheléselosztó konfigurálva. Egy belső szabványos terheléselosztó az összes porton érkező összes TCP és UDP-forgalom terheléselosztásához egyetlen szabály megadásával egyszerűbbé teheti a terheléselosztó használatát. A terheléselosztási döntési ne legyen. A következő 5 rekordos kapcsolat alapul: forrás IP-cím, forrásport, cél IP-címe, célport és protokoll.

A magas rendelkezésre ÁLLÁSÚ portok funkció segítségével kritikus forgatókönyvek, például a magas rendelkezésre állás és a hálózati virtuális készülékek (NVA) belső virtuális hálózatok számára is méretezhető. Ha nagy számú portok kell lennie az elosztott terhelésű is segíthet. 

A magas rendelkezésre ÁLLÁSÚ portok szolgáltatás van konfigurálva, amikor az előtér- és portok **0**, és a protokollt, hogy **összes**. A belső terheléselosztó erőforrás majd összes TCP és UDP-adatfolyamok, függetlenül attól, portszám egyensúlyba kerüljön.

## <a name="why-use-ha-ports"></a>Magas rendelkezésre ÁLLÁSÚ portok miért érdemes használni?

### <a name="nva"></a>Virtuális hálózati készülékek

Az Azure számítási a több biztonsági fenyegetések elleni védelmét biztosító NVAs is használhatja. NVAs használata a következő használati helyzetekben, megbízható és magas rendelkezésre állású kell lenniük, és azok kell horizontális felskálázás az igény szerinti.

Ezen célok egyszerűen által NVA példányok felvétele az Azure belső terheléselosztó a háttér-készlet, és egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztó szabály konfigurálása érhet el.

Magas rendelkezésre ÁLLÁSÚ portok számos előnyt kínálnak NVA magas rendelkezésre ÁLLÁSÚ forgatókönyvek esetén:
- Kifogástalan példányok, példányonkénti állapotának gyors feladatátvétel mintavétel
- A kibővített magasabb teljesítmény *n*-aktív példányok
- *N*-aktív és aktív-passzív forgatókönyvek
- Így a nem kell összetett megoldások, például figyelés készülékek Apache ZooKeeper csomópontok

Az alábbi ábra mutatja be a virtuális hálózati hub és küllős központi telepítés. A küllők kényszerített bújtatás az adatforgalmat, a központ virtuális hálózat és az NVA, mielőtt elhagynák a megbízható terület keresztül. A NVAs portok magas rendelkezésre ÁLLÁSÚ konfigurációval egy belső szabványos terheléselosztó mögött van. Az összes forgalom feldolgozni, és ennek megfelelően továbbítani.

![Hub és küllős rendelkező virtuális hálózatban, a magas rendelkezésre ÁLLÁSÚ módban rendszerbe NVAs ábrája](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Ha NVAs használ, győződjön meg arról a megfelelő szolgáltató legjobban a magas rendelkezésre ÁLLÁSÚ portok használata, és melyik forgatókönyvek is támogatottak.

### <a name="load-balancing-large-numbers-of-ports"></a>Terheléselosztás nagy számú portok

Magas rendelkezésre ÁLLÁSÚ portok is használ, a nagy számú portok terheléselosztást igénylő alkalmazásokat. Egy belső használatával leegyszerűsítheti a forgatókönyvekben [szabványos terheléselosztó](load-balancer-standard-overview.md) magas rendelkezésre ÁLLÁSÚ porttal. Egyetlen terheléselosztási szabály több egyedi terheléselosztási szabályok, egy minden port a felváltja.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A magas rendelkezésre ÁLLÁSÚ portok funkció minden globális Azure területen érhető el.

## <a name="supported-configurations"></a>Támogatott konfigurációk

### <a name="one-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Egy egyetlen nem fix IP (nem - közvetlen kiszolgálói válasz) magas rendelkezésre ÁLLÁSÚ portok konfigurálása a szabványos belső terheléselosztón

Ez a beállítás egy alapszintű magas rendelkezésre ÁLLÁSÚ portok. A következő konfiguráció lehetővé teszi a magas rendelkezésre ÁLLÁSÚ portok terheléselosztás konfigurálása az egyetlen előtérbeli IP-cím-
- A szabványos terheléselosztó beállításakor válassza ki a **magas rendelkezésre ÁLLÁSÚ portok** a terheléselosztó-konfiguráció, a jelölőnégyzet 
- Annak biztosítása, **fix IP-Címek** értéke **letiltott**.

Ez a konfiguráció nem teszi lehetővé más terheléselosztási szabály beállításokat az aktuális terheléselosztó erőforráson, valamint nincs más belső terheléselosztó erőforrás-konfiguráció a háttér-példányok adott halmazát.

Azonban a háttér-példányok mellett a magas rendelkezésre ÁLLÁSÚ portszabály nyilvános szabványos terheléselosztó konfigurálhatja.

## <a name="one-single-floating-ip-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Egy egyetlen fix IP-(közvetlen kiszolgálói válasz) magas rendelkezésre ÁLLÁSÚ portok konfigurációs szabványos belső terheléselosztón

Hasonló módon konfigurálhatja a terheléselosztó terheléselosztási szabály használandó **magas rendelkezésre ÁLLÁSÚ Port** az egyetlen időtúllépést, és a **fix IP-Címek** beállítása **engedélyezve**. 

Ez a konfiguráció lehetővé teszi több fix IP terheléselosztási szabályok, és / vagy egy nyilvános terheléselosztó. Azonban nem fix IP magas rendelkezésre ÁLLÁSÚ portszám nélküli boad terheléselosztási-konfiguráció használata ehhez a konfigurációhoz felett.

## <a name="multiple-ha-ports-configurations-on-the-internal-standard-load-balancer"></a>A szabványos belső terheléselosztón több magas rendelkezésre ÁLLÁSÚ portok konfiguráció

Ha adott esetben meg kell adni a azonos háttérkészlet egynél több magas rendelkezésre ÁLLÁSÚ port frontends, ezt úgy érhet el: 
- egynél több előtér konfigurálása magán az IP-címeket egyetlen belső szabványos terheléselosztó erőforrást.
- több terheléselosztási szabályok, ahol minden egyes szabály rendelkezik egyetlen konfigurálása egyedi előtérbeli IP-cím van kiválasztva.
- Válassza ki **magas rendelkezésre ÁLLÁSÚ portok** lehetőséget, és állítsa be **fix IP-Címek** való **engedélyezve** összes a terheléselosztási szabályok.

## <a name="internal-load-balancer-with-ha-ports--public-load-balancer-on-the-same-backend-instances"></a>A magas rendelkezésre ÁLLÁSÚ portok & azonos háttéralkalmazás-példányokon nyilvános terheléselosztót belső terheléselosztó

Konfigurálható **egy** nyilvános szabványos terheléselosztó erőforrás a háttérrendszer erőforrások együtt egyetlen belső szabványos terheléselosztó magas rendelkezésre ÁLLÁSÚ porttal.

>[!NOTE]
>Ez a funkció érhető el ma Azure Resource Manager-sablonok keresztül, de nem az Azure-portálon keresztül.

## <a name="limitations"></a>Korlátozások

- Magas rendelkezésre ÁLLÁSÚ portok konfigurációs lehetőség csak a belső terheléselosztó, nem egy nyilvános terheléselosztó használható.

- Magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályok és a szabályok nem - magas rendelkezésre ÁLLÁSÚ portok terheléselosztás kombinációja nem támogatott.

- A magas rendelkezésre ÁLLÁSÚ portok funkció az IPv6 nem érhető el.

- Csak egyetlen hálózati adapter folyamata szimmetria NVA forgatókönyvek esetén támogatott. Tekintse meg a leírás és diagram a [virtuális készülékekre](#nva). Azonban ha egy cél NAT adott esetben is képes működni, segítségével, győződjön meg arról, hogy a belső terheléselosztó elküldi a forgalom az azonos NVA.


## <a name="next-steps"></a>További lépések

- [Egy szabványos belső terheléselosztón magas rendelkezésre ÁLLÁSÚ portok konfigurálása](load-balancer-configure-ha-ports.md)
- [Standard terheléselosztó megismerése](load-balancer-standard-overview.md)
