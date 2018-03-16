---
title: "Hogyan containerize az Azure Service Fabric mikroszolgáltatások (előzetes verzió)"
description: "Az Azure Service Fabric hozzá van adva a Service Fabric mikroszolgáltatások containerize érdekében új funkciókat. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető."
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.openlocfilehash: e66e488d8e547e828c014b105a816a14726e5005
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Hogyan containerize a Service Fabric Reliable Services és Reliable Actors (előzetes verzió)

A Service Fabric containerizing Service Fabric mikroszolgáltatások (Reliable Services és megbízható alapú aktorszolgáltatások) támogatja. További információkért lásd: [service fabric-tárolók](service-fabric-containers-overview.md).

Ez a funkció jelenleg előzetes verzióban érhető, és ez a cikk lépéseit a különböző egy tároló-keretrendszeren belül fut. a szolgáltatás eléréséhez.  

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhető és éles környezetben nem támogatott. Jelenleg ez a funkció csak akkor működik a Windows. Tárolók futtatásához, a fürt Windows Server 2016 tárolókhoz kell futnia.

## <a name="steps-to-containerize-your-service-fabric-application"></a>A Service Fabric-alkalmazás containerize lépései

1. A Visual Studióban nyissa meg a Service Fabric-alkalmazás.

2. Osztály hozzáadása [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) a projekthez. Ez az osztály a kód a Service Fabric futásidejű bináris fájljai az alkalmazáson belüli megfelelően betölteni, ha egy tároló keretrendszeren belül fut. segítő.

3. Minden egyes szeretné containerize, a program belépési betöltő inicializálása kódcsomag mutat. Adja hozzá a statikus konstruktor a következő kódrészletet a belépési pont programfájl látható.

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

4. Build és [csomag](service-fabric-package-apps.md#Package-App) a projekthez. Építsenek, és hozzon létre egy csomagot, kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza ki a **csomag** parancsot.

5. A minden kódcsomag kell containerize, a PowerShell-parancsprogrammal [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). A használati a következőképpen történik:
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  A parancsfájl létrehoz egy mappát, $dockerPackageOutputDirectoryPath Docker-összetevők. A generált Dockerfile teszi közzé a portokat, futtassa a telepítő parancsfájlok stb. a igények alapján módosíthatja.

6. Ezután meg kell [build](service-fabric-get-started-containers.md#Build-Containers) és [leküldéses](service-fabric-get-started-containers.md#Push-Containers) a Docker-tároló csomag a tárházhoz.

7. A tároló kép, a tárház információkat, a beállításjegyzék-hitelesítés és a port-állomás leképezés hozzáadása ApplicationManifest.xml és ServiceManifest.xml módosíthatók. A jegyzékfájlokban módosítása, lásd: [hozzon létre egy Azure Service Fabric-tároló alkalmazás](service-fabric-get-started-containers.md). A kód Csomagdefiníció a szolgáltatás jegyzékben meg kell írni a megfelelő tároló-lemezképet. Győződjön meg arról, hogy a belépési pont módosítása ContainerHost típusra.

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

8. A port-állomás társítását a replikátor és a szolgáltatási végpont hozzáadása. Mindkét ezeket a portokat a Service Fabric által kiosztott futásidőben, mivel a ContainerPort értéke nulla esetén pedig a hozzárendelt portot használja a leképezés.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. Ez az alkalmazás teszteléséhez kell központilag telepítenie kell egy fürt, amely 5.7-es vagy újabb verziót futtat. Emellett meg kell módosítsa és frissítse a fürt beállításait a előzetes funkció engedélyezése érdekében. Ezen lépések [cikk](service-fabric-cluster-fabric-settings.md) a következő látható beállítás hozzáadásához.
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
10. Következő [telepítése](service-fabric-deploy-remove-applications.md) a szerkesztett alkalmazáscsomag ehhez a fürthöz.

Most rendelkeznie kell egy indexelése Service Fabric-alkalmazás fut a fürtön.

## <a name="next-steps"></a>További lépések
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-get-started-containers.md).
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
