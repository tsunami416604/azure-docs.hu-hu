<properties
   pageTitle="A helyi fürtön lévő alkalmazások üzembe helyezésének és frissítésének elsajátítása | Microsoft Azure"
   description="Állítson be egy helyi Service Fabric-fürtöt, helyezzen üzembe rajta egy meglévő alkalmazást, majd frissítse az adott alkalmazást."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/12/2016"
   ms.author="ryanwi"/>

# A helyi fürtön lévő alkalmazások üzembe helyezésének és frissítésének elsajátítása
Az Azure Service Fabric SDK teljes helyi fejlesztőkörnyezetet tartalmaz, amely segítségével gyorsan megismerkedhet a helyi fürtön lévő alkalmazások üzembe helyezésével és kezelésével. Ebben a cikkben létrehoz egy helyi fürtöt, üzembe helyez rajta egy meglévő alkalmazást, majd új verzióra frissíti, és mindezt a Windows PowerShellből fogja elvégezni.

> [AZURE.NOTE] A cikk feltételezi, hogy Ön már [beállította a fejlesztőkörnyezetet](service-fabric-get-started.md).

## Helyi fürt létrehozása
A Service Fabric-fürt olyan hardver-erőforrások készletét képviseli, amelyeken alkalmazásokat helyezhet üzembe. Egy fürt általában tetszőleges számú gépből épül fel, a számuk öttől akár több ezerig terjedhet. A Service Fabric azonban SDK olyan fürtkonfigurációt tartalmaz, amely csak egyetlen gépen futhat.

Fontos tisztában lenni azzal, hogy a Service Fabric helyi fürtje nem emulátor vagy szimulátor. Ugyanazt a platformkódot futtatja, mint ami a több számítógépes fürtökön található. Az egyetlen különbség, hogy az általában öt számítógép között elosztott platformfolyamatokat egyetlen számítógépen futtatja.

Az SDK két lehetőséget biztosít a helyi fürt beállításához: egy Windows PowerShell-parancsfájlt és a Local Cluster Manager rendszertálca-alkalmazást. Ebben az oktatóanyagban a PowerShell-parancsfájlt használjuk.

> [AZURE.NOTE] Ha a Visual Studióból egy alkalmazás üzembe helyezésével már létrehozott egy a helyi fürtöt, ezt a szakaszt kihagyhatja.


1. Nyisson meg egy új PowerShell-ablakot rendszergazdaként.

