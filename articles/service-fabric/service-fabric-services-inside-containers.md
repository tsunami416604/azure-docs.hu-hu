---
title: Azure Service Fabric-szolgáltatások tárolóba helyezése Windows rendszeren
description: Ismerje meg, hogyan tárolóba helyezése a Service Fabric Reliable Services és a Reliable Actors szolgáltatások Windows rendszeren.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: 0cb48a2272ce854005f9f3db5b6a9abf62cc7015
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599206"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>A Service Fabric Reliable Services és a Reliable Actors tárolóba helyezése Windows rendszeren

Service Fabric támogatja a containerizing Service Fabric-szolgáltatásait (Reliable Services és a megbízható Actor-alapú szolgáltatásokat). További információt a [Service Fabric](service-fabric-containers-overview.md)-tárolók című témakörben talál.

Ez a dokumentum útmutatást nyújt a szolgáltatás Windows-tárolón belüli futtatásához.

> [!NOTE]
> Jelenleg ez a funkció csak Windows rendszeren működik. A tárolók futtatásához a fürtnek a Windows Server 2016 tárolókkal kell futnia.

## <a name="steps-to-containerize-your-service-fabric-application"></a>A Service Fabric-alkalmazás tárolóba helyezése lépései

1. Nyissa meg a Service Fabric alkalmazást a Visual Studióban.

2. Adja hozzá az osztály [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) a projekthez. Az ebben az osztályban található kód a Service Fabric futtatókörnyezet bináris fájljainak megfelelő betöltésére szolgál az alkalmazásban, amikor egy tárolón belül fut.

3. Minden tárolóba helyezése, amelyet szeretne, inicializálja a betöltőt a program belépési pontján. Adja hozzá a következő kódrészletben látható statikus konstruktort a program belépési pontjának fájljához.

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

4. Készítse [](service-fabric-package-apps.md#Package-App) elő és csomagolja ki a projektet. A csomagok létrehozásához és létrehozásához kattintson a jobb gombbal az alkalmazás projektre Megoldáskezelő, majd válassza a **csomag** parancsot.

5. Minden tárolóba helyezése szükséges kód esetén futtassa a [CreateDockerPackage. Ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1)PowerShell-szkriptet. A használat a következő:

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
      A szkript létrehoz egy mappát Docker-összetevőkkel a következő helyen: $dockerPackageOutputDirectoryPath. Módosítsa a generált Docker `expose` bármely portra, futtassa a telepítési parancsfájlokat, és így tovább. igényei alapján.

6. Ezután [létre](service-fabric-get-started-containers.md#Build-Containers) kell hoznia és [le](service-fabric-get-started-containers.md#Push-Containers) kell küldenie a Docker-tároló csomagot a tárházba.

7. A ApplicationManifest. XML és a ServiceManifest. xml fájl módosításával adja hozzá a tároló képét, a tárház adatait, a beállításjegyzék-hitelesítést és a port – gazdagép megfeleltetést. A jegyzékfájlok módosításához tekintse meg az [Azure Service Fabric Container-alkalmazás létrehozása](service-fabric-get-started-containers.md)című témakört. A szolgáltatás jegyzékfájljában a csomag definícióját a megfelelő tároló képével kell helyettesíteni. Győződjön meg arról, hogy a BelépésiPont ContainerHost-típusra módosítja.

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

8. Adja hozzá a port – gazdagép leképezést a replikátor és a szolgáltatás végpontja számára. Mivel mindkét portot Service Fabric futtató futtatókörnyezet rendeli hozzá, a ContainerPort nullára van állítva, hogy a hozzárendelt portot használja a leképezéshez.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. A tároló-elkülönítési mód konfigurálásával kapcsolatban lásd az [elkülönítési mód konfigurálása]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode)című témakört. A Windows a tárolók két elkülönítési módját támogatja: a folyamatalapú és a Hyper-V módot. Az alábbi kódrészletek azt mutatják be, hogyan van megadva az elkülönítési mód az alkalmazás jegyzékfájljában.

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

> [!NOTE] 
> Alapértelmezés szerint Service Fabric alkalmazások hozzáférhetnek az Service Fabric futtatókörnyezethez az alkalmazásspecifikus kérelmeket fogadó végpont formájában. Érdemes lehet letiltani ezt a hozzáférést, ha az alkalmazás nem megbízható programkódot üzemeltet. További információkért tekintse [meg a Service Fabric ajánlott biztonsági eljárásokat](service-fabric-best-practices-security.md#platform-isolation). Ha le szeretné tiltani a Service Fabric futtatókörnyezet elérését, adja hozzá a következő beállítást az importált szolgáltatás jegyzékfájljának megfelelő alkalmazás-jegyzékfájl szabályzatok szakaszában, a következőképpen:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Az alkalmazás teszteléséhez a 5,7-es vagy újabb verziót futtató fürtre kell telepítenie. A 6,1-es vagy alacsonyabb verziójú futtatókörnyezet esetén szerkesztenie és frissítenie kell a fürt beállításait az előzetes verzió funkció engedélyezéséhez. A jelen [cikkben](service-fabric-cluster-fabric-settings.md) ismertetett lépéseket követve adja hozzá a következő beállítást.
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

11. Ezután [telepítse](service-fabric-deploy-remove-applications.md) a szerkesztett alkalmazáscsomag erre a fürtre.

Most már rendelkeznie kell egy, a fürtöt futtató, tárolóban Service Fabric alkalmazással.

## <a name="next-steps"></a>További lépések
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-get-started-containers.md).
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
