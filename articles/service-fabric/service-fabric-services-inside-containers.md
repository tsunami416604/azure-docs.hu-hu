---
title: Az Azure Service Fabric-szolgáltatások a Windows tárolóba
description: Útmutató a Service Fabric Reliable Services és Reliable Actors szolgáltatások a Windows tárolóba.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: aljo, anmola
ms.openlocfilehash: f5e31c6cf08ab455b835231f54b564a3e4ed8dad
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806198"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>A Service Fabric Reliable Services és Reliable Actors Windows a tárolóba

Service Fabric támogatja a Service Fabric mikroszolgáltatásokat bontását a (a Reliable Services és Reliable Actors-alapú szolgáltatások). További információkért lásd: [service fabric-tárolók](service-fabric-containers-overview.md).

A dokumentum a Windows-tárolón belül futó service útmutatóval szolgál.

> [!NOTE]
> Ez a funkció jelenleg csak működik a Windows. Tárolók futtatásához, a fürt tárolókkal rendelkező Windows Server 2016 rendszeren kell futnia.

## <a name="steps-to-containerize-your-service-fabric-application"></a>A Service Fabric-alkalmazás tárolóalapúvá lépései

1. A Visual Studióban nyissa meg a Service Fabric-alkalmazásokat.

2. Osztály hozzáadása [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) a projekthez. Ennek az osztálynak a kódja megfelelően betölteni a Service Fabric futtatókörnyezet bináris fájlokat, az alkalmazáson belül, ha a tárolóban futtatott segítő.

3. Minden kódcsomaghoz szeretné a tárolóba, inicializálja a betöltőt, a program belépési pont. Adja hozzá a statikus konstruktort, a program belépési pont fájlt az alábbi kódrészlet látható.

  ```csharp
  namespace MyApplication
  {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
  ```

4. Hozhat létre és [csomag](service-fabric-package-apps.md#Package-App) a projekthez. Hozhat létre, és hozzon létre egy csomagot, kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza ki a **csomag** parancsot.

5. Minden kódcsomaghoz kell igény szerint tárolóalapúvá alakíthatja, a PowerShell-parancsprogrammal [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). A használati a következőképpen történik:

    Teljes .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      A szkript létrehoz egy mappát $dockerPackageOutputDirectoryPath, Docker-összetevőkkel. A létrehozott docker-fájlban való módosítása `expose` portokat, a telepítő szkriptek futtatása és így tovább. igényei alapján.

6. Ezután kell [összeállítása](service-fabric-get-started-containers.md#Build-Containers) és [leküldéses](service-fabric-get-started-containers.md#Push-Containers) a Docker-tároló csomag a tárházhoz.

7. Módosítsa az ApplicationManifest.xml és a ServiceManifest.xml a tárolórendszerképet, adattár információit, beállításjegyzék-hitelesítést és port-gazdagép leképezés hozzáadása. A jegyzékek módosítása, lásd: [egy Azure Service Fabric-tárolóalkalmazás létrehozása](service-fabric-get-started-containers.md). A kód Csomagdefiníció szolgáltatásjegyzékben kell írni a megfelelő tárolórendszerképet. Ellenőrizze, hogy módosítsa a belépési pont ContainerHost típusra.

  ```xml
<!-- Code package is your service executable. -->
<CodePackage Name="Code" Version="1.0.0">
  <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
  </EntryPoint>
  <!-- Pass environment variables to your container: -->
</CodePackage>
  ```

8. A port-gazdagép-leképezés a replikátor és egy szolgáltatásvégpont hozzáadása. Mindkét ezeket a portokat a Service Fabric által kiosztott futásidőben, mivel a ContainerPort értéke nulla esetén pedig a hozzárendelt port használatára való hozzárendelésére.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. Tároló elkülönítési mód konfigurálása, lásd: [elkülönítési mód konfigurálása]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). A Windows a tárolók két elkülönítési módját támogatja: a folyamatalapú és a Hyper-V módot. Az alábbi kódrészletek bemutatják, hogyan az elkülönítési mód az Alkalmazásjegyzék-fájl megadott.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
  ...
  </ContainerHostPolicies>
</Policies>
 ```
  ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
  ...
  </ContainerHostPolicies>
</Policies>
 ```

10. Ez az alkalmazás teszteléséhez szüksége telepíteni kell egy fürtöt, amely 5.7-es vagy újabb verziót futtat. A futtatókörnyezet 6.1-es vagy alacsonyabb verziók kell szerkesztése és az előzetes verziójú funkció engedélyezéséhez a fürt beállításainak frissítése. Kövesse a jelen [cikk](service-fabric-cluster-fabric-settings.md) a következő beállítás hozzáadásához.
```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
```

11. Tovább [üzembe helyezése](service-fabric-deploy-remove-applications.md) a szerkesztett alkalmazáscsomagot a fürt.

Most már a fürt futtatása tárolóalapú Service Fabric-alkalmazás.

## <a name="next-steps"></a>További lépések
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-get-started-containers.md).
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
