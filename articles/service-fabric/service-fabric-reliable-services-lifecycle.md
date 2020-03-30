---
title: A megbízható szolgáltatások életciklusának áttekintése
description: Ismerje meg az életciklus-események egy Azure Service Fabric reliable services alkalmazás állapotalapú és állapotmentes szolgáltatások.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: fe338ca3f25cd606da7f95f6c9437a3cd3dc4e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258277"
---
# <a name="reliable-services-lifecycle-overview"></a>Megbízható szolgáltatások életciklusának áttekintése
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

Amikor az Azure Service Fabric megbízható szolgáltatások életciklusára gondol, az életciklus alapjai a legfontosabbak. Az életciklus általában a következőket tartalmazza:

- Indításkor:
  - A szolgáltatások épülnek.
  - A szolgáltatások lehetőséget kapnak nulla vagy több hallgató létrehozására és visszaküldésére.
  - A visszaküldött figyelők meg vannak nyitva, így a szolgáltatással való kommunikáció.
  - A szolgáltatás **RunAsync** metódusa neve, amely lehetővé teszi a szolgáltatás számára, hogy hosszú ideig futó feladatokat vagy háttérmunkát végezzen.
- Leállítás kor:
  - A **RunAsync-nek** átadott megszakítási jogkivonat megszakad, és a figyelők be vannak zárva.
  - A figyelők bezárása után maga a szolgáltatásobjektum is destructed.

Vannak részletek körül a pontos sorrendje ezeket az eseményeket. Az események sorrendje kissé változhat attól függően, hogy a megbízható szolgáltatás állapot nélküli vagy állapotalapú. Emellett az állapotalapú szolgáltatások esetében az elsődleges csereforgatókönyvkel kell foglalkoznunk. Ebben a sorrendben az Elsődleges szerepkör átkerül egy másik replika (vagy jön vissza) a szolgáltatás leállítása nélkül. Végül, meg kell gondolni hiba vagy hiba feltételeket.

## <a name="stateless-service-startup"></a>Állapotnélküli szolgáltatás indítása
Egy állapotmentes szolgáltatás életciklusa egyszerű. Itt van az események sorrendje:

1. A szolgáltatás megvan építve.
2. Aztán, ezzel párhuzamosan, két dolog történik:
    - `StatelessService.CreateServiceInstanceListeners()`meghívásra kerül, és a visszaküldött figyelők megnyílnak. `ICommunicationListener.OpenAsync()`minden hallgatónak meg kell hívni.
    - A szolgáltatás `StatelessService.RunAsync()` metódusa neve.
3. Ha van, a `StatelessService.OnOpenAsync()` szolgáltatás metódusa neve. Ez a hívás nem gyakori felülbírálás, de elérhető. A kiterjesztett szolgáltatásinicializálási feladatok jelenleg elindíthatók.

Ne feledje, hogy nincs rendezés a figyelők és a **RunAsync**hívások között. A figyelők megnyílhatnak a **RunAsync indítása** előtt. Hasonlóképpen **meghívhatrunasync,** mielőtt a kommunikációs figyelők nyitottak vagy akár épített. Ha bármilyen szinkronizálásra van szükség, akkor a végrehajtó ra marad edzésként. Íme néhány gyakori megoldás:

  - Néha a figyelők nem működhetnek, amíg más információk nem jönnek létre, vagy nem végeznek munkát. Az állapotmentes szolgáltatások esetében ez a munka általában más helyeken is elvégezhető, például a következőkben: 
    - A szolgálat konstruktorában.
    - A `CreateServiceInstanceListeners()` hívás közben.
    - Részeként az építőiparban a hallgató is.
  - Előfordulhat, hogy a **RunAsync** kódja nem indul el, amíg a figyelők meg vannak nyitva. Ebben az esetben további koordinációra van szükség. Az egyik gyakori megoldás az, hogy van egy zászló a figyelők, amely jelzi, ha befejezték. Ezt a jelzőt a rendszer ezután a **RunAsync-ben** ellenőrzi, mielőtt folytatná a tényleges munkát.

## <a name="stateless-service-shutdown"></a>Állapotnélküli szolgáltatás leállítása
Az állapotmentes szolgáltatás leállításához ugyanezt a mintát követi, csak fordítva:

1. Ezzel párhuzamosan:
    - Minden nyitott hallgató zárva van. `ICommunicationListener.CloseAsync()`minden hallgatónak meg kell hívni.
    - A törlési `RunAsync()` jogkivonat törlődik. A törlési jogkivonat `IsCancellationRequested` tulajdonságának ellenőrzése igaz értéket ad `ThrowIfCancellationRequested` vissza, és `OperationCanceledException`ha meghívják, a token metódusa egy .
