---
title: 'Gyors útmutató: A CI/CD-folyamat létrehozása a php-hez az Azure DevOps Projects segítségével'
description: A DevOps Projects megkönnyíti az Azure használatának első lépéseit. A segítségével néhány gyors lépésben elindíthat egy alkalmazást a választott Azure-szolgáltatásban.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 1b1faa8b90476350975b80c4701618ccf4cde680
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165356"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-projects"></a>A CI/CD-folyamat létrehozása a php-hez az Azure DevOps Projects segítségével

Az Azure DevOps Projects megjelenít egy egyszerűsített felület, amely az Azure-erőforrásokat hoz létre és egy folyamatos integrációs (CI) és a folyamatos továbbítás (CD) folyamatot a PHP-alkalmazás Azure folyamatokban.  

Ha nem rendelkezik Azure-előfizetéssel, beszerezheti az egyik ingyenes keresztül [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

 A DevOps Projects a CI/CD-folyamat Azure folyamatokat hoz létre. Hozzon létre egy új ingyenes Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. A DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása** ikonra, majd ezután keressen **DevOps Projects**.  

3. Kattintson a **Létrehozás** gombra.

    ![A folyamatos teljesítés konfigurálása indítása](_img/azure-devops-project-php/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza ki a minta PHP-alkalmazást.  
        A PHP-minták például több alkalmazás-keretrendszerek közül választhat. Az alapértelmezett minta keretrendszer Laravel. 
        
2. Ne módosítsa az alapértelmezett beállítást, és válassza ki **tovább**.  

1. Webes alkalmazás a tárolók az alapértelmezett telepítési cél.  
    Az alkalmazás-keretrendszer, amely korábban kiválasztott szabja meg, amely innen érhető el az Azure szolgáltatás üzembe helyezési cél típusát.  Hagyja az alapértelmezett szolgáltatást, és válassza ki **tovább**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps szervezetet, vagy válasszon ki egy már meglévő szervezet. 

    a. Az Azure DevOps, válassza ki a projekt nevét. 
    
    b. Válassza ki az Azure-előfizetést és helyet, adja meg az alkalmazás nevét, és válassza **kész**.   
        Néhány perc múlva a DevOps Projects irányítópult jelenik meg az Azure Portalon. Egy mintaalkalmazás be van állítva, a szervezet Azure DevOps-tárházban, a build fut, és az alkalmazás üzembe helyezése az Azure-bA. Ezt az irányítópultot a kódtárat a CI/CD-folyamat és az Azure-ban az alkalmazás rálátást biztosít.  
        
2. Válassza ki **Tallózás** a futó alkalmazás megtekintéséhez.

    ![Irányítópult nézet](_img/azure-devops-project-php/dashboardnopreview.png) 
    
 A DevOps Projects automatikusan konfigurálja a CI-build és kiadás eseményindító.  Most már készen áll, hogy egy csapattal közösen dolgozzon a PHP-alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

 DevOps-projektek Git-tárházat az Azure-Adattárakkal vagy GitHub hoz létre. A tárház megtekintheti, és módosítsa a kódokat az alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1. Az irányítópult bal oldalán, a DevOps Projects válassza a master ággal mutató hivatkozás.   
    Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. Az adattárklón URL-címének megtekintéséhez válassza a **Klónozás** lehetőséget a böngésző jobb felső részén.   
    A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe. A következő néhány lépést, a webböngésző segítségével győződjön meg arról, és a véglegesítés kódot közvetlenül a főágban módosításait.

1. Lépjen a bal oldalon a **resources/views/welcome.blade.php** fájlt.

1. Válassza ki **szerkesztése**, majd módosítsa annak a keresett és.  Módosítsa például az egyik div címkén belüli szöveget.

1. Válassza ki **véglegesítése**, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Projects-irányítópultot.  
Meg kell jelennie egy build folyamatban van. Az imént végrehajtott módosítások automatikusan gyártja és a CI/CD-folyamat segítségével telepítve.

## <a name="examine-the-cicd-pipeline"></a>Vizsgálja meg a CI/CD-folyamat

 A DevOps Projects egy teljes CI/CD-folyamat automatikusan konfigurálja az Azure-folyamatok. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Ismerkedjen meg a build és kiadás folyamatok, tegye a következőket:

1. Válassza ki a DevOps Projects irányítópult tetején lévő **hozhat létre folyamatokat**.  
    Ez a hivatkozás megnyílik egy böngészőlap, és a buildelési folyamat az új projekt.

1. Mutasson a **állapot** mezőben, majd válassza ki a **három** (...).  
    Egy menü több beállítások, például az üzenetsor-kezelési egy új build, készítsen szüneteltetése és szerkesztését a buildelési folyamat jeleníti meg.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A build futtatja a feladatokat, például a Git-adattárból adatforrások beolvasása számos, a függőségek visszaállítását és közzétételi kiírja a telepítéshez használt.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, select, **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
    A **előzmények** ablaktábla megjeleníti a legutóbbi módosítások build auditnaplót. Azure folyamatok nyomon követi, hogy az összeállítási folyamat végzett módosításokat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**.  
      A DevOps Projects automatikusan létrejön egy CI eseményindító, és a tárházban minden véglegesítéshez elindul egy új létrehozást. Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.   
    A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza ki **készítése és kiadása**, majd válassza ki **kiadásokban**.  
     DevOps-projektek az Azure-bA központi telepítések felügyeletéhez szükséges kiadási folyamatot hoz létre.

1. A kibocsátási folyamat melletti három pontra (...), majd válassza ki és **szerkesztése**.  
    A kiadási folyamathoz egy folyamatot, amely meghatározza a kibocsátási folyamat tartalmazza. 

12. Az **Összetevők** alatt válassza az **Elvetést**.  
    A buildelési folyamat, az előző lépésekben vizsgálni összetevő szolgálja ki a kimenetet eredményez. 

1. Mellett a **Drop** ikonra, válassza ki a **a folyamatos készregyártás eseményindítója**.   
    A kibocsátási folyamat rendelkezik egy engedélyezett CD eseményindító, amely futtatja a központi telepítés minden alkalommal, amikor egy új buildösszetevő áll rendelkezésre.  Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. A bal oldalon válassza ki a **feladatok**.  
        A feladatok olyan tevékenységek, amely végrehajtja a központi telepítési folyamat.  Ebben a példában egy feladat létrehozása az Azure App Service üzembe helyezéséhez.

1. A jobb oldalon válassza ki a **verziók megtekintéséhez** kiadások előzményeinek megjelenítéséhez.

1. A kiadások egyik melletti három pontra (...), majd válassza ki és **nyílt**.  
        Ebben a nézetben több menüt is megtekinthet, például a kiadás összegzését, a társított munkaelemeket vagy a teszteket.

1. Válassza a **Véglegesítéseket**.  
        Ez a nézet megjeleníti az adott központi telepítési társított kód véglegesítéseket. 

1. Válassza a **Naplókat**.  
        A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha többé már nincs szükség rájuk törölheti az Azure App Service és a többi kapcsolódó erőforrást. Használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések

Ha konfigurálta a CI/CD-folyamat, build, és automatikusan létrehozott folyamatok kiadását. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. A CI/CD-folyamat kapcsolatos további információkért tekintse meg az oktatóanyag:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
