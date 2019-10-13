---
title: 'Oktatóanyag: CI/CD-folyamat létrehozása meglévő kódhoz a Azure DevOps Projects használatával'
description: Azure DevOps Projects megkönnyíti az Azure megkezdését. A DevOps Projects segítségével a saját kód és a GitHub-tárház használatával néhány gyors lépéssel elindíthat egy alkalmazást egy tetszőleges Azure-szolgáltatásban.
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
ms.openlocfilehash: 4a48b5c413f6dfb58a1ba3f896ded49250cde306
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286305"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Oktatóanyag: CI/CD-folyamat létrehozása meglévő kódhoz a Azure DevOps Projects használatával

Azure DevOps Projects egy egyszerűsített felhasználói felülettel rendelkezik, ahol meglévő kód-és git-tárházat hozhat létre, vagy kiválaszthat egy minta alkalmazást, amellyel folyamatos integrációs (CI) és folyamatos átviteli (CD) folyamatokat hozhat létre az Azure-ba.

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
* Hozzáférés egy olyan GitHubhoz vagy külső git-tárházhoz, amely .NET, Java, PHP, node, Python vagy statikus webes kódot tartalmaz.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A Azure DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A Azure DevOps Projects Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. A bal oldali panelen válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali navigációs sávon, majd keressen rá **DevOps projects**a **Létrehozás**gombra.

   ![A DevOps Projects irányítópult](_img/azure-devops-project-github/azuredashboard.png)

3. Válassza **a saját kód**használata lehetőséget, majd kattintson a **tovább**gombra.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>A GitHub-tárházhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása

1. Válassza a **GitHub** vagy egy külső **git** -kód tárház lehetőséget. Ehhez az oktatóanyaghoz válassza a **GitHub**lehetőséget. Előfordulhat, hogy először hitelesítenie kell a GitHubot, hogy az Azure hozzáférhessen a GitHub-tárházhoz.

2. A **tárház** és az **ág**kiválasztásával fejezze be a **Next (tovább**) gombot.

3. Ha Docker-tárolókat használ, az **alkalmazás Dockerized** **Igen**értékre vált, ebben az **oktatóanyagban ne válassza** a **tovább**lehetőséget. A Docker-tárolók használatával kapcsolatos további információkért vigye az egérmutatót az **i** ikon fölé.

   ![.NET-keretrendszer](_img/azure-devops-project-github/appframework.png)

4. A legördülő listából válassza ki az **alkalmazás futtatókörnyezetét** és **keretrendszerét**, majd kattintson a **tovább**gombra. A kiválasztott alkalmazás-keretrendszer határozza meg az elérhető Azure-szolgáltatások telepítési céljának típusát.

5. Válassza ki az **Azure-szolgáltatást** az alkalmazás üzembe helyezéséhez, majd válassza a **tovább**lehetőséget.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure-DevOps és Azure-előfizetés konfigurálása

1. Adja meg a **projekt nevét**.

2. Hozzon létre egy új ingyenes **Azure DevOps-szervezetet** , vagy válasszon ki egy meglévő szervezetet a legördülő listából.

3. Válassza ki az **Azure-előfizetését**, adja meg a **webalkalmazás** nevét, vagy használja az alapértelmezett értéket. Válasszon ki egy **helyet**, majd kattintson a **kész**gombra. Néhány perc elteltével a DevOps-projekt üzembe helyezésének áttekintése megjelenik a Azure Portal.

4. Válassza az **Ugrás az erőforráshoz** lehetőséget a DevOps-projekt irányítópultjának megtekintéséhez. A jobb felső sarokban rögzítse a **projektet** az irányítópulton a gyors hozzáférés érdekében. Azure DevOps Projects automatikusan konfigurálja a CI-Build és a kiadási triggert. A kód a GitHub-tárházban vagy egy másik külső tárházban marad. Egy minta alkalmazás van beállítva az **Azure DevOps-szervezetben**lévő tárházban. A rendszer végrehajtja a buildet, és az alkalmazást üzembe helyezi az Azure-ban.

   ![DevOps Projects irányítópult nézet](_img/azure-devops-project-github/projectsdashboard.png)