2. Futtassa a fürtbeállítási parancsfájlt az SDK-mappából:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```

    A fürtbeállítás eltarthat néhány percig. A beállítás befejezése után az alábbihoz hasonló kimenetnek kell megjelennie:

    ![A fürtbeállítás kimenete][cluster-setup-success]

    Most már készen arra, hogy megpróbáljon üzembe helyezni egy alkalmazást a fürtön.

## Alkalmazás üzembe helyezése
A Service Fabric SDK az alkalmazások létrehozásához szükséges keretrendszerek és fejlesztőeszközök gazdag készletét tartalmazza. Ha meg szeretné ismerni az alkalmazások létrehozási módját a Visual Studióban, tekintse meg [Az első Service Fabric-alkalmazás létrehozása a Visual Studióban](service-fabric-create-your-first-application-in-visual-studio.md) témakört.

Ebben az oktatóanyagban egy meglévő mintaalkalmazást (a neve WordCount) használunk, így a platformkezelési szempontokra (például üzembe helyezés, figyelés és frissítés) összpontosíthatunk.


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

4. [Töltse le a WordCount alkalmazást](http://aka.ms/servicefabric-wordcountapp) a létrehozott helyre.

5. Csatlakozzon a helyi fürthöz:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Hívja meg az SDK üzembe helyezési parancsát, hozzon létre egy új alkalmazást, és adja meg az alkalmazáscsomag nevét és elérési útját.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Ha minden megfelelően működik, a következőhöz hasonló kimenetnek kell megjelennie:

    ![Alkalmazás üzembe helyezése a helyi fürtön][deploy-app-to-local-cluster]

7. Ha szeretné az alkalmazást működés közben megtekinteni, indítsa el a böngészőt, és navigáljon a következő webhelyre: [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Ennek nagyjából a következőképpen kell kinéznie:

    ![Az üzembe helyezett alkalmazás felhasználói felülete][deployed-app-ui]

    A WordCount alkalmazás rendkívül egyszerű. Ügyféloldali JavaScript-kódot tartalmaz öt karakterből álló „szavak” véletlenszerű előállításához, amelyek aztán az ASP.NET Web API-n keresztül továbbítódnak az alkalmazásnak. Egy állapotalapú szolgáltatás nyomon követi a megszámolt szavak számát. Ezek particionálása a szó első karaktere alapján történik.

    Az üzembe helyezett alkalmazás négy partíciót tartalmaz. Az A–G kezdetű szavak az első partícióban, a H–N kezdetű szavak a második partícióban vannak tárolva és így tovább.

## Az alkalmazás részleteinek és állapotának megtekintése
Miután üzembe helyeztük az alkalmazást, nézzük meg a PowerShellben az alkalmazás egyes részleteit.

1. A fürt összes üzembe helyezett alkalmazásának lekérdezése:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Feltételezve, hogy már üzembe helyezte a WordCount alkalmazást, a következőhöz hasonló elemeknek kell megjelennie:

    ![Az összes üzembe helyezett alkalmazás lekérdezése a PowerShellben][ps-getsfapp]

2. Lépjen a következő szintre a WordCount alkalmazásban szereplő szolgáltatáskészlet lekérdezésével.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Az alkalmazáshoz használható szolgáltatások listája a PowerShellben][ps-getsfsvc]

    Vegye figyelembe, hogy az alkalmazás két szolgáltatásból áll: a webes kezelőfelületből és az állapotalapú szolgáltatásból, amely a szavakat kezeli.

3. Végül tekintse meg a WordCountService partícióinak listáját:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![A szolgáltatáspartíciók megtekintése a PowerShellben][ps-getsfpartitions]

    Az Ön által éppen most használt parancsok készlete, például a Service Fabric PowerShell-parancsok, bármilyen (helyi vagy távoli) fürt számára elérhetők, amelyekhez kapcsolódni tud.

    Ha vizuálisabban szeretne kommunikálni a fürttel, használhatja a webalapú Service Fabric Explorer eszközt is, ha a böngészőben megnyitja a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) weblapot.

    ![Az alkalmazás részleteinek megtekintése a Service Fabric Explorerben][sfx-service-overview]

    > [AZURE.NOTE] A Service Fabric Explorer használatával kapcsolatos további tudnivalókért lásd: [A fürt megjelenítése a Service Fabric Explorerrel](service-fabric-visualizing-your-cluster.md).

## Alkalmazás frissítése
A Service Fabric állásidő nélküli frissítéseket biztosít az alkalmazás állapotának figyelésével, miközben megtörténik a bevezetése az egész fürtön. Végezzük el a WordCount alkalmazás egyszerű frissítését.

Az alkalmazás új verziója mostantól csak a magánhangzóval kezdődő szavakat fogja számolni. A frissítés bevezetése során két változást figyelhetünk meg az alkalmazás viselkedésében. Az első az a sebesség, amelynél a számok növekedésének le kell lassulnia, mivel kevesebb szót kell megszámolni. A második, mivel az első partíció két magánhangzót tartalmaz (A és E), és az összes többi partíció mindegyike csak egyet, az első partíciónál a szám gyorsabban fog növekedni, mint a többinél.

1. [Töltse le a WordCount v2 csomagot](http://aka.ms/servicefabric-wordcountappv2) ugyanarra a helyre, amelyre a v1 csomagot töltötte.

2. Térjen vissza a PowerShell-ablakhoz, és az SDK frissítési parancsával regisztrálja az új verziót a fürtben. Ezután indítsa el a fabric:/WordCount alkalmazás frissítését.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Amint a frissítés elindul, a következőhöz hasonló kimenetet kell látnia a PowerShellben.

    ![Folyamatban lévő frissítés a PowerShellben][ps-appupgradeprogress]

3. A frissítés folyamán lehet, hogy egyszerűbb az állapot figyelése a Service Fabric Explorerből. Nyisson meg egy böngészőablakot, és navigáljon a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) weboldalra. A bal oldali fában bontsa ki az **Alkalmazások** elemet, majd válassza ki a **WordCount**, végül a **fabric:/WordCount** elemet. Az alapvető erőforrások lapon látni fogja a frissítési állapot előrehaladását a fürt frissítési tartományain keresztül.

    ![A frissítés folyamata a Service Fabric Explorerben][sfx-upgradeprogress]

    Ahogy a frissítés előrehalad az egyes tartományokon keresztül, a rendszer mindig állapotellenőrzést végez annak érdekében, hogy biztosítsa az alkalmazás megfelelő viselkedését.

4. Ha újrafuttatja fabric:/WordCount alkalmazásban található szolgáltatáskészlet korábbi lekérdezését, megfigyelheti, hogy míg a WordCountService verziója megváltozott, a WordCountWebService verziója nem.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Az alkalmazásszolgáltatások lekérdezése frissítés után][ps-getsfsvc-postupgrade]

    Ez rávilágít arra, hogyan kezeli a Service Fabric az alkalmazásfrissítéseket. Csak azokat a szolgáltatásokat (vagy a szolgáltatásokban található kódokat, illetve konfigurációs csomagokat) érinti, amelyek megváltoztak, ezáltal a frissítés folyamata sokkal gyorsabb és megbízhatóbb lesz.

5. Végül térjen vissza a böngészőhöz, és figyelje meg az új alkalmazásverzió működését. A számláló a vártnak megfelelően lassabban számol, és az első partíció valamivel nagyobb kötettel fejeződik be.

    ![Az alkalmazás új verziójának megtekintése a böngészőben][deployed-app-ui-v2]

## Következő lépések
- Most, hogy már üzembe helyezett és frissített néhány előre létrehozott alkalmazást, [megpróbálhatja felépíteni a saját alkalmazását a Visual Studióban](service-fabric-create-your-first-application-in-visual-studio.md).
- A jelen cikkben a helyi fürttel elvégzett összes művelet elvégezhető egy [Azure-fürttel](service-fabric-cluster-creation-via-portal.md) is.
- A cikkben végrehajtott frissítés nagyon alapszintű volt. A Service Fabric frissítéskezelésének hatékonyságát és rugalmasságát a [frissítési dokumentációból](service-fabric-application-upgrade.md) ismerheti meg.

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



<!--HONumber=jun16_HO2-->


