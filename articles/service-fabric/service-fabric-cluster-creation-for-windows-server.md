---
title: "Hozzon létre egy önálló Azure Service Fabric-fürt |} Microsoft Docs"
description: "Az Azure Service Fabric-fürt létrehozása (fizikai vagy virtuális) bármely gépen Windows Server rendszert futtató, hogy helyszíni-e, vagy a felhőben."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: chackdan;maburlik;dekapur
ms.openlocfilehash: 6aa2905a97ec6b8c125f2ab9572a8e40bf525b27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>A Windows Server rendszert futtató önálló fürt létrehozása
Azure Service Fabric használatával Service Fabric-fürtök létrehozása a virtuális gépek vagy a Windows Server rendszerű számítógépek. Ez azt jelenti, telepítése és a Service Fabric-alkalmazások futtatása bármely összekapcsolt Windows Server számítógépek tartalmazó környezetben is kell azt a helyszíni vagy bármely felhőalapú szolgáltatóhoz. A Service Fabric biztosít a telepítési csomagot a különálló Windows Server csomag nevű Service Fabric-fürtök létrehozása.

Ez a cikk végigvezeti a különálló Service Fabric-fürt létrehozásának lépései.

> [!NOTE]
> Csomag különálló Windows Server kereskedelmi forgalomban elérhető, és az üzemi környezetek használható. Ez a csomag a Service Fabric funkciói "Előzetes verziójú" tartalmazhat. Görgessen le a "[az előzetes funkciók a csomagban](#previewfeatures_anchor)." az előzetes verziójú funkciók a lista szakaszában. Is [töltse le a végfelhasználói licencszerződés](http://go.microsoft.com/fwlink/?LinkID=733084) most.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Segítségre van szüksége a Service Fabric Windows Server-csomag
* A Windows Server a Service Fabric önálló csomag a közösségi kérdezze meg a [Azure Service Fabric fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Nyissa meg a jegy [Professional támogatása a Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  További tudnivalók a Microsoft-támogatást Professional [Itt](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Is kaphat támogatási csomag részeként [Microsoft Premier támogatási](https://support.microsoft.com/en-us/premier).
* További részletekért lásd: [Azure Service Fabric támogatási lehetőségek](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support).
* A támogatáshoz gyűjtését, futtassa a [Service Fabric önálló naplógyűjtő](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>A Service Fabric Windows Server-csomag
A fürt létrehozásához használja a Service Fabric Windows Server-csomag (Windows Server 2012 R2 és újabb) itt található: <br>
[Töltse le a Windows Server - háló önálló szolgáltatáscsomag - hivatkozás](http://go.microsoft.com/fwlink/?LinkId=730690)

Részleteket a csomag tartalma található [Itt](service-fabric-cluster-standalone-package-contents.md).

A Service Fabric futásidejű csomag a fürt létrehozásakor automatikusan lett letöltve. Ha telepíti a gép nem csatlakozik az internethez, töltse le a sávon kívüli futásidejű csomag innen: <br>
[Töltse le a Windows Server - Service Fabric-futtatókörnyezet - hivatkozás](https://go.microsoft.com/fwlink/?linkid=839354)

Önálló fürtkonfiguráció minták keresése: <br>
[Önálló fürtkonfiguráció – minták](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>A fürt létrehozása
A Service Fabric használatával is telepíthető egy egy-gép fejlesztési fürtöt a *ClusterConfig.Unsecure.DevCluster.json* fájl [minták](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Bontsa ki az önálló csomagját a számítógépen, a minta konfigurációs fájlt a helyi számítógépre másolja, majd futtassa a *CreateServiceFabricCluster.ps1* parancsfájl használatával egy rendszergazda PowerShell-munkamenetet, a különálló csomag mappa :
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>1A. lépés: egy nem biztonságos helyi fejlesztési fürtök létrehozása
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Tekintse meg a környezetben való telepítés szakasz [tervezze meg és készítse elő a fürtöt tartalmazó környezetben](service-fabric-cluster-standalone-deployment-preparation.md) hibaelhárítási részleteket.

Ha végzett a futó fejlesztési forgatókönyvre, eltávolíthatja a Service Fabric-fürt a gépről azáltal szakaszban található lépéseket "[távolítsa el a fürt](#removecluster_anchor)". 

### <a name="step-1b-create-a-multi-machine-cluster"></a>1B. lépés: hozzon létre egy többgépes fürtben
Miután lezajlott, a tervezési és előkészítő lépések részletes a hivatkozáson, készen áll a éles fürt a fürt konfigurációs fájl segítségével létrehozásához. <br>
[Tervezze meg és készítse elő a fürtöt tartalmazó környezetben](service-fabric-cluster-standalone-deployment-preparation.md)

1. A konfigurációs fájl futtatásával írt érvényesítése a *TestConfiguration.ps1* parancsfájl az önálló csomag mappából:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Kimenetet kell látnia, például alatt. Ha a "Átadott" adja vissza a rendszer "True", megerősítések átadott alsó mező és a fürt kell központilag telepíthető a bemeneti konfiguráció alapján.

    ```
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

2. A fürt létrehozása: futtassa a *CreateServiceFabricCluster.ps1* parancsfájl központi telepítése a Service Fabric-fürt minden egyes gépet a konfigurációban teljes. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Központi telepítés nyomainak kerülnek a virtuális gép/gép a CreateServiceFabricCluster.ps1 PowerShell parancsfájl futtatásának. Ezek a könyvtárban, ahol a parancsfájl futtatásának alapú almappájában DeploymentTraces, találhatók. Tekintse meg, ha a Service Fabric megfelelően települt-e a gép, a telepített fájlkeresés a FabricDataRoot könyvtárban, ahogy az a fürt konfigurációs fájlban FabricSettings szakaszban (az alapértelmezett c:\ProgramData\SF). Valamint FabricHost.exe és Fabric.exe folyamatok látható, a Feladatkezelő futtató.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>1 c. lépés: kapcsolat nélküli (internet-kapcsolatát) fürt létrehozása
A Service Fabric-futtatókörnyezet csomag automatikusan letölti a fürt létrehozásakor. A fürt nem csatlakozik az internethez számítógépekre való telepítésekor szüksége lesz a Service Fabric-futtatókörnyezet csomag külön-külön letöltéséhez, és adja meg a fürt létrehozásakor a elérési utat.
A futásidejű csomag letölthető külön történik, az internethez csatlakozik egy másik gép [- Service Fabric-futtatókörnyezet - hivatkozás töltse le a Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Ha a kapcsolat nélküli fürtöt telepít, és futtassa a fürt létrehozása a futásidejű csomag másolása `CreateServiceFabricCluster.ps1` rendelkező a `-FabricRuntimePackagePath` paraméter szerepel, a lent látható módon: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```
Ha `.\ClusterConfig.json` és `.\MicrosoftAzureServiceFabric.cab` sebességnek felelnek meg a fürt konfigurálása és a futásidejű .cab fájl elérési útvonalát.


### <a name="step-2-connect-to-the-cluster"></a>2. lépés: Csatlakozzon a fürthöz
Szeretne csatlakozni egy biztonságos fürtöt, tekintse meg a [biztonságos fürt csatlakozni a Service fabric](service-fabric-connect-to-secure-cluster.md).

Csatlakozás nem biztonságos fürthöz, futtassa a következő PowerShell-parancsot:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```
Példa:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>3. lépés: Elindítani a Service Fabric Explorerrel
Most csatlakozhat a fürthöz, a Service Fabric Explorerrel vagy közvetlenül az egyik a gépek http://localhost:19080/Explorer/index.html vagy távolról http://<*IPAddressofaMachine*>: 19080/Explorer / index.html.

## <a name="add-and-remove-nodes"></a>Hozzáadása és eltávolítása, csomópontok
Adja hozzá, vagy távolítsa el a csomópontok a különálló Service Fabric-fürt számára, az üzleti igényeinek változását. Lásd: [különálló Service Fabric-fürt a csomópontok hozzáadásához és eltávolításához](service-fabric-cluster-windows-server-add-remove-nodes.md) a részletes lépéseket.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>A fürt eltávolítása
Egy fürt eltávolításához futtassa a *RemoveServiceFabricCluster.ps1* PowerShell-szkriptet a csomag mappájából, és adja meg a JSON-konfigurációs fájl elérési útját. Megadhatja a törlés naplójának mentési helyét is.

Ezt a parancsfájlt, amely csomópontként a fürt konfigurációs fájlban felsorolt minden gép rendszergazdai hozzáféréssel rendelkezik gépi. Az ezt a parancsfájlt futtató számítógép nem rendelkezik a fürt részeként.

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Az összegyűjtött telemetrikus adatok és tilthatják le, hogyan
Alapértelmezés szerint a termék telemetriai adatokat gyűjt a Service Fabric-használata a termék tökéletesítése érdekében. Az ajánlott eljárásokat elemző eszköz, amely fut, a telepítés részeként keresi a kapcsolatot a [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Ha nem érhető el, a telepítés sikertelen lesz, kivéve, ha kikapcsolja a telemetriai adatok.

1. A telemetria-feldolgozási folyamat megpróbálja feltölteni a következő adatok [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) naponta egyszer. A legjobb feltöltés, és nincs hatással van a fürt működését. A telemetriai adatok csak akkor lesz elküldve a csomópont, amelyen a feladatátvételi elsődleges kezelőben. Nincs más csomópontok küldött telemetriai adatokat.
2. A telemetriai adatokat a következőkből áll:

* Szolgáltatások száma
* ServiceTypes száma
* Alkalmazások száma
* ApplicationUpgrades száma
* Failoverunits egységek száma
* Található inbuildfailoverunits egységek száma
* UnhealthyFailoverUnits száma
* Replikák száma
* InBuildReplicas száma
* StandByReplicas száma
* OfflineReplicas száma
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Csomópontok száma
* IsContextComplete: Igaz/hamis értékű
* ClusterId: Ez az egyes fürtök véletlenszerűen előállított GUID
* ServiceFabricVersion
* A virtuális gép vagy a gép, amelyből a telemetriai adatainak feltöltése IP-címe

Telemetria letiltása, adja hozzá a következőt *tulajdonságok* a fürt config: *enableTelemetry: hamis*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Előzetes verziójú funkciók a csomagban
nincs.


> [!NOTE]
> Az új kezdve [GA verziójával a különálló fürt Windows Server (verzió 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), frissítheti a fürt későbbi kiadásokban manuálisan vagy automatikusan. Tekintse meg [frissítése egy különálló Service Fabric-fürt verziószáma](service-fabric-cluster-upgrade-windows-server.md) dokumentum.
> 
> 

## <a name="next-steps"></a>Következő lépések
* [Központi telepítése, és távolítsa el az alkalmazásokat a PowerShell használatával](service-fabric-deploy-remove-applications.md)
* [Önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)
* [Különálló Service Fabric-fürt a csomópontok hozzáadásához és eltávolításához](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Frissítse a különálló Service Fabric-fürt verziószáma](service-fabric-cluster-upgrade-windows-server.md)
* [Hozzon létre egy különálló Service Fabric-fürt Windowst futtató Azure virtuális gépeken](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Biztonságos Windows használja-e a Windows biztonsági önálló fürtben](service-fabric-windows-cluster-windows-security.md)
* [Biztonságos Windows X509 használata önálló fürtben, tanúsítványok](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
