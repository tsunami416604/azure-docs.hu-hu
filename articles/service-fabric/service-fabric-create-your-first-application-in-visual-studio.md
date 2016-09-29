<properties
   pageTitle="Az első Service Fabric-alkalmazás létrehozása a Visual Studióban | Microsoft Azure"
   description="Service Fabric-alkalmazás létrehozása, üzembe helyezése és hibakeresése a Visual Studio használatával"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2016"
   ms.author="ryanwi"/>


# Az első Azure Service Fabric-alkalmazás létrehozása a Visual Studióban

A Service Fabric SDK egy Visual Studio-bővítmény tartalmaz, amely sablonokat és eszközöket biztosít a Service Fabric-alkalmazások létrehozásához, üzembe helyezéséhez és hibakereséséhez. Ez a témakör végigvezeti Önt az első alkalmazás létrehozásának folyamatán a Visual Studióban.

## Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy [beállította a fejlesztőkörnyezetet](service-fabric-get-started.md).

## Bemutató videó

A következő videó végigkalauzolja az ebben az oktatóanyagban található lépéseken.

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## Az alkalmazás létrehozása

A Service Fabric-alkalmazás egy vagy több szolgáltatást tartalmazhat, melyek mindegyike adott szerepkörrel rendelkezik az alkalmazás funkcióinak biztosításához. Az Új projekt varázslóval az első szolgáltatási projektjével együtt egy alkalmazási projektet is létrehozhat. Később további szolgáltatásokat is hozzáadhat.

1. Indítsa el a Visual Studiót rendszergazdaként.

2. Kattintson a **File > New Project > Cloud > Service Fabric Application** (Fájl > Új projekt > Felhő > Service Fabric-alkalmazás) elemre.

3. Adjon nevet az alkalmazásnak, majd kattintson az **OK** gombra.

    ![A Visual Studio Új projekt párbeszédpanelje][1]

4. A következő lapon, az alkalmazásba belefoglalni kívánt első szolgáltatástípusként válassza az **Állapotalapú** lehetőséget. Adjon neki nevet, majd kattintson az **OK** gombra.

    ![A Visual Studio Új szolgáltatás párbeszédpanelje][2]

    >[AZURE.NOTE] További tudnivalók a beállításokról: [Service Fabric programming model overview](service-fabric-choose-framework.md) (A Service Fabric programozási modell áttekintése).

    A Visual Studio létrehozza az alkalmazási projektet és az állapotalapú szolgáltatási projektet, és megjeleníti őket a Megoldáskezelőben.

    ![A Megoldáskezelő folytatja az alkalmazás létrehozását állapotalapú szolgáltatással][3]

    Az alkalmazási projekt nem tartalmaz közvetlenül semmilyen kódot. Helyette számos szolgáltatási projektre hivatkozik. Ezenfelül három egyéb típusú tartalmat is tartalmaz:

    - **Profilok közzététele**: különböző környezetek eszközbeállításainak a kezelésére használható.

    - **Parancsfájlok**: az alkalmazás üzembe helyezéséhez/frissítéséhez szükséges PowerShell-parancsfájlt tartalmazza. A Visual Studio a saját belső szkriptjét használja. A szkript meghívása közvetlenül a parancssorból is történhet.

    - **Alkalmazásdefiníció**: tartalmazza az alkalmazásjegyzéket az *ApplicationPackageRoot* területen. A társított alkalmazások *ApplicationParameters* területen található paraméterfájljai határozzák meg az alkalmazást, és teszik lehetővé az adott környezetnek megfelelően történő konfigurálást.

    A szolgáltatási projekt tartalmának áttekintéséhez lásd: [Bevezetés a Reliable Services használatába](service-fabric-reliable-services-quick-start.md).

## Az alkalmazás üzembe helyezése és hibakeresése

Most, hogy megírta az alkalmazást, próbálja meg futtatni azt.

1. Nyomja le az F5 billentyűt a Visual Studióban, hogy üzembe helyezze az alkalmazást a hibakereséshez.

    >[AZURE.NOTE] Az üzembe helyezés az első alkalommal hosszabb időt vehet igénybe, mivel a Visual Studio egy helyi fejlesztői fürtöt hoz létre. A helyi fürt ugyanazt a platformkódot futtatja, amelyet Ön egy többgépes fürtben hoz létre, csak egyetlen számítógépen. A fürt létrehozási állapota a Visual Studio kimeneti ablakában jelenik meg.

    Amikor a fürt létrejött, értesítést fog kapni a helyi fürt SDK-hoz tartozó rendszertálca-kezelő alkalmazásától.

    ![A helyifürt-rendszertálca értesítése][4]

2. Az alkalmazás elindításakor a Visual Studio automatikusan megjeleníti a Diagnosztikai eseménynaplót, ahol a szolgáltatás nyomkövetési kimenetei tekinthetők meg.

    ![Diagnosztikai eseménynapló][5]

    Állapotalapú szolgáltatás sablon használata esetén az üzenetekben egyszerűen a MyStatefulService.cs `RunAsync` metódusa számlálójának növekvő értékei jelennek meg.

