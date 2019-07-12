---
title: Oktatóanyag az Azure-alkalmazások konfigurálása dinamikus konfiguráció egy .NET Core-alkalmazás |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan dinamikus frissítése a .NET Core-alkalmazások konfigurációs adatai
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: 1649fefda5073761d616fc48c602cab84d293ed0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799972"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Oktatóanyag: A dinamikus konfiguráció használata egy .NET Core-alkalmazás

Az alkalmazás konfigurációs .NET Core ügyféloldali kódtár támogatja a konfigurációs beállításokat az igény szerinti készletét frissítése anélkül, hogy ez az alkalmazás újraindítása. Ez úgy első példányát implementálható `IConfigurationRefresher` a konfigurációszolgáltatót, és majd hívása az közül `Refresh` azon a példányon, bárhol a kódba.

Annak érdekében, hogy a beállítások frissítve, és ezáltal a konfigurációs tár túl sok hívás, gyorsítótár minden egyes beállítás szolgál. Mindaddig, amíg a gyorsítótárazott értéket adják lejárt, a frissítési művelet nem frissíti az értéket, akkor is, ha az értéke megváltozott a konfigurációs tárban. Az alapértelmezett lejárati időt, az egyes kérések 30 másodperc, de szükség esetén felülbírálható lesz.

Ez az oktatóanyag bemutatja, hogyan valósíthat meg dinamikus konfiguráció frissítéseit a kódban. Az alkalmazás a rövid útmutatók rendszerben bevezetett épül. A folytatás előtt Befejezés [hozzon létre egy .NET Core-alkalmazást az alkalmazás konfigurációs](./quickstart-dotnet-core-app.md) első.

Bármely Kódszerkesztő segítségével hajtsa végre a lépéseket ebben az oktatóanyagban. [A Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség, hogy az a Windows, macOS és Linux platformokon érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be alkalmazását az igény szerinti, egy alkalmazás a konfigurációs adattárolónál a konfiguráció frissítése.
> * A legfrissebb konfigurációt, az alkalmazás vezérlők helyezhet el.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz, telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Töltse be újra az alkalmazás konfigurációs adatait

Nyissa meg *Program.cs* és a frissítési konfiguráció megadásához a fájl frissítése a `AddAzureAppConfiguration` metódust, és az eseményindító manuális frissítés használatával a `Refresh` metódust.

```csharp
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        IConfigurationRefresher refresher = null;

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });
                    
                    _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
```

A `ConfigureRefresh` a beállításait, frissítse a konfigurációs adatokat az alkalmazás a konfigurációs adattárolónál a frissítési művelet akkor aktiválódik, amikor segítségével módszert. Egy példányát `IConfigurationRefresher` meghívásával lekérhető `GetRefresher` metódus a megadott beállításokkal `AddAzureAppConfiguration` metódust, és a `Refresh` metódus ezen a példányon felhasználható a frissítési művelet bárhol a kód aktiválása.
    
> [!NOTE]
> Egy konfigurációs beállítás alapértelmezett gyorsítótár lejárati idő pedig 30 másodperc, de meghívásával bírálható felül a `SetCacheExpiration` argumentumaként átadott a beállítások inicializáló metódust a `ConfigureRefresh` metódust.

## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. Nevű környezeti változó értéke **ConnectionString**, és állítsa be az alkalmazás a konfigurációs adattárolónál a hozzáférési kulcsot. Ha használja a Windows-parancssort, futtassa a következő parancsot, és a parancssort, a módosítás érvénybe léptetéséhez indítsa újra:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell használatakor a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Ha macOS vagy Linux használ, futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Futtassa a konzolalkalmazást hozhat létre a következő parancsot:

        dotnet build

1. A build sikeres befejezése után futtassa a következő parancsot az alkalmazás helyi futtatásához:

        dotnet run

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/dotnet-core-app-run.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **összes erőforrás**, és válassza ki a konfigurációs tár-példányt, amely a rövid útmutatóban létrehozott.

1. Válassza ki **konfigurációs Explorer**, és frissítse az értékeket a következő kulcsokat:

    | Kulcs | Value |
    |---|---|
    | TestApp:Settings:Message | Az Azure App-konfiguráció – frissített adatokat |

1. Nyomja le az Enter billentyűt a aktiválhatja, és nyomtassa ki a frissített értéket, a parancssort vagy a PowerShell ablakban.

    ![A rövid útmutató app frissítése helyi](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Mivel a gyorsítótár lejárati idő 10 másodperc használatával lett beállítva a `SetCacheExpiration` metódust a frissítési műveletet, a konfigurációs beállítás értéke konfigurációjának megadása közben csak frissül, ha a legutóbbi frissítés óta eltelt legalább 10 másodperc a beállítás.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure hozzáadott felügyeltszolgáltatás-identitás leegyszerűsítheti az Alkalmazáskonfigurációt hozzá, és az alkalmazás hitelesítőadat-kezelés javítására. Ismerje meg, hogyan használhatja az Alkalmazáskonfigurációt, folytassa az Azure CLI-minták.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)
