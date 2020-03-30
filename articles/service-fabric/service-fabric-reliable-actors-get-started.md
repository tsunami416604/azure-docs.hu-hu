---
title: Aktoralapú szolgáltatás létrehozása az Azure Service Fabric en
description: Ismerje meg, hogyan hozhat létre, debug, és üzembe helyezheti az első aktor-alapú szolgáltatás C# szolgáltatás használatával Fabric reliable actors.
author: vturecek
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: a6e4fb48653572139463738c82de632ff7d55074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466256"
---
# <a name="getting-started-with-reliable-actors"></a>A megbízható szereplők kelése
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-actors-get-started.md)
> * [Java Linuxon](service-fabric-reliable-actors-get-started-java.md)

Ez a cikk végigvezeti egy egyszerű reliable actor alkalmazás létrehozásán és hibakereséséen a Visual Studióban. A megbízható szereplőkkel kapcsolatos további információkért [lásd: Bevezetés a szolgáltatásfabric megbízható szereplőinek bemutatása című témakörben.](service-fabric-reliable-actors-introduction.md)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a Service Fabric-fejlesztői környezet, beleértve a Visual Studio, be van állítva a számítógépen. További részletek a [fejlesztői környezet beállításáról.](service-fabric-get-started.md)

## <a name="create-a-new-project-in-visual-studio"></a>Új projekt létrehozása a Visual Studióban

Indítsa el a Visual Studio 2019-es vagy újabb rendszergazdáját, majd hozzon létre egy új **Service Fabric-alkalmazásprojektet:**

![Service Fabric eszközök visual studio - új projekt][1]

A következő párbeszédpanelen válassza az **Aktorszolgáltatás** lehetőséget a **.NET Core 2.0 csoportban,** és adja meg a szolgáltatás nevét.

![Service Fabric projektsablonok][5]

A létrehozott projekt a következő struktúrát mutatja:

![Service Fabric projektstruktúra][2]

## <a name="examine-the-solution"></a>Vizsgálja meg az oldatot

A megoldás három projektet tartalmaz:

* **Az alkalmazásprojekt (MyApplication).** Ez a projekt az összes szolgáltatást együtt csomagolja a telepítéshez. Az *ApplicationManifest.xml* és a PowerShell-parancsfájlokat tartalmazza az alkalmazás kezeléséhez.

* **A felület projekt (HelloWorld.Interfaces)**. Ez a projekt tartalmazza az aktor felületdefinícióját. Az aktor felületek bármely projektben definiálhatók bármilyen névvel.  A felület határozza meg az aktor szerződést, amely az aktor megvalósítása és az aktorhívó ügyfelek által megosztott.  Mivel az ügyfélprojektek függhetnek tőle, általában érdemes azt egy olyan összeállításban definiálni, amely elkülönül az aktor implementációjától.

* **Az aktor szolgáltatási projekt (HelloWorld)**. Ez a projekt határozza meg a Service Fabric szolgáltatás, amely az aktor lesz. Ez tartalmazza a végrehajtását a színész, *HelloWorld.cs*. Az aktor implementáció egy olyan `Actor` osztály, amely az alaptípusból származik, és megvalósítja a *MyActor.Interfaces* projektben definiált felületeket. Az aktorosztálynak olyan konstruktotort is meg kell valósítania, amely elfogad egy `ActorService` példányt, `ActorId` és átad ják az alaposztálynak. `Actor`
    
    Ez a projekt *Program.cs*is tartalmaz, amely regisztrálja az `ActorRuntime.RegisterActorAsync<T>()`aktorosztályokat a Service Fabric futásidejével a használatával. Az `HelloWorld` osztály már regisztrálva van. A projekthez hozzáadott további akta implementációkat is regisztrálni kell a `Main()` metódusban.

## <a name="customize-the-helloworld-actor"></a>A HelloWorld-színész testreszabása

A projektsablon néhány módszert `IHelloWorld` definiál a felületen, `HelloWorld` és megvalósítja azokat az aktor implementációjában.  Cserélje le ezeket a módszereket, hogy az aktor szolgáltatás egy egyszerű "Hello World" karakterláncot ad vissza.

