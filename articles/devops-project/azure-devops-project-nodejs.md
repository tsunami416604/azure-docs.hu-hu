---
title: CI/CD-folyamat létrehozása a Node. js-hez Azure DevOps Projects
description: DevOps Projects megkönnyíti az Azure megkezdését. A segítségével néhány gyors lépésben elindíthat egy alkalmazást a választott Azure-szolgáltatásban.
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
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
monikerRange: vsts
ms.openlocfilehash: 11edeb35119e2c598fd83fd89c65ba4dc4679650
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256099"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Rövid útmutató: CI/CD-folyamat létrehozása az Azure-folyamatokban Node. js-hez Azure DevOps Projects

Azure DevOps Projects az Azure-erőforrások létrehozását és a Node. js-alkalmazás folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatát mutatja be az Azure-folyamatokban.  

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **erőforrás létrehozása**lehetőséget, majd keressen rá **DevOps projects**. 

    ![Folyamatos kézbesítési konfigurációs erőforrás létrehozása](_img/azure-devops-project-nodejs/create-azure-resource.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza ki a Node. js-minta alkalmazást.  
    A Node.js-minták esetén több alkalmazási keretrendszer közül választhat.

1. Az alapértelmezett minta keretrendszer Express. js. Hagyja meg az alapértelmezett beállítást, majd kattintson a **tovább**gombra.  
    Az alapértelmezett üzembehelyezési cél a **Web App on Windows**.  Az alkalmazás-keretrendszer, amelyet korábban választott, az itt elérhető Azure-szolgáltatás telepítési célhelyét diktálja.  

2. Hagyja meg az alapértelmezett szolgáltatást, majd válassza a **tovább**lehetőséget.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure-DevOps és Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon egy meglévő szervezetet. 

    a. Adja meg a projekt nevét.

    b. Válassza ki az Azure-előfizetést és-helyet, adjon meg egy nevet az alkalmazásnak, majd válassza a **kész**lehetőséget.  
    Néhány perc elteltével megjelenik a DevOps Projects irányítópult a Azure Portal. Egy minta alkalmazás az Azure DevOps-szervezet egy tárházában van beállítva, a rendszer létrehoz egy buildet, és az alkalmazást üzembe helyezi az Azure-ban. Ez az irányítópult a Code adattárba, a CI/CD-folyamatba és az Azure-beli alkalmazásba is betekintést nyújt.
     
3. A futó alkalmazás megtekintéséhez válassza a **Tallózás** lehetőséget.

    ![A CI/CD-folyamat irányítópult-nézete](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 
    
DevOps Projects automatikusan konfigurálta a CI-Build és a kiadási triggert.  Most már készen áll arra, hogy együttműködjön egy, a Node. js-alkalmazásban található csapattal a CI/CD folyamattal, amely automatikusan üzembe helyezi a legújabb munkát a webhelyen.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Projects egy git-tárházat hoz létre az Azure Reposben vagy a GitHubban. Az alábbi lépéseket követve megtekintheti az adattárat, és programkód-módosításokat hajthat végre az alkalmazásban.

1. A DevOps Projects irányítópult bal oldalán válassza ki a fő ág hivatkozását.  
Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. A tárház klónozási URL-címének megtekintéséhez válassza a böngésző jobb felső sarkában található **klónozás** elemet.   
    A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe. A következő néhány lépésben a webböngésző segítségével közvetlenül a főágban hajthat végre és véglegesíthet kódmódosításokat.

1. A böngésző bal oldalán lépjen a **views/index. mopsz** fájlra.

1. Válassza a **Szerkesztés**lehetőséget, majd módosítsa a H2-fejlécet.  
    Írja be például a következőt: **első lépések azonnal Azure DevOps projects** , vagy végezze el a módosítást.

1. Válassza **a**végrehajtás lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Projects irányítópultot.   
Ekkor megjelenik egy Build folyamatban. Az imént létrehozott módosításokat a rendszer automatikusan felépíti és telepíti a CI/CD-folyamaton keresztül.

## <a name="examine-the-azure-cicd-pipeline"></a>Az Azure CI-/CD-folyamat vizsgálata

Az előző lépésben DevOps Projects automatikusan konfigurálta a teljes CI/CD-folyamatot. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Az alábbi lépéseket követve Ismerkedjen meg a létrehozási és a kiadási folyamatokkal.

1. A DevOps Projects irányítópult tetején válassza a **folyamatok létrehozása**lehetőséget.  
Ez a hivatkozás egy böngésző fület és az új projekthez tartozó Build folyamatot nyitja meg.

1. Mutasson az **állapot** mezőre, majd válassza a három pontot (...).  
    Ez a művelet egy menüt nyit meg, ahol számos tevékenységet indíthat el, például egy új Build kiépítését, egy Build szüneteltetését és a build folyamat szerkesztését.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán ellenőrizheti a felépítési folyamat különféle feladatait.  
A Build számos feladatot hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A build folyamat tetején válassza a folyamat létrehozása nevet.

1. Módosítsa a felépítési folyamat nevét egy ennél több leíróra, válassza a **mentés & üzenetsor**lehetőséget, majd kattintson a **Mentés**gombra.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
Az **Előzmények** ablaktáblán megjelenik a Build legutóbbi változásainak naplózási nyomvonala.  Az Azure-folyamatok nyomon követik a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszik a verziók összehasonlítását.

1. Válassza az **Eseményindítókat**.   
 DevOps Projects automatikusan létrehozott egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.   
A forgatókönyvtől függően megadhat olyan házirendeket, amelyek bizonyos számú buildet megtartanak vagy eltávolítanak.

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások**lehetőséget.  
 DevOps Projects létrehoz egy kiadási folyamatot az Azure-ba történő üzembe helyezések kezeléséhez.

1. A bal oldalon válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.  
A kiadási folyamat meghatározza a kiadási folyamatot.

12. Az **Összetevők** alatt válassza az **Elvetést**.  
    Az előző lépésekben megvizsgált buildelési folyamat létrehozza az összetevőhöz használt kimenetet. 

1. A **drop** ikon mellett válassza ki a **folyamatos üzembe helyezési triggert**.  
Ez a kiadási folyamat egy engedélyezett CD-triggerrel rendelkezik, amely minden alkalommal futtat egy központi telepítést, amikor új Build-összetevő érhető el. Igény szerint letilthatja az indítást, hogy a központi telepítések manuális végrehajtást igényeljenek. 


1. A bal oldalon válassza a **feladatok**lehetőséget.   
A feladatok azok a tevékenységek, amelyeket a telepítési folyamat végrehajt. Ebben a példában egy feladatot hoztak létre a Azure App Serviceba való üzembe helyezéshez.


1. A jobb oldalon válassza a **kiadások megtekintése**lehetőséget.  
Ebben a nézetben a kiadások előzményei jelennek meg.

1. Válassza ki az egyik kiadás melletti három pontot (...), majd kattintson a **Megnyitás**gombra.  
Több menüt is megvizsgálhat, például a kiadás összegzését, a kapcsolódó munkaelemeket és teszteket.

1. Válassza a **Véglegesítéseket**.   
Ez a nézet az adott központi telepítéshez társított kód-véglegesítő kódokat jeleníti meg.

1. Válassza a **Naplókat**.  
A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti Azure App Service és az egyéb kapcsolódó erőforrásokat, amelyeket akkor hozott létre, ha már nincs rá szükség. Használja a **delete** funkciót a DevOps projects irányítópulton.


## <a name="next-steps"></a>Következő lépések

A CI/CD-folyamat beállításakor a rendszer automatikusan létrehozta a létrehozási és kiadási folyamatokat. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videók

> [!VIDEO https://www.youtube.com/embed/3etwjubReJs]
