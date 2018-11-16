---
title: Oktatóanyag – Azure Key Vault használata egy Azure-webalkalmazást a .NET-ben |} A Microsoft Docs
description: Oktatóanyag – ASP.NET core-alkalmazás titkos Key vault kulcsainak olvasásához konfigurálása
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: defe1a109381c7ee44c6fc5e5db4c6f6ecc5ac6f
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706840"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Oktatóanyag: Azure Key Vault használata a .NET-ben az Azure web app használatával

Az Azure Key Vault segítségével API-kulcsok például titkainak védelmére és adatbázis-kapcsolati karakterláncok. Ez hozzáférést biztosít az alkalmazások, szolgáltatások és informatikai erőforrásra lenne szükség.

Ebben az oktatóanyagban elsajátíthatja, hogyan tudja olvasni azok adatait egy Azure key vault az Azure-webalkalmazások létrehozása. A folyamat felügyelt identitásokat használ az Azure-erőforrásokhoz. Azure-alapú webes alkalmazások kapcsolatos további információkért lásd: [Azure Web Apps](../app-service/app-service-web-overview.md).

A cikk bemutatja, hogyan való:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Titkos kulcs tárolása a kulcstartóban.
> * Titkos kulcs lekérése a kulcstartóból.
> * Azure-webalkalmazás létrehozása.
> * [Felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a webalkalmazáshoz.
> * A szükséges engedélyek megadása a webalkalmazás számára az adatoknak a kulcstartóból való olvasásához.
> * Futtassa a webalkalmazást az Azure-ban.

A folytatás előtt olvassa el a [Key Vault alapvető fogalmait](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Előfeltételek

* Windows rendszeren:
  * [.NET Core 2.1 SDK vagy újabb](https://www.microsoft.com/net/download/windows)

* Mac gépen:
  * [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)

* Összes platform:
  * [Git](https://git-scm.com/downloads)
  * Azure-előfizetés <br />(Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.)
  * [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4-es vagy újabb, Windows, Mac és Linux rendszereken érhető el
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="managed-service-identity-and-how-it-works"></a>Felügyeltszolgáltatás-identitás- és annak működéséről

Az Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, nem a kódban. Azonban szüksége hitelesítéséhez az Azure Key Vaulttal a kulcsok lekéréséhez. Hitelesítést a Key Vault, a hitelesítő adatokat kell. Egy klasszikus bootstrap kapcsolatos dilemma megoldása. Felügyelt Felügyeltszolgáltatás-identitás (MSI) megoldja a problémát azáltal, hogy egy _identitás bootstrap_ , amely leegyszerűsíti a folyamatot.

Az Azure-szolgáltatások MSI engedélyezésekor (például: virtuális gépek, az App Service-ben vagy a függvények), az Azure létrehoz egy [szolgáltatásnév](key-vault-whatis.md#basic-concepts). MSI azért teszi ezt a szolgáltatást az Azure Active Directory (Azure AD)-példány, és be annak a példánynak a szolgáltatásnév hitelesítő adatok kódtárba.

![MSI-diagram](media/MSI.png)

Ezután a kód meghívja a egy helyi metadata szolgáltatás érhető el a az Azure-erőforráshoz hozzáférési jogkivonatot kapjon. A kód a helyi MSI_ENDPOINT-tól beszerzett hozzáférési jogkivonattal végzi el a hitelesítést az Azure Key Vault szolgáltatásban.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-bA az Azure CLI-vel, írja be:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal.
1. Válasszon egy erőforráscsoport-nevet, és töltse ki a helyőrzőt. A következő példa létrehoz egy erőforráscsoportot az USA nyugati régiójában:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Ez az oktatóanyag során erőforráscsoport használja.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Hozzon létre egy kulcstartót az erőforráscsoportban, adja meg a következő információkat:

* A Key vault neve: 3 – 24 karakter, amely csak számokat, betűket és kötőjeleket tartalmazhat karakterláncot (például: 0 – 9, a – z, A-Z, - és)
* Erőforráscsoport neve
* Hely: **USA nyugati RÉGIÓJA**

Adja meg az Azure CLI az alábbi parancsot:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Jelenleg az Ön Azure-fiókja az egyetlen fiók, amelyik jogosult műveleteket végrehajtani ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Most egy titkos kulcsot is hozzáadhat. Egy SQL-kapcsolati karakterlánc vagy bármely egyéb információkat, amelyek továbbra is szeretné, biztonságos és az alkalmazás számára elérhető lehet.

Írja be a következő parancsot a key vaultban titkos kulcs létrehozása nevű **AppSecret**. A titkos kód tárolja az értéket **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Egyszerű szövegként a titkos kódban tárolt érték megtekintése egyszerű, adja meg a következő parancsot:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Ez a parancs megjeleníti a titkos információkat, beleértve az URI-t is. A fenti lépések végrehajtása után rendelkeznie kell egy, a kulcstartóban tárolt titkos kulcshoz tartozó URI-val. Jegyezze fel ezt az információt. Egy későbbi lépésben szüksége lesz rá.

## <a name="create-a-net-core-web-app"></a>.NET Core-webalkalmazás létrehozása

Kövesse ezt [oktatóanyag](../app-service/app-service-web-get-started-dotnet.md) .NET Core-webalkalmazás létrehozása és **közzététele** azt az Azure-bA. A következő videót is megtekinthet:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Nyissa meg és szerkessze a megoldást

1. Keresse meg a **oldalak** > **About.cshtml.cs** fájlt.
2. Telepítse a NuGet-csomagok:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Importálja a About.cshtml.cs fájlban a következő kódot:

   ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

4. A kód a AboutModel osztály kell ehhez hasonló:

   ```
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
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. A Visual Studio 2017 főmenüjében válassza **Debug** > **Start** vagy hibakeresés nélkül. 
1. Amikor megjelenik a böngésző, lépjen az **About** (Névjegy) oldalra.
1. Megjelenik az **AppSecret** értéke.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Felügyelt identitás engedélyezése a webalkalmazáshoz

Az Azure Key Vault lehetővé teszi a biztonságos tárolása a hitelesítő adatokat és egyéb titkos adatait, de kérheti le azokat a Key Vault hitelesítenie kell a kódot. [Felügyelt identitások Azure-erőforrások áttekintő](../active-directory/managed-identities-azure-resources/overview.md) segít a probléma megoldásához, így az Azure automatikusan felügyelt identitást services, Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

1. Az Azure CLI, a hozzárendelés-azonosító parancs futtatásával hozzon létre a az alkalmazás identitását:

   ```azurecli

   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"

   ```

   >[!NOTE]
   >Csere \<YourAppName\> az Azure-on közzétett alkalmazás nevére. Ha a közzétett alkalmazás neve például **MyAwesomeapp.azurewebsites.net**, cserélje le \<YourAppName\> a **MyAwesomeapp**.

1. Jegyezze fel a `PrincipalId` az Azure-ban az alkalmazás közzétételekor. Az 1. lépésben a parancs kimenete a következő formátumban kell megadni:

   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
   ```

>[!NOTE]
>Az ebben az eljárásban használt parancs egyenértékű azzal, mintha megnyitná a [portált](https://portal.azure.com), és a webalkalmazás tulajdonságai között átállítaná az **Identitás / Rendszer által hozzárendelt** beállítást **Be** értékűre.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Engedélyek kiosztása az alkalmazásnak a Key Vault titkos kulcsainak olvasásához

Cserélje le \<YourKeyVaultName\> a kulcstartó nevét, és \<PrincipalId\> értékét a **PrincipalId** a következő parancsban:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Ez a parancs biztosítja az identitás (MSI) az alkalmazás service engedéllyel, hogy tegye **lekérése** és **lista** a kulcstartóra vonatkozó műveletek.

## <a name="publish-the-web-application-to-azure"></a>A webalkalmazás közzététele az Azure-ban

A webalkalmazás közzététele az Azure-ba, hogy ismét, hogy az élő webalkalmazását lehet beolvasni a titkos érték.

1. A Visual Studióban válassza a **key-vault-dotnet-core-quickstart** projektet.
2. Válassza a **Publish** > **Start** (Közzététel > Indítás) lehetőséget.
3. Kattintson a **Létrehozás** gombra.

Az alkalmazás futtatásakor megtekintheti, hogy a titkos érték tudja olvasni.

Most már sikeresen létrehozott egy webalkalmazást a .NET-ben tároló és a hozzá tartozó titkos kódok olvas be a Key Vault.

## <a name="next-steps"></a>További lépések

>[!div class="nextstepaction"]
>[Azure Key Vault fejlesztői útmutató](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-developers-guide)
