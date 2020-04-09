---
title: Oktatóanyag – Integrált DevOps az IaaS-hez és a PaaS-hoz az Azure-ban
description: Ebben az oktatóanyagban megtudhatja, hogyan állíthatja be a folyamatos integrációt (CI) és a folyamatos üzembe helyezést (CD) egy alkalmazás Azure-beli virtuális gépekhez az Azure Pipelines használatával.
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
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885897"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Oktatóanyag: Integrált DevOps az IaaS és a PaaS számára az Azure-ban

Az Azure-beli teljes körű megoldásokkal a csapatok devops-eljárásokat valósíthatnak meg az egyes alkalmazáséletciklus-fázisokban: megtervezheti, fejlesztheti, leszállíthatja és működtetheti. 

Az alábbiakban néhány, a felhőalapú számítási feladatok egyszerűsítését célzó Azure-szolgáltatások, és kombinálható, hogy elképesztően hatékony forgatókönyvek.
Ezek a technológiák emberekkel és folyamatokkal kombinálva lehetővé teszik a csapatok számára, hogy folyamatosan értéket biztosítsanak az ügyfelek számára. 

- Azure: https://portal.azure.com – Portál felhőbeli számítási feladatok létrehozásához. Az egyszerű webalkalmazásoktól az összetett felhőalkalmazásokig mindent kezelhet és figyelhet 
- Azure DevOps: https://dev.azure.com – Tervezzen okosabban, működjön együtt jobban, és szállítson gyorsabban a modern fejlesztői szolgáltatások készletével 
- Azure Machine Learning-stúdió: https://ml.azure.com - Adatok előkészítése, betanítása és üzembe helyezése gépi tanulási modellek 
 

Az Azure DevOps egy beépített Azure-szolgáltatás, amely a DevOps-folyamat minden egyes részét folyamatos integrációval és folyamatos kézbesítéssel automatizálja bármely Azure-erőforrás számára.
Függetlenül attól, hogy az alkalmazás virtuális gépeket, webalkalmazásokat, Kubernetes-t vagy bármely más erőforrást használ, megvalósíthatja az infrastruktúrát kódként, a folyamatos integrációt, a folyamatos tesztelést, a folyamatos kézbesítést és a folyamatos figyelést az Azure-ral és az Azure DevOps-szal.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - CI/CD konfigurálása 
Az Azure Pipelines a CI/CD-automatizálási eszközök teljes körű, teljes funkcionalitású készletét biztosítja a virtuális gépekre történő telepítésekhez. Az Azure-beli virtuális gépek folyamatos kézbesítési folyamatát közvetlenül az Azure Portalról konfigurálhatja. Ez a dokumentum az Azure Portaltöbbgépes telepítések CI/CD-folyamatának beállításához kapcsolódó lépéseket tartalmazza. 


**Ci/CD konfigurálása virtuális gépeken**

Virtuális gépek példányként adható [krakti csoport,](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) és célzott többgépes frissítés. A követelmények alapján kiválaszthatja a beépített telepítési stratégiák, a _gördülő,_ _a kanári,_ a _kék-zöld,_ vagy tovább szabhatja őket. Üzembe helyezés után a telepítési előzmények nézetek a telepítési csoportokon belül biztosít nyomon követhetőséget a virtuális gépről a folyamatra, majd a véglegesítésre. 
 
**Működés közbeni üzembe helyezés:** A működés közbeni üzembe helyezés az alkalmazás előző verziójának példányait az alkalmazás új verziójának példányaira cseréli a gépek rögzített készletén (gördülő készlet) minden egyes ismétlésben. Vegyük végig, hogyan konfigurálhatja a virtuális gépek működés közbeni frissítését.  
Az Azure Portalon belül folyamatos kézbesítési lehetőség használatával konfigurálhatja a "**virtuális gépek**" működés közbeni frissítéseit. 

