---
title: Gyors útmutató – Azure Key Vault ügyféloldali kódtár Node.jshoz (v4)
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kulcsokat egy Azure Key vaultból az Node.js ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 1157fb69704af59a75989b22338b88a8576428ce
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076672"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Gyors útmutató: Azure Key Vault Node.jshez készült ügyféloldali kódtár (v4)

Ismerkedjen meg a Node.js Azure Key Vault ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot.

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Node.js a Key Vault ügyféloldali kódtár használatával:

- Fokozza a biztonságot, és szabályozhatja a kulcsokat és a jelszavakat.
- Percek alatt létrehozhatja és importálhatja a titkosítási kulcsokat.
- Csökkentse a késést a felhő méretezésével és a globális redundanciával.
- Leegyszerűsítheti és automatizálhatja a TLS/SSL-tanúsítványok feladatait.
- Használja az FIPS 140-2 2-es szintű hitelesített HSM.

[API-referenciák dokumentációja](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index?view=azure-node-latest)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Az operációs rendszer jelenlegi [Node.js](https://nodejs.org) .
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/)

Ez a rövid útmutató azt feltételezi, hogy az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) -t egy Linux-terminál ablakban futtatja.

## <a name="setting-up"></a>Beállítás

### <a name="install-the-package"></a>A csomag telepítése

A konzol ablakban telepítse a Node.js Azure Key Vault Secrets könyvtárát.

```console
npm install @azure/keyvault-secrets
```

Ebben a rövid útmutatóban az Azure. Identity csomagot is telepítenie kell:

```console
npm install @azure/identity
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

A Node.js Azure Key Vault ügyféloldali kódtára lehetővé teszi a kulcsok és a kapcsolódó eszközök, például tanúsítványok és titkos kódok kezelését. Az alábbi kódrészletek megmutatják, hogyan hozhat létre egy ügyfelet, hogyan állíthat be titkos kulcsot, beolvashat egy titkos kulcsot, és törölhet egy titkos kulcsot.

A teljes konzol alkalmazás a következő címen érhető el: https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app .

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a kód elejéhez:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

A Key Vault hitelesítése és a Key Vault-ügyfél létrehozása a környezeti változók [beállítása](#set-environmental-variables) a fenti lépésekben, valamint a [SecretClient konstruktorban](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-)függ. 

A kulcstartó neve a Key Vault URI-ra van bontva, a következő formátumban: `https://<your-key-vault-name>.vault.azure.net` . 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Titkos kód mentése

Most, hogy az alkalmazás hitelesítése megtörtént, egy titkos kulcsot helyez el a kulcstartóba a [Client. setSecret metódus](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-) használatával, amelyhez szükség van a titkos kulcs nevére – ebben a példában a "keresési kifejezésként" nevet használjuk.  

```javascript
await client.setSecret(secretName, secretValue);
```

Ellenőrizze, hogy a titkos kulcs be van-e állítva az az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Titkos kód beolvasása

Most már lekérheti a korábban beállított értéket a [Client. getSecret metódussal](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

A titkos kód most már mentve van `retrievedSecret.value` .

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül törölje a titkos kulcsot a Key vaultból a [Client. beginDeleteSecret metódussal](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-).

```javascript
await client.beginDeleteSecret(secretName)
```

A titkos kód az az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal ellenőrizhető:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, használhatja az Azure CLI-t vagy Azure PowerShell a kulcstartó és a hozzá tartozó erőforráscsoport eltávolításához.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Mintakód

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy titkos kulcsot, és lekérte a titkos kulcsot. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése