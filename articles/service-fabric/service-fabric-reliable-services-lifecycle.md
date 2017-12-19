---
title: "Az Azure Service Fabric Reliable Services-életciklus áttekintése |} Microsoft Docs"
description: "További tudnivalók a Service Fabric Reliable Services különböző életciklus események"
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ebfe23ea1e07e7578e8bd352a482ecb1016829de
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Megbízható életciklusának áttekintése
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

Most végezni, az Azure Service Fabric megbízható szolgáltatásokat nyújt, a legfontosabb az életciklus alapjait. A élettartama általában az alábbiakat tartalmazza:

- Indításakor:
  - Szolgáltatások össze.
  - A szolgáltatások létrehozásához, és térjen vissza a nulla vagy több figyelői lehetőségük van.
  - A visszaadott figyelői megnyitása, lehetővé téve a szolgáltatással folytatott kommunikáció.
  - A szolgáltatás **RunAsync** metódus lehívásra kerül, így a szolgáltatás nincs hosszan futó feladatokat vagy háttérműveletek.
- Leállításakor:
  - A megszakítási token átadott **RunAsync** megszakad, és a figyelők be van zárva.
  - Után zárja be a figyelők, a szolgáltatás objektum destructed van.

Ezek az események pontos rendelési körül részletek érhetők el. Az események sorrendjének attól függően, hogy a megbízható szolgáltatás állapot nélküli és állapotalapú némileg módosíthatja. Emellett az állapotalapú szolgáltatások esetén azt kell foglalkozik az elsődleges swap forgatókönyv. Ez a folyamat során az elsődleges szerepkör átkerül egy másik replika (vagy ismét elérhető lesz) szolgáltatás leállítása nélkül. Végül azt kell gondolni a hiba vagy probléma feltételt.

## <a name="stateless-service-startup"></a>Állapot nélküli a szolgáltatás indítása
Az állapotmentes szolgáltatások életciklusát egyszerű. Az események sorrendje a következő:

1. A szolgáltatás létre lehet hozni az adatforrásnézetet.
2. Ezt követően párhuzamosan, két dolog történik:
    - `StatelessService.CreateServiceInstanceListeners()`meghívták, és minden visszaadott figyelői megnyitása. `ICommunicationListener.OpenAsync()`a minden egyes figyelő neve.
    - A szolgáltatás `StatelessService.RunAsync()` metódust.
3. Ha van ilyen, a szolgáltatás `StatelessService.OnOpenAsync()` metódust. Ez a hívás ritka felülbírálást, de akkor érhető el.

Ne feledje, hogy nem a hívások létrehozásához, és nyissa meg a figyelők közötti rendezés és **RunAsync**. A figyelők úgy is megnyithatja, mielőtt **RunAsync** elindult. Hasonlóképpen, hívhat meg **RunAsync** előtt a kommunikációs figyelőket megnyitott vagy akár felépített. Bármely szinkronizálásra szükség, ha azt balról gyakorlat szerint a végrehajtója. Az alábbiakban néhány gyakori:

  - Egyes esetekben a figyelői nem működőképes, amíg az egyéb információk jön létre, vagy a munkát. Az állapotmentes szolgáltatások, munkahelyi általában végezheti el a más helyeken, például a következő: 
    - A szolgáltatás konstruktorában.
    - Során a `CreateServiceInstanceListeners()` hívható meg.
    - A figyelő magát a konstrukció részeként.
  - Egyes esetekben a kód **RunAsync** nem indul el, amíg a figyelők is nyitva. Ebben az esetben a további koordinációs szükség. Egy közös megoldás az, hogy van-e a figyelők belül jelzőt, amely azt jelzi, amikor befejeződött rendelkezik. Ez a jelző majd be van jelölve **RunAsync** a tényleges munkát folytatása előtt.

## <a name="stateless-service-shutdown"></a>Állapot nélküli szolgáltatás leállítása
Az állapotmentes szolgáltatások leállítása, ugyanilyen mintájú követi, csak a névkeresési:

