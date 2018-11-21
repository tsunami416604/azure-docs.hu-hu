---
title: 'Rövid útmutató: A Go programnyelv CI/CD-folyamat létrehozása az Azure DevOps Projects használatával'
description: A DevOps Projects megkönnyíti az Azure használatának első lépéseit. Ez segít egy Go programozási nyelvet web Apps néhány gyors lépésben az Azure-szolgáltatások indítása.
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
ms.openlocfilehash: ca068b39c222a210e261234a132a0506f6b023bb
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263751"
---
# <a name="create-a-cicd-pipeline-for-go-by-using-azure-devops-projects"></a>A CI/CD-folyamat létrehozása a Góhoz készült Azure DevOps Projects használatával

Az Azure DevOps Projects segítségével konfigurálhatja a folyamatos integrációs (CI) és a Go-alkalmazás folyamatos készregyártás (CD). DevOps-projektek az Azure DevOps- és kiadáskészítő kezdeti konfigurálása leegyszerűsíti a folyamatot.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Projects a CI/CD-folyamat Azure folyamatokat hoz létre. Hozzon létre egy új Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. A DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása**.

1. A Keresés mezőbe írja be a **DevOps-projekt**, majd válassza ki **létrehozás**.

    ![A DevOps Projects-irányítópult](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Válasszon ki egy mintaalkalmazás és az Azure-szolgáltatás

1. Válassza ki a **Go** minta alkalmazását, és válassza ki **tovább**.  
    
1. Az alapértelmezett keretrendszer az **Egyszerű Go-alkalmazás**. Kattintson a **Tovább** gombra.  
    Az alkalmazás-keretrendszer, amely korábban kiválasztott szabja meg az Azure-szolgáltatás üzembe helyezési célok elérhető központi telepítés típusát. 
    
1. Hagyja meg az alapértelmezett Azure-szolgáltatás, és válassza ki **tovább**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása 

1. Hozzon létre egy új ingyenes Azure DevOps szervezetet, vagy válasszon egy már meglévő szervezet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetést és helyet, adja meg az alkalmazás nevét, és válassza **kész**.  
    Néhány perc múlva a DevOps Projects irányítópult jelenik meg az Azure Portalon. A szervezet Azure DevOps-tárház beállítása egy mintaalkalmazást, build hajtja végre, és az alkalmazás van üzembe helyezve az Azure-bA. 
    
    Az irányítópult biztosít a kód tárházban, a CI/CD-folyamat és az alkalmazás az Azure-ban. Válassza ki a jobb **Tallózás** a futó alkalmazás megtekintéséhez.

    ![Irányítópult nézet](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Mentse a kódot módosításokat, és hajtsa végre a CI/CD

A DevOps Projects Git-adattár az Azure-Adattárakkal vagy GitHub hoz létre. A tárház megtekintéséhez, és módosítsa a kódokat az alkalmazásban, tegye a következőket:

1. A DevOps Projects bal oldalán válassza a master ággal mutató hivatkozás.  
    A hivatkozás megnyitja az újonnan létrehozott Git-tárház egy nézetet.

1. A tárház clone URL-cím megtekintéséhez jelölje ki **Klónozás** jobb felső sarokban.  
    A Git-tárház klónozhat a kedvenc IDE-ben. A következő néhány lépésben a webböngésző segítségével közvetlenül a főágban hajthat végre és véglegesíthet kódmódosításokat.

1. A bal oldali, nyissa meg a *views/index.html* fájlt, és válassza ki **szerkesztése**.

1. Módosítsa a fájl. Például módosítsa a div címkék egyikében valamilyen szöveget.

1. Válassza ki **véglegesítése**, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Projects-irányítópultot.  
    Build folyamatban kell lennie. A módosítások automatikusan gyártja és a CI/CD-folyamat segítségével telepítve.

## <a name="examine-the-cicd-pipeline"></a>Vizsgálja meg a CI/CD-folyamat

A DevOps Projects egy teljes CI/CD-folyamat automatikusan konfigurálja az Azure-Adattárakkal. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Ismerkedjen meg az Azure DevOps-build és a folyamatok felszabadítása, tegye a következőket:

1. Nyissa meg a DevOps Projects-irányítópultot.

1. A képernyő felső részén válassza ki a **folyamatok alakíthatók ki**.  
    Egy böngészőben lap megjeleníti a buildelési folyamat az új projekt.

1. Mutasson a **állapot** mezőben, majd válassza ki a három pontra (...).  
    Menü megjelenítése több beállítások, például az üzenetsor új build, készítsen felfüggesztetéssel és a buildelési folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A build különböző feladatokat, hajt végre, például beolvasásakor a Git-tárházban, a függőségek visszaállítását és közzétételi kimenete-forrásokat az központi telepítésére használatos.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ezen a panelen a legutóbbi módosítások build auditnaplót jeleníti meg. Az Azure DevOps nyomon követi a végzett módosítások a buildelési folyamat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**.  
    A DevOps Projects automatikusan CI eseményindítót hoz létre, és minden véglegesítéshez az adattárhoz elindul egy új létrehozást. Igény szerint kiválaszthatja, hogy belefoglalja vagy kizárja az ágak a CI folyamat.

1. Válassza a **Megtartást**.  
    A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza ki **készítése és kiadása**, majd válassza ki **kiadásokban**.  
    DevOps-projektek az Azure-bA központi telepítések felügyeletéhez szükséges kiadási folyamatot hoz létre.

1. A kibocsátási folyamat melletti három pontra (...), majd válassza ki és **szerkesztése**.  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**.  
    A build vizsgálni, korábban hozza létre egyetlen folyamat a kimeneti összetevő szolgálja ki. 

1. Jobb oldalán a **Drop** ikonra, válassza ki **a folyamatos készregyártás eseményindítója**.  
    A kibocsátási folyamat rendelkezik egy engedélyezett CD eseményindító, amely végrehajtja a központi telepítés minden alkalommal, amikor egy új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. Válassza a bal oldali **feladatok**.  
    Feladatok tevékenységeit végzi az üzembe helyezési folyamat olyan. Ebben a példában egy feladat létrehozása az Azure App Service üzembe helyezéséhez.

1. Válassza ki a jobb **verziók megtekintéséhez** kiadások előzményeinek megjelenítéséhez.

1. Egy kiadási melletti három pontra (...), majd válassza ki és **nyílt**.  
    Áttekintheti a több menük, például a kiadás összegzését, a társított munkaelemekhez és a teszteket.

1. Válassza a **Véglegesítéseket**.  
    Ez a nézet megjeleníti az ehhez a központi telepítéshez rendelt kódja véglegesítéseket. 

1. Válassza a **Naplókat**.  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket alatt és után a központi telepítések.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, törölheti az Azure App Service-példány és a kapcsolódó ebben a rövid útmutatóban létrehozott erőforrásokat. Ehhez használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések

További információ a build módosítása és a csapat igényeinek folyamatok felszabadítása::

> [!div class="nextstepaction"]
> [A többfázisú folyamatos készregyártás (CD) folyamat meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
