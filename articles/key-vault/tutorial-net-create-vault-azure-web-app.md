---
title: Oktatóanyag – Azure Key Vault használata egy Azure-webalkalmazást a .NET-ben |} A Microsoft Docs
description: Ebben az oktatóanyagban konfigurál, olvassa el a titkos kulcs a key vaultból egy ASP.NET core-alkalmazást.
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 2c2bb3e4064294bb8d4a63b009069fd6834ca31e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370890"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Oktatóanyag: Az Azure Key Vault használata a .NET-keretrendszerben Azure web app használatával

Az Azure Key Vault segítségével API-kulcsok például titkainak védelmére és adatbázis-kapcsolati karakterláncok. Ez hozzáférést biztosít az alkalmazások, szolgáltatások és informatikai erőforrásra lenne szükség.

Ebben az oktatóanyagban elsajátíthatja, hogyan tudja olvasni azok adatait egy Azure key vault az Azure-webalkalmazások létrehozása. A folyamat felügyelt identitásokat használ az Azure-erőforrásokhoz. Azure-alapú webes alkalmazások kapcsolatos további információkért lásd: [Azure App Service](../app-service/overview.md).

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Titkos kód hozzáadása a kulcstartóhoz.
> * Titkos kulcs lekérése a kulcstartóból.
> * Hozzon létre egy Azure-webalkalmazást.
> * A webalkalmazás egy felügyelt identitás engedélyezése.
> * A webalkalmazáshoz tartozó engedélyeket rendeljenek.
> * A webalkalmazás futtatása az Azure-ban.

Mielőtt elkezdené, olvassa el a [Key Vault alapvető fogalmait](key-vault-whatis.md#basic-concepts). 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* A Windows: [.NET Core SDK-t 2.1-es vagy újabb](https://www.microsoft.com/net/download/windows)
* Mac számítógépen: [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)
* Windows, Mac és Linux:
  * [Git](https://git-scm.com/downloads)
  * Ehhez az oktatóanyaghoz az Azure CLI helyi futtatása. Rendelkeznie kell az Azure CLI 2.0.4-es vagy újabb verziója van telepítve. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](https://review.docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Tudnivalók a felügyeltszolgáltatás-identitásról

Az Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, azok nem jelennek meg a kódot. Azonban szüksége hitelesítéséhez az Azure Key Vaulttal a kulcsok lekéréséhez. Hitelesítést a Key Vault, a hitelesítő adatokat kell. Egy klasszikus bootstrap kapcsolatos dilemma megoldása. Felügyelt Felügyeltszolgáltatás-identitás (MSI) megoldja a problémát azáltal, hogy egy _identitás bootstrap_ , amely leegyszerűsíti a folyamatot.

Ha engedélyezi az MSI egy Azure-szolgáltatás, például az Azure Virtual Machines, az Azure App Service vagy az Azure Functions, az Azure létrehoz egy [szolgáltatásnév](key-vault-whatis.md#basic-concepts). MSI azért teszi ezt a szolgáltatást az Azure Active Directory (Azure AD)-példány, és egyszerű szolgáltatás hitelesítő adatai kódtárba be annak a példánynak.

![MSI-diagram](media/MSI.png)

Ezután a hozzáférési jogkivonatot kapjon a kód meghívja egy helyi metaadat-szolgáltatás, amely az Azure-erőforrás érhető el. A kód a hozzáférési jogkivonatot, amely a helyi MSI-végpontról lekérdezi azt egy Azure Key Vault szolgáltatással való hitelesítésre használja.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal.

Ezután válassza ki az erőforráscsoport nevét, és töltse ki a helyőrzőt. A következő példa létrehoz egy erőforráscsoportot az USA nyugati régiójában:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Ez az oktatóanyag során erőforráscsoport használja.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Hozzon létre egy kulcstartót az erőforráscsoportban, adja meg a következő információkat:

* A Key vault name: egy karakterlánc 3 – 24 karakter, amely csak számokat (0 – 9) tartalmazhatnak betűket (a – z, A – Z), és kötőjelet (-)
* Erőforráscsoport neve
* Hely: **USA nyugati régiója**

Adja meg az Azure CLI az alábbi parancsot:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Ezen a ponton az Azure-fiókkal az egyetlen, amely az új tárolón műveletek végrehajtásához engedélyezett.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Most egy titkos kulcsot is hozzáadhat. Egy SQL-kapcsolati karakterlánc vagy bármely egyéb információkat, amelyek továbbra is szeretné, biztonságos és az alkalmazás számára elérhető lehet.

A key vaultban titkos kulcs létrehozása nevű **AppSecret**, adja meg a következő parancsot: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

A titkos kód tárolja az értéket **MySecret**.

A titkos kulcsot pedig egyszerű szövegként szerepel érték megtekintéséhez írja be a következő parancsot:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Ez a parancs megjeleníti a titkos információk, beleértve az URI-t. 

A fenti lépések végrehajtása után rendelkeznie kell egy, a kulcstartóban tárolt titkos kulcshoz tartozó URI-val. Jegyezze meg ezt az információt az oktatóanyag későbbi használatra. 

## <a name="create-a-net-core-web-app"></a>.NET Core-webalkalmazás létrehozása

.NET Core-webalkalmazás létrehozásához, és tegye közzé az Azure-ba, kövesse a [ASP.NET Core-webalkalmazás létrehozása az Azure-ban](../app-service/app-service-web-get-started-dotnet.md). 

Az alábbi videót is megtekintheti:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Nyissa meg és szerkessze a megoldást

1. Nyissa meg a **oldalak** > **About.cshtml.cs** fájlt.

1. Telepítse a NuGet-csomagok:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importálja a következő kódot a *About.cshtml.cs* fájlt:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   A kód a AboutModel osztály kell kinéznie:

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. A Visual Studio 2017 főmenüjében válassza **Debug** > **Start**, vagy anélkül hibakeresés. 
1. A böngészőben nyissa meg a **kapcsolatos** lapot.  
    Megjelenik az **AppSecret** értéke.

## <a name="enable-a-managed-identity"></a>Egy felügyelt identitás engedélyezése

Az Azure Key Vault lehetővé teszi a biztonságos tárolása a hitelesítő adatokat és egyéb titkos adatait, de kérheti le azokat a Key Vault hitelesítenie kell a kódot. [Felügyelt identitások Azure-erőforrások áttekintő](../active-directory/managed-identities-azure-resources/overview.md) segít a probléma megoldásához, így az Azure automatikusan felügyelt identitást services, Azure AD-ben. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a Key Vault, beleértve a hitelesítő adatok megjelennek a kód nélkül bármely szolgáltatással való hitelesítésre.

Az Azure CLI-ben az identitás, az alkalmazás létrehozásához, a parancsot hozzárendelés-azonosító:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Cserélje le \<YourAppName > az Azure-on közzétett alkalmazás nevére.  
    Ha a közzétett alkalmazás neve például **MyAwesomeapp.azurewebsites.net**, cserélje le \<YourAppName > a **MyAwesomeapp**.

Jegyezze fel a `PrincipalId` az Azure-ban az alkalmazás közzétételekor. Az 1. lépésben a parancs kimenete a következő formátumban kell megadni:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>A parancs az eljárás megegyezik a fog a [az Azure portal](https://portal.azure.com) és váltás a **identitás / a rendszer által hozzárendelt** beállítást **a** a webalkalmazásban tulajdonságok.

## <a name="assign-permissions-to-your-app"></a>Engedélyek hozzárendelése az alkalmazáshoz

Cserélje le \<YourKeyVaultName > a key vaultban, és cserélje le a nevű \<PrincipalId > értékét a **PrincipalId** az alábbi parancsban:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Ez a parancs biztosítja az identitás (MSI), az app service engedélye jelenleg nincs **első** és **lista** műveleteket a key vaultban.

## <a name="publish-the-web-app-to-azure"></a>A webalkalmazás közzététele az Azure-ban

A webalkalmazás közzététele az Azure-bA még egyszer ellenőrizni, hogy az élő webalkalmazását lehet beolvasni a titkos érték.

1. A Visual Studióban válassza a **key-vault-dotnet-core-quickstart** projektet.
2. Válassza a **Publish** > **Start** (Közzététel > Indítás) lehetőséget.
3. Kattintson a **Létrehozás** gombra.

Az alkalmazás futtatásakor megtekintheti, hogy a titkos érték tudja olvasni.

Most sikeresen létrehozott egy webalkalmazást a .NET-ben, amely tárolja, és beolvassa a titkos kulcsok a key vaultból.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége, törölheti a virtuális gép és a key vaultban.

## <a name="next-steps"></a>További lépések

>[!div class="nextstepaction"]
>[Azure Key Vault fejlesztői útmutató](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
