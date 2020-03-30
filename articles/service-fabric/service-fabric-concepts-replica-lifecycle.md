---
title: Replikák és példányok az Azure Service Fabricben
description: Ismerje meg a replikák és példányok a Service Fabric, beleértve azok életciklusának és függvényeinek áttekintését.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258563"
---
# <a name="replicas-and-instances"></a>Replikák és példányok 
Ez a cikk áttekintést nyújt az állapotalapú szolgáltatások és állapotmentes szolgáltatások példányainak életciklusáról.

## <a name="instances-of-stateless-services"></a>Állapotmentes szolgáltatások példányai
Az állapotmentes szolgáltatás példánya a fürt egyik csomópontján futó szolgáltatáslogika másolata. Egy partíción belüli példányt a **InstanceId**egyedileg azonosít. Egy példány életciklusa a következő ábrán látható:

![Példány életciklusa](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Miután a fürterőforrás-kezelő meghatározza a példány elhelyezését, megadja ezt az életciklus-állapotot. A példány a csomóponton indul. Az alkalmazásgazda elindult, a példány létrejön, majd megnyílik. Az indítás befejezése után a példány átvált a kész állapotba. 

Ha az alkalmazás gazdagép vagy csomópont a példány összeomlik, átkerül az eldobott állapotba.

### <a name="ready-rd"></a>Kész (RD)
A kész állapotban a példány működik a csomóponton. Ha ez a példány egy megbízható szolgáltatás, **runasync** van meghívva. 

Ha az alkalmazás gazdagép vagy csomópont a példány összeomlik, átkerül az eldobott állapotba.

### <a name="closing-cl"></a>Zárás (CL)
A záró állapotban az Azure Service Fabric a példány leállítása ezen a csomóponton. Ennek a leállásnak számos oka lehet – például egy alkalmazásfrissítés, terheléselosztás vagy a törölt szolgáltatás. A leállítás befejezése után az eldobott állapotba vált.

### <a name="dropped-dd"></a>Eldobva (DD)
Az eldobott állapotban a példány már nem fut a csomóponton. Ezen a ponton a Service Fabric fenntartja a metaadatokat erről a példányról, amely végül törlődik is.

> [!NOTE]
> A **ForceRemove (Szerkesztés remove)** beállítással bármely állapotból `Remove-ServiceFabricReplica`az eldobott állapotba lehet áttérni.
>

## <a name="replicas-of-stateful-services"></a>Állapotalapú szolgáltatások másolatai
Az állapotalapú szolgáltatás replikája a fürt egyik csomópontján futó szolgáltatáslogika másolata. Ezenkívül a replika megőrzi a szolgáltatás állapotának másolatát. Két kapcsolódó fogalom ismerteti az állapotalapú replikák életciklusát és viselkedését:
- Replika életciklusa
- Replika-szerepkör

A következő vita a tartósan állapotalapú szolgáltatásokat ismerteti. Illékony (vagy a memóriában lévő) állapotalapú szolgáltatások esetén a lefelé és az eldobott állapotok egyenértékűek.

![Replika életciklusa](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Az InBuild-kópia olyan replika, amely et a kópiakészlethez való csatlakozásra hoztak létre vagy készítettek elő. A replikaszerepkörtől függően az IB eltérő szemantikával rendelkezik. 

Ha az alkalmazásgazda vagy az InBuild replika csomópontja összeomlik, lefelé változik.

   - **Elsődleges InBuild replikák**: Elsődleges InBuild az első replikák egy partíciót. Ez a replika általában a partíció létrehozásakor történik. Elsődleges InBuild replikák is akkor merülnek fel, amikor a partíció összes replikák újraindul, vagy eldobják.

   - **IdleSecondary InBuild replikák:** Ezek vagy a fürt erőforrás-kezelő által létrehozott új replikák, vagy a meglévő replikák, amelyek lementek, és hozzá kell adni a készletbe. Ezeket a replikákat az elsődleges határozza meg vagy hozza létre, mielőtt activesecondary-ként csatlakozhatnának a replikakészlethez, és részt vehetnének a kvórum-műveletek nyugtázásában.

   - **ActiveSecondary InBuild replikák:** Ez az állapot figyelhető meg néhány lekérdezések. Ez egy optimalizálás, ahol a replikakészlet nem változik, de egy replika kell építeni. Maga a replika követi a normál állapotgép-átmeneteket (a replikaszerepkörökről szóló szakaszban leírtak szerint).

### <a name="ready-rd"></a>Kész (RD)
A Ready replika olyan replika, amely részt vesz a műveletek replikációjában és kvórumnyugtázásában. A kész állapot elsődleges és aktív másodlagos replikákra alkalmazható.

Ha az alkalmazásgazda vagy a kész replika csomópontja összeomlik, lefelé kerül.

### <a name="closing-cl"></a>Zárás (CL)
A replika a következő esetekben lép be a záró állapotba:

- **A replika kódjának leállítása:** A Service Fabric előfordulhat, hogy le kell állítania a kópia futó kódját. Ennek a leállásnak több oka is lehet. Például előfordulhat egy alkalmazás, háló vagy infrastruktúra-frissítés, vagy a replika által jelentett hiba miatt. Amikor a replika bezárása befejeződik, a replika áttűnések lefelé állapotba. A lemezen tárolt replikához társított megőrzött állapot nem törlődik.

- **A replika eltávolítása a fürtből:** A Service Fabric előfordulhat, hogy el kell távolítania a megőrzött állapotot, és le kell állítania a kópia futó kódját. Ennek a leállásnak több oka is lehet, például a terheléselosztásnak.

### <a name="dropped-dd"></a>Eldobva (DD)
Az eldobott állapotban a példány már nem fut a csomóponton. A csomóponton sem maradt állapot. Ezen a ponton a Service Fabric fenntartja a metaadatokat erről a példányról, amely végül törlődik is.

### <a name="down-d"></a>Le (D)
A lefelé állapotban a replikakód nem fut, de a replikához a replikához továbbra is megőrzött állapot létezik az adott csomóponton. A replika számos okból lemaradhat – például a csomópont le, a replikakód összeomlása, az alkalmazásfrissítés vagy a replikahibák.

A szolgáltatásfabric szükség szerint megnyit egy lefelé replikát, például amikor a frissítés befejeződik a csomóponton.

A replika szerepkör nem releváns a lefelé állapotban.

### <a name="opening-op"></a>Nyitás (OP)
Egy lefelé replika belép a nyitó állapotba, amikor a Service Fabric kell, hogy a replika újra. Ez az állapot például az alkalmazás kódfrissítésének befejezése után lehet egy csomóponton. 

Ha az alkalmazásgazda vagy a nyitó replika csomópontja összeomlik, lefelé kerül.

A replika szerepkör nem releváns a nyitó állapotban.

### <a name="standby-sb"></a>Készenlét (SB)
A standby replika egy megőrzött szolgáltatás replikája, amely leállt, majd meglett nyitva. Ezt a replikát a Service Fabric is használja, ha egy másik replikakészletet kell hozzáadnia (mert a replika már rendelkezik az állapot egy részével, és a létrehozási folyamat gyorsabb). A StandByReplicaKeepDuration lejárta után a készenléti replika elvetésre kerül.

Ha az alkalmazásgazda vagy a készenléti replika csomópontja összeomlik, lefelé változik.

A replika szerepkör nem releváns a készenléti állapotban.

> [!NOTE]
> Minden olyan replika, amely nem le, vagy leesett kell *tekinteni,* hogy fel .
>

> [!NOTE]
> A **ForceRemove** beállítással bármely állapotból az eldobott állapotba `Remove-ServiceFabricReplica`lehet áttérni.
>

## <a name="replica-role"></a>Replika-szerepkör 
A kópia szerepköre határozza meg a kópiakészlet ben betöltött funkcióját:

- **Elsődleges (P)**: Van egy elsődleges a replikakészletben, amely felelős az olvasási és írási műveletek végrehajtásáért. 
- **ActiveSecondary (S)**: Ezek olyan replikák, amelyek állapotfrissítéseket kapnak az elsődleges, alkalmazza őket, majd küldje vissza nyugták. A replikakészletben több aktív másodlagos állapot van. Ezeknek az aktív másodlagos állapotoknak a száma határozza meg, hogy a szolgáltatás hány hibát képes kezelni.
- **IdleSecondary (I)**: Ezek a replikák az elsődleges által épül. Az elsődleges állapotot kapják, mielőtt aktív másodlagosra léptethetnék őket. 
- **Nincs (N)**: Ezek a replikák nem rendelkeznek a replikakészletben.
- **Ismeretlen (U)**: Ez a kópia kezdeti szerepköre, mielőtt bármilyen **ChangeRole** API-hívást kapna a Service Fabric-ből.

Az alábbi ábra bemutatja a replikaszerepkör-átmeneteket és néhány példaesetet, amelyben előfordulhatnak:

![Replika-szerepkör](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Új elsődleges replika létrehozása.
- U -> I: Új tétlen replika létrehozása.
- U -> N: Készenléti replika törlése.
- I -> S: Az aktív másodlagos tétlenség támogatása, hogy annak elismerése hozzájáruljon a határozatképességhez.
- I -> P: Előmozdítása a tétlen másodlagos elsődleges. Ez akkor fordulhat elő speciális újrakonfigurálások, ha az alapjárati másodlagos a megfelelő jelölt, hogy elsődleges.
- I -> N: Az aljárati másodlagos replika törlése.
- S -> P: Az elsődleges aktív másodlagos támogatása. Ennek oka lehet a fürterőforrás-kezelő által kezdeményezett elsődleges vagy elsődleges mozgás feladatátvétele. Például lehet, hogy egy alkalmazás frissítése vagy terheléselosztás válaszul.
- S -> N: Az aktív másodlagos replika törlése.
- P -> S: Az elsődleges replika lefokozása. Ennek oka lehet a fürterőforrás-kezelő által kezdeményezett elsődleges mozgás. Például lehet, hogy egy alkalmazás frissítése vagy terheléselosztás válaszul.
- P -> N: Az elsődleges replika törlése.

> [!NOTE]
> A magasabb szintű programozási modellek, például [a Reliable Actors](service-fabric-reliable-actors-introduction.md) és a Reliable [Services,](service-fabric-reliable-services-introduction.md)elrejti a replika szerepkörök fogalmát a fejlesztő től. A színészekben a szerep fogalma szükségtelen. A szolgáltatásokban a legtöbb esetben nagyrészt leegyszerűsödik.
>

## <a name="next-steps"></a>További lépések
A Service Fabric-fogalmakról az alábbi cikkben olvashat bővebben:

[A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)

