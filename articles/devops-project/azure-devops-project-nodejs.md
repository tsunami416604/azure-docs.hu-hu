---
title: CI/CD-folyamat létrehozása PWA GatsbyJS és Azure DevOps Projects
description: DevOps Projects megkönnyíti az Azure megkezdését. A segítségével néhány gyors lépésben elindíthat egy alkalmazást a választott Azure-szolgáltatásban.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: angrobe
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 508a61d6bbb00692855e09601aed67ab3be9cc8d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209067"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Rövid útmutató: CI/CD-folyamat létrehozása az Azure-folyamatokban Node. js-hez Azure DevOps Projects
Ebben a rövid útmutatóban létrehoz egy NodeJS Progressive-webalkalmazást (PWA) a [GatsbyJS](https://www.gatsbyjs.org/) és az egyszerűsített Azure DevOps-projekt létrehozási felületének használatával. Ha elkészült, folyamatos integrációs (CI) és folyamatos továbbítási (CD) folyamattal rendelkezik az Azure-folyamatok PWA. Azure DevOps Projects beállítja a fejlesztéshez, üzembe helyezéshez és figyeléshez szükséges tudnivalókat.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Egy [Azure DevOps](https://azure.microsoft.com/services/devops/) -szervezet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és a bal oldali ablaktáblán válassza az **erőforrás létrehozása**lehetőséget. 

   ![Azure-erőforrás létrehozása Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

2. Keresse meg és válassza ki **DevOps projects**, majd válassza a **Létrehozás**lehetőséget.

 ![DevOps-projekt létrehozása](_img/azure-devops-project-nodejs/create-devops-project.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza ki a Node. js-minta alkalmazást.   

 ![Válassza ki a Node. js-mintát](_img/azure-devops-project-nodejs/select-nodejs-devops-project.png) 

2. Az alapértelmezett mintakeretrendszer az **Express.js**. Módosítsa a kijelölést **egyszerű Node. js-alkalmazásra** , majd kattintson a **tovább**gombra. 

 ![Válassza ki az egyszerű Node. js-alkalmazást](_img/azure-devops-project-nodejs/simple-nodejs-project.png) 

3. Az ebben a lépésben elérhető központi telepítési célokat a 2. lépésben kiválasztott alkalmazás-keretrendszer határozza meg.  Ebben a példában a **Windows Web App** az alapértelmezett telepítési cél.  Hagyja **Web App for containers** a beállítást, és válassza a **tovább**lehetőséget.

 ![Telepítési cél kiválasztása](_img/azure-devops-project-nodejs/select-web-server.png) 

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Adja meg a projekt nevét és az Azure-előfizetést

1. A DevOps projekt-létrehozási munkafolyamat utolsó lépéseként rendeljen hozzá egy projekt nevét, válasszon ki egy Azure-előfizetést, majd válassza a **kész**lehetőséget.  

 ![Projekt nevének kijelölése és előfizetés kiválasztása](_img/azure-devops-project-nodejs/assign-project-name.png) 

2. Megjelenik egy összefoglaló oldal a projekt létrehozásakor, és az alkalmazás üzembe helyezése az Azure-ban történik. Egy rövid időszak után létrejön egy projekt az [Azure DevOps-szervezetben](https://dev.azure.com/) , amely tartalmaz egy git-tárházat, egy Kanban-táblát, egy üzembe helyezési folyamatot, egy tesztelési tervet, valamint az alkalmazás által igényelt összetevőket.  

## <a name="managing-your-project"></a>A projekt kezelése

1. Keresse meg az **összes erőforrást** , és keresse meg a DevOps-projektet. Válassza ki a **DevOps-projektet**.

![Azure DevOps-irányítópult az erőforrás-listában](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

2. Olyan irányítópultra van irányítva, amely megtekinti a projekt kezdőlapját, a kódlap, a CI/CD folyamatot, valamint a futó alkalmazásra mutató hivatkozást. Válassza ki a **projekt kezdőlapját** az alkalmazás **Azure DevOps** való megtekintéséhez, majd egy másik böngésző lapon válassza ki az **alkalmazás-végpontot** az élő minta alkalmazás megtekintéséhez.  Ezt a mintát később a GatsbyJS által generált PWA használatára módosítjuk.

![Azure DevOps-irányítópult](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

3. Az Azure DevOps-projektből meghívhatja a csapattagokat, hogy működjenek együtt egy Kanban-testülettel a munka nyomon követésének megkezdéséhez.  További információ: [itt](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Az Azure DevOps áttekintése](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>A tárház klónozása és a Gatsby-PWA telepítése

A DevOps Projects egy git-tárházat hoz létre az Azure Reposben vagy a GitHubban. Ez a példa egy Azure-tárházat hozott létre.  A következő lépés a tárház klónozása és a módosítások elvégzése.

1. Válassza ki a **DevOps-projektben** a **repókat** , majd kattintson a **klónozás**elemre.  A git-tárház klónozása az asztalra különböző mechanizmusokkal történik.  Válassza ki a fejlesztési élményhez illőt.  

![Az adattár klónozása](_img/azure-devops-project-nodejs/clone-the-repo.png)

2. Miután a tárház klónozása megtörtént az asztalról, hajtson végre néhány módosítást az alapszintű sablonban. Először telepítse a GatsbyJS CLI-t a terminálról.
```powershell
npm install -g gatsby
```

3. A terminálban navigáljon a tárház gyökeréhez. A következő három mappát kell tartalmaznia, amely így néz ki:
```powershell
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2/23/2020  10:42 PM                Application
d-----        2/23/2020   3:05 PM                ArmTemplates
d-----        2/23/2020   3:05 PM                Tests
```

4. Az alkalmazás mappájában nem szeretnénk az összes fájlt használni, mert egy Gatsby-indítóval fogunk lecserélni. A levágáshoz futtassa a következő parancsokat a sorozatokban.
```powershell
cp .\Application\Dockerfile .
rmdir Application
```

5. Minta PWA létrehozásához használja a Gatsby CLI-t. Futtassa `gatsby new` a terminálról a PWA varázsló elindításához, és válassza ki a `gatsby-starter-blog` az alapszintű sablonhoz. Ehhez a példához hasonlónak kell lennie:
```powershell
c:\myproject> gatsby new
√ What is your project called? ... my-gatsby-project
? What starter would you like to use? » - Use arrow-keys. Return to submit.
    gatsby-starter-default
    gatsby-starter-hello-world
>   gatsby-starter-blog
    (Use a different starter)
```

6. Most már rendelkezik egy `my-gatsby-project`nevű mappával. Nevezze át `Application`re, és másolja a `Dockerfile`.
```powershell
mv my-gatsby-project Application
mv Dockerfile Application
```

7. A kedvenc szerkesztőjében nyissa meg a Docker, és módosítsa az első sort `FROM node:8`ról `FROM node:12`re. Ez a változás biztosítja, hogy a tároló a 8. x verzió helyett a Node. js 12. x verziót használja. A GatsbyJS a Node. js modernebb verzióit igényli.

8. Ezután nyissa meg a Package. JSON fájlt az alkalmazás mappájában, és szerkessze a [parancsfájlok mezőt](https://docs.npmjs.com/files/package.json#scripts) , hogy a fejlesztési és üzemi kiszolgálók az összes rendelkezésre álló hálózati adapteren (például 0.0.0.0) és a 80-as porton figyeljenek. Ezen beállítások nélkül a Container app Service nem tudja átirányítani a forgalmat a tárolón belül futó Node. js-alkalmazásba. A `scripts` mezőnek az alábbihoz hasonlónak kell lennie. Pontosabban módosítani szeretné a `develop`, `serve`és `start` célokat az alapértelmezett beállítások alapján.
```json
  "scripts": {
    "build": "gatsby build",
    "develop": "gatsby develop  -H 0.0.0.0 -p 80",
    "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
    "start": "npm run serve",
    "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
    "clean": "gatsby clean",
    "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
  }
```

## <a name="edit-your-cicd-pipelines"></a>A CI/CD-folyamatok szerkesztése

1. Mielőtt véglegesíti a kódot az előző szakaszban, hajtson végre néhány módosítást a build és a Release folyamaton. Szerkessze a "folyamat létrehozása" lehetőséget, és frissítse a csomópont-feladatot a Node. js 12. x verziójának használatára. Állítsa a **feladat verziója** mezőt 1. x értékre, a **Version (verzió** ) mezőre pedig 12. x.
a Node. js ![frissítése 12. x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

2. Ebben a rövid útmutatóban nem hozunk létre egység-teszteket, és letiltjuk ezeket a lépéseket a Build-folyamatban. Tesztek írásakor újra engedélyezheti ezeket a lépéseket. Kattintson a jobb gombbal, és válassza ki a **teszt függőségeinek telepítése** és az **egységek futtatása tesztek** és a Letiltás műveletek elemet.

![Build tesztek letiltása](_img/azure-devops-project-nodejs/disable-build-unittests.png)

3. A kiadási folyamat szerkesztése.
![a kiadási folyamat szerkesztése](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

4. A Build folyamathoz hasonlóan módosítsa a csomópont feladatát 12. x használatára, és tiltsa le a két teszt feladatot. A kiadásnak hasonlónak kell lennie a képernyőképhez.

![Befejezett kiadási folyamat](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. A böngésző bal oldalán lépjen a **views/index. mopsz** fájlra.

1. Válassza a **Szerkesztés**lehetőséget, majd módosítsa a H2-fejlécet.  
    Írja be például a következőt: **első lépések azonnal Azure DevOps projects** , vagy végezze el a módosítást.

1. Válassza **a**végrehajtás lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Projects irányítópultot.   
Ekkor megjelenik egy Build folyamatban. Az elvégzett módosításokat a rendszer automatikusan felépíti és telepíti a CI/CD-folyamaton keresztül.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Véglegesítse a módosításokat, és vizsgálja meg az Azure CI/CD folyamatát

Az előző két lépésben hozzáadott egy Gatsby létrehozott PWA a git-tárházhoz, és szerkesztette a folyamatokat a kód összeállításához és üzembe helyezéséhez. Véglegesítheti a kódot, és megtekintheti az előrehaladást a build és a Release folyamaton keresztül.

1. A projekt git-tárházának gyökeréből egy terminálon futtassa a következő parancsokat a kód Azure DevOps-projektbe való leküldéséhez:
```powershell
git add .
git commit -m "My first Gatsby PWA"
git push
```

2. A rendszer azonnal elindít egy összeállítást, amint `git push` befejeződik. Az **Azure DevOps irányítópultján**követheti a folyamat előrehaladását.

![Azure DevOps-irányítópult az erőforrás-listában](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

3. Néhány perc elteltével a létrehozási és kiadási folyamatokat be kell fejezni, és a PWA üzembe kell helyezni egy tárolón. Kattintson az **alkalmazás-végpont** hivatkozásra a fenti irányítópulton, és egy Gatsby Starter-projektet kell megjelennie a blogokhoz.



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti Azure App Service és az egyéb kapcsolódó erőforrásokat, amelyeket akkor hozott létre, amikor már nincs szüksége az erőforrásokra. Használja a **delete** funkciót a DevOps projects irányítópulton.


## <a name="next-steps"></a>További lépések

A CI/CD folyamat konfigurálásakor a rendszer automatikusan létrehozza a létrehozási és kiadási folyamatokat. Ezeket a build és kiadási folyamatokat a csapat igényeinek megfelelően módosíthatja. A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

