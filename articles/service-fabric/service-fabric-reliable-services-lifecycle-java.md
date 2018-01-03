---
title: "Az Azure Service Fabric Reliable Services-életciklus áttekintése |} Microsoft Docs"
description: "További tudnivalók a Service Fabric megbízható szolgáltatások különböző életciklus események"
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 93fd003ff5ba7673e5a719fb1ced0cbb97034610
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Megbízható életciklusának áttekintése
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

Végezni a megbízható szolgáltatásokat nyújt, a legfontosabb az életciklus alapjait. Általában:

* Indítás közben
  * Szolgáltatások össze
  * Rendelkeznek, és térjen vissza a nulla vagy több figyelői lehetőséget
  * A visszaadott figyelői nyitva vannak, a szolgáltatással való kommunikáció engedélyezése
  * A szolgáltatás runAsync metódusában nevezik, így a szolgáltatás hosszú ideig futó vagy háttérben munka
* Leállítás közben
  * A megszakítási token runAsync átadott megszakadt, és a figyelők be van zárva.
  * Vagyis egyszer teljes, a szolgáltatás objektum van destructed

Ezek az események pontos rendelési körül részletek érhetők el. Azt jelzi, hogy attól függően, hogy a megbízható szolgáltatás Stateless vagy állapotalapú alkalmazások és szolgáltatások kis mértékben változhat-különösen az események sorrendje. Emellett az állapotalapú szolgáltatások esetén tudunk és az elsődleges swap forgatókönyv kezelésére. Ez a folyamat során az elsődleges szerepkör átkerül egy másik replika (vagy ismét elérhető lesz) szolgáltatás leállítása nélkül. Végül azt kell gondolniuk hiba vagy probléma feltételt.

## <a name="stateless-service-startup"></a>Állapot nélküli a szolgáltatás indítása
Az állapotmentes szolgáltatások életciklusát meglehetősen egyszerű. Az események sorrendje a következő:

1. A szolgáltatás összeállított
2. Ezt követően a párhuzamos két dolgot fordulhat elő:
    - `StatelessService.createServiceInstanceListeners()`meghívták és bármely visszaadott figyelői megnyitása (`CommunicationListener.openAsync()` meghívta az egyes figyelő)
    - A szolgáltatás runAsync metódusában (`StatelessService.runAsync()`) neve
3. Ha jelen van, a szolgáltatás saját onOpenAsync módszer neve (pontosabban `StatelessService.onOpenAsync()` nevezik. Ez egy ritka felülbírálást de érhető el).

Fontos megjegyezni, hogy nincs nincs létrehozásához, és nyissa meg a figyelők és runAsync hívások közötti rendezés. Előfordulhat, hogy nyissa meg a figyelők, runAsync megkezdése előtt. Ehhez hasonlóan runAsync is szükségessé tehet meghívása előtt a kommunikációs figyelőket is nyitva, vagy még össze. Bármely szinkronizálásra szükség, ha azt balról gyakorlat szerint a végrehajtója. Közös megoldások:

* Egyes esetekben a figyelői nem működőképes, amíg az egyéb információk jön létre, vagy végzett munkát. Az állapotmentes szolgáltatások munkahelyi általában végezheti el a szolgáltatást a konstruktorban, során a `createServiceInstanceListeners()` hívja, vagy maga a figyelő a konstrukció részeként.
* Egyes esetekben runAsync kódja nem szeretné elindítani, amíg a figyelők is nyitva. Ebben az esetben a további koordinációs szükség. Egy gyakori megoldás, néhány jelző belül a figyelők, amely azt jelzi, ha végzi, amely beadása runAsync a tényleges munkát folytatása előtt.

## <a name="stateless-service-shutdown"></a>Állapot nélküli szolgáltatás leállítása
Ha egy állapotmentes szolgáltatások leállítása, ugyanilyen mintájú után következik, csak a névkeresési:

1. Párhuzamos
    - Bármely nyitott figyelők be legyen zárva (`CommunicationListener.closeAsync()` meghívta az egyes figyelő)
    - A megszakítási token átadott `runAsync()` megszakadt (ellenőrzésére a visszavonásra token `isCancelled` tulajdonság igaz értéket ad vissza, és a token hívása `throwIfCancellationRequested` metódus jelez egy `CancellationException`)
