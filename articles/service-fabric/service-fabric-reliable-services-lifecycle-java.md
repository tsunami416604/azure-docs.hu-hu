---
title: Az Azure Service Fabric megbízható szolgáltatások életciklusa
description: Ismerje meg az életciklus-események egy Azure Service Fabric reliable services alkalmazás Java használatával állapotalapú és állapotmentes szolgáltatások.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 1d3be958a0649ed3e80df2d63adbdf0b91831dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639598"
---
# <a name="reliable-services-lifecycle"></a>A Reliable Services életciklusa
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

A Reliable Services az Azure Service Fabricben elérhető programozási modellek egyike. A megbízható szolgáltatások életciklusának megismerése során a legfontosabb, hogy megértse az alapvető életciklus-eseményeket. Az események pontos sorrendje a konfiguráció részleteitől függ. 

A Megbízható szolgáltatások életciklusa általában a következő eseményeket tartalmazza:

* Indításkor:
  * A szolgáltatások épülnek.
  * A szolgáltatások nak lehetősége van nulla vagy több hallgató létrehozására és visszaküldésére.
  * A visszaküldött figyelők meg vannak nyitva, a szolgáltatással való kommunikációhoz.
  * A szolgáltatás `runAsync` metódusa neve, így a szolgáltatás hosszú ideig futó vagy háttérmunkát végezhet.
* Leállítás kor:
  * A lemondási jogkivonat, amely átlett a `runAsync` törölt, és a figyelők zárva vannak.
  * Maga a szolgáltatásobjektum is destructed.

Az események sorrendje a Megbízható szolgáltatások kissé változhat attól függően, hogy a megbízható szolgáltatás állapot nélküli vagy állapotalapú. 

Emellett az állapotalapú szolgáltatások esetében az elsődleges csereforgatókönyvet is meg kell oldania. Ebben a sorrendben az elsődleges szerepkör átkerül egy másik replika (vagy jön vissza) a szolgáltatás leállítása nélkül. 

Végül, meg kell gondolni hiba vagy hiba feltételeket.

## <a name="stateless-service-startup"></a>Állapotnélküli szolgáltatás indítása
Egy állapotmentes szolgáltatás életciklusa meglehetősen egyszerű. Itt van az események sorrendje:

1. A szolgáltatás megvan építve.
2. Ezek az események párhuzamosan fordulnak elő:
    - `StatelessService.createServiceInstanceListeners()`meghívásra kerül, és a visszaküldött figyelők megnyílnak. `CommunicationListener.openAsync()`minden hallgatónak meg kell hívni.
    - A szolgáltatás `runAsync` metódusa`StatelessService.runAsync()`( ) neve.
3. Ha van, a szolgáltatás `onOpenAsync` saját metódusa van hivatott. Pontosabban, `StatelessService.onOpenAsync()` hívják. Ez egy nem gyakori felülbírálás, de elérhető.

Fontos megjegyezni, hogy nincs rendelés a hallgatók létrehozására és megnyitására `runAsync`irányuló hívás és a hívás között. Előfordulhat, hogy `runAsync` a figyelők indítás előtt megnyílnak. Hasonlóképpen `runAsync` lehet meghívni, mielőtt a kommunikációs figyelők meg vannak nyitva, vagy mielőtt még létre. Ha bármilyen szinkronizálásra van szükség, azt a végrehajtónak kell elvégeznie. Íme néhány gyakori megoldás:

* Néha a figyelők nem működhetnek, amíg más információk nem jönnek létre, vagy más munka nem történik. Az állapotmentes szolgáltatások esetében ez a munka általában a szolgáltatás konstruktorában végezhető el. Meg lehet tenni `createServiceInstanceListeners()` a hívás során, vagy részeként az építőiparban a hallgató is.
* Néha a `runAsync` kód nem indul el, amíg a hallgatók nyitva vannak. Ebben az esetben további koordinációra van szükség. A gyakori megoldás az, hogy egy zászlót a figyelők. A jelző jelzi, ha a figyelők befejezték. A `runAsync` módszer ezt ellenőrzi, mielőtt folytatná a tényleges munkát.

## <a name="stateless-service-shutdown"></a>Állapotnélküli szolgáltatás leállítása
Állapotmentes szolgáltatás leállításakor ugyanez támarányú, de fordítva történik a minta:

1. Ezek az események párhuzamosan fordulnak elő:
    - Minden nyitott hallgató zárva van. `CommunicationListener.closeAsync()`minden hallgatónak meg kell hívni.
    - A lemondási jogkivonat, amely átlett `runAsync()` adták, törlődik. A törlési `isCancelled` jogkivonat `true`tulajdonságának ellenőrzése visszaadja a `throwIfCancellationRequested` vissza, `CancellationException`és ha meghívják, a token metódusa a .
2. Amikor `closeAsync()` befejezi az egyes `runAsync()` figyelők és befejezi, `StatelessService.onCloseAsync()` a szolgáltatás metódusa neve, ha jelen van. Ez ismét nem általános felülbírálás, de használható az erőforrások biztonságos bezárására, a háttérfeldolgozás leállítására, a külső állapot mentésjéhez vagy a meglévő kapcsolatok bezárásához.
3. A `StatelessService.onCloseAsync()` befejezés után a szolgáltatásobjektum destructed.

## <a name="stateful-service-startup"></a>Állapotalapú szolgáltatás indítása
Az állapotalapú szolgáltatások mintája hasonló az állapotmentes szolgáltatásokhoz, néhány változtatással.  Itt van az események sorrendje egy állapotalapú szolgáltatás indításához:

1. A szolgáltatás megvan építve.
2. `StatefulServiceBase.onOpenAsync()`a neve. Ezt a hívást a szolgáltatás gyakran nem bírálja felül.
3. Ezek az események párhuzamosan fordulnak elő:
    - `StatefulServiceBase.createServiceReplicaListeners()`a meghívásra kerül. 
      - Ha a szolgáltatás elsődleges szolgáltatás, az összes visszaadott figyelők meg vannak nyitva. `CommunicationListener.openAsync()`minden hallgatónak meg kell hívni.
      - Ha a szolgáltatás másodlagos szolgáltatás, csak `listenOnSecondary = true` a megnyitottként megjelölt figyelők. Miután a hallgatók, hogy nyitott a másodlagos kevésbé gyakori.
    - Ha a szolgáltatás jelenleg elsődleges, a `StatefulServiceBase.runAsync()` szolgáltatás metódusa neve meg van hívva.
4. Miután az összes replika figyelő `openAsync()` hívások befejezéséhez, és `runAsync()` hívják, `StatefulServiceBase.onChangeRoleAsync()` a neve. Ezt a hívást a szolgáltatás gyakran nem bírálja felül.

Az állapotmentes szolgáltatásokhoz hasonlóan az állapotalapú szolgáltatásban nincs koordináció a figyelők létrehozásának `runAsync` és megnyitása sorrendje között, és mikor hívják meg. Ha koordinációra van szüksége, a megoldások nagyjából ugyanazok. De van még egy eset az állami szolgálatra. Tegyük fel, hogy a kommunikációs figyelőkhöz érkező hívások néhány megbízható gyűjteményben tárolt információkat [igényelnek.](service-fabric-reliable-services-reliable-collections.md) Mivel a kommunikációs figyelők megnyílhatnak, mielőtt a megbízható `runAsync` gyűjtemények olvashatóvagy írható, és indítás előtt néhány további koordinációra van szükség. A legegyszerűbb és leggyakoribb megoldás az, hogy a kommunikációs figyelők hibakódot adnak vissza. Az ügyfél a hibakódot használja a kérelem újrapróbálkozásához.

## <a name="stateful-service-shutdown"></a>Állapotalapú szolgáltatás leállítása
Az állapotmentes szolgáltatásokhoz hasonlóan a leállítás során az életciklus-események megegyeznek az indítássorán, de megfordulnak. Állapotalapú szolgáltatás leállításakor a következő események következnek be:

1. Ezek az események párhuzamosan fordulnak elő:
    - Minden nyitott hallgató zárva van. `CommunicationListener.closeAsync()`minden hallgatónak meg kell hívni.
    - A lemondási jogkivonat, amely átlett `runAsync()` adták, törlődik. A törlési jogkivonat `isCancelled()` metódusának `true`hívása visszaadja a `throwIfCancellationRequested()` függvényt, `OperationCanceledException`és ha meghívják, a token metódusa egy .
2. Befejezése `closeAsync()` után az egyes `runAsync()` figyelők és befejezi, `StatefulServiceBase.onChangeRoleAsync()` a szolgáltatás neve. Ezt a hívást a szolgáltatás gyakran nem bírálja felül.

   > [!NOTE]  
   > A `runAsync` befejezésre való várakozás csak akkor szükséges, ha ez a replika elsődleges kópia.

3. A `StatefulServiceBase.onChangeRoleAsync()` módszer befejezése után `StatefulServiceBase.onCloseAsync()` a metódus neve meg történik. Ez a hívás nem gyakori felülbírálás, de elérhető.
3. A `StatefulServiceBase.onCloseAsync()` befejezés után a szolgáltatásobjektum destructed.

## <a name="stateful-service-primary-swaps"></a>Állapotalapú szolgáltatási elsődleges swapügyletek
Amíg egy állapotalapú szolgáltatás fut, a `runAsync` kommunikációs figyelők megnyílnak, és a metódus csak az adott állapotalapú szolgáltatások elsődleges replikáihoz van meghívva. Másodlagos replikák vannak kialakítva, de nem lát további hívásokat. Amíg egy állapotalapú szolgáltatás fut, a replika, amely jelenleg az elsődleges módosíthatja. Az állapotalapú replika által látható életciklus-események attól függnek, hogy a lefokozott vagy előléptetett replika-e a felcserélés során.

### <a name="for-the-demoted-primary"></a>A lefokozott elsődleges
A Service Fabric-nek szüksége van az elsődleges replika, amely lefokozták az üzenetek feldolgozásának leállításához és a háttérmunka leállításához. Ez a lépés hasonló a szolgáltatás leállításakor. Az egyik különbség az, hogy a szolgáltatás nem destructed vagy zárt, mert továbbra is másodlagos. Az alábbi események történnek:

1. Ezek az események párhuzamosan fordulnak elő:
    - Minden nyitott hallgató zárva van. `CommunicationListener.closeAsync()`minden hallgatónak meg kell hívni.
    - A lemondási jogkivonat, amely átlett `runAsync()` adták, törlődik. A törlési jogkivonat `isCancelled()` metódusának `true`ellenőrzése visszaadja a visszaadást. Ha meghívják, a `throwIfCancellationRequested()` token metódusa egy `OperationCanceledException`.
2. Befejezése `closeAsync()` után az egyes `runAsync()` figyelők és befejezi, `StatefulServiceBase.onChangeRoleAsync()` a szolgáltatás neve. Ezt a hívást a szolgáltatás gyakran nem bírálja felül.

### <a name="for-the-promoted-secondary"></a>Az előléptetett másodlagos
Hasonlóképpen a Service Fabric szüksége van a másodlagos replika, amely elő segítette az üzenetek figyelése a hálózaton, és elindítani a háttérfeladatokat, amelyeket el kell végeznie. Ez a folyamat hasonló a szolgáltatás létrehozásakor. A különbség az, hogy maga a replika már létezik. Az alábbi események történnek:

1. Ezek az események párhuzamosan fordulnak elő:
    - `StatefulServiceBase.createServiceReplicaListeners()`meghívásra kerül, és a visszaküldött figyelők megnyílnak. `CommunicationListener.openAsync()`minden hallgatónak meg kell hívni.
    - A szolgáltatás `StatefulServiceBase.runAsync()` metódusa neve.
2. Miután az összes replika figyelő `openAsync()` hívások befejezéséhez, és `runAsync()` hívják, `StatefulServiceBase.onChangeRoleAsync()` a neve. Ezt a hívást a szolgáltatás gyakran nem bírálja felül.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Gyakori problémák az állapotalapú szolgáltatás leállítása és az elsődleges lefokozás során
A Service Fabric több okból is módosítja az állapotalapú szolgáltatás elsődleges állapotát. A leggyakoribb okok a [fürt újraegyensúlyozása](service-fabric-cluster-resource-manager-balancing.md) és [az alkalmazások frissítése](service-fabric-application-upgrade.md). Ezekben a műveletekben fontos, hogy a `cancellationToken`szolgáltatás tiszteletben tartsa a . Ez a normál szolgáltatásleállításakor is érvényes, például ha a szolgáltatást törölték.

A lemondást nem kezelő szolgáltatások számos problémát tapasztalhatnak. Ezek a műveletek lassúak, mert a Service Fabric megvárja, hogy a szolgáltatások leállnak szabályosan. Ez végső soron sikertelen frissítésekhez vezethet, amelyek időkimaradáshoz és visszaállításhoz vezethetnek. A megszakítási jogkivonat teljesítésének elmulasztása is kiegyensúlyozatlan fürtöket okozhat. A fürtök kiegyensúlyozatlanná válnak, mert a csomópontok felforrósodnak. A szolgáltatások azonban nem kiegyensúlyozhatók újra, mert túl sokáig tart, amíg áthelyezik őket máshová. 

Mivel a szolgáltatások állapotalapúak, az is valószínű, hogy a szolgáltatások [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md)használatát használják. A Service Fabric, amikor egy elsődleges lefokozták, az egyik első dolog, ami történik, hogy az írási hozzáférést az alapul szolgáló állapot hoz vissza vonják. Ez egy második problémakészlethez vezet, amelyek hatással lehetnek a szolgáltatás életciklusára. A gyűjtemények az időzítés és a replika áthelyezése vagy leállítása alapján adnak vissza kivételeket. Fontos, hogy ezeket a kivételeket megfelelően kezelje. 

A Service Fabric által okozott kivételek vagy [állandóak`FabricException`( )](https://docs.microsoft.com/java/api/system.fabric.exception) vagy [átmenetiek`FabricTransientException`( )](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Az állandó kivételeket naplózni kell, és el kell dobni. Az átmeneti kivételek újrapróbálkozási logika alapján újrapróbálkozási logika alapján újrapróbálkozási.

A megbízható szolgáltatások tesztelésének és érvényesítésének fontos része a kivételek `ReliableCollections` kezelése, amelyek a szolgáltatás életciklus-eseményeivel együtt történő használatából származnak. Azt javasoljuk, hogy mindig töltse le a szolgáltatást terhelés alatt. Az éles környezetben való üzembe helyezés előtt is el kell végeznie a frissítéseket és [a káosztesztelést.](service-fabric-controlled-chaos.md) Ezek az alapvető lépések segítenek annak biztosításában, hogy a szolgáltatás megfelelően legyen megvalósítva, és hogy megfelelően kezelje az életciklus-eseményeket.

## <a name="notes-on-service-lifecycle"></a>Megjegyzések a szolgálati életciklushoz
* Mind `runAsync()` a metódus, mind a `createServiceInstanceListeners/createServiceReplicaListeners` hívások nem kötelezőek. Lehet, hogy egy szolgáltatásnak mindkettő, vagy egyik sem. Ha például a szolgáltatás a felhasználói hívásokra adott válaszként végzi el `runAsync()`a munkáját, nincs szükség a megvalósítására. Csak a kommunikációs figyelők és a hozzájuk tartozó kód szükséges.  Hasonlóképpen a kommunikációs figyelők létrehozása és visszaadása nem kötelező. Előfordulhat, hogy a szolgáltatásnak csak háttérmunkája van, ezért csak a . `runAsync()`
* Érvényes egy szolgáltatás sikeres befejezéséhez `runAsync()` és visszatéréséhez. Ez nem tekinthető hibaállapotnak. A szolgáltatás befejezésének háttérmunkáját képviseli. Állapotalapú megbízható szolgáltatások `runAsync()` esetén a szolgáltatás lelettesített elsődleges, majd előléptetik vissza az elsődleges.
* Ha egy szolgáltatás `runAsync()` váratlan kivétellel lép ki, ez egy hiba. A szolgáltatásobjektum leáll, és a rendszer állapothibát jelez.
* Bár nincs időkorlát a visszatérés re ezeket a módszereket, akkor azonnal elveszíti a képességét, hogy írjon. Ezért nem tud szokták befejezni a valódi munkát. Javasoljuk, hogy a lemondási kérelem kézhezvételekor a lehető leggyorsabban térjen vissza. Ha a szolgáltatás nem válaszol ezekre az API-hívásokra ésszerű időn alatt, a Service Fabric előfordulhat, hogy erőszakkal megszünteti a szolgáltatást. Ez általában csak az alkalmazás frissítésekor vagy egy szolgáltatás törlésekor fordul elő. Ez az időhosszabbítás alapértelmezés szerint 15 perc.
* Az elérési `onCloseAsync()` út hibái `onAbort()` a megnevezést eredményezik. Ez a hívás egy utolsó esélyt, a legjobb megoldás lehetőséget a szolgáltatás, hogy tisztítsák meg, és engedje el az erőforrásokat, hogy azt állította. Ez általában akkor hívható, ha egy állandó hiba észlelése a csomóponton, vagy ha a Service Fabric nem tudja megbízhatóan kezelni a szolgáltatáspéldány életciklusa belső hibák miatt.
* `OnChangeRoleAsync()`akkor hívják meg, ha az állapotalapú szolgáltatásreplika szerepkört módosít, például elsődleges vagy másodlagos. Elsődleges replikák kapnak írási állapot (engedélyezett ek létrehozása és írása megbízható gyűjtemények). Másodlagos replikák olvasási állapot (csak a meglévő megbízható gyűjtemények olvasni). Az állapotalapú szolgáltatásban végzett legtöbb munka az elsődleges replika. A másodlagos replikák csak olvasható érvényesítést, jelentésgenerálást, adatbányászatot vagy más írásvédett feladatokat hajthatnak végre.

## <a name="next-steps"></a>További lépések
* [Bevezetés a megbízható szolgáltatásokba](service-fabric-reliable-services-introduction.md)
* [Megbízható szolgáltatások rövid útmutató](service-fabric-reliable-services-quick-start-java.md)

