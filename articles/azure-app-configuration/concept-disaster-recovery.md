---
title: Az Azure App konfigurációs rugalmassága és vészhelyreállítás
description: Lean hogyan valósíthatja meg a rugalmasság és a vészhelyreállítás az Azure App konfigurációja.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 96ef09ac081aa328014217592a7fcd3ed6314c0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523764"
---
# <a name="resiliency-and-disaster-recovery"></a>Rugalmasság és vészhelyreállítás

Jelenleg az Azure App Configuration egy regionális szolgáltatás. Minden konfigurációs tároló egy adott Azure-régióban jön létre. A régiószintű kimaradás az adott régió összes üzletét érinti. Az Alkalmazáskonfigurálás nem kínál automatikus feladatátvételt egy másik régióba. Ez a cikk általános útmutatást nyújt arról, hogyan használhatja az Azure-régiók több konfigurációs tárolóját az alkalmazás földrajzi rugalmasságának növeléséhez.

## <a name="high-availability-architecture"></a>Magas rendelkezésre állású architektúra

A régiók közötti redundancia megvalósításához több alkalmazáskonfigurációs áruházat kell létrehoznia különböző régiókban. Ezzel a beállítással az alkalmazás rendelkezik legalább egy további konfigurációs tároló, hogy esik vissza, ha az elsődleges tároló elérhetetlenné válik. Az alábbi ábra az alkalmazás és az elsődleges és másodlagos konfigurációs tárolók közötti topológiát mutatja be:

![Georedundáns üzletek](./media/geo-redundant-app-configuration-stores.png)

Az alkalmazás az elsődleges és a másodlagos tárolókból is betölti a konfigurációját párhuzamosan. Ezzel növeli a konfigurációs adatok sikeres beszerzésének esélyét. Ön a felelős azért, hogy az adatokat mindkét tárolóban szinkronban tartsa. A következő szakaszok bemutatják, hogyan hozhat létre földrajzi rugalmasságot az alkalmazásba.

## <a name="failover-between-configuration-stores"></a>Feladatátvétel a konfigurációs tárolók között

Technikailag az alkalmazás nem hajt végre feladatátvételt. Egyszerre két alkalmazáskonfigurációs tárolóból próbálja meg lekérni ugyanazt a konfigurációs adatokat. Rendezze el a kódot úgy, hogy először a másodlagos tárolóból, majd az elsődleges tárolóból töltődjön be. Ez a megközelítés biztosítja, hogy az elsődleges tárolóban lévő konfigurációs adatok elsőbbséget élveznek, amikor elérhetővé válik. A következő kódrészlet bemutatja, hogyan valósítható meg ez a megállapodás a .NET Core-ban:

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Figyelje `optional` meg, `AddAzureAppConfiguration` hogy a paraméter átkerült a függvénybe. A beállítás `true`esetén ez a paraméter megakadályozza, hogy az alkalmazás ne folytatódjon, ha a függvény nem tudja betölteni a konfigurációs adatokat.

## <a name="synchronization-between-configuration-stores"></a>Szinkronizálás a konfigurációs tárolók között

Fontos, hogy a georedundáns konfigurációs tárolók mindegyike azonos adatkészlettel rendelkezik. Az Alkalmazáskonfiguráció **Exportálás** funkciójával adatokat másolhat az elsődleges tárolóból igény szerint a másodlagosba. Ez a funkció az Azure Portalon és a CLI-n keresztül is elérhető.

Az Azure Portalon leküldéses egy módosítást egy másik konfigurációs tárolóba az alábbi lépések végrehajtásával.

1. Nyissa meg az **Importálás/exportálás** lapot, és válassza**az Alkalmazáskonfigurációs** > **cél** >  **exportálása** > Erőforrás kiválasztása**lehetőséget.**

1. A megnyíló új panelen adja meg a másodlagos tároló előfizetését, erőforráscsoportját és erőforrásnevét, majd válassza az **Alkalmaz lehetőséget.**

1. A felhasználói felület frissül, így kiválaszthatja, hogy milyen konfigurációs adatokat szeretne exportálni a másodlagos tárolóba. Az alapértelmezett időértéket változatlanul hagyhatja, és a **Címtől** és **a To címkét** is ugyanarra az értékre állíthatja. Kattintson az **Alkalmaz** gombra.

1. Ismételje meg az előző lépéseket az összes konfigurációs módosításhoz.

Az exportálási folyamat automatizálásához használja az Azure CLI.To automated this export process, use the Azure CLI. A következő parancs bemutatja, hogyan lehet egyetlen konfigurációs módosítást exportálni az elsődleges tárolóból a másodlagosba:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan bővítheti az alkalmazást a földrajzi rugalmasság elérése érdekében futásidejű alkalmazáskonfiguráció. Az alkalmazáskonfigurációból is beágyazhat konfigurációs adatokat létrehozási vagy telepítési időpontban. További információ: [Integrálás CI/CD-folyamattal.](./integrate-ci-cd-pipeline.md)
