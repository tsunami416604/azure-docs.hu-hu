---
title: Gyors útmutató – a .NET-hez készült ügyféloldali kódtár (v4) Azure Key Vault
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kódokat egy Azure Key vaultban a .NET ügyféloldali kódtár (v4) használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: cc5aa7f10d83edc757e99820b9591a953df72062
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612251"
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

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3,1 SDK vagy újabb](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/)

## <a name="setup"></a>Telepítés

### <a name="create-a-new-console-app"></a>Új Console-alkalmazás létrehozása

A .NET Core Console-alkalmazás létrehozásához és fordításához hajtsa végre az alábbi lépéseket.

1. A parancssorban futtassa a következő parancsot egy nevű projekt létrehozásához `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Váltson az újonnan létrehozott *Key-Vault-Console-app* könyvtárra, és futtassa a következő parancsot a projekt felépítéséhez:

    ```dotnetcli
    dotnet build
    ```

    A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>A csomagok telepítése

A parancssorból telepítse a Azure Key Vault .NET-hez készült ügyféloldali kódtárat:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

Ehhez a rövid útmutatóhoz telepítenie kell az Azure SDK-ügyfél függvénytárát is az Azure Identity szolgáltatáshoz:

```dotnetcli
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE[Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[!INCLUDE[Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatás egyszerű hozzáférésének biztosítása a kulcstartóhoz

<!-- TODO: need to pass -g myResourceGroup to this command too -->
[!INCLUDE[Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

[!INCLUDE[Set environment variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Objektummodell

A .NET-hez készült Azure Key Vault ügyféloldali kódtára lehetővé teszi a kulcsok és a kapcsolódó eszközök kezelését. A kapcsolódó eszközök közé tartoznak például tanúsítványok és titkos kulcsok. A [példák](#code-examples) az ügyfelek létrehozására, a titkos kulcs beolvasására és a titkos kód törlésére mutatnak.

A teljes konzol alkalmazás a következő címen érhető el: https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app .

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a *program.cs*tetejéhez:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

A Key Vault hitelesítés és az ügyfél létrehozása a környezeti [változók beállítása](#set-environment-variables) lépésben megadott környezeti változóktól függ. A kulcstartó neve a Key Vault URI-ra van bontva, a következő formátumban: `https://<your-key-vault-name>.vault.azure.net` . A következő kód [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) használ a Key Vault hitelesítéséhez, amely környezeti változókat olvas be a hozzáférési jogkivonat lekéréséhez.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Titkos kód mentése

Most, hogy a konzol alkalmazás hitelesítése megtörtént, adjon hozzá egy titkos kulcsot a kulcstartóhoz. Ehhez a feladathoz használja az [ügyfelet. SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) metódus. A metódus első paramétere elfogadja a titkos &mdash; "keresési kifejezésként" nevét ebben a mintában.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Ellenőrizze, hogy a titkos kulcs be van-e állítva az az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Titkos kód beolvasása

Most már lekérheti a korábban beállított értéket az [ügyféllel. GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync) metódus.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

A titkos kód most már mentve van `secret.Value` .

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül törölje a titkos kulcsot a Key vaultból az [ügyféllel. DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync) metódus.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

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

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Key Vault kiürítése

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
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

Módosítsa a .NET Core Console alkalmazást úgy, hogy az a következő lépések végrehajtásával működjön együtt a Key Vault:

1. Cserélje le a *program.cs* található kódot a következő kódra:

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
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                client.SetSecret(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                KeyVaultSecret secret = client.GetSecret(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                client.StartDeleteSecret(secretName);
                System.Threading.Thread.Sleep(5000);
                Console.WriteLine(" done.");
            }
        }
    }
    ```

1. Futtassa az alábbi parancsot az alkalmazás futtatásához.

    ```dotnetcli
    dotnet run
    ```

1. Ha a rendszer kéri, adjon meg egy titkos értéket. Például: mySecretPassword.

    Megjelenik a következő kimenet egy változata:

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
    ```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót, egy titkos kulcsot, és beolvasta ezt a titkot. Tekintse [meg a teljes konzol alkalmazást a githubon](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, tekintse meg a következő cikkeket:

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése
