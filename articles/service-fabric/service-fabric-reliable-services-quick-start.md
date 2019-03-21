---
title: Hozzon létre az első Service Fabric-alkalmazás a C# |} A Microsoft Docs
description: Bevezetés a Microsoft Azure Service Fabric-alkalmazás létrehozása állapot nélküli és állapotalapú szolgáltatásokkal.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: 26520e3ff8f5bb97866015d3148b4c8b8813b319
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168570"
---
# <a name="get-started-with-reliable-services"></a>Ismerkedés a Reliable Services használatával
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-quick-start.md)
> * [Java Linuxon](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Az Azure Service Fabric-alkalmazás egy vagy több olyan szolgáltatást, futtathatja a kódot tartalmaz. Ez az útmutató bemutatja, hogyan létrehozása állapot nélküli és állapotalapú mind a Service Fabric-alkalmazások [Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Alapfogalmak
Ismerkedés a Reliable Services szolgáltatással, akkor csak kell néhány alapvető fogalmak megismerése:

* **Szolgáltatás típusa**: Ez a szolgáltatás megvalósítása. Azt határozza meg az osztályt, írása, amely kibővíti `StatelessService` és más kód vagy függőségeket, egy nevet és a egy verziószámot együtt használni.
* **Szolgáltatáspéldány nevű**: A szolgáltatás futtatásához hoz létre a típusú szolgáltatás elnevezett példányok sokkal például osztálytípus figyelőobjektum-példányok létrehozása. Szolgáltatáspéldány egy URI-t használó formája névvel rendelkezik a "fabric: /" séma, mint például "fabric: / MyApp/MyService".
* **Szolgáltatásgazda**: A nevesített szolgáltatáspéldányok hoz létre a gazdafolyamatokon belül futó kell. A szolgáltatásgazda az csak az egy folyamatot, ahol a szolgáltatás példányát futtathatja.
* **Eszközregisztrációs szolgáltatás**: Regisztrációs magában foglal minden. A szolgáltatás típusának futtatásához egy gazdagépet a szolgáltatás példányainak, Service Fabric lehetővé teszi a Service Fabric-futtatókörnyezet regisztrálva kell lennie.  

## <a name="create-a-stateless-service"></a>Állapotmentes szolgáltatás létrehozása
Állapotmentes szolgáltatás olyan szolgáltatás, amely jelenleg a felhőalapú alkalmazások általános. Állapot nélküli számít, mert a szolgáltatás nem tartalmaz adatokat, amelyek megbízhatóan tárolt vagy magas rendelkezésre állásúvá kell. Ha egy állapotmentes szolgáltatás egy példányának leáll, az összes belső állapotuk elvész. Az ilyen típusú szolgáltatás állapota kell őrizhető meg egy külső áruházban, például az Azure-táblák és a egy SQL database-hez, hogy magas rendelkezésre állású és megbízható.

Indítsa el a Visual Studio 2015 vagy Visual Studio 2017-rendszergazdaként, és hozzon létre egy új Service Fabric-projektet nevű *HelloWorld*:

![Az új projekt párbeszédpanel segítségével új Service Fabric-alkalmazás létrehozása](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Ezután hozzon létre egy állapotmentes szolgáltatás projektet **.NET Core 2.0** nevű *HelloWorldStateless*:

![A második párbeszédpanelen állapotmentes szolgáltatás projekt létrehozása](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

A megoldás mostantól két projektet tartalmaz:

* *HelloWorld*. Ez a *alkalmazás* projektet, amely tartalmazza a *szolgáltatások*. Az alkalmazásjegyzékben, amely leírja az alkalmazás, valamint egy PowerShell-parancsfájlok, amelyek segítenek az alkalmazások üzembe helyezésére száma is tartalmaz.
* *HelloWorldStateless*. Ez az a-projekt. Az állapotmentes szolgáltatás megvalósítása tartalmaz.

## <a name="implement-the-service"></a>A szolgáltatás megvalósítása
Nyissa meg a **HelloWorldStateless.cs** fájlt a szolgáltatási projektben. Service Fabric segítségével a szolgáltatás bármely üzleti logika futtatható. A szolgáltatás API-t biztosít a kód két belépési pontok:

* Nevű egy nyílt belépési pont metódust *RunAsync*, megkezdheti a számítási feladatokat, beleértve a hosszú ideig futó számítási feladatok végrehajtása.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Egy kommunikációs belépési ponthoz, amelyen csatlakoztathatja a kommunikációs verem választott, például az ASP.NET Core. Ez az, ahol elkezdheti kérések fogadása a felhasználók és más szolgáltatások védelmét.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Ebben az oktatóanyagban fogunk dolgozni a `RunAsync()` belépési pont metódust. Ez az, ahol azonnal elkezdheti a kódja fut.
A projekt sablon tartalmaz egy minta megvalósítását `RunAsync()` , amely növeli a működés közbeni száma.

> [!NOTE]
> További információk a kommunikációs verem használata: [OWIN helyi üzemeltetési szolgáltatások Service Fabric webes API](service-fabric-reliable-services-communication-webapi.md)
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

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

A platform meghívja ezt a módszert, ha egy szolgáltatás egy példányának elhelyezett, és készen áll a hajtsa végre. Egy állapotmentes szolgáltatás, amely egyszerűen azt jelenti, hogy a szolgáltatás példánya megnyitásakor. A megszakítás jogkivonat van megadva, a koordinációt, amikor a service-példányt kell zárni. A Service Fabric-példányok a megnyitása vagy bezárása ciklus fordulhat elő, a szolgáltatás életciklusa alatt számos alkalommal egészére. Ez akkor fordulhat elő, beleértve a különböző okok miatt:

* A rendszer a szolgáltatáspéldányok terheléselosztási erőforrás helyezi.
* A kód fordulnak elő hibák.
* Az alkalmazás- vagy frissül.
* Az alapul szolgáló hardver során kimaradás lép fel.

Ennek vezénylését kezelt a rendszer, hogy a szolgáltatás magas rendelkezésre állású és megfelelően elosztott terhelésű.

`RunAsync()` érdemes blokkolja a szinkron módon történik. RunAsync megvalósítását kell adja vissza egy feladat vagy hosszan futó vagy blokkoló műveleteket, hogy a modul folytatja várakoztatás. Vegye figyelembe a a `while(true)` ciklus az előző példában, a feladat-adatszolgáltató `await Task.Delay()` szolgál. Ha a számítási feladatok szinkron módon kell letiltja, olyan időszakra ütemezze az új feladat `Task.Run()` a a `RunAsync` végrehajtására.

A számítási feladat megszakításának célja egy olyan együttműködési vezényli a megadott megszakítás jogkivonatot. A rendszer megvárja a feladat befejezéséhez (a sikeres befejezése, megszakítás vagy hiba), mielőtt áthelyezi azt. Fontos, hogy tartsa tiszteletben a megszakítás jogkivonat, Befejezés munka és a kilépéshez `RunAsync()` ismerhető meg leggyorsabban, amikor a rendszer kéri a megszakítás.

Az állapotmentes szolgáltatás ebben a példában egy helyi változó tárolja a száma. De mivel ez egy állapotmentes szolgáltatás, a tárolt érték csak az aktuális élettartama a szolgáltatáspéldány létezik-e. A szolgáltatás áthelyezi vagy újraindul, ha az érték elvész.

## <a name="create-a-stateful-service"></a>Állapotalapú szolgáltatás létrehozása
A Service Fabric állapotalapú szolgáltatás egy új adatraktározási mutatja be. Az állapotalapú szolgáltatások megbízható a szolgáltatásban, a kód által használt közös elhelyezésű állapot fenntartására. Állapot magas rendelkezésre állású legyen a Service Fabric által állapotban ahhoz, hogy egy külső tároló megőrzése nélkül.

Konvertálható egy számláló értékét állapot nélküli, magas rendelkezésre állású, tartós, akkor is, ha a szolgáltatás áthelyezi vagy újraindul, az állapotalapú szolgáltatások van szükség.

Az egyazon *HelloWorld* alkalmazás, hozzáadhat egy új szolgáltatás és a szolgáltatások jobb gombbal a projekt hivatkozásait **Hozzáadás -> új Service Fabric-szolgáltatás**.

![Szolgáltatás hozzáadása a Service Fabric-alkalmazás](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Válassza ki **.NET Core 2.0 -> állapotalapú szolgáltatás** , és nevezze el *HelloWorldStateful*. Kattintson az **OK** gombra.

![Az új projekt párbeszédpanel segítségével hozzon létre egy új Service Fabric állapotalapú szolgáltatás](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Az alkalmazás most már két szolgáltatások: az állapotmentes szolgáltatás *HelloWorldStateless* és az állapotalapú szolgáltatásból *HelloWorldStateful*.

Az állapotalapú szolgáltatások a ugyanazon belépési pontok állapotmentes szolgáltatás rendelkezik. A fő különbség az, rendelkezésre állását egy *riasztásiállapot-szolgáltató* , amely megbízható tárolását állapota. A Service Fabric tartalmaz egy állam adatbázisszolgáltatói implementációt nevű [a Reliable Collections](service-fabric-reliable-services-reliable-collections.md), amely készíthet a replikált adattárakon keresztül a Reliable State Manager. Egy állapotalapú Reliable Services alapértelmezés szerint ez szolgáltatóját használja.

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
`RunAsync()` állapotalapú és állapotmentes szolgáltatások hasonlóan működik. Azonban egy állapotalapú szolgáltatásban, a platform további munkát végez az Ön nevében hajtja végre a rendszer `RunAsync()`. Ezt a munkát ezzel biztosítható, hogy a Reliable State Manager és a Reliable Collections készen áll a használatára is tartalmazhatnak.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>A Reliable Collections és a Reliable State Manager
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) szótár megvalósítása, amely segítségével megbízhatóan tárolja az állapot a szolgáltatásban. A Service Fabric és a Reliable Collections, az adatok közvetlenül a szolgáltatásban egy külső állandó tároló nélkül tárolhatja. A Reliable Collections legyen az adatok magas rendelkezésre állású. A Service Fabric úgy hajtja ezt végre létrehozása és kezelése több *replikák* a szolgáltatás az Ön számára. Egy API, amely a kezelésével járó összetettséget, e replikák és az állapotváltozási adat áramlik kódbázis is tartalmazza.

A Reliable Collections képes tárolni bármilyen .NET, beleértve az egyéni típusok, mindössze pár korlátozásokkal:

* A Service Fabric biztosítja az állapot szerint magas rendelkezésre állású *replikálása* csomópont, és a Reliable Collections között állapota minden egyes replikának tárolni az adatokat a helyi lemezre. Ez azt jelenti, hogy minden, a Reliable Collections tárolt kell *szerializálható*. Alapértelmezés szerint a Reliable Collections használata [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) szerializálásra, ezért fontos győződjön meg arról, hogy a típusok a következők [támogatja az adatok szerződés szerializáló](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) az alapértelmezett szerializáló használata esetén.
* Objektumok a magas rendelkezésre állású lesznek replikálva, amikor a Reliable Collections tranzakció véglegesítése után. A Reliable Collections tárolt objektumok őrzi meg a helyi memória a szolgáltatásban. Ez azt jelenti, hogy rendelkezik-e egy helyi odkaz nA Objekt.
  
   Fontos, hogy Ön nem mutálódni azokat az objektumokat helyi példánya egy tranzakcióban a megbízható gyűjteményben frissítési műveletek végrehajtása nélkül. Ennek oka az, helyi példánya objektumok módosításai nem lesznek automatikusan replikálva. Helyezze be újra a szótár be újra az objektum vagy valamelyikével kell a *frissítése* a szótár metody.

A Reliable State Manager kezeli a Reliable Collections az Ön számára. Egyszerűen kérhet a Reliable State Manager egy megbízható gyűjteményben név szerint bármikor és bárhol a service-ben. A Reliable State Manager biztosítja, hogy vissza egy hivatkozást. Nem javasoljuk, hogy menti a megbízható gyűjtemény példányok mutató hivatkozásokat a tag változók vagy tulajdonságai. Speciális ügyelni kell annak érdekében, hogy a hivatkozás értéke egy példánya mindig a szolgáltatás-életciklusának. A Reliable State Manager kezeli ezt a munkát, az Ön számára, és ismételje meg a műveletet látogatások van optimalizálva.

### <a name="transactional-and-asynchronous-operations"></a>Tranzakciós és az aszinkron műveletek
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

A Reliable Collections rendelkeznek számos, ugyanazokat a műveleteket, hogy azok `System.Collections.Generic` és `System.Collections.Concurrent` igényló, kivéve a LINQ. A Reliable Collections műveletek aszinkron jellegűek. Ennek az oka az írási műveletek a Reliable Collections replikálja, és megőrizni az adatokat a lemezre i/o-műveletek végrehajtása.

Megbízható gyűjtemény műveletek *tranzakciós*, így megtarthatja állapot konzisztens több megbízható gyűjtemények és műveleteket. Például, előfordulhat, hogy egy munkaelem egy megbízható üzenetsorból sorból végrehajtania egy műveletet, a és mentheti az eredményt egy megbízható szótárban tároló, egy tranzakción belül. Ez atomi műveletnek számít, és Ez garantálja, hogy vagy a teljes művelet sikeres lesz, vagy a teljes művelet visszaállítja. Hiba bekövetkezése után, az elem eltávolítása a sorból, de még mielőtt mentené az eredményt, a teljes tranzakció vissza lesz állítva, és az elem továbbra is a feldolgozási várakozási sorban.

## <a name="run-the-application"></a>Az alkalmazás futtatása
Mi most térjen vissza a *HelloWorld* alkalmazás. Most már lefordíthatja, és a szolgáltatások üzembe helyezéséhez. Amikor lenyomja **F5**, az alkalmazás fogja beépített és telepíteni kell a a helyi fürtöt.

Után a szolgáltatások kezdjen programokat futtatni, megtekintheti a létrehozott esemény-nyomkövetése Windows (ETW) események egy **diagnosztikai események** ablak. Vegye figyelembe, hogy a megjelenített események az állapotmentes szolgáltatás és az állapotalapú szolgáltatásból, az alkalmazás is. A stream kattintva szüneteltetheti a **szüneteltetése** gombra. Az üzenet részleteit az üzenet kibontásával majd ellenőrizheti.

> [!NOTE]
> Mielőtt futtatja az alkalmazást, ellenőrizze, hogy a helyi fejlesztési fürtön futó. Tekintse meg a [– első lépések útmutató](service-fabric-get-started.md) a helyi környezet beállításával kapcsolatos információkat.
> 
> 

![Diagnosztikai események megtekintése a Visual Studióban](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>További lépések
[A Visual Studióban a Service Fabric-alkalmazás hibakeresése](service-fabric-debugging-your-application.md)

[Első lépések: Service Fabric webes API szolgáltatások OWIN helyi üzemeltetés](service-fabric-reliable-services-communication-webapi.md)

[További tudnivalók a Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[Alkalmazásfrissítés](service-fabric-application-upgrade.md)

[Reliable Services – fejlesztői referencia](https://msdn.microsoft.com/library/azure/dn706529.aspx)

