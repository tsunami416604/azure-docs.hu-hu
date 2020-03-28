---
title: Oktatóanyag az Azure App konfigurációs kulcstárolójának hivatkozásaihoz egy Java tavaszi rendszerindítási alkalmazásban | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure App Configuration Key Vault-referenciáit egy Java spring boot alkalmazásból
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: b6b6d10165eed331c397e17a18e382b095e1f74f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216740"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Oktatóanyag: Key Vault-hivatkozások használata Java Spring alkalmazásban

Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure App Konfigurációs szolgáltatás az Azure Key Vault használatával. Az alkalmazáskonfiguráció és a Key Vault a legtöbb alkalmazástelepítésben egymás mellett használt kiegészítő szolgáltatások.

Az Alkalmazáskonfiguráció segít a szolgáltatások együttes használatában a Key Vaultban tárolt értékeket tartalmazó kulcsok létrehozásával. Amikor az Alkalmazás konfigurációja ilyen kulcsokat hoz létre, a Key Vault-értékek URI-it tárolja, nem pedig magukat az értékeket.

Az alkalmazás az alkalmazáskonfigurációs ügyfélszolgáltató segítségével olvassa be a Key Vault-hivatkozásokat, ugyanúgy, mint az alkalmazáskonfigurációban tárolt többi kulcs esetében. Ebben az esetben az alkalmazáskonfigurációban tárolt értékek olyan URI-k, amelyek a Key Vault ban lévő értékekre hivatkoznak. Ezek nem key vault-értékek vagy hitelesítő adatok. Mivel az ügyfélszolgáltató felismeri a kulcsokat key vault-hivatkozásként, a Key Vault segítségével lekéri az értékaikat.

Az alkalmazás felelős az alkalmazáskonfiguráció és a Key Vault megfelelő hitelesítésért. A két szolgáltatás nem kommunikál közvetlenül.

Ez az oktatóanyag bemutatja, hogyan valósíthatja meg a Key Vault-hivatkozásokat a kódban. A rövid útmutatókban bevezetett webalkalmazásra épül. A folytatás előtt töltse ki [a Java Spring alkalmazás létrehozása az alkalmazás konfigurációjával](./quickstart-java-spring-app.md) című alkalmazást.

Bármelyik kódszerkesztőt használhatja az oktatóanyag lépéseihez. A [Visual Studio Code](https://code.visualstudio.com/) például egy platformfüggetlen kódszerkesztő, amely a Windows, a macOS és a Linux operációs rendszerekhez érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy alkalmazáskonfigurációs kulcsot, amely a Key Vaultban tárolt értékre hivatkozik.
> * A kulcs értékének elérése Java Spring alkalmazásból.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Egy támogatott [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) a 8-as verzióval.
* [Apache Maven](https://maven.apache.org/download.cgi) 3.0-s vagy újabb verzió.

## <a name="create-a-vault"></a>Tároló létrehozása

1. Válassza az **Erőforrás létrehozása** lehetőséget az Azure Portal bal felső sarkában:

    ![Kimenet a kulcstartó létrehozása után](./media/quickstarts/search-services.png)
1. A keresőmezőbe írja be a Key Vault ( Key Vault ) **mezőbe.**
1. Az eredmények listájában válassza a bal oldali **kulcstartók** lehetőséget.
1. A **kulcstartókban**válassza **a Hozzáadás**lehetőséget.
1. A key **vault létrehozása**jobb oldalán adja meg a következő információkat:
    * **Az előfizetés** kiválasztásához válassza az Előfizetés lehetőséget.
    * Az **Erőforráscsoport**csoportban válassza **az Új létrehozása** lehetőséget, és adjon meg egy erőforráscsoport nevét.
    * A **Key Vault neve,** egyedi nevet kell megadni. Ebben az oktatóanyagban adja meg **a Contoso-vault2**értéket.
    * A **Régió** legördülő listában válasszon egy helyet.
1. Hagyja a többi **Key Vault-beállítás létrehozása** az alapértelmezett értékekkel.
1. Kattintson a **Létrehozás** gombra.

Ezen a ponton az Azure-fiók az egyetlen, amely jogosult az új tároló eléréséhez.

![Kimenet a kulcstartó létrehozása után](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Ha titkos kulcsot szeretne hozzáadni a tárolóhoz, csak néhány további lépést kell tennie. Ebben az esetben adjon hozzá egy üzenetet, amely segítségével tesztelheti a Key Vault beolvasása. Az üzenet neve **Üzenet**, és a "Hello from Key Vault" értéket tárolja benne.

1. A Key Vault tulajdonságlapjain válassza a **Titkok**lehetőséget.
1. Válassza **a Létrehozás/importálás**lehetőséget.
1. A **Titkos kapcsolat ablaktáblában** adja meg a következő értékeket:
    * **Feltöltési beállítások**: Írja be **a kézi értéket**.
    * **Név**: Üzenet **beírása**.
    * **Érték**: Adja meg **a Hello a Key Vault**.
1. Hagyja a másik **hozzon létre egy titkos** tulajdonságokat az alapértelmezett értékekkel.
1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Kulcstartóhivatkozás hozzáadása az alkalmazás konfigurációjához

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza az **Összes erőforrás**lehetőséget, majd válassza ki a rövid útmutatóban létrehozott Alkalmazáskonfigurációs tároló példányt.

1. Válassza a **Configuration Explorer**lehetőséget.

1. Válassza a + Key vault **hivatkozásának** > **Key vault reference**lehetőséget, majd adja meg a következő értékeket:
    * **Kulcs**: Válassza a **/application/config.keyvaultmessage lehetőséget**
    * **Címke**: Hagyja üresen ezt az értéket.
    * **Előfizetés**, **Erőforrás csoport**és **Kulcstartó**: Adja meg az előző szakaszban létrehozott key vault értékeinek megfelelő értékeket.
    * **Titkos:** Válassza ki az előző szakaszban létrehozott **Üzenet** titkos kulcsot.

## <a name="connect-to-key-vault"></a>Csatlakozás a Key Vaulthoz

1. Ebben az oktatóanyagban egy egyszerű szolgáltatása a Key Vault hitelesítéséhez. Az egyszerű szolgáltatás létrehozásához használja az Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Ez a művelet kulcs-/értékpárok sorozatát adja vissza:

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

1. Futtassa a következő parancsot, hogy az egyszerű szolgáltatás hozzáférjen a kulcstartóhoz:

    ```console
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Futtassa a következő parancsot az objektum-id levételéhez, majd adja hozzá az alkalmazás konfigurációjához.

    ```console
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Hozza létre a következő környezeti változókat az előző lépésben megjelenített egyszerű szolgáltatásértékek használatával:

    * **AZURE_CLIENT_ID**: *ügyfélazonosító*
    * **AZURE_CLIENT_SECRET**: *clientSecret*
    * **AZURE_TENANT_ID**: *tenantId*

> [!NOTE]
> Ezek a Key Vault hitelesítő adatok csak az alkalmazáson belül használható.  Az alkalmazás közvetlenül hitelesíti magát a Key Vault ezekkel a hitelesítő adatokkal az alkalmazás konfigurációs szolgáltatásának bevonása nélkül.  A Key Vault hitelesítést biztosít mind az alkalmazás, mind az alkalmazás konfigurációs szolgáltatása számára kulcsok megosztása vagy kitárása nélkül.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>A kód frissítése key vault-hivatkozás használatához

1. Nyissa meg *a MessageProperties.java*. Adjon hozzá egy keyVaultMessage nevű új *változót:*

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Nyissa meg *a HelloController.java*. Frissítse a *getMessage* metódust, hogy tartalmazza a Key Vaultból beolvasott üzenetet.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Hozzon létre egy *azureCredentials.java* nevű új fájlt, és adja hozzá az alábbi kódot.

    ```java
    package com.example;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Hozzon létre egy új fájlt *Nevű AppConfiguration.java*. És adja hozzá az alábbi kódot.

    ```java
    package com.example;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Hozzon létre egy új fájlt a források META-INF könyvtár nevű *spring.factories* és add.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.AppConfiguration
    ```

1. Készítsd el a Tavaszi Boot alkalmazást a Mavennel, és futtassa, például:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Az alkalmazás futtatása után a *curl* segítségével tesztelheti az alkalmazást, például:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Megjelenik az alkalmazáskonfigurációs tárolóban megadott üzenet. A Key Vaultban megadott üzenet is megjelenik.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy alkalmazáskonfigurációs kulcsot, amely a Key Vaultban tárolt értékre hivatkozik. Ha meg szeretné tudni, hogyan használhatja a funkciójelzőket a Java Spring alkalmazásban, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitásintegráció](./quickstart-feature-flag-spring-boot.md)
