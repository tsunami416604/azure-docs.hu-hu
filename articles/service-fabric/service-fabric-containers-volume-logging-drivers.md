---
title: Azure Files kötet-illesztőprogram a Service Fabric számára
description: A Service Fabric támogatja az Azure Files használatát a tárolókötetek biztonsági mentéséhez.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: 514a0cb12359d58e38ebc30ae12cdb277757f2b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750041"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Azure Files kötet-illesztőprogram a Service Fabric számára

Az Azure Files kötet-illesztőprogram egy [Docker kötet beépülő](https://docs.docker.com/engine/extend/plugins_volume/) modul, amely [az Azure Files](/azure/storage/files/storage-files-introduction) alapú kötetek Docker-tárolók. Service Fabric-alkalmazásként van csomagolva, amely egy Service Fabric-fürtre telepíthető, hogy köteteket biztosítson a fürtön belüli más Service Fabric tárolóalkalmazások számára.

> [!NOTE]
> Az Azure Files kötet beépülő modul 6.5.661.9590-es verziója általánosan elérhető.
>

## <a name="prerequisites"></a>Előfeltételek
* Az Azure Files kötet beépülő modul Windows-verziója csak [windows Server 1709-es](/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10-es](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) vagy újabb operációs rendszeren működik.

* Az Azure Files kötet beépülő modul Linux-verziója a Service Fabric által támogatott összes operációsrendszer-verzión működik.

* Az Azure Files kötet beépülő modul csak a Service Fabric 6.2-es és újabb verziója.

* Kövesse az [Azure Files dokumentációjában](/azure/storage/files/storage-how-to-create-file-share) található utasításokat, és hozzon létre egy fájlmegosztást a Service Fabric tárolóalkalmazás kötetként való használatához.

* Szüksége lesz [a Powershell a Service Fabric modul](/azure/service-fabric/service-fabric-get-started) vagy [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) telepítve.

* Ha Hyper-V-tárolókat használ, a következő kódrészleteket hozzá kell adni a ClusterManifest (helyi fürt) vagy fabricSettings szakaszban az Azure Resource Manager sablon (Azure cluster) vagy ClusterConfig.json (önálló fürt).

A ClusterManifest,a következő kell adni a Hosting szakaszban. Ebben a példában a kötet neve **sfazurefile,** a fürtön meghallgatott port pedig **19100.** Cserélje le őket a fürt höz megfelelő értékekre.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

A fabricSettings szakaszban az Azure Resource Manager sablon (az Azure-központi telepítések) vagy clusterconfig.json (önálló üzemelő példányok esetén) a következő kódrészletet kell hozzáadni. Ismét cserélje le a kötet nevét és a portértékeket a sajátjára.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Mintaalkalmazás központi telepítése a Service Fabric Azure Files kötet-illesztőprogramjával

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Az Azure Resource Manager használata a megadott Powershell-parancsfájlon keresztül (ajánlott)

Ha a fürt az Azure-ban található, azt javasoljuk, hogy alkalmazásokat telepíteni az Azure Resource Manager-alkalmazás erőforrás-modell használatával a könnyű használat és az infrastruktúra karbantartása a kód felé való elmozdulás érdekében. Ez a megközelítés szükségtelenné teszi az Azure Files kötet-illesztőprogram alkalmazásverziójának nyomon követését. Azt is lehetővé teszi, hogy minden egyes támogatott operációs rendszer hez külön Azure Resource Manager-sablonok karbantartása. A parancsfájl feltételezi, hogy az Azure Files alkalmazás legújabb verzióját telepíti, és az operációs rendszer típusának, a fürt-előfizetés-azonosítónak és az erőforráscsoportnak a paramétereit veszi igénybe. A parancsfájl letölthető a [Service Fabric letöltési webhelyéről.](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip) Vegye figyelembe, hogy ez automatikusan beállítja a ListenPort, amely az a port, amelyen az Azure Files kötet beépülő figyel a Docker démon kéréseit, 19100-ra. Módosíthatja a "listenPort" nevű paraméter hozzáadásával. Győződjön meg arról, hogy a port nem ütközik a fürt vagy az alkalmazások által használt más portokkal.
 

Az Azure Resource Manager központi telepítésének parancsa a Windows rendszerhez:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Az Azure Resource Manager Linuxra vonatkozó telepítési parancsa:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Miután sikeresen futtatta a parancsfájlt, átugorhatja az [alkalmazás konfigurálása szakaszra.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Önálló fürtök manuális üzembe helyezése

A Service Fabric alkalmazás, amely a tárolók kötetek letölthető a [Service Fabric letöltési helyről.](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip) Az alkalmazás a [PowerShell](./service-fabric-deploy-remove-applications.md), [CLI](./service-fabric-application-lifecycle-sfctl.md) vagy [FabricClient API-k](./service-fabric-deploy-remove-applications-fabricclient.md)segítségével telepíthető a fürtre.

1. A parancssorból módosítsa a könyvtárat a letöltött alkalmazáscsomag gyökérkönyvtárára.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Ezután másolja az alkalmazáscsomagot a lemezképtárolóba a megfelelő értékekkel az [ApplicationPackagePath] és a [ImageStoreConnectionString] számára:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Az alkalmazástípus regisztrálása

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Hozza létre az alkalmazást, különös figyelmet fordítva a **ListenPort** alkalmazás paraméter értékére. Ez az érték az a port, amelyen az Azure Files kötet beépülő modul figyeli a Docker démon tól érkező kérelmeket. Győződjön meg arról, hogy az alkalmazásnak biztosított port megegyezik a ClusterManifest VolumePluginPorts portjával, és nem ütközik a fürt vagy az alkalmazások által használt más portokkal.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> A Windows Server 2016 Datacenter nem támogatja az SMB-csatlakoztatások hozzárendelését a tárolókhoz[(Ez csak a Windows Server 1709-es verziójában támogatott](/virtualization/windowscontainers/manage-containers/container-storage)). Ez a megkötés megakadályozza a hálózati kötetek leképezését és az Azure Files kötet-illesztőprogramokat az 1709-nél régebbi verziókon.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Az alkalmazás központi telepítése helyi fejlesztési fürtön
Kövesse [az](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters) 1-3.

 Az Azure Files kötetbeépülő alkalmazás alapértelmezett szolgáltatáspéldányszáma -1, ami azt jelenti, hogy a fürt minden egyes csomópontjára telepített szolgáltatás példánya telepítve van. Azonban az Azure Files kötet beépülő alkalmazás helyi fejlesztési fürtön történő telepítésekor a szolgáltatáspéldányok számát 1-ként kell megadni. Ez a **InstanceCount** alkalmazás paraméteren keresztül végezhető el. Ezért az Azure Files kötetbeépülő alkalmazás helyi fejlesztési fürtön való létrehozásának parancsa a következő:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Az alkalmazások konfigurálása a kötet használatára
A következő kódrészlet bemutatja, hogyan adható meg egy Azure-fájlalapú kötet az alkalmazás jegyzékfájljában. Az adott elem érdekes a **Kötet** címke:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Az Azure Files kötetbővítmény illesztőprogram-neve **sfazurefile**. Ez az érték az **alkalmazásjegyzékBen** lévő Volume tag elem **Illesztőprogram** attribútumához van beállítva.

A **kötet** címke a kódrészlet felett, az Azure Files kötet beépülő modul megköveteli a következő attribútumokat:
- **Forrás** - Ez a kötet neve. A felhasználó bármilyen nevet választhat a kötetéhez.
- **Cél** – Ez az attribútum az a hely, amelyhez a kötet le van képezve a futó tárolóban. Így az úti cél nem lehet olyan hely, amely már létezik a tárolóban

Amint az a fenti kódrészlet **DriverOption** elemeiben látható, az Azure Files kötet beépülő modul a következő illesztőprogram-beállításokat támogatja:
- **shareName** - Az Azure Files fájlmegosztás neve, amely a tároló kötetét biztosítja.
- **storageAccountName** – Az Azure Files fájlmegosztást tartalmazó Azure storage-fiók neve.
- **storageAccountKey** – Access kulcs az Azure-tárfiók, amely tartalmazza az Azure Files fájlmegosztást.
- **storageAccountFQDN** – a tárfiókhoz társított tartománynév. Ha a storageAccountFQDN nincs megadva, a tartománynév az alapértelmezett utótaggal(.file.core.windows.net) jön létre a storageAccountName értékkel.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Saját kötet vagy naplózási illesztőprogram használata
A Service Fabric lehetővé teszi a saját egyéni [kötet](https://docs.docker.com/engine/extend/plugins_volume/) vagy [naplózásillesztőprogramok](https://docs.docker.com/engine/admin/logging/overview/) használatát is. Ha a Docker kötet/naplózási illesztőprogram nincs telepítve a fürtre, manuálisan telepítheti az RDP/SSH protokollok használatával. A telepítést ezekkel a protokollokkal egy [virtuálisgép-méretezési készletes indítási parancsfájlvagy](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) [egy SetupEntryPoint parancsfájl](/azure/service-fabric/service-fabric-application-model)segítségével hajthatja végre.

Egy példa a parancsfájl t telepíteni a [Docker kötet-illesztőprogram az Azure-ban](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) a következő:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Az alkalmazásokban a telepített kötet- vagy naplózási illesztőprogram használatához meg kell **adnia** a megfelelő értékeket a Kötet és **a LogConfig** elemekben az alkalmazásjegyzék **ContainerHostPolicies** elemében.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

Kötet beépülő modul megadásakor a Service Fabric automatikusan létrehozza a kötetet a megadott paraméterek használatával. A **Kötet** elem **Forráscímkéje** a kötet neve, az **Illesztőprogram-címke** pedig a kötetillesztőprogram beépülő modulját adja meg. A **célcímke** az a hely, amelyhez a **forrás** le van képezve a futó tárolóban. Így az úti cél nem lehet olyan hely, amely már létezik a tárolóban. A beállítások a **DriverOption** címkével a következőképpen adhatók meg:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Alkalmazásparaméterek támogatottak a kötetek, ahogy az előző `MyStorageVar` jegyzékkódrészlet (keressen egy példa használata).

Ha meg van adva egy Docker-napló-illesztőprogram, telepítenie kell az ügynököket (vagy tárolókat) a fürtben lévő naplók kezeléséhez. A **DriverOption** címke segítségével megadhatja a naplóillesztő beállításait.

## <a name="next-steps"></a>További lépések
* A tárolóminták megtekintéséhez, beleértve a kötet-illesztőprogramot is, látogasson el a [Service Fabric-tárolómintákra](https://github.com/Azure-Samples/service-fabric-containers)
* Tárolók üzembe helyezéséhez egy Service Fabric-fürtre, olvassa el a cikk [egy tároló üzembe helyezése a Service Fabric](service-fabric-deploy-container.md)
