---
title: 'Oktatóanyag: A meglévő kód CI/CD-folyamat létrehozása az Azure DevOps Projects használatával'
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. Saját kód és a GitHub-adattár használatával az Azure-szolgáltatás néhány gyors lépéssel tetszőleges alkalmazást elindíthat az DevOps Projects segítségével.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 88ee15a3b5cc53542d9e098dee485b8a526bb9a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60555343"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Oktatóanyag: A meglévő kód CI/CD-folyamat létrehozása az Azure DevOps Projects használatával

Az Azure DevOps Projects megjelenít egy egyszerűsített felület, ahol a meglévő kódot és a Git-adattár, vagy válasszon egy mintaalkalmazást, hozzon létre egy folyamatos integrációs (CI) és a folyamatos továbbítás (CD) folyamatot az Azure-bA.

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * CI/CD-folyamatok létrehozására a DevOps Projects használata
> * A GitHub-adattárban való hozzáférés konfigurálása és keretrendszer kiválasztása
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * Változtatások véglegesítése a Githubban, és automatikusan telepíthet az Azure-bA
> * Vizsgálja meg az Azure folyamatok CI/CD-folyamat
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.
* A GitHub vagy a külső Git-adattár, amely tartalmazza a .NET, Java, PHP, Node, Python vagy statikus webes kód való hozzáférést.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az Azure DevOps Projects a CI/CD-folyamat Azure folyamatokat hoz létre. Hozzon létre egy új Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. Az Azure DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **új**.

