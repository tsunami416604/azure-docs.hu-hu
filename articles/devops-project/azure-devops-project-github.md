---
title: 'Oktatóanyag: Hozzon létre egy CI/CD-folyamatot a meglévő kódhoz az Azure DevOps-projektek használatával'
description: Az Azure DevOps-projektek megkönnyítik az azure-ral való ismerkedést. Néhány gyors lépésben a DevOps-projektek segítségével saját kóddal és GitHub-tártárral indíthat el egy alkalmazást egy Azure-szolgáltatásban.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73615131"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Oktatóanyag: Hozzon létre egy CI/CD-folyamatot a meglévő kódhoz az Azure DevOps-projektek használatával

Az Azure DevOps-projektek egyszerűsített folyamatot mutat be a folyamatos integrációs (CI) és a folyamatos kézbesítési (CD) folyamat létrehozásához az Azure-ba. Hozhat a meglévő kódot és a Git-tárta, vagy választhat egy mintaalkalmazás.

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * CI/CD-folyamat létrehozása DevOps-projektek használatával
> * Konfigurálja a Hozzáférést a GitHub-tárterülethez, és válasszon egy keretrendszert
> * Az Azure DevOps és egy Azure-előfizetés konfigurálása 
> * Módosítások véglegesítése a GitHubon, és automatikusan üzembe helyezése az Azure-ban
> * Az Azure Pipelines CI/CD-folyamat vizsgálata
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.
* Hozzáférés egy GitHubhoz vagy külső Git-tárta, amely .NET, Java, PHP, Node.js, Python vagy statikus webkódot tartalmaz.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az Azure DevOps-projektek létrehoz egy CI/CD-folyamatot az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. Az Azure DevOps-projektek azure-erőforrásokat is létrehoznak az Ön által kiválasztott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.

   ![Az Azure Portal menüje – Erőforrás létrehozása](_img/azure-devops-project-github/createaresource.png)

3. Válassza a **DevOps** > **DevOps-projekt lehetőséget.**

   ![A DevOps-projektek irányítópultja](_img/azure-devops-project-github/azuredashboard.png)

1. Válassza **a Saját kód átviete**lehetőséget, majd a **Tovább**gombot.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Hozzáférés konfigurálása a GitHub-tárterülethez, és a keretrendszer kiválasztása

1. Válassza ki a **GitHubot** vagy egy külső Git-kódtárot. **Git** Ehhez az oktatóanyaghoz válassza a **GitHub**lehetőséget. Előfordulhat, hogy első alkalommal kell hitelesítenie magát a GitHub-szolgáltatással, hogy az Azure hozzáférhessen a GitHub-tárházhoz.

1. Jelöljön ki egy **adattárat** és egy **ágat,** majd kattintson a **Tovább**gombra.

1. Ha Docker-tárolókat használ, módosítsa **az Is app Dockerized alkalmazást** **IGEN-re.** Ebben az oktatóanyagban hagyja **a NO lehetőséget,** majd válassza a **Tovább**gombot. A Docker-tárolók használatával kapcsolatos további információkért mutasson az **i** ikonra.

   ![Alkalmazáskeret kiválasztása a legördülő menüben](_img/azure-devops-project-github/appframework.png)

1. A legördülő menükben válasszon ki egy **alkalmazásfutási időt** és egy **alkalmazáskeretrendszert**, majd válassza a **Tovább**gombot. Az alkalmazáskeretrendszer határozza meg, hogy milyen típusú Azure-szolgáltatás üzembe helyezési cél érhető el.

1. Válasszon ki egy **Azure-szolgáltatást** az alkalmazás üzembe helyezéséhez, majd válassza a **Tovább**gombot.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és egy Azure-előfizetés konfigurálása

1. Adja meg a **Projekt nevének nevét.**

1. Hozzon létre egy új ingyenes szervezetet az **Azure DevOps Organization** ben, vagy válasszon ki egy meglévő szervezetet a legördülő menüből.

1. Válassza ki az előfizetést az **Azure-előfizetésben**, és vagy adjon meg egy nevet a **webalkalmazásban,** vagy használja az alapértelmezett. Jelöljön ki egy **Helyet,** majd kattintson **a Kész gombra.** Néhány perc múlva a DevOps-projektek üzembe helyezési áttekintése jelenik meg az Azure Portalon.