2. Egyszer `closeAsync()` minden egyes figyelő működése befejeződött és `runAsync()` is befejeződött, a szolgáltatás `StatelessService.onCloseAsync()` metódus lehívásra kerül, ha van ilyen (újra ez egy ritka felülbírálás).
3. Miután `StatelessService.onCloseAsync()` befejeződött, a szolgáltatás objektum destructed van

## <a name="stateful-service-startup"></a>Az állapotalapú szolgáltatás indítása
Állapotalapú szolgáltatások hasonló mintát állapotmentes szolgáltatásokhoz, néhány módosításokkal rendelkezik. Az állapotalapú szolgáltatás elindítása, az események sorrendje a következőképpen történik:

1. A szolgáltatás létre lehet hozni az adatforrásnézetet.
2. `StatefulServiceBase.onOpenAsync()`nevezik. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.
3. A következő dolog párhuzamosan történik:
    - `StatefulServiceBase.createServiceReplicaListeners()`meghívták. 
      - Ha az elsődleges szolgáltatás, az összes visszaadott figyelői megnyitása. `CommunicationListener.openAsync()`a minden egyes figyelő neve.
      - Ha a másodlagos szolgáltatás, csak ezek a figyelők jelölésű `listenOnSecondary = true` megnyitása. Figyelők, amelyek nyitva a másodlagos adatbázist, akkor az ritkább.
    - Ha a szolgáltatás jelenleg egy elsődleges, a szolgáltatás által `StatefulServiceBase.runAsync()` metódust.
