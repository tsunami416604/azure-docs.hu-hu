---
title: Magas rendelkezésre állású portok áttekintése az Azure-ban
titleSuffix: Azure Load Balancer
description: Ismerje meg a magas rendelkezésre állású portok terheléselosztás a belső terheléselosztó.
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
ms.openlocfilehash: 5ada709350802344bfa65cce269735baa416edf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234451"
---
# <a name="high-availability-ports-overview"></a>Magas rendelkezésre állású portok – áttekintés

Az Azure Standard Load Balancer segítségével a terheléselosztást és az UDP-t egyszerre minden porton forgalmazhatja, amikor belső terheléselosztót használ. 

A magas rendelkezésre állású (HA) portok terheléselosztási szabály egy változata a terheléselosztási szabály, konfigurálva egy belső standard terheléselosztó. Egyszerűsítheti a terheléselosztó használatát, ha egyetlen szabályt biztosít a belső standard terheléselosztó összes portjára érkező összes TCP- és UDP-folyamat terheléselosztásához. A terheléselosztási döntés folyamatonként történik. Ez a művelet a következő öthangú kapcsolaton alapul: forrás IP-cím, forrásport, cél IP-cím, célport és protokoll

A HA portok terheléselosztási szabályai segítenek a kritikus forgatókönyvekben, például a virtuális hálózatokon belüli hálózati virtuális készülékek (NVA-k) magas rendelkezésre állásában és méretezésében. A szolgáltatás akkor is segíthet, ha nagy számú portnak kell terheléselosztással rendelkeznie. 

A HA portok terheléselosztási szabályai akkor vannak konfigurálva, ha az előtér- és háttérportokat **0-ra,** a protokollt pedig az **Összes**értékre állítja. A belső terheléselosztó erőforrás kiegyenlíti az összes TCP- és UDP-folyamatot, a portszámtól függetlenül.

## <a name="why-use-ha-ports"></a>Miért érdemes ha portokat használni?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Hálózati virtuális berendezések

Az NVA-k segítségével biztosíthatja az Azure-számítási feladatok at többféle biztonsági fenyegetések. Ha nva-kat használ ezekben a forgatókönyvekben, azok megbízhatónak és magas rendelkezésre állásúnak kell lenniük, és az igény szerint horizontális felskálázást kell végezniük.

Ezeket a célokat egyszerűen elérheti, ha NVA-példányokat ad hozzá a belső terheléselosztó háttérkészletéhez, és konfigurál egy HA port terheléselosztó szabályt.

NVA HA-forgatókönyvek esetén a HA portok a következő előnyöket kínálják:
- Gyors feladatátvétel biztosítása a kifogástalan állapotú példányok számára, példányonkénti állapotpróbákkal
- Nagyobb teljesítmény biztosítása az *n-aktív*példányokra való kibővített verzióval
- *N*-aktív és aktív-passzív forgatókönyvek biztosítása
- Nincs szükség olyan összetett megoldásokra, mint például az Apache ZooKeeper csomópontok a készülékek figyeléséhez

Az alábbi ábrán egy küllős virtuális hálózati központi telepítés látható. A küllők a hub virtuális hálózatra és az NVA-n keresztül kényszerítik a forgalmat, mielőtt elhagyják a megbízható helyet. Az NVA-k egy belső standard terheléselosztó mögött vannak, ha portkonfigurációval. Minden forgalom ennek megfelelően feldolgozható és továbbítható. Ha a következő ábrán láthatóként van konfigurálva, a HA ports terheléselosztási szabály emellett folyamatszimmetriát biztosít a bejövő és kimenő forgalom számára.

