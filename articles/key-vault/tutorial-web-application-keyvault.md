---
title: Titkos kulcs beolvasására kulcstároló Azure webes alkalmazások konfigurálása |} Microsoft Docs
description: Az oktatóanyag a titkos kulcs beolvasására kulcstároló ASP.Net core alkalmazások konfigurálása
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 04/16/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 4a765b314b9879877bb6ff926e4a6584456b7823
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Oktatóanyag: A titkos kulcs olvasni a Key Vault Azure webes alkalmazások konfigurálása

Ebben az oktatóanyagban, nyissa meg a szükséges lépések egy Azure webalkalmazás kiolvasni az információt a kezelt service identitásokkal kulcstároló keresztül. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy kulcstartót.
> * Titkos kulcs tárolása a Key Vault.
> * Hozzon létre egy Azure webes alkalmazást.
> * Felügyelt szolgáltatás-identitások engedélyezése
> * Az alkalmazás adatokat olvasni a kulcstárolónak a szükséges engedélyek megadására.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

A bejelentkezés az Azure-ban a parancssori felület, adhatja meg:

```azurecli
az login
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name ContosoResourceGroup --location eastus
```

Az imént létrehozott erőforráscsoport Ez az oktatóanyag során használatos.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

A kulcstároló következő az előző lépésben létrehozott erőforráscsoport létrehozása. Meg kell adnia néhány információt:

>[!NOTE]
> Az egész ebben az oktatóanyagban a kulcstároló neve "ContosoKeyVault" lesz, bár egy egyedi nevet kell használnia.

* Tároló neve **ContosoKeyVault**.
* Az erőforráscsoport neve **ContosoResourceGroup**.
* A hely **USA keleti régiója**.

```azurecli
az keyvault create --name '<YourKeyVaultName>' --resource-group ContosoResourceGroup --location eastus
```

Ez a parancs kimenetét mutatja az újonnan létrehozott kulcstároló tulajdonságainak. Jegyezze fel az alábbi két tulajdonságok:

* **A tároló neve**: A példában ez a **ContosoKeyVault**. A kulcstároló neve minden Key Vault parancsok fogja használni.
* **Tároló URI-ja**: A példában ez a https://<YourKeyVaultName>.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

>[!IMPORTANT]
> Ha a hiba a paraméter 'vault_name' meg kell felelnie a következő mintát: "^ [a-zA-Z0 – 9-] {3,24} $" a - name paraméter értéke nincs egyedi vagy -hosszú 24 3 alfanumerikus karakterekből álló karakterlánc nem felel meg.

Ezen a ponton az Azure-fiókjába az új tárolóhoz bármilyen műveletet végezhet egyetlen.

## <a name="add-a-secret-to-key-vault"></a>Kulcstároló titkos kulcs hozzáadása

Azt jelöli, ennek működéséről titkos kulcs ad hozzá. Sikerült az SQL kapcsolati karakterláncot vagy bármilyen egyéb olyan adatot, tartsa biztonságos helyen, de az alkalmazás számára elérhetővé tenni kívánt tárolásához. Ebben az oktatóanyagban a jelszó fogja meghívni **AppSecret** értékét fogja tárolni, és **MySecret** azt.

Írja be az alábbi parancsok futtatásával hozza létre a titkos kulcs a Key Vault nevű **AppSecret** értékét tárolja, amely **MySecret**:

```azurecli
az keyvault secret set --vault-name '<YourKeyVaultName>' --name 'AppSecret' --value 'MySecret'
```

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurecli
az keyvault secret show --name 'AppSecret' --vault-name '<YourKeyVaultName>'
```

Ez a parancs megjeleníti a titkos adatok, ideértve az URI azonosító. Az alábbi lépések elvégzése után egy URI-t egy titkos kulcsot az Azure Key Vault kell rendelkezniük. Jegyezze meg ezt az információt. Esetleg szükség lenne rá egy későbbi lépésben.

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Ebben a szakaszban egy ASP.NET MVC alkalmazás létrehozása, és telepítse azt az Azure-bA egy webalkalmazást. Azure Web Apps kapcsolatos további információkért lásd: [webalkalmazások áttekintése](../app-service/app-service-web-overview.md).

1. Hozzon létre egy projektet a Visual Studióban a **File > New > Project** (Fájl > Új > Projekt) lehetőség kiválasztásával. 

2. A **New Project** (Új projekt) párbeszédpanelen válassza a **Visual C# > Web > ASP.NET Core Web Application** (Visual C# > Web > ASP.NET Core-webalkalmazás) lehetőséget.

3. Adjon nevet az alkalmazásnak **WebKeyVault**, majd válassza ki **OK**.
   >[!IMPORTANT]
   > Adjon nevet az alkalmazás WebKeyVault, másolja és illessze be a kódját a névtér fog egyezni. Ha a hely bármi más szüksége lesz a kódot kell egyeznie a hely nevének módosításához.

    ![A New ASP.NET Project (Új ASP.NET-projekt) párbeszédpanel](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Bármilyen típusú ASP.NET Core-webalkalmazást üzembe helyezhet az Azure-ban. A jelen oktatóanyag esetében válassza ki a **webalkalmazás** sablont, és győződjön meg arról, hogy a hitelesítés beállítása **nem hitelesítési**.

    ![ASPNET nincs hitelesítés párbeszédpanel](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Kattintson az **OK** gombra.

6. Az ASP.NET Core-projekt létrehozása után megjelenik az ASP.NET Core kezdőlapja. Itt számos olyan erőforrásra mutató hivatkozást találhat, amelyek segítenek a munka megkezdésében.

7. A menüből válassza a **Debug > Start without Debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a webalkalmazás helyi futtatásához.

## <a name="modify-the-web-app"></a>A webalkalmazás módosítása

Két NuGet-csomagok, amelyet a webes alkalmazás telepítve van. A telepítendő őket kövesse az alábbi lépéseket:

1. A solution explorer kattintson a jobb gombbal a webhely nevét a.
2. Válassza ki **kezelése NuGet-csomagok megoldás...**
3. Jelölje be a keresőmezőbe melletti jelölőnégyzetet. **Előzetes tartalmazza**
4. Keresse meg a két NuGet-csomagok alábbi, és fogadja el, hogy a megoldás felvenni:

    * [Microsoft.Azure.Services.AppAuthentication (előzetes verzió)](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) -megkönnyíti a hozzáférési jogkivonatok az Azure-szolgáltatások hitelesítési forgatókönyvek beolvasása. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/2.4.0-preview) -Key Vault való kommunikáció metódust tartalmaz.

5. A Solution Explorer segítségével nyissa meg a `Program.cs` , és cserélje le a Program.cs fájl tartalmát az alábbira. Helyettesítő ```<YourKeyVaultName>``` a kulcstároló neve:

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
        namespace WebKeyVault
        {
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
             )
                .UseStartup<Startup>()
                .Build();
    
            private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
        }
        }
    ```

