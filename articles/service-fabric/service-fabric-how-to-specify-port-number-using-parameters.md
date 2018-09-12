---
title: A port számát, a paraméterek használatával az Azure Service Fabric szolgáltatás megadása |} A Microsoft Docs
description: Bemutatja, hogyan paraméterek használatával adja meg a portot, az alkalmazás a Service Fabricben
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
ms.openlocfilehash: d69e02126564388bf045693b9960e6e574307641
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391334"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>A port számát, a paraméterek használatával a Service Fabric szolgáltatás megadása

Ez a cikk bemutatja, hogyan adható meg a portot a paraméterek használatával a Service Fabric szolgáltatás Visual Studio használatával.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Adja meg a port számát, a paraméterek használatával szolgáltatás eljárását

Ebben a példában az asp.net core webes API-t egy paraméter használatával állítsa be a port számát.

1. Nyissa meg a Visual Studiót, és a egy új Service Fabric-alkalmazás létrehozása.
1. Az állapot nélküli ASP.NET Core-sablon kiválasztása.
1. Válassza ki a webes API-t.
1. Nyissa meg az objektumhoz a ServiceManifest.xml fájlban.
1. Megjegyzés: az a végpont nevét, a szolgáltatás számára megadott. Az alapértelmezett szint a `ServiceEndpoint`.
1. Nyissa meg az ApplicationManifest.xml fájl
1. Az a `ServiceManifestImport` elem, vegyen fel egy új `RessourceOverrides` elemet egy hivatkozást a végpont-objektumhoz a ServiceManifest.xml fájlban.

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

1. Az a `Endpoint` elem, most felülbírálhatja egy paraméter használatával bármely attribútum. Ebben a példában adja meg `Port` , illetve beállíthatja a szögletes zárójelek – például használatával paraméter neve `[MyWebAPI_PortNumber]`

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

1. Továbbra is az ApplicationManifest.xml fájlt, majd adja meg a paramétert a `Parameters` elem

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. Adja meg, és egy `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Nyissa meg a ApplicationParameters mappát, és a `Cloud.xml` fájl
1. Adjon meg egy másik portot egy távoli fürtön való közzétételéhez használt, adja hozzá a paraméter portszámmal ehhez a fájlhoz.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Ha az alkalmazás a Visual Studio használatával a Cloud.xml közzétételéhez közzétételi profil, a szolgáltatás 80-as port használatára van konfigurálva. Az alkalmazás üzembe helyezése a MyWebAPI_PortNumber paraméter megadása nélkül, ha a szolgáltatás a 8080-as portot használja.

## <a name="next-steps"></a>További lépések
Az ebben a cikkben ismertetett alapfogalmak némelyike kapcsolatos további információkért lásd: a [alkalmazások több cikket is környezetek kezelése](service-fabric-manage-multiple-environment-app-configuration.md).

Egyéb elérhető a Visual Studio alkalmazás-felügyeleti képességekkel kapcsolatos információkért lásd: [kezelése a Service Fabric-alkalmazásokat a Visual Studióban](service-fabric-manage-application-in-visual-studio.md).
