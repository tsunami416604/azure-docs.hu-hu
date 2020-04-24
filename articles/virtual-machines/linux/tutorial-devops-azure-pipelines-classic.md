---
title: Oktatóanyag – Azure Linux Virtual Machines működés közbeni üzembe helyezésének konfigurálása
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezés (CD) folyamatát, amely fokozatosan frissíti az Azure Linux Virtual Machines egy csoportját a működés közbeni üzembe helyezési stratégia használatával
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113483"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Oktatóanyag – az Azure Linux Virtual Machines működés közbeni üzembe helyezési stratégiájának konfigurálása

Az Azure DevOps egy beépített Azure-szolgáltatás, amely a DevOps-folyamat minden egyes részét automatizálja az Azure-erőforrások folyamatos integrálásával és folyamatos szállításával.
Akár virtuális gépeket, webalkalmazásokat, Kubernetes vagy más erőforrásokat használ, az infrastruktúrát kódként, folyamatos integrációként, folyamatos teszteléssel, folyamatos kézbesítéssel és folyamatos figyeléssel implementálhatja az Azure és az Azure DevOps használatával.  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS – CI/CD konfigurálása 
Az Azure-folyamatok teljes körűen Kiemelt CI/CD Automation-eszközöket biztosítanak a virtuális gépek üzembe helyezéséhez. Az Azure-beli virtuális gépek folyamatos kézbesítési folyamatát közvetlenül a Azure Portal állíthatja be. Ez a dokumentum egy CI/CD-folyamat beállításához szükséges lépéseket tartalmazza a többszámítógépes üzemelő példányok Azure Portal-ból való működésének megadásához. Tekintse meg az olyan egyéb stratégiákat is, mint például a [Canary](https://aka.ms/AA7jdrz) és a [Blue-green](https://aka.ms/AA83fwu), amelyek Azure Portal-ből származó, beépítettek. 


**CI/CD konfigurálása Virtual Machines**

A virtuális gépeket célként lehet hozzáadni egy [központi telepítési csoporthoz](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) , és a több gépre kiterjedő frissítést is megcélozhat. A üzembe helyezést követően az üzembe helyezési **Előzmények** a virtuális gép és a folyamat közötti nyomon követést, majd a végrehajtást is biztosítják. 
 

**Működés közbeni központi telepítések**: a működés közbeni központi telepítés az alkalmazás korábbi verziójának példányait váltja fel az alkalmazás új verziójának az egyes iterációk rögzített készletén lévő példányaival. Nézzük meg, hogyan konfigurálhatja a működés közbeni frissítést a virtuális gépekre.  
A folyamatos kézbesítési lehetőség használatával a "**virtuális gépekre**" is konfigurálhatja a működés közbeni frissítéseket a Azure Portalon belül. 

Itt találja a lépésenkénti útmutatót. 
1. Jelentkezzen be a Azure Portalba, és navigáljon a virtuális géphez. 
2. A virtuális gép bal oldali ablaktábláján navigáljon a **folyamatos kézbesítés** menüre. Ezután kattintson a **configure (Konfigurálás**) elemre. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. A konfigurációs panelen kattintson az "Azure DevOps Organization" elemre egy meglévő fiók kiválasztásához, vagy hozzon létre egyet. Ezután válassza ki azt a projektet, amelynek a folyamatát konfigurálni szeretné.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. A központi telepítési csoport a központi telepítési célként szolgáló gépek olyan logikai készlete, amely a fizikai környezeteket képviseli; ilyen például a "dev", a "test", a "ellenőrzését" és a "Production". Létrehozhat egy új központi telepítési csoportot, vagy kijelölhet egy meglévő központi telepítési csoportot is. 
5. Válassza ki a Build folyamatot, amely közzéteszi a virtuális gépre telepítendő csomagot. Vegye figyelembe, hogy a közzétett csomagnak telepítve kell lennie a _. ps1_ vagy a `deployscripts` _Deploy.sh_ telepítési parancsfájlnak a mappában a csomag gyökerében. Ezt az üzembe helyezési parancsfájlt az Azure DevOps-folyamat futtatja a futási időben.
6. Válassza ki az Ön által választott üzembe helyezési stratégiát. Ebben az esetben a "gördülő" lehetőséget kell választania.
7. Lehetőség van arra is, hogy címkével lássa el a gépet a szerepkörrel. Például: "web", "db" stb. Ez segít a kizárólag adott szerepkörrel rendelkező virtuális gépek célzásában.
8. A folyamatos kézbesítési folyamat konfigurálásához kattintson az **OK gombra** a párbeszédpanelen. 
9. Ha elkészült, egy folyamatos kézbesítési folyamattal fog rendelkezni, amely a virtuális gépre való üzembe helyezésre van konfigurálva.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Látni fogja, hogy a virtuális gép üzembe helyezése folyamatban van. A hivatkozásra kattintva navigáljon a folyamathoz. Kattintson a **Release-1** elemre az üzemelő példány megtekintéséhez. Vagy a **Szerkesztés** lehetőségre kattintva módosíthatja a kiadási folyamat definícióját. 
11. Ha több virtuális géppel is konfigurálható, ismételje meg a 2-4-as lépéseket más virtuális gépekhez az üzembe helyezési csoportba való felvételhez. Vegye figyelembe, hogy ha olyan központi telepítési csoportot választ ki, amelyhez már létezik egy folyamat, a virtuális gép csak az új folyamatok létrehozása nélkül lesz hozzáadva a központi telepítési csoporthoz. 
12. Ha elkészült, kattintson a folyamat-definícióra, navigáljon az Azure DevOps-szervezethez, és kattintson a kiadási folyamat **szerkesztése** lehetőségre. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Kattintson a hivatkozás **1 feladat, 1 feladat** a **fejlesztői** fázisban elemre. Kattintson a **telepítés** fázisra.
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. A jobb oldali konfigurációs ablaktáblában megadhatja, hogy az egyes iterációk során párhuzamosan telepíteni kívánt gépek száma. Ha egyszerre több gépre szeretne üzembe helyezni, akkor a csúszkával megadhatja azt százalékban kifejezve.  

15. Az üzembe helyezési parancsfájl végrehajtása feladat alapértelmezés szerint a közzétett csomag gyökérkönyvtárában hajtja végre a _Deploy. ps1_ vagy a _Deploy.sh_ telepítési parancsfájlt a "deployscripts" mappában.  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Egyéb központi telepítési stratégiák

- [A Kanári üzembe helyezési stratégia konfigurálása](https://aka.ms/AA7jdrz)
- [A kék-zöld telepítési stratégia konfigurálása](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Azure DevOps-projekt 
Az Azure-t minden eddiginél könnyebben kezdheti meg.
 
A DevOps Projects az alkalmazás futtatását bármely Azure-szolgáltatásban, mindössze három lépésben: válasszon egy alkalmazást, egy futtatókörnyezetet és egy Azure-szolgáltatást.
 
[További információ](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>További háttéranyagok 
- [Üzembe helyezés az Azure Virtual Machines DevOps Project használatával](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Az alkalmazás folyamatos üzembe helyezésének megvalósítása egy Azure virtuálisgép-méretezési csoportba](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
