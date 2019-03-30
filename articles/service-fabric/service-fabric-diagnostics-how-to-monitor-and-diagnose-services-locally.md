---
title: Hibakeresés az Azure Service Fabric-alkalmazásokat a Windows |} A Microsoft Docs
description: Ismerje meg, hogyan figyelheti és diagnosztizálhatja a Microsoft Azure Service Fabric használatával egy helyi fejlesztői gépen írt szolgáltatásokat.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 31c559c1ab314b7e1f29bd96f74d6d82cfcc0420
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670047"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>A helyi gép fejlesztési telepítőjének szolgáltatások monitorozása és diagnosztizálása
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Figyelés, észlelni, diagnosztizálása és hibaelhárítása lehetővé teszi a felhasználói élmény minimális megszakadását folytatásához szolgáltatások. Monitorozás és diagnosztika kritikus fontosságú egy tényleges üzembe helyezett éles környezetben, amelyek a hatékonyság függ egy hasonló modell bevezetése során győződjön meg arról, amikor helyez át egy való életből vett telepítő működnek a szolgáltatások fejlesztését. A Service Fabric megkönnyíti a szolgáltatás-fejlesztők számára a diagnosztikai is zökkenőmentesen együttműködő egygépes helyi fejlesztési beállításokat és a való életből vett éles fürt feladatainak megvalósítása.

## <a name="event-tracing-for-windows"></a>Windows esemény-nyomkövetés
[Esemény nyomkövetése Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) a javasolt technológiát a nyomkövetési üzeneteket a Service Fabricben. Néhány ETW használatának előnyei a következők:

* **ETW gyors.** Azt, hogy minimális hatással van a kód végrehajtási időpontok nyomkövetés technológiaként lett létrehozva.
* **ETW-nyomkövetési problémamentesen működik a helyi fejlesztési környezetben, és emellett valós fürt beállítását.** Ez azt jelenti, hogy nem kell a nyomkövetés újraírnia, amikor készen áll a kód egy valódi fürtön történő üzembe helyezéséhez.
* **A Service Fabric rendszer kód belső nyomkövetés ETW is használ.** Ez lehetővé teszi, hogy az alkalmazás hívásláncait közbeékeléses a Service Fabric rendszer nyomkövetések megtekintéséhez. Azt is segítségével könnyebben megismerheti a feladatütemezések és az alkalmazás kódja és események közötti összefüggéseket az alapul szolgáló rendszerben.
* **Nincs beépített támogatás a Service Fabric a Visual Studio tools ETW-események megtekintése.** Miután a Visual Studio megfelelően van konfigurálva a Service Fabric segítségével a diagnosztikai események megtekintése a Visual Studio ETW-események jelennek meg. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>A Service Fabric rendszer események megtekintése a Visual Studióban
A Service Fabric bocsát ki, mi történik a platform alkalmazásfejlesztők ETW-események. Ha ezt még nem tette meg, lépjen tovább, és kövesse a [az első alkalmazás létrehozása a Visual Studióban](service-fabric-tutorial-create-dotnet-app.md). Ezt az információt a megkezdheti az alkalmazás a diagnosztikai eseménynaplóhoz, a nyomkövetési üzenetek megjelenítése a segítséget.

1. Nyissa meg a diagnosztikai események ablakában nem jelenik meg automatikusan, ha a **nézet** lapra a Visual Studióban, majd **Other Windows** , majd **diagnosztikai eseménynaplóban**.
2. Minden esemény jelzi, hogy a csomópont, az alkalmazás és szolgáltatás, az esemény érkezik standard szintű metaadat-információkat tartalmaz. Az események használatával is végezhet a **események szűrése** az események ablak tetején. Szűrheti, például a **csomópontnév** vagy **szolgáltatás neve.** Esemény részletei dolgozik, ha fel is függesztheti használatával, és a **szüneteltetése** gombra az események ablak tetején, és később események adatveszteség nélküli folytatásához.
   
   ![Visual Studio Diagnostics Events Viewer](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>A saját egyéni nyomkövetési az alkalmazáskód hozzáadása
A Service Fabric a Visual Studio projektsablonokat tartalmaz mintakódot. A kód bemutatja, hogyan adhat hozzá egyéni kódot ETW-nyomkövetések, amely a Service Fabric a nyomkövetéseket a rendszer mellett a Visual Studio ETW megjelenítő megjelennek. Ez a módszer az előnye, hogy a metaadatok automatikusan hozzáadott nyomkövetéseket, és a Visual Studio diagnosztikai eseménynaplóban már konfigurálva van a megjelenítésükhöz.

Projektek alapján létrehozott a **szolgáltatássablonok** (állapot nélküli vagy állapot-nyilvántartó) csak keresse meg a `RunAsync` végrehajtása:

1. A hívás `ServiceEventSource.Current.ServiceMessage` a a `RunAsync` metódus azt mutatja be, az alkalmazás kódja egy egyéni ETW-nyomkövetési példát.
2. Az a **ServiceEventSource.cs** fájlt, láthatja a túlterhelés a `ServiceEventSource.ServiceMessage` metódushoz, amely miatt a teljesítmény javítása érdekében a nagy gyakoriságú eseményeket kell használni.

Projektek alapján létrehozott a **aktor sablonok** (állapot nélküli vagy állapot-nyilvántartó):

1. Nyissa meg a **"ProjectName".cs** where fájl *ProjectName* a Visual Studio-projektek számára is választott neve.  
2. Keresse meg a kódot `ActorEventSource.Current.ActorMessage(this, "Doing Work");` a a *DoWorkAsync* metódust.  Ez a példa egy egyéni ETW-nyomkövetési alkalmazáskód megírva.  
3. A fájl **ActorEventSource.cs**, megtalálja a túlterhelés a `ActorEventSource.ActorMessage` metódushoz, amely miatt a teljesítmény javítása érdekében a nagy gyakoriságú eseményeket kell használni.

A szolgáltatás programkód egyéni ETW-nyomkövetési felvett elkészítheti, telepítheti és futtassa újra az alkalmazást az esemény a diagnosztikai eseménynaplóban talál. Ha az alkalmazás hibakeresése **F5**, a diagnosztikai eseménynaplóban automatikusan megnyílik.

## <a name="next-steps"></a>További lépések
Helyi diagnosztikai fent az alkalmazáshoz hozzáadott ugyanazt nyomkövetés a kódot, amelyek segítségével megtekintheti ezeket az eseményeket az Azure-fürtön az alkalmazás futtatásakor fog működni. Tekintse meg ezeket a cikkeket, amely tárgyalják az eszközök különböző beállításait, és leírja, hogyan beállíthatja őket.

* [Azure Diagnostics-naplók összegyűjtése](service-fabric-diagnostics-how-to-setup-wad.md)
* [Események összesítése és gyűjtemény használatával eventflow segítségével](service-fabric-diagnostics-event-aggregation-eventflow.md)