<a node="diagram"></a>
![A küllős virtuális hálózat diagramja, HA módban telepített NVA-kkal](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Ha nva-kat használ, erősítse meg a szolgáltatóikkal, hogyan lehet a legjobban használni a HA-portokat, és hogy megtudja, mely forgatókönyvek támogatottak.

### <a name="load-balancing-large-numbers-of-ports"></a>Nagy számú port terheléselosztása

Ha portokat is használhat olyan alkalmazásokhoz, amelyek nagy számú port terheléselosztását igénylik. Ezeket a forgatókönyveket leegyszerűsítheti egy belső [standard terheléselosztó](load-balancer-standard-overview.md) HA-portokkal. Egyetlen terheléselosztási szabály több egyedi terheléselosztási szabályt vált fel, mindegyik porthoz egyet.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A HA portok funkció az összes globális Azure-régióban elérhető.

## <a name="supported-configurations"></a>Támogatott konfigurációk

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Egyetlen, nem lebegő IP-cím (nem direct server return) HA-port konfiguráció egy belső standard terheléselosztón

Ez a konfiguráció egy alapvető HA portkonfiguráció. Egyetlen előtér-IP-címre konfigurálhat egy HA-port terheléselosztási szabályt az alábbi módon:
1. A Standard Load Balancer konfigurálása közben jelölje be a **HA portok** jelölőnégyzetet a Terheléselosztó szabály konfigurációjában.
2. **Lebegő IP esetén**válassza **a Letiltva**lehetőséget.

Ez a konfiguráció nem teszi lehetővé az aktuális terheléselosztó erőforrás on-ra vonatkozó más terheléselosztási szabály konfigurációját. Azt is lehetővé teszi, hogy nincs más belső terheléselosztó erőforrás-konfiguráció a megadott háttérpéldányok adott készletéhez.

Azonban a HA-portszabály mellett konfigurálhat egy nyilvános standard terheléselosztót a háttérpéldányokhoz.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Egyetlen, lebegő IP-cím (Direct Server Return) HA-port konfiguráció egy belső standard terheléselosztón

Hasonlóképpen beállíthatja a terheléselosztót úgy, hogy a **haporttal** rendelkező terheléselosztási szabályt használja egyetlen előtérrel, ha a **lebegő IP-címet** **engedélyezve állítja.** 

Ezzel a konfigurációval további lebegő IP-terheléselosztási szabályokat és/vagy nyilvános terheléselosztót adhat hozzá. Azonban nem használhat nem lebegő IP-, HA-portok terheléselosztási konfigurációt ezen a konfiguráción felül.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Több HA-port konfiguráció egy belső standard terheléselosztón

Ha a forgatókönyv ben egynél több HA-port előtér-kiszolgálót kell konfigurálnia ugyanahhoz a háttérkészlethez, a következőket teheti: 
- Konfiguráljon egynél több előtér-privát IP-címet egyetlen belső standard terheléselosztó erőforráshoz.
- Konfiguráljon több terheléselosztási szabályt, ahol minden szabályhoz egyetlen egyedi előtér-IP-cím van kiválasztva.
- Válassza ki a **HA portok** beállítást, majd állítsa **a lebegő IP-címet** **engedélyezve** az összes terheléselosztási szabályhoz.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Belső terheléselosztó HA portokkal és nyilvános terheléselosztó ugyanazon a háttérpéldányon

*Egyetlen* nyilvános standard terheléselosztó erőforrást konfigurálhat a háttérerőforrásokhoz, valamint egyetlen belső standard terheléselosztót HA-portokkal.

>[!NOTE]
>Ez a funkció jelenleg elérhető az Azure Resource Manager-sablonokon keresztül, de nem érhető el az Azure Portalon keresztül.

## <a name="limitations"></a>Korlátozások

- A HA portok terheléselosztási szabályai csak a belső standard terheléselosztóhoz érhetők el.
- A HA-portok terheléselosztási szabályának és a nem HA-portok terheléselosztási szabályának kombinálása nem támogatott.
- A meglévő IP-töredékeket a HA Ports terheléselosztási szabályai továbbítják ugyanarra a célhelyre, mint az első csomag.  UdP- vagy TCP-csomag IP-töredezettsége nem támogatott.
- A folyamatszimmetria (elsősorban nva-forgatókönyvek esetén) csak akkor támogatott háttérpéldányés egyetlen hálózati adapter (és egyetlen IP-konfiguráció) alkalmazással, ha a fenti ábrán látható módon és a HA-portok terheléselosztási szabályainak használatával használható. Más forgatókönyv nem áll rendelkezésre. Ez azt jelenti, hogy két vagy több terheléselosztó erőforrás és azok szabályai független döntéseket hoznak, és soha nem koordináltak. Lásd a hálózati virtuális készülékek leírását és [diagramját.](#nva) Ha több hálózati adaptert használ, vagy az NVA-t egy nyilvános és belső terheléselosztó között szendvicsezi, az áramlási szimmetria nem érhető el.  Lehet, hogy a munka körül ezt a forrás NAT'ing a beáramló áramlás az IP a készülék, hogy a válaszok érkeznek az azonos NVA.  Azonban azt javasoljuk, hogy egyetlen hálózati adapter t, és a fenti ábrán látható referenciaarchitektúra használatát.


## <a name="next-steps"></a>További lépések

- [HA-portok konfigurálása belső standard terheléselosztón](load-balancer-configure-ha-ports.md)
- [További információ a standard terheléselosztóról](load-balancer-standard-overview.md)
