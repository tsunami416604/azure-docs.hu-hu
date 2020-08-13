---
title: Gyors útmutató – a .NET-hez készült ügyféloldali kódtár (v4) Azure Key Vault
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kódokat egy Azure Key vaultban a .NET ügyféloldali kódtár (v4) használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 84f89b9def3fc5d98ebb669b9f5b86523854f7a8
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185723"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Gyors útmutató: Azure Key Vault .NET-hez készült ügyféloldali kódtár (SDK v4)

Ismerkedjen meg a .NET-hez készült Azure Key Vault ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot.

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A .NET-hez készült Key Vault ügyféloldali kódtára a következőre használható:

- Fokozza a biztonságot, és szabályozhatja a kulcsokat és a jelszavakat.
- Percek alatt létrehozhatja és importálhatja a titkosítási kulcsokat.
- Csökkentse a késést a felhő méretezésével és a globális redundanciával.
- Leegyszerűsítheti és automatizálhatja a TLS/SSL-tanúsítványok feladatait.
- Használja az FIPS 140-2 2-es szintű hitelesített HSM.

[API-referenciák dokumentációja](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A [.net Core 3,1 SDK vagy újabb verzió](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/)

Ez a rövid útmutató feltételezi, hogy futtatja `dotnet` , az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)-t és a Windows-parancsokat egy Windows-terminálon (például a [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), a [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)vagy a [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Beállítás

### <a name="create-new-net-console-app"></a>Új .NET Console-alkalmazás létrehozása

A konzol ablakban a `dotnet new` parancs használatával hozzon létre egy új, a nevű .net-konzol alkalmazást `key-vault-console-app` .

```console
dotnet new console -n key-vault-console-app
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

A konzol ablakban telepítse a Azure Key Vault .NET-hez készült ügyféloldali kódtárat:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Ebben a rövid útmutatóban a következő csomagokat is telepítenie kell:

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatás egyszerű hozzáférésének biztosítása a kulcstartóhoz

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Környezeti változók beállítása

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Objektummodell

A .NET-hez készült Azure Key Vault ügyféloldali kódtára lehetővé teszi a kulcsok és a kapcsolódó eszközök, például tanúsítványok és titkos kódok kezelését. Az alábbi kódrészletek megmutatják, hogyan hozhat létre egy ügyfelet, hogyan állíthat be titkos kulcsot, beolvashat egy titkos kulcsot, és törölhet egy titkos kulcsot.

A teljes konzol alkalmazás a következő címen érhető el: https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app .

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a kód elejéhez:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

A Key Vault hitelesítése és a Key Vault-ügyfél létrehozása a fenti [környezeti változók beállítása](#set-environmental-variables) című lépés környezeti változóktól függ. A Key Vault neve a "https://. vault.azure.net" formátumban van kibontva a Key Vault URI-ra \<your-key-vault-name\> . Az alábbi kód a Key Vault hitelesítéséhez használja a ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) értéket, amely környezeti változókat olvas be a hozzáférési jogkivonat lekéréséhez. 

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Titkos kód mentése

Most, hogy az alkalmazás hitelesítése megtörtént, egy titkos kulcsot is hozzáadhat a kulcstartóhoz az [ügyfél használatával. SetSecret-metódus](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) ehhez a mintában a "keresési kifejezésként" nevet kell használni a titkos kulcshoz.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Ellenőrizze, hogy a titkos kulcs be van-e állítva az az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Titkos kód beolvasása

Most már lekérheti a korábban beállított értéket az [ügyféllel. GetSecret metódus](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

A titkos kód most már mentve van `secret.Value` .

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül törölje a titkos kulcsot a Key vaultból az [ügyféllel. DeleteSecret metódus](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

A titkos kód az az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal ellenőrizhető:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja az Azure CLI-t vagy Azure PowerShell a kulcstartó és a hozzá tartozó erőforráscsoport eltávolításához.

### <a name="delete-a-key-vault"></a>Key Vault törlése
```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Key Vault kiürítése
```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>Erőforráscsoport törlése

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


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy titkos kulcsot, és lekérte a titkos kulcsot. Tekintse [meg a teljes konzol alkalmazást a githubon](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése
