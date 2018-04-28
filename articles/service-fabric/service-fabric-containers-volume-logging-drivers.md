---
title: Az Azure Service Fabric Docker Compose (előzetes verzió) |} Microsoft Docs
description: Az Azure Service Fabric a Docker Compose formátum használatával kell levezényelni a meglévő tárolók Service Fabric használatával könnyebben fogad el. Támogatja a Docker Compose jelenleg előzetes verzió.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 72ff8e99453d3067492d50c3e35396dba9f23c12
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>Docker kötet beépülő modulok és naplózás-illesztőprogramok a tárolóban
Az Azure Service Fabric támogatja megadó [Docker kötet beépülő modulok](https://docs.docker.com/engine/extend/plugins_volume/) és [Docker naplózási illesztőprogramok](https://docs.docker.com/engine/admin/logging/overview/) a tárolószolgáltatás számára. Akkor is megmaradnak az adatok [Azure fájlok](https://azure.microsoft.com/services/storage/files/) Ha a tároló áthelyezése vagy egy másik állomás újraindul.

Linux-tárolók kötet illesztőprogramok jelenleg támogatott. Windows tárolók használata, leképezheti a kötetet egy Azure-fájlok [SMB3 megosztás](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) kötet illesztőprogram nélkül. Ez a leképezés, frissítse a virtuális gépek (VM) a fürt Windows Server 1709 legújabb verziójára.


## <a name="install-the-docker-volumelogging-driver"></a>A Docker kötet/naplózási illesztőprogram telepítése

Ha a Docker kötet/naplózási illesztőprogram nincs telepítve a számítógépen, telepítheti azt manuálisan az RDP/SSH protokoll használatával. A telepítés ezen protokollok használatával végezheti el a [virtuálisgép-méretezési csoport indítási parancsfájl](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) vagy egy [SetupEntryPoint parancsfájl](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

A parancsfájl telepítéséhez például a [Docker kötet illesztőprogram az Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) a következőképpen történik:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

> [!NOTE]
> Windows Server 2016 Datacenter nem támogatja a leképezést SMB csatlakoztatások tárolókhoz ([, amely csak a Windows Server verzió 1709 támogatott](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Ennél a határértéknél megakadályozza, hogy a kötet hálózatleképezés és Azure fájlok kötet illesztőprogramok 1709 régebbi verzióin. 
>   


## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>Adja meg a beépülő modul vagy illesztőprogram a jegyzékben
A beépülő modulok vannak megadva az alkalmazásjegyzékben az alábbiak szerint:

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
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
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

A **forrás** a címke a **kötet** elem a forrás mappára hivatkozik. A forrásmappa lehet a virtuális gépen, amelyen a tárolók vagy egy állandó távoli tároló mappa. A **cél** címke az a hely, amely a **forrás** futó tárolóban van leképezve. Így az a cél nem lehet egy helyet, amelyen belül a tároló már létezik.

Alkalmazás paraméterek a kötetek esetében támogatottak, ahogy az az előző jegyzék részlet (keressen `MyStoreVar` például használja).

A beépülő modul kötet megadásakor a Service Fabric automatikusan a megadott paraméterek használatával hozza létre a kötetet. A **forrás** címke pedig a kötet neve és a **illesztőprogram** kód adja meg a kötet illesztőprogram beépülő modul. Beállítások használatával adható meg a **DriverOption** tag az alábbiak szerint:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Ha egy Docker-napló illesztőprogram meg van adva, akkor telepítése ügynökök (vagy a tárolókat) a naplófájlok kezelésére a fürtben. A **DriverOption** címke segítségével adja meg a napló illesztőprogram-beállításait.

## <a name="next-steps"></a>További lépések
A tárolók a Service Fabric-fürt üzembe helyezése, tekintse meg a cikk [üzembe egy tárolót a Service Fabric](service-fabric-deploy-container.md).
