---
title: 'Rövid útmutató: CI/CD-folyamat létrehozása pythonhoz az Azure DevOps-projektekkel'
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
ms.openlocfilehash: b912dfe3fb6461a925977192a6631ecac1357d35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77049818"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-projects"></a>CI/CD-folyamat létrehozása a Pythonhoz az Azure DevOps-projektekkel

Ebben a rövid útmutatóban az egyszerűsített Azure DevOps-projektek használatával folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot állíthat be a Python-alkalmazáshoz az Azure Pipelines-ban. Az Azure DevOps-projektek segítségével mindent beállíthat, amire szüksége van az alkalmazás fejlesztéséhez, üzembe helyezéséhez és figyeléséhez. 

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Egy Azure DevOps-fiók](https://azure.microsoft.com/services/devops/) és -szervezet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps-projektek létrehoznak egy CI/CD-folyamatot az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrásokat az Azure-előfizetés az Ön által kiválasztott.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és a bal oldali ablaktáblában válassza **az Erőforrás létrehozása lehetőséget.** 

   ![Azure-erőforrás létrehozása az Azure Portalon](_img/azure-devops-project-python/fullbrowser.png)

1. Keresse meg a **DevOps-projekteket,** és válassza a **Create (Létrehozás)** lehetőséget.

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza ki a Python mintaalkalmazást. A Python-minták esetén több alkalmazási keretrendszer közül választhat.

1. Az alapértelmezett mintakeretrendszer a Django. Hagyja meg az alapértelmezett beállítást, majd kattintson a **Tovább gombra.**    
Az alapértelmezett üzembehelyezési cél a Web App for Containers. Az alkalmazáskeretrendszer, amely et korábban választott, meghatározza az Azure-szolgáltatás üzembe helyezési cél itt elérhető típusát. 

3. Hagyja el az alapértelmezett szolgáltatást, és válassza a **Tovább gombot.**
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és egy Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps Services-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

    a. Adja meg a projekt nevét az Azure DevOps-ban.  

    b. Válassza ki az Azure-előfizetést és a helyet, adja meg az alkalmazás nevét, majd válassza a **Kész**lehetőséget.  
     Néhány perc elteltével a projekt irányítópultja megjelenik az Azure Portalon. A mintaalkalmazás be van állítva egy tárházban az Azure DevOps-szervezet, a build végrehajtása, és az alkalmazás telepítve van az Azure-ban. Ez az irányítópult betekintést nyújt a kódtárházba, a CI/CD-folyamatba és az Azure-beli alkalmazásba.  
    
2. A futó alkalmazás megtekintéséhez válassza a **Tallózás** gombot.

    ![Irányítópult nézet](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   DevOps-projektek automatikusan konfigurálja a CI build és kiadás eseményindító. Most már készen áll arra, hogy együttműködjön egy csapattal egy Python-alkalmazásban egy CI/CD-folyamat használatával, amely automatikusan telepíti a legújabb munkáját a webhelyére.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

 A DevOps-projektek létrehoznak egy Git-tárházat az Azure Reposban vagy a GitHubon. A tárház megtekintéséhez és az alkalmazás kódmódosításához tegye a következőket: 

1. A DevOps-projektek irányítópult bal oldalán válassza ki a főág hivatkozását.  
        Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. Az adattárklón URL-címének megtekintéséhez válassza a **Klónozás** lehetőséget a böngésző jobb felső részén.   
A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe.  A következő néhány lépésben a webböngésző segítségével közvetlenül a főágban hajthat végre és véglegesíthet kódmódosításokat.

1. A bal oldalon lépjen a **app/templates/app/index.html** fájlhoz.

1. Válassza a **Szerkesztés** elemet, és módosítson a szöveg valamely részén. Módosítsa például az egyik div címkén belüli szöveget.

1. Válassza **a Véglegesítés**lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps-projektek irányítópultját.   
    Most látnia kell egy folyamatban lévő buildet. Az imént végrehajtott módosítások automatikusan létrejönnek és ci/CD-folyamaton keresztül települnek.

## <a name="examine-the-cicd-pipeline"></a>A CI/CD-folyamat vizsgálata

Az előző lépésben a DevOps-projektek automatikusan konfigurálták a teljes CI/CD-folyamatot. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A build- és kiadási folyamatok megismeréséhez tegye a következőket:

1. A DevOps-projektek irányítópultjának tetején válassza a **Folyamatok összeállítása**lehetőséget.  
A böngészőlapon az új projekt buildelési folyamata látható.

1. Mutasson az **Állapot** mezőre, majd jelölje ki a **három pontot** (...).  
        A menü számos lehetőséget jelenít meg, például egy új build várólistára kerülését, a build szüneteltetését és a buildfolyamat szerkesztését.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán megvizsgálhatja a buildfolyamat különböző feladatait.  
        A build különböző feladatokat hajt végre, például a Git-tárházból származó források lekérése, a függőségek visszaállítása és a központi telepítések kimenetének közzététele.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a buildfolyamat nevét valami leíróbbra, válassza **a Mentés & várólistát,** majd a **Mentés lehetőséget.**

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
        Ekkor megjelenik a build legutóbbi módosításainak naplója.  Az Azure DevOps nyomon követi a buildfolyamat on végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza **az Eseményindítók lehetőséget.**  
         DevOps-projektek automatikusan létrehoz egy CI-eseményindítót, és minden véglegesítést a tárház hoz egy új buildet.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.  
        A forgatókönyvtől függően megadhatja, hogy bizonyos számú buildet megtartjon vagy eltávolítson.

1. Válassza **a Build and Release (Létrehozás és feloldás)** lehetőséget, majd a **Kiadások**lehetőséget.   
 A DevOps-projektek létrehozegy kiadási folyamatot az Azure-ba történő központi telepítések kezeléséhez.

1. Jelölje ki a három pontot a kiadási folyamat mellett, majd válassza **a Szerkesztés**lehetőséget.  
A kiadási folyamat határozza meg a kiadási folyamatot.  
        
12. Az **Összetevők** alatt válassza az **Elvetést**.   
Az előző lépésekben megvizsgált buildfolyamat létrehozza a műtermékhez használt kimenetet. 

1. A **Legördülő** ikon mellett válassza a **Folyamatos üzembe helyezés eseményindítót**.  
        A kiadási folyamat rendelkezik egy engedélyezett CD-eseményindítóval, amely minden alkalommal futtat egy központi telepítést, amikor új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy az üzemelő példányok manuális végrehajtást igényelhessenek. 

1. A bal oldalon válassza a **Feladatok**lehetőséget.   
A feladatok azok a tevékenységek, amelyeket a központi telepítési folyamat hajt végre. Ebben a példában egy feladat jött létre az Azure App Service üzembe helyezéséhez.

1. A jobb oldalon válassza a **Kiadások megtekintése lehetőséget** a kiadások előzményeinek megjelenítéséhez.  
        
1. Jelölje ki a három pontot (...) az egyik kiadás mellett, majd kattintson a **Megnyitás gombra.**  
        Ebből a nézetből számos menüt kell felfedezni, például a kiadások összegzését, a kapcsolódó munkaelemeket és a teszteket.

1. Válassza a **Véglegesítéseket**. 
        Ez a nézet az adott központi telepítéshez társított kódvéglegesítéseket jeleníti meg. 

1. Válassza **a Naplók lehetőséget.**   
A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Megtekintheti őket a központi telepítés alatt és után is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti az Azure App Service-t és a kapcsolódó erőforrásokat, ha már nincs rájuk szüksége. Használja a **Törlés** funkciót a DevOps-projektek irányítópulton.

## <a name="next-steps"></a>További lépések

A CI/CD folyamat konfigurálásakor a rendszer automatikusan létrehozza a build- és kiadási folyamatokat. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. A CI/CD-folyamatról a következő témakörben olvashat bővebben:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
