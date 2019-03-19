---
title: A Key Vault-támogatás hozzáadása az ASP.NET-projektben, Visual Studio használatával – az Azure Key Vault |} A Microsoft Docs
description: Ez az oktatóanyag segítségével elsajátíthatja a Key Vault támogatásának hozzáadása egy ASP.NET- vagy ASP.NET Core-webalkalmazás.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ghogen
ms.openlocfilehash: de849ae290228826ee500ae1c7e623210e585d34
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113248"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Key Vault hozzáadása a webalkalmazás a Visual Studio csatlakoztatott szolgáltatásai segítségével

Ebben az oktatóanyagban, megtudhatja, hogyan adhat hozzá, amit az Azure Key Vaulttal a webes projekt a Visual Studióban, a titkos kulcsok kezeléséhez, hogy az ASP.NET Core vagy bármilyen típusú ASP.NET-projekt indításához. A csatlakoztatott szolgáltatás használatával a Visual Studio 2017, a Visual Studio automatikusan adja hozzá a NuGet-csomagok és a konfigurációs beállításokat kell csatlakozni a Key Vault az Azure-ban is rendelkezhet. 

A módosításokat, hogy csatlakoztatott szolgáltatásai lehetővé teszi a projekt a Key Vault engedélyezése a részletekért lásd: [Key Vault csatlakoztatott szolgáltatás – Mi történt a saját ASP.NET 4.7.1 projekt](vs-key-vault-aspnet-what-happened.md) vagy [Key Vault csatlakoztatott szolgáltatás – Mi történt ASP.NET Core-projektemmel](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- A **Visual Studio 2017 15.7-es verziója**, telepített **webfejlesztési** számítási feladattal. [Ezt innen töltheti le](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- ASP.NET (és nem alapvető) szüksége lesz a .NET-keretrendszer 4.7.1 fejlesztői eszközök, amelyek alapértelmezés szerint nincs telepítve. A telepítéshez indítsa el a Visual Studio telepítőjét, válassza a **módosítás**, és válassza a **az egyes összetevők**, majd a jobb oldalon bontsa ki a **ASP.NET és webfejlesztési**, és válassza a **4.7.1 .NET-keretrendszer fejlesztői eszközök**.
- An ASP.NET 4.7.1 or ASP.NET Core 2.0 web project open.

## <a name="add-key-vault-support-to-your-project"></a>Key Vault támogatásának hozzáadása a projekthez

1. A **Megoldáskezelőben** válassza az **Add** > **Connected Service** (Hozzáadás, Csatlakoztatott szolgáltatás) lehetőséget.
   Megjelenik a Connected Service (Csatlakoztatott szolgáltatás) lap a projekthez adható szolgáltatásokkal.
1. Elérhető szolgáltatások menüjében válassza **biztonságos titkos kulcsok az Azure Key Vault**.

   !["Az Azure Key Vault biztonságos titkoskulcs" kiválasztása](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Ha bejelentkezett a Visual Studióba, és rendelkezik a fiókjához társított Azure-előfizetéssel, egy lap jelenik meg, amely az előfizetéseit tartalmazza egy legördülő listában. Győződjön meg arról, hogy be van jelentkezve a Visual studióba, és ugyanazt a fiókot használja az Azure-előfizetését, hogy a fiók be van jelentkezve a következővel.

1. Válassza ki az előfizetést szeretné használni, és kattintson az új vagy meglévő kulcstároló, vagy válassza a Szerkesztés hivatkozásra módosíthatja az automatikusan létrehozott nevet.

   ![Válassza ki előfizetését.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Írja be a Key vault használni kívánt nevét.

   ![Nevezze át a Key Vaultban, és válasszon ki egy erőforráscsoportot](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat egy automatikusan létrehozott egyedi névvel.  Ha szeretne létrehozni egy új csoportot egy másik névvel, használhatja a [az Azure Portal](https://portal.azure.com), majd zárja be a lapot, és töltse be újra az erőforráscsoportok listájának újraindítás.
1. Válassza ki a régiót, amelyben létrehozza a Key Vaultban. Ha webalkalmazása az Azure-ban üzemeltetett, válassza ki a régiót, amelyben az optimális teljesítmény webes alkalmazást.
1. Válasszon ki egy díjszabási modellt. További információkért lásd: [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/).
1. Kattintson az OK gombra, fogadja el a konfigurációs beállításokkal.
1. Válasszon **Hozzáadás** a Key Vault létrehozása. A létrehozási folyamat meghiúsulhat, ha úgy dönt, hogy olyan nevet, amely már használták.  Ha ez történik, használja a **szerkesztése** hivatkozás nevezze át a Key Vaultban, és próbálkozzon újra.

   ![Csatlakoztatott szolgáltatás hozzáadása a projekthez](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Ezután adjon hozzá egy titkos kulcsot a Key vaultban az Azure-ban. A portálon jó helyen lekéréséhez kattintson a kezelés titkos kulcsok a Key vaultban tárolt mutató hivatkozás. Ha már bezárta a lap, vagy a projekthez, navigálhat a a [az Azure portal](https://portal.azure.com) kiválasztásával **minden szolgáltatás**alatt **biztonsági**, válassza a **Key Vault**, majd válassza ki a létrehozott Key Vault.

   ![Navigálás a portálon](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. A kulcs a Key Vault szakaszban létrehozott tároló, válassza a **titkok**, majd **létrehozás/importálás**.

   ![Titkos kulcs létrehozás/importálás](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Adja meg a titkos kulcs, például a "MySecret" és a teszteléshez használhat bármilyen karakterlánc típusú értéket adjon meg hozzá, majd válassza a **létrehozás** gombra.

   ![Titkos kulcs létrehozása](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (nem kötelező) Adjon meg egy másik titkos, de ezúttal helyezzük el egy kategória szerint elnevezése "Titkos – MySecret". Ez a szintaxis adja meg a "Titok" kategória, amely tartalmazza a titkos kulcs "MySecret."
 
Most a kód a titkos kulcsokat is elérheti. A következő lépések eltérnek attól függően, hogy a ASP.NET 4.7.1- vagy ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>A titkos kód eléréséhez

1. A két nuget-csomagok telepítéséhez [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-kódtárakat.

2. Nyissa meg a Program.cs fájlt, és frissítse a kódot az alábbira: 
   ```
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```
3. Ezután nyissa meg a About.cshtml.cs fájlt, és írja be az alábbi kódot
   1. Ez Microsoft.Extensions.Configuration hivatkozást tartalmaz a utasítás használatával    
       ```
       using Microsoft.Extensions.Configuration
       ```
   2. Ez a konstruktor hozzáadása
       ```
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```
   3. Frissítse a OnGet módot. A helyőrző értékét az itt látható a fenti parancsok létrehozott titkos nevű frissítése
       ```
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Futtassa az alkalmazást helyileg navigáljon az oldalról. Meg kell beolvasni a titkos érték

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

További információ a Key Vault fejlesztői olvassa el a [Key Vault fejlesztői útmutató](key-vault-developers-guide.md)