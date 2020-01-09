---
title: Az Azure Service Fabric Reliable Services életciklusa
description: Ismerkedjen meg az Azure Service Fabric Reliable Services alkalmazásban a Java használatával állapot-nyilvántartó és állapot nélküli szolgáltatásokkal kapcsolatos életciklus-eseményekkel.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 1d3be958a0649ed3e80df2d63adbdf0b91831dbd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639598"
---
# <a name="reliable-services-lifecycle"></a>A Reliable Services életciklusa
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services az Azure Service Fabric-ban elérhető programozási modellek egyike. A Reliable Services életciklusának megismerése során a legfontosabb az alapszintű életciklus eseményeinek megismerése. Az események pontos sorrendje a konfigurációs adatoktól függ. 

Általánosságban elmondható, hogy a Reliable Services életciklusa a következő eseményeket tartalmazza:

* Indításkor:
  * A szolgáltatások építése megtörténik.
  * A szolgáltatásokból lehetőség van nulla vagy több figyelő létrehozására és visszaadására.
  * A szolgáltatással folytatott kommunikációhoz minden visszaadott figyelő meg van nyitva.
  * A szolgáltatás `runAsync` metódusának neve, így a szolgáltatás hosszan futó vagy háttérbeli munkát végezhet.
* Leállítás közben:
  * A `runAsync`nak átadott visszavonási tokent megszakították, és a figyelők bezárulnak.
  * Maga a szolgáltatási objektum megsemmisült.

A Reliable Services eseményeinek sorrendje némileg változhat attól függően, hogy a megbízható szolgáltatás állapota vagy állapota nem megfelelő-e. 

Az állapot-nyilvántartó szolgáltatások esetében az elsődleges swap-forgatókönyvet is meg kell címezni. Ebben a sorozatban az elsődleges szerepkört egy másik replikába (vagy visszatérve) helyezi át a szolgáltatás leállítása nélkül. 

Végül a hiba-vagy meghibásodási feltételeket kell meggondolni.

## <a name="stateless-service-startup"></a>Állapot nélküli szolgáltatás indítása
Az állapot nélküli szolgáltatások életciklusa meglehetősen egyszerű. Az események sorrendje:

1. A szolgáltatás építése megtörténik.
2. Ezek az események párhuzamosan történnek:
    - a rendszer meghívja a `StatelessService.createServiceInstanceListeners()`t, és megnyit minden visszaadott figyelőt. `CommunicationListener.openAsync()` hívása minden Figyelőnél megtörténik.
    - A szolgáltatás `runAsync` metódusa (`StatelessService.runAsync()`) hívása.
3. Ha van ilyen, a szolgáltatás saját `onOpenAsync` metódusát hívja meg. Pontosabban `StatelessService.onOpenAsync()` neve. Ez egy nem gyakori felülbírálás, de elérhető.

Fontos megjegyezni, hogy a figyelők létrehozására és megnyitására irányuló hívás, valamint a `runAsync`meghívása között nincs megrendelés. Előfordulhat, hogy a figyelők megnyílnak a `runAsync` elindítása előtt. Hasonlóképpen előfordulhat, hogy a rendszer meghívja a `runAsync` a kommunikációs figyelők megnyitása előtt, vagy még a megépítésük előtt. Ha bármilyen szinkronizálásra van szükség, azt a végrehajtónak kell elvégeznie. Íme néhány gyakori megoldás:

* Néha a figyelők nem működhetnek addig, amíg más adatokat nem hoznak létre, vagy más munkát nem végeznek. Az állapot nélküli szolgáltatások esetében általában a szolgáltatás konstruktorában végezhető el a munka. A `createServiceInstanceListeners()` hívása során vagy maga a figyelő felépítésének részeként is elvégezhető.
* Előfordulhat, hogy a `runAsync` kódja nem indul el, amíg a figyelők meg nem nyílnak. Ebben az esetben további koordinációra van szükség. Egy gyakori megoldás egy jelző hozzáadása a figyelőkhöz. A jelző jelzi, hogy mikor fejeződött be a figyelők. A `runAsync` metódus ellenőrzi ezt, mielőtt folytatná a tényleges munkát.

## <a name="stateless-service-shutdown"></a>Állapot nélküli szolgáltatás leállítása
Állapot nélküli szolgáltatás leállításakor ugyanezt a mintát követjük, de fordítva:

1. Ezek az események párhuzamosan történnek:
    - Minden nyitott figyelő le van zárva. `CommunicationListener.closeAsync()` hívása minden Figyelőnél megtörténik.
    - A `runAsync()`nak átadott törlési tokent megszakították. A lemondási token `isCancelled` tulajdonságának beolvasása `true`, és ha meg van nevezni, a jogkivonat `throwIfCancellationRequested` metódusa `CancellationException`.
