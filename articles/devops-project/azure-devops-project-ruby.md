---
title: 'Rövid útmutató: CI/CD-folyamat létrehozása a Ruby on Rails számára az Azure DevOps-projektek használatával'
description: Az Azure DevOps-projektek megkönnyítik az azure-ral való ismerkedést. Néhány gyors lépésben elindíthategy Ruby-webalkalmazást egy Azure-szolgáltatásban.
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
ms.openlocfilehash: 5cb47cdd76d1de284c4dc6dbdfbfedd0095653ab
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "70899564"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-projects"></a>CI/CD-folyamat létrehozása a Ruby on Rails számára az Azure DevOps-projektek használatával

Konfigurálja a folyamatos integrációt (CI) és a folyamatos kézbesítést (CD) a Ruby on Rails alkalmazáshoz az Azure DevOps-projektek használatával. DevOps-projektek leegyszerűsíti az Azure DevOps buildelési és kiadási folyamat kezdeti konfigurációját.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az Azure DevOps-projektek létrehoz egy CI/CD-folyamatot az Azure Repos-ban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrásokat az Azure-előfizetés az Ön által kiválasztott.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet.

1. A keresőmezőbe írja be a **DevOps-projektek**kifejezést, majd válassza a **Létrehozás lehetőséget.**

    ![A DevOps-projektek irányítópultja](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Válasszon ki egy mintaalkalmazást és az Azure-szolgáltatást

1. Válassza ki a **Ruby** mintaalkalmazást.

1. Válassza a **Ruby on Rails** alkalmazás-keretrendszert. Ha elkészült, válassza a **Tovább gombot.**

1. Az alapértelmezett üzembehelyezési cél a **Web App on Linux**.  
    Szükség esetén kiválaszthatja **a Web App for Containers lehetőséget.** Az alkalmazáskeretrendszer, amely et korábban választott, meghatározza az Azure-szolgáltatás üzembe helyezési cél, amely itt érhető el. 
    
1. Válassza ki a kívánt célszolgáltatást, majd kattintson a **Tovább gombra.**

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és egy Azure-előfizetés konfigurálása 

1. Hozzon létre egy új, ingyenes Azure DevOps-szervezetet, vagy válasszon egy meglévő szervezetet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetést és a tartózkodási helyet, adja meg az alkalmazás nevét, majd válassza a **Kész gombot.**  
    Néhány perc múlva a DevOps-projektek irányítópultja megjelenik az Azure Portalon. A mintaalkalmazás be van állítva egy tárházban az Azure DevOps-szervezetben, egy build végrehajtásra kerül, és az alkalmazás telepítve van az Azure-ban. 
    
    Az irányítópult betekintést nyújt a kódtárcsa, a CI/CD-folyamat és az azure-beli alkalmazás számára. A jobb oldalon válassza a **Tallózás** gombot a futó alkalmazás megtekintéséhez.

    ![Irányítópult nézet](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>A kódmódosítások véglegesítése és a CI/CD végrehajtása

Az Azure DevOps-projektek létrehoz egy Git-tárházban az Azure-folyamatokban vagy a GitHubon. A tártár megtekintéséhez és az alkalmazás kódmódosításához tegye a következőket:

1. A DevOps-projektek irányítópulton a bal oldalon válassza ki a főág hivatkozását.  
    A hivatkozás megnyitja a nézetet az újonnan létrehozott Git-tárműtár.

1. A tárnóklón URL-címének megtekintéséhez válassza a **klónozás** lehetőséget a jobb felső sarokban.  
    Tudod klón a Git repo a kedvenc IDE. A következő néhány lépésben a webböngésző segítségével közvetlenül a főágban hajthat végre és véglegesíthet kódmódosításokat.

1. A bal oldalon nyissa meg az *alkalmazás/nézetek/pages/home.html.erb* fájlt, és válassza a **Szerkesztés**lehetőséget.

1. Módosítsa a fájlt. Például módosítson néhány szöveget az egyik div címkén belül.

1. Válassza **a Véglegesítés**lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps-projektek irányítópultját.  
    A buildnek folyamatban kell lennie. A végrehajtott módosítások automatikusan létrejönnek és ci/CD-folyamaton keresztül települnek.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Az Azure Pipelines CI/CD-folyamat vizsgálata

Az Azure DevOps-projektek automatikusan konfigurálja a teljes CI/CD-folyamatot az Azure DevOps-szervezetben. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Az Azure DevOps buildelési és kiadási folyamatokkal való ismerkedéshez tegye a következőket:

1. Nyissa meg a DevOps-projektek irányítópultját.

1. A lap tetején válassza a **Folyamatok összeállítása**lehetőséget.  
    A böngészőlapon az új projekt buildelési folyamata látható.

1. Mutasson az **Állapot** mezőre, majd jelölje ki a három pontot (...).  
    A menü számos lehetőséget jelenít meg, például egy új build várólistára kerülését, a build szüneteltetését és a buildfolyamat szerkesztését.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán megvizsgálhatja a buildfolyamat különböző feladatait.  
    A build különböző feladatokat hajt végre, például a Git-tárház ból származó források lekérése, a függőségek visszaállítása és a központi telepítésekhez használt kimenetek közzététele.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a buildfolyamat nevét valami leíróbbra, válassza **a Mentés & várólistát,** majd a **Mentés lehetőséget.**

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ez az ablaktábla a build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure DevOps nyomon követi a buildfolyamat on végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza **az Eseményindítók lehetőséget.**  
    DevOps-projektek automatikusan létrehoz egy CI-eseményindítót, és minden véglegesítése a tárújraíráshoz új buildet indít el. Szükség esetén választhatja az ágak felvétele vagy kizárása a CI folyamatból.

1. Válassza a **Megtartást**.  
    A forgatókönyvtől függően megadhatja, hogy bizonyos számú buildet megtartjon vagy eltávolítson.

1. Válassza **a Build and Release (Létrehozás és kiadás)** lehetőséget, majd a **Kiadások**lehetőséget.  
    A DevOps-projektek létrehozegy kiadási folyamatot az Azure-ba történő központi telepítések kezeléséhez.

1. Jelölje ki a kiadási folyamat melletti három pontot (...), majd kattintson a **Szerkesztés gombra.**  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**.  
    A korábban megvizsgált buildfolyamat a műtermékhez használt kimenetet hozza létre. 

1. A **Legördülő** ikon jobb oldalán válassza a **Folyamatos üzembe helyezés eseményindító jat**.  
    Ez a kiadási folyamat rendelkezik egy engedélyezett CD-eseményindítóval, amely minden alkalommal végrehajtja a központi telepítést, amikor új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy az üzemelő példányok manuális végrehajtást igényelhessenek. 

1. A bal oldalon válassza a **Feladatok**lehetőséget.  
    A feladatok azok a tevékenységek, amelyeket a központi telepítési folyamat hajt végre. Ebben a példában egy feladat jött létre az Azure App Service üzembe helyezéséhez.

1. A jobb oldalon válassza a **Kiadások megtekintése lehetőséget** a kiadások előzményeinek megjelenítéséhez.

1. Jelölje ki a három pontot (...) egy kiadás mellett, majd kattintson a **Megnyitás gombra.**  
    Több menüt is megvizsgálhat, például a kiadások összegzését, a kapcsolódó munkaelemeket és a teszteket.

1. Válassza a **Véglegesítéseket**.  
    Ebben a nézetben a központi telepítéshez társított kódvéglegesítések láthatók. 

1. Válassza **a Naplók lehetőséget.**  
    A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket a központi telepítés alatt és után is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az Azure App Service-példány és a kapcsolódó erőforrásokat, amelyeket ebben a rövid útmutatóban hozott létre. Ehhez használja a **Delete** funkciót a DevOps-projektek irányítópulton.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni arról, hogy miként módosíthatja a build- és kiadási folyamatokat a csapat igényeinek megfelelően, olvassa el a következő témaköröket:

> [!div class="nextstepaction"]
> [A többlépcsős folyamatos üzembe helyezési (CD) folyamat meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
