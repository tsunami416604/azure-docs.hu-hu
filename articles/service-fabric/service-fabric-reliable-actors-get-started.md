---
title: Az első szereplő-alapú Azure mikroszolgáltatási létrehozása a C# |} Microsoft Docs
description: Ez az oktatóanyag végigvezeti a létrehozása, a Hibakeresés és a Service Fabric Reliable Actors használatával egyszerű szereplő-alapú szolgáltatás telepítése.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: 32d3fa09c863c47753267e97e7c4730dff869887
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="getting-started-with-reliable-actors"></a>Bevezetés a Reliable Actors
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-actors-get-started.md)
> * [Java Linuxon](service-fabric-reliable-actors-get-started-java.md)

Ez a cikk végigvezeti a létrehozása és a Visual Studio egy egyszerű megbízható szereplő alkalmazásban. További információ a Reliable Actors: [Bevezetés a Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik-e a Service Fabric fejlesztőkörnyezet, beleértve a Visual Studio, állítsa be a számítógépre. További információkért lásd: [a fejlesztési környezet beállítása](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>A Visual Studio új projekt létrehozása

Indítsa el a Visual Studio 2015-ös vagy újabb verziójú, rendszergazda, és ezután hozzon létre egy új **Service Fabric-alkalmazás** projekt:

![Service Fabric tools for Visual Studio – új projekt][1]

A következő párbeszédpanelen válassza ki a **szereplő szolgáltatás** alatt **.Net 2.0 alapvető** , és adja meg a szolgáltatás nevét.

![A Service Fabric projektsablonjai][5]

A létrehozott projekt jeleníti meg az alábbi szerkezettel:

![A Service Fabric-projekt struktúra][2]

## <a name="examine-the-solution"></a>Vizsgálja meg a megoldás

A megoldás három projektet tartalmaz:

* **Az alkalmazási projektet (MyApplication)**. Ebben a projektben az összes szolgáltatást együtt a központi telepítési csomagokat. Tartalmazza a *ApplicationManifest.xml* és az alkalmazás kezelésére szolgáló PowerShell-parancsfájlokat.

* **A felület projekt (HelloWorld.Interfaces)**. A projekt a felületdefiníció szereplő tartalmaz. Aktor felületek bármely bármely nevű projekt adható meg.  A felület a szereplő egyezményben szereplő végrehajtása és a szereplő hívja az ügyfelek által közösen használt határozza meg.  Ügyfél-projektek is függnek tőle, mert általában érdemes egy szerelvény, amely nem szereplő végrehajtása meg.

* **Az aktor projekt (HelloWorld)**. Ez a projekt határozza meg a Service Fabric-szolgáltatás, amelyet a szereplő üzemeltetéséhez. Az aktor végrehajtásának tartalmaz *HellowWorld.cs*. Az aktor megvalósítása egy osztályt, amely alaptípusból származik `Actor` és a meghatározott felületek megvalósítja a *MyActor.Interfaces* projekt. Az aktor osztály is meg kell valósítania elfogadó konstruktorral egy `ActorService` példány és egy `ActorId` , és továbbadja őket a következő `Actor` osztály.
    
    A projekt is tartalmaz *Program.cs*, amely a Service Fabric futásidejű használatával regisztrálja szereplő osztályok `ActorRuntime.RegisterActorAsync<T>()`. A `HelloWorld` osztály már regisztrálva van. Megvalósított hozzáadni a projekthez további szereplő megoldásokkal is szerepelnie kell a `Main()` metódust.

## <a name="customize-the-helloworld-actor"></a>A HelloWorld szereplő testreszabása

A projekt sablon meghatározza az egyes módszerek a `IHelloWorld` illesztőfelület és megvalósítja az azokat a `HelloWorld` szereplő végrehajtására.  Cserélje le azokat a módszereket, így a szereplő szolgáltatás egyszerű "Hello, World" karakterláncot ad vissza.

A a *HelloWorld.Interfaces* a projekt a *IHelloWorld.cs* fájlt, cserélje le a felületdefiníció az alábbiak szerint:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

Az a **HelloWorld** a projekt **HelloWorld.cs**, cserélje le a teljes osztálydefiníció az alábbiak szerint:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Nyomja le az **Ctrl-Shift-B** a projekt felépítéséhez, és győződjön meg arról, hogy minden lefordítja.

## <a name="add-a-client"></a>Ügyfél hozzáadása

Az aktor hívó egyszerű Konzolalkalmazás létrehozása.

1. Kattintson a jobb gombbal a megoldásban a Solution Explorer > **Hozzáadás** > **új projekt...** .

2. Az a **.NET Core** projekt típusok, válassza a **Konzolalkalmazás (.NET Core)**.  Nevet a projektnek *ActorClient*.
    
    ![Adja hozzá az új projekt párbeszédpanelje][6]    
    
    > [!NOTE]
    > A Konzolalkalmazás nincs az alkalmazás általában használja a Service Fabric ügyfélként típusa, de egy kényelmes példa Hibakeresés és tesztelése a helyi Service Fabric-fürt használatával teszi.

3. A konzolalkalmazást kell lennie egy 64 bites alkalmazás, a felület projekt és más függőségek kompatibilitás megőrzése érdekében.  A Megoldáskezelőben kattintson a jobb gombbal a **ActorClient** projektre, és kattintson a **tulajdonságok**.  Az a **Build** lapon **Platform cél** való **x64**.
    
    ![Tulajdonságok létrehozása][8]

4. Az ügyfélprojekt a megbízható szereplője NuGet-csomag szükséges.  Kattintson a **Tools** (Eszközök)  > **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemre.  A Csomagkezelő konzol adja meg a következő parancsot:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    A NuGet-csomagot és annak függőségeit a ActorClient projektben van telepítve.

5. Az ügyfélprojekt is igényli egy hivatkozást a felületek projektre.  A ActorClient projektben kattintson a jobb gombbal **függőségek** majd **hivatkozás hozzáadása...** .  Válassza ki **projektek > megoldás** (Ha nincs bejelölve), és ezután osztásjelek melletti jelölőnégyzet **HelloWorld.Interfaces**.  Kattintson az **OK** gombra.
    
    ![Hivatkozás párbeszédpanel hozzáadása][7]

6. A ActorClient projekt cserélje le a teljes tartalmát *Program.cs* az alábbi kódra:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Rendszert futtató és hibakeresés

Nyomja le az **F5** hozhat létre, telepítheti és futtathatja az alkalmazást helyileg, a Service Fabric fejlesztési fürt.  A telepítési folyamat során megjelenik a folyamatjelző a **kimeneti** ablak.

![A Service Fabric hibakeresési kimeneti ablak][3]

A kimenet tartalmazza a szöveg, ha *az alkalmazás készen áll az*, tesztelheti a szolgáltatást a ActorClient alkalmazás használatával lehetséges.  A Megoldáskezelőben kattintson a jobb gombbal a a **ActorClient** projektre, majd kattintson a **Debug** > **Start új példány**.  A parancssori alkalmazás megjelenjen-e a szereplő szolgáltatás kimenete.

![alkalmazás kimenete][9]

> [!TIP]
> A Service Fabric szereplője futásidejű megfelelően kibocsát egy [szereplő módszerek kapcsolódó események és teljesítményszámlálók](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). A diagnosztika és teljesítményfigyelés hasznosak.

## <a name="next-steps"></a>További lépések
További információ [Reliable Actors használatát a Service Fabric-platformról](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png