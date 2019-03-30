---
title: Alkalmazás hibakeresése a Visual Studióban |} A Microsoft Docs
description: A megbízhatóság és a szolgáltatások teljesítményét javíthatja a fejlesztés és a egy helyi fejlesztési fürtön a Visual Studio hibakereső őket.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 9fbd9b8e298713dad022196989027f9e43ce806d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667735"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>A Service Fabric-alkalmazás hibakeresése a Visual Studio használatával
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Egy helyi Service Fabric-alkalmazás hibakeresése
Időt és pénzt takaríthat üzembe helyezése és hibakeresése egy helyi számítógép fejlesztési fürtöt az Azure Service Fabric-alkalmazásokat. Visual Studio 2017 vagy Visual Studio 2015 is helyezze üzembe az alkalmazást a helyi fürthöz, és automatikusan csatlakoznak a hibakeresőt az alkalmazás összes példánya A Visual Studio csatlakozni a hibakeresőt rendszergazdaként kell futtatni.

1. A lépéseket követve indítsa el a helyi fejlesztési fürt [a Service Fabric fejlesztési környezet beállítása](service-fabric-get-started.md).
2. Nyomja meg **F5** , vagy kattintson **Debug** > **Start Debugging**.
   
    ![Az alkalmazás hibakeresésének indításához][startdebugging]
3. Állítson be töréspontokat a kódot, és lépegessen végig az alkalmazás a parancsok kattintva a **Debug** menü.
   
   > [!NOTE]
   > A Visual Studio csatolja az alkalmazás összes példányáról. Bár Ön ke krokování-kódon keresztül, töréspontokat a kiválasztott ütközhet első egyidejű munkamenetek eredményez több folyamat. Próbálja ki a töréspontok letiltása után azokat Ön találat, azáltal, hogy minden egyes töréspontot feltétele, hogy a hozzászóláslánc azonosítója vagy diagnosztikai események használatával.
   > 
   > 
4. A **diagnosztikai események** ablak profilnál automatikusan megnyílik, hogy meg tudja tekinteni diagnosztikai események valós időben.
   
    ![Valós idejű diagnosztikai események megtekintése][diagnosticevents]
5. Megnyithatja a **diagnosztikai események** Cloud Explorer ablak.  A **Service Fabric**, kattintson a jobb gombbal bármelyik csomópont, és válassza a **View Streaming nyomkövetések**.
   
    ![A diagnosztikai események ablak megnyitása][viewdiagnosticevents]
   
    Ha meg szeretné szűrni a nyomokra kattintva egy adott szolgáltatás vagy alkalmazás, egyszerűen engedélyezze az adott szolgáltatás vagy alkalmazás trasy streamování.
6. A diagnosztikai események láthatja az automatikusan létrehozott **ServiceEventSource.cs** fájlt, és lehet meghívni a alkalmazáskód.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. A **diagnosztikai események** ablak támogatja a szűrést, a felfüggesztetéssel és a valós idejű események vizsgálatával.  A szűrő egy egyszerű keresés az esemény-üzenet, beleértve annak tartalmát.
   
    ![Szűrés, szüneteltetése és folytatása vagy a valós idejű események vizsgálata][diagnosticeventsactions]