Itt van a lépésről-lépésre forgatókönyv. 
1. Jelentkezzen be az Azure Portalon, és keresse meg a virtuális gépet. 
2. A virtuális gép bal oldali ablaktáblájában keresse meg a **folyamatos kézbesítés** menüt. Ezután kattintson a **Configure gombra.** 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. A konfigurációs panelen kattintson az "Azure DevOps Organization" elemre egy meglévő fiók kiválasztásához vagy hozzon létre egyet. Ezután válassza ki azt a projektet, amely alatt konfigurálni szeretné a folyamatot.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. A központi telepítési csoport a fizikai környezeteket képviselő központi telepítési célgépek logikai készlete; például "Dev", "Test", "UAT" és "Production". Létrehozhat egy új központi telepítési csoportot, vagy kiválaszthat egy meglévő telepítési csoportot. 
5. Válassza ki azt a buildfolyamatot, amely közzéteszi a virtuális gépre telepítendő csomagot. Vegye figyelembe, hogy a közzétett csomagnak rendelkeznie kell egy központi telepítési _parancsfájldeploy.ps1_ vagy _deploy.sh_ _deployscripts_ mappában a csomag gyökér. Ezt a központi telepítési parancsfájlt az Azure DevOps-folyamat fogja végrehajtani futási időben.
6. Válassza ki a kívánt telepítési stratégiát. Ebben az esetben válasszuk ki a "Gördülő".
7. Szükség esetén a szerepkörrel is megjelölheti a gépet. Például "web", "db" stb. Ez segít a csak adott szerepkört tartalmazó virtuális gépek megcélzásának.
8. Kattintson az **OK** gombra a párbeszéden a folyamatos kézbesítési folyamat konfigurálásához. 
9. Miután elkészült, lesz egy folyamatos kézbesítési folyamat konfigurálva a virtuális gépre való üzembe helyezéshez.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Látni fogja, hogy a virtuális gép üzembe helyezése folyamatban van. A kapcsolatra kattintva navigálhat a folyamathoz. Kattintson **a Release-1** gombra a központi telepítés megtekintéséhez. Vagy a Szerkesztés **gombra** kattintva módosíthatja a kiadási folyamat definícióját. 
11. Ha több virtuális gépet kell konfigurálnia, ismételje meg a 2-4. Vegye figyelembe, hogy ha olyan központi telepítési csoportot választ, amelyhez már létezik egy folyamatfuttatás, a virtuális gép csak új folyamatok létrehozása nélkül kerül a központi telepítési csoportba. 
12. Miután elkészült, kattintson a folyamat definíciójára, keresse meg az Azure DevOps-szervezetet, és kattintson a Kiadási folyamat **szerkesztése** elemre. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Kattintson a linkre **1 feladat, 1 feladat** **fejlesztési** szakaszban. Kattintson a **Telepítés** fázisra.
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. A jobb oldali konfigurációs ablaktáblán megadhatja, hogy hány gépet szeretne párhuzamosan telepíteni az egyes ismétlésekben. Abban az esetben, ha egyszerre több gépre szeretne telepíteni, a csúszka használatával megadhatja a százalékos értéket.  

15. A Központi telepítési parancsfájl végrehajtása feladat alapértelmezés szerint végrehajtja a _deploy.ps1_ központi telepítési parancsfájlt vagy _deploy.sh_ a közzétett csomag gyökérkönyvtárában lévő _deployscripts_ mappában.
  
