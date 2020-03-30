---
title: Konfigurációs fájlok paraméterezése az Azure Service Fabricben
description: Ismerje meg, hogyan paraméterezi a konfigurációs fájlokat a Service Fabric, hasznos technika több környezet kezelése során.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644630"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Konfigurációs fájlok paraméterezése a Service Fabric ben

Ez a cikk bemutatja, hogyan paraméterezheti a konfigurációs fájlt a Service Fabric.This article shows you how to parameterize a configuration file in Service Fabric.  Ha még nem ismeri a több környezetben futó alkalmazások kezelésének alapfogalmait, olvassa el [az Alkalmazások kezelése több környezethez](service-fabric-manage-multiple-environment-app-configuration.md)című részt.

## <a name="procedure-for-parameterizing-configuration-files"></a>A konfigurációs fájlok paraméterezési eljárása

Ebben a példában felülbírálja a konfigurációs értéket az alkalmazás központi telepítésének paramétereivel.

1. Nyissa meg a * \<MyService>\PackageRoot\Config\Settings.xml* fájlt a szolgáltatási projektben.
1. A következő XML hozzáadásával adja meg a konfigurációs paraméter nevét és értékét, például a gyorsítótár 25-nek megfelelő méretét:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Mentse és zárja be a fájlt.
1. Nyissa meg a * \<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml* fájlt.
1. Az ApplicationManifest.xml fájlban deklaráljon `Parameters` egy paramétert és egy alapértelmezett értéket az elemben.  Javasoljuk, hogy a paraméternév tartalmazza a szolgáltatás nevét (például "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Az `ServiceManifestImport` ApplicationManifest.xml fájl szakaszában adjon `ConfigOverrides` `ConfigOverride` hozzá egy elemet és egy elemet, hivatkozva a konfigurációs csomagra, a szakaszra és a paraméterre.

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
> Abban az esetben, ha egy ConfigOverride hozzáadása esetén a Service Fabric mindig kiválasztja az alkalmazás paramétereit vagy az alkalmazásjegyzékben megadott alapértelmezett értéket.
>
>

## <a name="next-steps"></a>További lépések
A Visual Studio egyéb alkalmazáskezelési képességeiről a [Service Fabric-alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md)című témakörben talál további információt.
