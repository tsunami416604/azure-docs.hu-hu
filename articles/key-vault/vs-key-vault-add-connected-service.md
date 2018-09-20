---
title: A Key Vault-támogatás hozzáadása az ASP.NET-projektben a Visual Studio használatával |} A Microsoft Docs
description: Ez az oktatóanyag segítségével elsajátíthatja a Key Vault támogatásának hozzáadása egy ASP.NET- vagy ASP.NET Core-webalkalmazás.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 9cf49ae97da3bf67300bdc222c86bb712aeaed37
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465792"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Key Vault hozzáadása a webalkalmazás a Visual Studio csatlakoztatott szolgáltatásai segítségével

Ebben az oktatóanyagban, megtudhatja, hogyan adhat hozzá, amit az Azure Key Vaulttal a webes projekt a Visual Studióban, a titkos kulcsok kezeléséhez, hogy az ASP.NET Core vagy bármilyen típusú ASP.NET-projekt indításához. A csatlakoztatott szolgáltatás használatával a Visual Studio 2017, a Visual Studio automatikusan adja hozzá a NuGet-csomagok és a konfigurációs beállításokat kell csatlakozni a Key Vault az Azure-ban is rendelkezhet. 

A módosításokat, hogy csatlakoztatott szolgáltatásai lehetővé teszi a projekt a Key Vault engedélyezése a részletekért lásd: [Key Vault csatlakoztatott szolgáltatás – Mi történt a saját ASP.NET 4.7.1 projekt](vs-key-vault-aspnet-what-happened.md) vagy [Key Vault csatlakoztatott szolgáltatás – Mi történt ASP.NET Core-projektemmel](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- **A Visual Studio 2017 verzió 15.7** együtt a **webfejlesztés** számítási feladattal. [Töltse le most](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- ASP.NET (és nem alapvető) szüksége lesz a .NET-keretrendszer 4.7.1 fejlesztői eszközök, amelyek alapértelmezés szerint nincs telepítve. A telepítéshez indítsa el a Visual Studio telepítőjét, válassza a **módosítás**, és válassza a **az egyes összetevők**, majd a jobb oldalon bontsa ki a **ASP.NET és webfejlesztési**, és válassza a **4.7.1 .NET-keretrendszer fejlesztői eszközök**.
- Egy ASP.NET 4.7.1 vagy ASP.NET Core 2.0-s webes projekt megnyitása.

## <a name="add-key-vault-support-to-your-project"></a>Key Vault támogatásának hozzáadása a projekthez

1. A **Megoldáskezelőben**, válassza a **Hozzáadás** > **csatlakoztatott szolgáltatás**.
   A projekthez is hozzáadhat szolgáltatásokat a csatlakoztatott szolgáltatás oldalon jelenik meg.
1. Elérhető szolgáltatások menüjében válassza **biztonságos titkos kulcsok az Azure Key Vault**.

   !["Az Azure Key Vault biztonságos titkoskulcs" kiválasztása](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Ha már bejelentkezett a Visual Studióban, és a fiókhoz társított Azure-előfizetéssel rendelkezik, megjelenik egy oldal, és a egy legördülő lista az összes előfizetés. Győződjön meg arról, hogy be van jelentkezve a Visual studióba, és ugyanazt a fiókot használja az Azure-előfizetését, hogy a fiók be van jelentkezve a következővel.

1. Válassza ki az előfizetést szeretné használni, és kattintson az új vagy meglévő kulcstároló, vagy válassza a Szerkesztés hivatkozásra módosíthatja az automatikusan létrehozott nevet.

   ![Válassza ki előfizetését.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Írja be a Key vault használni kívánt nevét.

   ![Nevezze át a Key Vaultban, és válasszon ki egy erőforráscsoportot](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat egy automatikusan létrehozott egyedi neve.  Ha szeretne létrehozni egy új csoportot egy másik névvel, használhatja a [az Azure Portal](https://portal.azure.com), majd zárja be a lapot, és töltse be újra az erőforráscsoportok listájának újraindítás.
1. Válassza ki a régiót, amelyben létrehozza a Key Vaultban. Ha webalkalmazása az Azure-ban üzemeltetett, válassza ki a régiót, amelyben az optimális teljesítmény webes alkalmazást.
1. Válasszon ki egy díjszabási modellt. További információkért lásd: [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/).
1. Kattintson az OK gombra, fogadja el a konfigurációs beállításokkal.
1. Válasszon **Hozzáadás** a Key Vault létrehozása. A létrehozási folyamat meghiúsulhat, ha úgy dönt, hogy olyan nevet, amely már használták.  Ha ez történik, használja a **szerkesztése** hivatkozás nevezze át a Key Vaultban, és próbálkozzon újra.

   ![Csatlakoztatott szolgáltatás hozzáadása a projekthez](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Ezután adjon hozzá egy titkos kulcsot a Key vaultban az Azure-ban. A portálon jó helyen lekéréséhez kattintson a kezelés titkos kulcsok a Key vaultban tárolt mutató hivatkozás. Ha már bezárta a lap, vagy a projekthez, navigálhat a a [az Azure portal](https://portal.azure.com) kiválasztásával **minden szolgáltatás**alatt **biztonsági**, válassza a **Key Vault**, majd válassza ki az újonnan létrehozott Key Vault.

   ![Navigálás a portálon](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. A kulcs a Key Vault szakaszban létrehozott tároló, válassza a **titkok**, majd **létrehozás/importálás**.

   ![Titkos kulcs létrehozás/importálás](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Adja meg a titkos kulcs, például a "MySecret", és adjon neki teszteléshez használhat bármilyen karakterlánc típusú értéket, majd válassza a **létrehozás** gombra.

   ![Titkos kulcs létrehozása](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (nem kötelező) Adjon meg egy másik titkos, de ezúttal helyezzük el egy kategória szerint elnevezése "Titkos – MySecret". Ez a szintaxis adja meg a "Titok" kategória, amely tartalmazza a titkos kulcs "MySecret."
 
Most a kód a titkos kulcsokat is elérheti. A következő lépések eltérnek attól függően, hogy a ASP.NET 4.7.1- vagy ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>A titkos kód (az ASP.NET Core-projektek) eléréséhez

A kapcsolat a Key Vault beállítása indításkor implementáló osztályt [Microsoft.AspNetCore.Hosting.IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) használatával, amely az indítási viselkedést leírt [egy külső alkalmazás javítása az ASP.NET Core és IHostingStartup szerelvény](/aspnet/core/fundamentals/host/platform-specific-configuration). Az indítási osztályt használ két, a Key Vault kapcsolati adatait tartalmazó környezeti változókat: ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED, állítsa igaz értékre, és az ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT, a kulcs beállítása Tároló URL-címe. A launchsettings.json fájlba kerülnek hozzáadásra, amikor futtatja a **csatlakoztatott szolgáltatás hozzáadása** folyamat.

A titkos kulcsok elérése:

1. A Visual Studióban, az ASP.NET Core-projektben most már hivatkozhat titkos adatokat az alábbi kifejezések használatával a kódban:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. .Cshtml lapon tegyük fel, hogy About.cshtml, adja hozzá a @inject irányelv tetején, a fájl egy változó beállításához használhatja a Key Vault-konfiguráció.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. Egy teszt, ellenőrizheti, hogy a titkos kód érhető el az egyik oldalra megjelenítésével. Használat @config a konfigurációs változója hivatkozni.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Hozhat létre és futtassa a webalkalmazást, navigáljon a névjegy lapra, és tekintse meg a "secret" értéket.

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>Hozzáférési kód a titkos kulcsokat (ASP.NET 4.7.1 projektek)

A kapcsolat a Key vault be van állítva a ConfigurationBuilder osztály, amely hozzá lett adva a web.config fájl futtatásakor információk a **csatlakoztatott szolgáltatás hozzáadása** folyamat.

A titkos kulcsok elérése:

1. Módosítsa a következőképpen web.config. A kulcsokat a helyőrzők, amely a Key Vault titkos értékeket az azurekeyvault értékre van ConfigurationBuilder lesz lecserélve.

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

1. A HomeController a névjegy vezérlő metódusban adja hozzá a következő sorokat beolvasni a titkos kulcsot, és tárolja a ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. A About.cshtml nézetben adja hozzá a következőt jeleníti meg a titkos kulcsot (csak tesztelés) értékét.

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Gratulálunk, mostantól meggyőződött róla, hogy a webalkalmazás használhat a Key Vault eléréséhez biztonságosan tárolt titkos kulcsok.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

További információ a Key Vault fejlesztői olvassa el a [Key Vault fejlesztői útmutató](key-vault-developers-guide.md)