2. Ha `closeAsync()` befejezi az egyes figyelőket, és `runAsync()` is befejeződik, a szolgáltatás `StatelessService.onCloseAsync()` metódusa is meghívásra kerül, ha van ilyen. Ez nem egy gyakori felülbírálás, de az erőforrások biztonságos bezárásához, a háttérben történő feldolgozás befejezéséhez, a külső állapot mentéséhez vagy a meglévő kapcsolatok bezárásához használható.
3. `StatelessService.onCloseAsync()` befejeződése után a szolgáltatás objektum megsemmisül.

## <a name="stateful-service-startup"></a>Állapot-nyilvántartó szolgáltatás indítása
Az állapot-nyilvántartó szolgáltatások olyan mintázattal rendelkeznek, amely hasonló az állapot nélküli szolgáltatásokhoz, néhány módosítással.  Itt látható az állapot-nyilvántartó szolgáltatás indításához szükséges események sorrendje:

1. A szolgáltatás építése megtörténik.
2. `StatefulServiceBase.onOpenAsync()` neve. Ez a hívás általában nincs felülbírálva a szolgáltatásban.
3. Ezek az események párhuzamosan történnek:
    - a rendszer meghívja a `StatefulServiceBase.createServiceReplicaListeners()`. 
      - Ha a szolgáltatás elsődleges szolgáltatás, a rendszer minden visszaadott figyelőt megnyit. `CommunicationListener.openAsync()` hívása minden Figyelőnél megtörténik.
      - Ha a szolgáltatás másodlagos szolgáltatás, csak a `listenOnSecondary = true`ként megjelölt figyelők nyílnak meg. A formátumú másodlagos zónák megnyitott figyelők kevésbé gyakoriak.
    - Ha a szolgáltatás jelenleg elsődleges, a szolgáltatás `StatefulServiceBase.runAsync()` metódusát hívja meg.
4. A replika-figyelő összes `openAsync()` hívásának befejezése után a rendszer meghívja a `runAsync()`t, `StatefulServiceBase.onChangeRoleAsync()` neve. Ez a hívás általában nincs felülbírálva a szolgáltatásban.

Az állapot nélküli szolgáltatások esetében az állapot-nyilvántartó szolgáltatáshoz hasonlóan nincs koordináció a figyelők létrehozási és megnyitási sorrendje és a `runAsync` meghívása között. Ha koordinációra van szüksége, a megoldások sokkal ugyanazok. De van még egy további eset az állapot-nyilvántartó szolgáltatáshoz. Tegyük fel, hogy a kommunikációs figyelőkre érkező hívások esetében szükséges, hogy az információk bizonyos [megbízható gyűjteményekben](service-fabric-reliable-services-reliable-collections.md)legyenek tárolva. Mivel előfordulhat, hogy a kommunikációs figyelők megnyithatók, mielőtt a megbízható gyűjtemények olvashatók vagy írhatók, és a `runAsync` elindítása előtt további koordinációra van szükség. A legegyszerűbb és leggyakoribb megoldás, ha a kommunikációs figyelők hibakódot adnak vissza. Az ügyfél a hibakód használatával tudja megismételni a kérést.

## <a name="stateful-service-shutdown"></a>Állapot-nyilvántartó szolgáltatás leállítása
Az állapot nélküli szolgáltatásokhoz hasonlóan a leállítás során az életciklus eseményei megegyeznek az indítás során, de fordítva. Állapot-nyilvántartó szolgáltatás leállításakor a következő események történnek:

1. Ezek az események párhuzamosan történnek:
    - Minden nyitott figyelő le van zárva. `CommunicationListener.closeAsync()` hívása minden Figyelőnél megtörténik.
    - A `runAsync()`nak átadott törlési tokent megszakították. A lemondási token `isCancelled()` metódusának hívása `true`, és ha a neve, a jogkivonat `throwIfCancellationRequested()` metódusa egy `OperationCanceledException`t küld.
2. `closeAsync()` befejezését követően az egyes figyelőket, és `runAsync()` is befejeződik, a szolgáltatás `StatefulServiceBase.onChangeRoleAsync()` is meghívja. Ez a hívás általában nincs felülbírálva a szolgáltatásban.

   > [!NOTE]  
   > A `runAsync` befejezésére való várakozás csak akkor szükséges, ha ez a replika elsődleges replika.

3. A `StatefulServiceBase.onChangeRoleAsync()` metódus befejeződése után a rendszer a `StatefulServiceBase.onCloseAsync()` metódust hívja meg. Ez a hívás nem gyakori felülbírálás, de elérhető.
3. `StatefulServiceBase.onCloseAsync()` befejeződése után a szolgáltatás objektum megsemmisül.

