---
title: Oktatóanyag – Azure Key Vault használata Windows rendszerű virtuális géppel a .NET-ben | Microsoft Docs
description: Ebben az oktatóanyagban egy ASP.NET Core-alkalmazást konfigurál, hogy beolvassa a titkos kulcsot a kulcstartóból.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: fbda2f645308e30a6f408335b7a1b37095522921
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003315"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Oktatóanyag: Azure Key Vault használata Windows rendszerű virtuális géppel a .NET-ben

Azure Key Vault segítséget nyújt a titkok, például az API-kulcsok, az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges adatbázis-kapcsolati karakterláncok védelemmel való ellátásához.

Ebből az oktatóanyagból megtudhatja, hogyan szerezhet be egy konzolos alkalmazást a Azure Key Vault információk olvasásához. Ehhez felügyelt identitásokat kell használnia az Azure-erőforrásokhoz. 

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot.
> * Kulcstartó létrehozása.
> * Adjon hozzá egy titkos kulcsot a kulcstartóhoz.
> * Titkos kulcs lekérése a kulcstartóból.
> * Hozzon létre egy Azure-beli virtuális gépet.
> * [Felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a virtuális géphez.
> * Rendeljen engedélyeket a virtuális gép identitásához.

Mielőtt elkezdené, olvassa el [Key Vault alapvető fogalmakat](basic-concepts.md). 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Windows, Mac és Linux rendszerekhez:
  * [Git](https://git-scm.com/downloads)
  * Ehhez az oktatóanyaghoz helyileg kell futtatnia az Azure CLI-t. Telepítenie kell az Azure CLI 2.0.4 vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Tudnivalók a felügyeltszolgáltatás-identitásról

A Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, így azok nem jelennek meg a kódban. A kulcsok lekéréséhez azonban hitelesítenie kell Azure Key Vault. Key Vault hitelesítéséhez szüksége lesz egy hitelesítő adatra. Ez egy klasszikus rendszerindítási dilemma. Managed Service Identity (MSI) ezt a problémát úgy oldja meg, hogy egy rendszerindító identitást biztosít, amely leegyszerűsíti a folyamatot.

Ha az MSI-t egy Azure-szolgáltatáshoz (például Azure Virtual Machines, Azure App Service vagy Azure Functions) engedélyezi, az Azure létrehoz egy [egyszerű szolgáltatást](basic-concepts.md). Az MSI ezt a szolgáltatást a Azure Active Directory (Azure AD) szolgáltatás példányán végzi el, és az egyszerű szolgáltatásnév hitelesítő adatait beinjektálja a példányba. 

![MSI](media/MSI.png)

A hozzáférési jogkivonat beszerzéséhez a kód meghívja az Azure-erőforráson elérhető helyi metaadat-szolgáltatást. Azure Key Vault szolgáltatásban való hitelesítéshez a kód a helyi MSI-végponttól kapott hozzáférési jogkivonatot használja. 

## <a name="create-resources-and-assign-permissions"></a>Erőforrások létrehozása és engedélyek kiosztása

Mielőtt elkezdené a kódolást, létre kell hoznia néhány erőforrást, be kell állítania egy titkos kulcsot a kulcstartóba, és hozzá kell rendelnie az engedélyeket.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha az Azure CLI használatával szeretne bejelentkezni az Azure-ba, írja be a következőt:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. 

Ez a példa egy erőforráscsoportot hoz létre az USA nyugati régiójában:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Az oktatóanyag során az újonnan létrehozott erőforráscsoportot fogja használni.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Key Vault létrehozása és feltöltése titkos kulccsal

Hozzon létre egy Key vaultot az erőforráscsoporthoz úgy, hogy az az kulcstartó [create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot adja meg a következő információkkal:

* Key Vault neve: 3 – 24 karakterből álló karakterlánc, amely csak számokat (0-9), betűket (a-z, A-z) és kötőjeleket (-) tartalmazhat.
* Erőforráscsoport neve
* Helyen **USA nyugati régiója**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Ezen a ponton az Azure-fiókja az egyetlen, amely jogosult a műveletek végrehajtására ezen az új kulcstartón.

Most adjon hozzá egy titkos kulcsot a kulcstartóhoz az az Key [Vault Secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) paranccsal


Ha a **AppSecret**nevű kulcstartóban szeretne titkos kulcsot létrehozni, írja be a következő parancsot:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ez a titok a **keresési kifejezésként**értéket tárolja.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Hozzon létre egy virtuális gépet az alábbi módszerek egyikének használatával:

* [Az Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Identitás kiosztása a virtuális géphez
Hozzon létre egy rendszerhez rendelt identitást a virtuális géphez az az [VM Identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) paranccsal:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Jegyezze fel a rendszer által hozzárendelt identitást, amely a következő kódban látható. Az előző parancs kimenete a következő lesz: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Engedélyek kiosztása a virtuális gép identitásához
Rendelje hozzá a korábban létrehozott identitási engedélyeket a kulcstartóhoz az az Key [Vault set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Bejelentkezés a virtuális gépre

A virtuális gépre való bejelentkezéshez kövesse a [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális géphez](../virtual-machines/windows/connect-logon.md)című témakör utasításait.

## <a name="set-up-the-console-app"></a>A konzol alkalmazásának beállítása

Hozzon létre egy Console-alkalmazást, és telepítse a `dotnet` szükséges csomagokat a parancs használatával.

### <a name="install-net-core"></a>A .NET Core telepítése

A .NET Core telepítéséhez nyissa meg a [.net-letöltések](https://www.microsoft.com/net/download) lapot.

### <a name="create-and-run-a-sample-net-app"></a>Minta .NET-alkalmazás létrehozása és futtatása

Nyisson meg egy parancssort.

A következő parancsok futtatásával kinyomtathatja a konzolon a ""Helló világ!"alkalmazás" parancsot:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>A csomagok telepítése

 A konzol ablakban telepítse az ehhez a rövid útmutatóhoz szükséges .NET-csomagokat:

 ```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>A konzol alkalmazás szerkesztése

Nyissa meg a *program.cs* fájlt, és adja hozzá a következő csomagokat:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Szerkessze a következő kétlépéses folyamat kódját tartalmazó osztályt:

1. A virtuális gép helyi MSI-végpontján lévő jogkivonat beolvasása. Ezzel az Azure AD-ből is lekéri a tokent.
1. Továbbítsa a tokent a kulcstartóba, majd olvassa be a titkot. 

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

Az előző kód azt mutatja be, hogyan végezheti el a műveleteket a Azure Key Vault egy Windows rendszerű virtuális gépen.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje a virtuális gépet és a kulcstartót.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
