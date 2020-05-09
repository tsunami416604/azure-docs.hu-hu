---
title: Gyors útmutató – Azure Key Vault a Javához készült ügyféloldali kódtár
description: Az Azure SDK-ügyfél kódtárainak írására szolgáló formátum-és tartalmi feltételeket biztosít.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: a3e6901d41cc14ab3ea27f7c8cd9bbc4c98608a6
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901336"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Gyors útmutató: Azure Key Vault a Javához készült ügyféloldali kódtár

Ismerkedjen meg a Javához készült Azure Key Vault-ügyfél függvénytárával. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot.

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Javához készült Key Vault ügyféloldali kódtár a következőre használható:

- Fokozza a biztonságot, és szabályozhatja a kulcsokat és a jelszavakat.
- Percek alatt létrehozhatja és importálhatja a titkosítási kulcsokat.
- Csökkentse a késést a felhő méretezésével és a globális redundanciával.
- Leegyszerűsítheti és automatizálhatja a TLS/SSL-tanúsítványok feladatait.
- Használja az FIPS 140-2 2-es szintű hitelesített HSM.

[A forráskód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault)[API-referenciájának dokumentációja](https://azure.github.io/azure-sdk-for-java) | [termék dokumentációs](index.yml) | [mintái](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)  | 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) 8-as vagy újabb verziója
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview)

Ez a rövid útmutató azt feltételezi, hogy az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) -t és az [Apache mavent](https://maven.apache.org) egy Linux-terminál ablakban futtatja.

## <a name="setting-up"></a>Beállítás

### <a name="create-new-java-console-app"></a>Új Java Console-alkalmazás létrehozása

A konzol ablakban a `mvn` parancs használatával hozzon létre egy új Java-konzol alkalmazást a névvel. `akv-java`

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

A projekt generálásának kimenete a következőképpen fog kinézni:

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

Módosítsa a könyvtárat az újonnan létrehozott AKV-Java/-mappába.

```console
cd akv-java
```

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a *Pom. XML* fájlt a szövegszerkesztőben. Adja hozzá az alábbi függőségi elemeket a függőségek csoportjához.

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

Ez a rövid útmutató egy előre létrehozott Azure Key vaultot használ. Kulcstartó létrehozásához kövesse az [Azure CLI](quick-create-cli.md)gyors üzembe helyezésének lépéseit, [Azure PowerShell](quick-create-powershell.md)a gyors üzembe helyezést, vagy [Azure Portal](quick-create-portal.md)a gyors üzembe helyezést. Azt is megteheti, hogy az alábbi Azure CLI-parancsokat is futtatja.

> [!Important]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. A következő példákban cserélje le a <az egyedi-kulcstartó-Name> a Key Vault nevét.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A felhőalapú alkalmazások hitelesítésének legegyszerűbb módja a felügyelt identitás; a részletekért tekintse meg a [app Service felügyelt identitás használata a Azure Key Vault eléréséhez](../general/managed-identity.md) című témakört.

Az egyszerűség kedvéért azonban ez a rövid útmutató egy asztali alkalmazást hoz létre, amely egy egyszerű szolgáltatásnév és egy hozzáférés-vezérlési házirend használatát igényli. A szolgáltatási elv egyedi nevet igényel a "http://<My-Unique-Service-elven-Name>" formátumban.

Hozzon létre egy szolgáltatási elvet az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancs használatával:

```azurecli
az ad sp create-for-rbac -n "http://<my-unique-service-principle-name>" --sdk-auth
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
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Jegyezze fel a clientId, a clientSecret és a tenantId, ahogy azokat a következő két lépésben használni fogjuk.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatás egyszerű hozzáférésének biztosítása a kulcstartóhoz

Hozzon létre egy olyan hozzáférési szabályzatot a kulcstartó számára, amely engedélyt ad a szolgáltatásnak a clientId az az a Key Vault [set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal. Adja meg a szolgáltatás egyszerű lekérését, listáját, és állítsa be mindkét kulcs és titok engedélyeit.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Környezeti változók beállítása

Az alkalmazás DefaultAzureCredential metódusa három környezeti változóra támaszkodik: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`és. `AZURE_TENANT_ID` használja ezeket a változókat az clientId, a clientSecret és a tenantId értékre, amelyet az [egyszerű szolgáltatásnév létrehozása](#create-a-service-principal) lépésben feljegyzett. A környezeti `export VARNAME=VALUE` változók beállításához használja a formátumot. (Ez a módszer csak a rendszerhéjból létrehozott jelenlegi rendszerhéj és folyamatok változóit állítja be, hogy véglegesen hozzáadja ezeket a változókat a környezetéhez, `/etc/environment ` szerkessze a fájlt.) 

A kulcstároló nevét a nevű `KEY_VAULT_NAME`környezeti változó néven is menteni kell.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektummodell

A Java-hoz készült Azure Key Vault ügyféloldali kódtár lehetővé teszi a kulcsok és a kapcsolódó eszközök, például tanúsítványok és titkos kódok kezelését. Az alábbi kódrészletek megmutatják, hogyan hozhat létre egy ügyfelet, hogyan állíthat be titkos kulcsot, beolvashat egy titkos kulcsot, és törölhet egy titkos kulcsot.

A teljes konzolos alkalmazás [alább](#sample-code)látható.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a kód elejéhez:

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

A Key Vault hitelesítése és a Key Vault-ügyfél létrehozása a fenti [környezeti változók beállítása](#set-environmental-variables) című lépés környezeti változóktól függ. A kulcstartó neve a Key Vault URI-ra van bontva, a következő formátumban `https://<your-key-vault-name>.vault.azure.net`:.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Titkos kód mentése

Most, hogy az alkalmazás hitelesítése megtörtént, a `secretClient.setSecret` metódus használatával titkos kulcsot helyezhet a kulcstartóba. Ehhez meg kell adni a titkos kulcsot – a "keresési kifejezésként" értéket hozzá kell rendelni az `secretName` ebben a példában szereplő változóhoz.  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Ellenőrizze, hogy a titkos kulcs be van-e állítva az az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Titkos kód beolvasása

Most már lekérheti a korábban beállított értéket a `secretClient.getSecret` metódussal.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Most már elérheti a beolvasott titok értékét a használatával `retrievedSecret.getValue()`.

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül törölje a titkos kulcsot a Key vaultból a `secretClient.beginDeleteSecret` metódussal.

```java
secretClient.beginDeleteSecret(secretName);
```

A titkos kód az az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal ellenőrizhető:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja az Azure CLI-t vagy Azure PowerShell a kulcstartó és a hozzá tartozó erőforráscsoport eltávolításához.

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

Ebben a rövid útmutatóban létrehozott egy titkos kulcsot, és lekérte a titkos kulcsot. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése
