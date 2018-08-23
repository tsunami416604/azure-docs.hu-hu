---
title: Azure-webalkalmazások konfigurálása a Key Vault titkos kulcsainak olvasásához – oktatóanyag | Microsoft Docs
description: 'Oktatóanyag: ASP.NET Core-alkalmazások konfigurálása a Key Vault titkos kulcsainak olvasásához'
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 91e2047998d6e743691821c631e15c94cd63cf15
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41918658"
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Oktatóanyag: Azure-webalkalmazások konfigurálása a Key Vault titkos kulcsainak olvasásához

Ebben az oktatóanyagban azokat a lépéseket ismerheti meg, amelyekkel Azure-webalkalmazásokat konfigurálhat a Key Vaultban tárolt adatok felügyeltszolgáltatás-identitások használatával való olvasására. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Key Vault létrehozása.
> * Titkos kulcs tárolása a Key Vaultban.
> * Azure-webalkalmazás létrehozása.
> * Felügyeltszolgáltatás-identitások engedélyezése.
> * A szükséges engedélyek megadása az alkalmazás számára adatoknak a Key Vaultból való olvasásához.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

Ha szeretne bejelentkezni az Azure-ba a parancssori felület használatával, írja be a következőt:

```azurecli
az login
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehoz egy *ContosoResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "ContosoResourceGroup" --location "East US"
```

Az oktatóanyag az imént létrehozott erőforráscsoport használja.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

A következő lépésben létre fog hozni egy Key Vaultot az előző lépésben létrehozott erőforráscsoportban. Jóllehet ebben az oktatóanyagban a Key Vault neve „ContosoKeyVault”, egyedi nevet kell használnia. Adja meg az alábbi információkat:

* Tároló neve: **ContosoKeyVault**.
* Erőforráscsoport neve: **ContosoResourceGroup**.
* Hely: **USA keleti régiója**.

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East US"
```

A parancs kimenete megjeleníti az újonnan létrehozott Key Vault tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

* **A tároló neve**: A példában ez a **ContosoKeyVault**. A Key Vault nevét fogja használni a Key Vaulthoz kapcsolódó parancshoz.
* **Tároló URI-ja**: A példában a https://<YourKeyVaultName>.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

>[!IMPORTANT]
> Ha megjelenik a „vault_name” paraméter hibás formátumát jelző hibaüzenet, mely szerint a paraméternek a „^[a-zA-Z0-9-]{3,24}$” mintának kell megfelelnie, akkor a -name paraméterérték nem egyedi, vagy nem egy 3–24 alfanumerikus karakterből álló sztring.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. Tárolhat egy SQL-kapcsolati sztringet, vagy bármely olyan adatot, amelyet biztonságosan kell tárolni, de elérhetővé kell tenni az alkalmazás számára. Ebben az oktatóanyagban a jelszó neve **AppSecret** lesz, és a **MySecret** értékét fogja tárolni.

Írja be az alábbi parancsokat, ha a **MySecret** értékét tároló titkos kulcsot szeretne létrehozni a Key Vaultban **AppSecret** névvel:

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "AppSecret" --value "MySecret"
```

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "ContosoKeyVault"
```

Ez a parancs megjeleníti a titkos információkat, beleértve az URI-t is. A fenti lépések elvégzése után rendelkeznie kell egy Azure Key Vaultban tárolt titkos kulcshoz tartozó URI-val. Jegyezze fel ezt az információt. Egy későbbi lépésben szüksége lesz rá.

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Ebben a szakaszban létrehoz egy ASP.NET MVC-alkalmazást, és üzembe helyezi webalkalmazásként az Azure-ban. Az Azure Web Apps szolgáltatással kapcsolatban [a Web Apps áttekintésével](../app-service/app-service-web-overview.md) foglalkozó témakörben tekinthet meg további információt.

1. Hozzon létre egy projektet a Visual Studióban a **File > New > Project** (Fájl > Új > Projekt) lehetőség kiválasztásával. 

2. A **New Project** (Új projekt) párbeszédpanelen válassza a **Visual C# > Web > ASP.NET Core Web Application** (Visual C# > Web > ASP.NET Core-webalkalmazás) lehetőséget.

3. Adja az alkalmazásnak a **WebKeyVault** nevet, majd kattintson az **OK** gombra.
   >[!IMPORTANT]
   > Az alkalmazásnak a WebKeyVault nevet kell adni, hogy a kód, amelyet másol és beilleszt, megegyezzen a névtérrel. Ha más nevet adott a webhelynek, módosítania kell a kódot, hogy az megegyezzen a webhely nevével.

    ![A New ASP.NET Project (Új ASP.NET-projekt) párbeszédpanel](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Bármilyen típusú ASP.NET Core-webalkalmazást üzembe helyezhet az Azure-ban. Ennél az oktatóanyagnál válassza a **Web Application** (Webalkalmazás) sablont, és ügyeljen arra, hogy a hitelesítés beállítása **No Authentication** (Nincs hitelesítés) legyen.

    ![Az ASP.NET „Nincs hitelesítés” párbeszédpanele](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Kattintson az **OK** gombra.

6. Az ASP.NET Core-projekt létrehozása után megjelenik az ASP.NET Core kezdőlapja. Itt számos olyan erőforrásra mutató hivatkozást találhat, amelyek segítenek a munka megkezdésében.

7. A menüből válassza a **Debug > Start without Debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a webalkalmazás helyi futtatásához.

## <a name="modify-the-web-app"></a>A webalkalmazás módosítása

A webalkalmazásnak két NuGet-csomagot kell telepítenie. A telepítésükhöz kövesse az alábbi lépéseket:

1. A Solution Explorer (Megoldáskezelő) felületén kattintson a jobb gombbal a webhely nevére.
2. Válassza a **Manage NuGet packages for solution...** (Megoldás NuGet-csomagjainak kezelése…) elemet.
3. Jelölje be a keresőmező melletti következő jelölőnégyzetet: **Include prerelease** (Előzetes verzió is).
4. Keresse meg az alábbi két NuGet-csomagot, és fogadja el a megoldáshoz való hozzáadásukat:

    * [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) – a segítségével egyszerűen lekérheti a hozzáférési jogkivonatokat a szolgáltatás és Azure-szolgáltatás közötti hitelesítési forgatókönyvekhez. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) – metódusokat tartalmaz a Key Vaulttal való kommunikációhoz.

5. Nyissa meg a `Program.cs` fájlt a Solution Explorer (Megoldáskezelő) használatával, és cserélje le a Program.cs fájl tartalmát az alábbi kódra. A ```<YourKeyVaultName>``` értéket cserélje le a Key Vault nevére:

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
            ).UseStartup<Startup>()
             .Build();

           private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
         }
    }
    ```

