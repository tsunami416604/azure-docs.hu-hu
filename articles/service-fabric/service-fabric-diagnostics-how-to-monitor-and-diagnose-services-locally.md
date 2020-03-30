---
title: Az Azure Service Fabric-alkalmazások hibakeresése a Windows ban
description: Megtudhatja, hogyan figyelheti és diagnosztizálhatja a Microsoft Azure Service Fabric használatával egy helyi fejlesztőgépen írt szolgáltatásokat.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8435bb82afddd0070679768bb8d22ad9290f2279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258511"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Szolgáltatások monitorozása és diagnosztizálása egy helyi gép fejlesztési beállításánál
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

A figyelés, észlelés, diagnosztika és hibaelhárítás lehetővé teszi, hogy a szolgáltatások a felhasználói élmény minimális megszakításával folytatódjanak. Bár a figyelés és a diagnosztika kritikus fontosságú egy tényleges üzembe helyezett éles környezetben, a hatékonyság függ egy hasonló modell a szolgáltatások fejlesztése során, hogy megbizonyosodjon arról, hogy működnek, ha egy valós beállításra lép. A Service Fabric megkönnyíti a szolgáltatásfejlesztők számára a diagnosztika megvalósítását, amely zökkenőmentesen működik mind az egygépes helyi fejlesztési beállítások, mind a valós éles fürtbeállítások között.

## <a name="event-tracing-for-windows"></a>Eseménykövetés windowsos környezetben
[Esemény nyomkövetése a Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) az ajánlott technológia az üzenetek nyomon követése a Service Fabric. Az ETW használatának néhány előnye:

* **Az ETW gyors.** Olyan nyomkövetési technológiaként épült, amely minimális hatással van a kódfuttatási időkre.
* **Az ETW nyomkövetés zökkenőmentesen működik a helyi fejlesztési környezetekben és a valós fürtbeállításokban is.** Ez azt jelenti, hogy nem kell átírnia a nyomkövetési kódot, ha készen áll a kód valódi fürtre való üzembe helyezésére.
* **A Service Fabric rendszerkódja etw-t is használ a belső nyomkövetéshez.** Ez lehetővé teszi, hogy tekintse meg az alkalmazás nyomait a Service Fabric rendszer nyomaival. Ez is segít, hogy könnyebben megértsék a szekvenciák és az alkalmazáskód és az alapul szolgáló rendszer eseményei közötti kapcsolatok.
* **A Service Fabric Visual Studio eszközei beépített támogatást nyújtanak az ETW-események megtekintéséhez.** Az ETW-események akkor jelennek meg a Visual Studio diagnosztikai események nézetében, ha a Visual Studio megfelelően van konfigurálva a Service Fabric szolgáltatással. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>A Service Fabric rendszereseményeinek megtekintése a Visual Studióban
A Service Fabric ETW-eseményeket bocsát ki, hogy segítsen az alkalmazásfejlesztőknek megérteni, mi történik a platformon. Ha még nem tette meg, kövesse az [első alkalmazás létrehozása a Visual Studióban](service-fabric-tutorial-create-dotnet-app.md)című részben leírt lépéseket. Ez az információ segít abban, hogy egy alkalmazás elinduljön és működjön a diagnosztikai események megjelenítője a nyomkövetési üzenetek et megjelenítő.

1. Ha a diagnosztikai események ablaka nem jelenik meg automatikusan, nyissa meg a Visual Studio **Nézet** lapját, válassza az **Egyéb Windows** lehetőséget, majd a **Diagnosztikai események megjelenítője**lehetőséget.
2. Minden esemény szabványos metaadat-információkkal rendelkezik, amelyek jelzik az esemény csomópontját, alkalmazását és szolgáltatását. Az események listáját az események ablakának tetején található **Események szűrése** mezővel is szűrheti. Szűrhet például a **Csomópont név** vagy **a Szolgáltatásnév elemre.** És amikor az esemény részleteit nézi, az események ablakának tetején található **Szünet** gombbal is szüneteltetheti, és később az események elvesztése nélkül folytathatja.
   
   ![Visual Studio diagnosztikai események megjelenítője](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Saját egyéni nyomkövetések hozzáadása az alkalmazáskódhoz
A Service Fabric Visual Studio projektsablonjai mintakódot tartalmaznak. A kód bemutatja, hogyan adhat hozzá egyéni alkalmazáskódot ETW-nyomkövetések, amelyek megjelennek a Visual Studio ETW-megjelenítő mellett a rendszer nyomkövetései a Service Fabric. Ennek a módszernek az az előnye, hogy a metaadatok automatikusan hozzáadódnak a nyomkövetésekhez, és a Visual Studio diagnosztikai eseménymegjelenítője már be van állítva azok megjelenítésére.

A **szolgáltatássablonokból** (állapot nélküli vagy állapotalapú) létrehozott `RunAsync` projektek esetében csak keresse meg a megvalósítást:

1. A `ServiceEventSource.Current.ServiceMessage` metódushívás `RunAsync` egy egyéni ETW-nyomkövetést mutat be az alkalmazáskódból.
2. A **ServiceEventSource.cs** fájlban egy túlterhelést `ServiceEventSource.ServiceMessage` talál a módszerhez, amelyet teljesítménybeli okok miatt nagy frekvenciájú eseményekhez kell használni.

Az **aktorsablonokból** létrehozott projektek (állapot nélküli vagy állapotalapú):

1. Nyissa meg a **"ProjectName".cs** fájlt, amelyben a *ProjectName* a Visual Studio-projekthez választott név.  
2. Keresse meg `ActorEventSource.Current.ActorMessage(this, "Doing Work");` a kódot a *DoWorkAsync* metódusban.  Ez egy példa egy egyéni ETW-nyomkövetésre, amelyet az alkalmazáskódból írtak.  
3. A **ActorEventSource.cs**fájlban a teljesítménymiatt `ActorEventSource.ActorMessage` nagy frekvenciájú eseményekhez használandó módszer túlterhelése található.

Miután egyéni ETW-nyomkövetést ad hozzá a szolgáltatáskódhoz, létrehozhatja, üzembe helyezheti és futtathatja az alkalmazást, hogy láthassa az esemény(eke)t a diagnosztikai események megjelenítőjében. Ha az alkalmazást az **F5**alkalmazással hibakeresésnek veti be, a Diagnosztikai események megjelenítője automatikusan megnyílik.

## <a name="next-steps"></a>További lépések
Ugyanazt a nyomkövetési kódot, amelyet a helyi diagnosztikához adott hozzá az alkalmazáshoz, olyan eszközökkel fog működni, amelyek segítségével megtekintheti ezeket az eseményeket, amikor az alkalmazást egy Azure-fürtön futtatja. Tekintse meg ezeket a cikkeket, amelyek ismertetik az eszközök különböző lehetőségeit, és leírják, hogyan állíthatja be őket.

* [Naplók gyűjtése az Azure Diagnostics segítségével](service-fabric-diagnostics-how-to-setup-wad.md)
* [Eseményösszesítés és -gyűjtés az EventFlow használatával](service-fabric-diagnostics-event-aggregation-eventflow.md)

