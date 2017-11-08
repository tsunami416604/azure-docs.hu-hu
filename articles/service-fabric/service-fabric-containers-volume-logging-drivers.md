---
title: "Az Azure Service Fabric Docker Compose előzetes verzió |} Microsoft Docs"
description: "Az Azure Service Fabric a Docker Compose formátum használatával könnyebben lehet levezényelni a Service Fabric használatával exsiting tárolók fogad el. Ez a támogatás jelenleg előzetes verzió."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 7464611e669165d9ec1f0de7422b20b3f3b8c2b5
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>Kötet beépülő modulok segítségével, és a tároló-illesztőprogramok naplózás

A Service Fabric támogatja megadó [Docker kötet beépülő modulok](https://docs.docker.com/engine/extend/plugins_volume/) és [Docker naplózási illesztőprogramok](https://docs.docker.com/engine/admin/logging/overview/) a tárolószolgáltatás számára. 

## <a name="install-volumelogging-driver"></a>Kötet/naplózási illesztőprogram telepítése

Ha a Docker kötet/naplózási illesztőprogram nincs telepítve a számítógépen, telepítse manuálisan VMSS indítási parancsfájl vagy keresztül az RDP/SSH-ing be a számítógépre. Például azért, hogy a Docker kötet illesztőprogramot telepítse, SSH be a számítógépre, majd hajtsa végre:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>Adja meg a beépülő modul vagy illesztőprogram a jegyzékben
A beépülő modulok az alkalmazásjegyzékben vannak megadva, ahogy az a következő jegyzékfájl:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
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
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
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

Az előző példában a `Source` a címke a `Volume` a forrás mappára hivatkozik. A forrásmappa lehet a virtuális gépen, amelyen a tárolók vagy egy állandó távoli tároló mappa. A `Destination` címke az a hely, amely a `Source` futó tárolóban van leképezve. 

Ha egy kötet beépülő modult, a Service Fabric automatikusan létrehozza a kötet a megadott paraméterekkel. A `Source` címke pedig a kötet neve és a `Driver` kód adja meg a kötet illesztőprogram beépülő modul. Beállítások adhatók meg a `DriverOption` címkét, ahogy az a következő kódrészletet:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Egy Docker-napló illesztőprogram meg van adva, akkor a fürtben lévő ügynökök (vagy a tárolókat) a naplók kezelése telepítéséhez szükséges.  A `DriverOption` címke segítségével adja meg a napló illesztőprogram beállításokat is.

Tekintse meg a Service Fabric-fürt – tárolók üzembe helyezése a következő cikkeket:


[Üzembe egy tárolót a Service Fabric](service-fabric-deploy-container.md)

