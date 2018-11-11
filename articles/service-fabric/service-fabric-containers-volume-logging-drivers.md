---
title: Service Fabric Azure Files kötet illesztőprogram (előzetes verzió) |} A Microsoft Docs
description: Service Fabric támogatja kötetek biztonsági mentése Azure Files használatával a tárolóból. Ez jelenleg előzetes verzióban érhető el.
services: service-fabric
documentationcenter: other
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/10/2018
ms.author: twhitney, subramar
ms.openlocfilehash: fabb44f9369dd7b7050ae353ab94263f140aae48
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346405"
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Service Fabric az Azure Files kötet illesztőprogram (előzetes verzió)
Az Azure Files kötet beépülő modul van egy [Docker kötet beépülő modul](https://docs.docker.com/engine/extend/plugins_volume/) biztosít [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) köteteket a Docker-tárolók alapján. A Docker-kötet beépülő modult, a Service Fabric-alkalmazás telepítését követően a Service Fabric-fürtök van csomagolva. Ez azt szolgál az Azure Files-alapú más Service Fabric-tárolóalkalmazások a fürtön üzembe helyezett köteteket.

> [!NOTE]
> Az Azure Files kötet beépülő modul 6.4.571.9494 verziója egy előzetes kiadásban ez a dokumentum elérhető. Előzetes kiadás, mint a **nem** az éles környezetben használata támogatott.
>

## <a name="prerequisites"></a>Előfeltételek
* Az Azure Files kötet beépülő modul a Windows verziója működik [Windows Server 1709-es](https://docs.microsoft.com/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 1709-es](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) vagy annál újabb operációs rendszereken csak. Az Azure Files kötet beépülő modul Linux-verzióját a Service Fabric által támogatott összes operációsrendszer-verziók működik.

* Az Azure Files kötet beépülő modul csak a Service Fabric 6.2-es és újabb verzió működik.

* Kövesse az utasításokat a [dokumentáció az Azure Files](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) kötetként használata a Service Fabric tárolóalkalmazását a fájlmegosztás létrehozásához.

* Szüksége lesz [Powershell használata a Service Fabric modul](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) vagy [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) telepítve.

* Ha a Hyper-v-tárolókat használ, az alábbi kódrészletek hozzá kell adni a clustermanifest jegyzékben (helyi fürt) vagy az ARM-sablon (Azure-fürtön) vagy ClusterConfig.json (önálló fürt) fabricSettings szakaszát. A kötet nevét és a portot, amelyet a fürt figyeli a kötetet kell. 

A a clustermanifest jegyzékben, a következő hozzá kell adnia az üzemeltetési szakaszban. Ebben a példában a kötet neve a következő **sfazurefile** és a portot figyeli a fürtön, **19300**.  

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19300" />
</Section>
```

Az alábbi kódrészletet az ARM-sablon (az Azure-környezetekben) vagy (az önálló verziója telepítéseinek) ClusterConfig.json fabricSettings szakaszának, hozzá kell adnia. 

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19300"
      }
    ]
  }
]
```


## <a name="deploy-the-service-fabric-azure-files-application"></a>A Service Fabric Azure Files üzembe helyezése

A Service Fabric-alkalmazás, amely a tárolókat biztosít a kötetek letölthető a következő [hivatkozás](https://aka.ms/sfvolume6.4). Az alkalmazást is üzembe helyezhetők a fürtön keresztül [PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle-sfctl) vagy [FabricClient API-k](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. A parancssorból lépjen a letöltött alkalmazáscsomag gyökérkönyvtárában.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Másolja az alkalmazáscsomag a lemezképtároló, futtassa az alábbi parancsot a megfelelő értéket [ApplicationPackagePath] és [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Regisztrálja az alkalmazás típusát

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Az alkalmazás létrehozása a parancsban az alábbi alkalmazás létrehozása, vegye figyelembe a **ListenPort** parametr aplikace. Az alkalmazás paraméterhez megadott érték a portot, amelyen az Azure Files kötet beépülő modul a Docker-démon érkező kéréseket figyeli. Fontos győződjön meg arról, hogy az alkalmazás megadott porton nem ütközik bármely más port, amelyet a fürt vagy az alkalmazások használni.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> A Windows Server 2016 Datacenter nem támogatja a leképezést SMB csatlakoztatása tárolókkal ([, amely csak a Windows Server 1709-es verzió támogatott](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Ennél a határértéknél megakadályozza, hogy a kötet hálózatleképezés és az Azure Files kötet illesztőprogramokat 1709-nél régebbi verziók.
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>A helyi fejlesztési fürt üzembe helyezése
A szolgáltatás példányok alapértelmezett száma az Azure Files kötet beépülő modul alkalmazás:-1, ami azt jelenti, hogy nincs-e az egyes csomópontokhoz a fürtben üzembe helyezett szolgáltatás egy példányának. Azonban egy helyi fejlesztési fürtön az Azure Files kötet beépülő modul alkalmazás üzembe helyezésekor, a szolgáltatás példányszám kell megadni, 1. Ezt megteheti a **InstanceCount** parametr aplikace. Ezért a parancs egy helyi fejlesztési fürtön az Azure Files kötet beépülő modul alkalmazás üzembe helyezéséhez a következő:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Az alkalmazások használata a kötet konfigurálása
A következő kódrészlet azt mutatja be, hogyan adható meg az Azure Files-alapú köteten az alkalmazásjegyzékben, az alkalmazás. Az adott elem a lényeges a **kötet** címkét:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

Az illesztőprogram neve az Azure Files kötet beépülő modul **sfazurefile**. Ez az érték van beállítva a **illesztőprogram** attribútuma a **kötet** elem az alkalmazásjegyzékben.

Az a **kötet** a fenti kódrészletben az Azure Files kötet beépülő modul elemhez kötelező megadni a következő címkékkel:
- **Forrás** – a kötet neve. A felhasználó kiválaszthatja a kötet bármely nevét.
- **Cél** – Ez a címke a helyre, amely a kötet le van képezve a futó tároló belül. Ebből kifolyólag a cél nem lehet egy olyan helyre, a tároló már létezik

Ahogy az a **DriverOption** elemek a fenti kódrészletben az Azure Files kötet beépülő modul támogatja a következő illesztőprogram-beállítások:
- **shareName** – az Azure Files fájlmegosztás, amely a kötet biztosít a tároló nevét.
- **storageAccountName** - neve az Azure storage-fiók, amely tartalmazza az Azure Files-fájl megosztása.
- **storageAccountKey** – az Azure storage-fiókot, amely tartalmazza az Azure Files fájlmegosztás elérési kulcsát.
- **storageAccountFQDN** -tartománynév, a storage-fiókhoz társított. Ha storageAccountFQDN nincs megadva, az alapértelmezett suffix(.file.core.windows.net) segítségével a storageAccountName tartománynév lesz összeállítva.  

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

## <a name="using-your-own-volume-or-logging-driver"></a>A saját kötet vagy a naplózás-illesztő segítségével
A Service Fabric lehetővé teszi a saját egyéni használatát [kötet](https://docs.docker.com/engine/extend/plugins_volume/) vagy [naplózás](https://docs.docker.com/engine/admin/logging/overview/) illesztőprogramokat. Ha a fürtön a Docker-kötet/naplózási illesztőprogram nincs telepítve, telepítheti manuálisan az RDP/SSH-protokoll használatával. A telepít, és ezeket a protokollokat, keresztül hajthat végre egy [virtuálisgép-méretezési csoport indítási parancsfájl](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) vagy egy [SetupEntryPoint parancsfájl](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Telepítése a parancsfájl egy példát a [Azure Docker kötet illesztőprogram](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) a következő:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Az alkalmazások használata a kötet vagy a naplózás illesztőprogram telepítve van, meg kellene a megfelelő értékeket adja meg a **kötet** és **LogConfig** alá elemek  **ContainerHostPolicies** az alkalmazásjegyzékben.

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

A Service Fabric beépülő modul egy kötetet adjon meg, automatikusan létrehozza a kötet a megadott paraméterek használatával. A **forrás** a címke a **kötet** elem a kötet nevét és a **illesztőprogram** kód adja meg a kötet illesztőprogram beépülő modul. A **cél** címkét az a hely, amely a **forrás** belül a futó tárolóhoz van leképezve. Ebből kifolyólag a cél nem lehet egy olyan helyre, a tároló már létezik. Beállítások használatával adható meg a **DriverOption** címkézése az alábbiak szerint:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Alkalmazás paramétereket támogatja kötetek, ahogyan az előző fürtjegyzék kódrészlete (keressen `MyStorageVar` példát használja).

Ha egy Docker-napló illesztőprogram meg van adva, akkor ügynökök (vagy tárolók) kezelése a naplók a fürt üzembe helyezéséhez. A **DriverOption** adja meg a napló illesztőprogram-címke használható.

## <a name="next-steps"></a>További lépések
* A tároló mintákat, többek között a kötet illesztőprogram, tekintse meg a [Service Fabric-tárolóminták](https://github.com/Azure-Samples/service-fabric-containers)
* Tárolók üzembe helyezése a Service Fabric-fürtöt, tekintse meg a cikk [tároló üzembe helyezése a Service Fabric szolgáltatásban](service-fabric-deploy-container.md)
