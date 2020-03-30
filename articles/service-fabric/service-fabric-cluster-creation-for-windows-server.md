---
title: Önálló Azure Service Fabric-fürt létrehozása
description: Hozzon létre egy Azure Service Fabric-fürtöt bármely Windows Server rendszert futtató gépen ( fizikai vagy virtuális) , legyen az helyszíni vagy felhőbeli.
author: dkkapur
ms.topic: conceptual
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 461d6021a201ca1fa5722bb44c427baca2a7728e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258823"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Windows Serveren futó különálló fürt létrehozása
Az Azure Service Fabric segítségével service fabric-fürtöket hozhat létre bármely virtuális gépen vagy Windows Server t futtató számítógépen. Ez azt jelenti, hogy a Service Fabric-alkalmazásokat bármilyen környezetben telepítheti és futtathatja, amely összekapcsolt Windows Server-számítógépekkészletét tartalmazza, legyen az a helyszínen vagy bármely felhőszolgáltatóval. A Service Fabric telepítőcsomagot biztosít az önálló Windows Server-csomagnak nevezett Service Fabric-fürtök létrehozásához. Az Azure-ban a hagyományos Service Fabric-fürtök felügyelt szolgáltatásként érhetők el, míg az önálló Service Fabric-fürtök önkiszolgálóak. A különbségekről az Azure és az [önálló Service Fabric-fürtök összehasonlítása](./service-fabric-deploy-anywhere.md)című témakörben nyújt további.

Ez a cikk bemutatja a Service Fabric önálló fürt létrehozásának lépéseit.

