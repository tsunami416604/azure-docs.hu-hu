---
title: Gyors útmutató – a .NET-hez készült ügyféloldali kódtár (SDK v3) Azure Key Vault
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kódokat egy Azure Key vaultban a .NET ügyféloldali kódtár használatával (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 2e5f3357baa774c9690e079a82b11d2c651ebe9b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87078871"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Gyors útmutató: Azure Key Vault .NET-hez készült ügyféloldali kódtár (SDK v3)

Ismerkedjen meg a .NET-hez készült Azure Key Vault ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot.

> [!NOTE]
> Ez a rövid útmutató a Microsoft. Azure. kulcstartó ügyféloldali kódtár v 3.0.4-verzióját használja. A Key Vault ügyféloldali kódtár legfrissebb verziójának használatához tekintse meg a [.net-hez készült ügyféloldali kódtár (SDK v4) Azure Key Vault](quick-create-net.md)című témakört. 

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A .NET-hez készült Key Vault ügyféloldali kódtára a következőre használható:

- Fokozza a biztonságot, és szabályozhatja a kulcsokat és a jelszavakat.
- Percek alatt létrehozhatja és importálhatja a titkosítási kulcsokat.
- Csökkentse a késést a felhő méretezésével és a globális redundanciával.
- Leegyszerűsítheti és automatizálhatja a TLS/SSL-tanúsítványok feladatait.
- Használja az FIPS 140-2 2-es szintű hitelesített HSM.

[API-referenciák dokumentációja](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. A következő példákban cserélje le a <az egyedi-kulcstartó-Name> a Key Vault nevét.


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A [.net Core 3,1 SDK vagy újabb verzió](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/)

Ez a rövid útmutató feltételezi, hogy futtatja `dotnet` , az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)-t és a Windows-parancsokat egy Windows-terminálon (például a [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), a [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)vagy a [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Beállítás

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

A konzol ablakban telepítse a Azure Key Vault .NET-hez készült ügyféloldali kódtárat:

```console
dotnet add package Microsoft.Azure.KeyVault
```

Ebben a rövid útmutatóban a következő csomagokat is telepítenie kell:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatás egyszerű hozzáférésének biztosítása a kulcstartóhoz

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

## <a name="object-model"></a>Objektummodell

A .NET-hez készült Azure Key Vault ügyféloldali kódtára lehetővé teszi a kulcsok és a kapcsolódó eszközök, például tanúsítványok és titkos kódok kezelését. Az alábbi mintakód bemutatja, hogyan állíthat be titkos kulcsot, és hogyan kérhet le titkos kulcsot.

A teljes konzol alkalmazás a következő címen érhető el: https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet .

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a kód elejéhez:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Hitelesítés a Key vaultban

Ez a .NET gyors útmutató környezeti változók alapján tárolja azokat a hitelesítő adatokat, amelyeket nem szabad programkódba helyezni. 

Az alkalmazás létrehozása és futtatása előtt a `setx` parancs használatával állítsa be a,, `akvClientId` `akvClientSecret` `akvTenantId` és `akvSubscriptionId` környezeti változókat a fent említett értékekre.

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Rendelje hozzá ezeket a környezeti változókat a kódban szereplő karakterláncokhoz, majd hitelesítse az alkalmazást a [KeyVaultClient osztályba](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)való átadásával:

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Titkos kód mentése

Most, hogy az alkalmazás hitelesítése megtörtént, a [SetSecretAsync metódussal](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) a kulcstartóba helyezheti a titkos kulcsot, amely az űrlapon található Key Vault URL-címét igényli `https://<your-unique-keyvault-name>.vault.azure.net/secrets/` . Emellett a titkos kulcs nevét is megköveteli – "keresési kifejezésként"-t használunk. 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Ellenőrizze, hogy a titkos kulcs be van-e állítva az az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal:

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

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy titkos kulcsot, és lekérte a titkos kulcsot. Tekintse [meg a teljes konzol alkalmazást a githubon](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [Szolgáltatások közötti hitelesítés megvalósítása Azure Key Vault .NET használatával](../general/service-to-service-authentication.md)
- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése
