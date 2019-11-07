---
title: 'Oktatóanyag: CI/CD-folyamat létrehozása meglévő kódhoz a Azure DevOps Projects használatával'
description: Azure DevOps Projects megkönnyíti az Azure megkezdését. Néhány gyors lépéssel a DevOps Projects a saját kód és a GitHub-tárház használatát teszi lehetővé az alkalmazások Azure-szolgáltatásokban való indításához.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: a0496999a9a5dc3e9bfd57df0ec035e6db77d620
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615131"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Oktatóanyag: CI/CD-folyamat létrehozása meglévő kódhoz a Azure DevOps Projects használatával

A Azure DevOps Projects egyszerűsített eljárást biztosít a folyamatos integráció (CI) és a folyamatos továbbítás (CD) folyamatának az Azure-ba való létrehozásához. Használhatja a meglévő kódot és a git-tárházat, vagy kijelölhet egy minta alkalmazást is.

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * DevOps Projects használata CI/CD-folyamat létrehozásához
> * A GitHub-tárházhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása
> * Azure-DevOps és Azure-előfizetés konfigurálása 
> * Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban
> * Az Azure-folyamatok CI/CD-folyamatának vizsgálata
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.
* Hozzáférés egy olyan GitHubhoz vagy külső git-tárházhoz, amely .NET, Java, PHP, Node. js, Python vagy statikus webes kódot tartalmaz.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A Azure DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A Azure DevOps Projects Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. A Azure Portal menüben válassza az **erőforrás létrehozása**lehetőséget.

   ![Azure Portal menü – erőforrás létrehozása](_img/azure-devops-project-github/createaresource.png)

3. Válassza ki a **DevOps** > **DevOps projektet**.

   ![A DevOps Projects irányítópult](_img/azure-devops-project-github/azuredashboard.png)

1. Válassza **a saját kód**használata lehetőséget, majd kattintson a **tovább**gombra.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>A GitHub-tárházhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása

1. Válassza a **GitHub** vagy egy külső **git** -kód tárház lehetőséget. Ebben az oktatóanyagban válassza a **GitHub**lehetőséget. Előfordulhat, hogy először hitelesítenie kell a GitHubot, hogy az Azure hozzáférjen a GitHub-tárházhoz.

1. Válasszon egy **tárházat** és egy **ágat**, majd válassza a **tovább**lehetőséget.

1. Ha Docker-tárolókat használ, módosítsa **az alkalmazás Dockerized** **Igen**értékre. Ebben az oktatóanyagban hagyja a **nincs** kiválasztva lehetőséget, majd kattintson a **tovább**gombra. A Docker-tárolók használatával kapcsolatos további információkért vigye az egérmutatót az **i** ikon fölé.

   ![Az alkalmazás-keretrendszer kiválasztása a legördülő menüben](_img/azure-devops-project-github/appframework.png)

1. A legördülő menüben válasszon ki egy alkalmazás- **futtatókörnyezetet** és egy **alkalmazás-keretrendszert**, majd válassza a **tovább**lehetőséget. Az alkalmazás-keretrendszer az elérhető Azure Service-telepítési cél típusát diktálja.

1. Válasszon ki egy **Azure-szolgáltatást** az alkalmazás telepítéséhez, majd kattintson a **tovább**gombra.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure-DevOps és Azure-előfizetés konfigurálása

1. Adja meg a **projekt nevének**nevét.

1. Hozzon létre egy új ingyenes céget az **Azure DevOps-szervezetben** , vagy válasszon ki egy meglévő szervezetet a legördülő menüből.

1. Válassza ki az előfizetését az **Azure-előfizetésben**, és adjon meg egy nevet a **webalkalmazásban** , vagy használja az alapértelmezett értéket. Válasszon ki egy **helyet**, majd kattintson a **kész**gombra. Néhány perc elteltével a DevOps Projects központi telepítésének áttekintése megjelenik a Azure Portalban.

1. Válassza az **Ugrás az erőforráshoz** lehetőséget az DevOps projects irányítópult megtekintéséhez. A jobb felső sarokban rögzítse a **projektet** az irányítópulton a gyors hozzáférés érdekében. Azure DevOps Projects automatikusan konfigurálja a CI-Build és a kiadási triggert. A kód a GitHub-tárházban vagy egy másik külső tárházban marad, és egy minta alkalmazás van beállítva egy adattárban az **Azure DevOps-szervezetben**. Azure DevOps Projects futtatja a buildet, és üzembe helyezi az alkalmazást az Azure-ban.

   ![Azure DevOps Projects irányítópult nézet](_img/azure-devops-project-github/projectsdashboard.png)

