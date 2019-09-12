---
title: 'Gyors útmutató: CI/CD-folyamat létrehozása a .NET-hez Azure DevOps Projects'
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. A segítségével néhány gyors lépéssel elindíthat egy .NET-alkalmazást a választott Azure-szolgáltatásban.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
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
ms.openlocfilehash: 7d2ccdfa1fe553d0795a82856dd255f4a54138bf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898019"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>CI/CD-folyamat létrehozása a .NET-hez Azure DevOps Projects

A folyamatos integráció (CI) és a folyamatos továbbítás (CD) konfigurálása a .NET Core-vagy ASP.NET-alkalmazáshoz DevOps Projects használatával. DevOps-projektek egyszerűbbé teszi a kezdeti konfigurálása az Azure-folyamatok készítése és kiadása folyamat.

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

2. Ez a minta egy ASP.NET Core MVC-alkalmazás. Válassza ki a **.net Core** alkalmazás-keretrendszert, majd kattintson a **tovább**gombra.    
    
3. Válassza a **Windows Web App** központi telepítési cél lehetőséget, majd kattintson a **tovább**gombra. Igény szerint további Azure-szolgáltatásokat is kiválaszthat a telepítéshez. Az alkalmazás-keretrendszer, amelyet korábban választott, megdiktálja az Azure-szolgáltatások telepítési céljának itt elérhető típusát.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása 

1. Adja meg a **projekt nevét**.

2. Hozzon létre egy új ingyenes **Azure DevOps-szervezetet** , vagy válasszon ki egy meglévő szervezetet a legördülő listából.

3. Válassza ki az **Azure-előfizetését**, adjon meg egy nevet a **webalkalmazásnak** , vagy használja az alapértelmezett értéket, majd válassza a **kész**lehetőséget. Néhány perc elteltével a DevOps Projects központi telepítésének áttekintése megjelenik a Azure Portalban. 

4. Válassza az **Ugrás az erőforráshoz** lehetőséget a DevOps-projekt irányítópultjának megtekintéséhez. A jobb felső sarokban rögzítse a **projektet** az irányítópulton a gyors hozzáférés érdekében. Egy minta alkalmazás van beállítva az **Azure DevOps-szervezetben**lévő tárházban. A rendszer végrehajtja a buildet, és az alkalmazást üzembe helyezi az Azure-ban.

5. Az irányítópulton látható a kód tárháza, a CI/CD-folyamat és az Azure-beli alkalmazás. Az Azure-erőforrások alatt kattintson a **Tallózás** gombra a futó alkalmazás megtekintéséhez.

   ![Irányítópult nézet](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Projects egy Git-tárház létrehozott Azure-kódtárak és a GitHub. Az adattár megtekintéséhez és a kód módosításának végrehajtásához tegye a következőket:

1. Az irányítópult bal oldalán, a DevOps Projects, jelölje be a hivatkozást a **fő** ágat. Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

2. A következő néhány lépésben a webböngészővel teheti meg és véglegesítheti a kód módosításait közvetlenül a **főágra.** A git-tárházat kedvenc IDE klónozásával is elvégezheti a tárház oldal jobb felső részén található **klónozás** lehetőség kiválasztásával. 

3. A bal oldalon navigáljon az alkalmazás-fájl struktúrában az **Application/ASPNET-Core-DotNet-Core/Pages/index. cshtml**elemre.

4. Válassza a **Szerkesztés**lehetőséget, majd módosítsa a H2-fejlécet. Írja be például a következőt: **első lépések azonnal a Azure DevOps projects** , vagy végezze el a módosítást.

      ![Kódszerkesztések](_img/azure-devops-project-aspnet-core/codechange.png)

5. Válassza a végrehajtás lehetőséget, hagyja meg a megjegyzést, **és válassza a** végrehajtás **újra lehetőséget.**

6. A böngészőben nyissa meg az Azure DevOps Project irányítópultot.  Látni fogja, hogy folyamatban van egy build. A módosítások automatikusan gyártja és a CI/CD-folyamat segítségével telepítve.

## <a name="examine-the-cicd-pipeline"></a>Vizsgálja meg a CI/CD-folyamat

Az előző lépésben Azure DevOps Projects automatikusan konfigurálta a teljes CI/CD-folyamatot. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A következő lépésekkel Ismerkedjen meg az Azure DevOps-buildelési és kiadási folyamatok.

1. Válassza ki a DevOps Projects irányítópult tetején lévő **hozhat létre folyamatokat**.  
Ez a hivatkozás megnyílik egy böngészőlap, és az Azure DevOps folyamat az új projekt létrehozása.

1. Válassza a három pontot (...).  Ez a művelet egy menüt nyit meg, ahol számos tevékenységet indíthat el, például egy új Build kiépítését, egy Build szüneteltetését és a build folyamat szerkesztését.

1. Válassza a **Szerkesztés** elemet.

    ![Buildelési folyamat](_img/azure-devops-project-aspnet-core/builddef.png)

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
 A build különböző feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
Az a **előzmények** panelen láthatja, hogy az útmutató legutóbbi módosításait build auditnapló.  Azure folyamatok nyomon követi, hogy az összeállítási folyamat végzett módosításokat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**.  
A DevOps Projects automatikusan létrejön egy CI eseményindító, és a tárházban minden véglegesítéshez elindul egy új létrehozást.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.  
A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások**lehetőséget.  
DevOps Projects létrehoz egy kiadási folyamatot az Azure-ba történő üzembe helyezések kezeléséhez.

1.  A bal oldalon válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.  
A kiadási folyamathoz egy folyamatot, amely meghatározza a kibocsátási folyamat tartalmazza.  

1. Az **Összetevők** alatt válassza az **Elvetést**.  Az előző lépésekben megvizsgált buildelési folyamat létrehozza a munkadarabhoz használt kimenetet. 

1. Mellett a **Drop** ikonra, válassza ki a **a folyamatos készregyártás eseményindítója**.  
A kibocsátási folyamat rendelkezik egy engedélyezett CD eseményindító, amely futtatja a központi telepítés minden alkalommal, amikor egy új buildösszetevő áll rendelkezésre. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását.  

1. A bal oldalon válassza ki a **feladatok**.   
A feladatok olyan tevékenységek, amely végrehajtja a központi telepítési folyamat. Ebben a példában egy feladatot hoztak létre a Azure App Serviceba való üzembe helyezéshez.

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