4. A replika figyelő minden után `openAsync()` meghívja a Befejezés gombra és `runAsync()` nevezik, `StatefulServiceBase.onChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

Állapotmentes szolgáltatásokhoz hasonlóan van a sorrendet, amelyben a figyelők vannak létrejött, és nem összehangolását és mikor **runAsync** nevezik. Ha koordinációs van szüksége, a megoldások olyan hasonlóan. Az állapotalapú szolgáltatásból egy további esetében van. Tegyük fel például, hogy a kiszolgálófarmban lévő a kommunikációs figyelőket hívások szükséges-e tartani belül néhány információt [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md). Mivel a kommunikációs figyelőket sikerült megnyitni, mielőtt a megbízható gyűjtemények olvasható és írható, és mielőtt **runAsync** sikerült elindítani, néhány további koordinációs szükség. A legegyszerűbb és leggyakoribb megoldás van a kommunikációs figyelőket vissza egy hibakódot az ügyfél által használt funkcióját, és próbálkozzon újra a kéréssel.

## <a name="stateful-service-shutdown"></a>Az állapotalapú szolgáltatás leállítása
Állapotmentes szolgáltatások, például a leállítás során életciklus-események megegyeznek a rendszerindítás során, de fordított irányú. Ha egy állapotalapú szolgáltatás leáll, a következők történnek:

1. Párhuzamos:
    - Bármely nyitott figyelők be van zárva. `CommunicationListener.closeAsync()`a minden egyes figyelő neve.
    - A megszakítási token átadott `runAsync()` meg lett szakítva. A megszakítási token hívása `isCancelled()` metódus igaz értéket ad vissza, és hívása, a jogkivonat `throwIfCancellationRequested()` metódus jelez egy `OperationCanceledException`.
2. Után `closeAsync()` minden egyes figyelő befejezése és `runAsync()` is befejeződött, a szolgáltatás `StatefulServiceBase.onChangeRoleAsync()` nevezik. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

   > [!NOTE]  
   > Várjon, amíg a szükséges **runAsync** befejezéséhez is csak akkor szükséges, ha a replika egy elsődleges másodpéldány.

3. Miután a `StatefulServiceBase.onChangeRoleAsync()` metódus befejezésekor a `StatefulServiceBase.onCloseAsync()` metódust. Ez a hívás ritka felülbírálást, de akkor érhető el.
3. Miután `StatefulServiceBase.onCloseAsync()` befejeződik, a szolgáltatás objektum destructed van.

## <a name="stateful-service-primary-swaps"></a>Az állapotalapú szolgáltatás elsődleges címek cseréje
Az állapotalapú szolgáltatás futása közben csak az elsődleges replikára változott, hogy az állapotalapú szolgáltatások rendelkezik-e a kommunikációs figyelőket megnyitott és azok **runAsync** a hívott metódus. Másodlagos replikák össze, de nincs további hívásainak. Az állapotalapú szolgáltatás futása közben, a replika jelenleg az elsődleges módosíthatja. Ez mit jelent a feltételeknek az életciklus replika látható eseményeket? Az állapot-nyilvántartó replika látja a viselkedés attól függ, hogy a replika alatt lefokozásra vagy előléptetett során a felcserélés.

### <a name="for-the-primary-thats-demoted"></a>Az elsődleges, amely lefokozása
Az elsődleges másodpéldány, hogy visszalép a Service Fabric kell ennek a replikának üzenetek feldolgozása, és lépjen ki a háttérműveletek műveletet. Ennek eredményeképpen ebben a lépésben tűnik, amikor a szolgáltatás le van állítva. Egy különbség, hogy a szolgáltatás nem destructed vagy bezárva, mert egy másodlagos marad. A következő API-k nevezzük:

1. Párhuzamos:
    - Bármely nyitott figyelők be van zárva. `CommunicationListener.closeAsync()`a minden egyes figyelő neve.
    - A megszakítási token átadott `runAsync()` megszakadt. A megszakítási token ellenőrzése `isCancelled()` metódus igaz értéket ad vissza, és ha neve, a jogkivonat `throwIfCancellationRequested()` metódus jelez egy `OperationCanceledException`.
2. Után `closeAsync()` minden egyes figyelő befejezése és `runAsync()` is befejeződött, a szolgáltatás `StatefulServiceBase.onChangeRoleAsync()` nevezik. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

### <a name="for-the-secondary-thats-promoted"></a>A másodlagos, amely állapotban van
Hasonlóképpen a Service Fabric kell a másodlagos másodpéldány, amely tartományvezérlővé történő megkezdeni a figyelést az üzenetek a keresztülhaladnak a hálózaton, és indítsa el a háttér feladatok elvégzéséhez szükséges. Ennek eredményeképpen a folyamat szolgál a szolgáltatás létrehozásakor azzal a különbséggel, hogy maga a replika már létezik. A következő API-k nevezzük:

1. Párhuzamos:
    - `StatefulServiceBase.createServiceReplicaListeners()`meghívták, és minden visszaadott figyelői megnyitása. `CommunicationListener.openAsync()`a minden egyes figyelő neve.
    - A szolgáltatás `StatefulServiceBase.runAsync()` metódust.
2. A replika figyelő minden után `openAsync()` meghívja a Befejezés gombra és `runAsync()` nevezik, `StatefulServiceBase.onChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.


### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Az állapotalapú szolgáltatás leállítása és elsődleges lefokozás során kapcsolatos gyakori hibák
A Service Fabric módosítja az elsődleges számos okból az állapotalapú szolgáltatás. A leggyakoribb vannak [fürt újraelosztás](service-fabric-cluster-resource-manager-balancing.md) és [az alkalmazásfrissítés](service-fabric-application-upgrade.md). Ezek a műveletek során (és a normál szolgáltatás leállítása, például szeretné látni, hogy törölték-e a szolgáltatás), fontos, hogy a szolgáltatás tiszteletben tartják a `cancellationToken`. 

Szolgáltatások szabályszerűen nem kezelő törlését is több problémákba ütközhetnek. Ezek a műveletek lassúak, mivel a Service Fabric szabályosan leállítja a szolgáltatások vár. Ez végső soron előfordulhat, hogy a sikertelen frissítések adott túllépi az időkorlátot, és állítsa vissza. Hiba a megszakítási token tiszteletben is eredményezheti, hogy imbalanced fürtök. Fürtök válhat egyenetlen, mert a csomópontok gyakran használt adatok lekérése, de a szolgáltatások nem rebalanced, mert azt túl lassan máshol helyezze el őket. 

Mivel a szolgáltatások állapotalapú, akkor valószínű is, hogy használják a [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md). A Service Fabric egy elsődleges lefokozása, az első dolog, ami történik esetén meg kell, hogy a mögöttes állapota írási visszavonva. Ennek eredménye, hogy egy második problémák, amelyek hatással lehetnek a szolgáltatási életciklus. A visszatérési kivételek időzítése, és hogy a replika áthelyezik alapuló gyűjtemények vagy leáll. Az ilyen kivételek megfelelően kell kezelni. A Service Fabric által okozott kivételeket sorolhatók állandó [(`FabricException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception) és átmeneti [(`FabricTransientException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception._fabric_transient_exception) kategóriák. Állandó kivételek legyen naplózva, és alapján néhány újrapróbálkozási logika átmeneti kivételek követően újra ellenőrzésekor.

A kivételek használatát érkező kezelése a `ReliableCollections` szolgáltatás életciklus-események együtt tesztelése és ellenőrzése egy megbízható szolgáltatás fontos részét képezi. Azt javasoljuk, hogy mindig a szolgáltatás terhelés frissítések végrehajtása közben és [chaos tesztelés](service-fabric-controlled-chaos.md) üzemi környezetben üzembe helyezése előtt. Az alábbi alapvető lépések segítségével, győződjön meg arról, hogy a szolgáltatás megfelelően van megvalósítva, és életciklus-események megfelelően kezeli.

## <a name="notes-on-service-lifecycle"></a>Tudnivalók a szolgáltatási életciklus
* Mindkét a `runAsync()` metódus és a `createServiceInstanceListeners/createServiceReplicaListeners` hívások nem kötelező. Szolgáltatásként lehet őket, egyaránt, vagy egyiket sem. Például, ha a szolgáltatás felhasználói hívások válaszként a tevékenységeket, nincs szükség ahhoz, hogy a megvalósítása `runAsync()`. Csak a kommunikációs figyelőket és a kapcsolódó kódra szükség. Hasonlóképpen létrehozása és a kommunikációs figyelőket adatszolgáltató nem kötelező, a szolgáltatás esetleg csak háttérműveletek elvégzéséhez, és így kell megvalósítani`runAsync()`
* A szolgáltatás befejezi érvényes `runAsync()` sikeresen és visszatérési belőle. Ez nem tekinthető hiba feltétel és a szolgáltatás befejezését a háttérműveletek jelenti. Az állapot-nyilvántartó megbízható szolgáltatások `runAsync()` volna lehet újra hívni Ha a szolgáltatás elsődleges lefokozásra és majd előléptetni elsődleges.
* Ha egy szolgáltatás kilép a `runAsync()` egy váratlan kivétel kiváltása, ez a hiba és a szolgáltatás objektum le van állítva, és a rendszerállapot hibát jelzett.
* Bár a visszatérésre ezek a módszerek nem korlátozott, azonnal elveszti a írjanak, és ezért nem tudja végrehajtani a valódi munkát. Térjen vissza a megszakítási kérelem fogadása után a lehető leggyorsabban ajánlott. Ha a szolgáltatás nem válaszol az adott API-hívások elfogadható időn belül a Service Fabric kényszerített előfordulhat, hogy a szolgáltatás leáll. Általában ez csak akkor fordul elő alkalmazásfrissítések vagy egy szolgáltatás törlésekor során. Ez az időkorlát értéke alapértelmezés szerint 15 percenként.
* Hibák a `onCloseAsync()` elérési eredményez `onAbort()` Ez a szolgáltatás törlése egy utolsó-alkalommal legjobb lehetőség meghívott kialakításához, és felszabadíthatja a minden olyan erőforrásnál, amely azt állítják.

## <a name="next-steps"></a>További lépések
* [Bevezetés a Reliable Services használatába](service-fabric-reliable-services-introduction.md)
* [Megbízható szolgáltatások – első lépések](service-fabric-reliable-services-quick-start-java.md)
* [Megbízható szolgáltatás használati speciális](service-fabric-reliable-services-advanced-usage.md)
