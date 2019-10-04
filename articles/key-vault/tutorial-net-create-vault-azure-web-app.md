---
title: Oktatóanyag – Azure Key Vault használata egy Azure-webalkalmazással a .NET-ben | Microsoft Docs
description: Ebben az oktatóanyagban egy ASP.NET Core-alkalmazást konfigurál, hogy beolvassa a titkos kulcsot a kulcstartóból.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 84256d79ec543d038b4d3d3f3dc6901bbd003871
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003362"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Oktatóanyag: Azure Key Vault használata Azure-webalkalmazással a .NET-ben

A Azure Key Vault segítségével megvédheti a titkokat, például az API-kulcsokat és az adatbázis-kapcsolatok karakterláncait. Hozzáférést biztosít az alkalmazásaihoz, szolgáltatásaihoz és informatikai erőforrásaihoz.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre olyan Azure-webalkalmazást, amely az Azure Key vaultból tud adatokat olvasni. A folyamat felügyelt identitásokat használ az Azure-erőforrásokhoz. További információ az Azure-webalkalmazásokról: [Azure app Service](../app-service/overview.md).

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Adjon hozzá egy titkos kulcsot a kulcstartóhoz.
> * Titkos kulcs lekérése a kulcstartóból.
> * Hozzon létre egy Azure-webalkalmazást.
> * Felügyelt identitás engedélyezése a webalkalmazáshoz.
> * Rendeljen engedélyt a webalkalmazáshoz.
> * Futtassa a webalkalmazást az Azure-ban.

