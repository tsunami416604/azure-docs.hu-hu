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
ms.date: 04/07/2018
ms.author: kumud
ms.openlocfilehash: e37b127b112768cd09989e1a4b5edf99ca101983
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141866"
---
# <a name="high-availability-ports-overview"></a>Magas rendelkezésre állású portok áttekintő lapján

Az Azure Standard Load Balancer segítségével összes porton a TCP és UDP-folyamatok terheléselosztás egyszerre egy belső load balancer használatakor. 

Egy magas rendelkezésre ÁLLÁS portok szabály valamely változatában egy terheléselosztási szabályt, egy belső Standard Load Balancer konfigurálva. Terheléselosztás egyetlen szabállyal egy belső Standard Load Balancer-az összes porton érkező összes TCP és UDP-folyamatok biztosításával egyszerűsítheti a terheléselosztó használatát. A terheléselosztás döntési folyamat kiszolgálónként történik. Ez a művelet a következő 5 rekordos kapcsolat alapul: forrás IP címe, forrásport, cél IP-cím, céloldali port és protokoll.

A magas rendelkezésre ÁLLÁSÚ portok a szolgáltatás segít a kritikus esetekhez, például a magas rendelkezésre állás és a méretezési csoport hálózati virtuális berendezések (nva-k) belüli virtuális hálózatok. A funkció segíthet is, ha nagy számú portok kell lennie, elosztott terhelésű. 

A magas rendelkezésre ÁLLÁSÚ portok szolgáltatás van konfigurálva, amikor az előtér- és portok **0** és a protokoll **összes**. A belső terheléselosztó-erőforráshoz ezután elosztja az összes TCP és UDP-folyamatok, függetlenül attól, port száma.

## <a name="why-use-ha-ports"></a>Magas rendelkezésre ÁLLÁSÚ portok miért érdemes használni?

### <a name="nva"></a>Hálózati virtuális berendezések

Használhatja az nva-k védelme érdekében az Azure számítási feladatban a biztonsági fenyegetések többféle típusával szemben. Ezekben az esetekben az nva-k használatakor, megbízható és magas rendelkezésre állású kell lenniük, és azok kell horizontális felskálázása az igény szerinti.

Ezen célok érhet el egyszerűen NVA-példányok hozzáadásával a belső terheléselosztó háttérbeli készletéhez, és konfigurálása egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályt.

Az NVA magas rendelkezésre ÁLLÁS forgatókönyvek magas rendelkezésre ÁLLÁSÚ portok a következő előnyöket nyújtják:
- Adja meg a megfelelő példányokhoz hasonlóan a gyors feladatátvétel az állapot-mintavételei példányonként
- A horizontális felskálázás a magasabb teljesítmény biztosítása érdekében *n*– aktív példány
- Adja meg *n*– aktív és aktív-passzív forgatókönyvek
- Szükségtelenné teszi összetett megoldások, például az Apache ZooKeeper-csomópont monitorozáshoz készülékek

Az alábbi ábrán egy Központ-küllő virtuális hálózat üzembe helyezési mutat be. A küllők kényszerített bújtatás forgalmukat a központi virtuális hálózaton, és az nva-n, mielőtt a megbízható területet. Az nva-k, amelyek egy magas rendelkezésre ÁLLÁSÚ portok konfigurációval egy belső Standard terheléselosztó mögött találhatók. Minden forgalmat is feldolgozott és továbbított ennek megfelelően.

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

- Csak egyetlen hálózati adapterrel folyamat szimmetrikus NVA-forgatókönyvek esetén támogatott. Tekintse meg a leírást és a diagram [hálózati virtuális berendezések](#nva). Azonban a cél a forgatókönyv NAT is képes működni, használhatja azt, hogy a belső terheléselosztó küld a visszatérő forgalom ugyanazon az nva-t.


## <a name="next-steps"></a>További lépések

- [Magas rendelkezésre ÁLLÁSÚ portok konfigurálása belső Standard Load Balancer](load-balancer-configure-ha-ports.md)
- [Ismerje meg a Standard Load Balancer](load-balancer-standard-overview.md)
