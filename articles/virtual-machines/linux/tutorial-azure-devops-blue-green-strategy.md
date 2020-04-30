---
title: Oktatóanyag – az Azure Linux Virtual Machines Kanári-telepítések konfigurálása
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezés (CD) folyamatát, amely az Azure-beli virtuális gépek egy csoportját frissíti a kék-zöld telepítési stratégia használatával.
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120491"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Oktatóanyag – kék-zöld üzembe helyezési stratégia konfigurálása az Azure Linux Virtual Machines


## <a name="iaas---configure-cicd"></a>IaaS – CI/CD konfigurálása 
Az Azure-folyamatok teljes körűen Kiemelt CI/CD Automation-eszközöket biztosítanak a virtuális gépek üzembe helyezéséhez. Az Azure-beli virtuális gépek folyamatos kézbesítési folyamatát közvetlenül a Azure Portal állíthatja be. Ez a dokumentum egy olyan CI/CD-folyamat beállításához szükséges lépéseket tartalmazza, amely a több gépen üzemelő példányok esetében a kék-zöld stratégiát használja. Megtekintheti az olyan egyéb stratégiákat is, mint például a [Rolling](https://aka.ms/AA7jlh8) és a [Canary](https://aka.ms/AA7jdrz), amelyek a Azure Portalon kívülről támogatottak. 

 
 **CI/CD konfigurálása Virtual Machines**

A virtuális gépeket célként lehet hozzáadni egy [központi telepítési csoporthoz](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) , és a több gépre kiterjedő frissítéseket is megcélozhat. A telepítés után az üzembe helyezési **Előzmények** megtekintése egy központi telepítési csoportban a virtuális gép és a folyamat közötti nyomon követést, majd a véglegesítés elvégzését teszi lehetővé. 
 
  
**Kék-zöld**környezetek: A kék-zöld üzembe helyezés csökkenti az állásidőt egy azonos készenléti környezetben. A környezetek egyike sem él. Egy új kiadásra való felkészülés során befejezte a tesztelés végső fázisát a zöld környezetben. Ha a szoftver a zöld környezetben működik, állítsa át a forgalmat úgy, hogy minden bejövő kérelem a zöld környezetre lépjen – a kék környezet már tétlen.
A folyamatos kézbesítés lehetőséggel a Azure Portal a "**virtuális gépekre**" is beállíthatja a kék-zöld környezeteket. 

Itt találja a lépésenkénti útmutatót.

1. Jelentkezzen be a Azure Portalba, és navigáljon egy virtuális géphez 
2. A virtuális gép bal oldali ablaktábláján folytassa a **folyamatos kézbesítést**. Ezután kattintson a **Konfigurálás**elemre. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. A konfiguráció panelen kattintson az **Azure DevOps-szervezet** lehetőségre egy meglévő fiók kiválasztásához, vagy hozzon létre egyet. Ezután válassza ki azt a projektet, amelynek a folyamatát konfigurálni szeretné.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. A központi telepítési csoport a központi telepítési célként szolgáló gépek olyan logikai készlete, amely a fizikai környezeteket képviseli; ilyen például a "dev", a "test", a "ellenőrzését" és a "Production". Létrehozhat egy új központi telepítési csoportot, vagy kijelölhet egy meglévő központi telepítési csoportot is. 
5. Válassza ki a Build folyamatot, amely közzéteszi a virtuális gépre telepítendő csomagot. Vegye figyelembe, hogy a közzétett csomagnak rendelkeznie kell központi telepítési parancsfájllal _. ps1_ vagy `deployscripts` _Deploy.sh_ a mappában a csomag gyökerében. Ezt az üzembe helyezési parancsfájlt az Azure DevOps-folyamat futtatja a futási időben.
6. Válassza ki az Ön által választott üzembe helyezési stratégiát. Válassza a **kék-zöld**lehetőséget.
7. Vegyen fel egy "kék" vagy "zöld" címkét a kék-zöld környezetek részét képező virtuális gépekhez. Ha a virtuális gép készenléti szerepkörhöz van kiválasztva, akkor a címkét "zöldként" kell megjelölni, ellenkező esetben pedig "Blue"-ként kell megjelölni.
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. A folyamatos kézbesítési folyamat konfigurálásához kattintson **az OK** gombra. Ekkor egy folyamatos szállítási folyamattal fog rendelkezni, amely a virtuális gépre való üzembe helyezésre van konfigurálva.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Kattintson a kiadási folyamat **szerkesztése** az Azure DevOps elemre a folyamat konfigurációjának megtekintéséhez. A folyamat három szakaszból áll. Az első fázis egy üzembe helyezési csoport fázisa és üzembe helyezése a _zöld_ (készenléti virtuális gépek) címkével rendelkező virtuális gépeken. A második fázis szünetelteti a folyamatot, és megvárja a manuális beavatkozást a Futtatás folytatásához. Miután a felhasználó meggyőződött arról, hogy az üzembe helyezés stabil, most már átirányíthatja a forgalmat a _zöld_ virtuális gépekre, és folytathatja a folyamat futását, amely a _kék_ és _zöld_ címkéket a virtuális gépeken cseréli le. Ez biztosítja, hogy a régebbi verziójú alkalmazást tartalmazó virtuális gépek _zöld_ színnel legyenek megjelölve, és a következő folyamat futtatásakor legyenek telepítve.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. Az üzembe helyezési parancsfájl végrehajtása feladat alapértelmezés szerint végrehajtja a központi telepítési parancsfájl _telepítését. ps1_ vagy `deployscripts` _Deploy.sh_ a közzétett csomag gyökérkönyvtárában lévő mappában. Győződjön meg arról, hogy a kiválasztott build-folyamat közzéteszi ezt a csomag gyökérkönyvtárában.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Egyéb központi telepítési stratégiák
- [A működés közbeni üzembe helyezési stratégia konfigurálása](https://aka.ms/AA7jlh8)
- [A Kanári üzembe helyezési stratégia konfigurálása](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Azure DevOps-projekt 
Az Azure-t minden eddiginél könnyebben kezdheti meg.
 
A DevOps Projects az alkalmazás futtatását bármely Azure-szolgáltatásban, mindössze három lépésben: válasszon egy alkalmazást, egy futtatókörnyezetet és egy Azure-szolgáltatást.
 
[További információ](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>További háttéranyagok 
- [Üzembe helyezés az Azure Virtual Machines DevOps Project használatával](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Az alkalmazás folyamatos üzembe helyezésének megvalósítása egy Azure virtuálisgép-méretezési csoportba](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
