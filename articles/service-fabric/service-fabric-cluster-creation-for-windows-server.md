---
title: Hozzon létre egy önálló Azure Service Fabric-fürtön |} A Microsoft Docs
description: Azure Service Fabric-fürt létrehozása (fizikai vagy virtuális) bármely gépen Windows Server rendszert futtató, akár a helyszínen vagy a felhőben.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 3e9e3afd5172783c6b5ed8e6342ce9927353d006
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665117"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>A Windows Server rendszert futtató önálló fürt létrehozása
Az Azure Service Fabric segítségével Service Fabric-fürtök létrehozása virtuális gépek vagy a Windows Server rendszert futtató számítógépeket. Ez azt jelenti, is telepíti, és futtassa a Service Fabric-alkalmazásokat minden olyan környezetben, amely tartalmazza az egyes hálózatáról van szó, a Windows Server számítógépek, legyen az a helyszínen vagy bármely más szolgáltatónál. A Service Fabric biztosít az új telepítési csomagot hozhat létre a Service Fabric-fürtök a különálló Windows Server-csomag neve.

Ez a cikk végigvezeti az önálló Service Fabric-fürt létrehozására vonatkozó lépéseket.

> [!NOTE]
> A különálló Windows Server-csomag kereskedelmi forgalomban beszerezhető, és éles környezetekben üzemelő példányok is használható. Ez a csomag új Service Fabric-szolgáltatások, amelyek az "Előnézet" tartalmazhat. Görgessen le a "[előzetes verziójú funkciók a csomagban](#previewfeatures_anchor)." az előzetes verziójú funkciók szakasz listája. Is [töltse le a végfelhasználói licencszerződés](https://go.microsoft.com/fwlink/?LinkID=733084) most.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Támogatás a Service Fabric Windows Server-csomag
* Kérdezzen a Közösségtől önálló Service Fabric-csomaggal kapcsolatos, a Windows Server a [Azure Service Fabric-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Nyisson meg egy jegyet [professzionális támogatás a Service fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  További információ a Microsoft a professzionális támogatás [Itt](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Is kaphat támogatást a csomag részeként [Microsoft Premier szintű támogatás](https://support.microsoft.com/en-us/premier).
* További részletekért tekintse meg [Azure Service Fabric támogatási lehetőségeinek](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Jellegű támogatási célból naplóinak összegyűjtése, futtassa a [Service Fabric önálló naplógyűjtő](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>A Windows Serverhez készült Service Fabric-csomag letöltése
A fürt létrehozásához használja a Service Fabric Windows Server-csomag (Windows Server 2012 R2 és újabb) itt található: <br>
[Töltse le a Windows Server - Service Fabric önálló csomag - hivatkozás](https://go.microsoft.com/fwlink/?LinkId=730690)

Keresse meg a részleteket a csomag tartalma a [Itt](service-fabric-cluster-standalone-package-contents.md).

A Service Fabric futtatókörnyezet-csomag automatikusan letölti a fürt létrehozása során. Ha üzembe helyezése a gép nem csatlakozik az internethez, töltse le a sávon kívüli futtatókörnyezet-csomag innen: <br>
[Töltse le a Windows Server - Service Fabric-futtatókörnyezet - hivatkozás](https://go.microsoft.com/fwlink/?linkid=839354)

Önálló fürtkonfiguráció minták keresése: <br>
[Önálló fürt konfigurációs minták](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>A fürt létrehozása
Több fürtkonfigurációs mintafájl is települ a telepítőcsomaggal. A *ClusterConfig.Unsecure.DevCluster.json* a legegyszerűbb fürtkonfiguráció: egy nem védett, egyetlen számítógépen futó, három csomóponttal rendelkező fürt.  A többi konfigurációs fájl X.509 tanúsítványokkal vagy a Windows biztonsági szolgáltatásaival védett egy- vagy többgépes fürtöket ír le.  Ehhez az oktatóanyaghoz az alapértelmezett konfigurációs beállítások egyikét sem kell módosítania, csak át kell tekintenie a konfigurációs fájlt, és meg kell ismerkednie a beállításokkal.  A **csomópontok** szakasz ismerteti a fürt három csomópontját: név, IP-cím, [csomóponttípus, tartalék tartomány és frissítési tartomány](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  A **tulajdonságok** szakasz definiálja a fürt [biztonságát, megbízhatósági szintjét, diagnosztikai gyűjteményét és a benne lévő csomópontok típusait](service-fabric-cluster-manifest.md#cluster-properties).

Ebben a cikkben létrehozott fürt nem biztonságos.  Bárki csatlakozhat hozzá névtelenül és végrehajthat kezelési műveleteket, ezért az üzemben lévő fürtöket mindig X.509 tanúsítványok vagy a Windows rendszerbiztonság használatával kell védeni.  A biztonság konfigurálására csak a fürt létrehozásakor van lehetőség, a fürt létrehozása után már nem lehet engedélyezni. Frissítés a konfigurációs fájl engedélyezése [biztonsági tanúsítvány](service-fabric-windows-cluster-x509-security.md) vagy [Windows biztonsági](service-fabric-windows-cluster-windows-security.md). A Service Fabric-fürtök védelmével kapcsolatos további tudnivalókért lásd: [Fürt biztonságossá tétele](service-fabric-cluster-security.md).

### <a name="step-1-create-the-cluster"></a>1. lépés: A fürt létrehozása

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>„A” alkalmazási helyzet: Egy nem biztonságos helyi fejlesztési fürt létrehozása
A Service Fabric használatával bármely helyezhető egy gép egy fejlesztési fürtöt a *ClusterConfig.Unsecure.DevCluster.json* fájlban szereplő [minták](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Csomagolja ki a különálló csomag a számítógépre, a minta konfigurációs fájl a helyi számítógépre másolja, majd futtassa a *CreateServiceFabricCluster.ps1* a különálló csomag mappájából egy rendszergazdai PowerShell-munkameneten keresztül parancsfájl .

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

A környezet beállítása című [tervezze meg és készítse elő a fürt üzembe helyezése](service-fabric-cluster-standalone-deployment-preparation.md) hibaelhárítási részleteket.

Ha végzett a futó fejlesztési forgatókönyvek, eltávolíthatja a Service Fabric-fürtön a gépről hivatkozó szakaszban ismertetett lépések szerint "[fürt eltávolítása](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>„B” alkalmazási helyzet: Egy többgépes fürt létrehozása
Miután elvégezte a tervezési és előkészítő lépések részletes [tervezze meg és készítse elő a fürt üzembe helyezése](service-fabric-cluster-standalone-deployment-preparation.md), készen áll az éles fürt a fürt konfigurációs fájl használatával létrehozásához.

A fürt üzembe helyezését és konfigurálását végző fürtrendszergazdának rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen. A Service Fabric tartományvezérlőn nem telepíthető.

1. Az önálló csomagban lévő *TestConfiguration.ps1* szkript az ajánlott eljárásokat elemző eszközként használható annak ellenőrzésére, hogy egy fürt az adott környezetben üzembe helyezhető-e. Az [Üzembe helyezés előkészítése](service-fabric-cluster-standalone-deployment-preparation.md) az előfeltételeket és a környezeti követelményeket sorolja fel. A szkript futtatásával ellenőrizze, hogy létre tudja-e hozni a fejlesztési fürtöt:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Az alábbihoz hasonló kimenetnek kell megjelennie. Ha a "Sikeres" adja vissza "true"értékre, melyeknél sikeres az megerősítések alsó mezőbe, és a fürt üzembe helyezhető kell bemeneti konfigurációja alapján.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. A fürt létrehozása:  Futtassa a *CreateServiceFabricCluster.ps1* üzembe helyezi a Service Fabric-fürtön a konfigurációban minden gép között. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Az üzembe helyezés nyomait arra a gépre/virtuális gépre írja a rendszer, amelyen a CreateServiceFabricCluster.ps1 PowerShell-szkriptet futtatta. Ezek a DeploymentTraces nevű almappában találhatók, azon könyvtár alapján, amelyből a szkriptet futtatta. Ha ellenőrizni kívánja, hogy a Service Fabric megfelelően lett-e üzembe helyezve a gépen, keresse meg a telepített fájlokat a FabricDataRoot könyvtárban, ahogy az a fürtkonfigurációs fájl FabricSettings szakaszában szerepel (alapértelmezés szerint ez a c:\ProgramData\SF). A FabricHost.exe és a Fabric.exe folyamat futása is látható a Feladatkezelőben.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Forgatókönyv: C: Egy kapcsolat nélküli (internet-kapcsolat nélkül)-fürt létrehozása
A Service Fabric futtatókörnyezet-csomag automatikusan letölti a fürt létrehozásakor. Amikor a fürt üzembe helyezése a gép nem csatlakozik az internethez, kell külön töltse le a Service Fabric futtatókörnyezet-csomag, és adja meg a fürt létrehozásakor az elérési utat.
A futtatókörnyezet-csomag tölthetők, az internethez csatlakozik másik gépről [letöltési hivatkozás - Service Fabric-futtatókörnyezet – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Ahol a kapcsolat nélküli fürtre végzi, és a fürt létrehozásához futtassa a futtatókörnyezet-csomag másolása `CreateServiceFabricCluster.ps1` együtt a `-FabricRuntimePackagePath` paramétert tartalmaz, ebben a példában látható módon: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.JSON* és *.\MicrosoftAzureServiceFabric.cab* rendre vannak a fürt konfigurációját, és a futtatókörnyezet .cab-fájl elérési útja.

### <a name="step-2-connect-to-the-cluster"></a>2. lépés: Csatlakozás a fürthöz
Csatlakozás a fürthöz, ellenőrizheti a fürt fut, és elérhető-e. A ServiceFabric PowerShell-modul a futtatókörnyezettel együtt települ.  A fürtcsomópontok közül, vagy egy távoli számítógépről a Service Fabric-futtatókörnyezet képes csatlakozni a fürthöz.  A [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag kiépít egy kapcsolatot a fürttel.

A nem biztonságos fürtökhöz csatlakozhat, futtassa a következő PowerShell-parancsot:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Példa:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```

A fürtökhöz való csatlakozást bemutató egyéb példákért lásd: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md). Miután csatlakozott a fürthöz, a [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) parancsmaggal listázza a fürtben lévő csomópontokat és az egyes csomópontok állapotinformációit. A **HealthState** tulajdonságnak *OK* értékűnek kell lennie minden csomópont esetében.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>3. lépés: A fürt megjelenítése a Service Fabric Explorerrel
A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) hatékony eszköz a fürtök megjelenítéséhez és az alkalmazások kezeléséhez.  Service Fabric Explorert a szolgáltatást, amely a fürt, amely egy böngészővel megnyitásával érhető [ http://localhost:19080/Explorer ](http://localhost:19080/Explorer).

A fürt irányítópultja áttekintést nyújt a fürtről, beleértve az alkalmazások és a csomópontok állapotának összefoglalását. A csomópontnézet a fürt fizikai elrendezését mutatja. Az egyes csomópontoknál megtekintheti, hogy melyik alkalmazások kódja üzemel az adott csomóponton.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Csomópontok hozzáadása és eltávolítása
Az üzleti igényei változásával hozzáadhat vagy eltávolíthat csomópontokat az önálló Service Fabric-fürtről. A lépések részletes leírása: [Csomópontok hozzáadása vagy eltávolítása egy önálló Service Fabric-fürtről](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Fürt eltávolítása
Egy fürt eltávolításához futtassa a *RemoveServiceFabricCluster.ps1* PowerShell-szkriptet a csomag mappájából, és adja meg a JSON-konfigurációs fájl elérési útját. Megadhatja a törlés naplójának mentési helyét is.

Ezt a parancsfájlt minden olyan gép, amely számítanak csomópontnak a fürt konfigurációs fájlban felsorolt összes gép rendszergazdai hozzáféréssel rendelkezik. Ez a szkript futtatott a gép nem rendelkezik a fürt részeként.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Az összegyűjtött telemetrikus adatok, és hogyan
Alapértelmezés szerint a termék telemetriai adatokat a termék javítása érdekében a Service Fabric használati adatokat gyűjt. Az ajánlott eljárásokat elemző eszköz, amely futtatja a telepítés részeként ellenőrzi a csatlakozást [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1). Ha nem érhető el, a telepítés sikertelen lesz, kivéve, ha kikapcsolja a telemetriai adatokat.

1. A telemetriai adatok folyamat megpróbálja töltse fel az alábbi adatokat [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1) naponta egyszer. A legjobb feltöltése és nem befolyásolja a fürt funkciót. A telemetria csak is küld a csomópont, amely a feladatátvétel elsődleges kezelőben. Nincsenek más csomópontok telemetriai adatok küldése.
2. A telemetriát a következőkből áll:

* Szolgáltatások száma
* ServiceTypes száma
* Alkalmazásokk száma
* ApplicationUpgrades száma
* Feladatátvételi egységek száma
* Inbuild feladatátvételi egységek száma
* UnhealthyFailoverUnits száma
* Replikák száma
* InBuildReplicas száma
* StandByReplicas száma
* OfflineReplicas száma
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* A csomópontok száma
* IsContextComplete: Igaz/hamis
* ClusterId: Ez az, ha mindegyik fürthöz véletlenszerűen előállított GUID
* ServiceFabricVersion
* IP-címet a virtuális gép és a gép, amelyről a telemetriai adatok feltöltése

Telemetria letiltásához adja hozzá a következőt *tulajdonságok* a fürt config: *enableTelemetry: hamis*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Ebben a csomagban szereplő előzetes verziójú funkciók
Nincs.


> [!NOTE]
> Az új kezdve [általánosan elérhető verziója a Windows Server különálló fürt (verzió 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), manuálisan vagy automatikusan is frissítse a fürtöt a jövőbeli kiadások. Tekintse meg [egy önálló Service Fabric-fürt verziója frissítés](service-fabric-cluster-upgrade-windows-server.md) dokumentum részleteiről.
> 
> 

## <a name="next-steps"></a>További lépések
* [Üzembe helyezése és távolíthat el alkalmazásokat a PowerShell használatával](service-fabric-deploy-remove-applications.md)
* [Különálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)
* [Hozzáadása vagy eltávolítása, csomópontok önálló Service Fabric-fürt](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Egy önálló Service Fabric-fürt verziója frissítése](service-fabric-cluster-upgrade-windows-server.md)
* [Önálló Service Fabric-fürt létrehozása Windows rendszert futtató Azure virtuális gépekkel](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Különálló fürt védelme a a Windows, Windows-rendszerbiztonság használatával](service-fabric-windows-cluster-windows-security.md)
* [Különálló fürt védelme a Windows X509 használ a tanúsítványok](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png