6. Keresse meg a Solution Explorer segítségével a **lapok** szakaszt, és nyissa meg a `About.cshtml`. Cserélje le a tartalmát **About.cshtml.cs** az alábbi kódra:

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. A fő menüből **Debug** > **indítása nélkül hibakeresés**. Amikor megjelenik a böngésző, navigáljon a **kapcsolatos** lap. Az értéke a AppSecret jelenik meg.

>[!IMPORTANT]
> Ha HTTP-hiba 502.5 - folyamat hibaüzenet ellenőrizze a megadott kulcstároló neve `Program.cs`

## <a name="publish-the-web-application-to-azure"></a>Tegye közzé a webalkalmazást az Azure-bA

1. Válassza ki a szerkesztő fent **WebKeyVault**.
2. Válassza ki **közzététele**.
3. Válassza ki **közzététel** újra.
4. Válassza ki **létrehozása**.

>[!IMPORTANT]
> Egy böngészőablakban megnyitja és egy 502.5 - folyamat hibája üzenet jelenik meg. Ez várható. Szüksége lesz a jogosultságokat az alkalmazás identitását megnyithassa a Key Vault.

## <a name="enable-managed-service-identity"></a>Felügyelt Szolgáltatásidentitás engedélyezése

Az Azure Key Vault biztonságosan tárolni a hitelesítő adatokat és egyéb kulcsok és titkos lehetőséget biztosít, de a kódot kell hitelesítenie magát a Key Vault kérheti le azokat. Felügyelt szolgáltatás identitásának (MSI) teszi egyszerűbbé válik a probléma megoldásához adjon az Azure-szolgáltatások automatikusan felügyelt identitást az Azure Active Directory (Azure AD). Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, többek között a Key Vault, anélkül, hogy a hitelesítő adatok a kódban a szolgáltatással való hitelesítésre szolgáló használhatja.

1. Térjen vissza az Azure parancssori felület
2. A hozzárendelés-azonosító parancsot hoz létre az ehhez az alkalmazáshoz:

```azurecli
az webapp assign-identity --name WebKeyVault --resource-group ContosoResourcegroup
```

>[!NOTE]
>Ez megegyezik a portál fog, és átvált a **identitás** való **a** a webes alkalmazás tulajdonságai.

## <a name="grant-rights-to-the-application-identity"></a>Az Alkalmazásidentitás GRANT jogosultságokkal

Az Azure portál használatával, nyissa meg a kulcstároló hozzáférési házirendeket, és hozzáférést saját kezűleg titkos felügyeleti a Key Vault. Ez lehetővé teszi az alkalmazás futtatásához a helyi fejlesztési számítógépén.

1. A Key vault a Keresés a **erőforrások keresése** párbeszédpanel az Azure portálon.
2. Válassza ki **hozzáférési házirendek**.
3. Válassza ki **új hozzáadása**, a a **titkos engedélyek** szakaszban válasszon **beolvasása** és **lista**.
4. Válassza ki **egyszerű válasszon**, és adja hozzá az alkalmazásidentitás. Ez lesz a neve megegyezik az alkalmazáséval.
5. Válasszon **Ok**

Most már Azure-ban és a szolgáltatásalkalmazás-identitásnak a fiók jogosult kiolvasni az információt a Key Vault. Ha frissítse a lapot a webhely kezdőlapja kell megjelennie. Ha **kapcsolatos**. Megjelenik a értéket a Key Vault tárolja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforráscsoport és az ahhoz tartozó összes erőforrást törölheti a **az csoport törlése** parancsot.

  ```azurecli
  az group delete -n ContosoResourceGroup
  ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Key Vault fejlesztői útmutatója](key-vault-developers-guide.md)
