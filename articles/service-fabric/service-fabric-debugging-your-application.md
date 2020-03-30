---
title: Az alkalmazás hibakeresése a Visual Studióban
description: Javíthatja a szolgáltatások megbízhatóságát és teljesítményét azáltal, hogy helyi fejlesztési fürtön fejleszti és javítja azokat a Visual Studióban.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fff8a19d5643f7ce866c9eb9c57486340b6f8a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624140"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Service Fabric-alkalmazás hibakeresése a Visual Studióval
> [!div class="op_single_selector"]
> * [Visual Stúdió/Csharp](service-fabric-debugging-your-application.md) 
> * [Napfogyatkozás/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Helyi Service Fabric-alkalmazás hibakeresése
Időt és pénzt takaríthat meg az Azure Service Fabric-alkalmazás helyi számítógép-fejlesztési fürtben történő üzembe helyezésével és hibakeresésével. A Visual Studio 2019-es vagy 2015-ös webhely telepítheti az alkalmazást a helyi fürtre, és automatikusan csatlakoztathatja a hibakeresőt az alkalmazás összes példányához. A Visual Studio-t rendszergazdaként kell futtatni a hibakereső csatlakoztatásához.

1. Helyi fejlesztési fürt indítása a [Service Fabric-fejlesztői környezet beállítása](service-fabric-get-started.md)című lépés lépéseit követve.
2. Nyomja **le az F5 billentyűt,** vagy kattintson a **Hibakeresés indítása** > **gombra.**
   
    ![Alkalmazás hibakeresésének megkezdése][startdebugging]
3. Állítsa be a töréspontokat a kódban, és lépjen végig az alkalmazáson a **Debug** menü parancsai parancsra kattintva.
   
   > [!NOTE]
   > A Visual Studio az alkalmazás összes példányához csatolja a csatolást. A kód léptetése közben a töréspontokat több folyamat is eltalálhatja, ami egyidejű munkameneteket eredményez. Próbálja meg letiltani a töréspontokat a találat után, úgy, hogy minden töréspontot a szálazonosítótól vagy diagnosztikai események használatától függővé tesz.
   > 
   > 
4. A **Diagnosztikai események** ablak automatikusan megnyílik, így valós időben tekintheti meg a diagnosztikai eseményeket.
   
    ![Diagnosztikai események megtekintése valós időben][diagnosticevents]
5. A **Diagnosztikai események** ablakot a Cloud Explorerben is megnyithatja.  A **Service Fabric csoportban**kattintson a jobb gombbal bármelyik csomópontra, és válassza **a Streamelési nyomkövetések megtekintése parancsot.**
   
    ![A diagnosztikai események ablakának megnyitása][viewdiagnosticevents]
   
    Ha egy adott szolgáltatásra vagy alkalmazásra szeretné szűrni a nyomkövetéseket, engedélyezze az adott szolgáltatás vagy alkalmazás streamelési nyomkövetéseit.
6. A diagnosztikai események láthatók az automatikusan generált **ServiceEventSource.cs** fájlban, és az alkalmazáskódból hívják őket.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. A **Diagnosztikai események** ablak támogatja az események valós idejű szűrését, szüneteltetését és vizsgálatát.  A szűrő az eseményüzenet egyszerű karakterlánc-keresése, beleértve annak tartalmát is.
   
    ![Események szűrése, szüneteltetése és folytatása, illetve események valós idejű vizsgálata][diagnosticeventsactions]
8. A hibakeresési szolgáltatások olyan, mint bármely más alkalmazás hibakeresése. A hibakeresés érdekében általában a Visual Studióban állíthatja be a töréspontokat. Annak ellenére, hogy a megbízható gyűjtemények replikálódik több csomópont között, továbbra is megvalósítják IEnumerable. Ez a megvalósítás azt jelenti, hogy a Visual Studio Eredménynézetét hibakeresés közben használhatja a benne tárolt elemek megtekintéséhez. Ehhez állítson be egy töréspontot bárhol a kódban.
   
    ![Alkalmazás hibakeresésének megkezdése][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Parancsfájl futtatása hibakeresés részeként
Bizonyos esetekben előfordulhat, hogy egy parancsfájlt kell futtatnia egy hibakeresési munkamenet indításakor (például ha nem használja az Alapértelmezett szolgáltatásokat).

A Visual Studio-ban hozzáadhat egy **Start-Service.ps1** nevű fájlt a Service Fabric-alkalmazásprojekt (.sfproj) **Parancsfájlok** mappájába. Ez a parancsfájl meghívása az alkalmazás helyi fürtben való létrehozása után történik.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Távoli Service Fabric-alkalmazás hibakeresése
Ha a Service Fabric-alkalmazások futnak egy Service Fabric-fürtön az Azure-ban, távolról hibakeresés, ezeket az alkalmazásokat, közvetlenül a Visual Studióból.

> [!NOTE]
> A szolgáltatáshoz [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) és [Azure SDK szükséges a .NET 2.9-hez.](https://azure.microsoft.com/downloads/)    

<!-- -->
> [!WARNING]
> A távoli hibakeresés fejlesztési és tesztelési forgatókönyvekhez készült, és nem használható éles környezetekben, a futó alkalmazásokra gyakorolt hatás miatt.

1. Keresse meg a fürtöt a **Cloud Explorer ben.** Kattintson a jobb gombbal, és válassza **a Hibakeresés engedélyezése parancsot**
   
    ![Távoli hibakeresés engedélyezése][enableremotedebugging]
   
    Ez a művelet elindítja a távoli hibakeresési bővítmény engedélyezésének folyamatát a fürtcsomópontokon és a szükséges hálózati konfigurációkon.
2. Kattintson a jobb gombbal a fürtcsomópontra a **Cloud Explorerben,** és válassza **a Hibakereső csatolása parancsot**
   
    ![Hibakereső csatolása][attachdebugger]
3. A Csatolás a **folyamathoz** párbeszédpanelen válassza ki a hibakeresést, majd kattintson a **Csatolás gombra.**
   
    ![Folyamat kiválasztása][chooseprocess]
   
    Annak a folyamatnak a neve, amelyhez csatolni szeretné a szervizprojekt-összeállítás nevét.
   
    A hibakereső a folyamatot futtató összes csomóponthoz csatolva lesz.
   
   * Abban az esetben, ha egy állapotmentes szolgáltatást hibakeresést tesz le, a szolgáltatás minden csomóponton lévő összes példánya a hibakeresési munkamenet része.
   * Ha állapotalapú szolgáltatást vesz fel, csak bármely partíció elsődleges replikája lesz aktív, és ezért a hibakereső elkapja. Ha az elsődleges replika a hibakeresési munkamenet során mozog, a replika feldolgozása továbbra is a hibakeresési munkamenet része marad.
   * Csak egy adott szolgáltatás megfelelő partícióit vagy példányait, feltételes töréspontok használatával csak megtörni egy adott partíciót vagy példányt.
     
     ![Feltételes töréspont][conditionalbreakpoint]
     
     > [!NOTE]
     > Jelenleg nem támogatjuk a Service Fabric-fürt több példánya azonos szolgáltatás végrehajtható nevű hibakeresését.
     > 
     > 
4. Miután befejezte az alkalmazás hibakeresését, letilthatja a távoli hibakeresési bővítményt, ha a jobb gombbal a fürtre kattint a **Cloud Explorer** ben, és a **Hibakeresés letiltása parancsot választja.**
   
    ![Távoli hibakeresés letiltása][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Nyomkövetések streamelése távoli fürtcsomópontról
A nyomkövetéseket közvetlenül egy távoli fürtcsomópontról a Visual Studio-ba is továbbíthatja. Ez a funkció lehetővé teszi a Service Fabric fürtcsomóponton előállított ETW-nyomkövetési események streamelését.

> [!NOTE]
> Ehhez a szolgáltatáshoz [a Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) és [az Azure SDK szükséges a .NET 2.9-hez.](https://azure.microsoft.com/downloads/)
> Ez a funkció csak az Azure-ban futó fürtöket támogatja.
> 
> 

<!-- -->
> [!WARNING]
> A streamelési nyomkövetések fejlesztési és tesztelési forgatókönyvekhez való használatra szolgálnak, és nem használhatók éles környezetekben, a futó alkalmazásokra gyakorolt hatás miatt.
> Éles környezetben az Azure Diagnostics használatával események továbbítása kell támaszkodnia.

1. Keresse meg a fürtöt a **Cloud Explorer ben.** Kattintson a jobb gombbal, és válassza **a Streamelési nyomkövetések engedélyezése parancsot.**
   
    ![Távoli streamelési nyomkövetések engedélyezése][enablestreamingtraces]
   
    Ez a művelet elindítja a folyamatot, amely engedélyezi a streamelési nyomkövetési bővítményt a fürtcsomópontokon, valamint a szükséges hálózati konfigurációkat.
2. **Bontsa** ki a Csomópontok elemet a **Cloud Explorer**ben, kattintson a jobb gombbal arra a csomópontra, amelyből nyomkövetéseket szeretne streamelni, és válassza a **Streamelési nyomkövetések megtekintése parancsot.**
   
    ![Távoli streamelési nyomkövetések megtekintése][viewremotestreamingtraces]
   
    Ismételje meg a 2. Minden csomópont adatfolyam a dedikált ablakban jelenik meg.
   
    Most már láthatja a Service Fabric és a szolgáltatások által kibocsátott nyomkövetéseket. Ha azt szeretné, hogy az események et úgy szűrje, hogy csak egy adott alkalmazást jelenítsen meg, egyszerűen írja be az alkalmazás nevét a szűrőbe.
   
    ![Streamelési nyomkövetések megtekintése][viewingstreamingtraces]
3. Miután végzett a fürtből származó nyomkövetések streamelésével, letilthatja a távoli streamelési nyomkövetéseket, ha a jobb gombbal a fürtre kattint a **Cloud Explorer** ben, és válassza a **Streamelési nyomkövetések letiltása parancsot.**
   
    ![Távoli adatfolyam-nyomkövetések letiltása][disablestreamingtraces]

## <a name="next-steps"></a>További lépések
* [Tesztelje a Service Fabric szolgáltatást.](service-fabric-testability-overview.md)
* [A Service Fabric-alkalmazások kezelése a Visual Studióban.](service-fabric-manage-application-in-visual-studio.md)

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
