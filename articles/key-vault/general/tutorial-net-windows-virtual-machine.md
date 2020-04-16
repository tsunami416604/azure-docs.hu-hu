---
title: Oktatóanyag – Az Azure Key Vault használata Windows virtuális géppel a .NET | Microsoft dokumentumok
description: Ebben az oktatóanyagban konfigurálja a ASP.NET alapalkalmazást a kulcstartó ból egy titkos olvasásra.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 6ba78a44af7beb9b5b79aa1a87e08f5a82589cce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422864"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Oktatóanyag: Az Azure Key Vault használata Windows virtuális géppel a .NET rendszerben

Az Azure Key Vault segít megvédeni a titkos kulcsokat, például az API-kulcsokat, az adatbázis-kapcsolat ihletési karakterláncait, amelyek az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges.

Ebben az oktatóanyagban megtudhatja, hogyan szerezhet be egy konzolalkalmazást az Azure Key Vaultból származó információk olvasásához. Ehhez felügyelt identitásokat kell használnia az Azure-erőforrásokhoz. 

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot.
> * Kulcstartó létrehozása.
> * Titk a kulcstartóhoz.
> * Titkos kulcs lekérése a kulcstartóból.
> * Hozzon létre egy Azure virtuális gépet.
> * Felügyelt [identitás](../../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a virtuális gép számára.
> * Engedélyek hozzárendelése a virtuális gép identitásához.

Mielőtt elkezdené, olvassa el [a Key Vault alapfogalmait.](basic-concepts.md) 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="prerequisites"></a>Előfeltételek

Windows, Mac és Linux esetén:
  * [Git](https://git-scm.com/downloads)
  * Ez az oktatóanyag megköveteli, hogy az Azure CLI helyileg fusson. Az Azure CLI 2.0.4-es vagy újabb verziójának telepítve kell lennie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Tudnivalók a felügyeltszolgáltatás-identitásról

Az Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, így azok nem jelennek meg a kódban. Azonban a kulcsok lekéréséhez az Azure Key Vault ban kell hitelesítenie magát. A Key Vault hitelesítéséhez hitelesítő adatokra van szükség. Ez egy klasszikus bootstrap dilemma. A felügyelt szolgáltatásidentitás (MSI) úgy oldja meg ezt a problémát, hogy olyan _rendszerindítási identitást_ biztosít, amely leegyszerűsíti a folyamatot.

Ha engedélyezi az MSI-t egy Azure-szolgáltatáshoz, például az Azure virtuális gépekhez, az Azure App Service-hez vagy az Azure Functionshez, az Azure létrehoz egy [egyszerű szolgáltatást.](basic-concepts.md) AZ MSI ezt a szolgáltatás az Azure Active Directoryban (Azure AD) a szolgáltatás példányát, és beadja a szolgáltatás egyszerű hitelesítő adatait az adott példányban. 

![MSI](../media/MSI.png)

Ezután egy hozzáférési jogkivonat beszerzése, a kód meghívja a helyi metaadat-szolgáltatás, amely elérhető az Azure-erőforrás. Az Azure Key Vault-szolgáltatás hitelesítéséhez a kód a helyi MSI-végponttól kapott hozzáférési jogkivonatot használja. 

## <a name="create-resources-and-assign-permissions"></a>Erőforrások létrehozása és engedélyek hozzárendelése

A kódolás megkezdése előtt létre kell hoznia néhány erőforrást, titkos kulcsot kell behelyeznie a key vaultba, és engedélyeket kell hozzárendelnie.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha az Azure CLI használatával szeretne bejelentkezni az Azure CLI-be, írja be a következőt:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. 

Ez a példa létrehoz egy erőforráscsoportot az USA nyugati részén:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Az újonnan létrehozott erőforráscsoport fogja használni az oktatóanyag ban.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Hozzon létre egy kulcstartót, és feltöltse azt egy titkos

Hozzon létre egy key vault az erőforráscsoportban azáltal, hogy az [keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot a következő információkat:

* Kulcstartó neve: 3–24 karakterből álló karakterlánc, amely csak számokat (0-9), betűket (a-z, A-z) és kötőjeleket (-) tartalmazhat.
* Erőforráscsoport neve
* Helyszín: **USA nyugati telephelye**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Ezen a ponton az Azure-fiók az egyetlen, amely jogosult műveletek végrehajtására az új kulcstartó.

Most adjunk hozzá egy titkos kulcsot a key vault titkos [készlet](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) parancs


Ha egy **AppSecret**nevű titkos kulcsot szeretne létrehozni a key vaultban, írja be a következő parancsot:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ez a titkos tárolja az értéket **MySecret**.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Hozzon létre egy virtuális gépet az alábbi módszerek egyikével:

* [Az Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Az Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Identitás hozzárendelése a virtuális géphez
Hozzon létre egy rendszer által hozzárendelt identitást a virtuális géphez az [az vm identitáshozzárendelési](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) paranccsal:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Vegye figyelembe a rendszer által hozzárendelt identitás, amely megjelenik a következő kódban. Az előző parancs kimenete a következő lenne: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Engedélyek hozzárendelése a virtuális gép identitásához
Rendelje hozzá a korábban létrehozott identitásengedélyeket a key vault az [keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Bejelentkezés a virtuális gépre

A virtuális gépre való bejelentkezéshez kövesse a [Csatlakozás és a Windows rendszerű Azure-alapú virtuális gépre való bejelentkezés](../../virtual-machines/windows/connect-logon.md)utasításait.

## <a name="set-up-the-console-app"></a>A konzolalkalmazás beállítása

Hozzon létre egy konzolalkalmazást, és `dotnet` telepítse a szükséges csomagokat a paranccsal.

### <a name="install-net-core"></a>A .NET Core telepítése

A .NET Core telepítéséhez lépjen a [.NET letöltések](https://www.microsoft.com/net/download) lapra.

### <a name="create-and-run-a-sample-net-app"></a>Minta .NET alkalmazás létrehozása és futtatása

Nyisson meg egy parancssort.

A "Hello World" kinyomtatható a konzolra a következő parancsok futtatásával:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>A csomagok telepítése

A konzolablakból telepítse a rövid útmutatóhoz szükséges .NET csomagokat:

```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>A konzolalkalmazás szerkesztése

Nyissa meg a *Program.cs* fájlt, és adja hozzá a csomagokat:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Az osztályfájl szerkesztése úgy, hogy az tartalmazza a kódot a következő háromlépésben:

1. Token beolvasása a helyi MSI-végpontból a virtuális gépen. Ezzel is lekéri a jogkivonatot az Azure AD-ből.
2. Adja át a tokent a kulcstartónak, majd olvassa el a titkos kulcsot. 
3. Adja hozzá a tároló nevét és titkos nevét a kérelemhez.

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }
        
        static string FetchSecretValueFromKeyVault(string token)
        {
            //Step 3: Add the vault name and secret name to the request.
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

Az előző kód bemutatja, hogyan műveleteket az Azure Key Vault egy Windows virtuális gép.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje a virtuális gépet és a key vault.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
