---
title: Az Azure Service Fabric-szolgáltatások tárolóba hozása Windows rendszeren
description: Ismerje meg, hogyan konténerezheti a Service Fabric megbízható szolgáltatások és a Reliable Actors szolgáltatások a Windows rendszeren.
ms.topic: conceptual
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: 9fe5980c13f655f8f30cc42771971a5015460420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466185"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>A Service Fabric Reliable Services és a Reliable Actors tárolóba helyezése Windowson

A Service Fabric támogatja a Service Fabric mikroszolgáltatások (megbízható szolgáltatások és megbízható elévülnek szolgáltatások) tárolóba helyezését. További információ: [Service fabric containers](service-fabric-containers-overview.md).

Ez a dokumentum útmutatást nyújt a szolgáltatás Windows-tárolón belüli futtatásához.

> [!NOTE]
> Jelenleg ez a funkció csak windowsos. A tárolók futtatásához a fürtnek Windows Server 2016 tárolókkal kell futnia.

## <a name="steps-to-containerize-your-service-fabric-application"></a>A Service Fabric-alkalmazás konténerezési lépései

1. Nyissa meg a Service Fabric-alkalmazást a Visual Studióban.

2. OsztálySFBinaryLoader.cs [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) hozzáadása a projekthez. Ebben az osztályban a kód egy segítő, hogy megfelelően töltse be a Service Fabric futtatóbelek az alkalmazáson belül, ha egy tárolóban fut.

3. Minden egyes kódcsomaghoz, amelyet konténerbe szeretne foglalni, inicializálja a betöltőt a program belépési pontján. Adja hozzá a következő kódrészletben látható statikus konstruktorát a program belépési pontfájljához.

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

4. Készítse el és [csomagolja](service-fabric-package-apps.md#Package-App) a projektet. Csomag létrehozásához és létrehozásához kattintson a jobb gombbal az alkalmazásprojektre a Megoldáskezelőben, és válassza a **Csomag parancsot.**

5. Minden kódcsomag, amelyet meg kell konténerez, futtassa a PowerShell-parancsfájl [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). A használat a következő:

    Teljes .net
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
      A parancsfájl létrehoz egy mappát a Docker-összetevők $dockerPackageOutputDirectoryPath. Módosítsa a létrehozott Docker-fájlt bármely portra, `expose` futtassa a telepítőparancsfájlokat és így tovább. az Ön igényeinek megfelelően.

6. Ezután létre kell [hoznod](service-fabric-get-started-containers.md#Build-Containers) és le kell [adnia](service-fabric-get-started-containers.md#Push-Containers) a Docker-tárolócsomagot a tárházra.

7. Módosítsa az ApplicationManifest.xml és servicemanifest.xml fájlt a tárolókép, a tárházadatai, a rendszerleíró adatbázis hitelesítése és a port-állomás leképezés hozzáadásához. A jegyzékek módosításáról az [Azure Service Fabric-tárolóalkalmazás létrehozása című](service-fabric-get-started-containers.md)témakörben nyújt elő. A kódcsomag-definíciót a szolgáltatásjegyzékben megfelelő tárolórendszerképre kell cserélni. Győződjön meg arról, hogy az EntryPoint-ot ContainerHost típusra módosítja.

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

8. Adja hozzá a port-host leképezésa a replikátor és a szolgáltatás végpontját. Mivel mindkét portot futásidőben a Service Fabric rendeli hozzá, a ContainerPort nullára van állítva a hozzárendelt port leképezéséhez.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. A tárolóelkülönítési mód konfigurálásához [lásd: Elkülönítési mód konfigurálása]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). A Windows a tárolók két elkülönítési módját támogatja: a folyamatalapú és a Hyper-V módot. A következő kódrészletek azt mutatják, hogy az elkülönítési mód hogyan van megadva az alkalmazásjegyzékfájlban.

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
> Alapértelmezés szerint a Service Fabric-alkalmazások hozzáférhetnek a Service Fabric futásidejű, egy végpont alkalmazásspecifikus kéréseket fogad el. Fontolja meg a hozzáférés letiltását, ha az alkalmazás nem megbízható kódot üzemeltet. További információkért tekintse meg a [Service Fabric biztonsági gyakorlati tanácsait.](service-fabric-best-practices-security.md#platform-isolation) A Service Fabric futásidejű elérésének letiltásához adja hozzá a következő beállítást az alkalmazásjegyzék Házirend szakaszában, amely megfelel az importált szolgáltatásjegyzéknek, az alábbiak szerint:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Az alkalmazás teszteléséhez telepítenie kell egy olyan fürtre, amely 5.7-es vagy újabb verziójú. A 6.1-es vagy újabb futásidejű verziók esetén az előnézeti szolgáltatás engedélyezéséhez meg kell adnia és frissítenie kell a fürtbeállításokat. A következő beállítás hozzáadásához kövesse a [cikkben](service-fabric-cluster-fabric-settings.md) ismertetett lépéseket.
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

11. Ezután [telepítse](service-fabric-deploy-remove-applications.md) a szerkesztett alkalmazáscsomagot erre a fürtre.

Most rendelkeznie kell egy tárolóba adott Service Fabric-alkalmazás a fürt öt.

## <a name="next-steps"></a>További lépések
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-get-started-containers.md).
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
