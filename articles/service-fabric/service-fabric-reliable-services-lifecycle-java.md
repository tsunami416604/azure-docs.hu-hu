---
title: Az Azure Service Fabric Reliable Services életciklus |} Microsoft Docs
description: Ismerje meg a Service Fabric Reliable Services életciklus események.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: cc89d174a201b38d79c7993d548c8eac4a47fbcb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210681"
---
# <a name="reliable-services-lifecycle"></a>A Reliable Services életciklusa
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

Megbízható szolgáltatások az egyik elérhető Azure Service Fabric programozási modell. Megtanulni a Reliable Services életciklusát, esetén fontos tudni, hogy az alapvető életciklus-események. Események pontos rendelési attól függ, hogy konfigurációs adatait. 

A Reliable Services életciklusát általában a következő eseményeket tartalmazza:

* Indításakor:
  * Szolgáltatások össze.
  * Szolgáltatások létrehozni, és térjen vissza a nulla vagy több figyelői lehetőségük van.
  * A visszaadott figyelői nyílnak meg, a szolgáltatással folytatott kommunikáció.
  * A szolgáltatás `runAsync` metódus lehívásra kerül, így a szolgáltatás hosszan futó vagy is háttérben munka.
* Leállításakor:
  * A megszakítási jogkivonat számára átadott `runAsync` megszakad, és a figyelők be van zárva.
  * A szolgáltatás objektum destructed van.

Attól függően, hogy a megbízható szolgáltatás állapot nélküli és állapotalapú Reliable Services események sorrendje némileg változhatnak. 

Állapotalapú szolgáltatások esetén is, az elsődleges swap forgatókönyv eleget kell. Ez a folyamat során az elsődleges szerepkör átkerül egy másik replika (vagy ismét elérhető lesz) szolgáltatás leállítása nélkül. 

Végül meg kell gondolniuk hiba vagy probléma feltételt.

## <a name="stateless-service-startup"></a>Állapot nélküli a szolgáltatás indítása
Az állapotmentes szolgáltatások életciklusát meglehetősen egyszerű. Az események sorrendje a következő:

1. A szolgáltatás létre lehet hozni az adatforrásnézetet.
2. Ezek az események párhuzamosan:
    - `StatelessService.createServiceInstanceListeners()` meghívták, és minden visszaadott figyelői megnyitása. `CommunicationListener.openAsync()` a minden egyes figyelő neve.
    - A szolgáltatás `runAsync` metódus (`StatelessService.runAsync()`) nevezik.
3. Ha van ilyen, a szolgáltatás saját `onOpenAsync` metódust. Pontosabban `StatelessService.onOpenAsync()` nevezzük. Ez egy ritka felülbírálást, de érhető el.

Fontos megjegyezni, hogy nincs nincs a hívás létrehozásához, és nyissa meg a figyelők és a követőrendszer közötti rendezés `runAsync`. A figyelők sérülékennyé előtt `runAsync` elindult. Ehhez hasonlóan `runAsync` előfordulhat, hogy hívható meg, mielőtt a kommunikációs figyelőket is nyitva, vagy ahhoz, azok még nincs össze. Ha bármely szinkronizálásra szükség, azt kell használnia a végrehajtója. Az alábbiakban néhány gyakori:

* Egyes esetekben figyelői nem működőképes, amíg más információkat jön létre, vagy egyéb munkát. Az állapotmentes szolgáltatások, munkahelyi általában végezheti el a szolgáltatást a konstruktorban. Alatt is elvégezhető a `createServiceInstanceListeners()` hívja, vagy maga a figyelő a konstrukció részeként.
* Egyes esetekben a kód `runAsync` nem indul el, amíg a figyelők is nyitva. Ebben az esetben a további koordinációs szükség. A gyakori megoldás, hogy adja hozzá a jelzőt a figyelők. A jelző azt jelzi, ha a figyelők végzett. A `runAsync` metódus ellenőrzi ezt a tényleges munka folytatásához.

## <a name="stateless-service-shutdown"></a>Állapot nélküli szolgáltatás leállítása
Állapot nélküli szolgáltatás leállítása, ugyanilyen mintájú esetén meg kell majd, de a visszafelé:

1. Ezek az események párhuzamosan:
    - Bármely nyitott figyelők be van zárva. `CommunicationListener.closeAsync()` a minden egyes figyelő neve.
    - A megszakítási jogkivonat számára átadott `runAsync()` megszakadt. Ellenőrzésére a visszavonásra token `isCancelled` tulajdonság beolvasása `true`, és ha neve, a jogkivonat `throwIfCancellationRequested` metódus jelez egy `CancellationException`.
