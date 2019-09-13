---
title: 'Gyors útmutató: CI/CD-folyamat létrehozása a Pythonhoz Azure DevOps Projects'
description: A DevOps Projects megkönnyíti az Azure megkezdését. A segítségével néhány gyors lépésben elindíthat egy alkalmazást a választott Azure-szolgáltatásban.
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
ms.openlocfilehash: cb4e27aeada4f1b5f9e06279382327dcc51cc7dc
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899604"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-projects"></a>CI/CD-folyamat létrehozása a Pythonhoz Azure DevOps Projects

A Azure DevOps Projects egy olyan egyszerűsített élményt nyújt, amely Azure-erőforrásokat hoz létre, és beállítja a Python-alkalmazás folyamatos integrációs (CI) és folyamatos továbbítási (CD) folyamatát.  

Ha még nem rendelkezik Azure-előfizetéssel, a [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)használatával ingyenesen beszerezhet egyet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

 A DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban.  Létrehozhat egy ingyenes új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

2. Válassza az **erőforrás létrehozása** ikont a bal oldali ablaktáblán, majd keressen rá **DevOps projects**.  

3. Kattintson a **Létrehozás** gombra.

    ![Folyamatos kézbesítés konfigurálásának indítása](_img/azure-devops-project-python/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza ki a Python-minta alkalmazást. A Python-minták esetén több alkalmazási keretrendszer közül választhat.

1. Az alapértelmezett minta-keretrendszer a Django. Hagyja meg az alapértelmezett beállítást, majd kattintson a **tovább**gombra.    
A Web App for containers az alapértelmezett telepítési cél. Az alkalmazás-keretrendszer, amelyet korábban választott, az itt elérhető Azure-szolgáltatás telepítési célhelyét diktálja. 

3. Hagyja meg az alapértelmezett szolgáltatást, majd válassza a **tovább**lehetőséget.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon egy meglévő szervezetet. 

    a. Adja meg a projekt nevét az Azure DevOps.  

    b. Válassza ki az Azure-előfizetést és-helyet, adjon meg egy nevet az alkalmazásnak, majd válassza a **kész**lehetőséget.  
     Néhány perc elteltével a projekt irányítópultja megjelenik a Azure Portal. Egy minta alkalmazás az Azure DevOps-szervezet egy tárházában van beállítva, a rendszer létrehoz egy buildet, és az alkalmazást üzembe helyezi az Azure-ban. Ez az irányítópult a tárházba, a CI/CD-folyamatba és az Azure-beli alkalmazásba is betekintést nyújt.  
    
2. A futó alkalmazás megtekintéséhez válassza a **Tallózás** lehetőséget.

    ![Irányítópult nézet](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   DevOps Projects automatikusan konfigurálja a CI-Build és a kiadási triggert. Most már készen áll arra, hogy együttműködik egy Python-alkalmazásban található csapattal egy CI/CD-folyamattal, amely automatikusan üzembe helyezi a legújabb munkát a webhelyén.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

 A DevOps Projects egy git-tárházat hoz létre az Azure Reposben vagy a GitHubban. Az adattár megtekintéséhez és a kód módosításának végrehajtásához tegye a következőket: 

1. A DevOps Projects irányítópult bal oldalán válassza ki a fő ág hivatkozását.  
        Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. Az adattárklón URL-címének megtekintéséhez válassza a **Klónozás** lehetőséget a böngésző jobb felső részén.   
A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe.  A következő néhány lépésben a webböngésző segítségével közvetlenül a főágban hajthat végre és véglegesíthet kódmódosításokat.

1. A bal oldalon lépjen a **app/templates/app/index.html** fájlra.

1. Válassza a **Szerkesztés** elemet, és módosítson a szöveg valamely részén. Módosítsa például az egyik div címkén belüli szöveget.

1. Válassza ki **véglegesítése**, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Projects irányítópultot.   
    Ekkor megjelenik egy Build folyamatban. Az imént létrehozott módosításokat a rendszer automatikusan felépíti és telepíti a CI/CD-folyamaton keresztül.

## <a name="examine-the-cicd-pipeline"></a>Vizsgálja meg a CI/CD-folyamat

Az előző lépésben DevOps Projects automatikusan konfigurálta a teljes CI/CD-folyamatot. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A build és a Release folyamat megismeréséhez tegye a következőket:

1. Válassza ki a DevOps Projects irányítópult tetején lévő **hozhat létre folyamatokat**.  
Egy böngésző lap megjeleníti az új projekt összeállítási folyamatát.

1. Mutasson az **állapot** mezőre, majd válassza a **három** pontot (...).  
        A menüben számos lehetőség látható, például az új buildek várólistába helyezése, egy Build felfüggesztése és a build folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
        A build különböző feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezések kimeneteit.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
        Ekkor megjelenik a build legutóbbi módosításainak naplója.  Az Azure DevOps nyomon követi a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítókat**.  
         A DevOps Projects automatikusan létrehoz egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.  
        A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások**lehetőséget.   
 DevOps Projects létrehoz egy kiadási folyamatot az Azure-ba történő üzembe helyezések kezeléséhez.

1. Válassza ki a kiadási folyamat melletti három pontot, majd válassza a **Szerkesztés**lehetőséget.  
A kiadási folyamat meghatározza a kiadási folyamatot.  
        
12. Az **Összetevők** alatt válassza az **Elvetést**.   
Az előző lépések során megvizsgált összeállítási folyamat létrehozza az összetevőhöz használt kimenetet. 

1. Mellett a **Drop** ikonra, válassza ki a **a folyamatos készregyártás eseményindítója**.  
        A kiadási folyamatnak van egy engedélyezett CD-triggere, amely minden alkalommal futtat egy központi telepítést, amikor új Build-összetevő áll rendelkezésre. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. A bal oldalon válassza ki a **feladatok**.   
A feladatok olyan tevékenységek, amely végrehajtja a központi telepítési folyamat. Ebben a példában egy feladatot hoztak létre a Azure App Serviceba való üzembe helyezéshez.

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget a kiadások előzményeinek megjelenítéséhez.  
        
1. A kiadások egyik melletti három pontra (...), majd válassza ki és **nyílt**.  
        Ez a nézet több menüt is kivizsgálhat, például a kiadás összegzését, a társított munkaelemeket és a teszteket.

1. Válassza a **Véglegesítéseket**. 
        Ebben a nézetben láthatók az adott központi telepítéshez társított kód véglegesítve. 

1. Válassza a **Naplókat**.   
A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezeket az üzembe helyezések során és után is megtekintheti.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, törölheti Azure App Service és kapcsolódó erőforrásokat. Használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések

A CI/CD-folyamat beállításakor a rendszer automatikusan létrehozta a létrehozási és kiadási folyamatokat. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
