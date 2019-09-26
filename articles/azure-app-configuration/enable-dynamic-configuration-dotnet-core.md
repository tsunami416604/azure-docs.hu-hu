---
title: Oktatóanyag az Azure-alkalmazások konfigurációjának dinamikus konfigurációjának használatához .NET Core-alkalmazásban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan frissítheti dinamikusan a .NET Core-alkalmazások konfigurációs információit
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
ms.openlocfilehash: e56aba81b2e6b8e66aeb2c3e5284843055713826
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316075"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Oktatóanyag: Dinamikus konfiguráció használata .NET Core-alkalmazásokban

Az App Configuration .NET Core ügyféloldali függvénytár támogatja az igény szerinti konfigurációs beállítások frissítését anélkül, hogy újra kellene indítani az alkalmazást. Ezt úgy teheti meg, hogy először beolvassa a konfigurációs szolgáltató beállításai közül a példányát `IConfigurationRefresher` , majd a kódban bárhol meghívja `Refresh` a példányt.

A beállítások frissítésének megtartásához és a konfigurációs tárolóhoz való túl sok hívás elkerüléséhez minden beállításhoz gyorsítótárat kell használni. Amíg a beállítás gyorsítótárazott értéke lejárt, a frissítési művelet nem frissíti az értéket, még akkor is, ha az érték módosult a konfigurációs tárolóban. Az egyes kérések alapértelmezett lejárati ideje 30 másodperc, de szükség esetén felül lehet bírálni.

Ez az oktatóanyag bemutatja, hogyan valósítható meg a dinamikus konfigurációs frissítések a kódban. Ez a gyors üzembe helyezési útmutatóban bemutatott alkalmazásra épül. A folytatás előtt fejezze be [a .net Core-alkalmazás létrehozása az alkalmazás konfigurációjával](./quickstart-dotnet-core-app.md) című részből.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség, amely a Windows, MacOS és Linux platformokon érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Beállíthatja, hogy az alkalmazás a konfigurációját egy igény szerinti alkalmazás-konfigurációs tárolóval frissítse.
> * Adja meg a legújabb konfigurációt az alkalmazás vezérlői között.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazás konfigurációjától

Nyissa meg a *program.cs* , és frissítse a fájlt, és `System.Threading.Tasks` adjon hozzá egy hivatkozást a névtérhez, `AddAzureAppConfiguration` és adja meg a frissítési konfigurációt a metódusban, valamint a manuális frissítést a `Refresh` metódus használatával.

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
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
}
```

A `ConfigureRefresh` metódussal adhatja meg azokat a beállításokat, amelyeket a konfigurációs adatainak az alkalmazás konfigurációs tárolójával való frissítéséhez használ a frissítési művelet elindításakor. A `IConfigurationRefresher` metódus Meghívási `GetRefresher` metódusával `AddAzureAppConfiguration` lekérhető egy példány, és az `Refresh` adott példány metódusa használható a kód bármely pontjára a frissítési művelet elindításához.
    
> [!NOTE]
> A konfigurációs beállítás alapértelmezett gyorsítótár-lejárati ideje 30 másodperc, de felülbírálható úgy, hogy meghívja `SetCacheExpiration` a metódust az inicializálási beállítások argumentumként `ConfigureRefresh` megadott metódusban.

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Futtassa a következő parancsot a konzol alkalmazás létrehozásához:

        dotnet build

1. A létrehozás sikeres befejezése után futtassa a következő parancsot az alkalmazás helyi futtatásához:

        dotnet run

    ![Gyorsindítás alkalmazás elindítása helyi](./media/quickstarts/dotnet-core-app-run.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás**lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza a **Configuration Explorer**lehetőséget, és frissítse a következő kulcsok értékeit:

    | Kulcs | Value |
    |---|---|
    | TestApp:Settings:Message | Adatok az Azure-alkalmazás konfigurációjában – frissítve |

1. Nyomja le az ENTER billentyűt a frissítés elindításához és a frissített érték kinyomtatásához a parancssorban vagy a PowerShell-ablakban.

    ![Rövid útmutató alkalmazás helyi frissítése](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Mivel a gyorsítótár lejárati ideje 10 másodpercre `SetCacheExpiration` van beállítva, miközben a metódust a frissítési művelethez beállította, a konfigurációs beállítás értéke csak akkor lesz frissítve, ha a legutóbbi frissítés óta legalább 10 másodperc eltelt. Ehhez a beállításhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure által felügyelt szolgáltatás-identitást adott hozzá, amellyel egyszerűbbé válik az alkalmazások konfigurációjának elérése, és javítható a hitelesítő adatok kezelése az alkalmazáshoz. Ha többet szeretne megtudni az alkalmazások konfigurációjának használatáról, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)
