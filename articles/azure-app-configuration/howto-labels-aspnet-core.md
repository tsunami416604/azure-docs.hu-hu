---
title: Környezeti konfiguráció használata
titleSuffix: Azure App Configuration
description: Címkék használata a környezethez tartozó konfigurációs értékek biztosításához
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056801"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Különböző konfigurációk engedélyezése címkék használatával különböző környezetekben

Számos alkalmazásnak különböző konfigurációkat kell használnia a különböző környezetekhez. Tegyük fel, hogy egy alkalmazás olyan konfigurációs értékkel rendelkezik, amely meghatározza a háttér-adatbázishoz használandó kapcsolati karakterláncot. Az alkalmazás fejlesztői az éles környezetben használt másik adatbázist használnak. Az alkalmazás által használt adatbázis-kapcsolódási sztringnek meg kell változnia, mivel az alkalmazás a fejlesztéstől a Termelésig mozog.

Az Azure-alkalmazások konfigurációjában a *címkék* használatával különböző értékeket határozhat meg ugyanahhoz a kulcshoz. Megadhat például egy olyan kulcsot, amely különböző értékekkel rendelkezik a *fejlesztéshez* és a *gyártáshoz*. Megadhatja, hogy mely címkéket kell betölteni az alkalmazás-konfigurációhoz való csatlakozáskor.

A funkció bemutatásához módosítani fogjuk a gyors útmutatóban létrehozott webalkalmazást [: hozzon létre egy ASP.net Core alkalmazást az Azure-alkalmazás konfigurációjában](./quickstart-aspnet-core-app.md) , hogy különböző konfigurációs beállításokat használjon a fejlesztéshez és a termeléshez. A folytatás előtt végezze el a gyors üzembe helyezést.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Címke megadása konfigurációs érték hozzáadásakor

A Azure Portal lépjen a **Configuration Explorerbe** , és keresse meg a *TestApp: Settings: FontColor* kulcsot, amelyet a rövid útmutatóban hozott létre. Válassza ki a helyi menüt, majd kattintson az **érték hozzáadása**gombra.

> [!div class="mx-imgBorder"]
> ![Érték menüpont hozzáadása](media/labels-add-value.png)

Az **érték hozzáadása** képernyőn adja meg a **vörös** és a **fejlesztési** **címke** **értékét** . Hagyja üresen a **tartalom típusát** . Kattintson az **Alkalmaz** gombra.

## <a name="loading-configuration-values-with-a-specified-label"></a>Megadott címkével rendelkező konfigurációs értékek betöltése

Alapértelmezés szerint az Azure app Configuration csak a címke nélküli konfigurációs értékeket tölti be. Ha meghatározta a konfigurációs értékek címkéit, meg kell adnia az alkalmazás-konfigurációhoz való csatlakozáskor használni kívánt címkét (ka) t.

Az utolsó szakaszban létrehozott egy másik konfigurációs értéket a *fejlesztési* környezethez. A `HostingEnvironment.EnvironmentName` változó használatával dinamikusan meghatározhatja, hogy az alkalmazás jelenleg milyen környezetben fut. További információ: [több környezet használata ASP.net Coreban](/aspnet/core/fundamentals/environments).

Az aktuális környezetnek megfelelő címkével töltse be a konfigurációs értékeket a környezet nevének a `Select` metódusba való átadásával:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> A fenti kódrészlet egy nevű `AppConfigConnectionString`környezeti változóból tölti be az alkalmazás konfigurációs kapcsolatok sztringjét. Ügyeljen arra, hogy a környezeti változó megfelelően legyen beállítva.

A `Select` metódust kétszer kell meghívni. Az első alkalommal a címke nélküli konfigurációs értékeket tölti be. Ezután betölti a konfigurációs értékeket az aktuális környezetnek megfelelő címkével. Ezek a környezet-specifikus értékek felülbírálják a címkével nem rendelkező megfelelő értékeket. Nem kell meghatároznia a környezettel kapcsolatos értékeket minden kulcshoz. Ha egy kulcs nem rendelkezik olyan értékkel, amely az aktuális környezetnek megfelelő címkével rendelkezik, akkor a címke nélküli értéket használja a rendszer.

## <a name="testing-in-different-environments"></a>Tesztelés különböző környezetekben

A különböző konfigurációs értékek teszteléséhez nyissa meg `launchSettings.json` a fájlt a `Properties` könyvtár alatt. Keresse meg `config` a bejegyzést `profiles`a alatt. A `environmentVariables` szakaszban állítsa be a `ASPNETCORE_ENVIRONMENT` változót a `Production`következőre:.

Az új értékek beállítása után hozza létre és futtassa az alkalmazást.

```dotnetcli
dotnet build
dotnet run
```

Webböngészővel navigáljon a következőhöz: `http://localhost:5000`. Figyelje meg, hogy a betűszín fekete színű.

![Éles konfigurációval futó webalkalmazás](media/labels-website-prod.png)

Most frissítse `launchSettings.json` a `ASPNETCORE_ENVIRONMENT` változó értékét a következőre: `Development`. Futtassa ismét az `dotnet run` parancsot. Megfigyelheti, hogy a betűszín most piros színnel jelenik meg. Ennek az az oka, `TestApp:Settings:FontColor` hogy az alkalmazás már használja a `Development` címkével ellátott értéket. Az összes többi konfigurációs érték ugyanaz marad, mint a termelési értékük.

![Fejlesztői konfigurációval futó webalkalmazás](media/labels-website-dev.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Konfiguráció a ASP.NET Coreban](/aspnet/core/fundamentals/configuration/)
