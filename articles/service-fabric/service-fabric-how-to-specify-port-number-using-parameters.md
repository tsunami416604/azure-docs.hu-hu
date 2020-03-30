---
title: Szolgáltatás portszámának megadása paraméterek használatával
description: Bemutatja, hogyan adhat meg paramétereket egy alkalmazás portjának megadására a Service Fabric ben
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609859"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Szolgáltatás portszámának megadása a Service Fabric paramétereivel

Ez a cikk bemutatja, hogyan adhatja meg a szolgáltatás portszámát a Service Fabric paramétereivel a Visual Studio használatával.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Eljárás a szolgáltatás portszámának paraméterek használatával történő megadására

Ebben a példában a asp.net alapvető webes API-hoz egy paraméter használatával állíthatja be a portszámát.

1. Nyissa meg a Visual Studio alkalmazást, és hozzon létre egy új Service Fabric-alkalmazást.
1. Válassza a Stateless ASP.NET Core sablont.
1. Válassza a Webes API lehetőséget.
1. Nyissa meg a ServiceManifest.xml fájlt.
1. Jegyezze fel a szolgáltatáshoz megadott végpont nevét. Az alapértelmezett szint a `ServiceEndpoint`.
1. Az ApplicationManifest.xml fájl megnyitása
1. Az `ServiceManifestImport` elemben adjon `RessourceOverrides` hozzá egy új elemet, amely hivatkozik a ServiceManifest.xml fájl végpontjára.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Az `Endpoint` elemben most már felülbírálhat bármely attribútumot egy paraméter használatával. Ebben a példában `Port` szögletes zárójelek használatával adja meg és állítja be a paraméternevét – például`[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Még mindig az ApplicationManifest.xml fájlban, majd `Parameters` adja meg a paramétert az elemben

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. És határozza meg a`DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Az ApplicationParameters mappa `Cloud.xml` és a fájl megnyitása
1. Ha egy másik portot szeretne megadni a távoli fürtön való közzétételkor, adja hozzá a portszámot tartalmazó paramétert a fájlhoz.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Amikor az alkalmazást a Visual Studio-ból teszi közzé a Cloud.xml közzétételi profil használatával, a szolgáltatás a 80-as port használatára van konfigurálva. Ha az alkalmazást a MyWebAPI_PortNumber paraméter megadása nélkül telepíti, a szolgáltatás a 8080-as portot használja.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a cikkben tárgyalt néhány alapvető fogalmakról, olvassa el az [Alkalmazások kezelése több környezethez című cikket.](service-fabric-manage-multiple-environment-app-configuration.md)

A Visual Studio egyéb alkalmazáskezelési képességeiről a [Service Fabric-alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md)című témakörben talál további információt.
