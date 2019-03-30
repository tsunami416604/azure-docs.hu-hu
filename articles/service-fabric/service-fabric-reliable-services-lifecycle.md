---
title: Az Azure Service Fabric Reliable Services életciklusa – áttekintés |} A Microsoft Docs
description: További információ a különböző életciklussal kapcsolatos események, a Service Fabric Reliable Services
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: vturecek;
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ebc7aec63b34630b606178aa17e2ae7fdd0fc87f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669606"
---
# <a name="reliable-services-lifecycle-overview"></a>A Reliable Services-életciklus áttekintése
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

Ha szeretne áttérni az Azure Service Fabric Reliable Services életciklusokkal kapcsolatos, a legfontosabb életciklusának alapjait. Az életciklus általában a következőket tartalmazza:

- Indításakor:
  - Szolgáltatások vannak felépítve.
  - A szolgáltatások kell létrehozni és nulla vagy több figyelői adja vissza.
  - Minden visszaadott figyelői nyílik, így a szolgáltatással való kommunikáció.
  - A szolgáltatás **RunAsync** metódust meghívják, és lehetővé teszi a szolgáltatás nincs hosszan futó feladatokat vagy háttérműveletek.
- Leállítás: során
  - A megszakítás token átadott **RunAsync** meg lett szakítva, és a figyelők be van zárva.
  - Után zárja be a figyelők, a service objektum maga destructed van.

Nincsenek információ ezek az események pontos sorrendjét. Az események sorrendjének attól függően, hogy a Reliable Services állapot nélküli vagy állapot-nyilvántartó némileg módosíthatja. Emellett az állapotalapú szolgáltatások esetében azt kell foglalkozniuk, az elsődleges felcserélés forgatókönyv. Ez a feladatütemezés során elsődleges szerepe át egy másik replikára (vagy fog érkezni) a szolgáltatás leállítása nélkül. Végül azt kell gondolni a hiba vagy sikertelen feltételek.

## <a name="stateless-service-startup"></a>Állapotmentes szolgáltatás indítása
Az életciklus állapotmentes szolgáltatások rendkívül egyszerű. A következő események sorrendje:

1. A szolgáltatás jön létre.
2. Ezt követően párhuzamosan, két dolog történik:
    - `StatelessService.CreateServiceInstanceListeners()` meghívott és bármely figyelői megnyitott adja vissza. `ICommunicationListener.OpenAsync()` az egyes figyelő neve.
    - A szolgáltatás `StatelessService.RunAsync()` módszert hívja meg.
3. Ilyen esetekben a szolgáltatás `StatelessService.OnOpenAsync()` módszert hívja meg. Ez a hívás nem szokványos felülbírálást, de érhető el. Jelenleg a kiterjesztett szolgáltatás inicializálási feladatok indíthatók el.

Ne feledje, hogy a van nem hozhat létre, és nyissa meg a figyelők a hívások közötti rendezés és **RunAsync**. A figyelők előtt is megnyithatja **RunAsync** elindult. Ehhez hasonlóan hívhat **RunAsync** előtt nyissa meg, vagy akár képzett a kommunikációs figyelőket. Ha bármely szinkronizálásra szükség, azt van hátra gyakorlatként, a végrehajtója. Az alábbiakban néhány gyakori megoldások:

  - Néha a figyelői nem működőképes, amíg az egyéb információk jön létre, vagy a munkát. A állapotmentes szolgáltatások esetében, amelyek a munkát általában más helyeken, például a következő lehet elvégezni: 
    - A szolgáltatás konstruktor.
    - Során a `CreateServiceInstanceListeners()` hívja.
    - Maga a figyelő a fejlesztés részeként.
  - Néha az a kód **RunAsync** nem indul el, amíg meg nyitva, a figyelők. Ebben az esetben további koordináció szükség. Egy gyakori megoldás, hogy azt a jelzőt a figyelők belül, amely azt jelzi, amikor befejeződött rendelkezik. Ez a jelző majd be van jelölve **RunAsync** tényleges munka folytatása előtt.

## <a name="stateless-service-shutdown"></a>Az állapotmentes szolgáltatás leállítása
Az állapotmentes szolgáltatás leáll, ugyanezt a mintát követik, csak a fordított:

1. Párhuzamos:
    - Minden nyitott figyelői bezárul. `ICommunicationListener.CloseAsync()` az egyes figyelő neve.
    - A megszakítás token átadott `RunAsync()` meg lett szakítva. A megszakítás token ellenőrzése `IsCancellationRequested` tulajdonság igaz értéket ad vissza, és ha a neve, a jogkivonat `ThrowIfCancellationRequested` metódus jelez egy `OperationCanceledException`.
2. Miután `CloseAsync()` minden egyes figyelő befejezése és `RunAsync()` is befejezését követően a szolgáltatás `StatelessService.OnCloseAsync()` metódust meghívják, és ha van ilyen.  OnCloseAsync nevezzük, amikor az állapot nélküli szolgáltatáspéldány fog szabályosan le kell állítani. Ez akkor fordulhat elő, amikor a szolgáltatást kód frissítés alatt áll, a szolgáltatáspéldány terheléselosztás olvashatják vagy egy átmeneti hiba észlelhető. Ritka felülbírálása `StatelessService.OnCloseAsync()`, de használható, nyugodtan zárja be az erőforrások, állítsa le a háttérben történő feldolgozás, külső állapotmentést Befejezés vagy zárja le a meglévő kapcsolatok.
3. Miután `StatelessService.OnCloseAsync()` futtatása befejeződik, a service objektum destructed van.

## <a name="stateful-service-startup"></a>Állapotalapú szolgáltatás indítása
Állapotalapú szolgáltatások egy hasonló mintát állapotmentes szolgáltatások, néhány módosítással rendelkezik. Egy állapotalapú service kezdve az események sorrendje a következőképpen történik:

1. A szolgáltatás jön létre.
2. `StatefulServiceBase.OnOpenAsync()` a neve. Ez a hívás nem gyakran bírálja felül a szolgáltatásban.
3. A következő dolog párhuzamosan történik:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` meghívása. 
      - Ha az elsődleges szolgáltatás, az összes visszaadott figyelői nyílnak meg. `ICommunicationListener.OpenAsync()` az egyes figyelő neve.
      - Ha a szolgáltatás egy másodlagos szolgáltatást, csak ezek a figyelők megjelölve `ListenOnSecondary = true` nyílnak meg. Figyelők, amelyek meg van nyitva a másodlagos példány hozható létre, akkor az kevésbé gyakori.
    - Ha a szolgáltatás jelenleg egy elsődleges, a szolgáltatás a `StatefulServiceBase.RunAsync()` módszert hívja meg.
4. A replika figyelő minden után `OpenAsync()` meghívja a Befejezés gombra és `RunAsync()` nevezzük, `StatefulServiceBase.OnChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran bírálja felül a szolgáltatásban.

