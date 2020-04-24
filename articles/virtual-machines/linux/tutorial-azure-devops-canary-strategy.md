---
title: Oktatóanyag – az Azure Linux Virtual Machines Kanári-telepítések konfigurálása
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezés (CD) folyamatát, amely az Azure Linux Virtual Machines egy csoportját frissíti a Kanári-telepítési stratégia használatával
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
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120477"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Oktatóanyag – a Kanári üzembe helyezési stratégia konfigurálása az Azure Linux Virtual Machines


## <a name="iaas---configure-cicd"></a>IaaS – CI/CD konfigurálása 
Az Azure-folyamatok teljes körűen Kiemelt CI/CD Automation-eszközöket biztosítanak a virtuális gépek üzembe helyezéséhez. Az Azure-beli virtuális gépek folyamatos kézbesítési folyamatát közvetlenül a Azure Portal lehet konfigurálni. Ez a dokumentum egy olyan CI/CD-folyamat beállításával kapcsolatos lépéseket tartalmazza, amely a több gépen üzemelő példányokra vonatkozó Kanári-stratégiát használja. Tekintse meg az olyan egyéb stratégiákat is, mint például a [Rolling](https://aka.ms/AA7jlh8) és a [Blue-Green](https://aka.ms/AA83fwu), amelyek a Azure Portal használatával támogatottak. 


**CI/CD konfigurálása Virtual Machines**

A virtuális gépeket célként lehet hozzáadni egy [központi telepítési csoporthoz](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) , és a több gépre kiterjedő frissítéseket is megcélozhat. Az üzembe helyezést követően a telepítési csoportban lévő **telepítési előzmények** nyomon követik a virtuális gép és a folyamat közötti nyomkövetést, majd a végrehajtást. 
 
  
**Kanári**-környezetek: a Kanári-környezetek csökkentik a kockázatot a felhasználók kis részhalmazára való váltás lassan történő bevezetésével. Az új verzió megbízhatóságának növelése érdekében megkezdheti az infrastruktúra további kiszolgálóinak felszabadítását, és további felhasználókat is átirányíthat. A virtuális gépekre a folyamatos kézbesítés lehetőség használatával konfigurálhatja a Kanári-telepítéseket a Azure Portal. Itt találja a lépésenkénti útmutatót. 
1. Jelentkezzen be a Azure Portalba, és navigáljon egy virtuális géphez 
2. A virtuális gép bal oldali ablaktábláján navigáljon a **folyamatos kézbesítés** menüre. Kattintson a **Configure** (Konfigurálás) elemre. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. A konfiguráció panelen kattintson az **Azure DevOps-szervezet** lehetőségre egy meglévő fiók kiválasztásához, vagy hozzon létre egyet. Ezután válassza ki azt a projektet, amelynek a folyamatát konfigurálni szeretné.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. A központi telepítési csoport a központi telepítési célként szolgáló gépek olyan logikai készlete, amely a fizikai környezeteket képviseli; ilyen például a "dev", a "test", a "ellenőrzését" és a "Production". Létrehozhat egy új központi telepítési csoportot, vagy kijelölhet egy meglévő központi telepítési csoportot is. 
5. Válassza ki a Build folyamatot, amely közzéteszi a virtuális gépre telepítendő csomagot. Vegye figyelembe, hogy a közzétett csomagnak telepítve kell lennie a _. ps1_ vagy a `deployscripts` _Deploy.sh_ telepítési parancsfájlnak a csomag gyökerében lévő mappában. Ezt az üzembe helyezési parancsfájlt az Azure DevOps-folyamat futtatja a futási időben.
6. Válassza ki az Ön által választott üzembe helyezési stratégiát. Válassza a **Kanári**lehetőséget.
7. Vegyen fel egy "Canary" címkét a Kanári-telepítések részét képező virtuális gépekre, valamint egy "Prod" címkét azokra a virtuális gépekre, amelyek az üzembe helyezések részét képezik a Kanári üzembe helyezésének sikeressége után. A címkék segítenek a kizárólag adott szerepkörrel rendelkező virtuális gépek célzásában.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. A folyamatos kézbesítési folyamat konfigurálásához kattintson az **OK gombra** a párbeszédpanelen. Ekkor egy folyamatos szállítási folyamattal fog rendelkezni, amely a virtuális gépre való üzembe helyezésre van konfigurálva.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. Kattintson a kiadási folyamat **szerkesztése** az Azure DevOps elemre a folyamat konfigurációjának megtekintéséhez. A folyamat három szakaszból áll. Az első fázis egy üzembe helyezési csoport fázisa és üzembe helyezése a _Kanári_-ként címkézett virtuális gépeken. A második fázisban szünetelteti a folyamatot, és megvárja a manuális beavatkozást a Futtatás folytatásához. Ha a felhasználó meggyőződött arról, hogy a Kanári üzembe helyezése stabil, akkor folytathatja a folyamat futását, amely azután futtatja a harmadik fázist, amely _a termékként_címkézett virtuális gépekre lesz telepítve. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. Mielőtt folytatná a folyamat futását, győződjön meg arról, hogy legalább egy virtuális gép a következőként van megjelölve: _Prod_. A folyamat harmadik fázisában az alkalmazás csak a _Prod_ címkével rendelkező virtuális gépekre lesz telepítve.

11. Az üzembe helyezési parancsfájl végrehajtása feladat alapértelmezés szerint a közzétett csomag gyökérkönyvtárában hajtja végre a _Deploy. ps1_ vagy a _Deploy.sh_ telepítési parancsfájlt a "deployscripts" mappában. Győződjön meg arról, hogy a kiválasztott build-folyamat közzéteszi ezt a csomag gyökérkönyvtárában. 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Egyéb központi telepítési stratégiák
- [A működés közbeni üzembe helyezési stratégia konfigurálása](https://aka.ms/AA7jlh8)
- [A kék-zöld telepítési stratégia konfigurálása](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Azure DevOps-projekt 
Az Azure-t minden eddiginél könnyebben kezdheti meg.
 
A DevOps Projects az alkalmazás futtatását bármely Azure-szolgáltatásban, mindössze három lépésben: válasszon egy alkalmazást, egy futtatókörnyezetet és egy Azure-szolgáltatást.
 
[További információ](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>További háttéranyagok 
- [Üzembe helyezés az Azure Virtual Machines DevOps Project használatával](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Az alkalmazás folyamatos üzembe helyezésének megvalósítása egy Azure virtuálisgép-méretezési csoportba](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
