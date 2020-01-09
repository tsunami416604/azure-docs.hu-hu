---
title: Parametrizálja konfigurációs fájlok az Azure-ban Service Fabric
description: Megtudhatja, hogyan parametrizálja a konfigurációs fájlokat a Service Fabricban, amely hasznos módszer több környezet kezelésekor.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644630"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Konfigurációs fájlok parametrizálja Service Fabric

Ez a cikk bemutatja, hogyan parametrizálja egy konfigurációs fájlt a Service Fabricban.  Ha még nem ismeri az alkalmazások több környezethez való felügyeletének alapvető fogalmait, olvassa el az [Alkalmazások kezelése több környezethez](service-fabric-manage-multiple-environment-app-configuration.md)című témakört.

## <a name="procedure-for-parameterizing-configuration-files"></a>A parameterizing konfigurációs fájljainak eljárása

Ebben a példában egy konfigurációs értéket kell felülbírálnia az alkalmazás telepítésében található paraméterek használatával.

1. Nyissa meg a *\<MyService > \PackageRoot\Config\Settings.XML* fájlt a szolgáltatási projektben.
1. A következő XML-kód hozzáadásával állítsa be a konfigurációs paraméter nevét és értékét, például a gyorsítótár méretét 25-re:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Mentse és zárja be a fájlt.
1. Nyissa meg a *\<MyApplication > \ApplicationPackageRoot\ApplicationManifest.XML* fájlt.
1. A ApplicationManifest. xml fájlban deklaráljon egy paramétert és egy alapértelmezett értéket a `Parameters` elemben.  Javasoljuk, hogy a paraméter neve tartalmazza a szolgáltatás nevét (például "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. A ApplicationManifest. xml fájl `ServiceManifestImport` szakaszában adjon hozzá egy `ConfigOverrides` és `ConfigOverride` elemet, amely a konfigurációs csomagra, a szakaszra és a paraméterre hivatkozik.

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
> Abban az esetben, ha ConfigOverride ad hozzá, Service Fabric mindig kiválasztja az alkalmazás-jegyzékfájlban megadott alkalmazások paramétereit vagy az alapértelmezett értéket.
>
>

## <a name="next-steps"></a>Következő lépések
További információ a Visual Studióban elérhető egyéb alkalmazás-felügyeleti lehetőségekről: [Service Fabric alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md).
