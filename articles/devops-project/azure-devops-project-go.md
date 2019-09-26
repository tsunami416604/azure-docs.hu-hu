---
title: 'Gyors útmutató: CI/CD-folyamat létrehozása a go programozási nyelvhez Azure DevOps Projects használatával'
description: DevOps Projects megkönnyíti az Azure megkezdését. Segítségével néhány gyors lépéssel elindíthatja a go programozási nyelv webalkalmazását egy Azure-szolgáltatásban.
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
ms.openlocfilehash: f7429a6de05a301b579354d722ad354b78ce6cbf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899678"
---
# <a name="create-a-cicd-pipeline-for-go-by-using-azure-devops-projects"></a>CI/CD-folyamat létrehozása a Go-hoz a Azure DevOps Projects használatával

A folyamatos integráció (CI) és a folyamatos kézbesítés (CD) konfigurálása a go-alkalmazáshoz Azure DevOps Projects használatával. DevOps Projects leegyszerűsíti az Azure DevOps-létrehozási és-kiadási folyamatának kezdeti konfigurációját.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **erőforrás létrehozása**lehetőséget.

1. A keresőmezőbe írja be a **DevOps Project**kifejezést, majd válassza a **Létrehozás**lehetőséget.

    ![A DevOps Projects irányítópult](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Válasszon egy minta alkalmazást és egy Azure-szolgáltatást

1. Válassza ki a **Go** minta alkalmazást, majd kattintson a **tovább**gombra.  
    
1. Az alapértelmezett keretrendszer az **Egyszerű Go-alkalmazás**. Kattintson a **Tovább** gombra.  
    Az alkalmazás-keretrendszer, amelyet korábban választott, megdiktálja az üzembe helyezéshez elérhető Azure-szolgáltatások telepítési céljainak típusát. 
    
1. Hagyja meg az alapértelmezett Azure-szolgáltatást, és kattintson a **Tovább gombra**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása 

1. Hozzon létre egy új ingyenes Azure DevOps szervezetet, vagy válasszon egy már meglévő szervezet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetést és-helyet, adjon meg egy nevet az alkalmazásnak, majd válassza a **kész**lehetőséget.  
    Néhány perc múlva a DevOps Projects irányítópult jelenik meg az Azure Portalon. Egy minta alkalmazás van beállítva az Azure DevOps-szervezetben lévő tárházban, a rendszer létrehoz egy buildet, és az alkalmazást üzembe helyezi az Azure-ban. 
    
    Az irányítópulton látható a kód tárháza, a CI/CD-folyamat és az Azure-beli alkalmazás. A jobb oldalon kattintson a **Tallózás** gombra a futó alkalmazás megtekintéséhez.

    ![Irányítópult nézet](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Véglegesítse a kód módosításait, és hajtsa végre a CI/CD-t

A DevOps Projects git-tárházat hoz létre az Azure Reposben vagy a GitHubban. A tárház megtekintéséhez és a kód módosításának végrehajtásához tegye a következőket:

1. A DevOps Projects bal oldalon válassza ki a fő ág hivatkozását.  
    A hivatkozás egy nézetet nyit meg az újonnan létrehozott git-tárházban.

1. A tárház klónozási URL-címének megtekintéséhez válassza a jobb felső sarokban található **klónozás** elemet.  
    A git-tárházat a kedvenc IDE-ben is klónozott. A következő néhány lépésben a webböngésző segítségével közvetlenül a főágban hajthat végre és véglegesíthet kódmódosításokat.

1. A bal oldalon nyissa meg a *views/index.html* fájlt, majd válassza a **Szerkesztés**lehetőséget.

1. Módosítsa a fájlt. Például módosítsa a div-címkék egyikének szövegét.

1. Válassza ki **véglegesítése**, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Projects irányítópultot.  
    A buildnek folyamatban kell lennie. A módosítások automatikusan gyártja és a CI/CD-folyamat segítségével telepítve.

## <a name="examine-the-cicd-pipeline"></a>Vizsgálja meg a CI/CD-folyamat

DevOps Projects automatikusan konfigurál egy teljes CI/CD-folyamatot az Azure Reposben. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Az Azure DevOps Build és Release folyamatának megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps Projects irányítópultot.

1. A felső részen válassza a **folyamatok létrehozása**lehetőséget.  
    Egy böngésző lap megjeleníti az új projekt összeállítási folyamatát.

1. Mutasson az **állapot** mezőre, majd válassza a három pontot (...).  
    A menüben számos lehetőség látható, például az új buildek várólistába helyezése, egy Build felfüggesztése és a build folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A build különböző feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ez az ablaktábla a Build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure DevOps nyomon követi a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítókat**.  
    A DevOps Projects automatikusan létrehoz egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el. Lehetőség van arra is, hogy az ágakat belefoglalja vagy kizárja a CI-folyamatból.

1. Válassza a **Megtartást**.  
    A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások**lehetőséget.  
    DevOps Projects létrehoz egy kiadási folyamatot az Azure-ba történő üzembe helyezések kezeléséhez.

1. Válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**.  
    A korábban megvizsgált felépítési folyamat az összetevőhöz használt kimenetet hozza létre. 

1. A **drop** ikon jobb oldalán válassza a **folyamatos üzembe helyezés trigger**lehetőséget.  
    Ez a kiadási folyamat egy engedélyezett CD-triggerrel rendelkezik, amely minden alkalommal végrehajt egy központi telepítést, amikor új Build-összetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. A bal oldalon válassza a **feladatok**lehetőséget.  
    A feladatok a telepítési folyamat által végrehajtott tevékenységek. Ebben a példában egy feladatot hoztak létre a Azure App Serviceba való üzembe helyezéshez.

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget a kiadások előzményeinek megjelenítéséhez.

1. Válassza a kiadás melletti három pontot (...), majd kattintson a **Megnyitás**gombra.  
    Több menüt is megvizsgálhat, például a kiadás összegzését, a kapcsolódó munkaelemeket és teszteket.

1. Válassza a **Véglegesítéseket**.  
    Ez a nézet a telepítéshez társított kód-véglegesítő kódokat jeleníti meg. 

1. Válassza a **Naplókat**.  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezeket az üzembe helyezések során és után is megtekintheti.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az Azure App Service példányt és az ebben a rövid útmutatóban létrehozott kapcsolódó erőforrásokat. Ehhez használja a **Törlés** funkciót a DevOps projects irányítópulton.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogy a build és a Release folyamat hogyan módosítható a csapat igényeinek megfelelően, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A többfázisú folyamatos üzembe helyezés (CD) folyamatának meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
