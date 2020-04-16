---
title: Oktatóanyag – Az Azure Key Vault használata egy Azure-webalkalmazással a .NET | Microsoft dokumentumok
description: Ebben az oktatóanyagban konfigurálja a ASP.NET alapalkalmazást a kulcstartó ból egy titkos olvasásra.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: bb907c809d411128ee799d5057379a2022144882
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422878"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Oktatóanyag: Az Azure Key Vault használata egy Azure-webalkalmazással a .NET-ben

Az Azure Key Vault segít megvédeni a titkos kulcsokat, például az API-kulcsokat és az adatbázis-kapcsolati karakterláncokat. Hozzáférést biztosít az alkalmazásokhoz, szolgáltatásokhoz és informatikai erőforrásokhoz.

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy Azure-webalkalmazást, amely adatokat olvashat egy Azure-kulcstartóból. A folyamat felügyelt identitásokat használ az Azure-erőforrásokhoz. Az Azure-webalkalmazásokról az [Azure App Service című](../../app-service/overview.md)témakörben talál további információt.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Titk a kulcstartóhoz.
> * Titkos kulcs lekérése a kulcstartóból.
> * Hozzon létre egy Azure-webalkalmazást.
> * Felügyelt identitás engedélyezése a webalkalmazáshoz.
> * Engedély hozzárendelése a webalkalmazáshoz.
> * Futtassa a webalkalmazást az Azure-on.

Mielőtt elkezdené, olvassa el [a Key Vault alapfogalmait.](basic-concepts.md) 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="prerequisites"></a>Előfeltételek

