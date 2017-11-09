---
title: "Azure mikroszolgáltatások helyi telepítése és frissítése | Microsoft Docs"
description: "Megtudhatja, hogyan állítson be egy helyi Service Fabric-fürtöt, helyezzen üzembe rajta egy meglévő alkalmazást, majd frissítse az adott alkalmazást."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/03/2017
ms.author: ryanwi;mikhegn
ms.openlocfilehash: 9d547039491f6b0c0d308c64d11e9c54b4b4e3d5
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>A helyi fürtön lévő alkalmazások üzembe helyezésének és frissítésének elsajátítása
Az Azure Service Fabric SDK teljes helyi fejlesztőkörnyezetet tartalmaz, amely segítségével gyorsan megismerkedhet a helyi fürtön lévő alkalmazások üzembe helyezésével és kezelésével. Ebben a cikkben létrehoz egy helyi fürtöt, üzembe helyez rajta egy meglévő alkalmazást, majd új verzióra frissíti, és mindezt a Windows PowerShellből fogja elvégezni.

> [!NOTE]
> A cikk feltételezi, hogy Ön már [beállította a fejlesztőkörnyezetet](service-fabric-get-started.md).
> 
> 

## <a name="create-a-local-cluster"></a>Helyi fürt létrehozása
A Service Fabric-fürt olyan hardver-erőforrások készletét képviseli, amelyeken alkalmazásokat helyezhet üzembe. Egy fürt általában tetszőleges számú gépből épül fel, a számuk öttől akár több ezerig terjedhet. A Service Fabric azonban SDK olyan fürtkonfigurációt tartalmaz, amely csak egyetlen gépen futhat.

Fontos tisztában lenni azzal, hogy a Service Fabric helyi fürtje nem emulátor vagy szimulátor. Ugyanazt a platformkódot futtatja, mint ami a több számítógépes fürtökön található. Az egyetlen különbség, hogy az általában öt számítógép között elosztott platformfolyamatokat egyetlen számítógépen futtatja.

Az SDK két lehetőséget biztosít a helyi fürt beállításához: egy Windows PowerShell-parancsfájlt és a Local Cluster Manager rendszertálca-alkalmazást. Ebben az oktatóanyagban a PowerShell-szkriptet használjuk.

> [!NOTE]
> Ha a Visual Studióból egy alkalmazás üzembe helyezésével már létrehozott egy a helyi fürtöt, ezt a szakaszt kihagyhatja.
> 
> 

1. Nyisson meg egy új PowerShell-ablakot rendszergazdaként.
2. Futtassa a fürtbeállítási parancsfájlt az SDK-mappából:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    A fürt beállítása hosszabb időt vehet igénybe. A beállítást követően a következőhöz hasonló kimenetnek kell megjelennie:
   
    ![A fürtbeállítás kimenete][cluster-setup-success]
   
    Most már készen arra, hogy megpróbáljon üzembe helyezni egy alkalmazást a fürtön.

## <a name="deploy-an-application"></a>Alkalmazás üzembe helyezése
A Service Fabric SDK az alkalmazások létrehozásához szükséges keretrendszerek és fejlesztőeszközök gazdag készletét tartalmazza. Ha meg szeretné ismerni az alkalmazások létrehozási módját a Visual Studióban, tekintse meg [Az első Service Fabric-alkalmazás létrehozása a Visual Studióban](service-fabric-create-your-first-application-in-visual-studio.md) témakört.

Ebben az oktatóanyag egy meglévő mintaalkalmazást (a neve WordCount) használ, így a platformkezelési szempontokra (üzembe helyezés, figyelés és frissítés) összpontosíthat.

1. Nyisson meg egy új PowerShell-ablakot rendszergazdaként.
2. Importálja a Service Fabric SDK PowerShell-modulját.
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. Hozzon létre a letöltendő és üzembe helyezendő alkalmazás tárolására szolgáló könyvtárat, például: C:\ServiceFabric.
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [Töltse le a WordCount alkalmazást](http://aka.ms/servicefabric-wordcountapp) a létrehozott helyre.  Megjegyzés: A Microsoft Edge böngésző *.zip* kiterjesztéssel menti a fájlt.  Módosítsa a fájl kiterjesztését a következőre: *.sfpkg*.
5. Csatlakozzon a helyi fürthöz:
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. Az SDK telepítési parancsának használatával hozzon létre egy új alkalmazást, adjon meg hozzá egy nevet és egy, az alkalmazáscsomagra mutató elérési utat.
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    Ha minden megfelelően működik, a következő kimenetnek kell megjelennie:
   
    ![Alkalmazás üzembe helyezése a helyi fürtön][deploy-app-to-local-cluster]
7. Ha szeretné az alkalmazást működés közben megtekinteni, indítsa el a böngészőt, és navigáljon a következő webhelyre: [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). A következőnek kell megjelennie:
   
    ![Az üzembe helyezett alkalmazás felhasználói felülete][deployed-app-ui]
   
    A WordCount alkalmazás egyszerűen használható. Ügyféloldali JavaScript-kódot tartalmaz öt karakterből álló „szavak” véletlenszerű előállításához, amelyek aztán az ASP.NET Web API-n keresztül továbbítódnak az alkalmazásnak. Az állapotalapú szolgáltatások nyomon követik a szavak számának változását. Ezek particionálása a szó első karaktere alapján történik. A WordCount alkalmazás forráskódját a [klasszikus első lépéseket ismertető minták](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount) könyvtárában érheti el.
   
    Az üzembe helyezett alkalmazás négy partíciót tartalmaz. Az A–G kezdetű szavak az első partícióban, a H–N kezdetű szavak a második partícióban vannak tárolva és így tovább.

## <a name="view-application-details-and-status"></a>Az alkalmazás részleteinek és állapotának megtekintése
Miután üzembe helyeztük az alkalmazást, nézzük meg a PowerShellben az alkalmazás egyes részleteit.

1. A fürt összes üzembe helyezett alkalmazásának lekérdezése:
   
    ```powershell
    Get-ServiceFabricApplication
    ```
   
    Feltételezve, hogy kizárólag a WordCount alkalmazás lett üzembe helyezve, egy ehhez hasonló képernyőnek kell megjelennie:
   
    ![Az összes üzembe helyezett alkalmazás lekérdezése a PowerShellben][ps-getsfapp]
2. Lépjen a következő szintre a WordCount alkalmazásban szereplő szolgáltatáskészlet lekérdezésével.
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Az alkalmazáshoz használható szolgáltatások listája a PowerShellben][ps-getsfsvc]
   
    Az alkalmazás két szolgáltatásból áll: a webes kezelőfelületből és az állapotalapú szolgáltatásból, amely a szavakat kezeli.
3. Végül tekintse meg a WordCountService partícióinak listáját:
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![A szolgáltatáspartíciók megtekintése a PowerShellben][ps-getsfpartitions]
   
    Az Ön által használt parancsok készlete, például a Service Fabric PowerShell-parancsok, bármilyen (helyi vagy távoli) fürt számára elérhetők, amelyekhez kapcsolódni tud.
   
    Ha vizuálisabban szeretne kommunikálni a fürttel, használhatja a webalapú Service Fabric Explorer eszközt is, ha a böngészőben megnyitja a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) weblapot.
   
    ![Az alkalmazás részleteinek megtekintése a Service Fabric Explorerben][sfx-service-overview]
   
   > [!NOTE]
   > A Service Fabric Explorer használatával kapcsolatos további tudnivalókért lásd: [A fürt megjelenítése a Service Fabric Explorerrel](service-fabric-visualizing-your-cluster.md).
   > 
   > 

## <a name="upgrade-an-application"></a>Alkalmazás frissítése
A Service Fabric állásidő nélküli frissítéseket biztosít az alkalmazás állapotának figyelésével, miközben megtörténik a bevezetése az egész fürtön. Végezze el a WordCount alkalmazás frissítését.

