---
title: Replikák és az Azure Service Fabric-példány |} Microsoft Docs
description: Replikák és példányok--a funkció és életciklusának megismerése
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: 7f8638365b40395a5dd82457c40e5c15209ba1a7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="replicas-and-instances"></a>Replikák és példányok 
Ez a cikk áttekintést nyújt az életciklus replikák állapotalapú szolgáltatások és állapot nélküli services példányát.

## <a name="instances-of-stateless-services"></a>Az állapotmentes szolgáltatások példányok
Az állapotmentes szolgáltatások egy példány a szolgáltatás logika, amely a fürt a csomópontok egyikét futtatja egy példányát. A partíción belül példánya egyedileg azonosítja a **InstanceId**. Az egyik példány életciklus van modellezve, az alábbi diagram szemlélteti:

![Példány életciklusa](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>Található InBuild (IB)
Miután a fürt erőforrás-kezelő egy példány elhelyezés határozza meg, az életciklus állapotba lép. A példány el van indítva a csomópont. Alkalmazásgazda elindul, a példány létrejött, és majd. Az indítási befejezése után a példány átkerül a kész állapotba. 

Ha a gazda vagy az adott példány csomópont összeomlik, átkerül az eldobott állapotra.

### <a name="ready-rd"></a>Készültség (RD)
A kész állapotú példány működik, és a csomóponton. Ha ezt a példányt egy megbízható szolgáltatás **RunAsync** hivatkoznak. 

Ha a gazda vagy az adott példány csomópont összeomlik, átkerül az eldobott állapotra.

### <a name="closing-cl"></a>Záró (CL)
Lezárt állapotban Azure Service Fabric ezen a csomóponton a példány leállítása folyamatban van. A leállítás oka lehet számos oka – például az alkalmazásfrissítés, terheléselosztást vagy a szolgáltatás törlése folyamatban. Leállítás befejezése után átkerül az eldobott állapotra.

### <a name="dropped-dd"></a>Az eldobott (nn)
Az eldobott állapotában a példány már nem fut. a csomóponton. Ezen a ponton a Service Fabric ebben a példában a rendszer végül törli a metaadatait tárolja.

> [!NOTE]
> Már lehetséges való áttéréssel eldobott állapotára bármely állapotból használatával a **ForceRemove** beállítást `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Replikák állapotalapú szolgáltatások
Az állapotalapú szolgáltatás egy replikája a fürt a csomópontok egyikén futó szolgáltatás logika egy példányát. Továbbá a replika fenntart egy adott szolgáltatás állapotát. Két kapcsolódó fogalmak életciklusának és állapot-nyilvántartó replikák viselkedését mutatják be:
- A replika életciklusa
- A replika szerepkör

A következő vitafórum a megőrzött állapot-nyilvántartó szolgáltatások ismerteti. "Volatile" (vagy a memóriában) állapotalapú szolgáltatások esetén a le, és az eldobott állapotok egyenértékűek.

![A replika életciklusa](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>Található InBuild (IB)
Található InBuild replikájának létrehozása vagy csatlakoztatása a replikakészlethez készített replika. Attól függően, hogy a replika szerepkör a IB szemantikájú különböző. 

Ha az alkalmazást vagy a csomópont egy található InBuild másodpéldány összeomlik, átmenetek lefelé állapotát.

   - **Elsődleges található InBuild replikák**: elsődleges található InBuild egy partíció első replikáit. Ez a másodpéldány általában akkor történik, ha a partíció létrehozása folyamatban van. Elsődleges található InBuild replikák is merülhetnek fel, ha egy partíció összes replika újraindítása vagy van dobva.

   - **IdleSecondary található InBuild replikák**: ezek vagy a fürt-kezelő által létrehozott új replikákat, vagy meglévő replikák állt le, és hozzá kell adni a készlet vissza. Ezekre a replikákra rendezés vagy az elsődleges létrehozva ahhoz, hogy csatlakozzon a replikakészlethez ActiveSecondary, és kvórum nyugtázása műveletek részt.

   - **ActiveSecondary található InBuild replikák**: Ebben az állapotban lévő egyes lekérdezések követi. Az optimalizálás, amelyen a replika meg nem változik, de a replika kell kialakítani, akkor. A replika magát a következő a gép szokásos Állapotváltások (a szakaszban leírtak a replika szerepköre).

### <a name="ready-rd"></a>Készültség (RD)
Készen áll a replika egy replikát, amely részt vesz műveletek replikációs és kvórum nyugtázása. A kész állapot nem elsődleges és aktív másodlagos replikák alkalmazandó.

Ha az alkalmazást vagy a csomópont készen áll a replika összeomlik, átmenetek lefelé állapotát.

### <a name="closing-cl"></a>Záró (CL)
A replika a záró állapotba kerül, a következő esetekben:

- **A kód a replika leállítása**: a Service Fabric módosítania kell a replika kódfuttatásra le kell állítania. A Leállítás számos oka lehet. Ez például akkor fordulhat elő, egy alkalmazás, hálóhoz vagy infrastruktúrafrissítése miatt, vagy a replika által jelentett hiba miatt. A replika befejeződik zárja be, amikor a replika átmenetek lefelé állapotát. A megőrzött állapot, a lemezen tárolt replika társított nem törlődnek.

- **A replika eltávolítása a fürtről**: a Service Fabric módosítania kell a megőrzött állapot eltávolítása, és állítsa le a futó kód replika. A leállítás okait, számos, például hálózati terheléselosztást lehet.

### <a name="dropped-dd"></a>Az eldobott (nn)
Az eldobott állapotában a példány már nem fut. a csomóponton. A csomópontot a bal oldali állapot nélküli is van. Ezen a ponton a Service Fabric ebben a példában a rendszer végül törli a metaadatait tárolja.

### <a name="down-d"></a>Lefelé (D)
Alsó állapotban a replika kód nem fut, de a megőrzött állapot, hogy a replika létezik ezen a csomóponton. A replika le számos oka lehet – például a csomópont alatt, a replika kódot, az alkalmazásfrissítés vagy replika hibák crash.

Lefelé replika már meg van nyitva a Service Fabric szükséges, mint például a frissítés befejezése után a csomóponton.

A replika szerepköre nem megfelelő alsó állapotban.

### <a name="opening-op"></a>A nyitó (m)
Lefelé replika a nyitó állapotba kerül, ha a Service Fabric kell elindítani a replika vissza újra. Ebben az állapotban lehet például egy kódot frissítés alkalmazásához a csomópont befejeződése után. 

Ha az alkalmazást vagy a csomópont egy nyitó másodpéldány összeomlik, átmenetek lefelé állapotát.

A replika szerepkör nincs megfelelő állapotban a megnyitásakor.

### <a name="standby-sb"></a>A készenléti (SB)
A készenléti replikája egy replikát készít egy megőrzött szolgáltatás, amely csökkent, és ezután nyitották meg. Ennek a replikának szüksége lehet a Service Fabric, ha a másik replika hozzáadása (mert a replika már rendelkezik egy részét az állapot és a létrehozási folyamat gyorsabb). A StandByReplicaKeepDuration lejárta után a rendszer törli a készenléti állapotban lévő replikát.

Ha az alkalmazást vagy a csomópont egy készenléti replika összeomlik, átmenetek lefelé állapotát.

A replika szerepköre nem megfelelő a készenléti állapotban.

> [!NOTE]
> Bármely replika, amely nem nem működik, vagy kihagyott tekinthető *be*.
>

> [!NOTE]
> Már lehetséges való áttéréssel eldobott állapotára bármely állapotból használatával a **ForceRemove** beállítást `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>A replika szerepkör 
A replika szerepkörét az határozza meg a replika funkciójához:

- **Elsődleges (P)**: van egy elsődleges a replika, amely hajt végre az olvasási és írási műveletet. 
- **ActiveSecondary (S)**: ezek a állapot frissítések fogadása az elsődleges, őket, és elküldheti-e vissza a nyugtázás a replikákat. A replikakészlet több aktív másodlagos adatbázisok szerepelnek. Ezek aktív másodlagos adatbázisok száma meghatározza, hogy kezelni tud a szolgáltatás a hibák száma.
- **(I) IdleSecondary**: ezekre a replikákra az elsődleges által épített alatt. Kapják állapota az elsődleges, mielőtt azok aktív másodlagos tartományvezérlővé. 
- **Nincs (N)**: ezekre a replikákra a replika nem rendelkezik a felelős.
- **Ismeretlen (U)**: Ez az a kezdeti replika szerepkörét az egy, mielőtt bármelyik kap **ChangeRole** API-hívás a Service Fabric.

Az alábbi ábrán látható, a replika szerepkör átmenetek és néhány példát, amelyek akkor fordulhatnak elő:

![A replika szerepkör](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> új elsődleges replika P: létrehozását.
- U -> új inaktív replika I: létrehozását.
- U -> N: készenléti állapotban lévő replika törlését.
- I -> %s előléptetés aktív másodlagos tétlen másodlagos, hogy a nyugtázásokhoz közre kvórum felé.
- I -> a tétlen másodlagos elsődleges P: előléptetése. Ez akkor fordulhat elő különleges újrakonfigurálás alatt a tétlen másodlagos elsődleges kell a megfelelő jelölt esetén.
- I -> N: a tétlen másodlagos replika törlését.
- S -> az elsődleges aktív másodlagos P: előléptetése. Az elsődleges vagy a fürt-kezelő által kezdeményezett egy elsődleges adatátviteli valószínűleg. Például lehet egy alkalmazás frissítése vagy a terheléselosztás adott válaszként.
- S -> N: az aktív másodlagos replika törlését.
- P -> %s lefokozás az elsődleges replika. Ez okozhatja, hogy a fürt-kezelő által kezdeményezett egy elsődleges adatátviteli. Például lehet egy alkalmazás frissítése vagy a terheléselosztás adott válaszként.
- P -> N: az elsődleges replika törlését.

> [!NOTE]
> Mint a magasabb szintű programozási modellek [Reliable Actors](service-fabric-reliable-actors-introduction.md) és [Reliable Services](service-fabric-reliable-services-introduction.md), a fejlesztőtől származó replika szerepkörök koncepciójának elrejtése. Szereplője a szerepkör fogalmát nem szükséges. A szolgáltatások, nagy mértékben egyszerűsítve a legtöbb forgatókönyvhöz.
>

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmakat további információkért tekintse meg a következő cikkben:

[A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)