Mielőtt elkezdené, olvassa el [Key Vault alapvető fogalmakat](basic-concepts.md). 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Windows esetén: [.net Core 2,1 SDK vagy újabb](https://www.microsoft.com/net/download/windows)
* Mac rendszer esetén: [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)
* Windows, Mac és Linux rendszerekhez:
  * [Git](https://git-scm.com/downloads)
  * Ehhez az oktatóanyaghoz helyileg kell futtatnia az Azure CLI-t. Telepítenie kell az Azure CLI 2.0.4 vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Tudnivalók a felügyeltszolgáltatás-identitásról

A Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, így azok nem jelennek meg a kódban. A kulcsok lekéréséhez azonban hitelesítenie kell Azure Key Vault. Key Vault hitelesítéséhez szüksége lesz egy hitelesítő adatra. Ez egy klasszikus rendszerindítási dilemma. Managed Service Identity (MSI) ezt a problémát úgy oldja meg, hogy egy rendszerindító identitást biztosít, amely leegyszerűsíti a folyamatot.

Ha az MSI-t egy Azure-szolgáltatáshoz (például Azure Virtual Machines, Azure App Service vagy Azure Functions) engedélyezi, az Azure létrehoz egy [egyszerű szolgáltatást](basic-concepts.md). Az MSI ezt a szolgáltatást a Azure Active Directory (Azure AD) szolgáltatás példányán végzi el, és az egyszerű szolgáltatásnév hitelesítő adatait beinjektálja a példányba.

![MSI-diagram](media/MSI.png)

A hozzáférési jogkivonat beszerzéséhez a kód meghívja az Azure-erőforráson elérhető helyi metaadat-szolgáltatást. A kód a helyi MSI-végponttól kapott hozzáférési jogkivonatot használja a Azure Key Vault szolgáltatásba való hitelesítéshez.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal.

Ezután válasszon ki egy erőforráscsoport-nevet, és töltse ki a helyőrzőt. A következő példa létrehoz egy erőforráscsoportot az USA nyugati régiójában:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Ezt az erőforráscsoportot használja az oktatóanyag során.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A Key Vault az erőforráscsoporthoz való létrehozásához adja meg a következő adatokat:

* Key Vault neve: 3 – 24 karakterből álló karakterlánc, amely csak számokat (0-9), betűket (a-z, A-z) és kötőjeleket (-) tartalmazhat.
* Erőforráscsoport neve
* Helyen **USA nyugati régiója**

Az Azure CLI-ben adja meg a következő parancsot:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Ezen a ponton az Azure-fiókja az egyetlen, amely jogosult a műveletek végrehajtására ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Most hozzáadhat egy titkos kulcsot. Lehet, hogy egy SQL-kapcsolódási sztring vagy bármely más olyan információ, amelyet a biztonságos és elérhetővé kell tenni az alkalmazás számára.

Ha a **AppSecret**nevű kulcstartóban szeretne titkos kulcsot létrehozni, írja be a következő parancsot: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ez a titok a **keresési kifejezésként**értéket tárolja.

Ha a titkos kulcsban található értéket egyszerű szövegként szeretné megtekinteni, írja be a következő parancsot:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Ez a parancs a titkos adatokat jeleníti meg, beleértve az URI-t. 

A fenti lépések végrehajtása után rendelkeznie kell egy, a kulcstartóban tárolt titkos kulcshoz tartozó URI-val. Jegyezze fel ezt az információt az oktatóanyag későbbi használatához. 

## <a name="create-a-net-core-web-app"></a>.NET Core-Webalkalmazás létrehozása

.NET Core-webalkalmazás létrehozásához és az Azure-ba való közzétételéhez kövesse az [ASP.net Core Webalkalmazás létrehozása az Azure-ban](../app-service/app-service-web-get-started-dotnet.md)című témakör utasításait. 

Az alábbi videót is megtekintheti:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Nyissa meg és szerkessze a megoldást

1. Nyissa meg > a Pages**About.cshtml.cs** fájlt.

1. A következő NuGet-csomagok telepítése:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importálja a következő kódot a *About.cshtml.cs* fájlba:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   A AboutModel osztály kódjának így kell kinéznie:

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

1. A Visual Studio 2019 > főmenüjében **kattintson a hibakeresés** **indítása**lehetőségre hibakeresés nélkül. 
1. A böngészőben nyissa meg a **Névjegy** lapot.  
    Megjelenik az **AppSecret** értéke.

## <a name="enable-a-managed-identity"></a>Felügyelt identitás engedélyezése

Azure Key Vault lehetővé teszi a hitelesítő adatok és egyéb titkos kódok biztonságos tárolását, de a kódnak hitelesítenie kell a Key Vault, hogy beolvassa őket. A [felügyelt identitások az Azure-erőforrások áttekintésében](../active-directory/managed-identities-azure-resources/overview.md) segít a probléma megoldásában azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosítanak az Azure ad-ben. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vaultt, anélkül, hogy meg kellene adni a hitelesítő adatokat a kódban.

Az Azure CLI-ben az alkalmazás identitásának létrehozásához futtassa a assign-Identity parancsot:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Cserélje \<le a YourAppName > a közzétett alkalmazás nevére az Azure-ban.  
    Ha például a közzétett alkalmazás neve **MyAwesomeapp.azurewebsites.net**volt, cserélje le \<a YourAppName >t a **MyAwesomeapp**.

Jegyezze fel `PrincipalId` az alkalmazást az Azure-ban való közzétételekor. Az 1. lépésben szereplő parancs kimenetének a következő formátumúnak kell lennie:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Az ebben az eljárásban szereplő parancs egyenértékű a [Azure Portalra](https://portal.azure.com) való váltással, és a webalkalmazás tulajdonságainál az **identitás/rendszer** által hozzárendelt beállítás bekapcsolva értékre vált.

## <a name="assign-permissions-to-your-app"></a>Engedélyek kiosztása az alkalmazáshoz

Cserélje \<le a YourKeyVaultName >t a kulcstartó nevére, és cserélje le \<a PrincipalId > értéket a **PrincipalId** értékére a következő parancsban:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Ez a parancs megadja az App Service-nek a Key Vault beolvasási és **listázási** műveletének identitását (MSI).

## <a name="publish-the-web-app-to-azure"></a>A webalkalmazás közzététele az Azure-ban

Ismét közzéteheti a webalkalmazását az Azure-ban annak ellenőrzéséhez, hogy az élő webalkalmazás képes-e a titkos érték beolvasására.

1. A Visual Studióban válassza a **key-vault-dotnet-core-quickstart** projektet.
2. Válassza a **Publish** > **Start** (Közzététel > Indítás) lehetőséget.
3. Kattintson a **Létrehozás** gombra.

Az alkalmazás futtatásakor látnia kell, hogy le tudja olvasni a titkos értékét.

Most sikeresen létrehozott egy webalkalmazást a .NET-ben, amely a Key vaultból tárolja és beolvassa a titkos kulcsait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rájuk szükség, törölheti a virtuális gépet és a kulcstartót.

## <a name="next-steps"></a>További lépések

>[!div class="nextstepaction"]
>[Azure Key Vault fejlesztői útmutató](key-vault-developers-guide.md)
