---
title: Rövid útmutató – Azure Key Vault-ügyféltár Java-hoz
description: Formátum- és tartalomfeltételeket biztosít az Azure SDK-ügyféltárak rövid útmutatóinak írásához.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 7e0d05ce97def0a255e6ac9909544b04fb13bb37
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425027"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Rövid útmutató: Azure Key Vault-ügyféltár Java-hoz

Ismerkedés az Azure Key Vault ügyféltár Java. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapvető feladatok példakódját.

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Használja a Key Vault java-alapú ügyféltárt a következőkhöz:

- Növelje a biztonságot és szabályozza a kulcsokat és jelszavakat.
- Titkosítási kulcsok létrehozása és importálása percek alatt.
- Csökkentse a késést a felhőalapú skálával és a globális redundanciával.
- Egyszerűsítse és automatizálja a TLS/SSL-tanúsítványok feladatait.
- Használja fips 140-2 Level 2 validált HSMs.

[Forráskód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault) | [API referenciadokumentáció](https://azure.github.io/azure-sdk-for-java) | [Termékdokumentáció](index.yml) | [minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) 8-as vagy újabb verzió
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview)

Ez a rövid útmutató feltételezi, hogy [az Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) és az [Apache Maven](https://maven.apache.org) fut egy Linux terminálablakban.

## <a name="setting-up"></a>Beállítása

### <a name="create-new-java-console-app"></a>Új Java konzolalkalmazás létrehozása

A konzolablakban a `mvn` paranccsal hozzon létre egy `akv-java`új Java konzolalkalmazást a nevével.

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

A projekt generálásának kimenete valahogy így fog kinézni:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Módosítsa a könyvtárat az újonnan létrehozott akv-java/ mappára.

```console
cd akv-java
```

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá a következő függőségi elemeket a függőségek csoportjához.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.0.0</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

Ez a rövid útmutató egy előre létrehozott Azure-kulcstartót használ. Az [Azure CLI gyorsútmutató](quick-create-cli.md), az [Azure PowerShell gyorsindítása](quick-create-powershell.md)vagy az Azure Portal gyorsindításlépéseit követve hozhat létre kulcstrekész [tárolót.](quick-create-portal.md) Másik lehetőségként futtathatja az Azure CLI-parancsokat alább.

> [!Important]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. Cserélje le <egyedi keyvault-neve> a kulcstartó nevét az alábbi példákban.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A felhőalapú alkalmazások hitelesítésének legegyszerűbb módja a felügyelt identitás; A részleteket az Azure Key Vault eléréséhez az [App Service felügyelt identitásának használata.](../general/managed-identity.md) Az egyszerűség kedvéért azonban ez a rövid útmutató létrehoz egy asztali alkalmazást, amely egyszerű szolgáltatás és hozzáférés-vezérlési házirend használatát igényli.

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

Vegye figyelembe az ügyfélazonosítót, az ügyféltitkos kulcsot és a tenantId azonosítót, mivel a következő két lépésben használni fogjuk őket.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatásegyszerű hozzáférés a key vaulthoz

Hozzon létre egy hozzáférési szabályzatot a key vault, amely engedélyt ad a szolgáltatásnév átadásával az ügyfélazonosító az [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancs. Adja meg a szolgáltatás egyszerű lekérni, listát, és engedélyeket a kulcsok és a titkos kulcsok.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Környezeti változók beállítása

Az alkalmazásDefaultAzureCredential metódusa három környezeti változóra `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET`támaszkodik: , és `AZURE_TENANT_ID`. használja ezeket a változókat az ügyfélazonosító, clientSecret és tenantId értékeket, amelyeket a fenti [Szolgáltatásegyszerű szolgáltatás létrehozása](#create-a-service-principal) lépésben észlelt. Használja `export VARNAME=VALUE` a formátumot a környezeti változók beállításához. (Ez a módszer csak az aktuális rendszerhéj és a rendszerhéjból létrehozott folyamatok változóit állítja `/etc/environment ` be; hogy véglegesen hozzáadja ezeket a változókat a környezethez, és szerkesztheti a fájlt.) 

A kulcstartó nevét is mentenie kell egy `KEY_VAULT_NAME`környezeti változóként, amelynek neve .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektummodell

Az Azure Key Vault ügyfélkódtár Java lehetővé teszi a kulcsok és a kapcsolódó eszközök, például a tanúsítványok és a titkos kulcsok kezelését. Az alábbi kódminták megmutatják, hogyan hozhat létre ügyfelet, állíthat be egy titkos kulcsot, hogyan kérhet le egy titkos kulcsot, és hogyan törölhet egy titkos kulcsot.

A teljes konzolalkalmazás az [alábbiakban](#sample-code)található.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Direktívák hozzáadása

Adja hozzá a következő irányelveket a kód tetejéhez:

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

A kulcstartóba való hitelesítés és a key vault-ügyfél létrehozása a környezeti változóktól függ a [fenti környezeti változók](#set-environmental-variables) beállításában. A kulcstartó neve ki van bontva a key `https://<your-key-vault-name>.vault.azure.net`vault URI-ra, formátumban.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Titkos fájl mentése

Most, hogy az alkalmazás hitelesítése, a `secretClient.setSecret` módszer rel a keyvault egy titkos kulcsot helyezhet el. Ehhez a titkos névre van szükség – a "mySecret" `secretName` értéket hozzárendeltük a minta változójához.  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Ellenőrizheti, hogy a titkos kulcs az [az keyvault titkos show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal lett-e beállítva:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Titkos titok beolvasása

Most már bekeresheti a `secretClient.getSecret` korábban beállított értéket a metódussal.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Most már elérheti a beolvasott titkos `retrievedSecret.getValue()`kulcsot a segítségével.

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül törölje a titkos kulcsot a kulcstárolóból a `secretClient.beginDeleteSecret` módszerrel.

```java
secretClient.beginDeleteSecret(secretName);
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

```java
package com.keyvault.quickstart;

import java.io.Console;   

import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String kvUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, kvUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(kvUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();


        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Input the value of your secret > ");
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");

        System.out.println("Forgetting your secret.");
        secretValue = "";
        System.out.println("Your secret is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        secretClient.beginDeleteSecret(secretName);

        System.out.println("done.");


    }
}
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy titkos kulcsot, és lekérte azt. Ha többet szeretne megtudni a Key Vaultról és arról, hogyan integrálhatja azt az alkalmazásokkal, folytassa az alábbi cikkekkel.

- Az [Azure Key Vault áttekintésének elolvasása](../general/overview.md)
- Tekintse meg az [Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](../general/best-practices.md)
