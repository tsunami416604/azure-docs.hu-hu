---
title: A Jenkins az Azure Functions beépülő modul használata az Azure Functions üzembe helyezése
description: Ismerje meg, hogyan helyezhet üzembe Azure Functions használatával a Jenkins az Azure Functions beépülő modul
ms.service: jenkins
keywords: a jenkins, azure, devops, a java, az azure functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 02/23/2019
ms.openlocfilehash: 93504de6384be530ba037f662f7b043729aa3f99
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536914"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plugin"></a>A Jenkins az Azure Functions beépülő modul használata az Azure Functions üzembe helyezése

[Az Azure Functions](/azure/azure-functions/) egy kiszolgáló nélküli számítási szolgáltatás. Azure Functions használatával futtathatja igény szerinti kiépítés és infrastruktúra kezelése nélkül. Ez az oktatóanyag bemutatja egy Java-függvény üzembe helyezése az Azure Functions az Azure Functions beépülő modul használatával.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- **Jenkins-kiszolgáló**: Ha nem rendelkezik Jenkins-kiszolgáló telepítve van, tekintse meg a cikket [Jenkins-kiszolgáló létrehozása az Azure-ban](./install-jenkins-solution-template.md).

 > [!TIP]
 > Ebben az oktatóanyagban használt forráskódot található a [Visual Studio China GitHub-adattárat](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Egy Java-függvény létrehozása

Egy Java-függvény létrehozása a Java-futtatókörnyezet hibával, vagy használja a [az Azure portal](https://portal.azure.com) vagy a [Azure CLI-vel](/cli/azure/?view=azure-cli-latest).

A következő lépések bemutatják, hogyan hozhat létre egy Java-függvényt az Azure CLI használatával:

1. Hozzon létre egy erőforráscsoportot, és cserélje le a  **&lt;resource_group >** helyőrzőt az erőforráscsoport nevét.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Hozzon létre egy Azure storage-fiókot, és cserélje le a helyőrzőket a megfelelő értékekre.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. A helyőrzőket cserélje le a megfelelő értékeket a teszt függvényalkalmazás létrehozásához.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```
    
1. Frissítési verzió 2.x verziójú futtatókörnyezet, és cserélje le a helyőrzőket a megfelelő értékekre.

    ```cli
    az functionapp config appsettings set --name <function_app> --resource-group <resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2
    ```

## <a name="prepare-jenkins-server"></a>Jenkins-kiszolgáló előkészítése

A következő lépések elmagyarázzák, hogyan készíti elő a Jenkins-kiszolgáló:

1. Üzembe helyezése egy [Jenkins-kiszolgáló](https://aka.ms/jenkins-on-azure) az Azure-ban. Ha még nem rendelkezik a Jenkins-kiszolgáló telepítve van, a cikk egy példányát [Jenkins-kiszolgáló létrehozása az Azure-ban](./install-jenkins-solution-template.md) végigvezeti a folyamaton.

1. Jelentkezzen be a Jenkins-példány az ssh-val.

1. A Jenkins-példány telepítse a maven használatával a következő parancsot:

    ```terminal
    sudo apt install -y maven
    ```

1. A Jenkins-példány, telepítse a [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) szerint a következő parancs a terminál a parancssorba:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. A Jenkins irányítópultján a következő beépülő modulok telepítése:

    - Az Azure Functions beépülő modul
    - EnvInject beépülő modul

1. Jenkins az Azure-beli szolgáltatásnév hitelesítése és Azure-erőforrásokat kell. Tekintse meg a [üzembe helyezés az Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md) részletes útmutatásait.

1. Használja az Azure-szolgáltatás egyszerű, adja hozzá a "Microsoft Azure szolgáltatás egyszerű" hitelesítő adatok típusa a Jenkinsben. Tekintse meg a [üzembe helyezés az Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins) oktatóanyag.

## <a name="fork-the-sample-github-repo"></a>A minta GitHub-tárház elágaztatása

1. [Jelentkezzen be a GitHub-adattárat a páratlan vagy akár mintát alkalmazáshoz](https://github.com/VSChina/odd-or-even-function.git).

1. Válassza a jobb felső sarokban a GitHub **elágazás**.

1. Kövesse az utasításokat, jelölje be a GitHub-fiókot, és ezzel Befejezés.

## <a name="create-a-jenkins-pipeline"></a>Hozzon létre egy Jenkins-folyamat

Ebben a szakaszban hoz létre a [Jenkins folyamat](https://jenkins.io/doc/book/pipeline/).

1. Folyamat létrehozása a Jenkins irányítópultján.

1. Engedélyezése **készítse elő a környezetet a Futtatás**.

1. Adja hozzá az alábbi környezeti változókat a **tulajdonságok tartalom**, és cserélje le a helyőrzőket a környezetének megfelelő értékekkel:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. Az a **folyamat-definíció >** szakaszban jelölje be **SCM parancsfájlt folyamat**.

1. Adja meg a GitHub-elágazásba URL-CÍMÉT és a parancsfájl elérési útja ("doc/erőforrás/jenkins/JenkinsFile") található a [JenkinsFile példa](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>Létrehozása és üzembe helyezése

Azt már a Jenkins-feladat futtatásának időpontját.

1. Először szerezze be a hitelesítési kulcs keresztül utasításait a [Azure Functions – HTTP-eseményindítók és kötések](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys) cikk.

1. A böngészőben adja meg az alkalmazás URL-CÍMÉT. A helyőrzőket cserélje le a megfelelő értékeket, és adjon meg egy numerikus értéket  **&lt;input_number >** a Java függvény bemeneteként.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Látni fogja az alábbi példa kimenetében hasonló eredményeket (Ha páratlan szám - 365 – tesztként lett megadva):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatához, törölje a következő lépésben létrehozott erőforrások:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>További lépések

Azure Functions használatával kapcsolatos további tudnivalókért tekintse meg a következő erőforráshoz:
> [!div class="nextstepaction"]
> [Az Azure Functions – dokumentáció](/azure/azure-functions/)
