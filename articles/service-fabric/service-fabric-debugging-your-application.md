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
ms.openlocfilehash: 682059914b5d86f5e670e373a4acf3e4ac6246ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428208"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>A Service Fabric-alkalmazás hibakeresése a Visual Studio használatával
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Egy helyi Service Fabric-alkalmazás hibakeresése
Időt és pénzt takaríthat üzembe helyezése és hibakeresése egy helyi számítógép fejlesztési fürtöt az Azure Service Fabric-alkalmazásokat. A Visual Studio 2019 vagy 2015 helyezze üzembe az alkalmazást a helyi fürthöz, és automatikusan csatlakoznak a hibakeresőt az alkalmazás összes példányáról. A Visual Studio csatlakozni a hibakeresőt rendszergazdaként kell futtatni.

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
   
    Ha meg szeretné szűrni a nyomokra kattintva egy adott szolgáltatás vagy alkalmazás, engedélyezze az adott szolgáltatás vagy alkalmazás trasy streamování.
6. A diagnosztikai események láthatja az automatikusan létrehozott **ServiceEventSource.cs** fájlt, és lehet meghívni a alkalmazáskód.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. A **diagnosztikai események** ablak támogatja a szűrést, a felfüggesztetéssel és a valós idejű események vizsgálatával.  A szűrő egy egyszerű keresés az esemény-üzenet, beleértve annak tartalmát.
   
    ![Szűrés, szüneteltetése és folytatása vagy a valós idejű események vizsgálata][diagnosticeventsactions]
8. Szolgáltatások hibakeresésében olyan, mint bármely más alkalmazás hibakeresését. Könnyű Hibakeresés Visual studióval töréspontok általában értékre állítjuk. Annak ellenére, hogy a Reliable Collections több csomóponton replikálja, azok továbbra is valósítania az IEnumerable illesztőfelületet. Ezt a megvalósítást azt jelenti, hogy használhatja az eredmények megtekintése a Visual Studióban megtekintheti a belül keresztült tárolt hibakeresése közben. Ehhez állítson be egy töréspontot bárhol a kódban.
   
    ![Az alkalmazás hibakeresésének indításához][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Távoli Service Fabric-alkalmazás hibakeresése
Ha a Service Fabric-alkalmazások a Service Fabric-fürtön az Azure-ban futtatja, a ezeket az alkalmazásokat, közvetlenül a Visual Studióban távolról is hibakeresési.

> [!NOTE]
> A szolgáltatás használatához [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) és [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Távoli hibakeresés az adott fejlesztési és tesztelési célra, és nem használható éles környezetben, a futó alkalmazások gyakorolt hatás miatt.
> 
> 

1. Keresse meg a fürt **Cloud Explorer**. Kattintson a jobb gombbal, és válassza a **hibakeresés engedélyezése**
   
    ![Távoli hibakeresés engedélyezése][enableremotedebugging]
   
    Ez a művelet elindít folyamata a távoli hibakeresési futtatására szolgáló bővítmény a fürtcsomópontok és a szükséges hálózati konfigurációk.
2. Kattintson a jobb gombbal a fürt csomópontja **Cloud Explorer**, és válassza a **Hibakereső csatlakoztatása**
   
    ![Hibakereső csatlakoztatása][attachdebugger]
3. Az a **folyamat csatolása** párbeszédpanelen válassza ki a folyamatot, hibakeresés, és kattintson a kívánt **csatolása**
   
    ![Folyamat kiválasztása][chooseprocess]
   
    Szeretné csatlakoztatni, a folyamat neve megegyezik a szolgáltatási projekt szerelvény neve.
   
    A hibakereső, az összes csomóponton, a folyamat futtatása fogja csatolni.
   
   * Abban az esetben, ahol egy állapotmentes szolgáltatás hibakeresése az összes csomóponton a szolgáltatás minden példányát a a hibakeresési munkamenet részét képezik.
   * Ha egy állapotalapú szolgáltatás hibakeresése, minden partíció csak az elsődleges replika nem aktív, és ezért kivétel történt a hibakeresőt szerint. Ha az elsődleges replikán a hibakeresési munkamenet során, az adott replika feldolgozása továbbra is része lesz a hibakeresési munkamenet.
   * Csak a tényleges megfelelő partíciók vagy egy adott szolgáltatás példányainak, feltételes töréspontok használhatja egy adott partíció vagy a példány csak érvényteleníteni.
     
     ![Feltételes töréspontot][conditionalbreakpoint]
     
     > [!NOTE]
     > Szolgáltatás végrehajtható névvel több példánya a Service Fabric-fürt hibakeresés jelenleg nem támogatjuk.
     > 
     > 
4. Ha befejezte az alkalmazás hibakeresése, letilthatja a távoli hibakeresési bővítmény a kattintson a jobb gombbal a fürt **Cloud Explorer** válassza **-hibakeresés letiltása**
   
    ![Tiltsa le a távoli hibakeresés][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Egy távoli fürtön csomópontról trasy streamování
Ön is képes adatfolyam-nyomkövetéseket, közvetlenül a Visual Studio távoli fürtcsomópontra. Ez a funkció lehetővé teszi a stream ETW-nyomkövetési események, a Service Fabric-fürtcsomópont előállítása.

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

1. Keresse meg a fürt **Cloud Explorer**. Kattintson a jobb gombbal, és válassza a **adatfolyam-nyomkövetés engedélyezése**
   
    ![Engedélyezze a távoli trasy streamování][enablestreamingtraces]
   
    Ez a művelet elindít a streamelési nyomkövetések futtatására szolgáló bővítmény a fürtcsomópontokat, valamint a szükséges hálózati konfigurációk folyamata.
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
