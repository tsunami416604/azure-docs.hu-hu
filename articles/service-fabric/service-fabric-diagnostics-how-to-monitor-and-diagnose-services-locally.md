---
title: "A Windows Azure mikroszolgáltatások hibakeresése |} Microsoft Docs"
description: "Megtudhatja, hogyan figyelése és diagnosztizálása a szolgáltatások egy helyi fejlesztési gépen a Microsoft Azure Service Fabric használatával készítettek."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2017
ms.author: dekapur
ms.openlocfilehash: 08998340afb2f242b9a268331607b0d1ddb9b0c6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Figyelése és diagnosztizálása szolgáltatásai a helyi számítógép fejlesztési beállítása
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Figyelés, észlelésére, diagnosztizálása és hibaelhárítási lehetővé teszi a felhasználói élmény minimális megszakadását folytatásához szolgáltatások. Amíg figyelése és a diagnosztika nem kritikus tényleges telepített éles környezetben, a hatékonyság függ hasonló modell bevezetése szolgáltatásokat, hogy biztosítsa a működnek, helyez át egy valós telepítés fejlesztése során. A Service Fabric megkönnyíti a szolgáltatás fejlesztők számára, amely zökkenőmentesen használható legyen a helyi fejlesztési egyszámítógépes beállításokat és a valós üzemi fürt beállítások diagnosztika megvalósításához.

## <a name="event-tracing-for-windows"></a>A Windows esemény-nyomkövetés
[Esemény nyomkövetése Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) egy nyomkövetési üzenet, a Service Fabric ajánlott technológia. Néhány ETW használatának előnyei a következők:

* **ETW gyors.** A nyomkövetés technológia, amely rendelkezik egy kód végrehajtásának lassúságát gyakorolt minimális hatás mellett azokat készítették.
* **ETW-nyomkövetés zökkenőmentesen helyi fejlesztési környezetekben, és valós fürt beállítások között.** Ez azt jelenti, hogy nem kell újraírnia a a nyomkövetés programot, amikor készen áll a kód telepítésére valós fürtre.
* **A Service Fabric rendszer kód ETW is használja a belső nyomkövetés.** Ez lehetővé teszi, hogy az alkalmazás nyomkövetési adatait, a Service Fabric rendszer nyomkövetések időosztásos megtekintése. Emellett segít, hogy a rendszer könnyebb megérteni a feladatütemezések és a kapcsolatokat, az alkalmazás kódjában és az események között.
* **A rendszer az ETW-események megtekintése a Service Fabric Visual Studio eszközök beépített támogatja.** Után a Service Fabric megfelelően konfigurálva a Visual Studio ETW-események jelennek meg a Visual Studio a diagnosztikai nézetben. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>A Visual Studio Service Fabric rendszer eseményeinek megtekintése
A Service Fabric bocsát ki, hogy mi történik, a platform alkalmazásfejlesztők segítségével ETW-események. Ha még nem tette meg, lépjen tovább, és kövesse a [az első alkalmazás létrehozásának a Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md). Ezek az információk segítségével egy alkalmazás működik, és az diagnosztikai eseménynapló a nyomkövetési üzenetek megjelenítése.

1. Ha a diagnosztika események ablak automatikusan jelenjen meg, lépjen a **nézet** a Visual Studio lapra, majd **más Windows** , majd **diagnosztikai eseménynaplóhoz**.
2. Mindegyik esemény rendelkezik szabványos metaadat-információi, amely jelzi, hogy a csomópont, alkalmazás és szolgáltatás, az esemény származik. Az események listájának használatával is végezhet a **események szűréséhez** be az események ablak tetején. Végezhet, például a **csomópontnév** vagy **szolgáltatás neve.** Amikor tartózkodik esemény részleteit, fel is függesztheti használatával, és a **szüneteltetése** gombra az események ablak tetején, majd később események adatvesztés nélkül folytathatja.
   
   ![A Visual Studio diagnosztikai eseménynapló](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Adja hozzá a saját egyéni nyomkövetési adatokat az alkalmazás kódja
A Service Fabric Visual Studio projektsablonjai példakódot tartalmaz. A kód bemutatja, hogyan egyéni alkalmazás kód ETW-nyomkövetési adatok jelennek meg a Visual Studio ETW megjelenítő rendszer nyomkövetések mellett a Service Fabric hozzáadásához. Ez a módszer előnye, hogy a metaadatok automatikusan hozzáadódik a nyomkövetési adatokat, és a Visual Studio diagnosztikai eseménynaplóhoz már be van állítva is a megjelenítésükhöz.

Projektek alapján létrehozott a **szolgáltatássablonok** (állapot nélküli és állapotalapú) csak keressen a `RunAsync` végrehajtására:

1. A hívás `ServiceEventSource.Current.ServiceMessage` a a `RunAsync` módszer az alkalmazás kódjában egyéni ETW-nyomkövetés példáját mutatja be.
2. Az a **ServiceEventSource.cs** fájl, a túlterhelés megtalálja a `ServiceEventSource.ServiceMessage` nagyon gyakori események, teljesítmény oknál használandó módszert.

Projektek alapján létrehozott a **szereplő sablonok** (állapot nélküli és állapotalapú):

1. Nyissa meg a **"Projektnév".cs** where fájl *projektnév* a neve, a Visual Studio-projekt számára is választott.  
2. A kód található `ActorEventSource.Current.ActorMessage(this, "Doing Work");` a a *DoWorkAsync* metódust.  Ez az egy egyéni ETW-nyomkövetés alkalmazáskód írásbeli példát.  
3. A fájl **ActorEventSource.cs**, megtalálja a túlterhelés a `ActorEventSource.ActorMessage` nagyon gyakori események, teljesítmény oknál használandó módszert.

Egyéni ETW-nyomkövetés adásakor a szolgáltatáskód hibáit, létre, telepíthetnek és ismételt használatával ellenőrizheti az esemény a diagnosztikai eseménynaplóhoz az alkalmazás futtatásához. Ha az alkalmazás hibakeresése **F5**, a diagnosztikai eseménynaplóhoz automatikusan megnyílik.

## <a name="next-steps"></a>Következő lépések
Az alkalmazás helyi diagnosztikai fentiekben hozzáadott ugyanazt nyomkövetés a kódot az eszközöket, amelyek segítségével megtekintheti ezeket az eseményeket az Azure-fürttel az alkalmazás futtatásakor fog működni. Tekintse meg a cikkek ismertetik az eszközök a különböző lehetőségek közül, és írja le, hogyan állíthat be őket.

* [Az Azure diagnosztikai naplók gyűjtéséről](service-fabric-diagnostics-how-to-setup-wad.md)
* [Esemény összevonásának és a gyűjtemény EventFlow használatával](service-fabric-diagnostics-event-aggregation-eventflow.md)

