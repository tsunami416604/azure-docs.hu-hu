---
title: Oktatóanyag – integrált DevOps a IaaS és a Péter Azure-ban
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az alkalmazások folyamatos integrációját (CI) és folyamatos üzembe helyezését (CD) az Azure-beli virtuális gépekhez az Azure-folyamatok használatával.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: eba793a761bd9f96b1a4ec5d4730f08187a758ef
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515342"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Oktatóanyag: integrált DevOps a IaaS és a Pásti számára az Azure-ban

Az Azure-ban elérhető teljes körű megoldásokkal a csapatok az alkalmazások életciklusának minden fázisában DevOps eljárásokat hozhatnak létre: tervezés, fejlesztés, szállítás és működés. 

Az alábbiakban néhány olyan Azure-szolgáltatás található, amely egyszerűbbé teszi a Felhőbeli munkaterheléseket, és kombinálható a bámulatosan hatékony forgatókönyvek lehetővé tételéhez.
Ezek a technológiák, és az emberekkel és folyamatokkal együtt lehetővé teszik a csapatok számára, hogy folyamatosan értéket adjanak az ügyfeleknek. 

- Azure: https://portal.azure.com – portál a felhőalapú számítási feladatok létrehozásához. Az egyszerű webalkalmazások és az összetett felhőalapú alkalmazások kezelése és figyelése 
- Azure DevOps: https://dev.azure.com – tervezze meg az intelligensebb szolgáltatást, hatékonyabban működjön együtt, és hozzon létre gyorsabban egy modern fejlesztői szolgáltatásokkal 
- Azure Machine Learning Studio: https://ml.azure.com – az adatelőkészítés, a betanítás és a gépi tanulási modellek üzembe helyezése 
 

Az Azure DevOps egy beépített Azure-szolgáltatás, amely a DevOps-folyamat minden egyes részét automatizálja az Azure-erőforrások folyamatos integrálásával és folyamatos szállításával.
Akár virtuális gépeket, webalkalmazásokat, Kubernetes vagy más erőforrásokat használ, implementálhatja a kódot, a folyamatos integrációt, a folyamatos tesztelést, a folyamatos szállítást és a folyamatos figyelést az Azure és az Azure DevOps használatával.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS – CI/CD konfigurálása 
Az Azure-folyamatok teljes körűen Kiemelt CI/CD Automation-eszközöket biztosítanak a virtuális gépek üzembe helyezéséhez. Az Azure-beli virtuális gépek folyamatos kézbesítési folyamatát közvetlenül a Azure Portal lehet konfigurálni. Ez a dokumentum a Azure Portal-ból származó többszámítógépes telepítésekhez tartozó CI/CD-folyamatok beállításához szükséges lépéseket tartalmazza. A CI/CD konfigurálása Virtual Machineson.

A virtuális gépeket célként lehet hozzáadni egy [központi telepítési csoporthoz](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) , és a többszámítógépes működésre kiterjedő frissítésekre is használható. A központi telepítési csoportok üzembe helyezési előzményei a virtuális gép és a folyamat közötti nyomon követést biztosítanak, majd a véglegesíteni. 
 
**Működés közbeni frissítések**: a működés közbeni központi telepítés lecseréli az alkalmazás korábbi verziójának példányait az alkalmazás új verziójára az egyes iterációk rögzített készletén. Nézzük meg, hogyan konfigurálhatja a működés közbeni frissítést a virtuális gépekre.  
A folyamatos kézbesítési lehetőség használatával a "**virtuális gépekre**" is konfigurálhatja a működés közbeni frissítéseket a Azure Portalon belül. 

Itt találja a lépésenkénti útmutatót. 
1. Jelentkezzen be a Azure Portalba, és navigáljon a virtuális géphez. 
2. A virtuális gép bal oldali ablaktábláján navigáljon a **folyamatos kézbesítés** menüjére. Ezután kattintson a **configure (Konfigurálás**) elemre. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. A konfigurációs panelen kattintson az "Azure DevOps Organization" elemre egy meglévő fiók kiválasztásához, vagy hozzon létre egyet. Ezután válassza ki azt a projektet, amelynek a folyamatát konfigurálni szeretné.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. A központi telepítési csoport a központi telepítési célként szolgáló gépek olyan logikai készlete, amely a fizikai környezeteket képviseli; ilyen például a "dev", a "test", a "ellenőrzését" és a "Production". Létrehozhat egy új központi telepítési csoportot, vagy kijelölhet egy meglévő központi telepítési csoportot is. Igény szerint címkézheti a gépet a szerepkörrel. Például: "web", "db" stb.  
5. A folyamatos kézbesítési folyamat konfigurálásához kattintson az **OK gombra** a párbeszédpanelen. 
6. Ha elkészült, egy folyamatos kézbesítési folyamattal fog rendelkezni, amely a virtuális gépre való üzembe helyezésre van konfigurálva.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Látni fogja, hogy a virtuális gép üzembe helyezése folyamatban van. A hivatkozásra kattintva navigáljon a folyamathoz. Kattintson a **Release-1** elemre az üzemelő példány megtekintéséhez. Vagy a **Szerkesztés** lehetőségre kattintva módosíthatja a kiadási folyamat definícióját. 
8. Ha több virtuális géppel is konfigurálható, ismételje meg a 2-5. lépést a központi telepítési csoportba felvenni kívánt egyéb virtuális gépek esetében. 
9. Ha elkészült, kattintson a folyamat-definícióra, navigáljon az Azure DevOps-szervezethez, és kattintson a kiadási folyamat **szerkesztése** lehetőségre. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Kattintson a hivatkozás **1 feladat, 1 feladat** a **fejlesztői** fázisban elemre. Kattintson a **telepítés** fázisra.  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. A jobb oldali konfigurációs ablaktáblában láthatja, hogy a folyamat alapértelmezés szerint úgy van konfigurálva, hogy párhuzamosan végezze el a működés közbeni frissítést az összes cél számára. A központi telepítéseket beállíthatja úgy, hogy egy időben vagy százalékban történjen a csúszka használatával.  
  
  
A **Kanári** csökkenti a kockázatot, ha lassan kivezeti a változást a felhasználók kis részhalmazára. Az új verzió megbízhatóságának növelése érdekében megkezdheti az infrastruktúra további kiszolgálóinak felszabadítását, és további felhasználókat is átirányíthat. A "**virtuális gépek**" számára a folyamatos kézbesítés lehetőség használatával konfigurálhatja Azure Portal a Kanári-környezeteket. Itt találja a lépésenkénti útmutatót. 
1. Jelentkezzen be a Azure Portalba, és navigáljon egy virtuális géphez 
2. Az előző szakaszban 2-5-as lépések végrehajtásával több virtuális gépet adhat hozzá a központi telepítési csoporthoz. 
3. Adjon hozzá egy egyéni címkét a Kanári-telepítések részét képező virtuális gépekhez. Például: "Canary".
4. Ha a folyamat konfigurálva van a virtuális gépekhez, kattintson a folyamatra, indítsa el az Azure DevOps-szervezetet, **szerkessze** a folyamatot, és navigáljon a **fejlesztői** szakaszhoz. Címke hozzáadása a "Canary" szűrőhöz. 
5. Adjon hozzá egy másik üzembe helyezési csoport fázist, konfigurálja a fázist a címkékkel, hogy megcélozza a többi virtuális gépet a központi telepítési csoportban.  
6. Ha szeretné, konfiguráljon egy manuális ellenőrzési lépést, amely előléptetheti vagy elutasíthatja a Kanári-környezetek központi telepítését. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

A **Blue-Green** a központi telepítési állásidőt az azonos készenléti környezettel csökkenti. A környezetek egyike sem él. Az új kiadásra való felkészülés során az utolsó tesztelési fázist a zöld környezetben hajthatja végre. Ha a szoftver a zöld környezetben működik, állítsa át a forgalmat úgy, hogy minden bejövő kérelem a zöld környezetre lépjen – a kék környezet már tétlen.
A folyamatos kézbesítés lehetőséggel a Azure Portal a "**virtuális gépekre**" is beállíthatja a kék-zöld környezeteket. 

Itt találja a lépésenkénti útmutatót. 

1. Jelentkezzen be a Azure Portalba, és navigáljon egy virtuális géphez 
2. Több virtuális gép a központi telepítési csoportba való felvételéhez kövesse a **működés közbeni frissítések** szakasz 2-5. lépéseit. Adjon hozzá egyéni címkét a kék-zöld környezetek részét képező virtuális gépekhez. Például a "kék" vagy a "zöld" a készenléti szerepkörhöz tartozó virtuális gépek esetében. 
3. Ha a folyamat konfigurálva van a virtuális gépekhez, kattintson a folyamatra, indítsa el az Azure DevOps-szervezetet, **szerkessze** a folyamatot, és navigáljon a **fejlesztői** szakaszhoz. Címke hozzáadása a "zöld" szűrőhöz. 
4. Adjon hozzá egy ügynök nélküli fázist, állítsa be a fázist kézi ellenőrzési lépéssel, valamint egy meghívás – REST API-lépést a címkék cseréje érdekében. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Azure DevOps-projekt 
Az Azure-t minden eddiginél könnyebben kezdheti meg.
 
A DevOps Projects az alkalmazás futtatását bármely Azure-szolgáltatásban, mindössze három lépésben: válasszon egy alkalmazást, egy futtatókörnyezetet és egy Azure-szolgáltatást.
 
[További információk](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>További források 
- [Üzembe helyezés az Azure Virtual Machines DevOps Project használatával](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Az alkalmazás folyamatos üzembe helyezésének megvalósítása egy Azure virtuálisgép-méretezési csoportba](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
