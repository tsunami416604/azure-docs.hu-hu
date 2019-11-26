---
title: A Jenkins és az Azure áttekintése
description: Host the Jenkins build and deploy automation server in Azure and use Azure compute and storage resources to extend your continuous integration and deployment (CI/CD) pipelines.
keywords: jenkins, azure, devops, áttekintés
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 84d9a62c432208893ac5ff960a5f7aec743680d1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479346"
---
# <a name="azure-and-jenkins"></a>Az Azure és a Jenkins

A [Jenkins](https://jenkins.io/) egy népszerű, nyílt forráskódú automatizálási kiszolgáló, amely a szoftverprojektek folyamatos integrációs és teljesítési (CI-/CD-) folyamatainak beállítására használható. Üzembe helyezhet egy Jenkins üzemi környezetet az Azure-ban, vagy az Azure-erőforrások használatával kibővítheti meglévő Jenkins-konfigurációját. Az Azure-alkalmazások CI-/CD-folyamatainak egyszerűsítésére Jenkins beépülő modulok is használhatók.

Ez a cikk bemutatja az Azure Jenkinsszel való használatának alapjait, és részletesen ismerteti a Jenkins-felhasználók számára rendelkezésre álló főbb Azure-szolgáltatásokat. A saját Jenkins-kiszolgáló Azure-ban való használatának első lépéseit a [Jenkins-kiszolgáló Azure-ban való létrehozásával kapcsolatos](install-jenkins-solution-template.md) cikk ismerteti.

## <a name="host-your-jenkins-servers-in-azure"></a>Jenkins-kiszolgálók üzemeltetése az Azure-ban

A Jenkins Azure-ban való üzemeltetésével központosíthatja a buildek automatizálását, valamint a szoftverprojektek igényeinek megfelelően kibővítheti üzemi környezetét. A következő lehetőségei vannak a Jenkins Azure-ban való üzembe helyezésére:
 
- Az Azure Marketplace-en elérhető [Jenkins-megoldássablonnal](install-jenkins-solution-template.md)
- [Azure-beli virtuális gépekkel](/azure/virtual-machines/linux/overview) A Jenkins-példány virtuális gépen történő létrehozásának lépéseit lásd az [oktatóanyagban](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd).
- Az [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough) szolgáltatásban futó Kubernetes-fürt esetében lásd a [használati útmutatót](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Monitor and manage your Azure Jenkins deployment using [Azure Monitor logs](/azure/log-analytics/log-analytics-overview) and the [Azure CLI](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>A buildautomatizálás igény szerinti skálázása

Buildügynökök hozzáadásával a buildek számának, illetve a feladatok és folyamatok összetettségének növekedéséhez igazodva skálázhatja a Jenkins-build kapacitását a meglévő Jenkins üzemi környezetben. You can run these build agents on Azure virtual machines by using the [Azure VM Agents plug-in](https://plugins.jenkins.io/azure-vm-agents). További részleteket az [oktatóanyagban](/azure/jenkins/jenkins-azure-vm-agents) talál.

Az [Azure-szolgáltatásnévvel](/azure/azure-resource-manager/resource-group-overview) való konfigurálást követően a Jenkins-feladatok és -folyamatok a következőkre használhatják a hitelesítő adatokat:

- Securely store and archive build artifacts in [Azure Storage](/azure/storage/common/storage-introduction) using the [Azure Storage plug-in](https://plugins.jenkins.io/windows-azure-storage). A további részleteket a [Jenkins-tároló használati útmutatója](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) tartalmazza.
- Azure-erőforrások kezelése és konfigurálása az [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline) használatával.

## <a name="deploy-your-code-into-azure-services"></a>A kód üzembe helyezése Azure-szolgáltatásokban

A Jenkins beépülő moduljai használatával a Jenkins CI-/CD-folyamatok részeként helyezheti üzembe alkalmazásait az Azure-ban. Az [Azure App Service](/azure/app-service/) és [Azure Container Service](/azure/container-service/kubernetes/) szolgáltatásokban való üzembe helyezés révén az alapul szolgáló infrastruktúra kezelése nélkül készíthet elő, tesztelhet és adhat ki frissítéseket az alkalmazásaihoz.

 A beépülő modulokkal a következő szolgáltatások és környezetek helyezhetők üzembe:

- [Azure App Service on Linux](/azure/app-service/containers/app-service-linux-intro). Az első lépésekhez kapcsolódó információkat az [oktatóanyag](java-deploy-webapp-tutorial.md) tartalmazza.
- [Azure App Service](/azure/app-service/overview). Az első lépésekhez kapcsolódó információkat a [használati útmutató](deploy-Jenkins-app-service-plugin.md) tartalmazza.
