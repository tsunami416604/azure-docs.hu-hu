---
title: Rövid útmutató – Azure Key Vault-ügyféltár a .NET-hez (SDK v3)
description: Megtudhatja, hogy miként hozhat létre, kérhet le és törölhet titkokat egy Azure-kulcstárolóból a .NET ügyfélkódtár használatával (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: bdc686284711fb39e6cedb18aea4d1f5c9d1d318
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425006"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Rövid útmutató: Azure Key Vault-ügyféltár a .NET -hez (SDK v3)

Ismerkedés az Azure Key Vault ügyféltár .NET. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapvető feladatok példakódját.

> [!NOTE]
> Ez a rövid útmutató a Microsoft.Azure.KeyVault ügyfélkódtár 3.0.4-es verzióját használja. A Key Vault-ügyféltár legfrissebb verzióját a [.NET (SDK v4) Azure Key Vault ügyféltárban](quick-create-net.md)található. 

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A .NET key vault-ügyféltár segítségével:

- Növelje a biztonságot és szabályozza a kulcsokat és jelszavakat.
- Titkosítási kulcsok létrehozása és importálása percek alatt.
- Csökkentse a késést a felhőalapú skálával és a globális redundanciával.
- Egyszerűsítse és automatizálja a TLS/SSL-tanúsítványok feladatait.
- Használja fips 140-2 Level 2 validált HSMs.

[API-referenciadokumentáció](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [könyvtár forráskódcsomagja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. Cserélje le <egyedi keyvault-neve> a kulcstartó nevét az alábbi példákban.


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A [.NET Core 2.1 SDK vagy újabb](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview)

Ez a rövid útmutató `dotnet`feltételezi, hogy windowsos terminálon (például [PowerShell Core,](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6) [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)vagy Azure [Cloud Shell)](https://shell.azure.com/)fut, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)és Windows-parancsokat futtat.

## <a name="setting-up"></a>Beállítása

### <a name="create-new-net-console-app"></a>Új .NET konzolalkalmazás létrehozása

A konzolablakban a `dotnet new` paranccsal hozzon létre egy `akv-dotnet`új .


```console
dotnet new console -n akvdotnet
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
dotnet add package Microsoft.Azure.KeyVault
```

Ehhez a rövid útmutatóhoz a következő csomagokat is telepítenie kell:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

Ez a rövid útmutató egy előre létrehozott Azure-kulcstartót használ. Az [Azure CLI gyorsútmutató](quick-create-cli.md), az [Azure PowerShell gyorsindítása](quick-create-powershell.md)vagy az Azure Portal gyorsindításlépéseit követve hozhat létre kulcstrekész [tárolót.](quick-create-portal.md) Azt is megteheti, hogy egyszerűen futtassa az Azure CLI-parancsokat.

> [!Important]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. Cserélje le <egyedi keyvault-neve> a kulcstartó nevét az alábbi példákban.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A felhőalapú .NET-alkalmazások hitelesítésének legegyszerűbb módja felügyelt identitás; A részleteket az Azure Key Vault eléréséhez az [App Service felügyelt identitásának használata.](../general/managed-identity.md) Az egyszerűség kedvéért azonban ez a rövid útmutató létrehoz egy .NET konzolalkalmazást. Egy asztali alkalmazás azure-ral történő hitelesítéséhez egyszerű szolgáltatásés hozzáférés-vezérlési szabályzat használata szükséges.

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

Vegye figyelembe az ügyfélazonosítót és az ügyféltitkos kulcsot, mivel az alábbi [kulcstartóban](#authenticate-to-your-key-vault) a Hitelesítés a hitelesítés ben fogjuk használni őket.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatásegyszerű hozzáférés a key vaulthoz

Hozzon létre egy hozzáférési szabályzatot a key vault, amely engedélyt ad a szolgáltatásnév átadásával az ügyfélazonosító az [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancs. Adja meg a szolgáltatás egyszerű lekérni, listát, és engedélyeket a kulcsok és a titkos kulcsok.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Objektummodell

Az Azure Key Vault ügyfélkódtár .NET lehetővé teszi a kulcsok és a kapcsolódó eszközök, például a tanúsítványok és a titkos kulcsok kezelését. Az alábbi kódminták megmutatják, hogyan állíthat be egy titkot, és hogyan kérhet le egy titkot.

A teljes konzolalkalmazás https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnetelérhető a következő helyen: .

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Direktívák hozzáadása

Adja hozzá a következő irányelveket a kód tetejéhez:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Hitelesítés a kulcstartóban

Ez a .NET gyorsindítás a környezeti változókra támaszkodik a kódba nem helyezett hitelesítő adatok tárolásához. 

Az alkalmazás létrehozása és futtatása `setx` előtt a `akvClientId` `akvClientSecret`paranccsal állítsa be a , , , `akvTenantId`és `akvSubscriptionId` a környezeti változókat a fent említett értékekre.

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

**Macos**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Rendelje hozzá ezeket a környezeti változókat a kódkarakterláncaihoz, majd hitelesítse az alkalmazást a [KeyVaultClient osztálynak](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)való átadással:

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Titkos fájl mentése

Most, hogy az alkalmazás hitelesítve van, a [SetSecretAsync metódus](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) használatával titkos kulcsot helyezhet el a `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`keyvaultban. A titoknak is nevet kell használnia, a "mySecret"-et használjuk. 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Ellenőrizheti, hogy a titkos kulcs az [az keyvault titkos show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal lett-e beállítva:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Titkos titok beolvasása

Most már bekeresheti a korábban beállított értéket a [GetSecretAsync metódussal](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

A titka `keyvaultSecret.Value;`most mentve .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, használhatja az Azure CLI vagy az Azure PowerShell a key vault és a megfelelő erőforráscsoport eltávolításához.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy titkos kulcsot, és lekérte azt. Tekintse meg a [teljes konzolalkalmazást a GitHubon.](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet)

Ha többet szeretne megtudni a Key Vaultról és arról, hogyan integrálhatja azt az alkalmazásokkal, folytassa az alábbi cikkekkel.

- [Szolgáltatás-szolgáltatás hitelesítés](../general/service-to-service-authentication.md) megvalósítása az Azure Key Vault használatával .
- Az [Azure Key Vault áttekintésének elolvasása](../general/overview.md)
- Tekintse meg az [Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](../general/best-practices.md)