* Windows rendszeren: [.NET Core 2.1 SDK vagy újabb](https://www.microsoft.com/net/download/windows)
* Mac [használatra: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)
* Windows, Mac és Linux esetén:
  * [Git](https://git-scm.com/downloads)
  * Ez az oktatóanyag megköveteli, hogy az Azure CLI helyileg fusson. Az Azure CLI 2.0.4-es vagy újabb verziójának telepítve kell lennie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Tudnivalók a felügyeltszolgáltatás-identitásról

Az Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, így azok nem jelennek meg a kódban. Azonban a kulcsok lekéréséhez az Azure Key Vault ban kell hitelesítenie magát. A Key Vault hitelesítéséhez hitelesítő adatokra van szükség. Ez egy klasszikus bootstrap dilemma. A felügyelt szolgáltatásidentitás (MSI) úgy oldja meg ezt a problémát, hogy olyan _rendszerindítási identitást_ biztosít, amely leegyszerűsíti a folyamatot.

Ha engedélyezi az MSI-t egy Azure-szolgáltatáshoz, például az Azure virtuális gépekhez, az Azure App Service-hez vagy az Azure Functionshez, az Azure létrehoz egy [egyszerű szolgáltatást.](basic-concepts.md) AZ MSI ezt a szolgáltatás az Azure Active Directoryban (Azure AD) a szolgáltatás példányát, és beadja a szolgáltatás egyszerű hitelesítő adatait az adott példányban.

![MSI-diagram](../media/MSI.png)

Ezután egy hozzáférési jogkivonat beszerzése, a kód meghívja a helyi metaadat-szolgáltatás, amely elérhető az Azure-erőforrás. A kód a helyi MSI-végponttól kapott hozzáférési jogkivonatot használja az Azure Key Vault-szolgáltatás hitelesítéséhez.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal.

Ezután válassza ki az erőforráscsoport nevét, és töltse ki a helyőrzőt. A következő példa létrehoz egy erőforráscsoportot az USA nyugati régiójában:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Ezt az erőforráscsoportot használja ebben az oktatóanyagban.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Ha kulcstartót szeretne létrehozni az erőforráscsoportban, adja meg a következő információkat:

* Kulcstartó neve: 3–24 karakterből álló karakterlánc, amely csak számokat (0-9), betűket (a-z, A-z) és kötőjeleket (-) tartalmazhat.
* Erőforráscsoport neve
* Helyszín: **USA nyugati telephelye**

Az Azure CLI-ben adja meg a következő parancsot:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Ezen a ponton az Azure-fiók az egyetlen, amely jogosult műveletek végrehajtására az új tároló.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Most hozzáadhat egy titkot. Lehet, hogy egy SQL-kapcsolat ihúr, vagy bármely más információt, hogy meg kell tartani a biztonságos és az alkalmazás számára elérhető.

Ha egy **AppSecret**nevű titkos kulcsot szeretne létrehozni a key vaultban, írja be a következő parancsot: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ez a titkos tárolja az értéket **MySecret**.

Ha a titkos kulcsot egyszerű szövegként szeretné megtekinteni, írja be a következő parancsot:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Ez a parancs megjeleníti a titkos információkat, beleértve az URI-t is. 

A fenti lépések végrehajtása után rendelkeznie kell egy, a kulcstartóban tárolt titkos kulcshoz tartozó URI-val. Jegyezze fel ezt az információt az oktatóanyag későbbi felhasználása esetén. 

## <a name="create-a-net-core-web-app"></a>.NET Core webalkalmazás létrehozása

A .NET Core webalkalmazás létrehozásához és az Azure-ban való közzétételéhez kövesse a ASP.NET Core webalkalmazás létrehozása az [Azure-ban](../../app-service/app-service-web-get-started-dotnet.md)című útmutatóutasításait. 

Az alábbi videót is megtekintheti:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Nyissa meg és szerkessze a megoldást

1. Nyissa meg a **Pages** > **About.cshtml.cs** fájlt.

1. Telepítse a következő NuGet csomagokat:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importálja a következő kódot a *About.cshtml.cs* fájlba:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   Az AboutModel osztályban lévő kódnak így kell kinéznie:

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

1. A Visual Studio 2019 főmenüjében válassza a **Debug** > **Start**lehetőséget hibakereséssel vagy anélkül. 
1. A böngészőben lépjen a **Beszólás** lapra.  
    Megjelenik az **AppSecret** értéke.

## <a name="enable-a-managed-identity"></a>Felügyelt identitás engedélyezése

Az Azure Key Vault lehetővé teszi a hitelesítő adatok és egyéb titkos kulcsok biztonságos tárolásának módját, de a kódnak hitelesítenie kell magát a Key Vaultba azok lekéréséhez. [Az Azure-erőforrások felügyelt identitások áttekintése](../../active-directory/managed-identities-azure-resources/overview.md) segít megoldani ezt a problémát azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosít az Azure AD-ben. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault, anélkül, hogy a kódban hitelesítő adatok megjelenítése.

Az Azure CLI-ben az alkalmazás identitásának létrehozásához futtassa a hozzárendelés-identitás parancsot:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Cserélje \<le a YourAppName>-t az Azure-ban közzétett alkalmazás nevére.  
    Ha például a közzétett **MyAwesomeapp.azurewebsites.net**alkalmazásneve \<MyAwesomeapp.azurewebsites.net volt, cserélje le a YourAppName>-t **a MyAwesomeapp alkalmazásra.**

Jegyezze fel, `PrincipalId` hogy mikor teszi közzé az alkalmazást az Azure-ban. Az 1.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Az ebben az eljárásban a parancs egyenértékű az [Azure Portalra](https://portal.azure.com) való megtérésével, és a webalkalmazás tulajdonságaiban **a** Be értékre **rendelt Identitás / Rendszer** beállítás átállításával.

## <a name="assign-permissions-to-your-app"></a>Engedélyek hozzárendelése az alkalmazáshoz

Cserélje \<le a YourKeyVaultName>-t a \<kulcstartó nevére, és cserélje le az PrincipalId> a következő parancsban szereplő **PrincipalId** értékére:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Ez a parancs az alkalmazásszolgáltatás identitását (MSI) adja meg a kulcstartón végzett műveletek **leértékelésére** és **listázására.**

## <a name="publish-the-web-app-to-azure"></a>A webalkalmazás közzététele az Azure-ban

Tegye közzé a webalkalmazást ismét az Azure-ban, és ellenőrizze, hogy az élő webalkalmazás letudja-e kérni a titkos értéket.

1. A Visual Studióban válassza a **key-vault-dotnet-core-quickstart** projektet.
2. Válassza **a Közzététel** > **kezdete**lehetőséget.
3. Kattintson a **Létrehozás** gombra.

Az alkalmazás futtatásakor látnia kell, hogy képes lekérni a titkos értéket.

Most már sikeresen létrehozott egy webalkalmazást a .NET-ben, amely tárolja és lekéri a titkos kulcsait a kulcstartóból.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rájuk szükség, törölheti a virtuális gépet és a key vault.

## <a name="next-steps"></a>További lépések

>[!div class="nextstepaction"]
>[Azure Key Vault fejlesztői útmutató](developers-guide.md)
