---
title: CI/CD az Azure Spring Cloud-hoz
description: CI/CD az Azure Spring Cloud-hoz
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278515"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD az Azure Spring Cloud-hoz

A folyamatos integráció és a folyamatos kézbesítési eszközök lehetővé teszik a fejlesztők számára, hogy minimális erőfeszítéssel és kockázattal gyorsan telepíthessék a meglévő alkalmazások frissítéseit. Az Azure DevOps segítségével rendszerezheti és kezelheti ezeket a kulcsfontosságú feladatokat. Az Azure Spring Cloud jelenleg nem biztosít konkrét Azure DevOps beépülő modult.  A Spring Cloud-alkalmazásokat azonban integrálhatja a DevOps egy [Azure CLI-feladat](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)használatával. Ebből a cikkből megtudhatja, hogyan használhatja az Azure Spring Cloud Azure CLI-feladatait az Azure DevOps való integráláshoz.

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager szolgáltatási kapcsolatok létrehozása

[Ebből a cikkből](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) megtudhatja, hogyan hozhat létre Azure Resource Manager szolgáltatás-kapcsolódást az Azure DevOps-projekthez. Ügyeljen arra, hogy ugyanazt az előfizetést válassza, amelyet az Azure Spring Cloud Service-példányhoz használ.

## <a name="azure-cli-task-templates"></a>Azure CLI-feladatok sablonjai

### <a name="deploy-artifacts"></a>Összetevők üzembe helyezése

Projektjeit `tasks`egy sorozatán keresztül hozhatja létre és helyezheti üzembe. Ez a kódrészlet először definiál egy Maven-feladatot az alkalmazás létrehozásához, majd egy második feladatot, amely az Azure Spring Cloud Azure CLI-bővítmény használatával telepíti a JAR-fájlt.

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
      az extension add -y --name spring-cloud
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
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
