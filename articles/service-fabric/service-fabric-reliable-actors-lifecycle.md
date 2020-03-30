---
title: Az Azure Service Fabric szereplőiéletciklus áttekintése
description: Ismerteti a service fabric megbízható szereplő életciklusát, a szemétgyűjtést, valamint a szereplők és állapotuk manuális törlését
author: amanbha
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: b05da78091260297d94062c06cba100d01ce7e2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258316"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Az aktor életciklusa, az automatikus szemétgyűjtés és a manuális törlés
Az aktor akkor aktiválódik, amikor először hív meg bármelyik metódusát. Az aktor inaktív (az Actors futásidejű által gyűjtött szemét), ha nem használja egy konfigurálható ideig. Az aktor és állapota bármikor manuálisan is törölhető.

## <a name="actor-activation"></a>Aktor aktiválása
Ha egy szereplő aktiválva van, a következő történik:

* Amikor egy aktor hívása érkezik, és az egyik még nem aktív, új aktor jön létre.
* Az aktor állapota be van töltve, ha az állapot fenntartása.
* A `OnActivateAsync` (C#) `onActivateAsync` vagy (Java) metódus (amely felülbírálható az aktor implementációjában) neve.
* A színész most aktívnak tekinthető.

## <a name="actor-deactivation"></a>Aktor inaktiválása
Az aktor inaktiválásakor a következők következnek be:

* Ha egy szereplő tegy ideig nem használatos, a rendszer eltávolítja az Aktív szereplők táblából.
* A `OnDeactivateAsync` (C#) `onDeactivateAsync` vagy (Java) metódus (amely felülbírálható az aktor implementációjában) neve. Ez törli az összes időzítő a színész. Aktor műveletek, például az állapotváltozások nem hívható meg ebből a módszerből.

> [!TIP]
> A Fabric Actors futásidejű bocsát ki néhány [kapcsolódó események aktor aktiválása és kikapcsolása](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Ezek hasznosak a diagnosztika és a teljesítmény figyelése.
>
>

### <a name="actor-garbage-collection"></a>Színész szemétgyűjtés
Ha egy aktor inaktivált, az aktor objektumra mutató hivatkozások szabadulnak fel, és a közös nyelvi futásidejű (CLR) vagy a java virtuális gép (JVM) szemétgyűjtő által általában gyűjtött szemét lehet. A szemétgyűjtés csak az aktorobjektumot tisztítja meg; **nem** távolítja el az aktor állapotkezelőjében tárolt állapotot. Az aktor következő aktiválásakor új aktorobjektum jön létre, és az állapota helyreáll.

Mi számít "használatnak" a deaktiválás és a szemétgyűjtés céljából?

* Hívás fogadása
* `IRemindable.ReceiveReminderAsync`meghívott metódus (csak akkor alkalmazható, ha az aktor emlékeztetőket használ)

> [!NOTE]
> ha az aktor időzítőket használ, és az időzítő visszahívása meghívásra kerül, **az nem** számít "használatban lévőnek".
>
>

Mielőtt belemennénk a deaktiválás részleteibe, fontos meghatározni a következő kifejezéseket:

* *Bekésidő*. Ez az az időszak, amikor az Actors futásidejű ellenőrzi az Aktív szereplők tábla szereplők, amelyek inaktiválhatók és a szemétgyűjtés. Ennek alapértelmezett értéke 1 perc.
* *Tétlen időhosszabbítás*. Ez az az idő, amasinálás előtt az aktornak nem használtnak (tétlennek) kell maradnia, mielőtt inaktiválható és a szemétgyűjtésre kerül. Az alapértelmezett érték 60 perc.

Általában nem kell módosítani ezeket az alapértelmezett értékeket. Szükség esetén azonban ezek az időközök `ActorServiceSettings` módosíthatók az [aktorszolgáltatás regisztrálásakor:](service-fabric-reliable-actors-platform.md)

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Az aktor futásidejű minden egyes aktív szereplőnél nyomon követi, hogy mennyi ideig volt tétlen (azaz nem használt). Az aktor futásidejű `ScanIntervalInSeconds` ellenőrzi az egyes szereplők minden, hogy ha lehet szemetet `IdleTimeoutInSeconds`gyűjtött, és jelzi, ha már tétlen a.

Ha egy aktor thasználja, az alapjárati idő nullázódik 0.Any as an aactor is used, its indle time is reset to 0. Ezt követően, a színész lehet szemetet gyűjtött csak `IdleTimeoutInSeconds`akkor, ha ismét tétlen marad a . Emlékezzünk vissza, hogy egy aktor akkor tekinthető használtnak, ha egy aktorfelület-metódus vagy egy aktor emlékeztető visszahívása végrehajtásra kerül. Az aktor **nem** tekinthető használtnak, ha az időzítő visszahívása végrehajtásra kerül.

Az alábbi ábrán egyetlen szereplő életciklusa látható, amely bemutatja ezeket a fogalmakat.

![Példa az idlési időre][1]

A példa az aktor metódushívások, emlékeztetők és időzítők hatását mutatja be az aktor élettartamára. A példával kapcsolatos következő pontokat érdemes megemlíteni:

* A ScanInterval és az IdleTimeout beállítás sorrendben 5, illetve 10. (Az egységek itt nem számítanak, mivel célunk csak a koncepció szemléltetése.)
* A szemétgyűjtésre szánt szereplők vizsgálata t=0,5,10,15,20,25-nél történik, az 5-ös belépési intervallum meghatározása szerint.
* Egy periodikus időzítő a T=4,8,12,16,20,24-nél aktiválódik, és a visszahívás végrehajtása. Ez nem befolyásolja a színész tétlen idejét.
* A T=7 aktor metódushívása visszaállítja az alapjárati időt 0-ra, és késlelteti az aktor szemétgyűjteményét.
* Egy aktor emlékeztető visszahívása végrehajtja a T=14 és további késlelteti a szemétgyűjtés az aktor.
* A T=25-nél végzett szemétgyűjtési vizsgálat során a színész tétlen ideje végül meghaladja a 10-es tétlen időtúllépését, és a színész szemetet gyűjt.

Az aktor soha nem lesz szemetet gyűjteni, miközben az egyik módszer végrehajtása, nem számít, hogy mennyi időt töltött a módszer végrehajtása. Mint korábban említettük, az aktor felület metódusok és emlékeztető visszahívások végrehajtása megakadályozza a szemétgyűjtés visszaállításával az aktor tétlen ideje 0.As mentioned as arlier, the execution of aactor interface methods and reminder callbacks prevents garbage collection by resetting the actor's in diay time to 0. Az időzítő visszahívások végrehajtása nem állítja vissza az alapjárati időt 0-ra. Azonban a szemétgyűjtés az aktor van hatolva, amíg az időzítő visszahívási befejeződött a végrehajtás.

## <a name="manually-deleting-actors-and-their-state"></a>Szereplők és állapotuk manuális törlése
Az inaktivált szereplők szemétgyűjtése csak az aktor objektumot tisztítja meg, de nem távolítja el az aktor állapotkezelőjében tárolt adatokat. Ha egy szereplő újra aktiválódik, az adatok ismét elérhetővé válnak számára az állapotkezelőn keresztül. Azokban az esetekben, ahol a szereplők adatokat tárolnak a State Manager ben, és inaktiválva vannak, de soha nem aktiválódnak újra, szükség lehet az adataik törlésére.  A szereplők törlésére vonatkozó példákért olvassa el a [delete actors és állapotuk olvasását.](service-fabric-reliable-actors-delete-actors.md)

## <a name="next-steps"></a>További lépések
* [Aktor időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Színész események](service-fabric-reliable-actors-events.md)
* [Színész reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Aktor diagnosztikája és teljesítményfigyelése](service-fabric-reliable-actors-diagnostics.md)
* [Aktor API-referenciadokumentáció](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
