---
title: 'Oktatóanyag: Dinamikus konfiguráció használata .NET Core alkalmazásban'
titleSuffix: Azure App Configuration
description: Ebben az oktatóanyagban megtudhatja, hogyan frissítheti dinamikusan a .NET Core alkalmazások konfigurációs adatait
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
ms.openlocfilehash: afecc84748ae8ce85c07e3b482bd9b596bdca251
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75433678"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Oktatóanyag: Dinamikus konfiguráció használata .NET Core alkalmazásban

Az Alkalmazáskonfiguráció .NET Core ügyfélkönyvtár támogatja a konfigurációs beállítások igény szerinti frissítését anélkül, hogy egy alkalmazás újraindulna. Ez úgy valósítható meg, `IConfigurationRefresher` hogy először bekér egy `Refresh` példányt a konfigurációs szolgáltató beállításaiból, majd a kód bármely pontjára hívja az adott példányt.

Annak érdekében, hogy a beállítások naprakészek maradjanak, és ne legyen túl sok hívás a konfigurációs tárolóba, a rendszer minden beállításhoz gyorsítótárat használ. Amíg egy beállítás gyorsítótárazott értéke le nem jár, a frissítési művelet nem frissíti az értéket, még akkor sem, ha az érték megváltozott a konfigurációs tárolóban. Az alapértelmezett lejárati idő minden kérelem 30 másodperc, de szükség esetén felülbírálható.

Ez az oktatóanyag bemutatja, hogyan valósíthatja meg a dinamikus konfigurációs frissítéseket a kódban. A rövid útmutatókban bevezetett alkalmazásra épül. Mielőtt folytatna, először fejezze be [a .NET Core alkalmazás létrehozása az alkalmazás konfigurációjával.](./quickstart-dotnet-core-app.md)

Bármelyik kódszerkesztőt használhatja az oktatóanyag lépéseihez. [A Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség, amely windowsos, macOS és Linux platformokon érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a .NET Core alkalmazást úgy, hogy frissítse a konfigurációját az alkalmazáskonfigurációs tárolóban bekövetkezett változásokra válaszul.
> * Használja fel az alkalmazás legújabb konfigurációját.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag hoz a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazáskonfigurációból

Nyissa *meg Program.cs,* és frissítse a `System.Threading.Tasks` fájlt, hogy hivatkozást `AddAzureAppConfiguration` adjon a névtérre, `Refresh` adja meg a frissítési konfigurációt a metódusban, és indítsa el a manuális frissítést a módszerrel.

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

A `ConfigureRefresh` módszer segítségével adja meg a konfigurációs adatok frissítéséhez használt beállításokat az Alkalmazás konfigurációs tárolójával, amikor egy frissítési művelet aktiválódik. Egy példány `IConfigurationRefresher` lehet beolvasni `GetRefresher` a metódus hívási `AddAzureAppConfiguration` módszer a `Refresh` megadott beállításokat, és a módszer ebben a példányban lehet használni, hogy indítson egy frissítési művelet bárhol a kódban.
    
> [!NOTE]
> A gyorsítótár alapértelmezett lejárati ideje egy konfigurációs beállítás 30 másodperc, de felülbírálható a metódus hívásával a `SetCacheExpiration` beállítások inicializáló át a metódus argumentumot a `ConfigureRefresh` metódusnak.

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazáskonfigurációs tároló hozzáférési kulcsára. Ha a Windows parancssorát használja, futtassa a következő parancsot, és indítsa újra a parancssort a módosítás érvénybe léptetéséhez:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha windows PowerShellt használ, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    MacOS vagy Linux használata esetén futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. A konzolalkalmazás létrehozásához futtassa a következő parancsot:

        dotnet build

1. A sikeres build befejezése után futtassa a következő parancsot az alkalmazás helyi futtatásához:

        dotnet run

    ![A gyorsindítási alkalmazás helyi indítása](./media/quickstarts/dotnet-core-app-run.png)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza az **Összes erőforrás**lehetőséget, és válassza ki a rövid útmutatóban létrehozott Alkalmazáskonfigurációs tároló példányt.

1. Válassza a **Configuration Explorer**lehetőséget, és frissítse a következő kulcsok értékeit:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Beállítások:Üzenet | Az Azure App konfigurációjából származó adatok – frissítve |

1. Az Enter billentyűvel indítsa el a frissítést, és nyomtassa ki a frissített értéket a parancssorba vagy a PowerShell ablakba.

    ![A gyorsindítási alkalmazás helyi frissítése](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Mivel a gyorsítótár lejárati ideje 10 másodpercre volt állítva a `SetCacheExpiration` módszer rel, miközben megadta a frissítési művelet konfigurációját, a konfigurációs beállítás értéke csak akkor frissül, ha legalább 10 másodperc telt el az adott beállítás utolsó frissítése óta.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte, hogy a .NET Core alkalmazás dinamikusan frissítse a konfigurációs beállításokat az alkalmazáskonfigurációból. Ha meg szeretné tudni, hogyan használhatja az Azure felügyelt identitást az alkalmazáskonfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitásintegráció](./howto-integrate-azure-managed-service-identity.md)
