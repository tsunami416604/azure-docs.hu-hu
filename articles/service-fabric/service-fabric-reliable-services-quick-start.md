---
title: 'Az első Service Fabric-alkalmazás létrehozása C-ben #'
description: Bevezetés a Microsoft Azure Service Fabric-alkalmazások állapotnélküli és állapotalapú szolgáltatásokkal történő létrehozásához.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev
ms.openlocfilehash: 15dd9bf6ac19bdac7bc8b50fc70e0b3b0a4e9a83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083763"
---
# <a name="get-started-with-reliable-services"></a>Ismerkedés a Reliable Services használatával

> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-quick-start.md)
> * [Java Linuxon](service-fabric-reliable-services-quick-start-java.md)

Egy Azure Service Fabric-alkalmazás egy vagy több, a kódot futtató szolgáltatást tartalmaz. Ez az útmutató bemutatja, hogyan hozhat létre állapot nélküli és állapotalapú Service Fabric-alkalmazásokat [a Reliable Services](service-fabric-reliable-services-introduction.md)szolgáltatással.  

## <a name="basic-concepts"></a>Alapfogalmak

A Megbízható szolgáltatások első lépéseihez csak néhány alapvető fogalmat kell megértenie:

* **Szolgáltatás típusa**: Ez a szolgáltatás megvalósítása. Ez határozza meg az osztály írsz, hogy `StatelessService` kiterjeszti, és minden más kódot vagy függőséget használnak ott, valamint egy nevet és egy verziószámot.
* **Elnevezett szolgáltatáspéldány:** A szolgáltatás futtatásához hozzon létre a szolgáltatástípus elnevezett példányait, ugyanúgy, mint egy osztálytípusú objektumpéldányokat. A szolgáltatáspéldány neve URI formájában van a "fabric:/" használatával. például a "fabric:/MyApp/MyService".
* **Szolgáltatásgazda**: A létrehozott szolgáltatáspéldányok létre kell futtatni egy gazdagép folyamaton belül. A szolgáltatásgazda csak egy folyamat, ahol a szolgáltatás példányai futtathatók.
* **Szolgáltatás regisztráció**: Regisztráció hozza össze mindent. A szolgáltatás típusát regisztrálni kell a Service Fabric futásidejű egy szolgáltatásgazdagépen, hogy a Service Fabric hozzon létre példányokat.  

## <a name="create-a-stateless-service"></a>Állapotmentes szolgáltatás létrehozása

Az állapotmentes szolgáltatás egy olyan szolgáltatástípus, amely jelenleg a felhőalapú alkalmazások normája. Állapot nélkülinek minősül, mert maga a szolgáltatás nem tartalmaz olyan adatokat, amelyeket megbízhatóan kell tárolni, vagy magas rendelkezésre állásúvá kell tenni. Ha egy állapotmentes szolgáltatás egy példánya leáll, az összes belső állapota elvész. Az ilyen típusú szolgáltatás állapot egy külső tároló, például az Azure Tables vagy egy SQL-adatbázis, ahhoz, hogy magas rendelkezésre állású és megbízható állapotú állapotban kell maradnia.

Indítsa el a Visual Studio 2017-et vagy a Visual Studio 2019-et rendszergazdaként, és hozzon létre egy Új Service Fabric alkalmazásprojektet *HelloWorld*néven:

![Új Service Fabric-alkalmazás létrehozása az Új projekt párbeszédpanelen](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Ezután hozzon létre egy állapotmentes szolgáltatási projektet **a .NET Core 2.0** használatával, melynek neve *HelloWorldStateless*:

![A második párbeszédpanelen hozzon létre egy állapotmentes szolgáltatási projektet](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

A megoldás most már két projektet tartalmaz:

* *HelloWorld*. Ez az *alkalmazásprojekt,* amely tartalmazza a *szolgáltatásokat.* Emellett tartalmazza az alkalmazásjegyzéket, amely leírja az alkalmazást, valamint számos PowerShell-parancsfájlt, amelyek segítenek az alkalmazás üzembe helyezésében.
* *HelloWorldStateless*. Ez a szolgáltatási projekt. Ez tartalmazza a stateless szolgáltatás végrehajtása.

## <a name="implement-the-service"></a>A szolgáltatás megvalósítása

Nyissa meg a **HelloWorldStateless.cs** fájlt a szolgáltatási projektben. A Service Fabric szolgáltatás bármely üzleti logika futtatható. A szolgáltatás API két belépési pontot biztosít a kódhoz:

* Egy nyílt végű belépési pont módszer, a *RunAsync,* ahol megkezdheti a számítási feladatok, beleértve a hosszú ideig futó számítási számítási feladatok.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Egy kommunikációs belépési pont, ahol csatlakoztathatja a választott kommunikációs vermeket, például ASP.NET Core-t. Itt kezdheti meg a felhasználóktól és más szolgáltatásoktól érkező kérések fogadását.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Ebben az oktatóanyagban a `RunAsync()` belépési pont módszerére összpontosítunk. Ez az a hely, ahol azonnal elkezdheti futtatni a kódot.
A projektsablon tartalmazza a `RunAsync()` gördülő szám mintaimplementációját.

> [!NOTE]
> A kommunikációs veremekkel való munka részleteiről a [Szolgáltatás kommunikációja ASP.NET Core-szal című témakörben talál.](service-fabric-reliable-services-communication-aspnetcore.md)

### <a name="runasync"></a>RunAsync

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

A platform meghívja ezt a metódust, ha egy szolgáltatás egy példánya van elhelyezve, és készen áll a végrehajtásra. Egy állapotmentes szolgáltatás, ez egyszerűen azt jelenti, amikor a szolgáltatáspéldány meg van nyitva. A lemondási jogkivonat ot biztosítunk a szolgáltatáspéldány lezárásának koordinálására. A Service Fabric, a szolgáltatáspéldány nyitott/záró ciklusa a szolgáltatás teljes élettartama alatt többször is előfordulhat. Ennek különböző okai lehetnek, többek között:

* A rendszer áthelyezi a szolgáltatáspéldányokat az erőforrás-kiegyenlítéshez.
* Hibák lépnek fel a kódban.
* Az alkalmazás vagy a rendszer frissül.
* Az alapul szolgáló hardver kimaradást tapasztal.

Ezt a vezénylési kezeli a rendszer, hogy a szolgáltatás magas rendelkezésre állású és megfelelően kiegyensúlyozott.

`RunAsync()`nem blokkolhatja szinkron módon. A RunAsync megvalósításának vissza kell adnia egy feladatot, vagy meg kell várnia a hosszú ideig futó vagy blokkoló műveleteket, hogy a futtatóidő folytatódjon. Megjegyzés az `while(true)` előző példában a hurokban `await Task.Delay()` egy feladat-visszatérő használat. Ha a számítási feladatoknak szinkron módon kell blokkolniuk, `RunAsync` ütemezzen egy új feladatot a `Task.Run()` megvalósításban.

A számítási feladatok törlése a megadott lemondási jogkivonat által koordinált együttműködési erőfeszítés. A rendszer megvárja, amíg a feladat befejeződik (sikeres befejezéssel, megszakítással vagy hibával), mielőtt továbblépne. Fontos, hogy tartsa tiszteletben a lemondási `RunAsync()` jogkivonatot, fejezze be a munkát, és lépjen ki a lehető leggyorsabban, amikor a rendszer törlést kér.

Ebben az állapotmentes szolgáltatás példában a számláló egy helyi változóban tárolódik. De mivel ez egy állapotmentes szolgáltatás, a tárolt érték csak a szolgáltatáspéldány aktuális életciklusára van. Amikor a szolgáltatás áthelyezi vagy újraindul, az érték elvész.

## <a name="create-a-stateful-service"></a>Állapotalapú szolgáltatás létrehozása

A Service Fabric egy új típusú szolgáltatást vezet be, amely állapotalapú. Az állapotalapú szolgáltatás megbízhatóan karbantarthatja az állapotot a szolgáltatáson belül, a használt kóddal együtt elhelyezve. Állapot magas rendelkezésre állású service Fabric anélkül, hogy egy külső tároló állapotának megőrzése nélkül.

Ha egy számlálóértékét állapot nélküliről magas rendelkezésre állásúra és állandóra szeretné konvertálni, még akkor is, ha a szolgáltatás áthelyezi vagy újraindul, állapotalapú szolgáltatásra van szükség.

Ugyanebben a *HelloWorld* alkalmazásban hozzáadhat egy új szolgáltatást, ha a jobb gombbal az alkalmazásprojekt ben a Szolgáltatások hivatkozásaira kattint, és a **-> Új szolgáltatásháló-szolgáltatás hozzáadása parancsra**kattint.

![Szolgáltatás hozzáadása a Service Fabric-alkalmazáshoz](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Válassza a **.NET Core 2.0 -> állapotalapú szolgáltatás** nevét, és nevezze el *HelloWorldStateful névre.* Kattintson az **OK** gombra.

![Új Service Fabric állapotalapú szolgáltatás létrehozása az Új projekt párbeszédpanelen](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Az alkalmazás most már két szolgáltatás: az állapotmentes szolgáltatás *HelloWorldStateless* és az állapotalapú szolgáltatás *HelloWorldStateful*.

Az állapotalapú szolgáltatás ugyanazokkal a belépési pontokkal rendelkezik, mint egy állapotmentes szolgáltatás. A fő különbség az állapotmegbízhatóan tárolható *állapotszolgáltató* elérhetősége. A Service Fabric egy megbízható gyűjtemények nevű állapotszolgáltató-implementációval [érkezik,](service-fabric-reliable-services-reliable-collections.md)amely lehetővé teszi replikált adatstruktúrák létrehozását a Reliable State Manager-en keresztül. Az állapotalapú megbízható szolgáltatás alapértelmezés szerint ezt az állapotszolgáltatót használja.

Nyissa **meg a HelloWorldStateful.cs** a *HelloWorldStateful*alkalmazásban, amely a következő RunAsync metódust tartalmazza:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync

`RunAsync()`hasonlóan működik az államos és állapotmentes szolgáltatásokban. Azonban egy állapotalapú szolgáltatás, a platform végez további munkát az `RunAsync()`Ön nevében, mielőtt végrehajtja . Ez a munka magában foglalhatja annak biztosítását, hogy a megbízható állapotkezelő és a megbízható gyűjtemények készen állnak a használatra.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Megbízható gyűjtemények és a megbízható állapotkezelő

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[Az IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) egy szótárimplementáció, amely az állapot megbízható tárolására használható a szolgáltatásban. A Service Fabric és a Reliable Collections segítségével az adatokat közvetlenül a szolgáltatásban tárolhatja anélkül, hogy külső állandó tárolóra lenne szükség. A Megbízható gyűjtemények magas rendelkezésre állásúvá teszik az adatokat. A Service Fabric ezt úgy valósítja meg, hogy a szolgáltatás több *replikáját* hozza létre és kezeli. Emellett egy API-t is biztosít, amely absztrakt el a replikák kezelésének összetettségét és állapotátmeneteit.

A Reliable Collections bármilyen .NET típust tárolhat, beleértve az egyéni típusokat is, néhány kikötéssel:

* A Service Fabric magas rendelkezésre állásúvá teszi az *állapotot* a csomópontok közötti replikálással, és a Megbízható gyűjtemények az adatokat az egyes replika helyi lemezén tárolják. Ez azt jelenti, hogy a Megbízható gyűjteményekben tárolt mindennek *szerializálhatónak*kell lennie. Alapértelmezés szerint a Megbízható gyűjtemények [datacontract a](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) szerializálás, ezért fontos, hogy győződjön meg arról, hogy a típusok [által támogatott adategyezmény szerializáló,](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) ha az alapértelmezett szerializáló használata.
* Objektumok replikálása a magas rendelkezésre állás érdekében, ha tranzakciók véglegesítése a megbízható gyűjtemények. A Megbízható gyűjtemények ben tárolt objektumok a szolgáltatás helyi memóriájában vannak tárolva. Ez azt jelenti, hogy helyi hivatkozást tartalmaz az objektumra.
  
   Fontos, hogy ne mutálja az objektumok helyi példányait anélkül, hogy egy tranzakció megbízható gyűjteményén frissítési műveletet végezne. Ennek az az oka, hogy az objektumok helyi példányainak módosításai nem replikálódnak automatikusan. Újra be kell szúrnia az objektumot a szótárba, vagy a szótárban lévő *frissítési* módszerek egyikét kell használnia.

A Megbízható állapotkezelő megbízható gyűjteményeket kezel az Ön számára. Egyszerűen kérheti a megbízható állapotkezelő egy megbízható gyűjtemény név szerint bármikor és bárhol a szolgáltatásban. A megbízható állapotkezelő biztosítja, hogy egy hivatkozást vissza. Nem javasoljuk, hogy mentse a megbízható gyűjteménypéldányokra mutató hivatkozásokat osztálytag-változókban vagy -tulajdonságokban. Különös figyelmet kell fordítani annak biztosítására, hogy a hivatkozás az üzemi életciklus során mindig egy példányra legyen beállítva. A Megbízható állapotkezelő kezeli ezt a munkát az Ön számára, és úgy van optimalizálva, ismételt látogatások.

### <a name="transactional-and-asynchronous-operations"></a>Tranzakciós és aszinkron műveletek

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Megbízható gyűjtemények sok ugyanazokat a `System.Collections.Generic` műveleteket, hogy azok és `System.Collections.Concurrent` társaik nem, kivéve a nyelvi integrált lekérdezés (LINQ). A megbízható gyűjtemények műveletei aszinkron. Ennek az az oka, hogy a Megbízható gyűjteményekkel végzett írási műveletek I/O-műveleteket hajtanak végre az adatok lemezre replikálásához és megőrzéséhez.

A Megbízható beszedési műveletek *tranzakciósak,* így több megbízható gyűjtemény és művelet között is konzisztens maradhat. Például egy munkaelem várólistájának várólistáját egy megbízható várólistából lekérheti, műveletet hajthat végre rajta, és az eredményt egy megbízható szótárba mentheti, mindezt egyetlen tranzakción belül. Ez a rendszer atomi műveletként kezeli, és garantálja, hogy vagy a teljes művelet sikeres lesz, vagy a teljes művelet visszaáll. Ha hiba történik az elem várólistájának befejezése után, de az eredmény mentése előtt a teljes tranzakció visszalesz állítva, és az elem feldolgozásra a várólistában marad.

## <a name="run-the-application"></a>Az alkalmazás futtatása
Most visszatérünk a *HelloWorld* alkalmazáshoz. Most már felépítheti és üzembe helyezheti a szolgáltatásokat. Az **F5**billentyű lenyomásakor az alkalmazás a helyi fürtre épül, és telepítve lesz.

A szolgáltatások futásának megkezdése után megtekintheti a létrehozott eseménykövetés windowsos (ETW) események **etdiagnosztikai események** ablakban. Vegye figyelembe, hogy a megjelenített események az állapotmentes és az állapotalapú szolgáltatásból származnak az alkalmazásban. Az adatfolyam szüneteltetése a **Szünet** gombra kattintva. Ezután az üzenet kibontásával megvizsgálhatja az üzenet részleteit.

> [!NOTE]
> Az alkalmazás futtatása előtt győződjön meg arról, hogy egy helyi fejlesztési fürt fut. A helyi környezet beállításával kapcsolatos információkért tekintse meg az [első lépések útmutatót.](service-fabric-get-started.md)
> 
> 

![Diagnosztikai események megtekintése a Visual Studio alkalmazásban](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>További lépések
[A Service Fabric-alkalmazás hibakeresése a Visual Studióban](service-fabric-debugging-your-application.md)

[Első lépések: Service Fabric Web API-szolgáltatások az OWIN saját üzemeltetésével](service-fabric-reliable-services-communication-webapi.md)

[További információ a megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md)

[Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[Alkalmazás frissítése](service-fabric-application-upgrade.md)

[Fejlesztői referencia a megbízható szolgáltatásokhoz](https://msdn.microsoft.com/library/azure/dn706529.aspx)

