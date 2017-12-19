---
title: "A Visual Studio alkalmazás hibakeresése |} Microsoft Docs"
description: "Tovább fejlesztheti megbízhatóságának és teljesítményének a szolgáltatások fejlesztéséhez és a helyi fejlesztési fürtök hibakeresés a Visual Studio őket."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: vturecek;mikhegn
ms.openlocfilehash: 8c29b54415a42ec435f2b4c3ce6b407b2155f70c
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>A Service Fabric-alkalmazás hibakeresése a Visual Studio használatával
> [!div class="op_single_selector"]
> * [A Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Egy helyi Service Fabric-alkalmazás hibakeresése
Időt és pénzt takaríthat telepítésével és az Azure Service Fabric-alkalmazás egy számítógép helyi fejlesztési fürtöt a hibakeresést. A Visual Studio 2017 vagy Visual Studio 2015 Telepítse központilag az alkalmazást a helyi fürthöz, és automatikusan csatlakoznak a hibakereső az alkalmazás összes példányát.

1. Indítsa el a helyi fejlesztési fürtök lépéseit követve [a Service Fabric fejlesztési környezet létrehozása](service-fabric-get-started.md).
2. Nyomja le az **F5** , vagy kattintson a **Debug** > **Start Debugging**.
   
    ![Egy alkalmazást a hibakeresés elindításához][startdebugging]
3. Állítson be töréspontokat a kódot és az alkalmazás lépésenként parancsok kattintva a **Debug** menü.
   
   > [!NOTE]
   > A Visual Studio csatolja az alkalmazás összes példányát. Kód most lépjen, amíg töréspontok előfordulhat, hogy beolvasása kattintson az egyidejű munkamenetek eredményezve több folyamat. Próbálja a töréspontok letiltása után azok még találat, azáltal, hogy minden töréspont feltétele a Szálazonosító vagy diagnosztikai eseményeket használ.
   > 
   > 
4. A **a diagnosztikai** automatikusan megnyílik, megtekintheti a diagnosztikai valós időben.
   
    ![Valós idejű diagnosztikai eseményeinek megtekintése][diagnosticevents]
5. Is megnyithatja a **a diagnosztikai** Cloud Explorer ablakban.  A **Service Fabric**, kattintson a jobb gombbal bármelyik csomópont, és válassza a **nézet adatfolyam-nyomkövetések**.
   
    ![A diagnosztikai események ablak megnyitása][viewdiagnosticevents]
   
    Ha szeretne szűrni a nyomkövetések és egy adott szolgáltatás vagy alkalmazás, egyszerűen engedélyezése, hogy adott szolgáltatás vagy alkalmazás adatfolyam címein.
6. A diagnosztikai események láthatók a automatikusan létrehozott **ServiceEventSource.cs** fájlt, és az alkalmazás kódjában nevezzük.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. A **a diagnosztikai** ablak támogatja a szűrést, a felfüggesztés és a valós idejű események ellenőrzése.  A szűrő egy egyszerű karakterlánc-keresés az eseményüzenet, beleértve annak tartalmát.
   
    ![Szűréséhez, szüneteltetése és folytatása és vizsgálja meg a valós idejű események][diagnosticeventsactions]
8. Hibakereső szolgáltatásának olyan, mint bármely más alkalmazásban. Visual Studio alkalmazással töréspontok általában könnyen hibakeresési állítja be. Annak ellenére, hogy a megbízható gyűjtemények replikálásához több csomópont, azok továbbra is valósítania az IEnumerable illesztőfelületet. Ez azt jelenti, hogy az eredmények megtekintése a Visual Studio során használhatók hibakeresés megjelenítéséhez belül már tárolja. Egyszerűen állítson be egy töréspontot bárhol a kódban.
   
    ![Egy alkalmazást a hibakeresés elindításához][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>A távoli Service Fabric-alkalmazás hibakeresése
Ha a Service Fabric-alkalmazások az Azure Service Fabric-fürt futtatja, akkor is távolról hibakeresése a Visual Studio-ről.

> [!NOTE]
> A funkció használatához [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) és [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Fejlesztési/Tesztelési forgatókönyvek, és nem használható üzemi környezetben, mert a futó alkalmazások gyakorolt hatás célja a távoli hibakeresés.
> 
> 

1. Keresse meg a fürt **Cloud Explorer**, kattintson a jobb gombbal, és válassza a **hibakeresés engedélyezése**
   
    ![Távoli hibakeresésének engedélyezése][enableremotedebugging]
   
    Ez fogja indítsa a távoli hibakeresési bővítménnyel a fürtcsomópontokat, valamint a szükséges hálózati konfigurációk folyamata.
2. Kattintson a jobb gombbal a fürt csomópontja **Cloud Explorer**, és válassza a **hibakereső csatolása**
   
    ![Hibakereső csatlakoztatása][attachdebugger]
3. Az a **folyamat csatolása** párbeszédpanelen válassza ki a hibakeresési, és kattintson a kívánt folyamat **csatolása**
   
    ![Folyamat kiválasztása][chooseprocess]
   
    Szeretne csatlakozni, a folyamat neve megegyezik a szolgáltatási projekt szerelvény neve.
   
    A hibakereső kapcsolódni fog, a folyamat futó összes csomópontján.
   
   * Abban az esetben, ha egy állapotmentes szolgáltatások hibakeresést az összes csomóponton a szolgáltatás minden példányának a hibakeresési munkamenetben részét képezik.
   * Állapotalapú szolgáltatási hibakeresést, ha minden olyan partíció csak az elsődleges másodpéldány lesz aktív, és ezért kifogott által a hibakereső. Ha az elsődleges másodpéldány helyezi a hibakeresési munkamenetben, a replika feldolgozása továbbra is része lesz a hibakeresési munkamenetben.
   * Ahhoz, hogy csak a megfelelő partíciók vagy egy megadott szolgáltatás példányának tényleges, a feltételes töréspontok segítségével csak törés az egy adott partícióra vagy a példány.
     
     ![Feltételes töréspont][conditionalbreakpoint]
     
     > [!NOTE]
     > Jelenleg nem támogatjuk a szolgáltatás végrehajtható néven több példánya a Service Fabric-fürt hibakeresést.
     > 
     > 
4. Ha az alkalmazás hibakeresése végzett, kattintson a jobb gombbal a fürt a távoli hibakeresési bővítmény letiltása **Cloud Explorer** válassza **tiltsa le a hibakeresést.**
   
    ![Tiltsa le a távoli hibakeresés][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Adatfolyam-nyomkövetések távoli fürtcsomópontból
Akkor is alkalmasak adatfolyam nyomkövetések közvetlenül a Visual Studio egy távoli fürt csomópontja. Ez a funkció lehetővé teszi az adatfolyam ETW nyomkövetési eseményeit, a Service Fabric fürtcsomóponton előállított.

> [!NOTE]
> A szolgáltatás használatához [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) és [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/).
> Ez a funkció csak az Azure-ban futó fürtök támogatja.
> 
> 

<!-- -->
> [!WARNING]
> Adatfolyam-nyomkövetési szolgáltatásának fejlesztési/tesztelési és nem használható üzemi környezetben, mert a futó alkalmazások gyakorolt hatás célja.
> A termelési forgatókönyvek hagyatkozzon kizárólag az Azure Diagnostics használatával eseménye továbbítását.
> 
> 

1. Keresse meg a fürt **Cloud Explorer**, kattintson a jobb gombbal, és válassza a **adatfolyam-nyomkövetés engedélyezése**
   
    ![Távoli adatfolyam nyomkövetés engedélyezése][enablestreamingtraces]
   
    Ez a folyamat, ha engedélyezi a nyomkövetési adatokat adatfolyam a fürtcsomópontokat, valamint a szükséges hálózati konfigurációk kiterjesztés fog indítsa.
2. Bontsa ki a **csomópontok** elemében **Cloud Explorer**, kattintson a jobb gombbal a csomópont adatfolyam-nyomkövetéseit, és válassza a kívánt **nézet adatfolyam-nyomkövetések**
   
    ![Távoli adatfolyam-nyomkövetések megtekintése][viewremotestreamingtraces]
   
    Ismételje meg a 2. meg szeretné tekinteni a nyomkövetések csomópont. Az egyes csomópontok adatfolyamokkal dedikált ablakban jelennek meg.
   
    A Service Fabric és a szolgáltatások által kibocsátott nyomkövetések látni áll. Ha azt szeretné, csak az adott alkalmazást megjelenítendő események szűrésére, egyszerűen csak írja be a szűrő alkalmazása nevében.
   
    ![Adatfolyam-nyomkövetések megtekintése][viewingstreamingtraces]
3. Miután a folyamatos átviteli nyomkövetési adatokat a fürtről, bármikor letilthatja távoli adatfolyam nyomkövetési adatok, kattintson a jobb gombbal a fürtre **Cloud Explorer** válassza **adatfolyam-nyomkövetések letiltása**
   
    ![Tiltsa le a távoli adatfolyam nyomkövetések][disablestreamingtraces]

## <a name="next-steps"></a>Következő lépések
* [A Service Fabric szolgáltatás tesztelése](service-fabric-testability-overview.md).
* [A Service Fabric-alkalmazások, a Visual Studio kezelése](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
