---
title: Rövid útmutató – Azure Key Vault-ügyféltár a Node.js fájlhoz (v4)
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkokat egy Azure-kulcstárolóból a Node.js ügyféltár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 609076cd204457bb7c952c91cd249b1aece58022
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425013"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Rövid útmutató: Azure Key Vault-ügyféltár node.js (v4)

Ismerkedés az Azure Key Vault ügyfélkódtár node.js. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapvető feladatok példakódját.

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Használja a Key Vault-ügyfélkódtár a Node.js a következőkhöz:

- Növelje a biztonságot és szabályozza a kulcsokat és jelszavakat.
- Titkosítási kulcsok létrehozása és importálása percek alatt.
- Csökkentse a késést a felhőalapú skálával és a globális redundanciával.
- Egyszerűsítse és automatizálja a TLS/SSL-tanúsítványok feladatait.
- Használja fips 140-2 Level 2 validált HSMs.

[API-referenciadokumentáció](/javascript/api/overview/azure/key-vault?view=azure-node-latest) | [– könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | Csomag[(npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Az operációs rendszer aktuális [Node.js fájljai.](https://nodejs.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview)

Ez a rövid útmutató feltételezi, hogy [az Azure CLI-t](/cli/azure/install-azure-cli?view=azure-cli-latest) linuxos terminálablakban futtatja.

## <a name="setting-up"></a>Beállítása

### <a name="install-the-package"></a>A csomag telepítése

A konzolablakból telepítse az Azure Key Vault titkos tárat a Node.js.

```console
npm install @azure/keyvault-secrets
```

Ehhez a rövid útmutatóhoz telepítenie kell az azure.identity csomagot is:

```console
npm install @azure/identity
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

A felhőalapú alkalmazások hitelesítésének legegyszerűbb módja a felügyelt identitás; A részleteket az Azure Key Vault eléréséhez az [App Service felügyelt identitásának használata.](../general/managed-identity.md) Az egyszerűség kedvéért azonban ez a rövid útmutatók létrehoz egy konzolalkalmazást. Egy asztali alkalmazás azure-ral történő hitelesítéséhez egyszerű szolgáltatásés hozzáférés-vezérlési szabályzat használata szükséges.

Hozzon létre egy szolgáltatási elvet az Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) paranccsal:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Ez a művelet kulcs/ értékpárok sorozatát adja vissza. 

```azurecli
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

Vegye figyelembe az ügyfélazonosítót és az ügyféltitkos kulcsot, mivel az alábbi [Környezeti változó beállítása](#set-environmental-variables) lépésben fogjuk használni őket.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatásegyszerű hozzáférés a key vaulthoz

Hozzon létre egy hozzáférési szabályzatot a key vault, amely engedélyt ad a szolgáltatásnév átadásával az ügyfélazonosító az [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancs. Adja meg a szolgáltatás egyszerű lekérni, listát, és engedélyeket a kulcsok és a titkos kulcsok.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Környezeti változók beállítása

Az alkalmazásDefaultAzureCredential metódusa három környezeti változóra `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET`támaszkodik: , és `AZURE_TENANT_ID`. Állítsa be ezeket a változókat az ügyfélazonosító, az ügyféltitkos és a tenantId értékekre, amelyeket a `export VARNAME=VALUE` formátum használatával egyszerű szolgáltatás [létrehozása](#create-a-service-principal) lépésben észlelt. (Ez csak az aktuális rendszerhéj és a rendszerhéjból létrehozott folyamatok változóit állítja be; `/etc/environment ` ha véglegesen hozzá szeretné adni ezeket a változókat a környezethez, szerkesztheti a fájlt.) 

A kulcstartó nevét is mentenie kell egy `KEY_VAULT_NAME`környezeti változóként, amelynek neve .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektummodell

Az Azure Key Vault ügyfélkódtár node.js lehetővé teszi a kulcsok és a kapcsolódó eszközök, például a tanúsítványok és a titkos kulcsok kezelését. Az alábbi kódminták megmutatják, hogyan hozhat létre ügyfelet, állíthat be egy titkos kulcsot, hogyan kérhet le egy titkos kulcsot, és hogyan törölhet egy titkos kulcsot.

A teljes konzolalkalmazás https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-appelérhető a következő helyen: .

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Direktívák hozzáadása

Adja hozzá a következő irányelveket a kód tetejéhez:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

A kulcstartóba való hitelesítés és a key vault-ügyfél létrehozása a fenti környezeti változók beállítása és a [SecretClient konstruktor](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-)környezeti [változóitól](#set-environmental-variables) függ. 

A kulcstartó neve ki van bontva a key `https://<your-key-vault-name>.vault.azure.net`vault URI-ra, formátumban. 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Titkos fájl mentése

Most, hogy az alkalmazás hitelesítése, akkor egy titkos kulcsot a keyvault segítségével [client.setSecret módszer](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-) Ez megköveteli a nevet a titkos - mi használ "mySecret" ebben a mintában.  

```javascript
await client.setSecret(secretName, secretValue);
```

Ellenőrizheti, hogy a titkos kulcs az [az keyvault titkos show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal lett-e beállítva:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Titkos titok beolvasása

Most már bekeresheti a korábban beállított értéket az [client.getSecret metódussal.](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-)

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

A titka `retrievedSecret.value`most mentve .

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül töröljük a titkos kulcsot a kulcstartóból az [client.beginDeleteSecret metódussal.](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-)

```javascript
await client.beginDeleteSecret(secretName)
```

Ellenőrizheti, hogy a titkos titok eltűnt-e az [az keyvault titkos show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
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

Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy titkos kulcsot, és lekérte azt. Ha többet szeretne megtudni a Key Vaultról és arról, hogyan integrálhatja azt az alkalmazásokkal, folytassa az alábbi cikkekkel.

- Az [Azure Key Vault áttekintésének elolvasása](../general/overview.md)
- Tekintse meg az [Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](../general/best-practices.md)