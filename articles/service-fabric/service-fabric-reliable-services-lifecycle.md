---
title: A Reliable Services életciklusának áttekintése
description: Ismerje meg az Azure Service Fabric Reliable Services alkalmazásban az állapot-nyilvántartó és az állapot nélküli szolgáltatások életciklusának eseményeit.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: fe338ca3f25cd606da7f95f6c9437a3cd3dc4e69
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645497"
---
# <a name="reliable-services-lifecycle-overview"></a>A Reliable Services életciklusának áttekintése
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

Ha az Azure Service Fabric Reliable Services életciklusait gondolja, az életciklus alapjai a legfontosabbak. Az életciklus általában az alábbiakat tartalmazza:

- Indításkor:
  - A szolgáltatások építése megtörténik.
  - A szolgáltatásokból lehetőség van nulla vagy több figyelő létrehozására és visszaadására.
  - A rendszer megnyit minden visszaadott figyelőt, amely lehetővé teszi a kommunikációt a szolgáltatással.
  - A szolgáltatás **RunAsync** metódusának hívása, amely lehetővé teszi, hogy a szolgáltatás hosszan futó feladatokat vagy háttér-munkát végezzen.
- Leállítás közben:
  - A **RunAsync** átadott törlési jogkivonat meg lett szakítva, és a figyelők le vannak zárva.
  - A figyelők bezárását követően maga a szolgáltatási objektum megsemmisül.

Az események pontos sorrendjét részletesen megtalálhatja. Az események sorrendje kis mértékben változhat attól függően, hogy a megbízható szolgáltatás állapota vagy állapota nem megfelelő-e. Emellett az állapot-nyilvántartó szolgáltatások esetében az elsődleges swap-forgatókönyvvel kell foglalkozni. Ebben a sorozatban az elsődleges szerepkört egy másik replikába (vagy visszatérve) helyezi át a szolgáltatás leállítása nélkül. Végül a hiba-vagy meghibásodási körülményeket is meg kell gondolni.

## <a name="stateless-service-startup"></a>Állapot nélküli szolgáltatás indítása
Az állapot nélküli szolgáltatások életciklusa egyszerű. Az események sorrendje:

1. A szolgáltatás építése megtörténik.
2. Ezzel párhuzamosan két dolog történik:
    - a rendszer meghívja a `StatelessService.CreateServiceInstanceListeners()`, és megnyit minden visszaadott figyelőt. `ICommunicationListener.OpenAsync()` hívása minden Figyelőnél megtörténik.
    - A szolgáltatás `StatelessService.RunAsync()` metódusának hívása.
3. Ha van ilyen, a szolgáltatás `StatelessService.OnOpenAsync()` metódusának hívása. Ez a hívás nem gyakori felülbírálás, de elérhető. A kiterjesztett szolgáltatás-inicializálási feladatok most már elindíthatók.

Ne feledje, hogy a figyelők és a **RunAsync**létrehozására és megnyitására irányuló hívások között nincs megrendelés. A figyelők megnyithatók a **RunAsync** elindítása előtt. Hasonlóképpen, a **RunAsync** meghívása előtt megnyithatja a kommunikációs figyelőket, vagy megkezdheti a kialakítását. Ha bármilyen szinkronizálásra van szükség, akkor a végrehajtó feladata marad. Íme néhány gyakori megoldás:

  - Néha a figyelők nem működhetnek, amíg nem jön létre más információ, vagy nem végez munkát. Az állapot nélküli szolgáltatások esetében a munkát általában más helyszíneken is el lehet végezni, például a következő módon: 
    - A szolgáltatás konstruktorában.
    - A `CreateServiceInstanceListeners()` hívás közben.
    - A figyelő szerkezetének részeként.
  - Néha a **RunAsync** található kód nem indul el, amíg a figyelők meg nem nyílnak. Ebben az esetben további koordinációra van szükség. Az egyik gyakori megoldás az, hogy a figyelőn belül van egy jelölő, amely jelzi, hogy mikor fejeződött be. Ezt a jelzőt a rendszer a **RunAsync** -ben ellenőrzi, mielőtt folytatná a tényleges munkát.

## <a name="stateless-service-shutdown"></a>Állapot nélküli szolgáltatás leállítása
Állapot nélküli szolgáltatás leállításakor ugyanezt a mintát kell követni, épp ellenkezőleg:

1. Párhuzamosan:
    - Minden nyitott figyelő le van zárva. `ICommunicationListener.CloseAsync()` hívása minden Figyelőnél megtörténik.
    - A `RunAsync()`nak átadott törlési tokent megszakították. A lemondási token `IsCancellationRequested` tulajdonságának az igaz értéket adja vissza, és ha az hívása megtörtént, a jogkivonat `ThrowIfCancellationRequested` metódusa egy `OperationCanceledException`mutat be.
2. `CloseAsync()` befejezését követően az egyes figyelőket, és `RunAsync()` is befejeződik, a szolgáltatás `StatelessService.OnCloseAsync()` metódusának neve, ha van ilyen.  A OnCloseAsync akkor lesz meghívva, ha az állapot nélküli szolgáltatás példánya szabályosan le lesz állítva. Ez akkor fordulhat elő, ha a szolgáltatás kódjának frissítése folyamatban van, és a szolgáltatás a terheléselosztás miatt áthelyezi a szolgáltatási példányt, vagy átmeneti hibát észlel. Nem gyakori a `StatelessService.OnCloseAsync()`felülbírálására, de használható az erőforrások biztonságos bezárására, a háttérben történő feldolgozás megállítására, a külső állapot mentésének befejezésére vagy a meglévő kapcsolatok bezárására.
3. `StatelessService.OnCloseAsync()` befejeződése után a szolgáltatás objektum megsemmisül.

## <a name="stateful-service-startup"></a>Állapot-nyilvántartó szolgáltatás indítása
Az állapot-nyilvántartó szolgáltatásoknak hasonló mintázata van az állapot nélküli szolgáltatásokhoz, néhány módosítással. Egy állapot-nyilvántartó szolgáltatás indításához az események sorrendje a következő:

1. A szolgáltatás építése megtörténik.
2. `StatefulServiceBase.OnOpenAsync()` neve. Ez a hívás általában nincs felülbírálva a szolgáltatásban.
3. A következő dolgok párhuzamosan történnek:
    - a rendszer meghívja a `StatefulServiceBase.CreateServiceReplicaListeners()`. 
      - Ha a szolgáltatás elsődleges szolgáltatás, a rendszer minden visszaadott figyelőt megnyit. `ICommunicationListener.OpenAsync()` hívása minden Figyelőnél megtörténik.
      - Ha a szolgáltatás másodlagos szolgáltatás, csak a `ListenOnSecondary = true`ként megjelölt figyelők nyílnak meg. A formátumú másodlagos zónák megnyitott figyelők kevésbé gyakoriak.
    - Ha a szolgáltatás jelenleg elsődleges, a szolgáltatás `StatefulServiceBase.RunAsync()` metódusát hívja meg.
4. A replika-figyelő összes `OpenAsync()` hívásának befejezése után a rendszer meghívja a `RunAsync()`t, `StatefulServiceBase.OnChangeRoleAsync()` neve. Ez a hívás általában nincs felülbírálva a szolgáltatásban.

Az állapot nélküli szolgáltatásokhoz hasonlóan nincs koordináció a figyelők létrehozásának és megnyitási sorrendjének, valamint a **RunAsync** hívása során. Ha koordinációra van szüksége, a megoldások sokkal ugyanazok. Van egy további eset az állapot-nyilvántartó szolgáltatáshoz. Tegyük fel, hogy a kommunikációs figyelőkre érkező hívások esetében szükséges, hogy az információk bizonyos [megbízható gyűjteményekben](service-fabric-reliable-services-reliable-collections.md)legyenek tárolva.

   > [!NOTE]  
   > Mivel a kommunikációs figyelők megnyithatók, mielőtt a megbízható gyűjtemények olvashatók vagy írhatók, és mielőtt **RunAsync** , további koordinációra van szükség. A legegyszerűbb és legáltalánosabb megoldás a kommunikációs figyelők számára, hogy olyan hibakódot küldjön vissza, amelyet az ügyfél a kérelem megismétléséhez használ.

## <a name="stateful-service-shutdown"></a>Állapot-nyilvántartó szolgáltatás leállítása
Az állapot nélküli szolgáltatásokhoz hasonlóan a leállítás során az életciklus eseményei megegyeznek az indítás során, de fordítva. Állapot-nyilvántartó szolgáltatás leállításakor a következő események történnek:

1. Párhuzamosan:
    - Minden nyitott figyelő le van zárva. `ICommunicationListener.CloseAsync()` hívása minden Figyelőnél megtörténik.
    - A `RunAsync()`nak átadott törlési tokent megszakították. A lemondási token `IsCancellationRequested` tulajdonságának az igaz értéket adja vissza, és ha az hívása megtörtént, a jogkivonat `ThrowIfCancellationRequested` metódusa egy `OperationCanceledException`mutat be.
2. `CloseAsync()` befejezését követően az egyes figyelőket, és `RunAsync()` is befejeződik, a szolgáltatás `StatefulServiceBase.OnChangeRoleAsync()` is meghívja. Ez a hívás általában nincs felülbírálva a szolgáltatásban.

   > [!NOTE]  
   > A **RunAsync** befejezésére való várakozásra csak akkor van szükség, ha ez a replika elsődleges replika.

3. A `StatefulServiceBase.OnChangeRoleAsync()` metódus befejeződése után a rendszer a `StatefulServiceBase.OnCloseAsync()` metódust hívja meg. Ez a hívás nem gyakori felülbírálás, de elérhető.
3. `StatefulServiceBase.OnCloseAsync()` befejeződése után a szolgáltatás objektum megsemmisül.

## <a name="stateful-service-primary-swaps"></a>Állapot-nyilvántartó szolgáltatás elsődleges felcserélése
Egy állapot-nyilvántartó szolgáltatás futása közben csak az állapot-nyilvántartó szolgáltatások elsődleges replikái vannak megnyitva a kommunikációs figyelők, a **RunAsync** metódusuk pedig meghívva. A másodlagos replikák létre vannak építve, de nem találhatók további hívások. Egy állapot-nyilvántartó szolgáltatás futása közben az elsődleges replika a hiba vagy a fürt kiegyensúlyozásának optimalizálása miatt változhat. Mit jelent ez a replika által megtekinthető életciklus-események szempontjából? Az állapot-nyilvántartó replika viselkedése attól függ, hogy a replika le van-e lefokozva vagy előléptetve a swap során.

### <a name="for-the-primary-thats-demoted"></a>A lefokozni kívánt elsődleges
A lefokozni kívánt elsődleges replika esetében Service Fabric szüksége van erre a replikára az üzenetek feldolgozásának leállításához és a háttérben végzett munka befejezéséhez. Ennek eredményeképpen ez a lépés úgy néz ki, mint a szolgáltatás leállításakor. Az egyik különbség, hogy a szolgáltatás nincs elpusztulva vagy lezárva, mert másodlagosként marad. A következő API-kat hívják:

1. Párhuzamosan:
    - Minden nyitott figyelő le van zárva. `ICommunicationListener.CloseAsync()` hívása minden Figyelőnél megtörténik.
    - A `RunAsync()`nak átadott törlési tokent megszakították. A lemondási token `IsCancellationRequested` tulajdonságának az igaz értéket adja vissza, és ha az hívása megtörtént, a jogkivonat `ThrowIfCancellationRequested` metódusa egy `OperationCanceledException`mutat be.
2. `CloseAsync()` befejezését követően az egyes figyelőket, és `RunAsync()` is befejeződik, a szolgáltatás `StatefulServiceBase.OnChangeRoleAsync()` is meghívja. Ez a hívás általában nincs felülbírálva a szolgáltatásban.

### <a name="for-the-secondary-thats-promoted"></a>Az előléptetett másodlagos
Hasonlóképpen Service Fabric szüksége van arra a másodlagos másodpéldányra, amely a hálózaton lévő üzenetek figyelésének megkezdéséhez és a befejezéshez szükséges összes háttér-feladat elindításához szükséges. Ennek eredményeképpen a folyamat úgy néz ki, mint a szolgáltatás létrehozásakor, azzal a különbséggel, hogy maga a replika már létezik. A következő API-kat hívják:

1. Párhuzamosan:
    - a rendszer meghívja a `StatefulServiceBase.CreateServiceReplicaListeners()`, és megnyit minden visszaadott figyelőt. `ICommunicationListener.OpenAsync()` hívása minden Figyelőnél megtörténik.
    - A szolgáltatás `StatefulServiceBase.RunAsync()` metódusának hívása.
2. A replika-figyelő összes `OpenAsync()` hívásának befejezése után a rendszer meghívja a `RunAsync()`t, `StatefulServiceBase.OnChangeRoleAsync()` neve. Ez a hívás általában nincs felülbírálva a szolgáltatásban.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Gyakori problémák az állapot-nyilvántartó szolgáltatás leállítása és az elsődleges lefokozás során
A Service Fabric számos okból módosítja az állapot-nyilvántartó szolgáltatás elsődleges állapotát. A leggyakoribb a [fürtök újraelosztása](service-fabric-cluster-resource-manager-balancing.md) és az [alkalmazások frissítése](service-fabric-application-upgrade.md). A műveletek során (valamint a normál szolgáltatás leállításakor, mint ahogy a szolgáltatás törlődött), fontos, hogy a szolgáltatás tiszteletben tartsa a `CancellationToken`. 

A megszakítást nem kezelő szolgáltatások számos problémát tapasztalhatnak. Ezek a műveletek lassúak, mert Service Fabric megvárja, amíg a szolgáltatások szabályosan leállnak. Ez végső soron sikertelen frissítésekhez vezethet, amelyek időtúllépést és visszaállítást végeznek. A lemondási token betartásának elmulasztása miatt kiegyensúlyozatlan fürtök is lehetnek. A fürtök kiegyensúlyozatlan állapotba kerülhetnek, mivel a csomópontok melegek lesznek, de a szolgáltatások nem lehetnek kiegyensúlyozva, mert túl sokáig tart, hogy máshová helyezze őket. 

Mivel a szolgáltatások állapot-nyilvántartó, az is valószínű, hogy a [megbízható gyűjteményeket](service-fabric-reliable-services-reliable-collections.md)használják. Service Fabric az elsődleges lefokozása után az egyik első dolog, ami megtörténik, az írási hozzáférés az alapul szolgáló állapothoz visszavonva. Ez egy második olyan problémát eredményez, amely hatással lehet a szolgáltatás életciklusára. A gyűjtemények az időzítés alapján adják vissza a kivételeket, és azt, hogy a replika áthelyezése vagy leállítása folyamatban van-e. Ezeket a kivételeket helyesen kell kezelni. A Service Fabric által okozott kivételek állandó [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) és átmeneti [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategóriákba esnek. Az állandó kivételeket naplózni kell, és el kell dobni, amíg az átmeneti kivételek újrapróbálkoznak néhány újrapróbálkozási logika alapján.

A `ReliableCollections` használatából származó kivételek kezelése a szolgáltatási életciklus eseményeivel együtt fontos része a megbízható szolgáltatások tesztelésének és ellenőrzésének. Javasoljuk, hogy az éles környezetbe való üzembe helyezés előtt mindig a terhelés alatt futtassa a szolgáltatást a frissítés és a [káosz tesztelésének](service-fabric-controlled-chaos.md) végrehajtása során. Ezek az alapvető lépések segítenek biztosítani a szolgáltatás megfelelő megvalósítását és az életciklus-események megfelelő kezelését.


## <a name="notes-on-the-service-lifecycle"></a>Megjegyzések a szolgáltatás életciklusához
  - A `RunAsync()` metódus és a `CreateServiceReplicaListeners/CreateServiceInstanceListeners` hívások sem választhatók. A szolgáltatások közül bármelyik, mindkettő vagy egyik sem lehet. Ha például a szolgáltatás az összes munkáját a felhasználói hívásokra válaszol, nincs szükség a `RunAsync()`megvalósítására. Csak a kommunikációs figyelők és a hozzájuk tartozó kódok szükségesek. Hasonlóképpen, a kommunikációs figyelők létrehozása és visszaküldése nem kötelező, mivel a szolgáltatás csak a háttérben végezhető el, így csak a `RunAsync()`megvalósítására van szükség.
  - A szolgáltatás `RunAsync()` sikeres végrehajtásához és az onnan való visszatéréshez érvényes. A Befejezés nem egy hiba feltétele. A `RunAsync()` befejezése azt jelzi, hogy a szolgáltatás háttérben végzett munkája befejeződött. Az állapot-nyilvántartó megbízható szolgáltatások esetében a rendszer újból meghívja a `RunAsync()`, ha a replika lefokozása elsődlegesről másodlagosra történik, majd a rendszer visszaküldi az elsődlegesnek.
  - Ha egy szolgáltatás kilép a `RunAsync()`ból egy váratlan kivétel bedobásával, akkor ez egy hiba. A szolgáltatási objektum le van állítva, és a rendszer állapottal kapcsolatos hibát jelez.
  - Bár az ilyen metódusokból való visszaküldéshez nincs időkorlát, azonnal elveszíti a megbízható gyűjteményekbe való írás lehetőségét, ezért nem tudja befejezni a valódi munkát. Javasoljuk, hogy a törlési kérés fogadásakor a lehető leggyorsabban térjen vissza. Ha a szolgáltatás ésszerű időn belül nem válaszol ezekre az API-hívásokra, Service Fabric kényszerítheti a szolgáltatás bezárását. Ez általában csak az alkalmazások frissítésekor vagy a szolgáltatás törlésekor történik. Alapértelmezés szerint ez az időkorlát 15 perc.
  - A `OnCloseAsync()` elérési úton fellépő hibák meghívása `OnAbort()`t eredményez, ami a szolgáltatás utolsó esélyének legeredményesebb lehetősége, amely az általuk igényelt erőforrások tisztítására és felszabadítására szolgál. Ez általában akkor fordul elő, ha a csomóponton állandó hibát észlel, vagy ha Service Fabric belső meghibásodások miatt nem tudja megbízhatóan kezelni a szolgáltatási példány életciklusát.
  - a `OnChangeRoleAsync()` akkor lesz meghívva, ha az állapot-nyilvántartó szolgáltatás replikája megváltoztatja a szerepkört, például az elsődleges vagy a másodlagos. Az elsődleges replikák írási állapotot kapnak (a megbízható gyűjtemények létrehozásához és írásához engedélyezett). A másodlagos replikák olvasási állapotot kapnak (csak a meglévő megbízható gyűjteményekből olvashatók be). Egy állapot-nyilvántartó szolgáltatásban a legtöbb munka az elsődleges replikán történik. A másodlagos replikák csak olvasási ellenőrzés, jelentéskészítés, adatbányászat vagy más írásvédett feladatok végrehajtására használhatók.

## <a name="next-steps"></a>Következő lépések
- [Bevezetés a Reliable Servicesba](service-fabric-reliable-services-introduction.md)
- [Reliable Services – első lépések](service-fabric-reliable-services-quick-start.md)
- [Replikák és példányok](service-fabric-concepts-replica-lifecycle.md)
