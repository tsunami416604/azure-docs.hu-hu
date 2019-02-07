---
title: Magas rendelkezésre állású portok áttekintő lapján az Azure-ban
titlesuffix: Azure Load Balancer
description: Tudnivalók a magas rendelkezésre állású portok terheléselosztási egy belső terheléselosztón.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: kumud
ms.openlocfilehash: 328471292ea6cbe07e96cc18af7f9c524407de3d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809470"
---
# <a name="high-availability-ports-overview"></a>Magas rendelkezésre állású portok áttekintő lapján

Az Azure Standard Load Balancer segítségével összes porton a TCP és UDP-folyamatok terheléselosztás egyszerre egy belső load balancer használatakor. 

Egy magas rendelkezésre ÁLLÁS portok terheléselosztási szabály egy terheléselosztási szabályt, egy belső Standard Load Balancer konfigurált változata. Terheléselosztás egyetlen szabállyal egy belső Standard Load Balancer-az összes porton érkező összes TCP és UDP-folyamatok biztosításával egyszerűsítheti a terheléselosztó használatát. A terheléselosztás döntési folyamat kiszolgálónként történik. Ez a művelet a következő 5 rekordos kapcsolat alapul: forrás IP címe, forrásport, cél IP-cím, céloldali port és protokoll

A magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályok segíti a kritikus fontosságú, például a magas rendelkezésre állás és a méretezési csoport hálózati virtuális berendezések (nva-k) belüli virtuális hálózatok. A funkció segíthet is, ha nagy számú portok kell lennie, elosztott terhelésű. 

A magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályok van konfigurálva, amikor az előtér- és portok **0** és a protokoll **összes**. A belső terheléselosztó-erőforráshoz ezután elosztja a összes TCP és UDP-folyamatok, függetlenül attól, port száma

## <a name="why-use-ha-ports"></a>Magas rendelkezésre ÁLLÁSÚ portok miért érdemes használni?

### <a name="nva"></a>Hálózati virtuális berendezések

Használhatja az nva-k védelme érdekében az Azure számítási feladatban a biztonsági fenyegetések többféle típusával szemben. Ezekben az esetekben az nva-k használatakor, megbízható és magas rendelkezésre állású kell lenniük, és azok kell horizontális felskálázása az igény szerinti.

Ezen célok érhet el egyszerűen NVA-példányok hozzáadásával a belső terheléselosztó háttérbeli készletéhez, és konfigurálása egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályt.

Az NVA magas rendelkezésre ÁLLÁS forgatókönyvek magas rendelkezésre ÁLLÁSÚ portok a következő előnyöket nyújtják:
- Adja meg a megfelelő példányokhoz hasonlóan a gyors feladatátvétel az állapot-mintavételei példányonként
- A horizontális felskálázás a magasabb teljesítmény biztosítása érdekében *n*– aktív példány
- Adja meg *n*– aktív és aktív-passzív forgatókönyvek
- Szükségtelenné teszi összetett megoldások, például az Apache ZooKeeper-csomópont monitorozáshoz készülékek

Az alábbi ábrán egy Központ-küllő virtuális hálózat üzembe helyezési mutat be. A küllők kényszerített bújtatás forgalmukat a központi virtuális hálózaton, és az nva-n, mielőtt a megbízható területet. Az nva-k, amelyek egy magas rendelkezésre ÁLLÁSÚ portok konfigurációval egy belső Standard terheléselosztó mögött találhatók. Minden forgalmat is feldolgozott és továbbított ennek megfelelően. Konfigurálva, az alábbi ábrán megjelenítése, ha egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály emellett biztosít a flow szimmetrikus bejövő és kimenő forgalomra.