2. Ha `closeAsync()` minden egyes figyelő befejezése és `runAsync()` is befejeződött, a szolgáltatás `StatelessService.onCloseAsync()` metódus lehívásra kerül, ha telepítve. Ebben az esetben ez nem egy közös felülbírálás, de biztonságos erőforrások bezárása, állítsa le a háttérben történő feldolgozás, külső állapotmentést Befejezés vagy le a meglévő kapcsolatok használható.
3. Miután `StatelessService.onCloseAsync()` befejeződik, a szolgáltatás objektum destructed van.

## <a name="stateful-service-startup"></a>Az állapotalapú szolgáltatás indítása
Állapotalapú szolgáltatások egy minta hasonló állapotmentes szolgáltatások néhány módosításokkal rendelkezik.  Itt az események sorrendjének állapotalapú szolgáltatás elindítása:

1. A szolgáltatás létre lehet hozni az adatforrásnézetet.
2. `StatefulServiceBase.onOpenAsync()` nevezik. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.
3. Ezek az események párhuzamosan:
    - `StatefulServiceBase.createServiceReplicaListeners()` meghívták. 
      - Ha az elsődleges szolgáltatás, az összes visszaadott figyelői megnyitása. `CommunicationListener.openAsync()` a minden egyes figyelő neve.
      - Ha a másodlagos szolgáltatás, csak figyelői jelölésű `listenOnSecondary = true` megnyitása. Figyelők, amelyek nyitva a másodlagos adatbázist, akkor az ritkább.
    - Ha a szolgáltatás jelenleg egy elsődleges, a szolgáltatás által `StatefulServiceBase.runAsync()` metódust.
