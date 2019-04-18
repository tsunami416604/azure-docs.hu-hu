---
title: Oktatóanyag – Azure Key Vault használata a .NET-es Windows virtuális gép |} A Microsoft Docs
description: Ebben az oktatóanyagban konfigurál, olvassa el a titkos kulcs a key vaultból egy ASP.NET core-alkalmazást.
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: fb17afa4bfe8c00c91cc8fb33ab3326452065a9e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885417"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Oktatóanyag: Az Azure Key Vault használata a .NET-es Windows virtuális gép

Az Azure Key Vault segítségével például a kulcsok API-t, az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges adatbázis-kapcsolati karakterláncok titkainak védelmére.

Ebben az oktatóanyagban elsajátíthatja, hogyan tehet szert egy konzolalkalmazást az Azure Key Vault származó információk olvasásához. Ehhez a felügyelt identitások az Azure-erőforrásokhoz használhatja. 

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot.
> * Kulcstartó létrehozása.
> * Titkos kód hozzáadása a kulcstartóhoz.
> * Titkos kulcs lekérése a kulcstartóból.
> * Hozzon létre egy Azure virtuális gépen.
> * Engedélyezze a [identitás](../active-directory/managed-identities-azure-resources/overview.md) a virtuális gép.
> * Engedélyek hozzárendelése a virtuális gép identitását.

Mielőtt elkezdené, olvassa el a [Key Vault alapvető fogalmait](key-vault-whatis.md#basic-concepts). 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Windows, Mac és Linux:
  * [Git](https://git-scm.com/downloads)
  * Ehhez az oktatóanyaghoz az Azure CLI helyi futtatása. Rendelkeznie kell az Azure CLI 2.0.4-es vagy újabb verziója van telepítve. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Tudnivalók a felügyeltszolgáltatás-identitásról

Az Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, azok nem jelennek meg a kódot. Azonban szüksége hitelesítéséhez az Azure Key Vaulttal a kulcsok lekéréséhez. Hitelesítést a Key Vault, a hitelesítő adatokat kell. Egy klasszikus bootstrap kapcsolatos dilemma megoldása. Felügyelt Felügyeltszolgáltatás-identitás (MSI) megoldja a problémát azáltal, hogy egy _identitás bootstrap_ , amely leegyszerűsíti a folyamatot.

Ha engedélyezi az MSI egy Azure-szolgáltatás, például az Azure Virtual Machines, az Azure App Service vagy az Azure Functions, az Azure létrehoz egy [szolgáltatásnév](key-vault-whatis.md#basic-concepts). MSI azért teszi ezt a szolgáltatást az Azure Active Directory (Azure AD)-példány, és egyszerű szolgáltatás hitelesítő adatai kódtárba be annak a példánynak. 

![MSI](media/MSI.png)

Ezután a hozzáférési jogkivonatot kapjon a kód meghívja egy helyi metaadat-szolgáltatás, amely az Azure-erőforrás érhető el. Hitelesíthetik magukat egy Azure Key Vault szolgáltatásban, a kódot használja a hozzáférési jogkivonatot, amely a helyi MSI-végpontról kap. 

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

Ez az oktatóanyag során az újonnan létrehozott erőforráscsoportot használ.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Hozzon létre egy kulcstartót az előző lépésben létrehozott erőforráscsoportban, adja meg a következő információkat:

* A Key vault name: egy karakterlánc 3 – 24 karakter, amely csak számokat (0 – 9) tartalmazhatnak betűket (a – z, A – Z), és kötőjelet (-)
* Erőforráscsoport neve
* Hely: **USA nyugati régiója**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Ezen a ponton az Azure-fiókkal az egyetlen, amely engedélyezett műveletek végrehajtása az új kulcstartóban.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. A titkos kulcsot egy SQL-kapcsolati karakterlánc vagy bármely egyéb információkat, amelyek továbbra is szeretné, biztonságos és az alkalmazás számára elérhető lehet.

A key vaultban titkos kulcs létrehozása nevű **AppSecret**, adja meg a következő parancsot:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

A titkos kód tárolja az értéket **MySecret**.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Az alábbi módszerek egyikének használatával hozhat létre egy virtuális gépet:

* [Az Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>Identitás hozzárendelése a virtuális gép
Ebben a lépésben a virtuális gép egy rendszer által hozzárendelt identitással létrehozhat az Azure CLI az alábbi parancs futtatásával:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Vegye figyelembe a rendszer által hozzárendelt identitás, amely a következő kód jelenik meg. Az előző parancs kimenete a következő lesz: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Engedélyek hozzárendelése a virtuális gép identitása
Most a korábban létrehozott identitás engedélyeket rendelhet a kulcstartó a következő parancs futtatásával:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Bejelentkezés a virtuális gépre

Jelentkezzen be a virtuális gép, kövesse a [Connect, és jelentkezzen be a Windows rendszert futtató Azure virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="install-net-core"></a>A .NET Core telepítése

Telepítse a .NET Core, lépjen a [.NET letölti](https://www.microsoft.com/net/download) lapot.

## <a name="create-and-run-a-sample-net-app"></a>Létrehozása és futtatása a .NET-mintaalkalmazást

Nyisson meg egy parancssort.

A konzol "Hello World" kinyomtathatja a következő parancsok futtatásával:

```batch
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app"></a>A konzol alkalmazás szerkesztése

Nyissa meg a *Program.cs* fájlt, és adja hozzá ezeket a csomagokat:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Szerkessze a osztály fájlban szereplő kód a következő két lépésből álló folyamat:

1. Olvassa be a jogkivonatot a helyi MSI-végpontról a virtuális gépen. Így is olvas be egy tokent az Azure AD.
1. A jogkivonat át a key vaultban, és ezután beolvasni a titkos kód. 

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

A fenti kód bemutatja, hogyan hajtsa végre a műveleteket az Azure Key Vault egy Windows virtuális gépen.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, törölje a virtuális gép és a key vaultban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Key Vault REST API-val](https://docs.microsoft.com/rest/api/keyvault/)