2. Befejezése `CloseAsync()` után az egyes `RunAsync()` figyelők és befejezi, `StatelessService.OnCloseAsync()` a szolgáltatás metódusa neve, ha jelen van.  OnCloseAsync hívása, ha az állapotmentes szolgáltatáspéldány lesz szabályosan leállítani. Ez akkor fordulhat elő, ha a szolgáltatás kódja frissítése folyamatban van, a szolgáltatáspéldány terheléselosztás miatt áthelyezik, vagy átmeneti hibát észlel. Nem gyakori a `StatelessService.OnCloseAsync()`felülbírálás, de használható az erőforrások biztonságos bezárására, a háttérfeldolgozás leállítására, a külső állapot mentésjéhez vagy a meglévő kapcsolatok bezárásához.
3. A `StatelessService.OnCloseAsync()` befejezés után a szolgáltatásobjektum destructed.

## <a name="stateful-service-startup"></a>Állapotalapú szolgáltatás indítása
Az állapotalapú szolgáltatások hasonló mintával rendelkeznek, mint az állapotmentes szolgáltatások, néhány változtatással. Az állapotalapú szolgáltatás indításához az események sorrendje a következő:

1. A szolgáltatás megvan építve.
2. `StatefulServiceBase.OnOpenAsync()`a neve. Ezt a hívást a szolgáltatás gyakran nem bírálja felül.
3. A következő dolgok történnek párhuzamosan:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`a meghívásra kerül. 
      - Ha a szolgáltatás elsődleges szolgáltatás, az összes visszaadott figyelők meg vannak nyitva. `ICommunicationListener.OpenAsync()`minden hallgatónak meg kell hívni.
      - Ha a szolgáltatás másodlagos szolgáltatás, csak `ListenOnSecondary = true` a megnyitottként megjelölt figyelők. Miután a hallgatók, hogy nyitott a másodlagos kevésbé gyakori.
    - Ha a szolgáltatás jelenleg elsődleges, a `StatefulServiceBase.RunAsync()` szolgáltatás metódusa neve.
4. Miután az összes replika figyelő `OpenAsync()` hívások befejezéséhez, és `RunAsync()` hívják, `StatefulServiceBase.OnChangeRoleAsync()` a neve. Ezt a hívást a szolgáltatás gyakran nem bírálja felül.

Az állapotmentes szolgáltatásokhoz hasonlóan nincs koordináció a figyelők létrehozásának és megnyitása, valamint a **RunAsync** elnevezése között. Ha koordinációra van szüksége, a megoldások nagyjából ugyanazok. Van még egy eset az állapotalapú szolgáltatásra. Tegyük fel, hogy a kommunikációs figyelőkhöz érkező hívások néhány megbízható gyűjteményben tárolt információkat [igényelnek.](service-fabric-reliable-services-reliable-collections.md)

   > [!NOTE]  
   > Mivel a kommunikációs figyelők megnyílhatnak, mielőtt a megbízható gyűjtemények olvashatók vagy írhatók lennének, és mielőtt **a RunAsync** elkezdődhetne, további koordinációra van szükség. A legegyszerűbb és leggyakoribb megoldás az, hogy a kommunikációs figyelők egy hibakódot adnak vissza, amelyet az ügyfél a kérelem újrapróbálkozásához használ.

## <a name="stateful-service-shutdown"></a>Állapotalapú szolgáltatás leállítása
Az állapotmentes szolgáltatásokhoz hasonlóan a leállítás során az életciklus-események megegyeznek az indítássorán, de megfordulnak. Állapotalapú szolgáltatás leállításakor a következő események következnek be:

1. Ezzel párhuzamosan:
    - Minden nyitott hallgató zárva van. `ICommunicationListener.CloseAsync()`minden hallgatónak meg kell hívni.
    - A törlési `RunAsync()` jogkivonat törlődik. A törlési jogkivonat `IsCancellationRequested` tulajdonságának ellenőrzése igaz értéket ad `ThrowIfCancellationRequested` vissza, és `OperationCanceledException`ha meghívják, a token metódusa egy .
2. Befejezése `CloseAsync()` után az egyes `RunAsync()` figyelők és befejezi, `StatefulServiceBase.OnChangeRoleAsync()` a szolgáltatás neve. Ezt a hívást a szolgáltatás gyakran nem bírálja felül.

   > [!NOTE]  
   > A **RunAsync** befejezésére várni csak akkor szükséges, ha ez a replika elsődleges kópia.

3. A `StatefulServiceBase.OnChangeRoleAsync()` módszer befejezése után `StatefulServiceBase.OnCloseAsync()` a metódus neve meg történik. Ez a hívás nem gyakori felülbírálás, de elérhető.
3. A `StatefulServiceBase.OnCloseAsync()` befejezés után a szolgáltatásobjektum destructed.

## <a name="stateful-service-primary-swaps"></a>Állapotalapú szolgáltatás Elsődleges swapügyletek
Amíg egy állapotalapú szolgáltatás fut, csak az elsődleges replikák, hogy az állapotalapú szolgáltatások a kommunikációs figyelők nyitott és **a RunAsync** metódus neve. Másodlagos replikák vannak kialakítva, de nem lát további hívásokat. Állapotalapú szolgáltatás futása közben a jelenleg elsődleges replikát a hiba vagy a fürtkiegyensúlyozás optimalizálása miatt módosíthatja. Mit jelent ez a replikát látható életciklus-események szempontjából? Az állapotalapú replika által látott viselkedés attól függ, hogy a lefokozott vagy előléptetett replika-e a csere során.

### <a name="for-the-primary-thats-demoted"></a>Az Elemi számára, amelyet lefokoztak
Az elsődleges replika, amely lefokozták, service fabric szüksége van erre a replika az üzenetek feldolgozásának leállításához, és kilép minden háttérmunkát csinál. Ennek eredményeképpen ez a lépés úgy néz ki, mint a szolgáltatás leállításakor. Az egyik különbség az, hogy a szolgáltatás nem destructed vagy zárt, mert továbbra is másodlagos. A következő API-k neve:

1. Ezzel párhuzamosan:
    - Minden nyitott hallgató zárva van. `ICommunicationListener.CloseAsync()`minden hallgatónak meg kell hívni.
    - A törlési `RunAsync()` jogkivonat törlődik. A törlési jogkivonat `IsCancellationRequested` tulajdonságának ellenőrzése igaz értéket ad `ThrowIfCancellationRequested` vissza, és `OperationCanceledException`ha meghívják, a token metódusa egy .
2. Befejezése `CloseAsync()` után az egyes `RunAsync()` figyelők és befejezi, `StatefulServiceBase.OnChangeRoleAsync()` a szolgáltatás neve. Ezt a hívást a szolgáltatás gyakran nem bírálja felül.

### <a name="for-the-secondary-thats-promoted"></a>A másodlagos, hogy az előléptetett
Hasonlóképpen a Service Fabric szüksége van a másodlagos replika, amely elő segítette, hogy elkezdi figyelni az üzeneteket a vezetéken, és indítsa el a háttérben szükséges feladatokat. Ennek eredményeképpen ez a folyamat úgy néz ki, mint a szolgáltatás létrehozásakor, azzal a különbséggel, hogy maga a replika már létezik. A következő API-k neve:

1. Ezzel párhuzamosan:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`meghívásra kerül, és a visszaküldött figyelők megnyílnak. `ICommunicationListener.OpenAsync()`minden hallgatónak meg kell hívni.
    - A szolgáltatás `StatefulServiceBase.RunAsync()` metódusa neve.