1. Párhuzamos:
    - Bármely nyitott figyelők be van zárva. `ICommunicationListener.CloseAsync()`a minden egyes figyelő neve.
    - A megszakítási token átadott `RunAsync()` megszakadt. A megszakítási token ellenőrzése `IsCancellationRequested` tulajdonság igaz értéket ad vissza, és ha neve, a jogkivonat `ThrowIfCancellationRequested` metódus jelez egy `OperationCanceledException`.
2. Miután `CloseAsync()` minden egyes figyelő befejezése és `RunAsync()` is befejeződött, a szolgáltatás `StatelessService.OnCloseAsync()` metódus lehívásra kerül, ha van ilyen. Ritka felülbírálása `StatelessService.OnCloseAsync()`.
3. Miután `StatelessService.OnCloseAsync()` befejeződik, a szolgáltatás objektum destructed van.

## <a name="stateful-service-startup"></a>Az állapotalapú szolgáltatás indítása
Állapotalapú szolgáltatások hasonló mintát állapotmentes szolgáltatásokhoz, néhány módosításokkal rendelkezik. Az állapotalapú szolgáltatás elindítása, az események sorrendje a következőképpen történik:

1. A szolgáltatás létre lehet hozni az adatforrásnézetet.
2. `StatefulServiceBase.OnOpenAsync()`nevezik. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.
3. A következő dolog párhuzamosan történik:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`meghívták. 
      - Ha az elsődleges szolgáltatás, az összes visszaadott figyelői megnyitása. `ICommunicationListener.OpenAsync()`a minden egyes figyelő neve.
      - Ha a másodlagos szolgáltatás, csak ezek a figyelők jelölésű `ListenOnSecondary = true` megnyitása. Figyelők, amelyek nyitva a másodlagos adatbázist, akkor az ritkább.
    - Ha a szolgáltatás jelenleg egy elsődleges, a szolgáltatás által `StatefulServiceBase.RunAsync()` metódust.
4. A replika figyelő minden után `OpenAsync()` meghívja a Befejezés gombra és `RunAsync()` nevezik, `StatefulServiceBase.OnChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

Állapotmentes szolgáltatásokhoz hasonlóan van a sorrendet, amelyben a figyelők vannak létrejött, és nem összehangolását és mikor **RunAsync** nevezik. Ha koordinációs van szüksége, a megoldások olyan hasonlóan. Az állapotalapú szolgáltatásból egy további esetében van. Tegyük fel például, hogy a kiszolgálófarmban lévő a kommunikációs figyelőket hívások szükséges-e tartani belül néhány információt [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md). Mivel a kommunikációs figyelőket sikerült megnyitni, mielőtt a megbízható gyűjtemények olvasható és írható, és mielőtt **RunAsync** sikerült elindítani, néhány további koordinációs szükség. A legegyszerűbb és leggyakoribb megoldás van a kommunikációs figyelőket vissza egy hibakódot az ügyfél által használt funkcióját, és próbálkozzon újra a kéréssel.

## <a name="stateful-service-shutdown"></a>Az állapotalapú szolgáltatás leállítása
Állapotmentes szolgáltatások, például a leállítás során életciklus-események megegyeznek a rendszerindítás során, de fordított irányú. Ha egy állapotalapú szolgáltatás leáll, a következők történnek:

1. Párhuzamos:
    - Bármely nyitott figyelők be van zárva. `ICommunicationListener.CloseAsync()`a minden egyes figyelő neve.
    - A megszakítási token átadott `RunAsync()` megszakadt. A megszakítási token ellenőrzése `IsCancellationRequested` tulajdonság igaz értéket ad vissza, és ha neve, a jogkivonat `ThrowIfCancellationRequested` metódus jelez egy `OperationCanceledException`.
2. Után `CloseAsync()` minden egyes figyelő befejezése és `RunAsync()` is befejeződött, a szolgáltatás `StatefulServiceBase.OnChangeRoleAsync()` nevezik. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

   > [!NOTE]  
   > Várjon, amíg a szükséges **RunAsync** befejezéséhez is csak akkor szükséges, ha a replika egy elsődleges másodpéldány.

3. Miután a `StatefulServiceBase.OnChangeRoleAsync()` metódus befejezésekor a `StatefulServiceBase.OnCloseAsync()` metódust. Ez a hívás ritka felülbírálást, de akkor érhető el.
3. Miután `StatefulServiceBase.OnCloseAsync()` befejeződik, a szolgáltatás objektum destructed van.

