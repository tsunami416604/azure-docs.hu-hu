---
title: Service Fabric Azure fájlok kötet illesztőprogram (előzetes verzió) |} Microsoft Docs
description: A Service Fabric támogatja az Azure-fájlok használata a kötetek biztonsági mentése a tárolóból. Ez a funkció jelenleg előzetes verzióban érhetők.
services: service-fabric
documentationcenter: other
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/30/2018
ms.author: subramar
ms.openlocfilehash: 2d98cff1a5869091aa81097bbb34da6e525a2ad5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Service Fabric az Azure Files kötet illesztőprogram (előzetes verzió)
Az Azure-fájlok a kötet beépülő egy [Docker kötet beépülő modul](https://docs.docker.com/engine/extend/plugins_volume/) biztosít [Azure fájlok](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) Docker-tároló kötetek alapján. A Docker kötet beépülő modul, a Service Fabric-alkalmazás, amely a Service Fabric-fürtök telepíthető lesz csomagolva. Az célja Azure fájlok alapú más Service Fabric tároló központilag telepített alkalmazások kerülnek a fürthöz tartozó kötetek.

> [!NOTE]
> 6.255.389.9494 az Azure-fájlok kötet beépülő modul verziószáma egy előzetes kiadásaiban elérhető ez a dokumentum. Egy előzetes, mint az **nem** éles környezetben használható.
>

## <a name="prerequisites"></a>Előfeltételek
* Az Azure-fájlok kötet beépülő modul a Windows-verzión működik-e az [Windows Server verzió 1709](https://docs.microsoft.com/en-us/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10-es verzió 1709](https://docs.microsoft.com/en-us/windows/whats-new/whats-new-windows-10-version-1709) vagy annál újabb operációs rendszerek csak. Az Azure-fájlok kötet beépülő modul a Linux-verziójának működik-e az összes, a Service Fabric által támogatott operációsrendszer-verziók.

* Kövesse az utasításokat a [Azure fájlok dokumentáció](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-create-file-share) a Service Fabric-tároló alkalmazás kötetként használandó fájlmegosztás létrehozásához.

* Szüksége lesz [a Service Fabric modul Powershell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started) vagy [SFCTL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli) telepítve.

## <a name="deploy-the-service-fabric-azure-files-application"></a>A Service Fabric Azure fájlok alkalmazás központi telepítése

A Service Fabric-alkalmazás, amely a kötetek biztosít a tárolók letölthető a következő [hivatkozás](https://aka.ms/sfvolume). Az alkalmazás telepíthető legyen a fürt keresztül [PowerShell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-lifecycle-sfctl) vagy [FabricClient API-k](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. A parancssor használatával módosítsa a könyvtárat a letöltött alkalmazáscsomag gyökérkönyvtárában. 

    ```powershell 
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Másolja az alkalmazáscsomag az image store futtassa az alábbi parancsot a megfelelő értéket [ApplicationPackagePath] és [előtaggal]:

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

4. Az alkalmazás létrehozása az alábbi alkalmazás létrehozása, vegye figyelembe a parancsban a **ListenPort** alkalmazás paraméter. Az alkalmazás paraméterhez megadott érték a port, amelyen az Azure-fájlok kötet beépülő modul a Docker démon a érkező kéréseket figyeli. Fontos győződjön meg arról, hogy a portot, az alkalmazás számára biztosított nem ütköznek-e bármilyen más portot, amelynek használatával a fürt vagy az alkalmazásokat.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter nem támogatja a leképezést SMB csatlakoztatások tárolókhoz ([, amely csak a Windows Server verzió 1709 támogatott](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Ennél a határértéknél megakadályozza, hogy a kötet hálózatleképezés és Azure fájlok kötet illesztőprogramok 1709 régebbi verzióin. 
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>A helyi fejlesztési fürtök a az alkalmazás központi telepítése
Az Azure-fájlok kötet beépülő modul alkalmazás alapértelmezett szolgáltatás példányok száma -1, ami azt jelenti, hogy nincs-e a szolgáltatás, a fürt minden csomópontjára telepített példánya. Azonban a helyi fejlesztési fürtök Azure fájlok kötet beépülő modul alkalmazás telepítésekor a szolgáltatás példányszámot kell megadni: 1. Ez keresztül végezhető a **InstanceCount** alkalmazás paraméter. Ezért, a parancsot a Azure fájlok kötet beépülő modul alkalmazás a helyi fejlesztési fürtök telepítése:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>A kötetet használja az alkalmazások konfigurálása
Az alábbi kódrészletben láthatja, hogyan adható meg az Azure-fájlokat alapján köteten az alkalmazás az alkalmazás jegyzékében. A megadott egyik fontos eleme a **kötet** címke:

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

Az illesztőprogram neve az Azure-fájlok kötet beépülő modul **sfazurefile**. Ezen érték beállítása esetén az a **illesztőprogram** attribútuma a **kötet** elem található az alkalmazás jegyzékében.

Az a **kötet** a fentiek kódrészletet az Azure-fájlok kötet beépülő modul az elem szükséges a következő címkékkel: 
- **Forrás** – amely lehet egy mappa a virtuális gépen, amelyen a tárolók vagy egy állandó távoli tároló mappát ide
- **Cél** -ezt a címkét az a hely, amely a **forrás** futó tárolóban van leképezve. Ebből kifolyólag a cél nem lehet egy olyan helyre, a tárolóban már létezik

Ahogy az a **DriverOption** elemeinek a fentiek kódrészletet az Azure-fájlok kötet beépülő modul támogatja a következő illesztőprogram-beállítások:

- **Megosztásnév** -a fájlok Azure fájlmegosztás, amely a kötet biztosít a tároló neve
- **storageAccountName** - neve, az Azure storage-fiók, amely tartalmazza az Azure-fájlok fájl megosztása
- **storageAccountKey** -hozzáférési kulcsot az Azure storage-fiók, amely tartalmazza az Azure-fájlok fájlmegosztás

A fenti illesztőprogram lehetőségek mindegyike szükséges. 

## <a name="using-your-own-volume-or-logging-driver"></a>A saját kötet vagy a naplózás illesztőprogram
A Service Fabric azt is lehetővé teszi, hogy a saját egyéni kötetet és naplózás-illesztőprogramok használata. Ha a Docker kötet/naplózási illesztőprogram nincs telepítve a fürtön, telepítheti azt manuálisan az RDP/SSH protokoll használatával. A telepítés ezen protokollok használatával végezheti el a [virtuálisgép-méretezési csoport indítási parancsfájl](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) vagy egy [SetupEntryPoint parancsfájl](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

A parancsfájl telepítéséhez például a [Docker kötet illesztőprogram az Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) a következőképpen történik:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Az alkalmazások, a kötet és a naplózás illesztőprogram telepítette, akkor kell adja meg a megfelelő értékeket a **kötet** és **LogConfig** alá elemek  **ContainerHostPolicies** az alkalmazásjegyzékben. 

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

## <a name="next-steps"></a>További lépések
* Tekintse meg a tároló minták, beleértve a kötet illesztőprogram, látogasson el a [Service Fabric tároló minták](https://github.com/Azure-Samples/service-fabric-containers)
* A tárolók a Service Fabric-fürt üzembe helyezése, tekintse meg a cikk [üzembe egy tárolót a Service Fabric](service-fabric-deploy-container.md)


