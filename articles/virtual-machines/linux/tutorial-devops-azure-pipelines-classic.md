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
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77912528"
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
Az Azure Pipelines a CI/CD-automatizálási eszközök teljes körű, teljes funkcionalitású készletét biztosítja a virtuális gépekre történő telepítésekhez. Az Azure-beli virtuális gépek folyamatos kézbesítési folyamatát közvetlenül az Azure Portalról konfigurálhatja. Ez a dokumentum az Azure Portaltöbbgépes telepítések CI/CD-folyamatának beállításához kapcsolódó lépéseket tartalmazza. Ci/CD konfigurálása virtuális gépeken.

Virtuális gépek lehet hozzáadni a központi [telepítési csoport,](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) és célzott többgépes működés közbeni frissítéseket. A központi telepítési csoportokon belüli üzembe helyezési előzmények et követő nézetek nyomon követhetőséget biztosítanak a virtuális gépről a folyamatra, majd a véglegesítésre. 
 
**Működés közbeni frissítések**: A működés közbeni üzembe helyezés az alkalmazás előző verziójának példányait az alkalmazás új verziójának példányaira cseréli a gépek (gördülő készlet) rögzített készletén minden egyes ismétlésben. Vegyük végig, hogyan konfigurálhatja a virtuális gépek működés közbeni frissítését.  
Az Azure Portalon belül folyamatos kézbesítési lehetőség használatával konfigurálhatja a "**virtuális gépek**" működés közbeni frissítéseit. 

Itt van a lépésről-lépésre forgatókönyv. 
1. Jelentkezzen be az Azure Portalon, és keresse meg a virtuális gépet. 
2. A virtuális gép bal oldali ablaktáblájában keresse meg a **folyamatos kézbesítés** menüt. Ezután kattintson a **Configure gombra.** 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. A konfigurációs panelen kattintson az "Azure DevOps Organization" elemre egy meglévő fiók kiválasztásához vagy hozzon létre egyet. Ezután válassza ki azt a projektet, amely alatt konfigurálni szeretné a folyamatot.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. A központi telepítési csoport a fizikai környezeteket képviselő központi telepítési célgépek logikai készlete; például "Dev", "Test", "UAT" és "Production". Létrehozhat egy új központi telepítési csoportot, vagy kiválaszthat egy meglévő telepítési csoportot. A gép a szerepkörrel is megcímkézhető. Például "web", "db" stb.  
5. Kattintson az **OK** gombra a párbeszéden a folyamatos kézbesítési folyamat konfigurálásához. 
6. Miután elkészült, lesz egy folyamatos kézbesítési folyamat konfigurálva a virtuális gépre való üzembe helyezéshez.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Látni fogja, hogy a virtuális gép üzembe helyezése folyamatban van. A kapcsolatra kattintva navigálhat a folyamathoz. Kattintson **a Release-1** gombra a központi telepítés megtekintéséhez. Vagy a Szerkesztés **gombra** kattintva módosíthatja a kiadási folyamat definícióját. 
8. Ha több virtuális gépet kell konfigurálnia, ismételje meg a 2-5. 
9. Miután elkészült, kattintson a folyamat definíciójára, keresse meg az Azure DevOps-szervezetet, és kattintson a Kiadási folyamat **szerkesztése** elemre. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Kattintson a linkre **1 feladat, 1 feladat** **fejlesztési** szakaszban. Kattintson a **Telepítés** fázisra.  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. A jobb oldali konfigurációs ablaktáblán láthatja, hogy alapértelmezés szerint a folyamat úgy van beállítva, hogy párhuzamosan végezze el az összes tároló folyamatos frissítését. Beállíthatja, hogy a központi telepítések történni akár egy olyan időpontban, vagy a százalékos érték a csúszka használatával.  
  
  
**Kanári** csökkenti a kockázatot, lassan gördülő ki a változás egy kis részét a felhasználók. Ahogy egyre több bizalmat az új verzió, megkezdheti a kiadása, hogy több kiszolgálóaz infrastruktúra és útválasztás több felhasználó hozzá. A folyamatos kézbesítési lehetőség használatával konfigurálhatja a kanári-üzembe helyezéseket a "**virtuális gépekre"** az Azure Portalhasználatával. Itt van a lépésről-lépésre forgatókönyv. 
1. Jelentkezzen be az Azure Portalra, és navigáljon egy virtuális gépre 
2. Kövesse az előző szakasz 2-5. 
3. Egyéni címke hozzáadása a kanári-telepítésekhez kapcsolódó virtuális gépekhez. Például a "kanári".
4. Miután a folyamat konfigurálva van a virtuális gépekhez, kattintson a folyamatra, indítsa el az Azure DevOps-szervezetet, **szerkeszti** a folyamatot, és keresse meg a **fejlesztési** szakaszt. Add címke a szűrő "kanári". 
5. Adjon hozzá egy másik központi telepítési csoport fázist, konfigurálja a fázist a címkékkel a központi telepítési csoportban fennmaradó virtuális gépek célzásához.  
6. Szükség esetén konfiguráljon egy manuális ellenőrzési lépést, amely előléptetheti/elutasíthatja a kanári-telepítéseket. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**A Blue-Green** csökkenti az üzembe helyezési állásidőt az azonos készenléti környezettel. Bármikor az egyik környezet él. Ahogy felkészülsz egy új kiadásra, a tesztelés utolsó fázisát a zöld környezetben kell elvégezned. Miután a szoftver működik a zöld környezetben, kapcsolja be a forgalmat úgy, hogy az összes bejövő kérelmek et a zöld környezet - a kék környezet most tétlen.
Kék-zöld központi telepítések konfigurálása a "**virtuális gépek**" az Azure Portalon a folyamatos kézbesítési lehetőség használatával. 

Itt van a lépésről-lépésre forgatókönyv. 

1. Jelentkezzen be az Azure Portalra, és keresse meg a virtuális gépet 
2. Kövesse a 2-5. **Rolling updates** Egyéni címke hozzáadása a kék-zöld központi telepítések részét tartalmazó virtuális gépekhez. Például a "kék" vagy "zöld" a virtuális gépek, amelyek a készenléti szerepkör. 
3. Miután a folyamat konfigurálva van a virtuális gépekhez, kattintson a folyamatra, indítsa el az Azure DevOps-szervezetet, a folyamat **szerkesztése,** keresse meg a **fejlesztési** szakaszt. Add címke a szűrő "zöld". 
4. Adjon hozzá egy ügynök nélküli fázist, konfigurálja a fázist manuális ellenőrzési lépéssel és egy invoke-REST api-lépéssel a címkék felcseréléséhez. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Azure DevOps-projekt 
Az Azure-ral minden eddiginél egyszerűbben ismerkedhet meg.
 
A DevOps-projektek segítségével mindössze három lépésben kezdheti meg az alkalmazás futtatását bármely Azure-szolgáltatásban: válasszon ki egy alkalmazásnyelvet, egy futtatóidőt és egy Azure-szolgáltatást.
 
[További információ](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>További források 
- [Üzembe helyezés az Azure virtuális gépekre a DevOps-projekt használatával](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Az alkalmazás folyamatos üzembe helyezésének megvalósítása egy Azure virtuálisgép-méretezési készletbe](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
