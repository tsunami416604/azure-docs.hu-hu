---
title: Magas rendelkezésre állású portok áttekintő lapján az Azure-ban
titleSuffix: Azure Load Balancer
description: Tudnivalók a magas rendelkezésre állású portok terheléselosztási egy belső terheléselosztón.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: c6529e2585a7fca2d160d093d303afa02e6f9379
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215076"
---
# <a name="high-availability-ports-overview"></a>Magas rendelkezésre állású portok áttekintő lapján

Az Azure Standard Load Balancer segítségével összes porton a TCP és UDP-folyamatok terheléselosztás egyszerre egy belső load balancer használatakor. 

A magas rendelkezésre állású (HA) portok terheléselosztási szabálya egy belső standard Load Balancer konfigurált terheléselosztási szabály egy változata. Terheléselosztás egyetlen szabállyal egy belső Standard Load Balancer-az összes porton érkező összes TCP és UDP-folyamatok biztosításával egyszerűsítheti a terheléselosztó használatát. A terheléselosztás döntési folyamat kiszolgálónként történik. Ez a művelet a következő öt rekordos kapcsolaton alapul: forrás IP-címe, forrásport, cél IP-címe, célport és protokoll

A HA-portok terheléselosztási szabályai segítenek a kritikus forgatókönyvek, például a magas rendelkezésre állás és a virtuális hálózatokon belüli hálózati virtuális berendezések (NVA-EK) méretezése terén. A funkció segíthet is, ha nagy számú portok kell lennie, elosztott terhelésű. 

Ha az előtér-portok terheléselosztási szabályait úgy konfigurálja, hogy az előtérbeli és a háttérbeli portok **0-ra** és az **összes**protokollra legyenek beállítva. A belső terheléselosztó erőforrás ezután kiegyenlíti az összes TCP-és UDP-folyamatot, a portszámtól függetlenül.

## <a name="why-use-ha-ports"></a>Magas rendelkezésre ÁLLÁSÚ portok miért érdemes használni?

### <a name="nva"></a>Hálózati virtuális berendezések

Használhatja az nva-k védelme érdekében az Azure számítási feladatban a biztonsági fenyegetések többféle típusával szemben. Ezekben az esetekben az nva-k használatakor, megbízható és magas rendelkezésre állású kell lenniük, és azok kell horizontális felskálázása az igény szerinti.

Ezen célok érhet el egyszerűen NVA-példányok hozzáadásával a belső terheléselosztó háttérbeli készletéhez, és konfigurálása egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályt.

Az NVA magas rendelkezésre ÁLLÁS forgatókönyvek magas rendelkezésre ÁLLÁSÚ portok a következő előnyöket nyújtják:
- Adja meg a megfelelő példányokhoz hasonlóan a gyors feladatátvétel az állapot-mintavételei példányonként
- A horizontális felskálázás a magasabb teljesítmény biztosítása érdekében *n*– aktív példány
- Adja meg *n*– aktív és aktív-passzív forgatókönyvek
- Szükségtelenné teszi összetett megoldások, például az Apache ZooKeeper-csomópont monitorozáshoz készülékek

Az alábbi ábrán egy Központ-küllő virtuális hálózat üzembe helyezési mutat be. A küllők kényszerített bújtatás forgalmukat a központi virtuális hálózaton, és az nva-n, mielőtt a megbízható területet. Az nva-k, amelyek egy magas rendelkezésre ÁLLÁSÚ portok konfigurációval egy belső Standard terheléselosztó mögött találhatók. Minden forgalmat is feldolgozott és továbbított ennek megfelelően. Ha a következő ábrán látható módon van konfigurálva, akkor egy HA-portok terheléselosztási szabálya emellett a bejövő és a kimenő forgalomhoz is biztosít flow-szimmetriát.

<a node="diagram"></a>
![a sugaras virtuális hálózat NVA, HA az üzembe helyezési módban van](./media/load-balancer-ha-ports-overview/nvaha.png)

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

- HA a portok terheléselosztási szabályai csak belső standard Load Balancer esetén érhetők el.
- A csoportba foglalása az egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály és a egy nem magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály nem támogatott.
- A meglévő IP-töredékeket a HEKTÁRos portok terheléselosztási szabályai továbbítják az első csomaggal megegyező célra.  Az UDP-vagy TCP-csomagok IP-darabolása nem támogatott.
- Az IF ports terheléselosztási szabályai nem érhetők el az IPv6-hoz.
- A flow-szimmetria (elsősorban a NVA-forgatókönyvek esetében) a háttér-példány és egyetlen hálózati adapter (és egy IP-konfiguráció) esetében csak akkor támogatott, ha a fenti ábrán látható módon használja, és a HA portok terheléselosztási szabályait használja. Semmilyen más esetben nincs megadva. Ez azt jelenti, hogy két vagy több Load Balancer erőforrás és a hozzájuk tartozó szabályok független döntéseket hoznak, és soha nem koordinálják őket. Tekintse meg a leírást és a diagram [hálózati virtuális berendezések](#nva). Ha több hálózati adaptert használ, vagy a NVA egy nyilvános és belső Load Balancer között használja, a flow-szimmetria nem érhető el.  Ezt megteheti, ha a forrás NAT'ing a bejövő forgalmat a készülék IP-címére, hogy a válaszok ugyanarra a NVA érkezzenek.  Javasoljuk azonban, hogy egyetlen hálózati adaptert használjon, és használja a fenti ábrán látható hivatkozási architektúrát.


## <a name="next-steps"></a>Következő lépések

- [Magas rendelkezésre ÁLLÁSÚ portok konfigurálása belső Standard Load Balancer](load-balancer-configure-ha-ports.md)
- [Ismerje meg a Standard Load Balancer](load-balancer-standard-overview.md)