Állapotmentes szolgáltatások hasonlóan van nincs összehangolását a sorrendet, amelyben a figyelők létrejön és megnyílik, és ha **RunAsync** nevezzük. Koordinációs van szüksége, ha a megoldások hasonlóan. Állapotalapú szolgáltatás egy további eset van. Tegyük fel, hogy a hívások a kommunikációs figyelőket kiszolgálófarmban szükséges információ néhány belül tartani [a Reliable Collections](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Mivel a kommunikációs figyelőket sikerült megnyitni, mielőtt a megbízható gyűjtemények olyan olvasható és írható, és mielőtt **RunAsync** indítható, néhány további koordináció szükség. A legegyszerűbb és leggyakrabban használt megoldás az ügyfél által használt funkcióját, és ismételje meg a kérelmet hibakódot ad vissza, a kommunikációs figyelőket szól.

## <a name="stateful-service-shutdown"></a>Állapotalapú szolgáltatás leállítása
Állapotmentes szolgáltatások, mint a leállítás során életciklusesemények ugyanaz, mint indításakor, de fordított irányú. Ha egy állapotalapú szolgáltatás leáll, az alábbi események történnek:

1. Párhuzamos:
    - Minden nyitott figyelői bezárul. `ICommunicationListener.CloseAsync()` az egyes figyelő neve.
    - A megszakítás token átadott `RunAsync()` meg lett szakítva. A megszakítás token ellenőrzése `IsCancellationRequested` tulajdonság igaz értéket ad vissza, és ha a neve, a jogkivonat `ThrowIfCancellationRequested` metódus jelez egy `OperationCanceledException`.
2. Miután `CloseAsync()` minden egyes figyelő befejezése és `RunAsync()` is befejezését követően a szolgáltatás `StatefulServiceBase.OnChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran bírálja felül a szolgáltatásban.

   > [!NOTE]  
   > Várjon, amíg kell **RunAsync** befejezéséhez csak akkor szükséges, ha a replika egy elsődleges másodpéldány.

3. Miután a `StatefulServiceBase.OnChangeRoleAsync()` metódus befejeződik, a `StatefulServiceBase.OnCloseAsync()` módszert hívja meg. Ez a hívás nem szokványos felülbírálást, de érhető el.
3. Miután `StatefulServiceBase.OnCloseAsync()` futtatása befejeződik, a service objektum destructed van.

## <a name="stateful-service-primary-swaps"></a>Állapotalapú szolgáltatás elsődleges cseréje
Állapotalapú szolgáltatás futása közben, állapotalapú szolgáltatások csak az elsődleges replika rendelkezik-e a kommunikációs figyelőket megnyitott és azok **RunAsync** nevű metódust. Másodlagos replikák tevődnek, de nincs további hívások megtekintése. Egy állapotalapú szolgáltatás fut, amíg a replika, amely jelenleg az elsődleges tartalék vagy fürtterhelés optimalizálás miatt lehet módosítani. Ez mit jelent a feltételek az életciklus-események replika számára látható? Az állapot-nyilvántartó replika látja viselkedése attól függ, hogy-e a replika lefokozása, vagy több előléptetése során a lapozófájl-kapacitás.

### <a name="for-the-primary-thats-demoted"></a>Az elsődleges, amely lefokozása
A Service Fabric az elsődleges másodpéldány, hogy visszalép, a replika üzenetek feldolgozásának leállítása, és lépjen ki a háttérben történő munka történik mindez kell rendelkeznie. Ezt a lépést, ezért úgy tűnik, mint amikor a szolgáltatás leállt. Egy különbség az, hogy a szolgáltatás nem destructed vagy zárult be, mert egy másodlagos marad. A következő API-k nevezzük:

1. Párhuzamos:
    - Minden nyitott figyelői bezárul. `ICommunicationListener.CloseAsync()` az egyes figyelő neve.
    - A megszakítás token átadott `RunAsync()` meg lett szakítva. A megszakítás token ellenőrzése `IsCancellationRequested` tulajdonság igaz értéket ad vissza, és ha a neve, a jogkivonat `ThrowIfCancellationRequested` metódus jelez egy `OperationCanceledException`.
2. Miután `CloseAsync()` minden egyes figyelő befejezése és `RunAsync()` is befejezését követően a szolgáltatás `StatefulServiceBase.OnChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran bírálja felül a szolgáltatásban.

### <a name="for-the-secondary-thats-promoted"></a>A másodlagos, amely előléptetése
Ehhez hasonlóan a Service Fabric kell a másodlagos másodpéldány, amely lett előléptetve, indítsa el a hálózati üzenetek figyeli, és indítsa el minden olyan háttérfeladatokat, el kell végeznie. Ez a folyamat, ezért úgy tűnik, mint a szolgáltatás létrehozásakor, azzal a különbséggel, hogy maga a replika már létezik. A következő API-k nevezzük:

1. Párhuzamos:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` meghívott és bármely figyelői megnyitott adja vissza. `ICommunicationListener.OpenAsync()` az egyes figyelő neve.
    - A szolgáltatás `StatefulServiceBase.RunAsync()` módszert hívja meg.
2. A replika figyelő minden után `OpenAsync()` meghívja a Befejezés gombra és `RunAsync()` nevezzük, `StatefulServiceBase.OnChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran bírálja felül a szolgáltatásban.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Állapotalapú szolgáltatás leállítása és elsődleges lefokozása során felmerülő gyakori hibák
A Service Fabric módosítja az elsődleges, a számos okból állapotalapú szolgáltatás. A leggyakoribb vannak [fürt újraegyensúlyozása](service-fabric-cluster-resource-manager-balancing.md) és [alkalmazásfrissítés](service-fabric-application-upgrade.md). Ezek a műveletek során (és a normál szolgáltatás leállítása, például szeretné látni, hogy nem törölték-e a szolgáltatás), fontos, hogy a szolgáltatás tiszteletben a `CancellationToken`. 

Szolgáltatások, amelyek nem kezeli a törlés szabályszerűen több problémákat tapasztalhatnak. Ezek a műveletek lassúak, mert a szolgáltatások szabályosan leállítani a Service Fabric várakozik. Ez végső soron a sikertelen frissítések vezethet, hogy időtúllépés, és állítsa vissza. Tartsa tiszteletben a megszakítás token hibát is okozhat, imbalanced fürtök. Fürtök kiegyensúlyozatlanná válnak, mivel a csomópontok gyakori elérésű, azonban a szolgáltatások nem rebalanced, mert máshol helyezze el őket túl sokáig tart. 

Mivel a szolgáltatások állapot-nyilvántartó, annak valószínű oka is használják a [a Reliable Collections](service-fabric-reliable-services-reliable-collections.md). A Service Fabric, az elsődleges visszalép, az első dolog, amely akkor fordul elő, esetén, hogy az alapul szolgáló állapot írási hozzáférést visszavonták. Ez egy második együttesét problémákat, amelyek hatással lehetnek a szolgáltatás-életciklusának vezet. A gyűjtemények visszaadott kivételek alapján ütemezését, és hogy éppen áthelyezik a replika vagy leáll. Az ilyen kivételek megfelelően kell kezelni. A Service Fabric által okozott kivételek feldolgozását soroltuk állandó [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) és átmeneti [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategóriák. Állandó kivételeket kell naplózza és lépett fel, amíg az átmeneti kivételek végrehajtásával lehet újrapróbálkozni egy újrapróbálkozási logika alapján.

A kivételkezelést származó használatát a `ReliableCollections` service-életciklusesemények együtt teszteléséről és ellenőrzéséről a Reliable Services fontos részét képezi. Azt javasoljuk, hogy mindig terhelés alatt a szolgáltatás frissítések végrehajtása közben, és [káosz tesztelés](service-fabric-controlled-chaos.md) éles környezetben való üzembe helyezés előtt. Alapvető lépéseket segítségével, győződjön meg arról, hogy a megfelelő megvalósításához és életciklus-események megfelelően kezeli.


## <a name="notes-on-the-service-lifecycle"></a>Megjegyzés a kötegműveletekkel kapcsolatban a szolgáltatási életciklus
  - Mindkét a `RunAsync()` módszer és a `CreateServiceReplicaListeners/CreateServiceInstanceListeners` hívást kötelezők. Szolgáltatásként lehet őket, mindkettő, vagy egyiket sem. Például, ha a szolgáltatás felhasználói hívások válaszként összes feladatát, nincs szükség ahhoz, hogy megvalósítása `RunAsync()`. Csak a kommunikációs figyelőket és az ahhoz tartozó kódot szükség. Ehhez hasonlóan létrehozásával és a kommunikációs figyelőket visszaadó nem kötelező, mivel lehet, hogy a szolgáltatás csak ehhez a háttérműveletek, és ezért csak kell megvalósítani `RunAsync()`.
  - Egy szolgáltatás befejezéséhez érvényes `RunAsync()` sikeresen és azt adják vissza. Befejezése nem hiba feltétel. Befejezése `RunAsync()` azt jelzi, hogy a szolgáltatás a háttérműveletek befejeződött. A stateful reliable services for `RunAsync()` nem hívják újra történik, ha a replika visszalép az elsődleges, másodlagos, és ezután előléptetve elsődleges.
  - Ha egy szolgáltatás kilép a `RunAsync()` szerint néhány váratlan kivétel történt kivétel, ez jelent hibát. A service objektum leáll, és a egy állapot jelenik meg hibaüzenet.
  - Bár ezek a metódusok visszatérésre nincs időkorlát van, azonnal tudni írni a Reliable Collections, és ezért nem tudja végrehajtani bármely valós munkát. Azt javasoljuk, hogy a megszakítási kérés fogadásakor a lehető leggyorsabban visszaadása. Ha a szolgáltatás nem válaszol ezen API-hívások ésszerű időn belül, a Service Fabric kényszerített leállíthatja a szolgáltatáshoz. Ez általában csak akkor történik alkalmazásfrissítések, vagy ha egy szolgáltatás törlése folyamatban van. Ez az időtúllépési érték alapértelmezés szerint 15 perc.
  - A hibák a `OnCloseAsync()` elérési út eredményez `OnAbort()` hívott, amely a szolgáltatás karbantartása, és azok által igényelt erőforrásokat kiadása egy utolsó-alkalommal legjobb lehetőség. Ezt általában nevezzük, amikor egy állandó hibát észlel a csomóponton, vagy amikor a Service Fabric nem megbízható életciklusnak a kezelésében a szolgáltatáspéldány belső hiba miatt.
  - `OnChangeRoleAsync()` Ha az állapotalapú szolgáltatás replika például szerepkör, megváltoztatja az elsődleges vagy másodlagos nevezzük. Elsődleges replikára kapnak írási állapota (megengedett való létrehozásához és írásához a Reliable Collections). Másodlagos replikák olvasási állapota (csak olvasható meglévő megbízható gyűjteményekből) vannak megadva. A legtöbb munkát egy állapotalapú szolgáltatásban történik, az elsődleges replika. Másodlagos replikák csak olvasható érvényesítési, jelentés létrehozásához, data szintű adatbányászatra vagy más csak olvasási feladatokat hajthat végre.

## <a name="next-steps"></a>További lépések
- [A Reliable Services bemutatása](service-fabric-reliable-services-introduction.md)
- [A Reliable Services – gyorsútmutató](service-fabric-reliable-services-quick-start.md)
- [Replikák és példányok](service-fabric-concepts-replica-lifecycle.md)
