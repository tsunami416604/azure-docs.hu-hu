---
title: Az Azure Service Fabric-aktor-alapú szolgáltatás létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan létrehozásához, hibakereséséhez és a C# Service Fabric Reliable Actors használatával az első actor-alapú szolgáltatás üzembe helyezése.
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
ms.openlocfilehash: f92fe2432051b148bf0b35fccc3fa33db9b66a14
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093150"
---
# <a name="getting-started-with-reliable-actors"></a>Reliable Actors – első lépések
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-actors-get-started.md)
> * [Java Linuxon](service-fabric-reliable-actors-get-started-java.md)

Ez a cikk végigvezeti a létrehozása és hibakeresése a Visual Studióban egyszerű Reliable Actors-alkalmazás. A Reliable actors – további információkért lásd: [Bevezetés a Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a Service Fabric fejlesztési környezet, beleértve a Visual Studióban, állítsa be a számítógépre. További információkért lásd: [a fejlesztési környezet beállítása](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Hozzon létre egy új projektet a Visual Studióban

Indítsa el a Visual Studio 2015 vagy újabb rendszergazdaként, és ezután hozzon létre egy új **Service Fabric-alkalmazás** projekt:

![Service Fabric tools Pro Visual Studio – új projekt][1]

A következő párbeszédpanelen válasszon **Aktorszolgáltatás** alatt **.Net Core 2.0** , és adja meg a szolgáltatás nevét.

![Service Fabric-projekt sablonok][5]

A létrehozott projekt jelenít meg az alábbi struktúrával:

![A Service Fabric-projekt struktúrája][2]

## <a name="examine-the-solution"></a>Vizsgálja meg a megoldás

A megoldás három projektet tartalmaz:

* **Az alkalmazásprojekt (MyApplication)**. Ez a projekt csomagok központi telepítés együtt a szolgáltatásokhoz. Tartalmazza a *ApplicationManifest.xml* és a PowerShell-parancsfájlok kezelése az alkalmazást.

* **A felület project (HelloWorld.Interfaces)**. Ez a projekt az aktor illesztőjének definícióját tartalmazza. Aktorok bármely bármely nevű projekt lehet definiálni.  Az illesztő határozza meg, hogy az aktor szerződést az aktor implementálása és az aktort meghívó ügyfelek osztoznak által közösen használt.  Ügyfél-projektek is függnek tőle, mert általában logikus definiálja azt a szerelvény, amely elkülönül az aktor implementálása.

* **Az aktorszolgáltatás-projektet (HelloWorld)**. Ez a projekt határozza meg, amelyet szeretne üzemeltetni az aktor Service Fabric-szolgáltatást. Az aktor megvalósítása tartalmaz *HelloWorld.cs*. Az aktor implementálását OD základního typu osztálynak `Actor` valósítja meg a meghatározott felületek és a *MyActor.Interfaces* projekt. Az aktor osztálya is meg kell valósítania egy konstruktort, amely fogad egy `ActorService` példány és a egy `ActorId` , majd azokat továbbítja az alapszintű `Actor` osztály.
    
    A projekt is tartalmaz *Program.cs*, amely használatával a Service Fabric-futtatókörnyezet regisztrálja aktor osztályok `ActorRuntime.RegisterActorAsync<T>()`. A `HelloWorld` osztály már regisztrálva van. Minden olyan további aktorok megvalósításokhoz, hozzáadja a projekthez is szerepelnie kell a `Main()` metódust.

## <a name="customize-the-helloworld-actor"></a>A HelloWorld aktor testreszabása

A projekt sablont határozza meg az egyes módszerek a `IHelloWorld` felületet, és alkalmazza őket a `HelloWorld` aktor implementálását.  Cserélje le azokat a módszereket, hogy az aktorszolgáltatás vissza egy egyszerű "Hello World" karakterlánc.

Az a *HelloWorld.Interfaces* a projektre a *IHelloWorld.cs* fájlt, cserélje le a felületdefiníció a következőképpen:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

Az a **HelloWorld** projektre a **HelloWorld.cs**, cserélje le a teljes osztály-definíciót a következő:

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

Nyomja meg **Ctrl-Shift-B** lefordítja a projekt buildjének elkészítéséhez, és győződjön meg arról, hogy minden rendben.

## <a name="add-a-client"></a>Ügyfél hozzáadása

Hozzon létre egy egyszerű konzolalkalmazást az aktorszolgáltatás meghívásához.

1. Kattintson a jobb gombbal a megoldásra a Megoldáskezelőben > **Hozzáadás** > **új projekt...** .

2. Alatt a **.NET Core** típusok projektre, válassza a **Console App (.NET Core)**.  Adja a projektnek *ActorClient*.
    
    ![Adja hozzá az új projekt párbeszédpanel][6]    
    
    > [!NOTE]
    > Egy olyan konzolalkalmazást nem az alkalmazás általában használhat, mint a Service Fabric ügyfél típusa, de lehetővé teszi egy kényelmes példa a Hibakeresés és tesztelés a helyi Service Fabric-fürt használatával.

3. A konzolalkalmazást kell lennie egy 64 bites alkalmazáshoz a felület projekt és egyéb függőségek való kompatibilitás megőrzése érdekében.  A Megoldáskezelőben kattintson a jobb gombbal a **ActorClient** projektre, és kattintson a **tulajdonságok**.  Az a **hozhat létre** lapra, és állítsa **Platform cél** való **x64**.
    
    ![Tulajdonságok létrehozása][8]

4. Az ügyfélprojekt a reliable actors – NuGet-csomag szükséges.  Kattintson a **Tools** (Eszközök)  > **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemre.  A Package Manager Console adja meg a következő parancsot:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    A NuGet-csomagot és minden függőségét települnek a ActorClient projektben.

5. Az ügyfélprojekt a felületek projekt egy hivatkozást is szükséges.  A ActorClient projektben kattintson a jobb gombbal **függőségek** majd **hivatkozás hozzáadása...** .  Válassza ki **projektek > megoldás** (Ha nincs bejelölve), majd osztásjelek melletti jelölőnégyzetet, és **HelloWorld.Interfaces**.  Kattintson az **OK** gombra.
    
    ![Adja hozzá a hivatkozási párbeszédpanel][7]

6. A ActorClient projektben cserélje le a teljes tartalmát *Program.cs* a következő kóddal:
    
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

## <a name="running-and-debugging"></a>Rendszert futtató és a hibakeresés

Nyomja meg **F5** létrehozásához, üzembe helyezése, és helyileg futtatja az alkalmazást a Service Fabric fejlesztési fürtöt.  A telepítési folyamat során láthatja a folyamat állapotát a a **kimeneti** ablak.

![A Service Fabric hibakeresési kimeneti ablak][3]

Ha a szöveget a kimenetben *az alkalmazás készen áll*, és tesztelheti a szolgáltatást a ActorClient alkalmazás használatával lehetséges.  A Megoldáskezelőben kattintson a jobb gombbal a a **ActorClient** projektre, majd kattintson a **Debug** > **új példány indítása**.  A parancssori alkalmazás megjelenjen az aktorszolgáltatás kimenete.

![Alkalmazás kimenete][9]

> [!TIP]
> A Service Fabric Actors-futtatókörnyezet bocsát ki néhány [aktor módszerek kapcsolódó események és teljesítményszámlálók](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Ezek a diagnosztikai és az alkalmazásteljesítmény-figyelési hasznosak.

## <a name="next-steps"></a>További lépések
Tudjon meg többet [a Service Fabric platformot használja a Reliable Actors](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png