## <a name="stateful-service-primary-swaps"></a>Állapot-nyilvántartó szolgáltatás elsődleges felcserélése
Ha egy állapot-nyilvántartó szolgáltatás fut, a rendszer megnyit egy kommunikációs figyelőt, és a `runAsync` metódust csak az állapot-nyilvántartó szolgáltatások elsődleges replikái számára hívja meg. A másodlagos replikák létre vannak építve, de nem találhatók további hívások. Egy állapot-nyilvántartó szolgáltatás fut, a jelenleg az elsődleges replika is változhat. Az állapot-nyilvántartó replika által megtekinthető életciklus-események attól függnek, hogy a replika le van-e lefokozva vagy előléptetve a swap során.

### <a name="for-the-demoted-primary"></a>A lefokozott elsődleges
Service Fabric szüksége van az elsődleges replikára, amely le van fokozni az üzenetek feldolgozásának leállításához és a háttérbeli munka leállításához. Ez a lépés hasonló a szolgáltatás leállításakor. Az egyik különbség, hogy a szolgáltatás nincs elpusztulva vagy lezárva, mert másodlagosként marad. Az alábbi események történnek:

1. Ezek az események párhuzamosan történnek:
    - Minden nyitott figyelő le van zárva. `CommunicationListener.closeAsync()` hívása minden Figyelőnél megtörténik.
    - A `runAsync()`nak átadott törlési tokent megszakították. A lemondási token `isCancelled()` metódusának ellenőrzését `true`adja vissza. Ha az hívása megtörtént, a jogkivonat `throwIfCancellationRequested()` metódusa egy `OperationCanceledException`t dob.
2. `closeAsync()` befejezését követően az egyes figyelőket, és `runAsync()` is befejeződik, a szolgáltatás `StatefulServiceBase.onChangeRoleAsync()` is meghívja. Ez a hívás általában nincs felülbírálva a szolgáltatásban.

### <a name="for-the-promoted-secondary"></a>A előléptetett másodlagos
Hasonlóképpen Service Fabric szüksége van arra a másodlagos másodpéldányra, amelyet a rendszer a hálózati üzenetek figyelésének megkezdéséhez, valamint a befejezéshez szükséges összes háttér-feladat elindításához előléptet. Ez a folyamat hasonló a szolgáltatás létrehozásakor. A különbség az, hogy maga a replika már létezik. Az alábbi események történnek:

1. Ezek az események párhuzamosan történnek:
    - a rendszer meghívja a `StatefulServiceBase.createServiceReplicaListeners()`, és megnyit minden visszaadott figyelőt. `CommunicationListener.openAsync()` hívása minden Figyelőnél megtörténik.
    - A szolgáltatás `StatefulServiceBase.runAsync()` metódusának hívása.
2. A replika-figyelő összes `openAsync()` hívásának befejezése után a rendszer meghívja a `runAsync()`t, `StatefulServiceBase.onChangeRoleAsync()` neve. Ez a hívás általában nincs felülbírálva a szolgáltatásban.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Gyakori problémák az állapot-nyilvántartó szolgáltatás leállítása és az elsődleges lefokozás során
Service Fabric több okból is megváltoztatja az állapot-nyilvántartó szolgáltatás elsődleges állapotát. A leggyakoribb okok a [fürtök újraelosztása](service-fabric-cluster-resource-manager-balancing.md) és az [alkalmazások frissítése](service-fabric-application-upgrade.md). A műveletek során fontos, hogy a szolgáltatás tiszteletben tartsák a `cancellationToken`. Ez a szolgáltatás normál leállításakor is érvényes, például ha törölték a szolgáltatást.

A megszakítást nem kezelő szolgáltatások számos problémát tapasztalhatnak. Ezek a műveletek lassúak, mert Service Fabric megvárja, amíg a szolgáltatások szabályosan leállnak. Ez végső soron sikertelen frissítésekhez vezethet, amely időtúllépést és visszaállítást eredményezett. A lemondási token betartásának elmulasztása miatt kiegyensúlyozatlan fürtök is lehetnek. A fürtök kiegyensúlyozatlan állapotba kerülnek, mivel a csomópontok melegek lesznek. A szolgáltatásokat azonban nem lehet kiegyensúlyozni, mert túl sokáig tart ahhoz, hogy máshová helyezze őket. 

Mivel a szolgáltatások állapot-nyilvántartó, az is valószínű, hogy a szolgáltatások [megbízható gyűjteményeket](service-fabric-reliable-services-reliable-collections.md)használnak. Service Fabric az elsődleges lefokozása után az egyik első dolog, ami megtörténik, az írási hozzáférés az alapul szolgáló állapothoz visszavonva. Ez egy második olyan problémát eredményez, amely hatással lehet a szolgáltatás életciklusára. A gyűjtemények az időzítés alapján adják vissza a kivételeket, és azt, hogy a replika áthelyezése vagy leállítása folyamatban van-e. Fontos, hogy megfelelően kezelje ezeket a kivételeket. 

A Service Fabric által okozott kivételek állandók [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) vagy átmenetiek [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). A végleges kivételeket naplózni és dobni kell. Az újrapróbálkozási logikán alapuló átmeneti kivételek is megismételhetők.

A Reliable Services tesztelésének és ellenőrzésének fontos része a szolgáltatás-életciklus eseményeivel együtt a `ReliableCollections` használatával járó kivételek kezelése. Javasoljuk, hogy mindig a terhelés alatt futtassa a szolgáltatást. Az éles környezetbe való üzembe helyezés előtt frissítéseket és [Chaos-teszteket](service-fabric-controlled-chaos.md) is végre kell hajtania. Ezek az alapszintű lépések biztosítják a szolgáltatás megfelelő megvalósítását, valamint az életciklus-események megfelelő kezelését.

## <a name="notes-on-service-lifecycle"></a>A szolgáltatás életciklusával kapcsolatos megjegyzések
* A `runAsync()` metódus és a `createServiceInstanceListeners/createServiceReplicaListeners` hívások sem választhatók. Egy szolgáltatás lehet egy, mindkettő vagy egyik sem. Ha például a szolgáltatás az összes munkáját a felhasználói hívásokra válaszol, nincs szükség a `runAsync()`megvalósítására. Csak a kommunikációs figyelők és a hozzájuk tartozó kódok szükségesek.  Hasonlóképpen, a kommunikációs figyelők létrehozása és visszaküldése nem kötelező. Előfordulhat, hogy a szolgáltatás csak a háttérben dolgozik, ezért csak `runAsync()`megvalósítására van szükség.
* A szolgáltatás `runAsync()` sikeres végrehajtásához és visszaküldéséhez érvényes. Ez nem minősül meghibásodási feltételnek. A szolgáltatás befejezésének hátterét képviseli. Állapot-nyilvántartó Reliable Services esetén újra kell hívni a `runAsync()`, ha a szolgáltatás le van lefokozva az elsődlegestől, majd az elsődlegesre lett előléptetve.
* Ha egy szolgáltatás kilép a `runAsync()`ról egy váratlan kivétel bedobásával, ez a hiba. A szolgáltatási objektum le van állítva, és a rendszer állapottal kapcsolatos hibát jelez.
* Bár az ilyen metódusokból való visszatéréshez nincs időkorlát, azonnal elveszíti az írás lehetőségét. Ezért nem végezheti el a valódi munkát. Javasoljuk, hogy a törlési kérés fogadásakor a lehető leggyorsabban térjen vissza. Ha a szolgáltatás ésszerű időn belül nem válaszol ezekre az API-hívásokra, Service Fabric kényszerítheti a szolgáltatás bezárását. Ez általában csak az alkalmazások frissítése vagy a szolgáltatás törlésekor történik. Alapértelmezés szerint ez az időkorlát 15 perc.
* A `onCloseAsync()` elérési úton fellépő hibák a `onAbort()` hívását eredményezik. Ez a hívás egy utolsó esélyes, legjobb lehetőség arra, hogy a szolgáltatás kiürítse és felszabadítsa az általuk igényelt erőforrásokat. Ez általában akkor fordul elő, ha a csomóponton állandó hibát észlel, vagy ha Service Fabric belső meghibásodások miatt nem tudja megbízhatóan kezelni a szolgáltatási példány életciklusát.
* a `OnChangeRoleAsync()` akkor lesz meghívva, ha az állapot-nyilvántartó szolgáltatás replikája megváltoztatja a szerepkört, például az elsődleges vagy a másodlagos. Az elsődleges replikák írási állapotot kapnak (a megbízható gyűjtemények létrehozásához és írásához engedélyezett). A másodlagos replikák olvasási állapotot kapnak (csak a meglévő megbízható gyűjteményekből olvashatók be). Egy állapot-nyilvántartó szolgáltatásban a legtöbb munka az elsődleges replikán történik. A másodlagos replikák csak olvasási ellenőrzés, jelentéskészítés, adatbányászat vagy más írásvédett feladatok végrehajtására használhatók.

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Reliable Servicesba](service-fabric-reliable-services-introduction.md)
* [Reliable Services rövid útmutató](service-fabric-reliable-services-quick-start-java.md)

