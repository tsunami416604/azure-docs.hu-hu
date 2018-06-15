---
title: A paraméterek az Azure Service Fabric szolgáltatás portszámát megadása |} Microsoft Docs
description: Bemutatja, hogyan paraméterek használatával adja meg a portot, az alkalmazás a Service Fabric
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
ms.openlocfilehash: 06cfb375c6c18082a0d0316cfcb742a7779fc8a8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206378"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>A port számát, a paraméterek használatával a Service Fabric szolgáltatás megadása

Ez a cikk bemutatja, hogyan adja meg a portszámot, a paraméterek használatával a Service Fabric szolgáltatás Visual Studio használatával.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Az eljárás megadásának paraméterek szolgáltatás portjának száma

Ebben a példában az asp.net core webes API-t egy paraméter segítségével állítsa be a port számát.

1. Nyissa meg a Visual Studio, és hozzon létre egy új Service Fabric-alkalmazás.
1. Válassza ki az állapot nélküli ASP.NET Core sablont.
1. Válassza a webes API-t.
1. Nyissa meg a ServiceManifest.xml fájlt.
1. Megjegyzés: a végpont, a szolgáltatás számára megadott név. Az alapértelmezett szint a `ServiceEndpoint`.
1. Nyissa meg a ApplicationManifest.xml fájlt
1. Az a `ServiceManifestImport` elem, adjon hozzá egy új `RessourceOverrides` elem a végpont a ServiceManifest.xml fájlban hivatkozást.

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

1. Az a `Endpoint` elem, most felülbírálhatja azok az attribútumok egy paraméterrel. A jelen példában adja meg `Port` , majd állítsa be a kapcsos zárójeleket – például használatával paraméter neve `[MyWebAPI_PortNumber]`

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

1. Továbbra is a ApplicationManifest.xml fájlban, akkor adja meg a paraméter a `Parameters` elem

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. És adja meg a `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Nyissa meg a ApplicationParameters mappát és a `Cloud.xml` fájl
1. Távoli fürtre közzétételekor használt egy másik portra megadásához adja hozzá a paramétert portszámmal ehhez a fájlhoz.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="80" />
      </Parameters>
    ```

A Visual Studio használatával a Cloud.xml az alkalmazás közzétételéhez közzétételi profil, ha a szolgáltatás 80-as port használatára van konfigurálva. Az alkalmazás központi telepítése a MyWebAPI_PortNumber paraméter megadása nélkül, ha a szolgáltatás a 8080-as portot használ.

## <a name="next-steps"></a>További lépések
Ebben a cikkben ismertetett alapfogalmakat némelyike kapcsolatos további tudnivalókért tekintse meg a [több környezetek cikkek alkalmazásokat kezeléséhez](service-fabric-manage-multiple-environment-app-configuration.md).

Más elérhető a Visual Studio alkalmazás-felügyeleti képességekkel kapcsolatos információkért lásd: [kezelése a Service Fabric-alkalmazások, a Visual Studio](service-fabric-manage-application-in-visual-studio.md).