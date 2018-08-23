---
title: Útmutató szolgáltatások környezeti változók megadása az Azure Service Fabric |} A Microsoft Docs
description: Bemutatja, hogyan használhatja a környezeti változókat a Service Fabric-alkalmazások
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f75de635f08ae06db349387a436c636c149ec9f2
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058117"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>A Service Fabric Services környezeti változók megadása

Ez a cikk bemutatja, hogyan adhat meg környezeti változókat egy szolgáltatás vagy a container Service fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Eljárás szolgáltatások környezeti változók megadása

Ebben a példában egy tároló állítsa be egy környezeti változóhoz. A cikk feltételezi, hogy már rendelkezik egy alkalmazás és a jegyzékfájlban.

1. Nyissa meg az objektumhoz a ServiceManifest.xml fájlban.
1. Az a `CodePackage` elem, vegyen fel egy új `EnvironmentVariables` elem és a egy `EnvironmentVariable` elem minden környezeti változóhoz.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    A környezeti változók felülbírálhatja az alkalmazásjegyzékben.

1. Bírálja felül az alkalmazásjegyzékben levő környezeti változók, használja a `EnvironmentOverrides` elemet.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>További lépések
Az ebben a cikkben ismertetett alapfogalmak némelyike kapcsolatos további információkért lásd: a [alkalmazások több cikket is környezetek kezelése](service-fabric-manage-multiple-environment-app-configuration.md).

Egyéb elérhető a Visual Studio alkalmazás-felügyeleti képességekkel kapcsolatos információkért lásd: [kezelése a Service Fabric-alkalmazásokat a Visual Studióban](service-fabric-manage-application-in-visual-studio.md).