Az alkalmazás új verziója kizárólag a magánhangzóval kezdődő szavakat számolja össze. A frissítés előre haladtával két változást vehet észre az alkalmazás viselkedésében. Az első az a sebesség, amelynél a számok növekedésének le kell lassulnia, mivel kevesebb szót kell megszámolni. A második, mivel az első partíció két magánhangzót tartalmaz (A és E), és az összes többi partíció mindegyike csak egyet, az első partíciónál a szám gyorsabban fog növekedni, mint a többinél.

1. [Töltse le a WordCount 2-es verziójának csomagját](http://aka.ms/servicefabric-wordcountappv2) ugyanarra a helyre, ahová az 1-es verzió csomagját letöltötte.
2. Térjen vissza a PowerShell-ablakhoz, és az SDK frissítési parancsával regisztrálja az új verziót a fürtben. Ezután indítsa el a fabric:/WordCount alkalmazás frissítését.
   
    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```
   
    A frissítés indításakor a következő kimenetnek kell megjelennie a PowerShellben.
   
    ![Folyamatban lévő frissítés a PowerShellben][ps-appupgradeprogress]
3. A frissítés folyamán lehet, hogy egyszerűbb az állapot figyelése a Service Fabric Explorerből. Nyisson meg egy böngészőablakot, és navigáljon a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) weboldalra. A bal oldali fában bontsa ki az **Alkalmazások** elemet, majd válassza ki a **WordCount**, végül a **fabric:/WordCount** elemet. Az alapvető erőforrások lapján láthatja a frissítés állapotát a fürt frissítési tartományaiban.
   
    ![A frissítés folyamata a Service Fabric Explorerben][sfx-upgradeprogress]
   
    Ahogy a frissítés előrehalad az egyes tartományokon keresztül, a rendszer mindig állapotellenőrzést végez annak érdekében, hogy biztosítsa az alkalmazás megfelelő viselkedését.
4. Ha újra futtatja a korábbi, a fabric:/WordCount alkalmazás szolgáltatáskészletére irányuló lekérdezést, azt láthatja, hogy a WordCountService szolgáltatás verziószáma megváltozott, a WordCountWebService szolgáltatásé viszont nem:
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Az alkalmazásszolgáltatások lekérdezése frissítés után][ps-getsfsvc-postupgrade]
   
    Ez a példa rávilágít arra, hogyan kezeli a Service Fabric az alkalmazásfrissítéseket. Csak azokat a szolgáltatásokat (vagy a szolgáltatásokban található kódokat, illetve konfigurációs csomagokat) érinti, amelyek megváltoztak, ezáltal a frissítés folyamata sokkal gyorsabb és megbízhatóbb lesz.
5. Végül térjen vissza a böngészőhöz, és figyelje meg az új alkalmazásverzió működését. A számláló a vártnak megfelelően lassabban számol, és az első partíció valamivel nagyobb kötettel fejeződik be.
   
    ![Az alkalmazás új verziójának megtekintése a böngészőben][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Takarítás
A téma lezárása előtt fontos megjegyezni, hogy a helyi fürt valós. Az alkalmazások futtatása azonban a háttérben tovább folytatódik mindaddig, amíg el nem távolítja azokat.  Az alkalmazások jellegétől függően azok jelentős erőforrásokat is igénybe vehetnek a gépen. Az alkalmazás és a fürt kezelésére számos lehetősége van:

1. Egyedi alkalmazások és azok adatainak eltávolításához futtassa a következő parancsot:
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    Másik megoldásként törölje az alkalmazást a Service Fabric Explorer **MŰVELETEK** menüjéből, vagy a bal oldali alkalmazáslista-nézet helyi menüjéből.
   
    ![Alkalmazás törlése a Service Fabric Explorerrel][sfe-delete-application]
2. Az alkalmazás a fürtről történő törlését követően törölje a WordCount alkalmazástípus 1.0.0-s és 2.0.0-s verziójának regisztrációját is. A törléssel eltávolítja az alkalmazáscsomagokat a fürt lemezképtárolójából, beleértve a kódot és a konfigurációt is.
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    Esetleg a Service Fabric Explorerben válassza az **Unprovision Type** (Típus telepítésének visszavonása) lehetőséget az adott alkalmazás esetében.
3. A fürt leállításához, ugyanakkor az alkalmazás adatainak és nyomkövetéseinek megtartásához a rendszertálca alkalmazásban kattintson a **Stop Local Cluster** (Helyi fürt leállítása) parancsra.
4. A fürt teljes törléséhez a rendszertálca alkalmazásban kattintson a **Remove Local Cluster** (Helyi fürt eltávolítása) parancsra. Ez a beállítás egy másik lassú üzembe helyezést fog eredményezni, amikor legközelebb a Visual Studióban lenyomja az F5 billentyűt. A helyi fürtöt csak abban az esetben távolítsa el, ha egy ideig nem kívánja azt használni, vagy ha erőforrásokat kíván felszabadítani.

## <a name="one-node-and-five-node-cluster-mode"></a>Egycsomópontos és ötcsomópontos fürt üzemmód
Alkalmazások fejlesztésekor gyakran hajtja végre a kódírás, a hibakeresés, a kódmódosítás és a hibakeresés gyors iterációit. A folyamat optimalizálása érdekében a helyi fürt két módban futhat: egycsomópontos és ötcsomópontos módban. Mindkét fürt üzemmódnak megvannak az előnyei. Az ötcsomópontos fürt üzemmód lehetővé teszi, hogy egy valódi fürtön dolgozzon. Tesztelheti a feladatátvételi forgatókönyveket, a szolgáltatások több példányát és replikáját használhatja. Az egycsomópontos fürt üzemmód a gyors üzembe helyezésre és szolgáltatásregisztrációra van optimalizálva, így gyorsan ellenőrizheti a kódot a Service Fabric-futtatókörnyezettel.

Sem az egycsomópontos sem az ötcsomópontos fürt üzemmód nem emulátor vagy szimulátor. A helyi fejlesztési fürt ugyanazt a platformkódot futtatja, mint ami a többgépes fürtökön található.

> [!WARNING]
> Amikor módosítja a fürt üzemmódját, az aktuális fürtöt a rendszer eltávolítja, és egy új fürtöt hoz létre. A fürtben tárolt adatok törlődnek a fürt üzemmódjának váltásakor.
> 
> 

Az üzemmód egycsomópontos fürtre módosításához válassza a **Fürt üzemmód átkapcsolása** lehetőséget a Service Fabric Local Cluster Manager alkalmazásában.

![Fürt üzemmód átkapcsolása][switch-cluster-mode]

Vagy módosítsa a fürt üzemmódját a PowerShell-lel:

1. Nyisson meg egy új PowerShell-ablakot rendszergazdaként.
2. Futtassa a fürtbeállítási parancsfájlt az SDK-mappából:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    A fürt beállítása hosszabb időt vehet igénybe. A beállítást követően a következőhöz hasonló kimenetnek kell megjelennie:
   
    ![A fürtbeállítás kimenete][cluster-setup-success-1-node]

## <a name="next-steps"></a>Következő lépések
* Most, hogy már üzembe helyezett és frissített néhány előre létrehozott alkalmazást, [megpróbálhatja felépíteni a saját alkalmazását a Visual Studióban](service-fabric-create-your-first-application-in-visual-studio.md).
* A jelen cikkben a helyi fürttel elvégzett összes művelet elvégezhető egy [Azure-fürttel](service-fabric-cluster-creation-via-portal.md) is.
* A jelen cikkben végrehajtott frissítés alapszintű volt. A Service Fabric frissítéskezelésének hatékonyságát és rugalmasságát a [frissítési dokumentációból](service-fabric-application-upgrade.md) ismerheti meg.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