6. A Solution Explorer (Megoldáskezelő) segítségével lépjen a **Pages** részhez, és nyissa meg az `About.cshtml` fájlt. Cserélje le az **About.cshtml.cs** fájl tartalmát az alábbi kódra:

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

7. A főmenüben válassza a **Debug** > **Start without Debugging** (Hibakeresés > Indítás hibakeresés nélkül) elemet. Amikor megjelenik a böngésző, lépjen a **Névjegy** oldalra. Megjelenik az AppSecret értéke.

>[!IMPORTANT]
> Ha a HTTP Error 502.5 - Process Failure (feldolgozási hiba) üzenetet
> > kapja, ellenőrizze a(z) `Program.cs` helyen megadott Key Vault-nevet.

## <a name="publish-the-web-application-to-azure"></a>A webalkalmazás közzététele az Azure-ban

1. Válassza a szerkesztő felett található **WebKeyVault** elemet.
2. Kattintson a **Publish** (Közzététel), majd a **Start** (Indítás) gombra.
3. Hozzon létre egy új **App Service-t**, majd kattintson a **Publish** (Közzététel) gombra.
4. Kattintson a **Létrehozás** gombra.

>[!IMPORTANT]
> Megnyílik egy böngészőablak, és megjelenik az „502.5 – Folyamathiba” üzenet. Ez a várható eredmény. Meg kell adnia az alkalmazásidentitás számára a jogosultságokat a Key Vaultban tárolt titkos kulcsok olvasásához.

## <a name="enable-managed-service-identity"></a>Felügyeltszolgáltatás-identitás engedélyezése

Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. A felügyeltszolgáltatás-identitás (MSI) segít leegyszerűsíteni ezt a problémát, mivel az Azure-szolgáltatások számára egy automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD-ben). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

1. Térjen vissza az Azure CLI-hez.
2. Futtassa az identitás hozzárendelésére szolgáló parancsot az alkalmazás identitásának létrehozásához:

```azurecli
az webapp identity assign --name "WebKeyVault" --resource-group "ContosoResourcegroup"
```

>[!NOTE]
>Ez a parancs egyenértékű azzal, mintha megnyitná a portált, és a webalkalmazás tulajdonságai között átállítaná a **Felügyeltszolgáltatás-identitás** beállítást **Be** értékűre.

## <a name="grant-rights-to-the-application-identity"></a>Jogosultságok biztosítása az alkalmazásidentitás számára

Az Azure Portalon nyissa meg a Key Vault hozzáférési szabályzatait, és biztosítson magának a titkos kulcsok felügyeletét lehetővé tevő hozzáférést a Key Vaulthoz. Ez lehetővé teszi az alkalmazásnak a helyi fejlesztési számítógépen való futtatását.

1. Keresse meg a Key Vaultot az Azure Portal **Erőforrások keresése** párbeszédpanelén.
2. Válassza a **Hozzáférési szabályzatok** lehetőséget.
3. Válassza az **Új hozzáadása** elemet, a **Titkos kód engedélyei** szakaszban pedig válassza a **Lekérdezés** és a **Lista** lehetőséget.
4. Válassza az **Egyszerű szolgáltatás kiválasztása** lehetőséget, majd adja hozzá az alkalmazásidentitást. Ugyanaz lesz a neve, mint az alkalmazásnak.
5. Kattintson az **OK** gombra.

Az Azure-fiókja és az alkalmazásidentitás most már jogosultságokkal rendelkeznek az adatok Key Vaultból való beolvasására. Amikor frissíti az oldalt, a webhely kezdőlapjának kell megjelennie. Ha a **Névjegy** elemet választja, látni fogja a Key Vaultban tárolt értéket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez használja az **az group delete** parancsot.

  ```azurecli
  az group delete -n "ContosoResourceGroup"
  ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Key Vault fejlesztői útmutató](key-vault-developers-guide.md)