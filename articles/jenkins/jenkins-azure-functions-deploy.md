---
title: Üzembe helyezés az Azure Functions szolgáltatásba a Jenkins Azure Functions beépülő modul használatával
description: Ismerje meg, hogyan telepíthető az Azure Functions szolgáltatásra a Jenkins Azure Functions beépülő modul használatával
keywords: jenkins, azure, devops, java, azure függvények
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 731bac13a596bbeaf970b3f6ce976a582d1f11ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78250908"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Üzembe helyezés az Azure Functions szolgáltatásba a Jenkins Azure Functions beépülő modul használatával

[Az Azure Functions](/azure/azure-functions/) egy kiszolgáló nélküli számítási szolgáltatás. Az Azure Functions használatával igény szerinti kódot futtathat infrastruktúra kiépítése vagy kezelése nélkül. Ez az oktatóanyag bemutatja, hogyan telepíthet egy Java-függvényt az Azure Functions beépülő modul használatával.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- **Jenkins-kiszolgáló:** Ha nincs telepítve Jenkins-kiszolgáló, olvassa el a [Jenkins-kiszolgáló létrehozása az Azure-ban](./install-jenkins-solution-template.md)című cikket.

  > [!TIP]
  > Az oktatóanyaghoz használt forráskód a [Visual Studio China GitHub tárházban](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java)található.

## <a name="create-a-java-function"></a>Java függvény létrehozása

Java-függvény létrehozásához a Java futásidejű verem, használja az [Azure Portalon](https://portal.azure.com) vagy az [Azure CLI.](/cli/azure/?view=azure-cli-latest)

A következő lépések bemutatják, hogyan hozhat létre java függvényt az Azure CLI használatával:

1. Hozzon létre egy ** &lt;** erőforráscsoportot, lecserélve a resource_group>helyőrzőt az erőforráscsoport nevére.

    ```azurecli
    az group create --name <resource_group> --location eastus
    ```

1. Hozzon létre egy Azure-tárfiókot, a helyőrzők helyett a megfelelő értékeket.
 
    ```azurecli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Hozza létre a tesztfüggvény-alkalmazást, és cserélje le a helyőrzőket a megfelelő értékekre.

    ```azurecli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Jenkins-kiszolgáló előkészítése

A következő lépések bemutatják, hogyan készíthető el a Jenkins-kiszolgáló:

1. [Jenkins-kiszolgáló](https://aka.ms/jenkins-on-azure) üzembe helyezése az Azure-ban. Ha még nincs telepítve a Jenkins-kiszolgáló egy példánya, a [Jenkins-kiszolgáló létrehozása az Azure-ban](./install-jenkins-solution-template.md) című cikk végigvezeti a folyamaton.

1. Jelentkezzen be a Jenkins-példányba az SSH-val.

1. A Jenkins-példányon telepítse a mavent a következő paranccsal:

    ```terminal
    sudo apt install -y maven
    ```

1. A Jenkins-példányon telepítse az [Azure Functions core eszközeit](/azure/azure-functions/functions-run-local) a következő parancsok kiadásával a terminálparancson:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. A Jenkins irányítópulton telepítse a következő bővítményeket:

    - Azure Functions beépülő modul
    - EnvInject beépülő modul

1. A Jenkinsnek egyszerű Azure-szolgáltatásra van szüksége az Azure-erőforrások hitelesítéséhez és eléréséhez. Tekintse meg a [központi telepítés az Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md) részletes útmutatást.

1. Az Azure egyszerű szolgáltatás használatával adjon hozzá egy "Microsoft Azure service Principal" hitelesítő adatok típusát a Jenkinsben. Tekintse meg a [központi telepítés az Azure App Service oktatóanyag.](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins)

## <a name="fork-the-sample-github-repo"></a>A minta GitHub-tárhételágás

1. [Jelentkezzen be a GitHub-tárházban a páratlan vagy páros mintaalkalmazáshoz.](https://github.com/VSChina/odd-or-even-function.git)

1. A GitHub jobb felső sarkában válassza az **Elágazás**lehetőséget.

1. Kövesse az utasításokat a GitHub-fiók kiválasztásához és a forking befejezéséhez.

## <a name="create-a-jenkins-pipeline"></a>Jenkins-folyamat létrehozása

Ebben a szakaszban hozza létre a [Jenkins-folyamatot.](https://jenkins.io/doc/book/pipeline/)

1. A Jenkins irányítópulton hozzon létre egy folyamatot.

1. Környezet **előkészítése a futtatáshoz**engedélyezése.

1. Adja hozzá a következő környezeti változókat a **Tulajdonságok tartalom között,** és cserélje le a helyőrzőket a környezetének megfelelő értékekre:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. A **Pipeline->Definition (Folyamat >definíciója)** csoportban válassza **az SCM Pipeline parancsfájlját.**

1. Adja meg a GitHub-elágazás URL-címét és parancsfájlelérési útját ("doc/resources/jenkins/JenkinsFile"), amelyet a [JenkinsFile példában](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile)kell használni.

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

## <a name="build-and-deploy"></a>Létrehozás és üzembe helyezés

Itt az ideje, hogy futtassuk a Jenkins-munkát.

1. Először szerezze be az engedélyezési kulcsot az [Azure Functions HTTP-eseményindítók és kötések](/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys) cikkben található utasításokat.

1. A böngészőben adja meg az alkalmazás URL-címét. Cserélje le a helyőrzőket a megfelelő ** &lt;értékekre,** és adja meg a>input_number numerikus értékét a Java függvény bemeneteként.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. A következő példakimenethez hasonló eredményekjelennek meg (ahol tesztként páratlan számot - 365 -t használtak):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje a következő lépéssel létrehozott erőforrásokat:

```azurecli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Functionsről, olvassa el az alábbi erőforrást:
> [!div class="nextstepaction"]
> [Az Azure Functions dokumentációja](/azure/azure-functions/)