> [!NOTE]
> Ez az önálló Windows Server-csomag kereskedelmi forgalomban kapható, és éles környezetben is használható. Ez a csomag tartalmazhat új Service Fabric-funkciókat, amelyek az "előzetes verzió". Görgessen le a[csomagban található "Előnézeti funkciók" című témakörbe.](#previewfeatures_anchor) az előnézeti funkciók listájához. Most [már letöltheti a eula egy példányát.](https://go.microsoft.com/fwlink/?LinkID=733084)
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Támogatás a Windows Server Service Fabric csomaghoz
* Kérdezze meg a közösséget a Service Fabric önálló windows Server-csomagjáról az [Azure Service Fabric fórumon.](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?)
* Nyisson meg egy jegyet a [Service Fabric professzionális támogatásához.](https://support.microsoft.com/oas/default.aspx?prid=16146)  A Microsoft szakmai támogatásáról itt olvashat [bővebben.](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
* Ehhez a csomaghoz a [Microsoft Premier Támogatási Szolgálat](https://support.microsoft.com/en-us/premier)részeként is kaphat támogatást.
* További részletekért tekintse meg az [Azure Service Fabric támogatási lehetőségeit.](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)
* A támogatási célú naplók gyűjtéséhez futtassa a [Service Fabric önálló naplógyűjtőjét.](service-fabric-cluster-standalone-package-contents.md)

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>A Windows Serverhez készült Service Fabric-csomag letöltése
A fürt létrehozásához használja a Windows Server Service Fabric csomagot (Windows Server 2012 R2 és újabb) az itt található: <br>
[Letöltési hivatkozás – Service Fabric önálló csomag – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

A csomag tartalmával kapcsolatos részletek [itt találhatók.](service-fabric-cluster-standalone-package-contents.md)

A Service Fabric futásidejű csomag automatikusan letöltődik a fürt létrehozásakor. Ha olyan gépről telepít, amely nem csatlakozik az internethez, töltse le innen a sávon kívüli futásidejű csomagot: <br>
[Letöltési hivatkozás – Service Fabric futásidő – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Önálló fürtkonfigurációs minták keresése a következő helyen: <br>
[Önálló fürtkonfigurációs minták](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>A fürt létrehozása
Több fürtkonfigurációs mintafájl is települ a telepítőcsomaggal. A *ClusterConfig.Unsecure.DevCluster.json* a legegyszerűbb fürtkonfiguráció: egy nem védett, egyetlen számítógépen futó, három csomóponttal rendelkező fürt.  A többi konfigurációs fájl X.509 tanúsítványokkal vagy a Windows biztonsági szolgáltatásaival védett egy- vagy többgépes fürtöket ír le.  Ehhez az oktatóanyaghoz az alapértelmezett konfigurációs beállítások egyikét sem kell módosítania, csak át kell tekintenie a konfigurációs fájlt, és meg kell ismerkednie a beállításokkal.  A **csomópontok** szakasz ismerteti a fürt három csomópontját: név, IP-cím, [csomóponttípus, tartalék tartomány és frissítési tartomány](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  A **tulajdonságok** szakasz definiálja a fürt [biztonságát, megbízhatósági szintjét, diagnosztikai gyűjteményét és a benne lévő csomópontok típusait](service-fabric-cluster-manifest.md#cluster-properties).

A cikkben létrehozott fürt nem biztonságos.  Bárki csatlakozhat hozzá névtelenül és végrehajthat kezelési műveleteket, ezért az üzemben lévő fürtöket mindig X.509 tanúsítványok vagy a Windows rendszerbiztonság használatával kell védeni.  A biztonság konfigurálására csak a fürt létrehozásakor van lehetőség, a fürt létrehozása után már nem lehet engedélyezni. Frissítse a konfigurációs fájlt, amely engedélyezi [a tanúsítványok vagy a](service-fabric-windows-cluster-x509-security.md) Windows [biztonságát.](service-fabric-windows-cluster-windows-security.md) A Service Fabric-fürtök védelmével kapcsolatos további tudnivalókért lásd: [Fürt biztonságossá tétele](service-fabric-cluster-security.md).

### <a name="step-1-create-the-cluster"></a>1. lépés: A fürt létrehozása

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>A. forgatókönyv: Nem biztonságos helyi fejlesztési fürt létrehozása
A Service Fabric telepíthető egy gépes fejlesztői fürtre a [Samplesben](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)található *ClusterConfig.Unsecure.DevCluster.json* fájl használatával.

Csomagolja ki az önálló csomagot a számítógépre, másolja a minta konfigurációs fájlt a helyi számítógépre, majd futtassa a *CreateServiceFabric.ps1* parancsfájlt egy rendszergazdai PowerShell-munkameneten keresztül az önálló csomagmappából.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Tekintse meg a Környezet beállítása szakaszt a Terv című [témakörben, és készítse elő a fürt telepítését](service-fabric-cluster-standalone-deployment-preparation.md) a hibaelhárításrészleteire.

Ha befejezte a fejlesztési forgatókönyvek futtatását, eltávolíthatja a Service Fabric-fürtöt a gépről az "[Fürt eltávolítása](#removecluster_anchor)" című szakaszban leírt lépésekre hivatkozva. 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>B. forgatókönyv: Többgépes fürt létrehozása
Miután végigment a tervezés és előkészítés a tervezés és a [fürt telepítésének előkészítése](service-fabric-cluster-standalone-deployment-preparation.md)című tervezési és előkészítési lépéseken, készen áll az éles fürt létrehozására a fürt konfigurációs fájlja használatával.

A fürt üzembe helyezését és konfigurálását végző fürtrendszergazdának rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen. A Service Fabric tartományvezérlőn nem telepíthető.

1. Az önálló csomagban lévő *TestConfiguration.ps1* szkript az ajánlott eljárásokat elemző eszközként használható annak ellenőrzésére, hogy egy fürt az adott környezetben üzembe helyezhető-e. Az [Üzembe helyezés előkészítése](service-fabric-cluster-standalone-deployment-preparation.md) az előfeltételeket és a környezeti követelményeket sorolja fel. A szkript futtatásával ellenőrizze, hogy létre tudja-e hozni a fejlesztési fürtöt:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Az alábbihoz hasonló kimenetnek kell megjelennie. Ha az "Átadva" alsó mező "True" értéket ad vissza, a józansági ellenőrzések átmentek, és a fürt a bemeneti konfiguráció alapján telepíthetőnak tűnik.

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

2. Hozza létre a fürtöt: Futtassa a *CreateServiceFabricCluster.ps1* parancsfájlt a Service Fabric fürt telepítéséhez a konfiguráció minden egyes gépén. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Az üzembe helyezés nyomait arra a gépre/virtuális gépre írja a rendszer, amelyen a CreateServiceFabricCluster.ps1 PowerShell-szkriptet futtatta. Ezek a DeploymentTraces nevű almappában találhatók, azon könyvtár alapján, amelyből a szkriptet futtatta. Ha ellenőrizni kívánja, hogy a Service Fabric megfelelően lett-e üzembe helyezve a gépen, keresse meg a telepített fájlokat a FabricDataRoot könyvtárban, ahogy az a fürtkonfigurációs fájl FabricSettings szakaszában szerepel (alapértelmezés szerint ez a c:\ProgramData\SF). A FabricHost.exe és a Fabric.exe folyamat futása is látható a Feladatkezelőben.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>C forgatókönyv: Kapcsolat nélküli (internetkapcsolat talanított) fürt létrehozása
A Service Fabric futásidejű csomag automatikusan letöltődik a fürt létrehozásakor. Fürt telepítésekor az internethez nem kapcsolódó gépekre, külön kell letöltenie a Service Fabric futásidejű csomagot, és meg kell adnia az elérési utat a fürt létrehozásakor.
A futásidejű csomag külön tölthető le, egy másik, az internethez csatlakoztatott gépről, a [Download Link - Service Fabric Runtime - Windows Server rendszerben.](https://go.microsoft.com/fwlink/?linkid=839354) Másolja a futásidejű csomagot oda, ahonnan az offline fürtöt `CreateServiceFabricCluster.ps1` telepíti, és hozza létre a fürtöt a mellékelt paraméterrel való futtatással, amint `-FabricRuntimePackagePath` az a jelen példában látható: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* és *.\MicrosoftAzureServiceFabric.cab* a fürtkonfiguráció és a futásidejű .cab fájl elérési útjai.

### <a name="step-2-connect-to-the-cluster"></a>2. lépés: Csatlakozás a fürthöz
Csatlakozzon a fürthöz, és ellenőrizze, hogy a fürt fut-e és elérhető-e. A ServiceFabric PowerShell-modul a futtatókörnyezettel együtt települ.  Csatlakozhat a fürthöz az egyik fürtcsomópontról vagy egy távoli számítógépről a Service Fabric futásidejű.  A [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag kiépít egy kapcsolatot a fürttel.

Ha nem biztonságos fürthöz szeretne csatlakozni, futtassa a következő PowerShell-parancsot:

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

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>3. lépés: A fürt megjelenítése a Service Fabric-kezelő vel
A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) hatékony eszköz a fürtök megjelenítéséhez és az alkalmazások kezeléséhez.  A Service Fabric Explorer egy olyan szolgáltatás, amely a fürtben [http://localhost:19080/Explorer](http://localhost:19080/Explorer)fut, amelyet a böngésző segítségével érhet el a .

A fürt irányítópultja áttekintést nyújt a fürtről, beleértve az alkalmazások és a csomópontok állapotának összefoglalását. A csomópontnézet a fürt fizikai elrendezését mutatja. Az egyes csomópontoknál megtekintheti, hogy melyik alkalmazások kódja üzemel az adott csomóponton.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Csomópontok hozzáadása és eltávolítása
Az üzleti igényei változásával hozzáadhat vagy eltávolíthat csomópontokat az önálló Service Fabric-fürtről. A lépések részletes leírása: [Csomópontok hozzáadása vagy eltávolítása egy önálló Service Fabric-fürtről](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Fürt eltávolítása
Egy fürt eltávolításához futtassa a *RemoveServiceFabricCluster.ps1* PowerShell-szkriptet a csomag mappájából, és adja meg a JSON-konfigurációs fájl elérési útját. Megadhatja a törlés naplójának mentési helyét is.

Ez a parancsfájl bármely olyan számítógépen futtatható, amely rendszergazdai hozzáféréssel rendelkezik a fürt konfigurációs fájljában csomópontként felsorolt összes géphez. A parancsfájl futtatásához szükséges gépnek nem kell a fürt részét vennie.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Az összegyűjtött telemetriai adatok és azok letiltása
Alapértelmezés szerint a termék telemetriai adatokat gyűjt a Service Fabric-használat a termék javítása érdekében. A telepítő részeként futó ajánlott eljárások atalizátor ellenőrzi a kapcsolatot. [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) Ha nem érhető el, a telepítés sikertelen lesz, kivéve, ha leiratkozik a telemetriai adatokról.

1. A telemetriai folyamat naponta [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) egyszer próbálja feltölteni a következő adatokat. Ez egy legjobb feltöltés, és nincs hatással a fürt működését. A telemetriai adatokat csak a feladatátvevő kezelő elsődleges futó csomópontról küldi el a rendszer. Más csomópontok nem küldenek telemetriai adatokat.
2. A telemetria a következőkből áll:

* Szolgáltatások száma
* ServiceTypes száma
* Alkalmazások száma
* Alkalmazásfrissítések száma
* Feladatátvevő egységek száma
* InBuildFailoverUnits száma
* Nem megfelelő failoveregységek száma
* Replikák száma
* InBuildReplicas-ok száma
* StandByReplicas száma
* Kapcsolat nélküli kópiák száma
* CommonQueueLength (Közös várólistahossza)
* QueryQueueLength
* Feladat-egység– Várólistahossza
* CommitQueueLength
* Csomópontok száma
* IsContextComplete: Igaz/Hamis
* Fürtazonosító: Ez egy véletlenszerűen generált GUID az egyes fürtökhöz
* ServiceFabricVersion
* Annak a virtuális gépnek vagy gépnek az IP-címe, amelyről a telemetriai adatok at feltöltik

A telemetriai adatok letiltásához adja hozzá a *következőt* a fürt konfigurációs tulajdonságaihoz: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>A csomag előzetes verzióinak funkciói
Nincs.


> [!NOTE]
> A Windows [Server önálló fürtjének új ga-verziójától (5.3.204.x verzió)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)kezdve a fürt manuálisan vagy automatikusan frissíthető a jövőbeli kiadásokra. A részleteket [tekintse meg egy önálló Service Fabric-fürt verziódokumentum frissítése](service-fabric-cluster-upgrade-windows-server.md) című dokumentumban.
> 
> 

## <a name="next-steps"></a>További lépések
* [Alkalmazások telepítése és eltávolítása a PowerShell használatával](service-fabric-deploy-remove-applications.md)
* [Az önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)
* [Csomópontok hozzáadása vagy eltávolítása önálló Service Fabric-fürthöz](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Önálló Service Fabric-fürtverzió frissítése](service-fabric-cluster-upgrade-windows-server.md)
* [Önálló Service Fabric-fürt létrehozása Windows tfuttatóAzure-beli virtuális gépekkel](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Önálló fürt biztonságossá tétele windowsos rendszeren a Windows biztonsága érdekében](service-fabric-windows-cluster-windows-security.md)
* [Önálló fürt biztonságossá tétele Windows rendszeren X509-tanúsítványokkal](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
