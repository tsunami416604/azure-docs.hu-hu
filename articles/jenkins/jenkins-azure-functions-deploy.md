---
title: Üzembe helyezés Azure Functions a Jenkins Azure Functions beépülő modullal
description: Megtudhatja, hogyan helyezhet üzembe Azure Functions a Jenkins Azure Functions beépülő modullal
keywords: Jenkins, Azure, devops, Java, Azure functions
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 731bac13a596bbeaf970b3f6ce976a582d1f11ae
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250908"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Üzembe helyezés Azure Functions a Jenkins Azure Functions beépülő modullal

[Azure functions](/azure/azure-functions/) kiszolgáló nélküli számítási szolgáltatás. A Azure Functions használatával igény szerint futtathat programkódot az infrastruktúra kiépítése vagy kezelése nélkül. Ebből az oktatóanyagból megtudhatja, hogyan telepíthet Java-függvényeket Azure Functions a Azure Functions beépülő modullal.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- **Jenkins-kiszolgáló**: Ha nincs telepítve Jenkins-kiszolgáló, tekintse meg a Jenkins- [kiszolgáló létrehozása az Azure](./install-jenkins-solution-template.md)-ban című cikket.

  > [!TIP]
  > Az oktatóanyaghoz használt forráskód a [Visual Studio China GitHub](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java)-tárházban található.

## <a name="create-a-java-function"></a>Java-függvény létrehozása

A Java-futtatókörnyezettel rendelkező Java-függvények létrehozásához használja a [Azure Portal](https://portal.azure.com) vagy az [Azure CLI](/cli/azure/?view=azure-cli-latest)-t.

A következő lépések bemutatják, hogyan hozhat létre Java-függvényeket az Azure CLI használatával:

1. Hozzon létre egy erőforráscsoportot, és cserélje le a **&lt;resource_group >** helyőrzőt az erőforráscsoport nevére.

    ```azurecli
    az group create --name <resource_group> --location eastus
    ```

1. Hozzon létre egy Azure Storage-fiókot, és cserélje le a helyőrzőket a megfelelő értékekre.
 
    ```azurecli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Hozza létre a test Function alkalmazást, és cserélje le a helyőrzőket a megfelelő értékekre.

    ```azurecli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Jenkins-kiszolgáló előkészítése

A Jenkins-kiszolgáló előkészítését az alábbi lépések ismertetik:

1. Jenkins- [kiszolgáló](https://aka.ms/jenkins-on-azure) üzembe helyezése az Azure-ban. Ha még nincs telepítve a Jenkins-kiszolgáló egy példánya, a cikk, [Jenkins-kiszolgáló létrehozása az Azure](./install-jenkins-solution-template.md) -ban végigvezeti Önt a folyamaton.

1. Jelentkezzen be a Jenkins-példányba SSH-val.

1. A Jenkins-példányon telepítse a mavent a következő parancs használatával:

    ```terminal
    sudo apt install -y maven
    ```

1. A Jenkins-példányon telepítse a [Azure functions Core toolst](/azure/azure-functions/functions-run-local) a következő parancsok parancssorból történő kiadásával:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. A Jenkins-irányítópulton telepítse a következő beépülő modulokat:

    - Beépülő modul Azure Functions
    - EnvInject beépülő modul

1. A Jenkins-nek szüksége van egy Azure-szolgáltatásra az Azure-erőforrások hitelesítéséhez és eléréséhez. Részletes utasításokért tekintse meg a [központi telepítés Azure app Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md) .

1. Az Azure egyszerű szolgáltatásnév használatával adjon hozzá egy "Microsoft Azure egyszerű szolgáltatásnév" hitelesítő adatot a Jenkins-ben. Tekintse át a [központi telepítés Azure app Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins) oktatóanyagot.

## <a name="fork-the-sample-github-repo"></a>A minta GitHub-tárházának elágazása

1. [Jelentkezzen be a GitHub-tárházba a páratlan vagy akár a minta alkalmazáshoz](https://github.com/VSChina/odd-or-even-function.git).

1. A GitHub jobb felső sarkában válassza az **elágazás**lehetőséget.

1. Az utasításokat követve válassza ki a GitHub-fiókját, és fejezze be az elágazást.

## <a name="create-a-jenkins-pipeline"></a>Jenkins-folyamat létrehozása

Ebben a szakaszban létrehozza a [Jenkins](https://jenkins.io/doc/book/pipeline/)-folyamatot.

1. Hozzon létre egy folyamatot a Jenkins-irányítópulton.

1. **A környezet előkészítésének engedélyezése a futtatáshoz**.

1. Adja hozzá az alábbi környezeti változókat a **Tulajdonságok tartalmához**, és cserélje le a helyőrzőket a környezete megfelelő értékeire:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. A **folyamat – > definíció** szakaszban válassza ki **a folyamat parancsfájlt az SCM-ből**.

1. Adja meg a GitHub-elágazás URL-címét és a parancsfájl elérési útját ("doc/Resources/Jenkins/JenkinsFile") a [JenkinsFile példában](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile)való használatra.

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

Most már ideje futtatni a Jenkins-feladatot.

1. Először szerezze be az engedélyezési kulcsot a [Azure FUNCTIONS http-eseményindítók és-kötések](/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys) című cikkben található utasítások alapján.

1. A böngészőben adja meg az alkalmazás URL-címét. Cserélje le a helyőrzőket a megfelelő értékekre, és adjon meg egy numerikus értéket a **&lt;input_number >** a Java-függvény bemenete.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. A következő példa kimenetéhez hasonló eredmények jelennek meg (ahol a páros szám-365 – tesztként használták):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a létrehozott erőforrásokat a következő lépéssel:

```azurecli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Functionsről, tekintse meg a következő erőforrást:
> [!div class="nextstepaction"]
> [Azure Functions dokumentáció](/azure/azure-functions/)
