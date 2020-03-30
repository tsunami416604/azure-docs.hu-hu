---
title: 'Rövid útmutató: CI/CD-folyamat létrehozása a .NET számára az Azure DevOps-projektekkel'
description: Az Azure DevOps-projektek megkönnyítik az azure-ral való ismerkedést. A segítségével néhány gyors lépéssel elindíthat egy .NET-alkalmazást a választott Azure-szolgáltatásban.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "70898019"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>CI/CD-folyamat létrehozása a .NET számára az Azure DevOps-projektekkel

Konfigurálja a folyamatos integrációt (CI) és a folyamatos kézbesítést (CD) a ASP.NET . DevOps-projektek leegyszerűsíti a kezdeti konfigurációegy build- és kiadási folyamat az Azure-folyamatokban.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps-projektek létrehoznak egy CI/CD-folyamatot az Azure DevOps-ban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrásokat az Azure-előfizetés az Ön által kiválasztott.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali ablaktáblában válassza az Erőforrás ikon **létrehozása lehetőséget** a bal oldali navigációs sávon, majd keresse meg a **DevOps-projektek et.**  

3.  Kattintson a **Létrehozás** gombra.

    ![Folyamatos kézbesítés indítása](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza a **.NET** mintaalkalmazást. A .NET-minták esetén a nyílt forráskódú ASP.NET- vagy a platformfüggetlen .NET Core-keretrendszer közül választhat.

   ![.NET-keretrendszer](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

2. Ez a minta egy ASP.NET Core MVC-alkalmazás. Válassza a **.NET Core** alkalmazáskeretrendszert, majd a **Tovább**gombot.    
    
3. Válassza a **Windows Web App** telepítési célként lehetőséget, majd a **Tovább**gombot. Szükség esetén más Azure-szolgáltatásokat is választhat a központi telepítéshez. Az alkalmazáskeretrendszer, amely korábban kiválasztott, meghatározza, hogy milyen típusú Az Azure-szolgáltatás üzembe helyezési cél érhető el itt.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és egy Azure-előfizetés konfigurálása 

1. Adja meg a **Projekt nevét**.

2. Hozzon létre egy új, ingyenes **Azure DevOps Organization,** vagy válasszon egy meglévő szervezet a legördülő menüből.

3. Válassza ki az **Azure-előfizetést,** adja meg a **webalkalmazás** nevét, vagy vegye be az alapértelmezett et, majd válassza a **Kész gombot.** Néhány perc múlva a DevOps-projektek üzembe helyezése áttekintése jelenik meg az Azure Portalon. 

4. Válassza az **Ugrás az erőforrásra** lehetőséget a DevOps-projekt irányítópultjának megtekintéséhez. A jobb felső sarokban rögzítse a **Projectet** az irányítópultra a gyors elérés érdekében. A mintaalkalmazás az **Azure DevOps Organization**tárházban van beállítva. A build végrehajtása, és az alkalmazás telepítve van az Azure-ban.

5. Az irányítópult betekintést nyújt a kódtárcsa, a CI/CD-folyamat és az azure-beli alkalmazás számára. Az Azure-erőforrások jobb oldalán válassza a **Tallózás** lehetőséget a futó alkalmazás megtekintéséhez.

   ![Irányítópult nézet](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps-projektek git-tárházat hoztak létre az Azure Reposban vagy a GitHubon. A tárház megtekintéséhez és az alkalmazás kódmódosításához tegye a következőket:

1. A DevOps-projektek irányítópultbal válassza ki a **főág** hivatkozását. Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

2. A következő néhány lépésben a webböngészősegítségével közvetlenül a **főágra** módosíthatja és véglegesítheti a kódmódosításokat. A Git-tárházat a kedvenc IDE-ben is klónozhatja, ha a tárház oldal jobb felső részén a **Klónozás** lehetőséget választja. 

3. A bal oldalon keresse meg az alkalmazásfájl szerkezetét az **Application/aspnet-core-dotnet-core/Pages/Index.cshtml címre.**

4. Válassza **a Szerkesztés**lehetőséget, majd módosítsa a h2 címsort. Írja be például az Első lépések **az Azure DevOps-projekteket,** vagy egyéb módosításokat.

      ![Kódszerkesztések](_img/azure-devops-project-aspnet-core/codechange.png)

5. Válassza **a Véglegesítés**lehetőséget, hagyjon megjegyzést, majd válassza újra **a Véglegesítés** lehetőséget.

6. A böngészőben nyissa meg az Azure DevOps Project irányítópultját.  Látni fogja, hogy folyamatban van egy build. A végrehajtott módosítások automatikusan létrejönnek és ci/CD-folyamaton keresztül települnek.

## <a name="examine-the-cicd-pipeline"></a>A CI/CD-folyamat vizsgálata

Az előző lépésben az Azure DevOps-projektek automatikusan konfigurálta a teljes CI/CD-folyamatot. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Az alábbi lépésekkel ismerkedjen meg az Azure DevOps buildelési és kiadási folyamataival.

1. A DevOps-projektek irányítópultjának tetején válassza a **Folyamatok összeállítása**lehetőséget.  
Ez a hivatkozás megnyit egy böngészőlapot, és az Azure DevOps build-folyamat az új projekthez.

1. Válassza ki a három pontot (...).  Ez a művelet megnyit egy menüt, ahol számos tevékenységet indíthat el, például egy új build sorba állása, a build szüneteltetése és a buildfolyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

    ![Buildelési folyamat](_img/azure-devops-project-aspnet-core/builddef.png)

1. Ezen az ablaktáblán megvizsgálhatja a buildfolyamat különböző feladatait.  
 A build különböző feladatokat hajt végre, például a Git-tárházból származó források lekérése, a függőségek visszaállítása és a központi telepítésekhez használt kimenetek közzététele.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a buildfolyamat nevét valami leíróbbra, válassza **a Mentés & várólistát,** majd a **Mentés lehetőséget.**

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
Az **Előzmények** ablaktáblán a build legutóbbi változásainak naplózási nyomvonala jelenik meg.  Az Azure Pipelines nyomon követi a buildelőfolyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza **az Eseményindítók lehetőséget.**  
DevOps-projektek automatikusan létrehozott egy CI-eseményindítót, és minden véglegesítést a tárház hoz egy új buildet.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.  
A forgatókönyvtől függően megadhatja, hogy bizonyos számú buildet megtartjon vagy eltávolítson.

1. Válassza **a Build and Release (Létrehozás és kiadás)** lehetőséget, majd a **Kiadások**lehetőséget.  
A DevOps-projektek létrehozegy kiadási folyamatot az Azure-ba történő központi telepítések kezeléséhez.

1.  A bal oldalon jelölje ki a kiadási folyamat melletti három pontot (...), majd kattintson a **Szerkesztés gombra.**  
A kiadási folyamat tartalmaz egy folyamatot, amely meghatározza a kiadási folyamatot.  

1. Az **Összetevők** alatt válassza az **Elvetést**.  Az előző lépésekben megvizsgált buildelési folyamat létrehozza a munkadarabhoz használt kimenetet. 

1. A **Legördülő** ikon mellett válassza a **Folyamatos üzembe helyezés eseményindítót**.  
Ez a kiadási folyamat rendelkezik egy engedélyezett CD-eseményindítóval, amely minden alkalommal futtat egy központi telepítést, amikor új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy az üzemelő példányok manuális végrehajtást igényelhessenek.  

1. A bal oldalon válassza a **Feladatok**lehetőséget.   
A feladatok azok a tevékenységek, amelyeket a központi telepítési folyamat hajt végre. Ebben a példában egy feladat jött létre az Azure App Service üzembe helyezéséhez.

1. A jobb oldalon válassza a **Kiadások megtekintése**lehetőséget. Ebben a nézetben a kiadások előzményei jelennek meg.

1. Jelölje ki a három pontot (...) az egyik kiadás mellett, majd kattintson a **Megnyitás gombra.**  
Számos menüt kell feltárnia, például a kiadások összegzését, a kapcsolódó munkaelemeket és a teszteket.


1. Válassza a **Véglegesítéseket**.   
Ebben a nézetben az adott központi telepítéshez társított kódvéglegesítések láthatók. 

1. Válassza **a Naplók lehetőséget.**  
A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti az Azure App Service-t és más kapcsolódó erőforrásokat, amelyeket akkor hozott létre, amikor már nincs rájuk szüksége. Használja a **Törlés** funkciót a DevOps-projektek irányítópulton.

## <a name="next-steps"></a>További lépések

Ha arra vonatkozó információkra van szüksége, hogyan lehet módosítani a buildet és a kiadási folyamatokat a csapat igényei szerint, tekintse át az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videók

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