8. Szolgáltatások hibakeresésében olyan, mint bármely más alkalmazás hibakeresését. Töréspontokat a kiválasztott Visual Studión keresztül könnyen hibakeresési általában állítja. Annak ellenére, hogy a Reliable Collections több csomóponton replikálja, azok továbbra is valósítania az IEnumerable illesztőfelületet. Ez azt jelenti, hogy használhatja az eredmények megtekintése a Visual Studióban hibakeresésekor belül keresztült tárolt megtekintéséhez. Egyszerűen állítsa be egy töréspontot bárhol a kódban.
   
    ![Az alkalmazás hibakeresésének indításához][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Távoli Service Fabric-alkalmazás hibakeresése
Ha a Service Fabric-alkalmazások a Service Fabric-fürtön az Azure-ban futtatja, akkor is távoli hibakereséséhez ezeket, közvetlenül a Visual Studio.

> [!NOTE]
> A szolgáltatás használatához [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) és [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Távoli hibakeresés az adott fejlesztési és tesztelési célra, és nem használható éles környezetben, a futó alkalmazások gyakorolt hatás miatt.
> 
> 

1. Keresse meg a fürt **Cloud Explorer**, kattintson a jobb gombbal, és válassza a **hibakeresés engedélyezése**
   
    ![Távoli hibakeresés engedélyezése][enableremotedebugging]
   
    Ez a folyamat, ha engedélyezi a távoli hibakeresési futtatására szolgáló bővítmény a fürtcsomópontokat, valamint a szükséges hálózati konfigurációk elindít.
2. Kattintson a jobb gombbal a fürt csomópontja **Cloud Explorer**, és válassza a **Hibakereső csatlakoztatása**
   
    ![Hibakereső csatlakoztatása][attachdebugger]
3. Az a **folyamat csatolása** párbeszédpanelen válassza ki a folyamatot, hibakeresés, és kattintson a kívánt **csatolása**
   
    ![Folyamat kiválasztása][chooseprocess]
   
    Szeretné csatlakoztatni, a folyamat neve megegyezik a szolgáltatási projekt szerelvény neve.
   
    A hibakereső, az összes csomóponton, a folyamat futtatása fogja csatolni.
   
   * Abban az esetben, ahol egy állapotmentes szolgáltatás hibakeresés a a szolgáltatás minden csomópontján az összes példányát a hibakeresési munkamenet részét képezik.
   * Az állapotalapú szolgáltatások hibakeresés, ha minden partíció csak az elsődleges másodpéldány lesz aktív, és ezért kivétel történt a hibakeresőt szerint. Ha az elsődleges replikán a hibakeresési munkamenet során, az adott replika feldolgozása továbbra is része lesz a hibakeresési munkamenet.
   * Annak érdekében, hogy csak a tényleges megfelelő partíciók vagy egy adott szolgáltatás példányainak, feltételes töréspontok használhatja, ha egy adott partíció vagy a példány csak érvényteleníteni.
     
     ![Feltételes töréspontot][conditionalbreakpoint]
     
     > [!NOTE]
     > Szolgáltatás végrehajtható névvel több példánya a Service Fabric-fürt hibakeresés jelenleg nem támogatjuk.
     > 
     > 
4. Ha befejezte az alkalmazás hibakeresése, letilthatja a távoli hibakeresési bővítmény a kattintson a jobb gombbal a fürt **Cloud Explorer** válassza **-hibakeresés letiltása**
   
    ![Tiltsa le a távoli hibakeresés][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Egy távoli fürtön csomópontról trasy streamování
Ön is képes adatfolyam-nyomkövetéseket, közvetlenül a Visual Studio távoli fürtcsomópont. Ez a funkció lehetővé teszi a stream ETW-nyomkövetési események, a Service Fabric-fürtcsomópont előállítása.

> [!NOTE]
> A szolgáltatás használatához [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) és [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/).
> Ez a funkció csak az Azure-ban futó fürtök támogatja.
> 
> 

<!-- -->
> [!WARNING]
> Adatfolyam-nyomkövetések hivatott fejlesztési és tesztelési célra, és nem használható éles környezetben, a futó alkalmazások gyakorolt hatás miatt.
> Éles forgatókönyvekben a szoftverlicenceknek való használata az Azure Diagnostics eseménye továbbítását.
> 
> 

1. Keresse meg a fürt **Cloud Explorer**, kattintson a jobb gombbal, és válassza a **adatfolyam-nyomkövetés engedélyezése**
   
    ![Engedélyezze a távoli trasy streamování][enablestreamingtraces]
   
    Ez a folyamat, ha engedélyezi a streamelési nyomkövetések futtatására szolgáló bővítmény a fürtcsomópontokat, valamint a szükséges hálózati konfigurációk elindít.
2. Bontsa ki a **csomópontok** elemében **Cloud Explorer**, kattintson a jobb gombbal a kívánt adatfolyam hívásláncait, és válassza a csomópontot **Streamelési nyomok megtekintése**
   
    ![Távoli streamelési nyomok megtekintése][viewremotestreamingtraces]
   
    Ismételje meg a 2. a nyomkövetések megtekintéséhez csomópont. Egyes csomópontok stream egy dedikált ablakban jelennek meg.
   
    Most már érvényesülnek a Service Fabric és a szolgáltatások által kibocsátott nyomainak megtekintéséhez. Ha azt szeretné, hogy csak az adott alkalmazást jeleníti meg az események szűréséhez állítsa, egyszerűen írja be az alkalmazás a szűrő nevét.
   
    ![Streamelési nyomok megtekintése][viewingstreamingtraces]
3. Trasy streamování befejezte a fürtről, letilthatja a távoli trasy streamování, kattintson a jobb gombbal a fürt **Cloud Explorer** válassza **adatfolyam-nyomkövetések letiltása**
   
    ![Tiltsa le a távoli trasy streamování][disablestreamingtraces]

## <a name="next-steps"></a>További lépések
* [Service Fabric-szolgáltatás tesztelése](service-fabric-testability-overview.md).
* [A Visual Studióban a Service Fabric-alkalmazások kezelése](service-fabric-manage-application-in-visual-studio.md).

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