## <a name="stateful-service-primary-swaps"></a>Az állapotalapú szolgáltatás elsődleges címek cseréje
Az állapotalapú szolgáltatás futása közben csak az elsődleges replikára változott, hogy az állapotalapú szolgáltatások rendelkezik-e a kommunikációs figyelőket megnyitott és azok **RunAsync** a hívott metódus. Másodlagos replikák össze, de nincs további hívásainak. Az állapotalapú szolgáltatás futása közben, a replika jelenleg az elsődleges módosíthatja. Ez mit jelent a feltételeknek az életciklus replika látható eseményeket? Az állapot-nyilvántartó replika látja a viselkedés attól függ, hogy a replika alatt lefokozásra vagy előléptetett során a felcserélés.

### <a name="for-the-primary-thats-demoted"></a>Az elsődleges, amely lefokozása
Az elsődleges másodpéldány, hogy visszalép a Service Fabric kell ennek a replikának üzenetek feldolgozása, és lépjen ki a háttérműveletek műveletet. Ennek eredményeképpen ebben a lépésben tűnik, amikor a szolgáltatás le van állítva. Egy különbség, hogy a szolgáltatás nem destructed vagy bezárva, mert egy másodlagos marad. A következő API-k nevezzük:

1. Párhuzamos:
    - Bármely nyitott figyelők be van zárva. `ICommunicationListener.CloseAsync()`a minden egyes figyelő neve.
    - A megszakítási token átadott `RunAsync()` megszakadt. A megszakítási token ellenőrzése `IsCancellationRequested` tulajdonság igaz értéket ad vissza, és ha neve, a jogkivonat `ThrowIfCancellationRequested` metódus jelez egy `OperationCanceledException`.
2. Után `CloseAsync()` minden egyes figyelő befejezése és `RunAsync()` is befejeződött, a szolgáltatás `StatefulServiceBase.OnChangeRoleAsync()` nevezik. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

### <a name="for-the-secondary-thats-promoted"></a>A másodlagos, amely állapotban van
Hasonlóképpen a Service Fabric kell a másodlagos másodpéldány, amely tartományvezérlővé történő megkezdeni a figyelést az üzenetek a keresztülhaladnak a hálózaton, és indítsa el a háttér feladatok elvégzéséhez szükséges. Ennek eredményeképpen a folyamat szolgál a szolgáltatás létrehozásakor azzal a különbséggel, hogy maga a replika már létezik. A következő API-k nevezzük:

1. Párhuzamos:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`meghívták, és minden visszaadott figyelői megnyitása. `ICommunicationListener.OpenAsync()`a minden egyes figyelő neve.
    - A szolgáltatás `StatefulServiceBase.RunAsync()` metódust.
2. A replika figyelő minden után `OpenAsync()` meghívja a Befejezés gombra és `RunAsync()` nevezik, `StatefulServiceBase.OnChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Az állapotalapú szolgáltatás leállítása és elsődleges lefokozás során kapcsolatos gyakori hibák
A Service Fabric módosítja az elsődleges számos okból az állapotalapú szolgáltatás. A leggyakoribb vannak [fürt újraelosztás](service-fabric-cluster-resource-manager-balancing.md) és [az alkalmazásfrissítés](service-fabric-application-upgrade.md). Ezek a műveletek során (és a normál szolgáltatás leállítása, például szeretné látni, hogy törölték-e a szolgáltatás), fontos, hogy a szolgáltatás tiszteletben tartják a `CancellationToken`. 

Szolgáltatások szabályszerűen nem kezelő törlését is több problémákba ütközhetnek. Ezek a műveletek lassúak, mivel a Service Fabric szabályosan leállítja a szolgáltatások vár. Ez végső soron előfordulhat, hogy a sikertelen frissítések adott túllépi az időkorlátot, és állítsa vissza. Hiba a megszakítási token tiszteletben is eredményezheti, hogy imbalanced fürtök. Fürtök válhat egyenetlen, mert a csomópontok gyakran használt adatok lekérése, de a szolgáltatások nem rebalanced, mert azt túl lassan máshol helyezze el őket. 

