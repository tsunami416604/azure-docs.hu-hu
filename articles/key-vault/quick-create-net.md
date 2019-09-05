---
title: Gyors útmutató – a .NET-hez készült ügyféloldali kódtár Azure Key Vault
description: Az Azure SDK-ügyfél kódtárainak írására szolgáló formátum-és tartalmi feltételeket biztosít.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: c67b24d57117a248559424497939a04ce347658c
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308955"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>Gyors útmutató: A .NET-hez készült ügyféloldali kódtár Azure Key Vault

Ismerkedjen meg a .NET-hez készült Azure Key Vault ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot.

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A .NET-hez készült Key Vault ügyféloldali kódtára a következőre használható:

- Fokozza a biztonságot, és szabályozhatja a kulcsokat és a jelszavakat.
- Percek alatt létrehozhatja és importálhatja a titkosítási kulcsokat.
- Csökkentse a késést a felhő méretezésével és a globális redundanciával.
- Leegyszerűsítheti és automatizálhatja az SSL/TLS-tanúsítványok feladatait.
- Használja az FIPS 140-2 2-es szintű hitelesített HSM.

[API-referenciák dokumentációs](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [könyvtárának forráskód](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault) | [-csomagja (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A [.net Core 2,1 SDK vagy újabb verzió](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview)

Ez a rövid útmutató feltételezi, `dotnet`hogy futtatja, az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)-t és a Windows-parancsokat egy Windows-terminálon (például a [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), a [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)vagy a [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Beállítás

### <a name="create-new-net-console-app"></a>Új .NET Console-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben.

A konzol ablakban a `dotnet new` parancs használatával hozzon létre egy új, a nevű `akv-dotnet`Console-alkalmazást.


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

Ez a rövid útmutató egy előre létrehozott Azure Key vaultot használ. Kulcstartó létrehozásához kövesse az [Azure CLI](quick-create-cli.md)gyors üzembe helyezésének lépéseit, [Azure PowerShell](quick-create-powershell.md)a gyors üzembe helyezést, vagy [Azure Portal](quick-create-portal.md)a gyors üzembe helyezést. Azt is megteheti, hogy az alábbi Azure CLI-parancsokat egyszerűen futtatja.

> [!Important]
> Minden Key Vault egyedi névvel kell rendelkeznie. Az alábbi példa egy *myKV*nevű Key Vault hoz létre, de a tiéd nevet kell adnia, és ezt a nevet kell használnia ebben a rövid útmutatóban.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A felhőalapú .NET-alkalmazások hitelesítésének legegyszerűbb módja a felügyelt identitás; a részletekért lásd: [szolgáltatások közötti hitelesítés Azure Key Vault .NET használatával](service-to-service-authentication.md) . Az egyszerűség kedvéért azonban ez a rövid útmutató egy .NET-konzolos alkalmazást hoz létre. Egy asztali alkalmazás Azure-beli hitelesítéséhez az egyszerű szolgáltatásnév használata szükséges.
Hozzon létre egy szolgáltatási elvet az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancs használatával:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

A művelet a kulcs/érték párok sorozatát fogja visszaadni. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Jegyezze fel a clientId, a clientSecret, a subscriptionId és a tenantId, ahogy ezeket a [hitelesítést a Key Vault](#authenticate-to-your-key-vault) következő lépésében fogjuk használni.

Szüksége lesz az egyszerű szolgáltatásnév appID is. Az az [ad SP-lista](https://docs.microsoft.com/en-us/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) futtatásával a `--show-mine` (z) paraméterrel megkeresheti:

```azurecli
az ad sp list --show-mine
```

Az `appID` megjelenik a visszaadott JSON-ben:

```json
    "appId": "2cf5aa18-0100-445a-9438-0b93e577a3ed",
```

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatás egyszerű hozzáférésének biztosítása a kulcstartóhoz

Hozzon létre egy hozzáférési szabályzatot a kulcstartó számára, amely engedélyt ad a szolgáltatásnak. Ezt az az Key [Vault set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal teheti meg. Az egyszerű szolgáltatás lekérése, listázása és beállítása a kulcsokhoz és titkokhoz is érvényes lesz.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <appid-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Objektummodell

A .NET-hez készült Azure Key Vault ügyféloldali kódtára lehetővé teszi a kulcsok és a kapcsolódó eszközök, például tanúsítványok és titkos kódok kezelését. Az alábbi mintakód bemutatja, hogyan állíthat be titkos kulcsot, és hogyan kérhet le titkos kulcsot.

A teljes konzol alkalmazás a következő címen https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet érhető el:.

## <a name="code-examples"></a>Példák a kódokra

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a kód elejéhez:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Hitelesítés a Key vaultban

Ez a .NET rövid útmutató környezeti változók alapján tárolja azokat a hitelesítő adatokat, amelyeket nem kell a programkódba helyezni. 

Az alkalmazás létrehozása `setx` és futtatása előtt a parancs használatával állítsa be a `akvClientId`, `akvClientSecret` `akvTenantId`, és `akvSubscriptionId` környezeti változókat a fent említett értékekre.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>

setx akvTenantId <your-tentantId>

setx akvSubscriptionId <your-subscriptionId>
````

Minden alkalommal, amikor `setx`meghívja a (z) "sikeres" választ: A megadott érték mentése megtörtént. "

Rendelje hozzá ezeket a környezeti változókat a kódban szereplő karakterláncokhoz, majd hitelesítse az alkalmazást a [KeyVaultClient osztályba](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)való átadásával:

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Titkos kód mentése

Most, hogy az alkalmazás hitelesítése megtörtént, a [SetSecretAsync metódussal](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) a kulcstartóba helyezheti a titkos kulcsot, amely az űrlapon `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`található Key Vault URL-címét igényli. Emellett a titkos kulcs nevét is megköveteli – "keresési kifejezésként"-t használunk.  Előfordulhat, hogy ezeket a karakterláncokat resue változóhoz szeretné rendelni.

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Ellenőrizze, hogy a titkos kulcs be van-e állítva az az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Titkos kód beolvasása

Most már lekérheti a korábban beállított értéket a [GetSecretAsync metódussal](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync) .

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

A titkos kód most már mentve `keyvaultSecret.Value;`van.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja az Azure CLI-t vagy Azure PowerShell a kulcstartó és a hozzá tartozó erőforráscsoport eltávolításához.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy titkos kulcsot, és lekérte a titkos kulcsot. Tekintse [meg a teljes konzol alkalmazást a githubon](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [Szolgáltatások közötti hitelesítés megvalósítása Azure Key Vault .NET használatával](service-to-service-authentication.md)
- [A Azure Key Vault áttekintése](key-vault-overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](key-vault-developers-guide.md)
- Tudnivalók a [kulcsokról, a titkokról és a tanúsítványokról](about-keys-secrets-and-certificates.md)
- [Azure Key Vault ajánlott eljárások](key-vault-best-practices.md) áttekintése
