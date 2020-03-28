---
title: 'A .NET-keretrendszer oktatóanyaga: dinamikus konfiguráció az Azure App konfigurációjában'
description: Ebben az oktatóanyagban megtudhatja, hogyan frissítheti dinamikusan a .NET Framework alkalmazások konfigurációs adatait az Azure App Configuration használatával.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7780bdbc92868f62e8d066d171b2a04fe06a981d
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80245803"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Oktatóanyag: Dinamikus konfiguráció használata .NET Framework alkalmazásban

Az Alkalmazáskonfiguráció .NET ügyfélkódtár támogatja a konfigurációs beállítások igény szerinti frissítését anélkül, hogy egy alkalmazás újraindulna. Ez úgy valósítható meg, `IConfigurationRefresher` hogy először bekér egy `Refresh` példányt a konfigurációs szolgáltató beállításaiból, majd a kód bármely pontjára hívja az adott példányt.

Annak érdekében, hogy a beállítások naprakészek maradjanak, és ne legyen túl sok hívás a konfigurációs tárolóba, a rendszer minden beállításhoz gyorsítótárat használ. Amíg egy beállítás gyorsítótárazott értéke le nem jár, a frissítési művelet nem frissíti az értéket, még akkor sem, ha az érték megváltozott a konfigurációs tárolóban. Az alapértelmezett lejárati idő minden kérelem 30 másodperc, de szükség esetén felülbírálható.

Ez az oktatóanyag bemutatja, hogyan valósíthatja meg a dinamikus konfigurációs frissítéseket a kódban. A rövid útmutatókban bevezetett alkalmazásra épül. Mielőtt folytatna, először fejezze be [a .NET Framework alkalmazás létrehozása az alkalmazás konfigurációjával.](./quickstart-dotnet-app.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a .
> * Adja meg a legújabb konfigurációt az alkalmazásban.
## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 vagy újabb](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazáskonfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. A következő kulcsérték-párok hozzáadásához válassza a **Configuration Explorer** > + Create**Key-value (Kulcsérték** **létrehozása)** > lehetőséget:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Beállítások:Üzenet | Az Azure App konfigurációjából származó adatok |

    Egyelőre hagyja üresen a **Címke** és **a Tartalomtípus** mezőt.

7. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-net-framework-console-app"></a>.NET Framework konzolalkalmazás létrehozása

1. Indítsa el a Visual Studio alkalmazást, és válassza az**Új** > **projekt** **fájlja** > lehetőséget.

1. Az **Új projekt létrehozása**csoportban szűrje a **Konzolprojekt** típusát, és kattintson a **Console App (.NET Framework)** elemre. Kattintson a **Tovább** gombra.

1. Az **Új projekt konfigurálása**mezőbe írja be a projekt nevét. A **Keretrendszer**csoportban válassza a **.NET Framework 4.7.1** vagy újabb lehetőséget. Kattintson **a Létrehozás gombra.**

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazáskonfigurációból
1. Kattintson a jobb gombbal a projektre, és válassza **a NuGet csomagok kezelése parancsot.** A **Tallózás** lapon keressen rá a *Microsoft.Extensions.Configuration.AzureAppConfiguration* NuGet csomagra a projektben. Ha nem találja, jelölje be az **Előzetes kiadás belefoglalása** jelölőnégyzetet.

1. Nyissa *meg a Program.cs,* és adjon hozzá hivatkozást a .NET Core alkalmazáskonfigurációs szolgáltatóhoz.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Adjon hozzá két változót a konfigurációval kapcsolatos objektumok tárolásához.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Frissítse `Main` a módszert az alkalmazáskonfigurációhoz való csatlakozáshoz a megadott frissítési beállításokkal.

    ```csharp
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
    ```
    A `ConfigureRefresh` módszer segítségével adja meg a konfigurációs adatok frissítéséhez használt beállításokat az Alkalmazás konfigurációs tárolójával, amikor egy frissítési művelet aktiválódik. Egy példány `IConfigurationRefresher` lehet beolvasni `GetRefresher` a metódus hívása `AddAzureAppConfiguration` metódusa `Refresh` a megadott beállításokat, és a módszer ebben a példányban lehet használni, hogy indítson egy frissítési művelet bárhol a kódban.

    > [!NOTE]
    > A gyorsítótár alapértelmezett lejárati ideje egy konfigurációs beállítás 30 másodperc, de felülbírálható a metódus hívásával a `SetCacheExpiration` beállítások inicializáló át a metódus argumentumot a `ConfigureRefresh` metódusnak.

1. Adjon hozzá `PrintMessage()` egy metódust, amely elindítja a konfigurációs adatok manuális frissítését az alkalmazáskonfigurációból.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazáskonfigurációs tároló hozzáférési kulcsára. Ha a Windows parancssorát használja, futtassa a következő parancsot, és indítsa újra a parancssort a módosítás érvénybe léptetéséhez:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha windows PowerShellt használ, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. A Visual Studio újraindítása a módosítás érvénybe léptetéséhez. 

1. A konzolalkalmazás létrehozásához és futtatásához nyomja le a Ctrl+ F5 billentyűkombinációt.

    ![Alkalmazás indítása helyi](./media/dotnet-app-run.png)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza az **Összes erőforrás**lehetőséget, és válassza ki a rövid útmutatóban létrehozott Alkalmazáskonfigurációs tároló példányt.

1. Válassza a **Configuration Explorer**lehetőséget, és frissítse a következő kulcsok értékeit:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Beállítások:Üzenet | Az Azure App konfigurációjából származó adatok – frissítve |

1. A futó alkalmazásban nyomja le az Enter billentyűt a frissítés elindításához, és nyomtassa ki a frissített értéket a parancssori vagy a PowerShell-ablakban.

    ![Alkalmazásfrissítés helyi](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Mivel a gyorsítótár lejárati ideje 10 másodpercre volt állítva a `SetCacheExpiration` módszer rel, miközben megadta a frissítési művelet konfigurációját, a konfigurációs beállítás értéke csak akkor frissül, ha legalább 10 másodperc telt el az adott beállítás utolsó frissítése óta.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte, hogy a . Ha meg szeretné tudni, hogyan használhatja az Azure felügyelt identitást az alkalmazáskonfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitásintegráció](./howto-integrate-azure-managed-service-identity.md)