Mivel a szolgáltatások állapotalapú, akkor valószínű is, hogy használják a [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md). A Service Fabric egy elsődleges lefokozása, az első dolog, ami történik esetén meg kell, hogy a mögöttes állapota írási visszavonva. Ennek eredménye, hogy egy második problémák, amelyek hatással lehetnek a szolgáltatási életciklus. A visszatérési kivételek időzítése, és hogy a replika áthelyezik alapuló gyűjtemények vagy leáll. Az ilyen kivételek megfelelően kell kezelni. A Service Fabric által okozott kivételeket sorolhatók állandó [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) és átmeneti [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategóriák. Állandó kivételek legyen naplózva, és alapján néhány újrapróbálkozási logika átmeneti kivételek követően újra ellenőrzésekor.

A kivételek használatát érkező kezelése a `ReliableCollections` szolgáltatás életciklus-események együtt tesztelése és ellenőrzése egy megbízható szolgáltatás fontos részét képezi. Azt javasoljuk, hogy mindig a szolgáltatás terhelés frissítések végrehajtása közben és [chaos tesztelés](service-fabric-controlled-chaos.md) üzemi környezetben üzembe helyezése előtt. Az alábbi alapvető lépések segítségével, győződjön meg arról, hogy a szolgáltatás megfelelően van megvalósítva, és életciklus-események megfelelően kezeli.


## <a name="notes-on-the-service-lifecycle"></a>Tudnivalók a a szolgáltatási életciklus
  - Mindkét a `RunAsync()` metódus és a `CreateServiceReplicaListeners/CreateServiceInstanceListeners` hívások nem kötelező. A szolgáltatás rendelkezhetnek őket, mindegyikét, vagy sem. Például, ha a szolgáltatás felhasználói hívások válaszként a tevékenységeket, nincs szükség ahhoz, hogy a megvalósítása `RunAsync()`. Csak a kommunikációs figyelőket és a kapcsolódó kódra szükség. Hasonlóképpen, létrehozása és a kommunikációs figyelőket vissza nem kötelező, lehet, hogy a szolgáltatás csak háttérműveletek elvégzéséhez, és így kell megvalósítani, `RunAsync()`.
  - A szolgáltatás befejezi érvényes `RunAsync()` sikeresen és visszatérési belőle. Befejezése nincs hiba feltétel. Befejezése `RunAsync()` azt jelzi, hogy a szolgáltatás a háttérműveletek befejeződött. Állapot-nyilvántartó megbízható szolgáltatások esetén `RunAsync()` nem hívják újra történik, ha a replika másodlagos elsődleges visszalép, és majd előléptetni elsődleges.
  - Ha egy szolgáltatás kilép a `RunAsync()` által egy váratlan kivétel kiváltása, ez jelent hibát. A szolgáltatás objektum le van állítva, és egy állapot jelenik meg hibaüzenet.
  - Bár ezek a módszerek visszatérése van nem korlátozott, azonnal elveszíti a megbízható gyűjteményekre írjanak, és ezért nem tudja végrehajtani a valódi munkát. Azt javasoljuk, hogy a visszavonási kérelem fogadása után a lehető leggyorsabban vissza. Ha a szolgáltatás nem válaszol az adott API-hívások elfogadható időn belül, a Service Fabric kényszerített is leáll a szolgáltatás. Általában ez csak akkor fordul elő alkalmazásfrissítések vagy egy szolgáltatás törlésekor során. Ez az időkorlát értéke alapértelmezés szerint 15 percenként.
  - Hibák a `OnCloseAsync()` elérési eredményez `OnAbort()` meghívott, amelyek az egy utolsó-alkalommal legjobb lehetőség a szolgáltatás karbantartása, és felszabadíthatja a minden olyan erőforrásnál, amely azt állítják.

## <a name="next-steps"></a>Következő lépések
- [Bevezetés a Reliable Services használatába](service-fabric-reliable-services-introduction.md)
- [Megbízható szolgáltatások – első lépések](service-fabric-reliable-services-quick-start.md)
- [Megbízható szolgáltatás használati speciális](service-fabric-reliable-services-advanced-usage.md)
- [Replikák és példányok](service-fabric-concepts-replica-lifecycle.md)