5. Az irányítópulton látható a kód tárháza, a CI/CD-folyamat és az Azure-beli alkalmazás. Az Azure-erőforrások alatt kattintson a **Tallózás** gombra a futó alkalmazás megtekintéséhez.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban

Most már készen áll az alkalmazásban lévő csapattal való együttműködésre egy CI/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkát a webhelyén. A GitHub-tárház minden módosítása egy buildet indít az Azure DevOps, és egy CD-folyamat végrehajtja az Azure-ba történő üzembe helyezést.

1. A DevOps-projekt irányítópultján válassza a **tárolók**lehetőséget. A GitHub-tárház egy új böngészőablakban nyílik meg. végezze el az alkalmazás módosítását, majd kattintson a **módosítások véglegesítve**elemre.

2. Néhány pillanat elteltével a buildek az Azure-folyamatokban kezdődnek. A Build állapotát a DevOps Projects irányítópulton figyelheti, vagy megfigyelheti az Azure DevOps-szervezetben, ha kiválasztja a **folyamatok létrehozása** lapot a projekt irányítópultján.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Az Azure-folyamatok CI/CD-folyamatának vizsgálata

Azure DevOps Projects automatikusan konfigurálja a CI/CD-folyamatokat az Azure-folyamatokban. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A build és a Release folyamat megismeréséhez tegye a következőket:

1. A DevOps Projects irányítópulton válassza a **folyamatok létrehozása**lehetőséget.

2. Miután megnyitotta az **Azure-folyamatok** oldalát, megjelenik a legutóbbi buildek előzményei, valamint az egyes buildek állapota.

   ![Azure DevOps-folyamat buildek](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. A **builds oldal jobb** felső sarkában megtekintheti a jelenlegi Build, **a** **várólista** és az új buildek várólistára helyezését, valamint a három **&#8942;** pontot () egy menü megnyitásához a további beállításokkal. Válassza a **Szerkesztés**lehetőséget.

4. A build különböző feladatokat hajt végre, például a források beolvasását a tárházból, a függőségek visszaállítását és a kimenetek közzétételét a központi telepítésekhez. A **név**alatt a jobb oldalon módosítsa a build-folyamat nevét, hogy valami legyen a leíró. Válassza a **mentés & üzenetsor**, majd a **Mentés**lehetőséget, hagyja el a megjegyzést, majd kattintson ismét a **Mentés** gombra.

   ![Az Azure DevOps builds oldala](_img/azure-devops-project-github/buildpage.png)

5. Ha meg szeretné tekinteni a Build legutóbbi változásainak naplózását, válassza az **Előzmények** fület. az Azure DevOps nyomon követi a fordítási folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

6. Válassza az **Eseményindítók** fület. az Azure DevOps Project automatikusan létrehoz egy CI-eseményindítót néhány alapértelmezett beállítással. Az olyan eseményindítók, mint például a **folyamatos integráció engedélyezése** beállítható úgy, hogy a rendszer minden alkalommal végrehajtson egy összeállítást, ha a kód módosítása véglegesítve lett, vagy a buildek ütemezése adott időpontban fut.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az oktatóanyagban létrehozott Azure app Service-t és kapcsolódó erőforrásokat. Ehhez használja a **Törlés** funkciót a DevOps projects irányítópulton.

## <a name="next-steps"></a>Következő lépések

Ha az oktatóanyagban konfigurálta a CI/CD-folyamatot, a rendszer automatikusan létrehoz egy összeállítási és kiadási folyamatot Azure DevOps Projects. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>  * DevOps Projects használata CI/CD-folyamat létrehozásához
> * A GitHub-tárházhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása
> * Azure-DevOps és Azure-előfizetés konfigurálása
> * Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban
> * Az Azure-folyamatok CI/CD-folyamatának vizsgálata
> * Az erőforrások eltávolítása

A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A többfázisú folyamatos üzembe helyezés (CD) folyamatának meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Az alkalmazások figyeléséről további információt a következő témakörben talál:
  
 > [!div class="nextstepaction"]
 > [Mi az Azure monitor?](https://docs.microsoft.com/azure/azure-monitor/overview)
