---
title: 'Rövid útmutató: CI/CD-folyamat létrehozása php-hez az Azure DevOps-projektekkel'
description: A DevOps-projektek megkönnyítik az Azure-ral való ismerkedést. A segítségével néhány gyors lépésben elindíthat egy alkalmazást a választott Azure-szolgáltatásban.
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
ms.openlocfilehash: 7c097c2a7247c9cad6cdce8a89059ee8d90d159e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "70899590"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-projects"></a>CI/CD-folyamat létrehozása php-hez az Azure DevOps-projektekkel

Az Azure DevOps-projektek egyszerűsített élményt nyújt, amely létrehozza az Azure-erőforrásokat, és folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot állít be a PHP-alkalmazáshoz az Azure Pipelines-ban.  

Ha nem rendelkezik Azure-előfizetéssel, ingyenesen beszerezhet egyet a [Visual Studio Dev Essentials szolgáltatáson](https://visualstudio.microsoft.com/dev-essentials/)keresztül.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

 A DevOps-projektek létrehoznak egy CI/CD-folyamatot az Azure-folyamatokban. Létrehozhat egy ingyenes új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrásokat az Azure-előfizetés az Ön által kiválasztott.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali ablaktáblában válassza az **Erőforrás létrehozása ikont,** és keresse meg a **DevOps-projektek et.**  

3. Kattintson a **Létrehozás** gombra.

    ![Folyamatos kézbesítési konfiguráció indítása](_img/azure-devops-project-php/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza a PHP mintaalkalmazást.  
        A PHP minták több alkalmazáskeretrendszert tartalmaznak. Az alapértelmezett mintakeret a Laravel. 
        
2. Hagyja meg az alapértelmezett beállítást, majd kattintson a **Tovább gombra.**  

1. Az alapértelmezett üzembehelyezési cél a Web App for Containers.  
    Az alkalmazáskeretrendszer, amely et korábban választott, meghatározza az Azure-szolgáltatás üzembe helyezési cél, amely itt érhető el.  Hagyja el az alapértelmezett szolgáltatást, és válassza a **Tovább gombot.**
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és egy Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

    a. Válassza ki a projekt nevét az Azure DevOps-ban. 
    
    b. Válassza ki az Azure-előfizetést és a helyet, adja meg az alkalmazás nevét, majd válassza a **Kész**lehetőséget.   
        Néhány perc elteltével a DevOps-projektek irányítópultja megjelenik az Azure Portalon. A mintaalkalmazás az Azure DevOps-szervezet tárházában van beállítva, egy buildfuttatás, és az alkalmazás üzembe helyezi az Azure-ba. Ez az irányítópult betekintést nyújt a kódtárházba, a CI/CD-folyamatba és az Azure-beli alkalmazásba.  
        
2. A futó alkalmazás megtekintéséhez válassza a **Tallózás** gombot.

    ![Irányítópult nézet](_img/azure-devops-project-php/dashboardnopreview.png) 
    
   DevOps-projektek automatikusan konfigurált a CI build és kiadás eseményindító.  Most már készen áll, hogy egy csapattal közösen dolgozzon a PHP-alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

 A DevOps-projektek létrehoznak egy Git-tárházat az Azure Reposban vagy a GitHubon. A tárház megtekintéséhez és az alkalmazás kódmódosításához hajtsa végre az alábbi lépéseket:

1. A DevOps-projektek irányítópultbal válassza ki a főág hivatkozását.   
    Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. Az adattárklón URL-címének megtekintéséhez válassza a **Klónozás** lehetőséget a böngésző jobb felső részén.   
    A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe. A következő néhány lépésben a webböngészősegítségével közvetlenül a főágra hajtson végre kódmódosításokat.

1. A bal oldalon, megy a **források / views/welcome.blade.php** fájlt.

1. Válassza **a Szerkesztés**lehetőséget, majd módosítsa a szöveg egy részét.  Módosítsa például az egyik div címkén belüli szöveget.

1. Válassza **a Véglegesítés**lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps-projektek irányítópultját.  
Most látnia kell egy folyamatban lévő buildet. Az imént végrehajtott módosítások automatikusan létrejönnek és ci/CD-folyamaton keresztül települnek.

## <a name="examine-the-cicd-pipeline"></a>A CI/CD-folyamat vizsgálata

 DevOps-projektek automatikusan konfigurálja a teljes CI/CD-folyamat az Azure-folyamatokban. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A build- és kiadási folyamatok megismeréséhez tegye a következőket:

1. A DevOps-projektek irányítópultjának tetején válassza a **Folyamatok összeállítása**lehetőséget.  
    Ez a hivatkozás megnyit egy böngészőlapot és az új projekt buildfolyamatát.

1. Mutasson az **Állapot** mezőre, majd jelölje ki a **három pontot** (...).  
    A menü számos lehetőséget jelenít meg, például egy új build sorba állása, a build szüneteltetése és a buildfolyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán megvizsgálhatja a buildfolyamat különböző feladatait.  
    A build számos feladatot futtat, például a Git-tárházból származó források lekérése, a függőségek visszaállítása és a központi telepítésekhez használt kimenetek közzététele.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a buildfolyamat nevét valami leíróbbra, válassza **a Mentés & várólistát,** majd kattintson a **Mentés gombra.**

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
    Az **Előzmények** ablaktábla megjeleníti a build legutóbbi változásainak naplózási nyomvonalát. Az Azure Pipelines nyomon követi a buildelőfolyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza **az Eseményindítók lehetőséget.**  
      DevOps-projektek automatikusan létrehozott egy CI-eseményindítót, és minden véglegesítést a tárház hoz egy új buildet. Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.   
    A forgatókönyvtől függően megadhatja, hogy bizonyos számú buildet megtartjon vagy eltávolítson.

1. Válassza **a Build and Release (Létrehozás és kiadás)** lehetőséget, majd a **Kiadások**lehetőséget.  
     A DevOps-projektek létrehozegy kiadási folyamatot az Azure-ba történő központi telepítések kezeléséhez.

1. Jelölje ki a kiadási folyamat melletti három pontot (...), majd kattintson a **Szerkesztés gombra.**  
    A kiadási folyamat tartalmaz egy folyamatot, amely meghatározza a kiadási folyamatot. 

12. Az **Összetevők** alatt válassza az **Elvetést**.  
    Az előző lépésekben megvizsgált buildfolyamat létrehozza a műtermékhez használt kimenetet. 

1. A **Legördülő** ikon mellett válassza a **Folyamatos üzembe helyezés eseményindítót**.   
    Ez a kiadási folyamat rendelkezik egy engedélyezett CD-eseményindítóval, amely minden alkalommal futtat egy központi telepítést, amikor új buildösszetevő érhető el.  Szükség esetén letilthatja az eseményindítót, hogy az üzemelő példányok manuális végrehajtást igényelhessenek. 

1. A bal oldalon válassza a **Feladatok**lehetőséget.  
        A feladatok azok a tevékenységek, amelyeket a központi telepítési folyamat hajt végre.  Ebben a példában egy feladat jött létre az Azure App Service üzembe helyezéséhez.

1. A jobb oldalon válassza a **Kiadások megtekintése lehetőséget** a kiadások előzményeinek megjelenítéséhez.

1. Jelölje ki a három pontot (...) az egyik kiadás mellett, majd kattintson a **Megnyitás gombra.**  
        Ebben a nézetben több menüt is megtekinthet, például a kiadás összegzését, a társított munkaelemeket vagy a teszteket.

1. Válassza a **Véglegesítéseket**.  
        Ebben a nézetben az adott központi telepítéshez társított kódvéglegesítések láthatók. 

1. Válassza **a Naplók lehetőséget.**  
        A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti az Azure App Service-t és más kapcsolódó erőforrásokat, ha már nincs rájuk szüksége. Használja a **Törlés** funkciót a DevOps-projektek irányítópulton.

## <a name="next-steps"></a>További lépések

A CI/CD folyamat konfigurálásakor a rendszer automatikusan létrehozza a build- és kiadási folyamatokat. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ha többet szeretne megtudni a CI/CD-folyamatról, tekintse meg ezt az oktatóanyagot:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