**Kanári-alapú központi telepítések:** A kanári-telepítés csökkenti a kockázatot azáltal, hogy lassan gördülő ki a változás egy kis részét a felhasználók. Ahogy egyre több bizalmat az új verzió, megkezdheti a kiadása, hogy több kiszolgálóaz infrastruktúra és útválasztás több felhasználó hozzá. A folyamatos kézbesítési lehetőség használatával konfigurálhatja a kanári-üzembe helyezéseket a "**virtuális gépekre"** az Azure Portalhasználatával. Itt van a lépésről-lépésre forgatókönyv. 
1. Jelentkezzen be az Azure Portalra, és navigáljon egy virtuális gépre 
2. Kövesse a 2-7. **Rolling Deployments** A telepítési stratégia legördülő menüjében válassza a "Kanári" lehetőséget.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. Adjon hozzá egy "kanári" címkét a virtuális gépekhez, amelyek a kanári-alapú központi telepítések részét képezik, és egy "prod" címkét a virtuális gépekhez, amelyek a kanári-telepítés sikeres üzembe helyezése után a központi telepítések részét képezik.
4. Miután elkészült, lesz egy folyamatos kézbesítési folyamat konfigurálva a virtuális gépre való üzembe helyezéshez.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. Mint a **működés közbeni üzembe helyezések** szakaszban, kattintson a kiadási folyamat **szerkesztése** az Azure DevOps a folyamatkonfiguráció megtekintéséhez. A folyamat 3 fázisból áll – az első fázis a DG fázis, és _kanáriként_címkézett virtuális gépekre telepíthető. A második fázisban szünetelteti a folyamatot, és megvárja a manuális beavatkozást a futtatás folytatásához. Miután a felhasználó meggyőződött arról, hogy a kanári-telepítés stabil, folytathatja a folyamat futtatását, amely ezután futtatja a harmadik fázist, amely telepíti a _termékként_címkézett virtuális gépekre. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**Kék-zöld központi telepítések:** A kék-zöld központi telepítés csökkenti az állásidőt azáltal, hogy azonos készenléti környezetben. Bármikor az egyik környezet él. Ahogy felkészülsz egy új kiadásra, a tesztelés utolsó fázisát a zöld környezetben kell elvégezned. Miután a szoftver működik a zöld környezetben, kapcsolja be a forgalmat úgy, hogy az összes bejövő kérelmek et a zöld környezet - a kék környezet most tétlen.
Kék-zöld központi telepítések konfigurálása a "**virtuális gépek**" az Azure Portalon a folyamatos kézbesítési lehetőség használatával. 

Itt van a lépésről-lépésre forgatókönyv.

1. Jelentkezzen be az Azure Portalra, és keresse meg a virtuális gépet 
2. Kövesse a 2-7. **Rolling Deployments** Adjon hozzá egy "kék" vagy "zöld" címkét a virtuális gépekhez, amelyek a kék-zöld telepítések részét képezik. Ha a virtuális gép készenléti szerepkörhöz szolgál, akkor "zöld" címkével kell elenie.
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. Miután elkészült, lesz egy folyamatos kézbesítési folyamat konfigurálva a virtuális gépre való üzembe helyezéshez.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. A **működés közbeni üzembe helyezésekhez**hasonlóan az Azure DevOps **kiadási folyamatának szerkesztése** elemre kattintva megtekintheti a folyamat konfigurációját. A folyamat 3 fázisból áll – az első fázis egy DG fázis, és _zöldként_ (készenléti virtuális gépekként) címkézett virtuális gépekre telepíti. A második fázis szünetelteti a folyamatot, és megvárja a manuális beavatkozást a futtatás folytatásához. Miután a felhasználó meggyőződött arról, hogy a központi telepítés stabil, most már átirányíthatja a forgalmat a _zöld_ virtuális gépek, és folytassa a folyamat futtatásához, amely majd a csere _kék_ és _zöld_ címkéket a virtuális gépeken. Ez biztosítja, hogy a virtuális gépek, amelyek régebbi alkalmazásverziója _zöld_ címkével vannak ellátva, és a következő folyamat futtatása.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. Vegye figyelembe, hogy ez a telepítési stratégia megköveteli, hogy legalább egy virtuális gép kék és zöld címkével ellátott. Győződjön meg arról, hogy a folyamat manuális beavatkozási lépésben történő folytatása előtt legalább egy _kékként_címkézett virtuális gép van címkézve.





 
## <a name="azure-devops-project"></a>Azure DevOps-projekt 
Az Azure-ral minden eddiginél egyszerűbben ismerkedhet meg.
 
A DevOps-projektek segítségével mindössze három lépésben kezdheti meg az alkalmazás futtatását bármely Azure-szolgáltatásban: válasszon ki egy alkalmazásnyelvet, egy futtatóidőt és egy Azure-szolgáltatást.
 
[További információ](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>További források 
- [Üzembe helyezés az Azure virtuális gépekre a DevOps-projekt használatával](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Az alkalmazás folyamatos üzembe helyezésének megvalósítása egy Azure virtuálisgép-méretezési készletbe](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
