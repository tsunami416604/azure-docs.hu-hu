---
title: "Jenkins és Azure áttekintése |} Microsoft Docs"
description: "Gazdagépen a Jenkins összeállítása és az Azure automation-kiszolgáló központi telepítése, majd az Azure számítási és tárolási erőforrásokat kiterjeszteni a folyamatos integrációt és telepítést (CI/CD) folyamatok."
services: jenkins
author: rloutlaw
manager: justhe
ms.service: jenkins
ms.devlang: NA
ms.topic: article
ms.workload: na
ms.date: 08/22/2017
ms.author: routlaw
ms.custom: mvc
ms.openlocfilehash: 08906939ecc80999de94ad7e7141081f959fb6f2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="azure-and-jenkins"></a>Azure-ra és Jenkins

[Jenkins](https://jenkins.io/) folyamatos integrációt és kézbesítési (CI/CD) a szoftver projektek beállításához használt népszerű nyílt forráskódú automation kiszolgáló. A Jenkins központi telepítés az Azure-ban futtatni, vagy bővíteni a meglévő Azure-erőforrások használata Jenkins-konfigurációt. Egyszerűbbé teheti az alkalmazások az Azure-bA CI/CD Jenkins beépülő modulok is érhetők el.

A cikkben megismerkedhet az Azure használatával Jenkins, és részletesen leírja a core Azure elérhető szolgáltatások Jenkins felhasználók számára. Ismerkedés az Azure-ban a saját Jenkins kiszolgálóval, tekintse meg a [gyors üzembe helyezés](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>A Jenkins állomáskiszolgáló az Azure-ban

Állomás Jenkins központosíthatja a build automatizálás és a szoftver projektek igények növekedésével a telepítés méretezésére az Azure-ban. Az Azure használatával Jenkins telepítheti:
 
- [A Jenkins megoldássablonban](install-jenkins-solution-template.md) Azure piactéren.
- [Az Azure virtuális gépek](/azure/virtual-machines/linux/overview). Tekintse meg a [oktatóanyag](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd) Jenkins példány létrehozása a virtuális gép.
- Egy Kubernetes fürtön futó [Azure Tárolószolgáltatás](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), tekintse meg a [útmutató](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Figyelheti és kezelheti a használó Azure Jenkins telepítés [Naplóelemzési](/azure/log-analytics/log-analytics-overview), [Operations Management Suite](/azure/operations-management-suite/operations-management-suite-overview), és a [Azure CLI](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>A build automation az igény szerinti méretezése

Build ügynökök hozzáadása a meglévő Jenkins telepítési méretezése a buildek és a feladatok és adatcsatornák növekedését összetettsége a Jenkins build kapacitás. Ezek futtatása ügynökök létrehozása az Azure virtuális gépeken használatával a [Azure VM ügynökök beépülő modul](jenkins-azure-vm-agents.md). Tekintse meg a [oktatóanyag](/azure/jenkins/jenkins-azure-vm-agents) további részleteket.

Egyszer konfigurált egy [Azure szolgáltatás egyszerű](/azure/azure-resource-manager/resource-group-overview), Jenkins feladatok és folyamatok használhatja ezt a hitelesítő adatot:

- Biztonságos helyen tárolja, és archív összetevők létrehozása [Azure Storage](/azure/storage/common/storage-introduction) használatával a [Azure Storage beépülő modul](https://plugins.jenkins.io/windows-azure-storage). Tekintse át a [Jenkins tárolási útmutató](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) további.
- Kezelése és konfigurálása az Azure-erőforrások a [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>A kód telepítése az Azure-szolgáltatások

Az alkalmazások az Azure-bA telepíthető a Jenkins CI/CD folyamatok részeként Jenkins beépülő modulok segítségével. Olyan üzembe [Azure App Service](/azure/app-service/) és [Azure Tárolószolgáltatás](/azure/container-service/kubernetes/) lehetővé teszi, hogy szakaszt, tesztelési, és az alkalmazások kiadás frissítéseket az alkalmazás mögötti infrastruktúra kezelése nélkül.

 Bővítmények érhetők el a következő szolgáltatások és környezetben történő központi telepítéséhez:

- [Azure-webalkalmazásban Linux](/azure/app-service/containers/app-service-linux-intro). Tekintse meg a [oktatóanyag](java-deploy-webapp-tutorial.md) a kezdéshez.
- [Azure-webalkalmazásban](/azure/app-service/app-service-web-overview). Tekintse meg a [útmutató](deploy-Jenkins-app-service-plugin.md) a kezdéshez.