3. Bontsa ki az egyik eseményt, hogy további részleteket tekinthessen meg, beleértve azt a csomópontot is, amelyben a kód fut. Ebben az esetben ez a _Node_2, de az Ön számítógépén ez eltérő lehet.

    ![A diagnosztikai eseménynapló részletei][6]

    A helyi fürt egyetlen gépen üzemeltetett öt csomópontot tartalmaz. Olyan öt csomópontból álló fürtöt utánoz, amelynek a csomópontjai különböző gépeken vannak. Vegyük most a helyi fürt egyik csomópontját, és szimuláljuk rajta egy gép elvesztését, miközben a Visual Studio hibakeresőjét használjuk.

    >[AZURE.NOTE] A projektsablon által kibocsátott alkalmazásdiagnosztikai események a benne foglalt `ServiceEventSource` osztályt használják. További információ: [A szolgáltatások helyi figyelése és diagnosztikája](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

4. A szolgáltatási projektben keresse meg a StatefulService szolgáltatástípusból származtatott osztályt (például MyStatefulService), majd a `RunAsync` metódus első sorában állítson be egy töréspontot.

    ![Töréspont az állapotalapú szolgáltatás RunAsync metódusában][7]

5. Kattintson a jobb gombbal a Local Cluster Manager rendszertálca-alkalmazásra, és válassza ki a **Manage Local Cluster** (Helyi fürt kezelése) lehetőséget a Service Fabric Explorer elindításához.

    ![A Manage Local Cluster alkalmazásból indítsa el a Service Fabric Explorert.][systray-launch-sfx]

    A Service Fabric Explorer lehetővé teszi egy fürt vizuális megjelenítését, beleértve az azon üzembe helyezett alkalmazáskészletet és az azt felépítő fizikai csomópontokat is. A Service Fabric Explorerrel kapcsolatos további tudnivalókért lásd: [A fürt megjelenítése](service-fabric-visualizing-your-cluster.md).

6. A bal oldali panelen bontsa ki a **Cluster > Nodes** (Fürt > Csomópontok) elemet, és keresse meg azt csomópont, amelyikben a kódja fut.

7. Kattintson az **Actions > Deactivate (Restart)** (Műveletek > Inaktiválás (Újraindítás)) elemre a számítógép-újraindítás szimulálásához. (Vegye figyelembe, hogy az inaktiválás szintén a bal oldali ablaktábla csomóponti listanézetének helyi menüjéből végezhető el.)

    ![Csomópont leállítása a Service Fabric Explorerben][sfx-stop-node]

    Pillanatnyilag a töréspont megjelenését tekintheti meg a Visual Studióban, mivel az egyik csomóponton korábban végzett számítása zökkenőmentesen átadja a feladatokat egy másiknak.

8. Térjen vissza a Diagnosztikai eseménynaplóhoz, és vizsgálja meg az üzeneteket. Vegye figyelembe, hogy a számláló értéke továbbra is növekedett, annak ellenére, hogy az események valójában egy másik csomópontról érkeznek.

    ![A diagnosztikai eseménynapló a feladatátvétel után][diagnostic-events-viewer-detail-post-failover]

## Fürt üzemmód átkapcsolása

Alapértelmezés szerint a helyi fejlesztési fürt 5 csomópontos fürtként való futáshoz van konfigurálva, ami több csomópontra telepített szolgáltatások hibakeresésében nagyon hasznos. Azonban az alkalmazások 5 csomópontos fejlesztési fürtre való telepítése eltarthat egy ideig. Ha azt szeretné, hogy a kódmódosítások gyorsan települjenek, és nincs szükség az alkalmazás 5 csomóponton való futtatására, átválthatja a fejlesztési fürtöt 1 csomópontos üzemmódúra. Ha a kódot egy csomópontos fürtön szeretné futtatni, kattintson a jobb gombbal a tálcán található Local Cluster Manager elemre, és válassza ki a **Fürt üzemmód átkapcsolása -> 1 csomópontos** lehetőséget.  

![Fürt üzemmód átkapcsolása][switch-cluster-mode]

A fürt üzemmód átváltásakor a fejlesztési fürt visszaáll alaphelyzetbe, és az összes kiépített vagy a fürtön futó alkalmazás törlődik.

## Takarítás

  A téma lezárása előtt fontos megjegyezni, hogy a helyi fürt nagyon is valós. A hibakereső leállításával eltávolítja az adott alkalmazáspéldányt, és törli az alkalmazástípus regisztrációját. A fürt futtatása azonban a háttérben tovább folytatódik. A fürt kezelésére számos lehetősége van:

  1. A fürt leállításához, ugyanakkor az alkalmazás adatainak és nyomkövetéseinek megtartásához a rendszertálca alkalmazásban kattintson a **Stop Local Cluster** (Helyi fürt leállítása) parancsra.

  2. A fürt teljes törléséhez a rendszertálca alkalmazásban kattintson a **Remove Local Cluster** (Helyi fürt eltávolítása) parancsra. Vegye figyelembe, hogy ez a beállítás egy másik lassú üzembe helyezést fog eredményezni, amikor legközelebb a Visual Studióban lenyomja az F5 billentyűt. A fürtöt csak abban az esetben törölje, ha egy ideig nem kívánja használni a helyi fürtöt, vagy ha erőforrásokat kíván felszabadítani.

## Következő lépések

- Megismerkedhet egy [azure-beli fürt](service-fabric-cluster-creation-via-portal.md) vagy egy [önálló windowsbeli fürt](service-fabric-cluster-creation-for-windows-server.md) létrehozásával.
- Próbáljon szolgáltatást létrehozni a [Reliable Services](service-fabric-reliable-services-quick-start.md) vagy a [Reliable Actors](service-fabric-reliable-actors-get-started.md) programozási modellek használatával.
- Megtudhatja, hogyan tölthet fel szolgáltatásokat az internetre egy [webszolgáltatási előtér](service-fabric-add-a-web-frontend.md) használatával.
- Hajtsa végre a [laborgyakorlat](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) lépéseit, majd hozzon létre állapotmentes szolgáltatást, konfigurálja a figyelési és az állapotjelentéseket, és hajtson végre alkalmazásfrissítést.

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png



<!--HONumber=Sep16_HO4-->