<a node="diagram"></a>
![Központ-küllő virtuális hálózat magas rendelkezésre ÁLLÁSÚ módban rendszerbe állított nva ábrája](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Ha nva-kat használ, erősítse meg a szolgáltatók leginkább a magas rendelkezésre ÁLLÁSÚ portok használatára, és ismerje meg, hogy mely forgatókönyvek támogatottak.

### <a name="load-balancing-large-numbers-of-ports"></a>Terheléselosztás – nagy számú portok

Magas rendelkezésre ÁLLÁSÚ portok nagy számú portok terheléselosztást igénylő alkalmazásokhoz is használható. Egy belső használatával egyszerűsítheti a forgatókönyvekben [Standard Load Balancer](load-balancer-standard-overview.md) a magas rendelkezésre ÁLLÁSÚ portok. Egyetlen terheléselosztási szabály több egyéni terheléselosztási szabályok, minden egyes porthoz egy váltja fel.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Minden a globális Azure-régióban a magas rendelkezésre ÁLLÁSÚ portok funkció érhető el.

## <a name="supported-configurations"></a>Támogatott konfigurációk

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Egy önálló, nem fix (nem - közvetlen kiszolgálói válasz) IP-portok konfigurálása belső Standard Load Balancer

Ez a konfiguráció az alapkonfiguráció magas rendelkezésre ÁLLÁSÚ portok. Beállíthatja, hogy egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályának egyetlen előtér-IP-címet az alábbiak szerint:
1. Válassza ki a Standard Load Balancer konfigurálása, miközben a **magas rendelkezésre ÁLLÁSÚ portok** jelölőnégyzet be a terheléselosztó-konfiguráció.
2. A **nem fix IP-** válassza **letiltott**.

Ez a konfiguráció nem engedélyezése semmilyen más terheléselosztási szabály konfigurálását a jelenlegi terheléselosztó-erőforráshoz. Azt is lehetővé teszi, hogy nincs más belső terheléselosztó erőforrás konfigurációját a háttér-példányok adott halmazát.

Azonban konfigurálhatja úgy a magas rendelkezésre ÁLLÁSÚ portok szabály mellett a háttér-példányok nyilvános Standard Load Balancer.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Egy egyetlen, nem fix (közvetlen kiszolgálói válasz) IP-portok konfigurálása belső Standard Load Balancer

Ehhez hasonlóan beállíthatja a terheléselosztó egy terheléselosztási szabályt az használandó **magas rendelkezésre ÁLLÁS Port** egyetlen előtér beállításával a **nem fix IP-** való **engedélyezve**. 

Ez a konfiguráció használatával adhat hozzá további nem fix IP-terheléselosztási szabályok és/vagy egy nyilvános terheléselosztó. Nem használhatja azonban a nem fix IP portok terheléselosztási konfigurációs felül ezt a konfigurációt.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>A belső Standard Load Balancer több portok – konfiguráció

Ha, hogy konfigurálja-e egynél több magas rendelkezésre ÁLLÁSÚ port kezelőfelület ugyanazt a háttér-készletet, a következőket teheti: 
- Egynél több előtér-privát IP-cím konfigurálása egy adott belső Standard Load Balancer-erőforrás.
- Több terheléselosztási szabályt, ahol minden egyes szabály egy egyedi előtér-IP-címmel rendelkezik kiválasztott konfigurálása.
- Válassza ki a **magas rendelkezésre ÁLLÁSÚ portok** lehetőséget, majd állítsa **nem fix IP-** való **engedélyezve** a terheléselosztási szabályokhoz.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>A magas rendelkezésre ÁLLÁSÚ portok és a egy nyilvános terheléselosztó a háttér-példányt a belső terheléselosztó

Konfigurálható *egy* nyilvános Standard Load Balancer-erőforrás a háttér-erőforrásokhoz, és a egy belső Standard Load Balancer magas rendelkezésre ÁLLÁSÚ portok.

>[!NOTE]
>Ez a funkció jelenleg elérhető az Azure Resource Manager-sablonok, de az Azure Portalon nem érhető el.

## <a name="limitations"></a>Korlátozások

- Magas rendelkezésre ÁLLÁS portok konfigurációs csak a belső terheléselosztók esetében. Nem érhető nyilvános terheléselosztókhoz.

- A csoportba foglalása az egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály és a egy nem magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály nem támogatott.

- A magas rendelkezésre ÁLLÁSÚ portok funkció az IPv6 nem érhető el.

- A folyamat szimmetrikus (elsősorban az NVA-forgatókönyv) csak akkor támogatott, a háttér-példány és a egy egyetlen hálózati adapter (és egyetlen IP-konfiguráció) használatával, és a fenti ábrán látható módon használatakor a magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályok betöltése. Nincs megadva, a bármilyen más forgatókönyvhöz. Ez azt jelenti, hogy két vagy több Load Balancer-erőforrások és a megfelelő szabályok független döntéseket hozhat, és soha ne legyenek. Tekintse meg a leírást és a diagram [hálózati virtuális berendezések](#nva). Ha egy több hálózati adapter használatával, vagy az NVA egy nyilvános és belső Load Balancer között sandwiching, a flow szimmetrikus nem érhető el.  Előfordulhat, hogy a bejövő forgalom áramlását az IP-cím a készülék engedi meg válaszok írását az azonos nva érkezzen NAT'ing forrás szerint ez megoldható.  Azonban erősen ajánlott egy egyetlen hálózati adapter, valamint a fenti ábrán látható referenciaarchitektúra használatához.


## <a name="next-steps"></a>További lépések

- [Magas rendelkezésre ÁLLÁSÚ portok konfigurálása belső Standard Load Balancer](load-balancer-configure-ha-ports.md)
- [Ismerje meg a Standard Load Balancer](load-balancer-standard-overview.md)
