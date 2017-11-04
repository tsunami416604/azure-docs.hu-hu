---
title: "Az első Service Fabric-alkalmazás létrehozása a C# |} Microsoft Docs"
description: "Bevezetés a Microsoft Azure Service Fabric-alkalmazás létrehozása az állapotmentes és állapotalapú szolgáltatással."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: f67b69e7ad1f7588280de82669040bad5ec6172b
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="get-started-with-reliable-services"></a>Ismerkedés a Reliable Services használatával
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-quick-start.md)
> * [Java Linuxon](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Az Azure Service Fabric-alkalmazás egy vagy több olyan szolgáltatás, amely az Ön kódjának futtatásához tartalmazza. Ez az útmutató bemutatja, hogy az állapotmentes és állapotalapú mind a Service Fabric-alkalmazások létrehozása [Reliable Services](service-fabric-reliable-services-introduction.md).  A Microsoft Virtual Academy videó is bemutatja, hogyan hozzon létre egy megbízható állapotmentes szolgáltatások:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=s39AO76yC_7206218965">  
<img src="./media/service-fabric-reliable-services-quick-start/ReliableServicesVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="basic-concepts"></a>Alapfogalmak
Bevezetés a Reliable Services használatába, hogy meg kell ismernie néhány főbb fogalmait és kifejezéseit:

* **Szolgáltatás típusa**: Ez a szolgáltatás megvalósítása. Azt határozza meg a osztályt írt `StatelessService` és bármely más kód vagy függőségek használt, valamint nevét és verziószámát.
* **Szolgáltatáspéldány nevű**: a szolgáltatás futtatásához hoz létre a szolgáltatástípus elnevezett példányok sokkal például létrehozhat egy osztálytípus objektum példánya. A szolgáltatáspéldány egy URI segítségével formájában névvel rendelkezik a "fabric: /" sémáját, például a "fabric: / MyApp/MyService".
* **Szolgáltatásgazda**: egy gazdafolyamaton belül futtatnia kell a nevesített szolgáltatáspéldány hoz létre. A szolgáltatásgazda csak az egyik folyamat, ahol a szolgáltatás példányának futtatható.
* **Eszközregisztrációs szolgáltatás**: regisztráció egyesíti mindent. A szolgáltatás típusának regisztrálva kell lennie a Service Fabric-futtatókörnyezet engedélyezése az osztályt létrehozni a Service Fabric-szolgáltatás gazdán futtatásához.  

## <a name="create-a-stateless-service"></a>Állapot nélküli szolgáltatás létrehozása
Egy állapotmentes szolgáltatások olyan szolgáltatás, amely jelenleg a alapértelmezetté a felhőalapú alkalmazásokhoz. Állapot nélküli tekinthető, mert a szolgáltatás nem tartalmaz, amely megbízhatóan tárolja vagy magas rendelkezésre állású kell adatokat. Ha az állapot nélküli szolgáltatás egy példánya leáll, az összes belső állapotában elvész. Az ilyen típusú szolgáltatás állapota kell sikerült megőrizni a külső áruházban, például az Azure-táblákban vagy SQL-adatbázis, ahhoz, hogy a magas rendelkezésre állású és megbízható tenni.

Indítsa el a Visual Studio 2015-öt vagy a Visual Studio 2017 rendszergazdaként, és hozzon létre egy új Service Fabric-projektet nevű *HelloWorld*:

![Az új projekt párbeszédpanel segítségével hozzon létre egy új Service Fabric-alkalmazás](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Majd létre szeretne hozni egy állapotmentes szolgáltatások nevű projekt *HelloWorldStateless*:

![A második párbeszédpanelen állapotmentes szolgáltatások-projekt létrehozása](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

A megoldás most két projektet tartalmaz:

* *HelloWorld*. Ez a *alkalmazás* projektet, amely tartalmazza a *szolgáltatások*. Az alkalmazás jegyzékében, mely leírja az alkalmazást, valamint a PowerShell-parancsfájlok, amelyek segítenek az alkalmazás központi telepítése számos is tartalmaz.
* *HelloWorldStateless*. Ez az a projektet. Az állapotmentes szolgáltatások általi tartalmaz.

## <a name="implement-the-service"></a>A szolgáltatás megvalósítása
Nyissa meg a **HelloWorldStateless.cs** fájlt a szolgáltatás projektben. A Service Fabric szolgáltatás futtatható bármely üzleti logika. A service API két belépési pontok biztosít a kódot:

* Egy nyílt belépési pont metódus hívása *RunAsync*, ahol el lehet kezdeni a munkaterheléseket, ideértve a hosszan futó számítási feladatok végrehajtása.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Ahol a kommunikációs verem szerkesztőprogramban, például az ASP.NET Core csatlakoztatható kommunikációs belépési pont. Ez azért, ahol elindíthatja kéréseket fogad a felhasználók és más szolgáltatások védelmét.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Ebben az oktatóanyagban tárgyaljuk a `RunAsync()` belépési pont metódusa. Ez azért, ahol azonnal elindíthatja a kódja.
A projekt sablon tartalmaz egy minta végrehajtásának `RunAsync()` , amely növeli a működés közbeni száma.

> [!NOTE]
> A kommunikációs verem használható kapcsolatos részletekért lásd: [Service Fabric Web API szolgáltatások OWIN önálló üzemeltetéséhez.](service-fabric-reliable-services-communication-webapi.md)
> 
> 

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

        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

A platform ezt a módszert hívja, ha a szolgáltatás egy példánya elhelyezett és a végrehajtásra kész. Az állapotmentes szolgáltatások, amely egyszerűen jelenti, hogy a service-példány már meg van nyitva. A megszakítási jogkivonat koordinálására, amikor a szolgáltatáspéldány kell lezárni van megadva. A Service Fabric a megnyitása/bezárása ciklus egy szolgáltatáspéldány akkor fordulhat elő, a szolgáltatás életciklusa alatt számos alkalommal egész. Ez akkor fordulhat elő, beleértve a különböző okokból:

* A rendszer áthelyezi a szolgáltatáspéldány terheléselosztás erőforrás.
* Hibák merülnek fel, a kódban.
* Az alkalmazás- vagy frissítése.
* Az alapul szolgáló hardverben során kimaradás lép fel.

A vezénylési kezeli a rendszer megtartani a szolgáltatást, magas rendelkezésre állású és megfelelően kiegyensúlyozott.

`RunAsync()`meg nem blokkolják szinkron módon történik. RunAsync megvalósítását kell vissza egy feladatot, vagy folytatja a futtatókörnyezet engedélyezi a hosszan futó vagy blokkoló műveleteket a await. Vegye figyelembe, hogy a `while(true)` az előző példában a feladatot visszaadó hurok `await Task.Delay()` szolgál. Ha a terhelést a szinkron módon kell blokkolja, olyan időszakra ütemezze egy új feladatot `Task.Run()` a a `RunAsync` végrehajtására.

A számítási feladatok megszakítását egy együttműködési elérhető a megadott cancellation jogkivonat által összehangolva. A rendszer várakozik a feladat befejezéséhez (által sikeres befejezése, megszakítása vagy hiba), mielőtt az átvitel során. Fontos, hogy a megszakítási token tiszteletben, Befejezés munka és kilépés `RunAsync()` lehető leggyorsabban tegye, amikor a rendszer törlését kéri.

A állapotmentes szolgáltatások példában a count lokális változó tárolja. Azonban mivel ez egy állapotmentes szolgáltatások, a tárolt érték csak az aktuális a szolgáltatáspéldány-életciklus létezik. A szolgáltatás helyezi át, vagy újraindul, az érték elvész.

## <a name="create-a-stateful-service"></a>Az állapotalapú szolgáltatás létrehozása
A Service Fabric egy olyan szolgáltatás, amely állapotalapú új típusú vezet be. Az állapotalapú szolgáltatás megbízhatóan belül a szolgáltatást, a kód által használt közösen elhelyezett is állapotban. Állapot köszönhetően magas rendelkezésre állású Service Fabric nélkül megőrizni a külső áruházban állapotra.

Átalakítása a teljesítményszámláló értéke állapot nélküli magas rendelkezésre állású és állandó, akkor is, ha a szolgáltatás áthelyezi vagy újraindul, szükség van egy állapotalapú szolgáltatásra.

Ugyanazon *HelloWorld* alkalmazás, adhat hozzá egy új szolgáltatás kattintson a jobb gombbal a szolgáltatásokra vonatkozó hivatkozások a projektet, majd válassza **Hozzáadás -> új Service Fabric-szolgáltatás**.

![Szolgáltatás hozzáadása a Service Fabric-alkalmazás](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Válassza ki **állapotalapú alkalmazások és szolgáltatások szolgáltatás** és adjon neki nevet *HelloWorldStateful*. Kattintson az **OK** gombra.

![Az új projekt párbeszédpanel segítségével hozzon létre egy új állapotalapú Service Fabric-szolgáltatás](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Az alkalmazás most már rendelkeznie kell a két szolgáltatás: az állapotmentes szolgáltatások *HelloWorldStateless* és az állapotalapú szolgáltatásból *HelloWorldStateful*.

Állapotalapú szolgáltatási állapot nélküli szolgáltatásként ugyanazon belépési pontokat rendelkezik. A fő különbség a rendelkezésre állását a *állapotszolgáltató* , amely megbízhatóan tárolja állapotát. A Service Fabric tartalmaz egy állapot szolgáltató általi megvalósítása nevű [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md), mely készíthet replikált adatstruktúrák megbízható állapotkezelő keresztül. Egy állapot-nyilvántartó megbízható szolgáltatás alapértelmezés szerint az állapotszolgáltató használja.

Nyissa meg **HelloWorldStateful.cs** a *HelloWorldStateful*, amely tartalmazza a következő RunAsync metódusában:

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

            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
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
`RunAsync()`az állapot nélküli és állapotalapú alkalmazások és szolgáltatások hasonlóan működnek. Azonban az állapotalapú service, a platform hajt végre további feladata az Ön nevében végrehajtása előtt `RunAsync()`. Ez a munkahelyi tartalmazhatnak, ezzel biztosítható, hogy a megbízható állapotkezelője és megbízható gyűjtemények rendszer készen áll a használatra.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Megbízható gyűjtemények és a megbízható állapot-kezelő
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) a szótár megvalósítása, amely megbízható adattárolóban állapotokat a szolgáltatás segítségével. A Service Fabric és megbízható gyűjtemények tárolható adatok közvetlenül a szolgáltatás egy külső állandó tároló szükségessége nélkül. Megbízható gyűjtemények hogy az adatok magas rendelkezésre állású. A Service Fabric ezt a feladatot el létrehozásával és kezelésével több *replikák* meg a szolgáltatás. Az API-k, amelyek számítógépnél kivonatolja a bonyolultságára ezeket a replikák és a Állapotváltások kezelése is tartalmazza.

Megbízható gyűjtemények .NET bármilyen, beleértve a felhasználói típusok, a figyelmeztetések néhány tudja tárolni:

* A Service Fabric lehetővé teszi a állapot magas rendelkezésre állású által *replikálása* csomópontokat, és megbízható gyűjtemények állapot adattárolásra a helyi lemezre minden egyes replikához. Ez azt jelenti, hogy minden, a megbízható gyűjtemények tárolt kell *szerializálható*. Alapértelmezés szerint megbízható gyűjtemények használata [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) szerializálásra, ezért fontos, győződjön meg arról, hogy a típusok a következők [adategyezmény-szerializáló által támogatott](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) az alapértelmezett szerializáló használata esetén.
* Objektumok lesznek replikálva a magas rendelkezésre állású, ha megbízható gyűjteményre tranzakciók véglegesítése után. A megbízható gyűjtemények tárolt objektumok kell tartani a szolgáltatás a helyi memóriához. Ez azt jelenti, hogy rendelkezik-e helyi hivatkozik az objektumra.
  
   Fontos, hogy Ön nem mutálódni azokat az objektumokat helyi példányát a megbízható gyűjtemény a frissítési művelet végrehajtása egy tranzakcióban nélkül. Ez az objektum helyi példányát módosításai nem lesz automatikusan replikálja. Szúrja be az objektumot újra üzembe a szótár kell vagy valamelyikével a *frissítése* metódusai a szótárban.

A megbízható állapotkezelője megbízható gyűjtemények az Ön kezeli. Egyszerűen kérje meg a megbízható állapotkezelője megbízható gyűjtemény neve és bárhol, bármikor a szolgáltatásban. A megbízható állapotkezelője biztosítja, hogy vissza a hivatkozás. Nem ajánlott az, hogy menti megbízható gyűjtemény példányok hivatkozik a tag változók vagy tulajdonságok. Győződjön meg arról, hogy a hivatkozás értéke egy példányát a szolgáltatás életciklusának mindig különös gondot kell fordítani. A megbízható állapotkezelője kezeli a megfelelőek Önnek, és ismétlési látogatások van optimalizálva.

### <a name="transactional-and-asynchronous-operations"></a>Tranzakciós és aszinkron műveletek
```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Megbízható a gyűjteményeknél számos ugyanazokat a műveleteket, amelyek a `System.Collections.Generic` és `System.Collections.Concurrent` megfelelők esetében, kivéve a LINQ. A megbízható gyűjtemények műveletek aszinkron jellegűek. Ennek az az oka megbízható gyűjteményekkel írási műveletek végrehajtása i/o-műveletek történő replikálásához és megőrizni az adatokat a lemezre.

Megbízható gyűjtemény műveletek *tranzakciós*, így biztosítható állapot konzisztens több megbízható gyűjtemények és műveletek. Például akkor lehet, hogy egy megbízható várólista munkaelem created, végrehajtania egy műveletet hozzá és mentése az eredmény megbízható szótárat, egy tranzakción belül. Ez a rendszer egy atomi művelet, és Ez garantálja, hogy vagy a teljes műveletet sikeres lesz, vagy a teljes műveletet állítja vissza. Ha hiba lép fel, akkor created elem után, de az eredmény mentése előtt, a teljes tranzakció vissza lesz állítva, és a marad, a feldolgozási várakozási sorban.

## <a name="run-the-application"></a>Az alkalmazás futtatása
Azt, térjen vissza a *HelloWorld* alkalmazás. Most már létrehozhatja és a szolgáltatások telepítéséhez. Amikor lenyomja az **F5**, az alkalmazás össze lesz állítva és központi telepítése a helyi fürthöz.

Miután a szolgáltatás fut, a létrehozott esemény Windows (nyomkövetés) események megtekintheti egy **a diagnosztikai** ablak. Vegye figyelembe, hogy a megjelenített események az állapotmentes szolgáltatások és az állapotalapú szolgáltatásból, az alkalmazásban. Az adatfolyam szünetelteti gombra kattintva a **szüneteltetése** gombra. Üzenet kibontásával majd ellenőrizheti az üzenet részleteit.

> [!NOTE]
> Az alkalmazás futtatása előtt győződjön meg arról, hogy rendelkezik-e a helyi fejlesztési fürtök fut-e. Tekintse meg a [– első lépések útmutató](service-fabric-get-started.md) a helyi környezet beállításával kapcsolatos információk.
> 
> 

![A Visual Studio diagnosztikai eseményeinek megtekintése](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Következő lépések
[A Visual Studio a Service Fabric-alkalmazás hibakeresése](service-fabric-debugging-your-application.md)

[Első lépések: az OWIN futtató önálló Service Fabric Web API szolgáltatások](service-fabric-reliable-services-communication-webapi.md)

[További információ a megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md)

[Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[Az alkalmazásfrissítés](service-fabric-application-upgrade.md)

[Fejlesztői útmutató a Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

