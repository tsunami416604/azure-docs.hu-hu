---
title: "Magas rendelkezésre állású portok – áttekintés az Azure-ban |} Microsoft Docs"
description: "További információk a magas rendelkezésre állású portok terheléselosztási egy belső terheléselosztón."
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 46e284d1636988390f3533d93bfd07399f45dc92
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="high-availability-ports-overview"></a>Magas rendelkezésre állású portok áttekintése

Azure betöltése terheléselosztó szabványos segít egyenleg TCP és UDP-forgalom összes porton egyidejűleg, betölteni, egy belső terheléselosztó használata esetén. 

>[!NOTE]
> A magas rendelkezésre ÁLLÁS portok szolgáltatás érhető el a Load Balancer Standard, és jelenleg előzetes verzióban érhetők. Előzetes a szolgáltatás esetleg nincs rendelkezésre állásának és megbízhatóságának szolgáltatások általánosan rendelkezésre álló verziója is, azonos szintű. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Iratkozzon fel a Load Balancer szabványos preview Load Balancer szabványos erőforrásokkal magas rendelkezésre ÁLLÁSÚ portok használatára. Kövesse az utasításokat az előfizetési terheléselosztóhoz [szabványos preview](https://aka.ms/lbpreview#preview-sign-up) is.

Egy magas rendelkezésre ÁLLÁSÚ portok szabály egy terheléselosztási szabályt egy belső betöltése terheléselosztó Standard konfigurált változata. A terheléselosztó használatát egyszerűbbé teheti az összes porton egy belső betöltése terheléselosztó standard érkező összes TCP és UDP-forgalom terheléselosztásához egyetlen szabály megadásával. A terheléselosztási döntési ne legyen. A következő 5 rekordos kapcsolat alapul: forrás IP-cím, a forrásport, a cél IP-cím, a célport és a protokoll.

A magas rendelkezésre ÁLLÁSÚ portok funkció segítségével kritikus forgatókönyvek, például a magas rendelkezésre állás és a hálózati virtuális készülékek (NVA) belső virtuális hálózatok számára is méretezhető. Ha nagy számú portok kell lennie az elosztott terhelésű is segíthet. 

A magas rendelkezésre ÁLLÁSÚ portok szolgáltatás van konfigurálva, amikor az előtér- és portok **0**, és a protokollt, hogy **összes**. A belső terheléselosztó erőforrás majd összes TCP és UDP-adatfolyamok, függetlenül attól, portszám egyensúlyba kerüljön.

## <a name="why-use-ha-ports"></a>Magas rendelkezésre ÁLLÁSÚ portok miért érdemes használni?

### <a name="nva"></a>Virtuális hálózati készülékek

Az Azure számítási a több biztonsági fenyegetések elleni védelmét biztosító NVAs is használhatja. NVAs használata a következő használati helyzetekben, megbízható és magas rendelkezésre állású kell lenniük, és azok kell horizontális felskálázás az igény szerinti.

Ezen célok egyszerűen által NVA példányok felvétele az Azure belső terheléselosztó a háttér-készlet, és egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztó szabály konfigurálása érhet el.

Magas rendelkezésre ÁLLÁSÚ portok számos előnyt kínálnak NVA magas rendelkezésre ÁLLÁSÚ forgatókönyvek esetén:
- Kifogástalan példányok, példányonkénti állapotának gyors feladatátvétel mintavétel
- A kibővített magasabb teljesítmény  *n* -aktív példányok
- *N*-aktív és aktív-passzív forgatókönyvek
- Így a nem kell összetett megoldások, például figyelés készülékek Apache ZooKeeper csomópontok

Az alábbi ábra mutatja be a virtuális hálózati hub és küllős központi telepítés. A küllők kényszerített bújtatás az adatforgalmat, a központ virtuális hálózat és az NVA, mielőtt elhagynák a megbízható terület keresztül. A NVAs egy belső terheléselosztási terheléselosztó Standard portok magas rendelkezésre ÁLLÁSÚ konfigurációval mögött találhatók. Az összes forgalom feldolgozni, és ennek megfelelően továbbítani.

![Hub és küllős rendelkező virtuális hálózatban, a magas rendelkezésre ÁLLÁSÚ módban rendszerbe NVAs ábrája](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Ha NVAs használ, győződjön meg arról a megfelelő szolgáltató legjobban a magas rendelkezésre ÁLLÁSÚ portok használata, és melyik forgatókönyvek is támogatottak.

### <a name="load-balancing-large-numbers-of-ports"></a>Terheléselosztás nagy számú portok

Magas rendelkezésre ÁLLÁSÚ portok is használ, a nagy számú portok terheléselosztást igénylő alkalmazásokat. Egy belső használatával leegyszerűsítheti a forgatókönyvekben [Load Balancer szabványos](https://aka.ms/lbpreview) magas rendelkezésre ÁLLÁSÚ porttal. Egyetlen terheléselosztási szabály több egyedi terheléselosztási szabályok, egy minden port a felváltja.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A magas rendelkezésre ÁLLÁSÚ portok funkció érhető el a [Load Balancer szabványos azonos régiók](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Előnézet-előfizetés

Az előzetes betöltési terheléselosztó szabványos magas rendelkezésre ÁLLÁSÚ portok szolgáltatásának részt, az előfizetés regisztrálása a Load Balancer [szabványos előzetes](https://aka.ms/lbpreview#preview-sign-up). Azure CLI 2.0 vagy a PowerShell használatával is regisztrálhat.

>[!NOTE]
>Regisztráció egy óráig is eltarthat.

## <a name="limitations"></a>Korlátozások

A támogatott konfigurációk vagy a magas rendelkezésre ÁLLÁSÚ portok szolgáltatáshoz tartozó kivételek a következők:

- Egyetlen előtér-IP-konfigurációja lehet egy egyetlen közvetlen kiszolgálói válasz (DSR - fix IP-Címek az Azure-ban) terheléselosztói szabálynak, a magas rendelkezésre ÁLLÁSÚ portokkal, vagy azt, hogy egy egyetlen nem DSR terheléselosztási szabály magas rendelkezésre ÁLLÁSÚ porttal. Mindkettő nem lehet.
- Egy egyetlen hálózati illesztő IP-konfiguráció csak van egy nem-DSR terheléselosztói szabálynak, a magas rendelkezésre ÁLLÁSÚ portokkal. Az ipconfig más szabályok nem konfigurálható.
- Egy egyetlen hálózati illesztő IP-konfigurációja is megadták az egy vagy több DSR terheléselosztói szabály a magas rendelkezésre ÁLLÁSÚ portokkal, a megfelelő előtér-IP-konfigurációk mindegyike egyedi.
- A terheléselosztási szabályok összes magas rendelkezésre ÁLLÁSÚ portok (csak DSR), ha két (vagy több) terheléselosztó-szabály a háttér-készlethez is létezhetnek. Is igaz, ha a szabályok összes nem-magas rendelkezésre ÁLLÁSÚ portok (DSR és nem DSR). Ha magas rendelkezésre ÁLLÁSÚ portok és a nem magas rendelkezésre ÁLLÁSÚ portokra vonatkozó szabályokat, azonban két ilyen terheléselosztási szabályok nem létezhet.
- A magas rendelkezésre ÁLLÁSÚ portok funkció nem érhető el az IPv6.
- Csak egyetlen hálózati adapter folyamata szimmetria NVA forgatókönyvek esetén támogatott. Tekintse meg a leírás és diagram a [virtuális készülékekre](#nva). 



## <a name="next-steps"></a>Következő lépések

- [Egy belső Load Balancer Standard a magas rendelkezésre ÁLLÁSÚ portok konfigurálása](load-balancer-configure-ha-ports.md)
- [További tudnivalók a Load Balancer szabványos előzetes verzió](https://aka.ms/lbpreview)