1. A Keresés mezőbe írja be a **DevOps Projects**, majd válassza ki **létrehozás**.

    ![A DevOps Projects-irányítópult](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza ki **saját kód**, majd válassza ki **tovább**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>A GitHub-adattárban való hozzáférés konfigurálása és keretrendszer kiválasztása

1. Ezek közül bármelyikre **GitHub** vagy egy külső Git-adattárból, majd válassza ki a tárházban, és az ágat, amely tartalmazza az alkalmazás.

1. A webes keretrendszer, majd válassza ki és **tovább**.

    ![.NET-keretrendszer](_img/azure-devops-project-github/webframework.png)

    Az alkalmazás-keretrendszer, amely korábban kiválasztott szabja meg, amely innen érhető el az Azure szolgáltatás üzembe helyezési cél típusát. 
    
1. A célként megadott szolgáltatás, majd válassza ki és **tovább**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps szervezetet, vagy válasszon ki egy már meglévő szervezet.

    a. Az Azure DevOps adja meg a projekt nevét. 
    
    b. Válassza ki az Azure-előfizetést és helyet, adja meg az alkalmazás nevét, és válassza **kész**.

    Néhány perc múlva a DevOps Projects irányítópult jelenik meg az Azure Portalon. Egy mintaalkalmazás be van állítva, a szervezet Azure DevOps-tárházban, build hajtja végre, és az alkalmazás központi telepítése az Azure-bA. Ezt az irányítópultot biztosít a GitHub code tárházban, a CI/CD-folyamat és az alkalmazás az Azure-ban. 
    
1. Válassza ki **Tallózás** a futó alkalmazás megtekintéséhez.

    ![A DevOps Projects irányítópult-nézet](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Az Azure DevOps Projects automatikusan konfigurálja a CI-build és kiadás eseményindító. A kód a GitHub-adattárat, vagy egy másik külső tárházra marad. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Változtatások véglegesítése a Githubban, és automatikusan telepíthet az Azure-bA 

Most már készen áll, az alkalmazás a csapat együttműködhet egy CI/CD-folyamat, amely automatikusan telepíti a legújabb munkáját a webhely használatával. Minden módosítás a GitHub-adattárat a build elindítja az Azure DevOps, és a egy CD-folyamat végrehajtja a központi telepítés az Azure-bA.

1. Módosítsa az alkalmazáshoz, és majd véglegesítse a módosítást, a GitHub-adattárban.  
    Néhány pillanat múlva build Azure folyamatok indítja el. Figyelheti a build állapotát, a DevOps Projects irányítópultján, vagy nyomon követheti a böngészőben az Azure DevOps vállalattal.

1. A létrehozás befejezése után frissítse az alkalmazás ellenőrizheti a módosításokat.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Vizsgálja meg az Azure folyamatok CI/CD-folyamat

Az Azure DevOps Projects automatikusan konfigurálja a CI/CD-folyamat Azure folyamatokban. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Ismerkedjen meg a build és kiadás folyamatok, tegye a következőket:

1. Válassza ki a DevOps Projects irányítópult tetején lévő **folyamatok alakíthatók ki**.  
    Egy böngészőben lap megjeleníti a buildelési folyamat az új projekt.

1. Mutasson a **állapot** mezőben, majd válassza ki a három pontra (...).  
    Menü megjelenítése több beállítások, például az üzenetsor új build, készítsen felfüggesztetéssel és a buildelési folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A build különböző feladatokat, hajt végre, például beolvasásakor a Git-tárházban, a függőségek visszaállítását és közzétételi kimenete-forrásokat az központi telepítésére használatos.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ekkor megjelenik a build legutóbbi módosításainak naplója. Az Azure DevOps nyomon követi a végzett módosítások a buildelési folyamat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**.  
    Az Azure DevOps Projects automatikusan CI eseményindítót hoz létre, és minden véglegesítéshez az adattárhoz elindul egy új létrehozást. Igény szerint kiválaszthatja, hogy belefoglalja vagy kizárja az ágak a CI folyamat.

1. Válassza a **Megtartást**.  
        A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza ki **készítése és kiadása**, majd válassza ki **kiadásokban**.  
    Az Azure DevOps Projects létrehoz egy kiadási folyamatot, az Azure-bA központi telepítések felügyeletéhez szükséges.

1. A kibocsátási folyamat melletti három pontra (...), majd válassza ki és **szerkesztése**.  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot. 
    
1. Az **Összetevők** alatt válassza az **Elvetést**.  
    A buildelési folyamat, az előző lépésekben vizsgálni összetevő szolgálja ki a kimenetet eredményez. 

1. Mellett a **Drop** ikonra, válassza ki **a folyamatos készregyártás eseményindítója**.  
    Ez a kiadási folyamat egy olyan engedélyezett CD-eseményindítóval rendelkezik, amely minden alkalommal végrehajtja az üzembe helyezést, amikor elérhetővé válik egy új buildmunkadarab. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. Válassza a bal oldali **feladatok**.  
    Feladatok olyan, amely a telepítési folyamat végrehajtja a tevékenységek. Ebben a példában egy feladat létrehozása az Azure App Service-ben való üzembe helyezéséhez.

1. Válassza ki a jobb **verziók megtekintéséhez** kiadások előzményeinek megjelenítéséhez.

1. Egy kiadási melletti három pontra (...), majd válassza ki és **nyílt**.  
    Ismerje meg, például a kiadás összegzését, a társított munkaelemekhez és a tesztek a több menük találhatók meg.

1. Válassza a **Véglegesítéseket**.  
    Ez a nézet megjeleníti az ehhez a központi telepítéshez rendelt kódja véglegesítéseket. 

1. Válassza a **Naplókat**.  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket alatt és után a központi telepítések.

## <a name="configure-azure-application-insights-monitoring"></a>Az Azure Application Insights monitorozásának konfigurálása

Az Azure Application Insights segítségével egyszerűen monitorozhatja alkalmazása teljesítményét és használatát. Az Azure DevOps Projects automatikusan konfigurálja az Application Insights-erőforrás, az alkalmazás. Szükség szerint további riasztásokat és monitorozási lehetőségeket is konfigurálhat.

1. Az Azure Portalon nyissa meg a DevOps Projects-irányítópultot. 

1. A bal alsó részen válassza a **Application Insights** hivatkozást az alkalmazáshoz.  
    A **Application Insights** panel nyílik meg. Ebben a nézetben az alkalmazás használatára, teljesítményére és rendelkezésre állásának monitorozására vonatkozó információk találhatók.

    ![Az Application Insights panel](_img/azure-devops-project-github/appinsights.png) 

1. Válassza ki **időtartomány**, majd válassza ki **utolsó óra**. Az eredmények szűréséhez válassza **frissítés**.  
    Most már megtekintheti az összes tevékenységet az elmúlt 60 perc. Az időtartomány megjelenítéséből **x**.

1. Válassza ki **riasztások**, majd válassza ki **metrikariasztás hozzáadása**. 

1. Adjon meg egy nevet a riasztáshoz.

1. Az a **forrás az Alter** legördülő listában válassza a **App Service-erőforrást.** <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. Az a **metrika** legördülő listában, vizsgálja meg a különböző riasztási metrikákat.  
    Az alapértelmezett riasztás az **1 másodpercnél hosszabb kiszolgáló-válaszidőre** vonatkozik. Egyszerűen konfigurálhat különböző riasztásokat az alkalmazás monitorozási képességeinek fejlesztéséhez.

1. Válassza ki a **értesítendő e-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak keresztül** jelölőnégyzetet.  
    Szükség esetén további műveleteket is végrehajthat, ha egy riasztás jelenik meg úgy, hogy végrehajtja az Azure logic app.

1. Válassza ki **OK** a riasztás létrehozásához.  
    Néhány pillanat múlva a riasztás az irányítópulton aktívként jelenik meg.
    
1. Kilépés a **riasztások** területet, és térjen vissza a **Application Insights** ablaktáblán.

1. Válassza ki **rendelkezésre állási**, majd válassza ki **Hozzáadás teszt**. 

1. Adja meg a teszt nevét, és válassza ki **létrehozás**.  
    Létrejön egy egyszerű ping-teszt az alkalmazás rendelkezésre állásának ellenőrzéséhez. Néhány perc elteltével elérhetővé válnak a teszteredmények, és az Application Insights-irányítópulton megjelenik a rendelkezésre állás állapota.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, törölheti az Azure App Service-ben és a kapcsolódó erőforrásokat, ebben az oktatóanyagban létrehozott. Ehhez használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban konfigurálásakor a CI/CD-folyamat az Azure DevOps Projects automatikusan létrehozott egy build és kiadás folyamatot. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * CI/CD-folyamatok létrehozására a DevOps Projects használata
> * A GitHub-adattárban való hozzáférés konfigurálása és keretrendszer kiválasztása
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * Változtatások véglegesítése a Githubban, és automatikusan telepíthet az Azure-bA
> * Vizsgálja meg az Azure folyamatok CI/CD-folyamat
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

A CI/CD-folyamat kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [A többfázisú folyamatos készregyártás (CD) folyamat meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