4. A replika figyelő minden után `openAsync()` meghívja a Befejezés gombra és `runAsync()` nevezik, `StatefulServiceBase.onChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

Hasonlóan állapotmentes szolgáltatások, az állapotalapú service-ben van a sorrendet, amelyben a figyelők vannak létrejött, és nem összehangolását és mikor `runAsync` nevezik. Ha koordinációs van szüksége, a megoldások olyan hasonlóan. Azonban az állapotalapú szolgáltatásból egy további esetében. Tegyük fel például, hogy a kiszolgálófarmban lévő a kommunikációs figyelőket hívások szükséges-e tartani belül néhány információt [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md). Mivel a kommunikációs figyelőket is megnyílik, mielőtt a megbízható gyűjtemények olvasható és írható, és mielőtt `runAsync` elindul, néhány további koordinációs szükség. A legegyszerűbb és leggyakoribb megoldás van a kommunikációs figyelőket a hibakódot ad vissza. Az ügyfél használja a hiba kódja funkcióját, és próbálkozzon újra a kéréssel.

## <a name="stateful-service-shutdown"></a>Az állapotalapú szolgáltatás leállítása
Állapotmentes szolgáltatások, például a leállítás során életciklus-események megegyeznek a rendszerindítás során, de fordított irányú. Ha egy állapotalapú szolgáltatás leáll, a következők történnek:

1. Ezek az események párhuzamosan:
    - Bármely nyitott figyelők be van zárva. `CommunicationListener.closeAsync()` a minden egyes figyelő neve.
    - A megszakítási jogkivonat számára átadott `runAsync()` meg lett szakítva. A megszakítási token hívása `isCancelled()` metódus beolvasása `true`, és hívása, a jogkivonat `throwIfCancellationRequested()` metódus jelez egy `OperationCanceledException`.
2. Után `closeAsync()` minden egyes figyelő befejezése és `runAsync()` is befejeződött, a szolgáltatás `StatefulServiceBase.onChangeRoleAsync()` nevezik. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

   > [!NOTE]  
   > Várakozás a `runAsync` befejezéséhez van szükség, csak ha a replika egy elsődleges másodpéldány.

3. Miután a `StatefulServiceBase.onChangeRoleAsync()` metódus befejezésekor a `StatefulServiceBase.onCloseAsync()` metódust. Ez a hívás ritka felülbírálást, de akkor érhető el.
3. Miután `StatefulServiceBase.onCloseAsync()` befejeződik, a szolgáltatás objektum destructed van.

## <a name="stateful-service-primary-swaps"></a>Az állapotalapú szolgáltatás elsődleges cseréje
Egy állapotalapú szolgáltatás futása nyitva vannak-e a kommunikációs figyelőket és a `runAsync` módszer neve csak az elsődleges replikára változott, hogy az állapotalapú szolgáltatások esetén. Másodlagos replikák össze, de nincs további hívásainak. Az állapotalapú szolgáltatás futása közben, a replika jelenleg az elsődleges módosíthatja. Állapot-nyilvántartó replika látható attól függ, hogy a replika alatt lefokozásra vagy előléptetett során a felcserélés életciklusa eseményeket.

### <a name="for-the-demoted-primary"></a>A lefokozott elsődleges
A Service Fabric kell az elsődleges másodpéldány, állítsa le az üzenetek feldolgozása, és állítsa le a háttérműveletek visszalép. Ez a lépés akkor hasonló, ha a szolgáltatás leállítása. Egy különbség, hogy a szolgáltatás nem destructed vagy le van zárva, mert egy másodlagos marad. Az alábbi események történnek:

1. Ezek az események párhuzamosan:
    - Bármely nyitott figyelők be van zárva. `CommunicationListener.closeAsync()` a minden egyes figyelő neve.
    - A megszakítási jogkivonat számára átadott `runAsync()` megszakadt. A megszakítási token ellenőrzése `isCancelled()` metódus beolvasása `true`. Ha neve, a jogkivonat `throwIfCancellationRequested()` metódus jelez egy `OperationCanceledException`.
2. Után `closeAsync()` minden egyes figyelő befejezése és `runAsync()` is befejeződött, a szolgáltatás `StatefulServiceBase.onChangeRoleAsync()` nevezik. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

### <a name="for-the-promoted-secondary"></a>Az előléptetett másodlagos
Hasonlóképpen a Service Fabric kell a másodlagos másodpéldány, amely megkezdeni a figyelést az üzenetek a keresztülhaladnak a hálózaton, és kezdje bármely háttér feladatok elvégzéséhez szükséges állapotban van. Ez a folyamat hasonlít a szolgáltatás létrehozásakor. A különbség, hogy maga a replika már létezik. Az alábbi események történnek:

1. Ezek az események párhuzamosan:
    - `StatefulServiceBase.createServiceReplicaListeners()` meghívták, és minden visszaadott figyelői megnyitása. `CommunicationListener.openAsync()` a minden egyes figyelő neve.
    - A szolgáltatás `StatefulServiceBase.runAsync()` metódust.
2. A replika figyelő minden után `openAsync()` meghívja a Befejezés gombra és `runAsync()` nevezik, `StatefulServiceBase.onChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran felülbírálja a szolgáltatásban.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Az állapotalapú szolgáltatás leállítása és elsődleges lefokozás során kapcsolatos gyakori hibák
A Service Fabric módosítja az elsődleges több okokból állapotalapú szolgáltatás. A leggyakoribb okok [fürt újraelosztás](service-fabric-cluster-resource-manager-balancing.md) és [az alkalmazásfrissítés](service-fabric-application-upgrade.md). Ezek a műveletek során nagyon fontos, hogy a szolgáltatás tiszteletben tartja a `cancellationToken`. Ez is vonatkozik normál szolgáltatás leállításakor például ha a szolgáltatást törölték.

Szolgáltatásokról, amelyek nem szabályszerűen kezeli lemondás is több problémákba ütközhetnek. Ezek a műveletek lassúak, mivel a Service Fabric szabályosan leállítja a szolgáltatások vár. Ez végső soron vezethet a sikertelen frissítések adott túllépi az időkorlátot és a visszaállítás. Hiba a megszakítási token tiszteletben is okozhatja imbalanced fürtök. Fürtök válhat egyenetlen, mert a csomópontok gyakran használt adatok beolvasása. Azonban a szolgáltatásokat nem lehet rebalanced mert máshol helyezze azokat túl sokáig tart. 

Mivel a szolgáltatások állapotalapú, akkor valószínű is, hogy használja-e a szolgáltatások [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md). A Service Fabric egy elsődleges lefokozása, az első dolog, ami történik esetén meg kell, hogy a mögöttes állapota írási visszavonva. Ennek eredménye, hogy egy második problémákat, amelyek hatással lehetnek a szolgáltatási életciklus. A visszatérési kivételek időzítése, és hogy a replika áthelyezik alapuló gyűjtemények vagy leáll. Fontos az ilyen kivételek megfelelően kezeli. 