1. Válassza az **Ugrás az erőforrásra** lehetőséget a DevOps-projektek irányítópultjának megtekintéséhez. A jobb felső sarokban rögzítse a **Projectet** az irányítópultra a gyors elérés érdekében. Az Azure DevOps-projektek automatikusan konfigurálja a CI build és kiadás eseményindító. A kód a GitHub-tárházban vagy egy másik külső tárházban marad, és egy mintaalkalmazás be van állítva egy tárházban az **Azure DevOps Organization ben.** Az Azure DevOps-projektek futtatja a buildet, és telepíti az alkalmazást az Azure-ba.

   ![Az Azure DevOps Projects irányítópultjának nézete](_img/azure-devops-project-github/projectsdashboard.png)

1. Az irányítópulton a kódtár, a CI/CD-folyamat és az azure-beli alkalmazás látható. A jobb oldalon az Azure-erőforrások csoportban válassza a **Tallózás** lehetőséget a futó alkalmazás megtekintéséhez.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Módosítások véglegesítése a GitHubon, és automatikusan üzembe helyezése az Azure-ban

Most már készen áll arra, hogy egy csapattal együttműködjön az alkalmazásán. A CI/CD folyamat automatikusan telepíti a legújabb munkát a webhelyére. A GitHub-tárházban minden módosítás létrehoz egy buildet az Azure DevOps-ban, és egy CD-folyamat futtat egy üzembe helyezést az Azure-ban.

1. A DevOps-projektek irányítópultján válassza az **Adattárak**lehetőséget. A GitHub-tárház egy új böngészőlapon nyílik meg. Módosítsa az alkalmazást, és válassza **a Módosítások véglegesítése**lehetőséget.

1. Néhány pillanat múlva egy build elindul az Azure-folyamatokban. Figyelheti a build állapotát a DevOps-projektek irányítópulton. Az Azure DevOps-szervezetben is figyelheti, ha a DevOps-projektek irányítópultján kiválasztja a **Folyamatok összeállítása** lapot.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Az Azure Pipelines CI/CD-folyamat vizsgálata

Az Azure DevOps-projektek automatikusan konfigurálja a CI/CD-folyamat ot az Azure-folyamatokban. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A build- és kiadási folyamatok megismeréséhez tegye a következőket:

1. A DevOps-projektek irányítópultján válassza a **Folyamatok összeállítása**lehetőséget.

1. Miután az **Azure-folyamatok** lap megnyitása, látni fogja a legutóbbi buildek előzményeit és az egyes buildek állapotát.

   ![Az Azure Pipelines buildelési lapja](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. A **Builds** lap jobb felső sarkában a Szerkesztés lehetőséget választva **módosíthatja** az aktuális buildet, a **Várólista** új buildet, vagy a függőleges három pont gomb (**&#8942;**) lehetőséget választva megnyithat egy több lehetőséget tartalmazó menüt. Válassza a **Szerkesztés** elemet.

1. A build különböző feladatokat végez, például a források lekérése a tárházból, a függőségek visszaállítása és a központi telepítések kimenetének közzététele. A jobb oldalon, **a Név**csoportban módosítsa a buildfolyamat nevét valami leíróbbra. Válassza **a & várólista mentése**lehetőséget, majd a **Mentés**lehetőséget. Írjon be egy megjegyzést, majd kattintson ismét **a Mentés** gombra.

   ![Az Azure DevOps buildelési lapja](_img/azure-devops-project-github/buildpage.png)

1. A build legutóbbi módosításainak naplózási nyomvonalát az **Előzmények** lapon szeretné megtekinteni.  Az Azure DevOps nyomon követi a buildfolyamat on végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítók** lapot. Az Azure DevOps-projektek automatikusan létrehoz egy CI-eseményindítót néhány alapértelmezett beállítással. Beállíthatja az eseményindítók, például **engedélyezése folyamatos integráció** futtatni egy build minden alkalommal, amikor véglegesíti a kód módosítása. Azt is beállíthatja, hogy az eseményindítók ütemezze a buildeket, hogy meghatározott időpontokban fussanak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Azure App Service-szolgáltatásra és az oktatóanyagban létrehozott kapcsolódó erőforrásokra, törölheti őket. Használja a **Törlés** funkciót a DevOps-projektek irányítópulton.

## <a name="next-steps"></a>További lépések

Ha konfigurálta a CI/CD-folyamatot ebben az oktatóanyagban, automatikusan létrehozott egy build- és kiadási folyamatot az Azure DevOps-projektekben. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat.

A CI/CD-folyamatról a következő témakörben olvashat bővebben:

> [!div class="nextstepaction"]
> [A többlépcsős folyamatos üzembe helyezési (CD) folyamat meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Az alkalmazások figyeléséről az:
  
 > [!div class="nextstepaction"]
 > [Mi az Azure monitor?](https://docs.microsoft.com/azure/azure-monitor/overview)
