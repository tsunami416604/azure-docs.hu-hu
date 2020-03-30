---
title: Környezetenkénti konfiguráció használata
titleSuffix: Azure App Configuration
description: Címkék használata környezetenkénti konfigurációs értékek megadásához
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056801"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Címkék használata különböző konfigurációk engedélyezéséhez különböző környezetekben

Számos alkalmazásnak különböző konfigurációkat kell használnia a különböző környezetekben. Tegyük fel, hogy egy alkalmazás konfigurációs értékkel rendelkezik, amely meghatározza a háttéradatbázishoz használandó kapcsolati karakterláncot. Az alkalmazás fejlesztői az éles környezetben használtadatbázistól eltérő adatbázist használnak. Az alkalmazás által használt adatbázis-kapcsolati karakterláncnak meg kell változnia, ahogy az alkalmazás a fejlesztésről az éles környezetre vált.

Az Azure App konfigurációja, *címkék* használatával különböző értékeket határozhat meg ugyanahhoz a kulcshoz. Megadhat például egyetlen kulcsot különböző értékekkel a *Fejlesztés* és *a Termelés*területén. Megadhatja, hogy melyik címkét (ek) kell betölteni, amikor csatlakozik az alkalmazás konfigurációjához.

Ennek a funkciónak a bemutatásához módosítjuk a rövid útmutatóban létrehozott [webalkalmazást: Hozzon létre egy ASP.NET Core alkalmazást az Azure App Configuration alkalmazással,](./quickstart-aspnet-core-app.md) hogy különböző konfigurációs beállításokat használjon a fejlesztéshez és az éles környezethez. A folytatás előtt fejezze be a rövid útmutatót.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Címke megadása konfigurációs érték hozzáadásakor

Az Azure Portalon lépjen be a **Configuration Explorer** be, és keresse meg a *TestApp:Settings:FontColor* kulcsot, amelyet a rövid útmutatóban hozott létre. Válassza ki a helyi menüt, majd kattintson az **Érték hozzáadása parancsra.**

> [!div class="mx-imgBorder"]
> ![Érték hozzáadása menüelem](media/labels-add-value.png)

A **Hozzáadott érték** képernyőn írjon be egy **piros** **értéket** és egy **fejlesztési** **címkét.** Hagyja üresen **a Tartalomtípust.** Kattintson az **Alkalmaz** gombra.

## <a name="loading-configuration-values-with-a-specified-label"></a>Konfigurációs értékek betöltése megadott címkével

Alapértelmezés szerint az Azure App Konfigurációs csak betölti a konfigurációs értékek címke nélkül. Ha címkéket adott meg a konfigurációs értékekhez, akkor meg kell adnia az alkalmazáskonfigurációhoz való csatlakozáskor használandó címkét(eke-ket).

Az utolsó szakaszban létrehozott egy másik *konfigurációs* értéket a fejlesztési környezethez. A `HostingEnvironment.EnvironmentName` változó segítségével dinamikusan meghatározhatja, hogy az alkalmazás jelenleg melyik környezetben fut. További információ: [Több környezet használata ASP.NET Core alkalmazásban](/aspnet/core/fundamentals/environments)című témakörben olvashat.

Töltse be a konfigurációs értékeket az aktuális környezetnek `Select` megfelelő címkével a környezet nevének átadva a metódusba:

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
> A fenti kódrészlet betölti az alkalmazáskonfigurációs kapcsolati karakterláncot egy környezeti változóból, amelyet úgy hívnak, hogy. `AppConfigConnectionString` Győződjön meg arról, hogy ez a környezeti változó megfelelően van beállítva.

A `Select` metódus neve kétszer. Az első alkalommal címke nélküli konfigurációs értékeket tölt be. Ezután betölti a konfigurációs értékeket az aktuális környezetnek megfelelő címkével. Ezek a környezetspecifikus értékek felülírják a megfelelő értékeket címke nélkül. Nem kell minden kulcshoz környezetspecifikus értékeket megadnia. Ha egy kulcs nem rendelkezik az aktuális környezetnek megfelelő címkével rendelkező értékkel, akkor a címke nélküli értéket használja a program.

## <a name="testing-in-different-environments"></a>Tesztelés különböző környezetekben

A különböző konfigurációs értékek `launchSettings.json` teszteléséhez `Properties` nyissa meg a könyvtár alatti fájlt. Keresse `config` meg `profiles`a bejegyzést a alatt. A `environmentVariables` szakaszban állítsa `ASPNETCORE_ENVIRONMENT` a `Production`változót a-ra.

Az új értékek beállítása, az alkalmazás létrehozása és futtatása.

```dotnetcli
dotnet build
dotnet run
```

Keresse meg a böngészőt a alkalmazáshoz. `http://localhost:5000` Észre fogod venni, hogy a betűszín fekete.

![Éles konfigurációval futó webalkalmazás](media/labels-website-prod.png)

Most `launchSettings.json` frissítse a `ASPNETCORE_ENVIRONMENT` változó `Development`beállítását . Futtassa ismét az `dotnet run` parancsot. Észre fogod venni, hogy a betűszín most piros. Ennek az az oka, `TestApp:Settings:FontColor` hogy az `Development` alkalmazás most a címkével ellátott értékét használja. Az összes többi konfigurációs érték ugyanaz marad, mint a termelési értékeik.

![Fejlesztői konfigurációval futó webalkalmazás](media/labels-website-dev.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Konfiguráció ASP.NET Core-ban](/aspnet/core/fundamentals/configuration/)
