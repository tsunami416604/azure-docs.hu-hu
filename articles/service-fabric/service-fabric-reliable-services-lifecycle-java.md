---
title: "Az Azure Service Fabric Reliable Services-életciklus áttekintése |} Microsoft Docs"
description: "További tudnivalók a Service Fabric megbízható szolgáltatások különböző életciklus események"
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 80eb68346dd05c256c60725eb082aa0651fe7cbd
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Megbízható életciklusának áttekintése
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

Végezni a megbízható szolgáltatásokat nyújt, a legfontosabb az életciklus alapjait. Általában:

* Indítás közben
  * Szolgáltatások össze
  * Rendelkeznek, és térjen vissza a nulla vagy több figyelői lehetőséget
  * A visszaadott figyelői nyitva vannak, a szolgáltatással való kommunikáció engedélyezése
  * A szolgáltatás runAsync metódusában nevezik, így a szolgáltatás hosszú ideig futó vagy háttérben munka
* Leállítás közben
  * A megszakítási token runAsync átadott megszakadt, és a figyelők be van zárva.
  * Vagyis egyszer teljes, a szolgáltatás objektum van destructed

Ezek az események pontos rendelési körül részletek érhetők el. Azt jelzi, hogy attól függően, hogy a megbízható szolgáltatás Stateless vagy állapotalapú alkalmazások és szolgáltatások kis mértékben változhat-különösen az események sorrendje. Emellett az állapotalapú szolgáltatások esetén tudunk és az elsődleges swap forgatókönyv kezelésére. Ez a folyamat során az elsődleges szerepkör átkerül egy másik replika (vagy ismét elérhető lesz) szolgáltatás leállítása nélkül. Végül azt kell gondolniuk hiba vagy probléma feltételt.

## <a name="stateless-service-startup"></a>Állapot nélküli a szolgáltatás indítása
Az állapotmentes szolgáltatások életciklusát meglehetősen egyszerű. Az események sorrendje a következő:

1. A szolgáltatás összeállított
2. Ezt követően a párhuzamos két dolgot fordulhat elő:
    - `StatelessService.createServiceInstanceListeners()`meghívták és bármely visszaadott figyelői megnyitása (`CommunicationListener.openAsync()` meghívta az egyes figyelő)
    - A szolgáltatás runAsync metódusában (`StatelessService.runAsync()`) neve
3. Ha jelen van, a szolgáltatás saját onOpenAsync módszer neve (pontosabban `StatelessService.onOpenAsync()` nevezik. Ez egy ritka felülbírálást de érhető el).

Fontos megjegyezni, hogy nincs nincs létrehozásához, és nyissa meg a figyelők és runAsync hívások közötti rendezés. Előfordulhat, hogy nyissa meg a figyelők, runAsync megkezdése előtt. Ehhez hasonlóan runAsync is szükségessé tehet meghívása előtt a kommunikációs figyelőket is nyitva, vagy még össze. Bármely szinkronizálásra szükség, ha azt balról gyakorlat szerint a végrehajtója. Közös megoldások:

* Egyes esetekben a figyelői nem működőképes, amíg az egyéb információk jön létre, vagy végzett munkát. Az állapotmentes szolgáltatások munkahelyi általában végezheti el a szolgáltatást a konstruktorban, során a `createServiceInstanceListeners()` hívja, vagy maga a figyelő a konstrukció részeként.
* Egyes esetekben runAsync kódja nem szeretné elindítani, amíg a figyelők is nyitva. Ebben az esetben a további koordinációs szükség. Egy gyakori megoldás, néhány jelző belül a figyelők, amely azt jelzi, ha végzi, amely beadása runAsync a tényleges munkát folytatása előtt.

## <a name="stateless-service-shutdown"></a>Állapot nélküli szolgáltatás leállítása
Ha egy állapotmentes szolgáltatások leállítása, ugyanilyen mintájú után következik, csak a névkeresési:

1. Párhuzamos
    - Bármely nyitott figyelők be legyen zárva (`CommunicationListener.closeAsync()` meghívta az egyes figyelő)
    - A megszakítási token átadott `runAsync()` megszakadt (ellenőrzésére a visszavonásra token `isCancelled` tulajdonság igaz értéket ad vissza, és a token hívása `throwIfCancellationRequested` metódus jelez egy `CancellationException`)
2. Egyszer `closeAsync()` minden egyes figyelő működése befejeződött és `runAsync()` is befejeződött, a szolgáltatás `StatelessService.onCloseAsync()` metódus lehívásra kerül, ha van ilyen (újra ez egy ritka felülbírálás).
3. Miután `StatelessService.onCloseAsync()` befejeződött, a szolgáltatás objektum destructed van

## <a name="notes-on-service-lifecycle"></a>Tudnivalók a szolgáltatási életciklus
* Mindkét a `runAsync()` metódus és a `createServiceInstanceListeners` hívások nem kötelező. Szolgáltatásként lehet őket, egyaránt, vagy egyiket sem. Például, ha a szolgáltatás felhasználói hívások válaszként a tevékenységeket, nincs szükség ahhoz, hogy a megvalósítása `runAsync()`. Csak a kommunikációs figyelőket és a kapcsolódó kódra szükség. Hasonlóképpen létrehozása és a kommunikációs figyelőket adatszolgáltató nem kötelező, a szolgáltatás esetleg csak háttérműveletek elvégzéséhez, és így kell megvalósítani`runAsync()`
* A szolgáltatás befejezi érvényes `runAsync()` sikeresen és visszatérési belőle. Ez nem tekinthető hiba feltétel és a szolgáltatás befejezését a háttérműveletek jelenti. Az állapot-nyilvántartó megbízható szolgáltatások `runAsync()` volna lehet újra hívni Ha a szolgáltatás elsődleges lefokozásra és majd előléptetni elsődleges.
* Ha egy szolgáltatás kilép a `runAsync()` egy váratlan kivétel kiváltása, ez a hiba és a szolgáltatás objektum le van állítva, és a rendszerállapot hibát jelzett.
* Bár a visszatérésre ezek a módszerek nem korlátozott, azonnal elveszti a írjanak, és ezért nem tudja végrehajtani a valódi munkát. Térjen vissza a megszakítási kérelem fogadása után a lehető leggyorsabban ajánlott. Ha a szolgáltatás nem válaszol az adott API-hívások elfogadható időn belül a Service Fabric kényszerített előfordulhat, hogy a szolgáltatás leáll. Általában ez csak akkor fordul elő alkalmazásfrissítések vagy egy szolgáltatás törlésekor során. Ez az időkorlát értéke alapértelmezés szerint 15 percenként.
* Hibák a `onCloseAsync()` elérési eredményez `onAbort()` Ez a szolgáltatás törlése egy utolsó-alkalommal legjobb lehetőség meghívott kialakításához, és felszabadíthatja a minden olyan erőforrásnál, amely azt állítják.

> [!NOTE]
> Állapot-nyilvántartó megbízható szolgáltatások nem támogatottak a java még.
>
>

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Reliable Services használatába](service-fabric-reliable-services-introduction.md)
* [Megbízható szolgáltatások – első lépések](service-fabric-reliable-services-quick-start.md)
* [Megbízható szolgáltatás használati speciális](service-fabric-reliable-services-advanced-usage.md)
