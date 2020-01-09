---
title: Szolgáltatás portszámának megadása paraméterek használatával
description: Bemutatja, hogyan használhatók paraméterek egy alkalmazás portjának megadásához Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609859"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Szolgáltatás portszámának megadása a Service Fabric paramétereinek használatával

Ez a cikk bemutatja, hogyan határozhatja meg egy szolgáltatás portszámát a Visual Studio használatával Service Fabric paramétereket.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Eljárás a szolgáltatás portszámának megadásához paraméterek használatával

Ebben a példában a asp.net Core webes API portszámát paraméterrel állíthatja be.

1. Nyissa meg a Visual studiót, és hozzon létre egy új Service Fabric alkalmazást.
1. Válassza ki az állapot nélküli ASP.NET Core sablont.
1. Válassza a webes API lehetőséget.
1. Nyissa meg a ServiceManifest. xml fájlt.
1. Jegyezze fel a szolgáltatáshoz megadott végpont nevét. Az alapértelmezett szint a `ServiceEndpoint`.
1. A ApplicationManifest. xml fájl megnyitása
1. A `ServiceManifestImport` elemben adjon hozzá egy új `RessourceOverrides` elemet a ServiceManifest. xml fájlban található végpontra mutató hivatkozással.

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

1. A `Endpoint` elemben mostantól felülbírálhat bármely attribútumot egy paraméter használatával. Ebben a példában megadhatja `Port` és beállíthatja a paraméter nevét szögletes zárójelek használatával – például `[MyWebAPI_PortNumber]`

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

1. Továbbra is a ApplicationManifest. xml fájlban adja meg a paramétert a `Parameters` elemben.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. És Definiáljon egy `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Nyissa meg a ApplicationParameters mappát és a `Cloud.xml` fájlt
1. Ha másik portot szeretne megadni a távoli fürtre való közzétételkor, adja hozzá a paramétert a fájlhoz a portszámmal.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Ha az alkalmazást a Cloud. XML közzétételi profil használatával teszi közzé a Visual studióból, a szolgáltatás a 80-es port használatára van konfigurálva. Ha az alkalmazást a MyWebAPI_PortNumber paraméter meghatározása nélkül helyezi üzembe, a szolgáltatás a 8080-es portot használja.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az ebben a cikkben tárgyalt alapfogalmakról, tekintse meg az [Alkalmazások kezelése több környezethez című cikket](service-fabric-manage-multiple-environment-app-configuration.md).

További információ a Visual Studióban elérhető egyéb alkalmazás-felügyeleti lehetőségekről: [Service Fabric alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md).
