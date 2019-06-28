---
title: 'Gyors útmutató: A CI/CD-folyamat létrehozása a .NET-hez az Azure DevOps Projects segítségével'
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. A segítségével néhány gyors lépéssel elindíthat egy .NET-alkalmazást a választott Azure-szolgáltatásban.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
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
ms.openlocfilehash: 387801f2ecb2f5fa1639005726218efb54d75dc8
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331424"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>A CI/CD-folyamat létrehozása a .NET-hez az Azure DevOps Projects segítségével

Folyamatos integrációs (CI) és a folyamatos készregyártás (CD) a .NET core- vagy ASP.NET-alkalmazás konfigurálása az DevOps Projects segítségével. DevOps-projektek egyszerűbbé teszi a kezdeti konfigurálása az Azure-folyamatok készítése és kiadása folyamat.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

DevOps-projektek az Azure DevOps hoz létre a CI/CD-folyamat. Hozzon létre egy új Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. A DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása** ikonra a bal oldali navigációs sávon, és keressen a **DevOps Projects**.  

3.  Kattintson a **Létrehozás** gombra.

    ![Folyamatos kézbesítés indítása](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza a **.NET** mintaalkalmazást. A .NET-minták esetén a nyílt forráskódú ASP.NET- vagy a platformfüggetlen .NET Core-keretrendszer közül választhat.

   ![.NET-keretrendszer](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

2. Ez a minta egy ASP.NET Core MVC-alkalmazás. Válassza ki a **.NET Core** alkalmazás-keretrendszert, majd válassza ki **tovább**.    
    
3. Válassza ki **Windows webalkalmazás** központi telepítés céljaként, majd válassza ki **tovább**. Szükség esetén választhat más Azure-szolgáltatások az üzembe helyezéshez. Az alkalmazás-keretrendszer, amely korábban kiválasztott itt előírja a típusú Azure-szolgáltatás központi telepítési cél érhető el.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása 

1. Adjon meg egy **projektnév**.

2. Hozzon létre egy új szabad **Azure DevOps-szervezet** , vagy válasszon egy már meglévő szervezet a legördülő listából.

3. Válassza ki a **Azure-előfizetés**, adjon meg egy nevet a **webalkalmazás** vagy végezze el a az alapértelmezett, majd válassza ki **kész**. Néhány perc múlva a DevOps Projects üzembe helyezésének áttekintése az Azure-portálon jelenik meg. 

4. Válassza ki **erőforrás megnyitása** a DevOps-projekt-irányítópult megtekintéséhez. A jobb felső sarokban a rögzítés a **projekt** lehet gyorsan hozzáférni az irányítópulthoz. Egy mintaalkalmazás beállítása az adattár a **Azure DevOps-szervezet**. Build hajtja végre, és az alkalmazás van üzembe helyezve az Azure-bA.

5. Az irányítópult biztosít a kód tárházban, a CI/CD-folyamat és az alkalmazás az Azure-ban. Válassza ki a Azure-erőforrások jobb **Tallózás** a futó alkalmazás megtekintéséhez.

   ![Irányítópult nézet](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Projects egy Git-tárház létrehozott Azure-kódtárak és a GitHub. A tárház megtekintéséhez, és módosítsa a kódokat az alkalmazáshoz, tegye a következőket:

1. Az irányítópult bal oldalán, a DevOps Projects, jelölje be a hivatkozást a **fő** ágat. Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

2. A következő néhány lépést, használhatja a webböngészőt, és kód változtatások véglegesítése a közvetlenül a **fő** ágat. Klónozhatja is a Git-tárház a kedvenc IDE-ben kiválasztásával **Klónozás** a felső az adattár oldalát, jobb. 

3. A bal oldalon keresse meg az alkalmazás fájlstruktúra a **Application/aspnet-core-dotnet-core/Pages/Index.cshtml**.

4. Válassza ki **szerkesztése**, és ezután hajtson végre módosítást a h2 fejléc. Írja be például **azonnal Ismerkedés az Azure DevOps Projects** vagy valamilyen más módosítást.

      ![Kódszerkesztések](_img/azure-devops-project-aspnet-core/codechange.png)

5. Válassza ki **véglegesítése**, hagyjon megjegyzést, és válassza ki **véglegesítése** újra.

6. A böngészőben nyissa meg az Azure DevOps Project irányítópultot.  Látni fogja, hogy folyamatban van egy build. A módosítások automatikusan gyártja és a CI/CD-folyamat segítségével telepítve.

## <a name="examine-the-cicd-pipeline"></a>Vizsgálja meg a CI/CD-folyamat

Az előző lépésben az Azure DevOps Projects automatikusan konfigurálva egy teljes CI/CD-folyamat. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A következő lépésekkel Ismerkedjen meg az Azure DevOps-buildelési és kiadási folyamatok.

1. Válassza ki a DevOps Projects irányítópult tetején lévő **hozhat létre folyamatokat**.  
Ez a hivatkozás megnyílik egy böngészőlap, és az Azure DevOps folyamat az új projekt létrehozása.

1. Kattintson a három pontra (...).  Ez a művelet egy menüt, ahol elkezdheti több tevékenységet, például az üzenetsor-kezelési egy új build, készítsen szüneteltetése és szerkesztését a buildelési folyamat megnyitja.

1. Válassza a **Szerkesztés** elemet.

    ![Buildelési folyamat](_img/azure-devops-project-aspnet-core/builddef.png)

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
 A build hajt végre különböző feladatokat, például a központi telepítésekhez használt források a Git-adattárból, függőségek visszaállítását és kimenetek használt közzétételi beolvasása.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
Az a **előzmények** panelen láthatja, hogy az útmutató legutóbbi módosításait build auditnapló.  Azure folyamatok nyomon követi, hogy az összeállítási folyamat végzett módosításokat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**.  
A DevOps Projects automatikusan létrejön egy CI eseményindító, és a tárházban minden véglegesítéshez elindul egy új létrehozást.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.  
A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza ki **készítése és kiadása**, majd **kiadásokban**.  
DevOps-projektek az Azure-bA központi telepítések felügyeletéhez szükséges kiadási folyamatot hoz létre.

1.  A bal oldalon válassza ki a kibocsátási folyamat melletti három pontra (...), majd **szerkesztése**.  
A kiadási folyamathoz egy folyamatot, amely meghatározza a kibocsátási folyamat tartalmazza.  

1. Az **Összetevők** alatt válassza az **Elvetést**.  Az előző lépésekben megvizsgált buildelési folyamat létrehozza a munkadarabhoz használt kimenetet. 

1. Mellett a **Drop** ikonra, válassza ki a **a folyamatos készregyártás eseményindítója**.  
A kibocsátási folyamat rendelkezik egy engedélyezett CD eseményindító, amely futtatja a központi telepítés minden alkalommal, amikor egy új buildösszetevő áll rendelkezésre. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását.  

1. A bal oldalon válassza ki a **feladatok**.   
A feladatok olyan tevékenységek, amely végrehajtja a központi telepítési folyamat. Ebben a példában egy feladat létrehozása az Azure App Service üzembe helyezéséhez.

1. A jobb oldalon válassza ki a **verziók megtekintéséhez**. Ebben a nézetben a kiadások előzményei jelennek meg.

1. A kiadások egyik melletti három pontra (...), majd válassza ki és **nyílt**.  
Ismerje meg, például a kiadás összegzését, a társított munkaelemekhez és a tesztek a több menük találhatók meg.


1. Válassza a **Véglegesítéseket**.   
Ez a nézet megjeleníti az adott központi telepítési társított kód véglegesítéseket. 

1. Válassza a **Naplókat**.  
A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure App Service és a többi kapcsolódó erőforrást, ha többé már nincs szükség rájuk létrehozott törölheti. Használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések

Ha arra vonatkozó információkra van szüksége, hogyan lehet módosítani a buildet és a kiadási folyamatokat a csapat igényei szerint, tekintse át az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videók

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