2. Miután az összes replika figyelő `OpenAsync()` hívások befejezéséhez, és `RunAsync()` hívják, `StatefulServiceBase.OnChangeRoleAsync()` a neve. Ezt a hívást a szolgáltatás gyakran nem bírálja felül.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Gyakori problémák az állapotalapú szolgáltatás leállítása és az elsődleges lefokozás során
A Service Fabric számos okból módosítja az állapotalapú szolgáltatás elsődleges állapotát. A leggyakoribb a [fürt újraegyensúlyozása](service-fabric-cluster-resource-manager-balancing.md) és [az alkalmazások frissítése.](service-fabric-application-upgrade.md) Ezekben a műveletekben (valamint a normál szolgáltatás leállítása, mint ha látni szeretné, ha a `CancellationToken`szolgáltatás törölve), fontos, hogy a szolgáltatás tiszteletben tartja a . 

Azok a szolgáltatások, amelyek nem kezelik tisztán a lemondást, számos problémát tapasztalhatnak. Ezek a műveletek lassúak, mert a Service Fabric megvárja, hogy a szolgáltatások leállnak szabályosan. Ez végső soron sikertelen frissítésekhez vezethet, amelyek időkimaradáshoz és visszaállításhoz vezethetnek. A megszakítási jogkivonat teljesítésének elmulasztása kiegyensúlyozatlan fürtöket is okozhat. A fürtök kiegyensúlyozatlanná válnak, mert a csomópontok felforrósodnak, de a szolgáltatások nem kiegyensúlyozhatók újra, mert túl sokáig tart áthelyezni őket máshová. 

