---
title: 'Gyors útmutató: CI/CD-folyamat létrehozása a PHP-hez Azure DevOps Projects'
description: DevOps Projects megkönnyíti az Azure megkezdését. A segítségével néhány gyors lépésben elindíthat egy alkalmazást a választott Azure-szolgáltatásban.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 7c097c2a7247c9cad6cdce8a89059ee8d90d159e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899590"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-projects"></a>CI/CD-folyamat létrehozása a PHP-hez Azure DevOps Projects

A Azure DevOps Projects egy egyszerűsített élményt nyújt, amely Azure-erőforrásokat hoz létre, és beállítja a PHP-alkalmazás folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatát az Azure-folyamatokban.  

Ha még nem rendelkezik Azure-előfizetéssel, a [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)használatával ingyenesen beszerezhet egyet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

 A DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy ingyenes új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **erőforrás létrehozása** ikont, majd keresse meg a **DevOps projects**.  

3. Kattintson a **Létrehozás** gombra.

    ![Folyamatos kézbesítési konfiguráció indítása](_img/azure-devops-project-php/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza ki a PHP-minta alkalmazást.  
        A PHP-minták több alkalmazás-keretrendszer közül választhatnak. Az alapértelmezett minta-keretrendszer a Laravel. 
        
2. Hagyja meg az alapértelmezett beállítást, majd kattintson a **tovább**gombra.  

1. A Web App for containers az alapértelmezett telepítési cél.  
    Az alkalmazás-keretrendszer, amelyet korábban választott, az itt elérhető Azure-szolgáltatások telepítési célját diktálja be.  Hagyja meg az alapértelmezett szolgáltatást, majd válassza a **tovább**lehetőséget.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

    a. Válassza ki a projekt nevét az Azure DevOps. 
    
    b. Válassza ki az Azure-előfizetést és-helyet, adjon meg egy nevet az alkalmazásnak, majd válassza a **kész**lehetőséget.   
        Néhány perc elteltével megjelenik a DevOps Projects irányítópult a Azure Portal. Egy minta alkalmazás van beállítva az Azure DevOps-szervezetben lévő adattárban, egy Build-futtatási és az alkalmazás üzembe helyezése az Azure-ban. Ez az irányítópult a tárházba, a CI/CD-folyamatba és az Azure-beli alkalmazásba is betekintést nyújt.  
        
2. A futó alkalmazás megtekintéséhez válassza a **Tallózás** lehetőséget.

    ![Irányítópult nézet](_img/azure-devops-project-php/dashboardnopreview.png) 
    
   DevOps Projects automatikusan konfigurálta a CI-Build és a kiadási triggert.  Most már készen áll, hogy egy csapattal közösen dolgozzon a PHP-alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

 A DevOps Projects egy git-tárházat hoz létre az Azure Reposben vagy a GitHubban. Az adattár megtekintéséhez és a kód módosításának végrehajtásához hajtsa végre az alábbi lépéseket:

1. A DevOps Projects irányítópult bal oldalán válassza ki a fő ág hivatkozását.   
    Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. Az adattárklón URL-címének megtekintéséhez válassza a **Klónozás** lehetőséget a böngésző jobb felső részén.   
    A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe. A következő néhány lépésben a webböngésző használatával hajtsa végre és véglegesítse a kód módosításait közvetlenül a fő ágra.

1. A bal oldalon nyissa meg az **erőforrások/nézetek/üdvözlő. panel. php** fájlt.

1. Válassza a **Szerkesztés**lehetőséget, majd végezze el a módosítást egy bizonyos szövegre.  Módosítsa például az egyik div címkén belüli szöveget.

1. Válassza ki **véglegesítése**, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Projects irányítópultot.  
Ekkor megjelenik egy Build folyamatban. Az imént létrehozott módosításokat a rendszer automatikusan felépíti és telepíti a CI/CD-folyamaton keresztül.

## <a name="examine-the-cicd-pipeline"></a>Vizsgálja meg a CI/CD-folyamat

 DevOps Projects automatikusan beállítja a teljes CI/CD-folyamatot az Azure-folyamatokban. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A build és a Release folyamat megismeréséhez tegye a következőket:

1. Válassza ki a DevOps Projects irányítópult tetején lévő **hozhat létre folyamatokat**.  
    Ez a hivatkozás egy böngésző fület és az új projekthez tartozó Build folyamatot nyitja meg.

1. Mutasson az **állapot** mezőre, majd válassza a **három** pontot (...).  
    A menü több lehetőséget is megjelenít, például az új buildek kiépítését, a Build szüneteltetését és a build folyamat szerkesztését.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A Build számos feladatot futtat, például a források beolvasását a git-tárházból, a függőségek visszaállítását és az üzembe helyezéshez használt kimenetek közzétételét.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a felépítési folyamat nevét egy ennél részletesebb leírásra, válassza ki, **mentse & várólistát**, majd kattintson a **Mentés**gombra.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
    Az **Előzmények** ablaktábla a Build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Azure folyamatok nyomon követi, hogy az összeállítási folyamat végzett módosításokat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**.  
      A DevOps Projects automatikusan létrejön egy CI eseményindító, és a tárházban minden véglegesítéshez elindul egy új létrehozást. Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.   
    A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások**lehetőséget.  
     DevOps Projects létrehoz egy kiadási folyamatot az Azure-ba történő üzembe helyezések kezeléséhez.

1. Válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.  
    A kiadási folyamathoz egy folyamatot, amely meghatározza a kibocsátási folyamat tartalmazza. 

12. Az **Összetevők** alatt válassza az **Elvetést**.  
    Az előző lépések során megvizsgált összeállítási folyamat létrehozza az összetevőhöz használt kimenetet. 

1. Mellett a **Drop** ikonra, válassza ki a **a folyamatos készregyártás eseményindítója**.   
    Ez a kiadási folyamat egy engedélyezett CD-triggerrel rendelkezik, amely minden alkalommal futtat egy központi telepítést, amikor új Build-összetevő áll rendelkezésre.  Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. A bal oldalon válassza ki a **feladatok**.  
        A feladatok olyan tevékenységek, amely végrehajtja a központi telepítési folyamat.  Ebben a példában egy feladatot hoztak létre a Azure App Serviceba való üzembe helyezéshez.

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget a kiadások előzményeinek megjelenítéséhez.

1. Válassza ki az egyik kiadás melletti három pontot (...), majd kattintson a **Megnyitás**gombra.  
        Ebben a nézetben több menüt is megtekinthet, például a kiadás összegzését, a társított munkaelemeket vagy a teszteket.

1. Válassza a **Véglegesítéseket**.  
        Ez a nézet megjeleníti az adott központi telepítési társított kód véglegesítéseket. 

1. Válassza a **Naplókat**.  
        A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, törölheti Azure App Service és az egyéb kapcsolódó erőforrásokat is. Használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések

A CI/CD-folyamat beállításakor a rendszer automatikusan létrehozta a létrehozási és kiadási folyamatokat. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
