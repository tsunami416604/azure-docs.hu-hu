---
title: CI/CD az Azure Spring Cloud szolgáltatáshoz
description: CI/CD az Azure Spring Cloud szolgáltatáshoz
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278515"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD az Azure Spring Cloud szolgáltatáshoz

A folyamatos integráció és a folyamatos kézbesítési eszközök lehetővé teszik a fejlesztők számára, hogy a meglévő alkalmazások frissítéseit gyorsan, minimális erőfeszítéssel és kockázattal telepítsék. Az Azure DevOps segítségével rendszerezheti és vezérelheti ezeket a kulcsfontosságú feladatokat. Jelenleg az Azure Spring Cloud nem kínál egy adott Azure DevOps-bővítményt.  A Spring Cloud-alkalmazásokat azonban integrálhatja a DevOps-szal egy [Azure CLI-feladat](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)használatával. Ez a cikk bemutatja, hogyan használhatja az Azure CLI-feladatot az Azure Spring Cloud szolgáltatással az Azure DevOps-szal való integrációhoz.

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager szolgáltatáskapcsolat létrehozása

[Ebből a cikkből](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) megtudhatja, hogyan hozhat létre Azure Resource Manager-szolgáltatáskapcsolatot az Azure DevOps-projekttel. Ügyeljen arra, hogy ugyanazt az előfizetést válassza ki, amelyet az Azure Spring Cloud szolgáltatáspéldányához használ.

## <a name="azure-cli-task-templates"></a>Azure CLI feladatsablonok

### <a name="deploy-artifacts"></a>Összetevők telepítése

A projekteket a használatával hozhatlétre `tasks`és telepítheti. Ez a kódrészlet először egy Maven-feladatot határoz meg az alkalmazás létrehozásához, majd egy második feladatot, amely telepíti a JAR-fájlt az Azure Spring Cloud Azure CLI bővítmény használatával.

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

### <a name="deploy-from-source"></a>Telepítés forrásból

Lehetőség van közvetlenül az Azure-ba külön build lépés nélkül telepíthető.

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