Mivel a szolgáltatások állapotalapúak, valószínű, hogy a [Megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md)szolgáltatást is használják. A Service Fabric, amikor egy elsődleges lefokozták, az egyik első dolog, ami történik, hogy az írási hozzáférést az alapul szolgáló állapot hoz vissza vonják. Ez egy második problémakészlethez vezet, amelyek hatással lehetnek a szolgáltatás életciklusára. A gyűjtemények az időzítés és a replika áthelyezése vagy leállítása alapján adnak vissza kivételeket. Ezeket a kivételeket megfelelően kell kezelni. A Service Fabric által okozott kivételek állandó [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) és átmeneti ( [`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategóriákba tartoznak. Állandó kivételeket kell naplózni, és előkell állítani, míg az átmeneti kivételek lehet újra próbálkozás logika alapján.

A kivételek, amelyek a szolgáltatás `ReliableCollections` életciklus-események kel együtt a megbízható szolgáltatás tesztelése és érvényesítése fontos része a kivételek kezelése. Azt javasoljuk, hogy mindig futtassa a szolgáltatást terhelés alatt, miközben a frissítések és [a káosz tesztelés](service-fabric-controlled-chaos.md) éles üzembe helyezése előtt. Ezek az alapvető lépések segítenek annak biztosításában, hogy a szolgáltatás megfelelően legyen megvalósítva, és megfelelően kezelje az életciklus-eseményeket.


## <a name="notes-on-the-service-lifecycle"></a>Megjegyzések a szolgáltatás életciklusához
  - Mind `RunAsync()` a metódus, mind a `CreateServiceReplicaListeners/CreateServiceInstanceListeners` hívások nem kötelezőek. Egy szolgáltatás rendelkezhet az egyik, mindkettő, vagy egyik sem. Ha például a szolgáltatás a felhasználói hívásokra adott válaszként végzi el `RunAsync()`a munkáját, nincs szükség a megvalósítására. Csak a kommunikációs figyelők és a hozzájuk tartozó kód szükséges. Hasonlóképpen a kommunikációs figyelők létrehozása és visszaküldése nem kötelező, mivel a szolgáltatásnak `RunAsync()`csak háttérfeladata lehet, ezért csak a megvalósítását kell végrehajtania.
  - Érvényes egy szolgáltatás sikeres `RunAsync()` befejezéséhez és visszatéréséhez. A befejezés nem hibafeltétel. A `RunAsync()` kitöltés azt jelzi, hogy a szolgáltatás háttérmunkája befejeződött. Állapotalapú megbízható szolgáltatások `RunAsync()` esetén a rendszer újra megnevezi, ha a replika levan fokozva elsődleges másodlagos, majd előléptetik vissza az elsődleges.
  - Ha egy szolgáltatás `RunAsync()` váratlan kivétellel lép ki, az hibát jelent. A szolgáltatásobjektum levan állítva, és a rendszer állapothibát jelez.
  - Bár nincs időkorlát a visszatérés ezekből a módszerekből, azonnal elveszíti a képességét, hogy írjon a Megbízható gyűjtemények, és ezért nem tudja befejezni a valódi munkát. Azt javasoljuk, hogy a lemondási kérelem kézhezvételekor a lehető leggyorsabban térjen vissza. Ha a szolgáltatás nem válaszol ezekre az API-hívásokra ésszerű időn alatt, service fabric erőszakkal megszüntetheti a szolgáltatást. Ez általában csak az alkalmazás frissítésekor vagy egy szolgáltatás törlésekor történik. Ez az időhosszabbítás alapértelmezés szerint 15 perc.
  - Az elérési `OnCloseAsync()` út hibái `OnAbort()` az elérési út megnevezését eredményezik, ami egy utolsó esély a legjobb megoldás a szolgáltatás számára az általuk igényelt erőforrások karbantartására és felszabadítására. Ez általában akkor hívható, ha egy állandó hiba észlelése a csomóponton, vagy ha a Service Fabric nem tudja megbízhatóan kezelni a szolgáltatáspéldány életciklusa belső hibák miatt.
  - `OnChangeRoleAsync()`akkor hívják meg, ha az állapotalapú szolgáltatásreplika szerepkört módosít, például elsődleges vagy másodlagos. Elsődleges replikák kapnak írási állapot (engedélyezett ek létrehozása és írása megbízható gyűjtemények). Másodlagos replikák olvasási állapot (csak a meglévő megbízható gyűjtemények olvasni). Az állapotalapú szolgáltatásban végzett legtöbb munka az elsődleges replika. A másodlagos replikák csak olvasható érvényesítést, jelentésgenerálást, adatbányászatot vagy más írásvédett feladatokat hajthatnak végre.

## <a name="next-steps"></a>További lépések
- [Bevezetés a megbízható szolgáltatásokba](service-fabric-reliable-services-introduction.md)
- [Megbízható szolgáltatások – gyorsindítás](service-fabric-reliable-services-quick-start.md)
- [Replikák és példányok](service-fabric-concepts-replica-lifecycle.md)