A Service Fabric által kiváltott kivételekre is ki vagy állandó [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) vagy átmeneti [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception._fabric_transient_exception). Állandó kivételek legyen naplózva, és fel. Átmeneti kivételek követően újra megkísérelhető újrapróbálkozási logika alapján.

Tesztelése és ellenőrzése a Reliable Services fontos része a kivételeket, amelyeket a használatával kezeli a `ReliableCollections` szolgáltatás életciklus-események együtt. Azt javasoljuk, hogy mindig a szolgáltatás terhelés alatt. Is végre kell hajtania frissítések és [chaos tesztelés](service-fabric-controlled-chaos.md) üzemi környezetben üzembe helyezése előtt. Az alábbi alapvető lépések segítségével, győződjön meg arról, hogy a szolgáltatás megfelelően van megvalósítva, és megfelelően kezeli az életciklus-események.

## <a name="notes-on-service-lifecycle"></a>Tudnivalók a szolgáltatási életciklus
* Mindkét a `runAsync()` metódus és a `createServiceInstanceListeners/createServiceReplicaListeners` hívások nem kötelező. A szolgáltatás előfordulhat, hogy rendelkezik ezzel, mindkét, vagy sem. Például, ha a szolgáltatás felhasználói hívások válaszként a tevékenységeket, nincs szükség ahhoz, hogy a megvalósítása `runAsync()`. Csak a kommunikációs figyelőket és a kapcsolódó kódra szükség.  Hasonlóképpen létrehozása és a kommunikációs figyelőket adatszolgáltató nem kötelező. A szolgáltatás előfordulhat, hogy csak a teendő, háttérműveletek rendelkezik, így csak kell megvalósítani `runAsync()`.
* A szolgáltatás befejezi érvényes `runAsync()` sikeresen és visszatérési belőle. Ez a hiba a feltétel nem minősül. A háttérműveletek a szolgáltatás befejezési képviseli. Az állapotalapú Reliable Services `runAsync()` ehhez kell újra meghívni, ha a szolgáltatás elsődleges visszalép, és majd előléptetni elsődleges.
* Ha egy szolgáltatás kilép a `runAsync()` egy váratlan kivétel kiváltása, ez a hiba. A szolgáltatás objektum le van állítva, és egy állapot jelenik meg hibaüzenet.
* Bár ezek a módszerek visszatérése van nem korlátozott, írjanak azonnal elvesznek. Emiatt nem tudja végrehajtani a valódi munkát. Azt javasoljuk, hogy térjen vissza a megszakítási kérelem fogadása után a lehető leggyorsabban. Ha a szolgáltatás elfogadható időn belül nem válaszol az adott API-hívások, a Service Fabric kényszerített előfordulhat, hogy a szolgáltatás leáll. Ez általában csak alkalmazásfrissítések, vagy egy szolgáltatás törlésekor során történik. Ez az időkorlát értéke alapértelmezés szerint 15 percenként.
* Hibák a `onCloseAsync()` elérési eredményez `onAbort()` hívták. Ez a hívás a szolgáltatás karbantartása, és minden olyan erőforrásnál, amely azt állítják kiadás egy utolsó-alkalommal, elérhető legjobb lehetőség. Ez általában nevezzük, amikor egy állandó hiba lép fel a csomópont, vagy amikor a Service Fabric megbízhatóan nem tudja kezelni a szolgáltatáspéldány életciklus belső hiba miatt.
* `OnChangeRoleAsync()` van meghívva, amikor az állapot-nyilvántartó szolgáltatás replika-jal szerepkör, például elsődleges vagy másodlagos. Elsődleges replikára változott adta írási állapota (engedélyezettek létrehozásához és írásához megbízható gyűjteményeket). Másodlagos replikák adta olvasási állapota (csak Olvasás meglévő megbízható gyűjtemények). A legtöbb munkaelem egy állapotalapú szolgáltatás az elsődleges másodpéldány kellett végezni. Másodlagos replikák írásvédett érvényesítési, jelentéskészítésre, adatbányászat vagy más írásvédett feladatokat hajthat végre.

## <a name="next-steps"></a>További lépések
* [Bevezetés a Reliable Services használatába](service-fabric-reliable-services-introduction.md)
* [Megbízható Services gyors üzembe helyezés](service-fabric-reliable-services-quick-start-java.md)