1. Az irányítópulton a kód tárháza, a CI/CD-folyamat és az Azure-beli alkalmazás látható. A jobb oldalon az Azure-erőforrások területen válassza a **Tallózás** lehetőséget a futó alkalmazás megtekintéséhez.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban

Most már készen áll az alkalmazásban való együttműködésre a csapattal. A CI/CD-folyamat automatikusan üzembe helyezi a legújabb munkát a webhelyén. A GitHub-tárház minden módosítása elindítja az Azure DevOps, és egy CD-folyamat futtatja az Azure-ba való üzembe helyezést.

1. A DevOps Projects irányítópulton válassza a **tárak**lehetőséget. A GitHub-tárház egy új böngészőablakban nyílik meg. végezze el az alkalmazás módosítását, majd válassza a **módosítások véglegesítve**lehetőséget.

1. Néhány pillanat elteltével a buildek az Azure-folyamatokban kezdődnek. A Build állapotát a DevOps Projects irányítópulton figyelheti. Azt is megfigyelheti az Azure DevOps-szervezetben, ha a DevOps Projects irányítópultján a **folyamatok kiépítése** lapot választja.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Az Azure-folyamatok CI/CD-folyamatának vizsgálata

Azure DevOps Projects automatikusan konfigurálja a CI/CD-folyamatokat az Azure-folyamatokban. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A build és a Release folyamat megismeréséhez tegye a következőket:

1. A DevOps Projects irányítópulton válassza a **folyamatok létrehozása**lehetőséget.

1. Az **Azure-folyamatok** lap megnyitása után megjelenik a legutóbbi buildek előzményei, valamint az egyes buildek állapota.

   ![Az Azure-folyamatok buildek lapja](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. **A builds oldal jobb** felső sarkában a **Szerkesztés** lehetőségre kattintva módosíthatja az aktuális Build, a **várólista** új Build hozzáadását, vagy a függőleges három pont ( **&#8942;** ) gombot a további beállításokkal rendelkező menü megnyitásához. Válassza a **Szerkesztés** elemet.

1. A build különböző feladatokat hajt végre, például a források beolvasása a tárházból, a függőségek visszaállítása és a kimenetek közzététele a központi telepítésekhez. A jobb oldalon a **név**területen módosítsa a build-folyamat nevét egy ennél több leíróra. Válassza a **mentés & üzenetsor**lehetőséget, majd kattintson a **Mentés**gombra. Írjon be egy megjegyzést, majd kattintson ismét a **Save (Mentés** ) gombra.

   ![Az Azure DevOps builds oldala](_img/azure-devops-project-github/buildpage.png)

1. Ha meg szeretné tekinteni a Build legutóbbi változásainak naplózását, válassza az **Előzmények** fület.  Az Azure DevOps nyomon követi a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítók** fület. Azure DevOps projects automatikusan létrehoz egy CI-eseményindítót néhány alapértelmezett beállítással. Olyan eseményindítókat is beállíthat, mint például a **folyamatos integráció lehetővé tétele** , hogy minden egyes kód módosításakor futtasson egy buildet. Az eseményindítókat beállíthatja úgy is, hogy az adott időpontokban futtatandó buildek ütemezhetők legyenek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége Azure App Servicera és az oktatóanyagban létrehozott kapcsolódó erőforrásokra, törölheti őket. Használja a **delete** funkciót a DevOps projects irányítópulton.

## <a name="next-steps"></a>További lépések

Ha ebben az oktatóanyagban konfigurálta a CI/CD-folyamatot, a Azure DevOps Projects-ben automatikusan létrehozta a létrehozási és kiadási folyamatokat. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat.

A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A többfázisú folyamatos üzembe helyezés (CD) folyamatának meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Az alkalmazások figyeléséről további információt a következő témakörben talál:
  
 > [!div class="nextstepaction"]
 > [Mi az Azure monitor?](https://docs.microsoft.com/azure/azure-monitor/overview)
