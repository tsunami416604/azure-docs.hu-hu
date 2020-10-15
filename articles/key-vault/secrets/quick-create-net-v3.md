---
title: Gyors útmutató – a .NET-hez készült ügyféloldali kódtár (SDK v3) Azure Key Vault
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kódokat egy Azure Key vaultban a .NET ügyféloldali kódtár használatával (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 816cf8d385c12046a5363cf94ab5fa60e5d77e48
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078867"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Gyors útmutató: Azure Key Vault .NET-hez készült ügyféloldali kódtár (SDK v3)

Ismerkedjen meg a .NET-hez készült Azure Key Vault ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot.

> [!NOTE]
> Ez a rövid útmutató a Microsoft. Azure. kulcstartó ügyféloldali kódtár v 3.0.4-verzióját használja. A Key Vault ügyféloldali kódtár legfrissebb verziójának használatához tekintse meg a [.net-hez készült ügyféloldali kódtár (SDK v4) Azure Key Vault](quick-create-net.md)című témakört. 

[API-referenciák dokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/keyvault/v3)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.KeyVault)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. A következő példákban cserélje le a <az egyedi-kulcstartó-Name> a Key Vault nevét.


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A [.net Core 3,1 SDK vagy újabb verzió](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli)

Ez a rövid útmutató az `dotnet` Azure CLI-t használja

## <a name="setting-up"></a>Beállítás

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

    Ellenkező esetben nyisson meg egy böngészőt, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) és adja meg a terminálon megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="create-new-net-console-app"></a>Új .NET Console-alkalmazás létrehozása

A konzol ablakban a `dotnet new` parancs használatával hozzon létre egy új, a nevű .net-konzol alkalmazást `akv-dotnet` .


```console
dotnet new console -n akvdotnet
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>A csomag telepítése

A konzol ablakban telepítse a .NET-hez és a [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) -hez készült Azure Key Vault ügyféloldali kódtárat:

```console
dotnet add package Microsoft.Azure.KeyVault
dotnet add package Microsoft.Azure.Services.AppAuthentication
```

Ebben a rövid útmutatóban a következő csomagokat is telepítenie kell:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Hozzáférés biztosítása a kulcstartóhoz

Hozzon létre egy olyan hozzáférési szabályzatot a kulcstartó számára, amely titkos jogosultságot biztosít a felhasználói fiókjához

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

## <a name="object-model"></a>Objektummodell

A .NET-hez készült Azure Key Vault ügyféloldali kódtára lehetővé teszi a kulcsok és a kapcsolódó eszközök, például tanúsítványok és titkos kódok kezelését. Az alábbi mintakód bemutatja, hogyan állíthat be titkos kulcsot, és hogyan kérhet le titkos kulcsot.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a kód elejéhez:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Azure.KeyVault;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Management.ResourceManager.Fluent;
using Microsoft.Azure.Management.ResourceManager.Fluent.Authentication;
using Microsoft.Azure.Services.AppAuthentication;
```

### <a name="authenticate-to-your-key-vault"></a>Hitelesítés a Key vaultban

Konfigurálja a hitelesítést az alkalmazáshoz a [KeyVaultClient osztályban](/dotnet/api/microsoft.azure.keyvault.keyvaultclient). A példánkban a jelenleg bejelentkezett felhasználó által megadott tokent fogja használni `AzureServiceTokenProvider` :

```csharp
var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));
```

További információ `AzureServiceTokenProvider` : [hitelesítés az Azure-szolgáltatásokban](https://docs.microsoft.com/azure/key-vault/general/service-to-service-authentication#authenticating-to-azure-services)

### <a name="save-a-secret"></a>Titkos kód mentése

Most, hogy az alkalmazás hitelesítése megtörtént, a [SetSecretAsync metódussal](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) a kulcstartóba helyezheti a titkos kulcsot, amely az űrlapon található Key Vault URL-címét igényli `https://<your-unique-keyvault-name>.vault.azure.net/secrets/` . Emellett a titkos kulcs nevét is megköveteli – "keresési kifejezésként"-t használunk. 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Ellenőrizze, hogy a titkos kulcs be van-e állítva az az kulcstartó [Secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show) paranccsal:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Titkos kód beolvasása

Most már lekérheti a korábban beállított értéket a [GetSecretAsync metódussal](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync) .

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

A titkos kód most már mentve van `keyvaultSecret.Value;` .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja az Azure CLI-t vagy Azure PowerShell a kulcstartó és a hozzá tartozó erőforráscsoport eltávolításához.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Mintakód

Módosítsa a .NET Core Console alkalmazást úgy, hogy az a következő lépések végrehajtásával működjön együtt a Key Vault:

1. Cserélje le a *program.cs* található kódot a következő kódra:
```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Azure.KeyVault;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Management.ResourceManager.Fluent;
using Microsoft.Azure.Management.ResourceManager.Fluent.Authentication;
// </directives>

namespace akvdotnet
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Program P = new Program();
            string secretName = "mySecret";

            // kvURL must be updated to the URL of your key vault
            string kvURL = "https://myKV.vault.azure.net";

            // <authentication>

            var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
            var kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));        
            // </authentication>


            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.Write("Saving the value of your secret to your key vault ...");

            //set secret
            await kvClient.SetSecretAsync($"{kvURL}", secretName, secretValue);

            Console.WriteLine("done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");
            Console.WriteLine("Retrieving your secret from key vault.");

            //retrieve secret
            var keyvaultSecret = await kvClient.GetSecretAsync($"{kvURL}", secretName).ConfigureAwait(false);

            secretValue = keyvaultSecret.Result;
            Console.WriteLine("Your secret is " + secretValue);
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [Szolgáltatások közötti hitelesítés megvalósítása Azure Key Vault .NET használatával](../general/service-to-service-authentication.md)
- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése
