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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
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
A kezdéshez a Reliable Actors meg kell ismernie néhány főbb fogalmait és kifejezéseit:

* **Aktor szolgáltatás**. Reliable Actors vannak csomagolva, Reliable Services, a Service Fabric-infrastruktúra üzembe helyezhető. Szereplő példányok a megnevezett példánya. a rendszer aktiválja.
* **Aktor regisztrációs**. Mint a Reliable Services, a megbízható szereplő szolgáltatásnak kell regisztrálni kell a Service Fabric-futtatókörnyezet. Emellett a szereplő típus regisztrálva kell lennie a szereplő futtatási idő mellett.
* **Aktor felület**. Az aktor felület egy szigorú típusmegadású nyilvános felületének egy szereplő azonosítására szolgál. Az aktor illesztőfelületet a megbízható szereplő modell terminológia a szereplő tudja értelmezni üzenetek és a folyamat határozza meg. Az aktor felületet használják más szereplője és ügyfélalkalmazások "" (aszinkron) üzeneteket küldhet a szereplő. Reliable Actors több adapter is létrehozható.
* **ActorProxy osztály**. A ActorProxy osztály ügyfélalkalmazások használják a szereplő felületen keresztül közzétett módszerek meghívására. A ActorProxy osztály biztosít két fontos funkciók:
  
  * Nevek feloldása: tud-e a szereplő (Keresés a fürt, hol tárolja a csomópont) a fürtben található.
  * Kezelési hiba: metódus meghívásához újra és újra oldja fel a szereplő helyet, például a szereplő helyezhetők át a fürt egy másik csomópontra való igénylő meghibásodás után.

A következő szabályokat, amelyek a projektjükhöz szereplő csatolókat érdemes megemlíteni a következők:

* Aktor a felület metódusai nem túlterhelt.
* Aktor felület metódusok nem lehetnek kimenő, ref vagy választható paraméterek:.
* Általános illesztőfelületeinek nem támogatottak.

## <a name="create-a-new-project-in-visual-studio"></a>A Visual Studio új projekt létrehozása
Indítsa el a Visual Studio 2015-öt vagy a Visual Studio 2017 rendszergazdaként, és új Service Fabric-alkalmazás projekt létrehozása:

![Service Fabric tools for Visual Studio – új projekt][1]

A következő párbeszédpanelen kiválaszthatja a létrehozni kívánt projekt típusától.

![A Service Fabric projektsablonjai][5]

A HelloWorld projekt tegyük a Service Fabric Reliable Actors szolgáltatást használják.

A megoldás létrehozása után megjelenik az alábbi szerkezettel:

![A Service Fabric-projekt struktúra][2]

## <a name="reliable-actors-basic-building-blocks"></a>Megbízható szereplője alapvető építőelemeket, amelyekből
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

Az aktor szolgáltatás regisztrálni kell a Service Fabric futásidejű szolgáltatás típussal. Ahhoz, hogy a szereplő szolgáltatás futtatásához a szereplő példányok az aktor típusát is regisztrálni kell az Aktor szolgáltatással. A `ActorRuntime` regisztrációs módszer a szereplőket végrehajtja ezt a műveletet.

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
