---
title: A szolgáltatások környezeti változóinak megadása
description: Bemutatja, hogyan használhat környezeti változókat a Service Fabric alkalmazásaihoz
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f4c4f2a1c140e3d0f181c4fd55482056f9f91b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614315"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Környezeti változók megadása a Service Fabric szolgáltatásához

Ez a cikk bemutatja, hogyan adhat meg környezeti változókat egy szolgáltatáshoz vagy tárolóhoz a Service Fabricben.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>A szolgáltatások környezeti változóinak meghatározására szolgáló eljárás

Ebben a példában egy környezeti változót állít be egy tárolóhoz. A cikk feltételezi, hogy már rendelkezik egy alkalmazás- és szolgáltatásjegyzék.The article assumes you already have a application and service manifest.

1. Nyissa meg a ServiceManifest.xml fájlt.
2. Az `CodePackage` elemben adjon `EnvironmentVariables` hozzá egy `EnvironmentVariable` új elemet és egy elemet minden környezeti változóhoz.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   A környezeti változók felülbírálhatók az alkalmazásjegyzékben.

3. Az alkalmazásjegyzék ben lévő környezeti változók `EnvironmentOverrides` felülbírálásához használja az elemet.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Környezeti változók dinamikus megadása a Docker Compose használatával

A Service Fabric támogatja a [Docker-komponálás központi telepítéséhez való használatát.](service-fabric-docker-compose.md#supported-compose-directives) A fájlok írása környezeti változókat szerezhet a rendszerhéjból. Ez a viselkedés a kívánt környezeti értékek dinamikus helyettesítésére használható:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a cikkben tárgyalt néhány alapvető fogalmakról, olvassa el az [Alkalmazások kezelése több környezethez című cikket.](service-fabric-manage-multiple-environment-app-configuration.md)

A Visual Studio egyéb alkalmazáskezelési képességeiről a [Service Fabric-alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md)című témakörben talál további információt.
