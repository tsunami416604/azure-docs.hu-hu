---
title: Az Azure Service Fabric actors-életciklus áttekintése |} A Microsoft Docs
description: Ismerteti a Service Fabric Reliable Actor életciklusát, szemétgyűjtés és manuálisan az aktorok és állapotuk törlése
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 10f78b3b78e90fbb4f1d50cf581bfbce263f44aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872721"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Actors-életciklus szemétgyűjtés automatikus és manuális törlése
Egy aktor hívást kezdeményez annak a metódusaival valamelyik első alkalommal aktiválódik. Egy szereplő inaktív (szemétgyűjtési az Actors modul által összegyűjtött) esetén nem használható egy konfigurálható ideig. Egy aktor és annak állapotát is törölhetők manuálisan tetszőleges időpontban.

## <a name="actor-activation"></a>Aktor aktiválás
Ha egy szereplő aktiválva van, az alábbiak történnek:

* Ha a hívás érkezik egy szereplő számára, és a egy még nem aktív, egy új aktor jön létre.
* Az aktorok állapotának be van töltve, ha az állapot karbantartása.
* A `OnActivateAsync` (C#) vagy `onActivateAsync` (Java) metódust (amely az aktor implementálása felülbírálhatók).
* Az aktor most számít aktív.

## <a name="actor-deactivation"></a>Aktor inaktiválása
Amikor egy szereplő az Inaktiválás az alábbiak történnek:

* Ha egy szereplő valamennyi ideje nem történik, a rendszer eltávolítja az aktív Actors tábla.
* A `OnDeactivateAsync` (C#) vagy `onDeactivateAsync` (Java) metódust (amely az aktor implementálása felülbírálhatók). Ez törli az aktor az időzítők. Aktor műveletek, például állapot módosításokat a metódus nem hívható.

> [!TIP]
> A Fabric Actors-futtatókörnyezet bocsát ki néhány [aktor aktiválás és az inaktiválást kapcsolatos eseményeket](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Ezek a diagnosztikai és az alkalmazásteljesítmény-figyelési hasznosak.
>
>

### <a name="actor-garbage-collection"></a>Aktor szemétgyűjtés
Egy szereplő inaktiválódik, ha jelennek meg az aktor objektumra hivatkozik, és általában a közös nyelvi futtatókörnyezet (CLR) vagy a java virtuális gép (JVM) típusú szemétgyűjtő által gyűjtött szemétgyűjtési lehet. A szemétgyűjtés csak megtisztítja az aktor objektumot. ugyanúgy **nem** távolítsa el az aktor állapot Managerben tárolt állapotára. Az aktor aktiválódik, amikor legközelebb jön létre egy új szereplő objektum, és az állapot visszaállítása megtörtént.

Mi számít "használatban" inaktiválási és szemétgyűjtés céljából?

* Hívás fogadása
* `IRemindable.ReceiveReminderAsync` (csak akkor, ha az aktor használ emlékeztetők alkalmazható) meghívott metódus

> [!NOTE]
> az aktor időzítők használja, és időzítő visszahívási metódusa meghívásainak, ha az nem **nem** "használatban" számít.
>
>

Mielőtt belemennénk az inaktiválást, fontos, az alábbi feltételek meghatározása:

* *Beolvasás időköze*. Ez az az időköz, amelyen az Actors modul az aktív Actors táblában keres, amely lehet inaktiválni actors, és a szemétgyűjtési gyűjtött. Ennek alapértelmezett értéke 1 perc.
* *Üresjárat időkorlátja*. Ez az az időtartam, amelyet egy szereplő fel nem használt (inaktív), mielőtt ki lehet kapcsolni, és a szemétgyűjtési gyűjteni. Ennek alapértelmezett értéke 60 perc.

Általában nem kell módosítani ezeket az alapértelmezett értékeket. Azonban, ha szükséges, ezeket az időszakokat is módosítható keresztül `ActorServiceSettings` regisztrálásakor a [Aktorszolgáltatás](service-fabric-reliable-actors-platform.md):

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
Minden aktív színész esetében az actor-futtatókörnyezetben nyomon követi az az, hogy mennyi ideig üresjáratban volt (azaz a nem használt). Az actor-futtatókörnyezetben ellenőrzi az actors mindegyike minden `ScanIntervalInSeconds` megtekintéséhez a szemétgyűjtési által gyűjtött, és gyűjti, ha az inaktív lehet `IdleTimeoutInSeconds`.

Visszaállít egy szereplő használja, az üresjárati idő 0 alaphelyzetbe áll. Ezt követően az aktor lehet csak akkor, ha újra inaktív marad a szemétgyűjtő `IdleTimeoutInSeconds`. Ne felejtse el, hogy egy szereplő minősül, ha egy felület aktormetódus vagy egy aktor emlékeztető visszahívási használtak. Van egy szereplő **nem** számít, ha az időzítő visszahívási használni.

Az alábbi ábrán egy egyetlen aktor ezek a fogalmak szemléltetésére életciklusa látható.

![Üresjárati idő – példa][1]

A példában szereplő metódust hívja, emlékeztetők és időzítőket hatását az aktor élettartama jeleníti meg. Tudnivalók a példában a következő szempontokat is érdemes megemlíteni:

* ScanInterval és az IdleTimeout beállítása 5 és 10 jelölik. (Egységek nem számít, hogy itt, mivel az a célja, hogy csak a koncepció bemutatják.)
* Keresése, az actors szemétgyűjtésbe történik, ha T = 0, 5, 10, 15, 20, 25, 5. az ellenőrzési időköz szerint.
* Időzítő rendszeres T = 4, 8, 12, 16, 20, 24, akkor aktiválódik, és a visszahívás végrehajtása. Nem érinti a szereplő arra a tétlenségi időre.
* Egy aktor metódushívás T = 7 állítja vissza arra a tétlenségi időre 0-ra, és késlelteti az aktor szemétgyűjtési gyűjteménye.
* Egy aktor emlékeztető visszahívási T = 14 délelőtt, és további késlelteti az aktor szemétgyűjtési gyűjteménye.
* T = 25-kor a szemétgyűjtési gyűjtemény vizsgálat során az aktor üresjárati idő végül meghaladja a 10-es üresjárati időkorlátot, és az aktor szemétgyűjtő.

Egy aktor soha nem lesznek szemétgyűjtő azt a módszerekkel, függetlenül attól, hogy mennyi időt vesz igénybe, hogy a metódus végrehajtása a végrehajtása közben. Ahogy korábban említettük, az aktorok illesztőmetódusait és emlékeztető visszahívások végrehajtásának megakadályozza, hogy a szemétgyűjtés 0 üresjárati idő az aktor visszaállításával. Az időzítő visszahívások végrehajtását nem állítja alaphelyzetbe a arra a tétlenségi időre 0. A szemétgyűjtés, az aktor azonban késleltetve van mindaddig, amíg az időzítő visszahívás végrehajtása befejeződött.

## <a name="manually-deleting-actors-and-their-state"></a>Manuálisan az aktorok és állapotuk törlése
Inaktív szereplők szemétgyűjtés csak megtisztítja a szereplő objektum, de nem távolítja el az aktor State Manager tárolt adatokat. Ha egy szereplő újra aktivált, annak munkaelemeiben található adatok újra áll rendelkezésre, a State Manager keresztül. Olyan esetekben, ahol actors tárolja az adatokat az State Manager és az inaktiválása, de soha nem aktiválta újra azt lehet szükség az adatok törlése.  Törli az actors bemutató példákért olvassa el a [aktorok és állapotuk törlése](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>További lépések
* [Actors – időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Actors-események](service-fabric-reliable-actors-events.md)
* [Actors – újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
* [Actors diagnosztizálása és teljesítményfigyelése](service-fabric-reliable-actors-diagnostics.md)
* [Aktor API dokumentációja](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C#-minta kódja](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
