---
title: CI/CD az Azure Spring Cloud-hoz
description: CI/CD az Azure Spring Cloud-hoz
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 96795315fcb0dd9b90cd55e8baa46c6a394882ce
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038833"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD az Azure Spring Cloud-hoz

A folyamatos integráció és a folyamatos kézbesítési eszközök lehetővé teszik a fejlesztők számára, hogy minimális erőfeszítéssel és kockázattal gyorsan telepíthessék a meglévő alkalmazások frissítéseit. Az Azure DevOps segítségével rendszerezheti és kezelheti ezeket a kulcsfontosságú feladatokat. Az Azure Spring Cloud jelenleg nem biztosít konkrét Azure DevOps beépülő modult.  A Spring Cloud-alkalmazásokat azonban integrálhatja a DevOps egy [Azure CLI-feladat](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)használatával. Ebből a cikkből megtudhatja, hogyan használhatja az Azure Spring Cloud Azure CLI-feladatait az Azure DevOps való integráláshoz.

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager szolgáltatási kapcsolatok létrehozása

[Ebből a cikkből](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) megtudhatja, hogyan hozhat létre Azure Resource Manager szolgáltatás-kapcsolódást az Azure DevOps-projekthez. Ügyeljen arra, hogy ugyanazt az előfizetést válassza, amelyet az Azure Spring Cloud Service-példányhoz használ.

## <a name="azure-cli-task-templates"></a>Azure CLI-feladatok sablonjai

### <a name="deploy-artifacts"></a>Összetevők üzembe helyezése

Projektjeit `tasks` sorozat használatával hozhatja létre és helyezheti üzembe. Ez a kódrészlet először definiál egy Maven-feladatot az alkalmazás létrehozásához, majd egy második feladatot, amely az Azure Spring Cloud Azure CLI-bővítmény használatával telepíti a JAR-fájlt.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Üzembe helyezés forrásból

A közvetlenül az Azure-ba történő üzembe helyezése külön fordítási lépés nélkül lehetséges.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
