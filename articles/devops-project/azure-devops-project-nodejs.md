---
title: CI/CD-folyamat létrehozása PwA-hoz GatsbyJS és Azure DevOps-projektekkel
description: A DevOps-projektek megkönnyítik az Azure-ral való ismerkedést. A segítségével néhány gyors lépésben elindíthat egy alkalmazást a választott Azure-szolgáltatásban.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78209067"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Rövid útmutató: CI/CD-folyamat létrehozása az Azure Pipelines for Node.js szolgáltatásban az Azure DevOps-projektekkel
Ebben a rövid útmutatóban hozzon létre egy NodeJS progresszív webalkalmazást (PWA) a [GatsbyJS](https://www.gatsbyjs.org/) és az egyszerűsített Azure DevOps-projektlétrehozási felület használatával. Ha elkészült, folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamattal rendelkezik a PWA-hoz az Azure-folyamatokban. Az Azure DevOps-projektek beállítja, amire szüksége van a fejlesztéshez, üzembe helyezéshez és figyeléshez.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Egy [Azure DevOps-szervezet.](https://azure.microsoft.com/services/devops/)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps-projektek létrehoznak egy CI/CD-folyamatot az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrásokat az Azure-előfizetés az Ön által kiválasztott.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és a bal oldali ablaktáblában válassza **az Erőforrás létrehozása lehetőséget.** 

   ![Azure-erőforrás létrehozása az Azure Portalon](_img/azure-devops-project-nodejs/create-azure-resource.png)

2. Keresse meg a **DevOps-projekteket,** és válassza a **Create (Létrehozás)** lehetőséget.

 ![DevOps-projekt létrehozása](_img/azure-devops-project-nodejs/create-devops-project.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza a Node.js mintaalkalmazást.   

 ![Válassza ki a Node.js mintát](_img/azure-devops-project-nodejs/select-nodejs-devops-project.png) 

2. Az alapértelmezett mintakeretrendszer az **Express.js**. Módosítsa a kijelölést **Egyszerű node.js alkalmazásra,** majd válassza a **Tovább**lehetőséget. 

 ![Válassza ki az Egyszerű node.js alkalmazást](_img/azure-devops-project-nodejs/simple-nodejs-project.png) 

3. Az ebben a lépésben elérhető telepítési célokat a 2.  Ebben a példában a **Windows Web App** az alapértelmezett telepítési cél.  Hagyja el **a Web App tárolókhoz** beállítását, és válassza a **Tovább gombot.**

 ![A telepítési cél kiválasztása](_img/azure-devops-project-nodejs/select-web-server.png) 

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Projektnév és Azure-előfizetés konfigurálása

1. A DevOps-projektlétrehozási munkafolyamat utolsó lépésében hozzárendel egy projektnevet, kijelöl egy Azure-előfizetést, és a Kész lehetőséget **választja.**  

 ![Projektnév hozzárendelése és előfizetés kiválasztása](_img/azure-devops-project-nodejs/assign-project-name.png) 

2. Egy összefoglaló lap jelenik meg, miközben a projekt épül, és az alkalmazás telepítve van az Azure-ban. Rövid idő elteltével létrejön egy projekt az [Azure DevOps-szervezetben,](https://dev.azure.com/) amely tartalmazza a git-tárház, a kanbantábla, a telepítési folyamat, a tesztelési tervek és az alkalmazás által igényelt összetevők.  

## <a name="managing-your-project"></a>A projekt kezelése

1. Keresse meg a **Minden erőforrás t,** és keresse meg a DevOps-projektet. Válassza ki a **DevOps-projektet.**

![Azure DevOps-irányítópult az erőforráslistában](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

2. A rendszer egy irányítópultra irányítja, amely betekintést nyújt a projekt kezdőlapjára, a kódtárházba, a CI/CD-folyamatba és a futó alkalmazásra mutató hivatkozásba. Válassza ki a **Project kezdőlapját** az alkalmazás megtekintéséhez az **Azure DevOps-ban,** és egy másik böngészőlapon válassza az **alkalmazásvégpontot** az élő mintaalkalmazás megtekintéséhez.  Ezt a mintát később megváltoztatjuk, hogy a GatsbyJS által generált PWA-t használjuk.

![Azure DevOps irányítópult](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

3. Az Azure DevOps-projektből meghívhatja a csapattagokat az együttműködésre, és létrehozhat egy Kanban-táblát a munka nyomon követéséhez.  További információ: [itt](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Azure DevOps – áttekintés](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Klónozza a repo és telepítse a Gatsby PWA

A DevOps-projektek létrehoznak egy git-tárházat az Azure Reposban vagy a GitHubon. Ez a példa létrehozott egy Azure-tárt.  A következő lépés a tárműtér klónozása és a módosítások végrehajtása.

1. Válassza a **DevOps-projekt** ből a **Repók lehetőséget,** majd kattintson a **Klónozás gombra.**  Vannak különböző mechanizmusok klón a git tárház az asztalra.  Válassza ki azt, amelyik megfelel a fejlesztési élménynek.  

![Az adattár klónozása](_img/azure-devops-project-nodejs/clone-the-repo.png)

2. Miután a tárta klónozott az asztalra, hogy néhány módosítást az induló sablont. Kezdje a GatsbyJS CLI telepítésével a terminálról.
```powershell
npm install -g gatsby
```

3. A terminálról keresse meg a tárműtér gyökerét. Három mappát kell tartalmaznia, amelyek így néznek ki:
```powershell
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2/23/2020  10:42 PM                Application
d-----        2/23/2020   3:05 PM                ArmTemplates
d-----        2/23/2020   3:05 PM                Tests
```

4. Nem akarjuk, hogy az összes fájlt az Alkalmazás mappában, mert fogunk cserélni egy Gatsby starter. Futtassa a következő parancsokat egymás után, hogy vágja le.
```powershell
cp .\Application\Dockerfile .
rmdir Application
```

5. Használja a Gatsby CLI-t a PWA minta létrehozásához. Futtassa `gatsby new` a terminálról a PWA varázsló megkezdéséhez, és válassza ki `gatsby-starter-blog` az indítósablont. Ennek a mintához kell hasonlítania:
```powershell
c:\myproject> gatsby new
√ What is your project called? ... my-gatsby-project
? What starter would you like to use? » - Use arrow-keys. Return to submit.
    gatsby-starter-default
    gatsby-starter-hello-world
>   gatsby-starter-blog
    (Use a different starter)
```

6. Most már van `my-gatsby-project`egy mappája, amelynek neve . Nevezze át, `Application` és `Dockerfile` másolja be.
```powershell
mv my-gatsby-project Application
mv Dockerfile Application
```

7. Kedvenc szerkesztőjében nyissa meg a Docker-fájlt, `FROM node:12`és módosítsa az első sort a-ra. `FROM node:8` Ez a módosítás biztosítja, hogy a tároló a Node.js 12.x-es verzióját használja a 8.x-es verzió helyett. A GatsbyJS a Node.js modernebb verzióit igényli.

8. Ezután nyissa meg a package.json fájlt az Alkalmazás mappában, és [szerkesztse a parancsfájlok mezőt,](https://docs.npmjs.com/files/package.json#scripts) hogy a fejlesztői és az éles kiszolgálók az összes elérhető hálózati csatolón (például 0.0.0.0) és a 80-as porton figyeljék a fejlesztői és az éles kiszolgálókat. Ezek nélkül a beállítások nélkül a tárolóalkalmazás-szolgáltatás nem tudja a forgalmat a Node.js alkalmazásra irányítani a tárolóban. A `scripts` mezőnek az alábbiakra kell hasonlítania. Pontosabban a , `develop` `serve`és `start` a célokat az alapértelmezett értékekből szeretné módosítani.
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

## <a name="edit-your-cicd-pipelines"></a>Ci/CD-folyamatok szerkesztése

1. Mielőtt véglegesíti a kódot az előző szakaszban, néhány módosítást a build és a kiadási folyamatok. Írja be a "Build Pipeline" tevékenységet, és frissítse a Node feladatot a Node.js 12.x verziójának használatára. Állítsa a **Tevékenység verziómezőjét** 1.x-re, a **Verzió** mezőt pedig 12.x-re.
![Node.js frissítése 12.x-re](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

2. Ebben a rövid útmutatóban nem hozunk létre egységteszteket, és letiltjuk ezeket a lépéseket a build-folyamatban. Tesztek írásakor újra engedélyezheti ezeket a lépéseket. Kattintson a jobb gombbal a **Tesztfüggőségek telepítése** és **az egységtesztek futtatása** és letiltása feliratú feladatok kijelöléséhez.

![Buildtesztek letiltása](_img/azure-devops-project-nodejs/disable-build-unittests.png)

3. A kiadási folyamat szerkesztése.
![A kiadási folyamat szerkesztése](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

4. A buildfolyamathoz ugyanúgy módosítsa a Csomópont-feladatot a 12.x használatára, és tiltsa le a két tesztfeladatot. A kiadásnak ehhez a képernyőképhez kell hasonlítania.

![Befejeződött kiadási folyamat](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. A böngésző bal oldalán nyissa meg a **views/index.pug** fájlt.

1. Válassza **a Szerkesztés**lehetőséget, majd módosítsa a h2 címsort.  
    Írja be például az **Első lépések az Azure DevOps-projekteket,** vagy egyéb módosításokat.

1. Válassza **a Véglegesítés**lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps-projektek irányítópultját.   
Most látnia kell egy folyamatban lévő buildet. A végrehajtott módosítások automatikusan létrejönnek és ci/CD-folyamaton keresztül települnek.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>A módosítások véglegesítése és az Azure CI/CD-folyamat vizsgálata

Az előző két lépésben hozzáadott egy Gatsby által generált PWA-t a git-tárműtérhez, és szerkesztette a folyamatokat a kód létrehozásához és üzembe helyezéséhez. Véglegesíthetjük a kódot, és figyelhetjük, hogy halad a build- és kiadási folyamaton keresztül.

1. A projekt terminálon lévő git-tártárjának gyökeréből futtassa a következő parancsokat a kód Azure DevOps-projektbe való leküldéseshez:
```powershell
git add .
git commit -m "My first Gatsby PWA"
git push
```

2. A build akkor indul `git push` el, amikor befejeződik. Az **Azure DevOps irányítópulton**követheti az előrehaladást.

![Azure DevOps-irányítópult az erőforráslistában](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

3. Néhány perc múlva a build- és kiadási folyamatoknak be kell fejeződniük, és a PWA-t egy tárolóra kell telepíteni. Kattintson az **alkalmazás végpontja** hivatkozásra a fenti irányítópulton, és megjelenik egy Gatsby kezdő projekt a blogok számára.



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti az Azure App Service-t és más kapcsolódó erőforrásokat, amelyeket akkor hozott létre, amikor már nincs szüksége az erőforrásokra. Használja a **Törlés** funkciót a DevOps-projektek irányítópulton.


## <a name="next-steps"></a>További lépések

A CI/CD folyamat konfigurálásakor a rendszer automatikusan létrehozza a build- és kiadási folyamatokat. Ezeket a buildelési és kiadási folyamatokat a csapat igényeinek megfelelően módosíthatja. A CI/CD-folyamatról a következő témakörben olvashat bővebben:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

