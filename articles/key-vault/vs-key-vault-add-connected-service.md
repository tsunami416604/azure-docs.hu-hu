---
title: Key Vault-támogatás hozzáadása az ASP.NET projektben, Visual Studio használatával |} Microsoft Docs
description: Ez az oktatóanyag segítségével megtudhatja, hogyan Key Vault támogatásához egy ASP.NET- vagy ASP.NET Core webalkalmazáshoz.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 9f0cc6ee06042948442aace05d56fcffa3742a8d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Key Vault hozzáadása a webes alkalmazás a Visual Studio kapcsolódó szolgáltatások segítségével

Ebben az oktatóanyagban, megtudhatja, hogyan könnyen hozzáadása mindent megtalál az Azure Key Vault használatával webes projektek a Visual Studio, a titkos kulcsok kezeléséhez, függetlenül attól, hogy az ASP.NET Core vagy bármilyen ASP.NET-projekt indításához. Visual Studio 2017 kapcsolódó szolgáltatások szolgáltatásának használatát, akkor a Visual Studio automatikusan adja hozzá a NuGet-csomagok és a konfigurációs beállításokat, csatlakoznia kell az Azure Key Vault. 

A által végzett módosításokat, hogy kapcsolódó szolgáltatások a projekt engedélyezze a Key Vault a részletekért lásd: [kulcsot tároló csatlakoztatott szolgáltatás – Mi történt az ASP.NET 4.7.1 projekt](vs-key-vault-aspnet-what-happened.md) vagy [kulcsot tároló kapcsolódó szolgáltatás – Mi történt az ASP.NET Core projekt](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- **A Visual Studio 2017 verzió 15.7** rendelkező a **webes fejlesztési** alkalmazások és szolgáltatások telepítése. [Most töltse le](https://aka.ms/vsdownload).
- Az ASP.NET (és nem alapvető) szüksége van a .NET-keretrendszer 4.7.1 fejlesztői eszközök, amelyek alapértelmezés szerint nincs telepítve. A telepítéshez, indítsa el a Visual Studio telepítő, válassza a **módosítás**, és válassza a **az egyes összetevők**, majd a jobb oldalon bontsa ki a **ASP.NET és a webes fejlesztési**, és válassza a **4.7.1 .NET-keretrendszer fejlesztői eszközök**.
- Az ASP.NET 4.7.1 vagy az ASP.NET Core 2.0 webes projekt megnyitása.

## <a name="add-key-vault-support-to-your-project"></a>Key Vault-támogatás hozzáadása a projekthez

1. A **Megoldáskezelőben**, válassza a **Hozzáadás** > **kapcsolódó szolgáltatás**.
   A csatlakoztatott szolgáltatás lapján jelenik meg, amelyen szolgáltatások adhat hozzá a projekthez.
1. A választható szolgáltatások menüben válassza a **biztonságos titkokat az Azure Key Vault**.

   ![Válassza ki a "Biztonságos titkokat az Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Ha most jelentkezik be a Visual Studio, és a fiókjához társított Azure-előfizetéssel rendelkezik, megjelenik egy oldal, a legördülő lista az Ön előfizetéseit.
1. Válassza ki az előfizetést szeretné használni, és válassza az új vagy meglévő Key Vault, vagy válassza ki a szerkesztési hivatkozáshoz, az automatikusan létrehozott nevet módosításához.

   ![Válassza ki előfizetését.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Írja be a Key vault használni kívánt nevét.

   ![Nevezze át a Key Vault, és válasszon egy erőforráscsoportot](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Válasszon ki egy meglévő erőforráscsoportot, vagy válasszon egy automatikusan létrehozott unqiue nevű egy új létrehozásához.  Ha azt szeretné, más néven új csoport létrehozásához, használhatja a [Azure Portal](https://portal.azure.com), majd zárja be a lapot, és töltse be újra az erőforráscsoportok listáját indítsa újra.
1. Válassza ki a régiót, amelyben a kulcstároló létrehozásához. Ha a webalkalmazás az Azure-ban, a régiót, amelyen az optimális teljesítmény érdekében webalkalmazás kiválasztása
1. Válasszon ki egy árképzési modellt. További információkért lásd: [Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).
1. Kattintson az OK gombra, fogadja el a konfigurációs beállításokkal.
1. Válasszon **Hozzáadás** a kulcstároló létrehozásához. A létrehozási folyamat meghiúsulhat, ha úgy dönt, hogy a nevet, amely már használták.  Ha ez előfordul, a **szerkesztése** hivatkozás nevezze át a Key Vault, majd próbálja meg újból.

   ![Csatlakoztatott szolgáltatás hozzáadása a projekthez](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Most adja hozzá a titkos kulcsot a kulcstároló, az Azure-ban. Ahhoz, hogy a megfelelő helyen, a portálon, a felügyelete titkos kulcsok a Key Vault tárolt hivatkozásra kattintania. Ha korábban bezárta a lap, vagy a projekt, lépjen vele a [Azure-portálon](https://portal.azure.com) kiválasztásával **minden szolgáltatás**a **biztonsági**, válassza a **Key Vault**, majd válassza ki az imént létrehozott kulcstároló.

   ![Navigáljon a portálon](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. A kulcs a Key Vault szakaszban létrehozott tároló, válassza a **titkok**, majd **létrehozása vagy importálása**.

   ![Titkos kulcs létrehozása és importálása](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Adjon meg egy titkos kulcsot, például a "MySecret", és adjon neki egy tesztet, bármilyen karakterlánc típusú értéket, majd kattintson a **létrehozása** gombra.

   ![Titkos kulcs létrehozása](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (választható) Adja meg egy másik titkos, de ezúttal tegye közzé a kategória elnevezése "Titkok--MySecret". Ez a szintaxis adja meg egy kategóriát "Kulcsok", amely tartalmazza a titkos kulcs "MySecret."
 
Most a titkos kód végezheti el. A következő lépések eltérnek attól függően, hogy használja-e ASP.NET 4.7.1 vagy az ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>Hozzáférés a titkos kód (az ASP.NET Core projektek)

1. A Visual Studióban az ASP.NET Core projektben hivatkozhat ezeknek a kulcsoknak a kódot az alábbi kifejezések használatával:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. .Cshtml lapon fel About.cshtml, vegye fel a @inject irányelv tetejénél található a fájl egy változó beállításához használhatja a Key Vault konfigurációjának eléréséhez.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. Egy teszt, ellenőrizheti, hogy a titkos kulcs értéke érhető el az oldalak egyik megjelenítésével. Használjon @config config változó hivatkozni.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Build és a webalkalmazás futtatásához, nyissa meg a jogi tudnivalók megjelenítése Névjegy lapot, és tekintse meg a "secret" értéket.

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>A titkos kód eléréséhez (ASP.NET 4.7.1 projektek)

1. Az alábbiak szerint módosítsa a Web.config fájlban. A kulcsokban helyőrzők, amely a Key Vault titkos kulcsainak értékeivel AzureKeyVault ConfigurationBuilder váltja fel.

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. A HomeController a jogi tudnivalók megjelenítése Névjegy vezérlő metódusban adja hozzá a következő sorokat beolvasni a titkos kulcsot, és tárolja a ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. A About.cshtml nézetben adja hozzá a következő, a titkos kulcsát (csak tesztelés) értékének megjelenítésére.

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Gratulálunk, mostantól meggyőződött róla, hogy a webalkalmazás biztonságosan tárolt titkos kulcsok eléréséhez Key Vault használhatja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, törölje a csoportot. Ez a Key Vault és a kapcsolódó erőforrások törlése. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a Key Vault fejlesztői ehhez beolvassa a [Key Vault fejlesztői útmutatója](key-vault-developers-guide.md)