A *HelloWorld.Interfaces* projektben a *IHelloWorld.cs* fájlban cserélje le a felület definícióját az alábbiak szerint:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

A **HelloWorld** projektben **HelloWorld.cs**a következő osztálydefiníciót váltsa fel:

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

Nyomja **le a Ctrl-Shift-B billentyűkombinációt** a projekt felépítéséhez és győződjön meg arról, hogy minden összeállít.

## <a name="add-a-client"></a>Ügyfél hozzáadása

Hozzon létre egy egyszerű konzolalkalmazást az aktorszolgáltatás hívásához.

1. Kattintson a jobb gombbal a Megoldáskezelő > az Új projekt **hozzáadása...** > **New Project...** megoldásra.

2. A **.NET Core** projekttípusok alatt válassza a **Console App (.NET Core) lehetőséget.**  Nevezze el a projekt *ActorClient*.
    
    ![Új projekt hozzáadása párbeszédpanel][6]    
    
    > [!NOTE]
    > A konzolalkalmazás nem az a típusú alkalmazás, amelyet általában ügyfélként használna a Service Fabricben, de kényelmes példát nyújt a helyi Service Fabric-fürt használatával történő hibakereséshez és teszteléshez.

3. A konzolalkalmazásnak 64 bites alkalmazásnak kell lennie a felületprojekttel és más függőségekkel való kompatibilitás fenntartása érdekében.  A Megoldáskezelőben kattintson a jobb gombbal az **ActorClient** projektre, majd kattintson a **Tulajdonságok parancsra.**  A **Build** lapon állítsa **a Platform célt** **x64-re.**
    
    ![Tulajdonságok létrehozása][8]

4. Az ügyfélprojekthez a megbízható szereplők NuGet csomagra van szükség.  Kattintson **az Eszközök** > **NuGet Csomagkezelő** > **konzol ra.**  A Csomagkezelő konzolon írja be a következő parancsot:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    A NuGet csomag és annak összes függősége telepítve van az ActorClient projektben.

5. Az ügyfélprojekthez hivatkozni kell az interfaces projektre is.  Az ActorClient projektben kattintson a jobb gombbal **a Függőségek** elemre, majd kattintson **a Hivatkozás hozzáadása...** parancsra.  Válassza a **Projektek > megoldás** (ha még nincs bejelölve), majd jelölje be a **HelloWorld.Interfaces jelölőnégyzetet.**  Kattintson az **OK** gombra.
    
    ![Hivatkozás hozzáadása párbeszédpanel][7]

6. Az ActorClient projektben cserélje le *Program.cs* teljes tartalmát a következő kódra:
    
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

## <a name="running-and-debugging"></a>Futás és hibakeresés

Nyomja le **az F5** billentyűt az alkalmazás helyi létrehozásához, üzembe helyezéséhez és futtatásához a Service Fabric fejlesztői fürtben.  A központi telepítési folyamat során a **kimeneti** ablakban láthatja a folyamatot.

![A Service Fabric hibakeresési kimeneti ablaka][3]

Amikor a kimenet tartalmazza a szöveget, *Az alkalmazás készen áll,* lehetséges, hogy tesztelje a szolgáltatást a ActorClient alkalmazás használatával.  A Megoldáskezelőben kattintson a jobb gombbal az **ActorClient** projektre, majd kattintson az Új példány **hibakeresése** > **parancsra.**  A parancssori alkalmazásnak meg kell jelenítenie az aktor szolgáltatás kimenetét.

![Alkalmazás kimenete][9]

> [!TIP]
> A Service Fabric Actors futásidejű bocsát ki néhány [esemény és teljesítmény számlálók kapcsolatos aktor metódusok.](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters) Ezek hasznosak a diagnosztika és a teljesítmény figyelése.

## <a name="next-steps"></a>További lépések
További információ [arról, hogy a Reliable Actors hogyan használja a Service Fabric platformot.](service-fabric-reliable-actors-platform.md)


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png