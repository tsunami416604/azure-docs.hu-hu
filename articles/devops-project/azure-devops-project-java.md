---
title: 'Gyors útmutató: CI/CD-folyamat létrehozása a Javához – Azure DevOps Projects'
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
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
monikerRange: vsts
ms.openlocfilehash: 1a276770887bee39972ba8630fb13f52bcbe802d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049973"
---
# <a name="quickstart-set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-projects"></a>Rövid útmutató: CI/CD-folyamat beállítása Java-alkalmazáshoz Azure DevOps Projects

Ebben a rövid útmutatóban az egyszerűsített Azure DevOps Projectsi felülettel állíthatja be a Java-alkalmazás folyamatos integrációs (CI) és folyamatos továbbítási (CD) folyamatát az Azure-folyamatokban. A Azure DevOps Projects használatával beállíthatja az alkalmazás fejlesztéséhez, üzembe helyezéséhez és monitorozásához szükséges mindent. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Egy [Azure DevOps](https://azure.microsoft.com/services/devops/) -fiók és-szervezet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és a bal oldali ablaktáblán válassza az **erőforrás létrehozása**lehetőséget. 

   ![Azure-erőforrás létrehozása Azure Portal](_img/azure-devops-project-java/continuous-delivery-configuration-full-browser.png)

1. Keresse meg és válassza ki **DevOps projects**, majd válassza a **Létrehozás**lehetőséget.

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza ki a Java-minta alkalmazást.  
A Java-minták esetén több alkalmazási keretrendszer közül választhat.

1. Az alapértelmezett minta-keretrendszer tavasszal van. Hagyja meg az alapértelmezett beállítást, majd kattintson a **tovább**gombra.  A Web App for containers az alapértelmezett telepítési cél. Az alkalmazás-keretrendszer, amelyet korábban választott, az itt elérhető Azure-szolgáltatás telepítési célhelyét diktálja. 

2. Hagyja meg az alapértelmezett szolgáltatást, majd válassza a **tovább**lehetőséget.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon egy meglévő szervezetet. 
   
   1. Válasszon egy nevet a projektnek. 
   
   1. Válassza ki az Azure-előfizetést és-helyet, válasszon egy nevet az alkalmazásnak, majd válassza a **kész**lehetőséget.  
   Néhány perc elteltével megjelenik a DevOps Projects irányítópult a Azure Portal. Egy minta alkalmazás az Azure DevOps-szervezet egy tárházában van beállítva, a rendszer létrehoz egy buildet, és az alkalmazást üzembe helyezi az Azure-ban. Ezt az irányítópultot a kódtárat a CI/CD-folyamat és az Azure-ban az alkalmazás rálátást biztosít.
   
2. A futó alkalmazás megtekintéséhez válassza a **Tallózás** lehetőséget.
   
   ![Alkalmazás-irányítópult megtekintése Azure Portal](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

DevOps Projects automatikusan konfigurálta a CI-Build és a kiadási triggert.  Most már készen áll, hogy egy csapattal közösen dolgozzon a Java-alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Projects egy git-tárházat hoz létre az Azure Reposben vagy a GitHubban. Az adattár megtekintéséhez és a kód módosításának végrehajtásához tegye a következőket:

1. A DevOps Projects irányítópult bal oldalán válassza ki a fő ág hivatkozását.  
Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. A tárház klónozási URL-címének megtekintéséhez válassza a böngésző jobb felső sarkában található **klónozás** elemet.   
    A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe. A következő néhány lépésben a webböngésző segítségével közvetlenül a főágban hajthat végre és véglegesíthet kódmódosításokat.

1. A böngésző bal oldalán lépjen a **src/Main/WebApp/index.html** fájlra.

1. Válassza a **Szerkesztés**lehetőséget, majd végezze el a módosítást egy bizonyos szövegre.
    Módosítsa például az egyik div címkén belüli szöveget.

1. Válassza **a**végrehajtás lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Projects irányítópultot.   
Ekkor megjelenik egy Build folyamatban. Az imént létrehozott módosításokat a rendszer automatikusan felépíti és telepíti a CI/CD-folyamaton keresztül.

## <a name="examine-the-cicd-pipeline"></a>Vizsgálja meg a CI/CD-folyamat

 Az előző lépésben DevOps Projects automatikusan konfigurálta a teljes CI/CD-folyamatot. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Az alábbi lépéseket követve Ismerkedjen meg a létrehozási és a kiadási folyamatokkal.

1. A DevOps Projects irányítópult tetején válassza a **folyamatok létrehozása**lehetőséget.  
Ez a hivatkozás egy böngésző fület és az új projekthez tartozó Build folyamatot nyitja meg.

1. Mutasson az **állapot** mezőre, majd válassza a három pontot (...).  
    Ez a művelet egy menüt nyit meg, ahol számos tevékenységet indíthat el, például egy új Build kiépítését, egy Build szüneteltetését és a build folyamat szerkesztését.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
A Build számos feladatot hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a felépítési folyamat nevét egy ennél több leíróra, válassza a **mentés & üzenetsor**lehetőséget, majd kattintson a **Mentés**gombra.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
Az **Előzmények** ablaktáblán megjelenik a Build legutóbbi változásainak naplózási nyomvonala.  Azure folyamatok nyomon követi, hogy az összeállítási folyamat végzett módosításokat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**.   
 A DevOps Projects automatikusan létrejön egy CI eseményindító, és a tárházban minden véglegesítéshez elindul egy új létrehozást.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.   
A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások**lehetőséget.  
 DevOps Projects létrehoz egy kiadási folyamatot az Azure-ba történő üzembe helyezések kezeléséhez.

1. A bal oldalon válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.  
A kiadási folyamathoz egy folyamatot, amely meghatározza a kibocsátási folyamat tartalmazza.  
    
12. Az **Összetevők** alatt válassza az **Elvetést**.  
Az előző lépések során megvizsgált összeállítási folyamat létrehozza az összetevőhöz használt kimenetet. 

1. A **drop** ikon mellett válassza ki a **folyamatos üzembe helyezési triggert**.  
A kibocsátási folyamat rendelkezik egy engedélyezett CD eseményindító, amely futtatja a központi telepítés minden alkalommal, amikor egy új buildösszetevő áll rendelkezésre. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

1. A bal oldalon válassza a **feladatok**lehetőséget.   
A feladatok olyan tevékenységek, amely végrehajtja a központi telepítési folyamat. Ebben a példában egy feladatot hoztak létre a Azure App Serviceba való üzembe helyezéshez.

1. A jobb oldalon válassza a **kiadások megtekintése**lehetőséget.  
Ebben a nézetben a kiadások előzményei jelennek meg.

1. Válassza ki az egyik kiadás melletti három pontot (...), majd kattintson a **Megnyitás**gombra.  
Ismerje meg, például a kiadás összegzését, a társított munkaelemekhez és a tesztek a több menük találhatók meg.

1. Válassza a **Véglegesítéseket**.   
Ez a nézet megjeleníti az adott központi telepítési társított kód véglegesítéseket. 

1. Válassza a **Naplókat**.  
A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, törölheti Azure App Service és az egyéb kapcsolódó erőforrásokat is. Használja a **delete** funkciót a DevOps projects irányítópulton.

## <a name="next-steps"></a>Következő lépések

A CI/CD-folyamat beállításakor a rendszer automatikusan létrehozta a létrehozási és kiadási folyamatokat. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
