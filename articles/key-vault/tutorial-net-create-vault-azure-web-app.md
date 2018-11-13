---
title: Oktatóanyag – Az Azure Key Vault használata az Azure Web App-pal .NET-en | Microsoft Docs
description: 'Oktatóanyag: ASP.NET Core-alkalmazások konfigurálása a Key Vault titkos kulcsainak olvasásához'
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
ms.openlocfilehash: 9cc22e158a9473b7b60f7e8bcb57174abc1fb8cc
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218552"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>Oktatóanyag: Az Azure Key Vault használata az Azure Web App-pal .NET-en

Az Azure Key Vault segít megvédeni a titkos kulcsokat, például az API-kulcsokat, valamint az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges adatbázis-kapcsolati sztringeket.

Ebben az oktatóanyagban azokat a lépéseket ismerheti meg, amelyekkel Azure-webalkalmazásokat konfigurálhat az Azure Key Vaultban tárolt adatok olvasására felügyelt identitásokkal Azure-erőforrások számára. Ez az oktatóanyag az [Azure Web Apps](../app-service/app-service-web-overview.md) szolgáltatáson alapul. A továbbiakban az alábbiakat ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Titkos kulcs tárolása a kulcstartóban.
> * Titkos kulcs lekérése a kulcstartóból.
> * Azure-webalkalmazás létrehozása.
> * [Felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a webalkalmazáshoz.
> * A szükséges engedélyek megadása a webalkalmazás számára az adatoknak a kulcstartóból való olvasásához.
> * A webalkalmazás futtatása az Azure-ban

A folytatás előtt tekintse át az [alapvető fogalmakat](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Előfeltételek

* Windows rendszeren:
  * [.NET Core 2.1 SDK vagy újabb](https://www.microsoft.com/net/download/windows)

* Mac gépen:
  * Lásd a [Visual Studio for Mac újdonságait](https://visualstudio.microsoft.com/vs/mac/) bemutató cikket.

* Összes platform:
  * Git ([letöltés](https://git-scm.com/downloads)).
  * Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
  * Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4-es vagy újabb verziója. Ez elérhető Windows, Mac és Linux rendszerekhez.
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Mi az a Managed Service Identity, és hogyan működik?
 A folytatás előtt ismerkedjünk meg az MSI-vel. Az Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, így azok nem találhatók meg a kódban, azonban a lekérésükhöz hitelesítés szükséges az Azure Key Vaultban. A Key Vaultban való hitelesítéshez hitelesítő adatokra van szükség. Klasszikus rendszerindítási probléma. Az MSI az Azure és az Azure AD révén egy „rendszerindítási identitást” biztosít, amellyel sokkal egyszerűbb nekilátni a feladatoknak.

Lássuk, hogyan működik! Ha engedélyezi az MSI-t egy Azure-szolgáltatás, például a Virtual Machines, az App Service vagy a Functions számára, az Azure létrehoz egy [szolgáltatásnevet](key-vault-whatis.md#basic-concepts) a szolgáltatás Azure Active Directoryban található példánya számára, majd beszúrja a szolgáltatásnév hitelesítő adatait a szolgáltatás példányába. 

![MSI](media/MSI.png)

Következő lépésként a kód meghív egy, az Azure-erőforráson elérhető helyi metaadat-szolgáltatást a hozzáférési jogkivonat beszerzéséhez.
A kód a helyi MSI_ENDPOINT-tól beszerzett hozzáférési jogkivonattal végzi el a hitelesítést az Azure Key Vault szolgáltatásban. 

Kezdjünk is neki az oktatóanyagnak!

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Válasszon egy erőforráscsoport-nevet, és töltse ki a helyőrzőt.
A következő példa létrehoz egy erőforráscsoportot az USA nyugati régiójában:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

A cikkben végig az imént létrehozott erőforráscsoportot használjuk majd.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A következő lépésben létre fogunk hozni egy kulcstartót az előző lépésben létrehozott erőforráscsoportban. Adja meg az alábbi információkat:

* A kulcstartó neve: A név 3–24 karakter hosszúságú sztring lehet, és csak a következőket tartalmazhatja: 0–9, a–z, A–Z, és -.
* Az erőforráscsoport neve.
* Hely: **West US**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Jelenleg az Ön Azure-fiókja az egyetlen fiók, amelyik jogosult műveleteket végrehajtani ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. Tárolhat SQL-kapcsolati sztringeket vagy bármely olyan adatot, amelyet biztonságosan kell tárolni, azonban az alkalmazás számára elérhetővé kell tenni.

Írja be az alábbi parancsokat, amelyek egy titkos kulcsot hoznak létre az **AppSecret** nevű kulcstartóban. A titkos kulcs értéke **MySecret** lesz.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Ez a parancs megjeleníti a titkos információkat, beleértve az URI-t is. A fenti lépések végrehajtása után rendelkeznie kell egy, a kulcstartóban tárolt titkos kulcshoz tartozó URI-val. Jegyezze fel ezt az információt. Egy későbbi lépésben szüksége lesz rá.

## <a name="create-a-net-core-web-app"></a>.NET Core-webalkalmazás létrehozása

Kövesse az [oktatóanyag](../app-service/app-service-web-get-started-dotnet.md) lépéseit egy .NET Core-webalkalmazás létrehozásához és Azure-ban való **közzétételéhez**, **VAGY** tekintse meg a lenti videót.
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Nyissa meg és szerkessze a megoldást

1. Nyissa meg az Oldalak > About.cshtml.cs fájlt.
2. Telepítse ezt a két NuGet-csomagot.
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Importálja az alábbiakat az About.cshtml.cs fájlba.

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. Az AboutModel osztályban található kódnak az alábbihoz hasonlóan kell kinéznie:
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

A Visual Studio 2017 főmenüjében válassza a **Debug** > **Start** with/without Debugging (Hibakeresés > Indítás hibakereséssel/hibakeresés nélkül) elemet. Amikor megjelenik a böngésző, lépjen az **About** (Névjegy) oldalra. Megjelenik az **AppSecret** értéke.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Felügyelt identitás engedélyezése a webalkalmazáshoz

Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. [Az Azure-erőforrások felügyelt identitásainak áttekintése](../active-directory/managed-identities-azure-resources/overview.md) leegyszerűsíti ezt a problémát, mivel az Azure-szolgáltatások számára egy automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD-ben). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

1. Térjen vissza az Azure CLI-hez.
2. Futtassa az identitás hozzárendelésére szolgáló parancsot az alkalmazás identitásának létrehozásához: 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   Vegye figyelembe, hogy az Azure-ban le kell cserélnie a <YourAppName> elemet a közzétett alkalmazás nevére. Például ha a közzétett alkalmazás neve MyAwesomeapp.azurewebsites.net akkor a <YourAppName> elemet cserélje le a MyAwesomeapp névre.
 
 A fenti parancs kimenete a következőképpen néz ki. Jegyezze fel a PrincipalId értékét, amikor az Azure-ban közzéteszi az alkalmazást. Az adatok a következő formátumban jelennek meg:
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
        
Ezután futtassa ezt a parancsot a kulcstartó neve és a **PrincipalId** értéke használatával:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>A webalkalmazás közzététele az Azure-ban

Tegye közzé ismét ezt az alkalmazást az Azure-ban, hogy élő webalkalmazásként megtekinthető legyen, illetve hogy látható legyen, hogy a titkos érték lekérhető-e.

1. A Visual Studióban válassza a **key-vault-dotnet-core-quickstart** projektet.
2. Válassza a **Publish** > **Start** (Közzététel > Indítás) lehetőséget.
3. Kattintson a **Létrehozás** gombra.

A fenti parancsban engedélyt ad az App Service Identity (MSI) szolgáltatása számára a Key Vaultban **get** és **list** műveletek elvégzésére. <br />
Az alkalmazás futtatásakor meg kell jelennie a titkos kulcs lekért értékének. 

Ennyi az egész. Sikeresen létrehozott egy webalkalmazást a .NET-ben, amely a Key Vaultban tárolja és onnan kéri le a titkos kulcsokat.
