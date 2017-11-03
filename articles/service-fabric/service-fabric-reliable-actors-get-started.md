---
title: "Az első szereplő-alapú Azure mikroszolgáltatási létrehozása a C# |} Microsoft Docs"
description: "Ez az oktatóanyag végigvezeti a létrehozása, a Hibakeresés és a Service Fabric Reliable Actors használatával egyszerű szereplő-alapú szolgáltatás telepítése."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 3f447e049ccd33c77f422e8aa703ad6646f9ffa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-reliable-actors"></a>Bevezetés a Reliable Actors
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-actors-get-started.md)
> * [Java Linuxon](service-fabric-reliable-actors-get-started-java.md)
> 
> 

Ez a cikk az Azure Service Fabric Reliable Actors használatának alapjait ismerteti, és végigvezeti a létrehozása, hibakeresés, és egy egyszerű megbízható szereplő alkalmazást a Visual Studio telepítése.

## <a name="installation-and-setup"></a>Telepítés és beállítás
Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik-e a Service Fabric fejlesztőkörnyezet, állítsa be a számítógépre.
Ha telepíteni kell, lásd: részletes utasításokat a [a fejlesztési környezet beállítása](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Alapfogalmak
A Reliable Actors használatának elsajátításához csak néhány alapfogalommal kell tisztában lennie:

* **Aktorszolgáltatás**. A Reliable Actors aktorai Reliable Services-szolgáltatásokba vannak csomagolva, amelyek a Service Fabric-infrastruktúrában helyezhetők üzembe. Az aktorpéldányok elnevezett szolgáltatáspéldányokban aktiválhatók.
* **Aktorregisztráció**. Akárcsak a Reliable Services esetében, a Reliable Actor-szolgáltatásokat is regisztrálni kell a Service Fabric-futtatókörnyezetben. Emellett az aktor típusát regisztrálni kell az Actor-futtatókörnyezetben.
* **Aktorillesztő**. Az aktor illesztője határozza meg az aktor erős típusmegadású nyilvános illesztőjét. A Reliable Actor modellterminológiában az aktor illesztője határozza meg azt, hogy az aktor milyen típusú üzeneteket képes értelmezni és feldolgozni. Az aktor illesztőjét használják a további aktorok és ügyfélalkalmazások, hogy üzeneteket „küldjenek” (aszinkron módon) az aktor felé. A Reliable Actors több illesztőt is képes implementálni.
* **ActorProxy-osztály**. Az ActorProxy-osztály használatával hívják meg az ügyfélalkalmazások az aktor illesztőjén keresztül közzétett metódusokat. Az ActorProxy-osztály két fontos funkciót lát el:
  
  * Névfeloldás: Képes megállapítani az aktor helyét a fürtben (megtalálja a fürt azon csomópontját, ahol az aktor található).
  * Hibakezelés: Képes újra megpróbálkozni a metódusok meghívásával, majd újra feloldani az aktor helyét például olyan esetben, ha egy hiba miatt az aktort át kellett helyezni a fürt egy másik csomópontjára.

Az aktorok illesztőivel kapcsolatban a következő szabályokat érdemes megemlíteni:

* Az aktorok illesztőmetódusait nem lehet túlterhelni.
* Az aktorok illesztőmetódusainak nem lehetnek „out”, „ref” vagy opcionális paraméterei.
* Az általános illesztők nem támogatottak.

## <a name="create-a-new-project-in-visual-studio"></a>A Visual Studio új projekt létrehozása
Indítsa el a Visual Studio 2015-öt vagy a Visual Studio 2017 rendszergazdaként, és új Service Fabric-alkalmazás projekt létrehozása:

![Service Fabric tools for Visual Studio – új projekt][1]

A következő párbeszédpanelen kiválaszthatja a létrehozni kívánt projekt típusától.

![A Service Fabric projektsablonjai][5]

A HelloWorld projekt tegyük a Service Fabric Reliable Actors szolgáltatást használják.

A megoldás létrehozása után megjelenik az alábbi szerkezettel:

![A Service Fabric-projekt struktúra][2]

## <a name="reliable-actors-basic-building-blocks"></a>A Reliable Actors alapszintű építőelemei
Egy tipikus Reliable Actors megoldás három projektet áll:

* **Az alkalmazási projektet (MyActorApplication)**. Ez az a projekt, amely az összes szolgáltatást együtt a központi telepítési csomagokat. Tartalmazza a *ApplicationManifest.xml* és az alkalmazás kezelésére szolgáló PowerShell-parancsfájlokat.
* **A felület projekt (MyActor.Interfaces)**. Ez az a projekt, amely tartalmazza a felületdefiníció szereplő. A MyActor.Interfaces projektben a szereplője a megoldás által használt csatolók adhat meg. Az aktor-adapterek bármilyen tetszőleges nevet, a projekt definiálható, azonban a felület meghatározása a szereplő szerződés és az ügyfelek a szereplő hívja, ezért általában érdemes adjon meg egy külön szerelvényben szereplő végrehajtásához által közösen használt az aktor végrehajtása és több más projektek megoszthatók.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **Az aktor projekt (MyActor)**. Ez az a projekt a Service Fabric-szolgáltatás, amelyet szeretne üzemeltetni a szereplő meghatározásához használják. Az aktor végrehajtásának tartalmaz. Az aktor megvalósítása egy osztályt, amely alaptípusból származik `Actor` és a valósít meg a MyActor.Interfaces projektben definiált. Az aktor osztály is meg kell valósítania elfogadó konstruktorral egy `ActorService` példány és egy `ActorId` , és továbbadja őket a következő `Actor` osztály. Ez lehetővé teszi a platform függőségek konstruktor függőségi beszúrást.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

Az aktorszolgáltatást regisztrálni kell egy bizonyos szolgáltatástípusként a Service Fabric-futtatókörnyezetben. Ahhoz, hogy az aktorszolgáltatás futtatni tudja az aktorpéldányokat, az aktor típusát is regisztrálni kell az aktorszolgáltatásba. Az aktorok esetében ezt a feladatot az `ActorRuntime` regisztrációs metódus végzi el.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Ha a Visual Studio új projekt indítja, és csak szereplő definícióval rendelkezik, a regisztráció alapértelmezés szerint a kódot, amely a Visual Studio létrehozza a tartalmazza. A szolgáltatás más szereplője ad meg, ha meg kell hozzáadnia a szereplő regisztrációs használatával:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [!TIP]
> A Service Fabric szereplője futásidejű megfelelően kibocsát egy [szereplő módszerek kapcsolódó események és teljesítményszámlálók](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). A diagnosztika és teljesítményfigyelés hasznosak.
> 
> 

## <a name="debugging"></a>Hibakeresés
A Service Fabric-eszközök Visual Studio támogatja a helyi gépen hibakeresést. A hibakeresési munkamenetben elindíthatja szerezze meg az F5 billentyűt. A Visual Studio létrehozza (ha szükséges) csomagokat. Telepíti az alkalmazást a helyi Service Fabric-fürt és a hibakereső.

A telepítési folyamat során megjelenik a folyamatjelző a **kimeneti** ablak.

![A Service Fabric hibakeresési kimeneti ablak][3]

## <a name="next-steps"></a>Következő lépések
További információ [Reliable Actors használatát a Service Fabric-platformról](service-fabric-reliable-actors-platform.md).

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
