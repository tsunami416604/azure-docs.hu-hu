---
title: 'Oktatóanyag: CI/CD-folyamat létrehozása meglévő kódhoz a Azure DevOps Projects használatával'
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. A DevOps Projects segítségével a saját kód és a GitHub-tárház használatával néhány gyors lépéssel elindíthat egy alkalmazást egy tetszőleges Azure-szolgáltatásban.
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
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 2abe24ad65e1e8997b48a28b35ec0e65162022f2
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898009"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Oktatóanyag: CI/CD-folyamat létrehozása meglévő kódhoz a Azure DevOps Projects használatával

Azure DevOps Projects egy egyszerűsített felhasználói felülettel rendelkezik, ahol meglévő kód-és git-tárházat hozhat létre, vagy kiválaszthat egy minta alkalmazást, amellyel folyamatos integrációs (CI) és folyamatos átviteli (CD) folyamatokat hozhat létre az Azure-ba.

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * DevOps Projects használata CI/CD-folyamat létrehozásához
> * A GitHub-tárházhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban
> * Az Azure-folyamatok CI/CD-folyamatának vizsgálata
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.
* Hozzáférés egy olyan GitHubhoz vagy külső git-tárházhoz, amely .NET, Java, PHP, node, Python vagy statikus webes kódot tartalmaz.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A Azure DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A Azure DevOps Projects Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **új**lehetőséget.

1. A keresőmezőbe írja be a **DevOps projects**kifejezést, majd válassza a **Létrehozás**lehetőséget.

    ![A DevOps Projects irányítópult](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza **a saját kód**használata lehetőséget, majd kattintson a **tovább**gombra.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>A GitHub-tárházhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása

1. Válassza a **GitHub** vagy egy külső git-tárház lehetőséget, majd válassza ki a tárházat és az alkalmazást tartalmazó ágat.

1. Válassza ki a webes keretrendszert, majd kattintson a **tovább**gombra.

    ![.NET-keretrendszer](_img/azure-devops-project-github/webframework.png)

    Az alkalmazás-keretrendszer, amelyet korábban választott, az itt elérhető Azure-szolgáltatások telepítési célját diktálja be. 
    
1. Válassza ki a cél szolgáltatást, majd kattintson a **tovább**gombra.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet.

    a. Adja meg a projekt nevét az Azure DevOps. 
    
    b. Válassza ki az Azure-előfizetést és-helyet, adjon meg egy nevet az alkalmazásnak, majd válassza a **kész**lehetőséget.

    Néhány perc múlva a DevOps Projects irányítópult jelenik meg az Azure Portalon. Egy minta alkalmazás van beállítva az Azure DevOps-szervezetben lévő tárházban, a rendszer létrehoz egy buildet, és az alkalmazást üzembe helyezi az Azure-ban. Ez az irányítópult a GitHub-kód tárházában, a CI/CD-folyamatban, valamint az Azure-beli alkalmazásban nyújt láthatóságot. 
    
1. A futó alkalmazás megtekintéséhez válassza a **Tallózás** lehetőséget.

    ![DevOps Projects irányítópult nézet](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Projects automatikusan konfigurálja a CI-Build és a kiadási triggert. A kód a GitHub-tárházban vagy egy másik külső tárházban marad. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban 

Most már készen áll az alkalmazásban lévő csapattal való együttműködésre egy CI/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkát a webhelyén. A GitHub-tárház minden módosítása egy buildet indít az Azure DevOps, és egy CD-folyamat végrehajtja az Azure-ba történő üzembe helyezést.

1. Végezze el az alkalmazás módosítását, majd véglegesítse a módosítást a GitHub-tárházban.  
    Néhány pillanat elteltével a buildek az Azure-folyamatokban kezdődnek. A Build állapotát a DevOps Projects irányítópulton figyelheti, vagy megfigyelheti azt a böngészőben az Azure DevOps-szervezete használatával.

1. A Build befejezése után frissítse az alkalmazást a módosítások ellenőrzéséhez.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Az Azure-folyamatok CI/CD-folyamatának vizsgálata

Azure DevOps Projects automatikusan konfigurálja a CI/CD-folyamatokat az Azure-folyamatokban. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A build és a Release folyamat megismeréséhez tegye a következőket:

1. A DevOps Projects irányítópult tetején válassza a **folyamatok létrehozása**lehetőséget.  
    Egy böngésző lap megjeleníti az új projekt összeállítási folyamatát.

1. Mutasson az **állapot** mezőre, majd válassza a három pontot (...).  
    A menüben számos lehetőség látható, például az új buildek várólistába helyezése, egy Build felfüggesztése és a build folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A build különböző feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ekkor megjelenik a build legutóbbi módosításainak naplója. Az Azure DevOps nyomon követi a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítókat**.  
    A Azure DevOps Projects automatikusan létrehoz egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el. Lehetőség van arra is, hogy az ágakat belefoglalja vagy kizárja a CI-folyamatból.

1. Válassza a **Megtartást**.  
        A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások**lehetőséget.  
    Azure DevOps Projects létrehoz egy kiadási folyamatot az Azure-ba történő üzembe helyezések kezeléséhez.

1. Válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot. 
    
1. Az **Összetevők** alatt válassza az **Elvetést**.  
    Az előző lépések során megvizsgált összeállítási folyamat létrehozza az összetevőhöz használt kimenetet. 

1. A **drop** ikon mellett válassza a **folyamatos üzembe helyezési trigger**lehetőséget.  
    Ez a kiadási folyamat egy olyan engedélyezett CD-eseményindítóval rendelkezik, amely minden alkalommal végrehajtja az üzembe helyezést, amikor elérhetővé válik egy új buildmunkadarab. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. A bal oldalon válassza a **feladatok**lehetőséget.  
    A tevékenységek a telepítési folyamat által végrehajtandó tevékenységek. Ebben a példában egy feladatot hoztak létre az Azure app Service-ben való üzembe helyezéshez.

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget a kiadások előzményeinek megjelenítéséhez.

1. Válassza a kiadás melletti három pontot (...), majd kattintson a **Megnyitás**gombra.  
    Ismerje meg, például a kiadás összegzését, a társított munkaelemekhez és a tesztek a több menük találhatók meg.

1. Válassza a **Véglegesítéseket**.  
    Ez a nézet a telepítéshez társított kód-véglegesítő kódokat jeleníti meg. 

1. Válassza a **Naplókat**.  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezeket az üzembe helyezések során és után is megtekintheti.

## <a name="configure-azure-application-insights-monitoring"></a>Az Azure Application Insights monitorozásának konfigurálása

Az Azure Application Insights segítségével egyszerűen monitorozhatja alkalmazása teljesítményét és használatát. Azure DevOps Projects automatikusan konfigurál egy Application Insights erőforrást az alkalmazáshoz. Szükség szerint további riasztásokat és monitorozási lehetőségeket is konfigurálhat.

1. A Azure Portal nyissa meg a DevOps Projects irányítópultot. 

1. A jobb alsó sarokban válassza ki a **Application Insights** hivatkozást az alkalmazáshoz.  
    Megnyílik a **Application Insights** panel. Ebben a nézetben az alkalmazás használatára, teljesítményére és rendelkezésre állásának monitorozására vonatkozó információk találhatók.

    ![A Application Insights panel](_img/azure-devops-project-github/appinsights.png) 

1. Válasszaki az időtartomány elemet, majd válassza az **előző óra**lehetőséget. Az eredmények szűréséhez válassza a **frissítés**lehetőséget.  
    Mostantól az elmúlt 60 percben megtekintheti az összes tevékenységet. Az időtartományból való kilépéshez válassza az **x**elemet.

1. Válasszaa riasztások lehetőséget, majd kattintson a **metrika riasztás hozzáadása**lehetőségre. 

1. Adja meg a riasztás nevét.

1. A **forrás módosítása** legördülő listában válassza ki a **app Service erőforrást.** <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. A **metrika** legördülő listában vizsgálja meg a különböző riasztási metrikákat.  
    Az alapértelmezett riasztás az **1 másodpercnél hosszabb kiszolgáló-válaszidőre** vonatkozik. Egyszerűen konfigurálhat különböző riasztásokat az alkalmazás monitorozási képességeinek fejlesztéséhez.

1. Jelölje be az **értesítés e-mailben tulajdonosok, közreműködők és olvasók** számára jelölőnégyzetet.  
    Szükség esetén további műveleteket is végrehajthat, ha egy Azure logikai alkalmazás végrehajtásával riasztás jelenik meg.

1. A riasztás létrehozásához kattintson **az OK gombra** .  
    Néhány pillanat elteltével a riasztás aktívként jelenik meg az irányítópulton.
    
1. Lépjen ki a riasztások területről, és térjen vissza a **Application Insights** ablaktáblára.

1. Válassza a **rendelkezésre állás**lehetőséget, majd kattintson a **teszt hozzáadása**lehetőségre. 

1. Adja meg a teszt nevét, majd válassza a **Létrehozás**lehetőséget.  
    Létrejön egy egyszerű ping-teszt az alkalmazás rendelkezésre állásának ellenőrzéséhez. Néhány perc elteltével elérhetővé válnak a teszteredmények, és az Application Insights-irányítópulton megjelenik a rendelkezésre állás állapota.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az oktatóanyagban létrehozott Azure app Service-t és kapcsolódó erőforrásokat. Ehhez használja a **Törlés** funkciót a DevOps projects irányítópulton.

## <a name="next-steps"></a>További lépések

Ha az oktatóanyagban konfigurálta a CI/CD-folyamatot, a rendszer automatikusan létrehoz egy összeállítási és kiadási folyamatot Azure DevOps Projects. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * DevOps Projects használata CI/CD-folyamat létrehozásához
> * A GitHub-tárházhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban
> * Az Azure-folyamatok CI/CD-folyamatának vizsgálata
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A többfázisú folyamatos üzembe helyezés (CD) folyamatának meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
