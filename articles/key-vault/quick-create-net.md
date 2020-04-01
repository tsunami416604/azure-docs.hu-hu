---
title: Rövid útmutató – Azure Key Vault-ügyféltár a .NET-hez (v4)
description: Megtudhatja, hogy miként hozhat létre, kérhet le és törölhet titkokat egy Azure-kulcstárolóból a .NET ügyfélkódtár használatával (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: a94717c7bed3ba25a4682896053672fe100dc43a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398381"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Rövid útmutató: Azure Key Vault-ügyféltár a .NET-hez (SDK v4)

Ismerkedés az Azure Key Vault ügyféltár .NET. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapvető feladatok példakódját.

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A .NET key vault-ügyféltár segítségével:

- Növelje a biztonságot és szabályozza a kulcsokat és jelszavakat.
- Titkosítási kulcsok létrehozása és importálása percek alatt.
- Csökkentse a késést a felhőalapú skálával és a globális redundanciával.
- Egyszerűsítse és automatizálja a TLS/SSL-tanúsítványok feladatait.
- Használja fips 140-2 Level 2 validált HSMs.

[API-referenciadokumentáció](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [könyvtár forráskódcsomagja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [(NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A [.NET Core 2.1 SDK vagy újabb](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview)

Ez a rövid útmutató `dotnet`feltételezi, hogy windowsos terminálon (például [PowerShell Core,](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6) [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)vagy Azure [Cloud Shell)](https://shell.azure.com/)fut, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)és Windows-parancsokat futtat.

## <a name="setting-up"></a>Beállítása

### <a name="create-new-net-console-app"></a>Új .NET konzolalkalmazás létrehozása

A konzolablakban a `dotnet new` paranccsal hozzon létre egy `key-vault-console-app`új .

```console
dotnet new console -n key-vault-console-app
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára. Az alkalmazást a következőkkel hozhatja létre:

```console
dotnet build
```

A build kimenetnem tartalmazhat figyelmeztetéseket vagy hibákat.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>A csomag telepítése

A konzolablakból telepítse az Azure Key Vault-ügyfélkönyvtárat a .NET-hez:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Ehhez a rövid útmutatóhoz a következő csomagokat is telepítenie kell:

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

Ez a rövid útmutató egy előre létrehozott Azure-kulcstartót használ. Az [Azure CLI gyorsútmutató](quick-create-cli.md), az [Azure PowerShell gyorsindítása](quick-create-powershell.md)vagy az Azure Portal gyorsindításlépéseit követve hozhat létre kulcstrekész [tárolót.](quick-create-portal.md) Azt is megteheti, hogy egyszerűen futtassa az Azure CLI-parancsokat.

> [!Important]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. Cserélje le <egyedi keyvault-neve> a kulcstartó nevét az alábbi példákban.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location EastUS
```

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A felhőalapú .NET-alkalmazások hitelesítésének legegyszerűbb módja felügyelt identitás; A részleteket az Azure Key Vault eléréséhez az [App Service felügyelt identitásának használata.](managed-identity.md) Az egyszerűség kedvéért azonban ez a rövid útmutató létrehoz egy .NET konzolalkalmazást. Egy asztali alkalmazás azure-ral történő hitelesítéséhez egyszerű szolgáltatásés hozzáférés-vezérlési szabályzat használata szükséges.

Hozzon létre egy szolgáltatási elvet az Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) paranccsal:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Ez a művelet kulcs/ értékpárok sorozatát adja vissza. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Egyszerű szolgáltatás létrehozása az Azure PowerShell [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) paranccsal:

```azurepowershell
# Create a new service principal
$spn = New-AzADServicePrincipal -DisplayName "http://mySP"

# Get the tenant ID and subscription ID of the service principal
$tenantId = (Get-AzContext).Tenant.Id
$subscriptionId = (Get-AzContext).Subscription.Id

# Get the client ID
$clientId = $spn.ApplicationId

# Get the client Secret
$bstr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($spn.Secret)
$clientSecret = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
```

Az Azure PowerShell egyszerű szolgáltatásról további információt az [Azure-szolgáltatásegyszerű szolgáltatás létrehozása az Azure PowerShell használatával](/powershell/azure/create-azure-service-principal-azureps)című dokumentumban talál.

Vegye figyelembe az ügyfélazonosítót, az ügyféltitkos kulcsot és a tenantId azonosítót, mivel a következő lépésekben fogjuk használni őket.


#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatásegyszerű hozzáférés a key vaulthoz

Hozzon létre egy hozzáférési szabályzatot a key vault, amely engedélyt ad a szolgáltatásnév átadásával az ügyfélazonosító az [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancs. Adja meg a szolgáltatás egyszerű lekérni, listát, és engedélyeket a kulcsok és a titkos kulcsok.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions list get set delete purge
```

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName <your-unique-keyvault-name> -ServicePrincipalName <clientId-of-your-service-principal> -PermissionsToSecrets list,get,set,delete,purge
```

#### <a name="set-environmental-variables"></a>Környezeti változók beállítása

Az alkalmazásDefaultAzureCredential metódusa három környezeti változóra `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET`támaszkodik: , és `AZURE_TENANT_ID`. használja ezeket a változókat az ügyfélazonosító, clientSecret és tenantId értékeket, amelyeket a fenti [Szolgáltatásegyszerű szolgáltatás létrehozása](#create-a-service-principal) lépésben észlelt.

A kulcstartó nevét is mentenie kell egy `KEY_VAULT_NAME`környezeti változóként, amelynek neve;

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

Minden híváskor `setx`a "SUCCESS: Specified value was saved" választ kell kapnia.

```shell
AZURE_CLIENT_ID=<your-clientID>

AZURE_CLIENT_SECRET=<your-clientSecret>

AZURE_TENANT_ID=<your-tenantId>

KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektummodell

Az Azure Key Vault ügyfélkódtár .NET lehetővé teszi a kulcsok és a kapcsolódó eszközök, például a tanúsítványok és a titkos kulcsok kezelését. Az alábbi kódminták megmutatják, hogyan hozhat létre ügyfelet, állíthat be egy titkos kulcsot, hogyan kérhet le egy titkos kulcsot, és hogyan törölhet egy titkos kulcsot.

A teljes konzolalkalmazás https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-appelérhető a következő helyen: .

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Direktívák hozzáadása

Adja hozzá a következő irányelveket a kód tetejéhez:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

A kulcstartóba való hitelesítés és a key vault-ügyfél létrehozása a környezeti változóktól függ a [fenti környezeti változók](#set-environmental-variables) beállításában. A kulcstartó neve ki van bontva a key vault\<URI-ra a\>"https:// a kulcstartó neve .vault.azure.net" formátumban.

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Titkos fájl mentése

Most, hogy az alkalmazás hitelesítése, az ügyfél használatával titkos kulcsot helyezhet el [a keyvaultban. SetSecret metódus:](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) Ehhez nevet kell használni a titkos névhez - ebben a mintában "mySecret" nevet használunk.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Ellenőrizheti, hogy a titkos kulcs az [az keyvault titkos show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal lett-e beállítva:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Titkos titok beolvasása

Most már lekérheti a korábban beállított értéket az [ügyféllel. GetSecret metódus](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

A titka `secret.Value`most mentve .

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül törölje a titkos kulcsot a kulcstartóból az [ügyféllel. DeleteSecret metódus](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Ellenőrizheti, hogy a titkos titok eltűnt-e az [az keyvault titkos show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, használhatja az Azure CLI vagy az Azure PowerShell a key vault és a megfelelő erőforráscsoport eltávolításához.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Mintakód

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
}
```


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy titkos kulcsot, és lekérte azt. Tekintse meg a [teljes konzolalkalmazást a GitHubon.](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)

Ha többet szeretne megtudni a Key Vaultról és arról, hogyan integrálhatja azt az alkalmazásokkal, folytassa az alábbi cikkekkel.

- [Szolgáltatás-szolgáltatás hitelesítés](service-to-service-authentication.md) megvalósítása az Azure Key Vault használatával .
- Az [Azure Key Vault áttekintésének elolvasása](key-vault-overview.md)
- Tekintse meg az [Azure Key Vault fejlesztői útmutatóját](key-vault-developers-guide.md)
- Tudnivalók [a kulcsokról, titkos kulcsokról és tanúsítványokról](about-keys-secrets-and-certificates.md)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](key-vault-best-practices.md)
