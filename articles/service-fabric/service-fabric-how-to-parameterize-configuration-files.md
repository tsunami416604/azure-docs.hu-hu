---
title: Konfigurációs fájlok az Azure Service Fabric paraméterezni |} A Microsoft Docs
description: Megtudhatja, hogyan paraméterezni a konfigurációs fájlokat a Service Fabricben.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 9057cdc22e277e4e12e9f439f3fbe0c5a5cda2a2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900513"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Hogyan lehet paraméterezni a konfigurációs fájlokat a Service Fabricben

Ez a cikk bemutatja, hogyan paraméterezni a Service Fabric egy konfigurációs fájlt.  Ha nem már ismeri az alapfogalmakat kezelése több környezethez tartozó alkalmazásokat, olvassa el a [alkalmazások kezelése több környezethez](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Az eljárás a konfigurációs fájlok paraméterezése

Ebben a példában a paraméterek használatával az alkalmazások központi telepítésének a konfigurációs érték felülírása.

1. Nyissa meg a  *<MyService>\PackageRoot\Config\Settings.xml* fájlt a projektet.
1. Állítsa be a konfigurációs paraméter nevének és értékének, például gyorsítótár méretének 25-én egyenlő adja hozzá a következő XML-kódot:

  ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
  ```

1. Mentse és zárja be a fájlt.
1. Nyissa meg a  *<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml* fájlt.
1. Az ApplicationManifest.xml fájl deklaráljon egy paraméter és az alapértelmezett érték a `Parameters` elemet.  Javasoljuk, hogy a paraméter neve tartalmazza a szolgáltatás (például "MyService") nevét.

  ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
  ```
1. Az a `ServiceManifestImport` szakasz az ApplicationManifest.xml fájl hozzáadása egy `ConfigOverride` elem, a konfigurációs csomag, a szakasz és a paraméter hivatkozik.

  ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  ```

> [!NOTE]
> Abban az esetben, ahol hozzáadhat egy ConfigOverride Service Fabric mindig úgy dönt, az alkalmazás paramétereit vagy az alapértelmezett érték az alkalmazásjegyzékben megadott.
>
>

## <a name="next-steps"></a>További lépések
Egyéb elérhető a Visual Studio alkalmazás-felügyeleti képességekkel kapcsolatos információkért lásd: [kezelése a Service Fabric-alkalmazásokat a Visual Studióban](service-fabric-manage-application-in-visual-studio.md).