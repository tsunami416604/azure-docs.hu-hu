---
title: Replikák és példányok az Azure Service Fabricben |} A Microsoft Docs
description: Replikák és példányok--a funkció és életciklusának ismertetése
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882414"
---
# <a name="replicas-and-instances"></a>Replikák és példányok 
Ez a cikk áttekintést nyújt az életciklus állapotalapú szolgáltatások és állapotmentes szolgáltatások példányainak replikával.

## <a name="instances-of-stateless-services"></a>Az állapotmentes szolgáltatások példányok
Állapotmentes szolgáltatás egy példányának a szolgáltatás logika, amely a fürt csomópontjait futtat egy példányát. Egy példány egy partíción belül egyedileg azonosítja a **InstanceId**. Az életciklus-példány van modellezve a következő diagram mutatja:

![Példány életciklusa](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>Található InBuild (IB)
A fürterőforrás-kezelő egy példány elhelyezés határozza meg, miután azt a életciklus állapotba lép. A példány a csomópont elindult. Az alkalmazásgazda elindult, a példány létrehozott, majd megnyitni. Az indítás befejezése után a példány átkerül a kész állapotú. 

Ha az alkalmazás gazdagép vagy a csomópont a példány leáll, átirányítja az eldobott állapotra.

### <a name="ready-rd"></a>Kész (RD)
A kész állapotú a példány működik-e a csomóponton. Ha ezt a példányt egy megbízható szolgáltatás **RunAsync** meghívása. 

Ha az alkalmazás gazdagép vagy a csomópont a példány leáll, átirányítja az eldobott állapotra.

### <a name="closing-cl"></a>Záró (CL)
Lezárt állapotban az Azure Service Fabric ezen a csomóponton a példány leállítása folyamatban van. A leállítás oka lehet számos oka lehet – például egy alkalmazás frissítése, terheléselosztási vagy a szolgáltatás törlése folyamatban van. Leállítás befejezése után átirányítja az eldobott állapotra.

### <a name="dropped-dd"></a>Az eldobott (nn)
Az eldobott állapotban a példány már nem fut a csomóponton. Ezen a ponton a Service Fabric megőrzi a metaadatokat, ez a példány, amely végül szintén törölve.

> [!NOTE]
> Lehetőség arra átmenet bármely állapotból az eldobott állapotra használatával a **ForceRemove** beállítást `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Az állapotalapú szolgáltatások replikák
Az állapotalapú szolgáltatások replikája a fürt a csomópontok egyikén futó szolgáltatás logika egy példányát. Emellett a replika megőrzi a szolgáltatás állapotát. Két kapcsolódó fogalom írja le, életciklusát és állapot-nyilvántartó replikák viselkedését:
- Replika életciklusa
- Replika szerepkör

A következő vitafórum megőrzött állapot-nyilvántartó-szolgáltatásokat részletezi. Az ideiglenes (vagy a memóriában) állapotalapú szolgáltatások esetében a le, és az eldobott állapot a következő egyenértékű.

![Replika életciklusa](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>Található InBuild (IB)
Található InBuild replikájának létrehozott vagy való csatlakozáshoz a replikakészlethez készített replika. A replika szerepkörtől függően a IB különböző szemantikával rendelkezik. 

Ha összeomlik a gazda, vagy egy található InBuild replika a csomópont, átirányítja az állapota.

   - **Elsődleges található InBuild replikák**: Elsődleges található InBuild az első partíció replikáit. Ez a másodpéldány általában akkor fordul elő, ha a partíció létrehozása folyamatban van. Elsődleges található InBuild replikák is merülnek fel, ha egy partíció összes replika indítsa újra, vagy a rendszer eltávolítja.

   - **IdleSecondary található InBuild replikák**: Ezek a fürt Resource Manager által létrehozott új replikákat, vagy a meglévő replikákat, amelyek a csökkent, és hozzá kell adni a készlet be újra. Ezek a replikák áttöltésekor vagy az elsődleges által készített, csatlakozzon a replikakészlethez ActiveSecondary, és részt venni a kvórum nyugtázása a műveletek előtt.

   - **ActiveSecondary található InBuild replikák**: Ebben az állapotban az egyes lekérdezések valósul meg. Fontos, hogy egy optimalizálási, ahol a replikakészlet nem módosul, de egy replikának továbbítania kell építeni. A replika magát a gép szokásos állapotváltásra követi, (ahogyan az a szakasz a replika szerepköre).

### <a name="ready-rd"></a>Kész (RD)
Kész replika egy replikát, amely részt vesz a replikáció és a kvórum nyugtázása műveleteket. Elsődleges és aktív másodlagos replikákra akkor üzemkész állapotba kerül.

Ha az alkalmazás gazdagép- vagy kész replika a csomópont leáll, akkor átkerül állapota.

### <a name="closing-cl"></a>Záró (CL)
A replika záró állapotba lép, a következő esetekben:

- **A kód a replika leáll**: A Service Fabric állítsa le a futó kód replika lehet szükség. Előfordulhat, hogy a leállás ennek számos oka lehet. Ez például akkor fordulhat elő, egy alkalmazást, a fabric vagy az infrastruktúra frissítése miatt, vagy a replika által jelentett hiba miatt. A replika végeztével zárja be, amikor a replika állapota a értékre vált. Ez a lemezen tárolt replika társított a megőrzött állapot nem törlődnek.

- **A replika eltávolítása a fürtből**: Távolítsa el a megőrzött állapot, és állítsa le a futó kód replika szükség lehet a Service Fabric. Előfordulhat, hogy a leállás lehetséges okai például terheléselosztási.

### <a name="dropped-dd"></a>Az eldobott (nn)
Az eldobott állapotban a példány már nem fut a csomóponton. Nincs állapot, a csomóponton left is van. Ezen a ponton a Service Fabric megőrzi a metaadatokat, ez a példány, amely végül szintén törölve.

### <a name="down-d"></a>Lefelé (D)
A állapota a replika kódot nem fut, de a replika a megőrzött állapot létezik ezen a csomóponton. A replika leállása ennek számos oka lehet – például a csomópont alatt, a replika kód, egy alkalmazás frissítése vagy replika hibák összeomlás.

Lefelé replika nyitja meg a Service Fabric szükséges, mint például a csomóponton a frissítés befejeztével.

A replika szerepkör nem szerepel megfelelő az állapota.

### <a name="opening-op"></a>Nyitó (művelet)
Lefelé replika nyitó állapotba lép, amikor a Service Fabric kell újra viszi, készítsen biztonsági másolatot a replikát. Ebben az állapotban lehet például egy kód a frissítést az alkalmazás egy csomóponton befejeződése után. 

Ha összeomlik a gazda és a egy nyitó replika a csomópont, akkor átkerül állapota.

A replika szerepkör nem szerepel megfelelő nyitó állapotát.

### <a name="standby-sb"></a>A készenléti (SB)
Készenléti replika csökkent, és ezután nyitotta meg megőrzött szolgáltatás replikáját. Ez a másodpéldány lehet a Service Fabric, ha egy másik replikára hozzáadása (mert a replika már rendelkezik bizonyos részének állapotát, és a kiépítési folyamat éppen gyorsabb). A StandByReplicaKeepDuration lejárata után a rendszer elveti a készenléti replika.

Ha az alkalmazás gazdagép- vagy készenléti replika a csomópont leáll, átirányítja az állapota.

A replika szerepkör nem szerepel megfelelő a készenléti állapotot.

> [!NOTE]
> Bármely replika, amely le nem módosítható és nem tekinthető *mentése*.
>

> [!NOTE]
> Lehetőség arra átmenet bármely állapotból az eldobott állapotra használatával a **ForceRemove** beállítást `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Replika szerepkör 
A replika szerepkör meghatározza, hogy a függvény a replika:

- **Elsődleges (P)**: Van egy elsődleges hajtják végre, a replika olvasási és írási műveletek. 
- **(S) ActiveSecondary**: Ezek a állapot frissítések fogadása az elsődleges, alkalmazza őket, és küldje vissza a nyugtázás a replikákat. A replikakészlet nincsenek több aktív másodlagos példány hozható létre. Ezek aktív másodlagos adatbázisok száma határozza meg, a hibák száma a szolgáltatás képes kezelni.
- **(I) IdleSecondary**: Ezek a replikák szerint az elsődleges beépített folyamatban van. Ezek azért küldtük Önnek, állapota az elsődleges kiszolgálóról, mielőtt azok előléptetése aktív másodlagos. 
- **Egyik sem (N)**: Ezeket a replikákat a replika nem rendelkezik a felelős.
- **Ismeretlen (U)**: Ez a kezdeti replika szerepköre, mielőtt megkap minden **ChangeRole** API-hívás a Service Fabric.

A következő ábra szemlélteti a replika szerepkör átmenetek és néhány példa forgatókönyvet, amelyben fordulhat:

![Replika szerepkör](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U-P: &GT; Egy új elsődleges replika létrehozása.
- U -> I: Egy új inaktív replika létrehozása.
- U-N: &GT; Készenléti replika törlését.
- I -> S: Az üresjárati előléptetése másodlagos aktív másodlagos úgy, hogy a nyugtázás a közreműködés kvórum felé.
- E-P: &GT; A tétlen másodlagos elsődleges támogatása. Ez akkor fordulhat elő a speciális reconfigurations a tétlen másodlagos esetén a megfelelő jelölt elsődlegessé.
- E-N: &GT; A tétlen másodlagos replika törlését.
- S-P: &GT; Az aktív másodlagos elsődleges támogatása. Ez lehet az elsődleges és a egy elsődleges áthelyezése a fürt Resource Manager által kezdeményezett feladatátvétele miatt. Például lehet egy alkalmazás frissítése vagy a terheléselosztás adott válaszként.
- S-N: &GT; Az aktív másodlagos replika törlését.
- P -> S: Az elsődleges replika lefokozása. Ez lehet a fürt Resource Manager által kezdeményezett egy elsődleges mozgása miatt. Például lehet egy alkalmazás frissítése vagy a terheléselosztás adott válaszként.
- P-N: &GT; Az elsődleges replika törlését.

> [!NOTE]
> A magasabb szintű programozási modellek, mint például [Reliable Actors](service-fabric-reliable-actors-introduction.md) és [Reliable Services](service-fabric-reliable-services-introduction.md), a replika szerepkörök a fejlesztőtől származó fogalmát elrejtése. Actors a szerepkör fogalma nem szükséges. A szolgáltatások, nagymértékben egyszerűsödött, a legtöbb forgatókönyvhöz.
>

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmakról további információkért tekintse meg a következő cikket:

[A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)

