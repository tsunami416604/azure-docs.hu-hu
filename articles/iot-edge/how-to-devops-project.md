---
title: CI/CD-folyamat Azure DevOps Projects-Azure IoT Edgetal | Microsoft Docs
description: Azure DevOps Projects megkönnyíti az Azure megkezdését. Segítségével néhány gyors lépésben elindíthat egy Azure IoT Edge alkalmazást.
author: shizn
manager: ''
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: daa4bc7b1584dc2159d4128fa4b44056df347ecb
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72253098"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>CI/CD-folyamat létrehozása a IoT Edgehoz Azure DevOps Projects

A folyamatos integráció (CI) és a folyamatos kézbesítés (CD) konfigurálása a IoT Edge alkalmazáshoz DevOps Projects. DevOps Projects leegyszerűsíti az Azure-folyamatok Build és kiadási folyamatának kezdeti konfigurációját.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Projects egy CI/CD-folyamatot hoz létre az Azure DevOps-ben. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **erőforrás létrehozása**lehetőséget, majd keressen rá **DevOps projects**.  

1.  Kattintson a **Létrehozás** gombra.

## <a name="create-a-new-application-pipeline"></a>Új alkalmazás-folyamat létrehozása 

1. A Azure IoT Edge modul (ok) [C#](tutorial-csharp-module.md)írható, [Node. js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) és [Java](tutorial-java-module.md)nyelven. Válassza ki a kívánt nyelvet egy új alkalmazás indításához: **.net**, **Node. js**, **Python**, **C**vagy **Java**. A folytatáshoz kattintson a **Tovább** gombra.

   ![Új alkalmazás létrehozásához válassza a Language (nyelv) lehetőséget.](./media/how-to-devops-project/select-language.png)

2. Válassza az **egyszerű IoT** lehetőséget az alkalmazás-keretrendszerként, majd kattintson a **tovább**gombra.

   ![Egyszerű IoT-keretrendszer kiválasztása](media/how-to-devops-project/select-iot.png)

3. Válassza a **IoT Edge** lehetőséget az alkalmazást üzembe helyező Azure-szolgáltatásként, majd kattintson a **tovább**gombra.

   ![IoT Edge szolgáltatás kiválasztása](media/how-to-devops-project/select-iot-edge.png)

4. Hozzon létre egy új ingyenes Azure DevOps-szervezetet, vagy válasszon egy meglévő szervezetet.

   1. Adja meg a projekt nevét. 

   2. Válassza ki az Azure DevOps-szervezetét. Ha nem rendelkezik meglévő szervezettel, válassza a **További beállítások** lehetőséget, hogy újat hozzon létre. 

   3. Válassza ki az Azure-előfizetését.

   4. Használja a projekt neve alapján létrehozott IoT Hub nevet, vagy adja meg a sajátját.

   5. Fogadja el az alapértelmezett helyet, vagy válasszon egyet a bezáráshoz. 

   5. Válassza a **További beállítások** lehetőséget a DevOps projects által az Ön nevében létrehozott Azure-erőforrások konfigurálásához.

   6. A projekt létrehozásának befejezéséhez kattintson a **kész** gombra. 

   ![Alkalmazás neve és létrehozása](media/how-to-devops-project/select-devops.png)

Néhány perc elteltével megjelenik a DevOps Projects irányítópult a Azure Portal. Válassza ki a projekt nevét a folyamat előrehaladásának megtekintéséhez. Előfordulhat, hogy frissítenie kell a lapot. Egy minta IoT Edge alkalmazás van beállítva az Azure DevOps-szervezetben lévő adattárban, a rendszer létrehoz egy buildet, és az alkalmazást üzembe helyezi a IoT Edge eszközön. Ez az irányítópult a Code adattárba, a CI/CD-folyamatba és az Azure-beli alkalmazásba is betekintést nyújt.

   ![Alkalmazás megtekintése Azure Portal](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

DevOps Projects létrehozott egy git-tárházat a projekthez az Azure Reposban. Ebben a szakaszban megtekintheti az adattárat, és programkód-módosításokat hajthat végre az alkalmazásban.

1. Ha a projekthez létrehozott tárházra szeretne navigálni, válassza a **tárolók** lehetőséget a projekt irányítópultjának menüjében.  

   ![Az Azure Reposben létrehozott adattár megtekintése](./media/how-to-devops-project/view-repositories.png)

2. A következő lépések végigvezetik a böngészőben a kód módosításának lépésein. Ha a tárházat helyileg szeretné klónozottként használni, válassza a **klónozás** lehetőséget az ablak jobb felső sarkában. Használja a megadott URL-címet a git-tárház klónozásához a Visual Studio Code-ban vagy az előnyben részesített fejlesztői eszközben. 

3. A tárház már tartalmaz egy **FilterModule** nevű modul kódját a létrehozási folyamat során kiválasztott nyelv alapján. Nyissa meg a **modules/FilterModule/Module. JSON** fájlt.

   ![Module. JSON fájl megnyitása az Azure Reposban](./media/how-to-devops-project/open-module-json.png)

4. Figyelje meg, hogy ez a fájl az [Azure DevOps Build változókat](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) használja a **Version** paraméterben. Ez a konfiguráció biztosítja, hogy a rendszer minden új Build futtatásakor létrehozza a modul új verzióját. 


## <a name="examine-the-cicd-pipeline"></a>A CI/CD-folyamat vizsgálata

Az előző szakaszban Azure DevOps Projects automatikusan konfigurálta a IoT Edge alkalmazás teljes CI/CD-folyamatát. Most a folyamat igény szerinti megismeréséhez és testreszabásához. Az alábbi lépésekkel megismerheti az Azure DevOps Build és Release folyamatait.

1. Ha meg szeretné tekinteni a DevOps projekt összeállítási folyamatait, válassza a **folyamat létrehozása** lehetőséget a projekt irányítópultjának menüjében. Ez a hivatkozás egy böngésző fület és az Azure DevOps Build folyamatát nyitja meg az új projekthez.

   ![Build-folyamatok megtekintése az Azure-folyamatokban](./media/how-to-devops-project/view-build-pipelines.png)

2. Válassza a **Szerkesztés** elemet.

    ![Build folyamat szerkesztése](media/how-to-devops-project/click-edit-button.png)

3. A megnyíló panelen megtekintheti a létrehozási folyamat futtatásakor felmerülő feladatokat. A létrehozási folyamat különféle feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, IoT Edge modul lemezképeit fejleszti, leküldi IoT Edge modulokat egy tároló-beállításjegyzékbe, és közzéteszi az üzembe helyezéshez használt kimeneteket. Ha többet szeretne megtudni az Azure DevOps Azure IoT Edge feladatairól, tekintse meg [Az Azure-folyamatok folyamatos integrációhoz való konfigurálásával foglalkozó](how-to-ci-cd.md#configure-continuous-integration)témakört.

4. A folyamat részleteinek megnyitásához válassza a **folyamat fejlécét** a build folyamat tetején. Módosítsa a felépítési folyamat nevét egy ennél részletesebb leírásra.

   ![A folyamat részleteinek szerkesztése](./media/how-to-devops-project/edit-build-pipeline.png)

5. Válassza a **mentés & üzenetsor**lehetőséget, majd kattintson a **Mentés**gombra.

6. Válassza az **Eseményindítók** lehetőséget a folyamat összeállítása menüből. DevOps Projects automatikusan létrehozott egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

7. Válassza a **Megtartást**. A forgatókönyvtől függően megadhat olyan házirendeket, amelyek bizonyos számú buildet megtartanak vagy eltávolítanak.

8. Válassza a **History (előzmények**) lehetőséget. Az előzmények panel a Build legutóbbi változásainak naplózási nyomvonalát tartalmazza. Az Azure-folyamatok nyomon követik a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszik a verziók összehasonlítását.

9. Amikor elkészült a Build folyamattal, navigáljon a megfelelő kiadási folyamathoz. Válassza a **kiadások** a **folyamatok**alatt lehetőséget, majd kattintson a **Szerkesztés** elemre a folyamat részleteinek megtekintéséhez.

    ![Kiadási folyamat megtekintése](media/how-to-devops-project/release-pipeline.png)

10. Az **Összetevők** alatt válassza az **Elvetést**. Az összetevő által figyelt forrás az előző lépésekben megvizsgált összeállítási folyamat kimenete. 

11. A **drop** ikon mellett válassza ki a **folyamatos üzembe helyezési triggert** , amely a villámcsapáshoz hasonlít. Ez a kiadási folyamat engedélyezte az aktiválást, amely minden alkalommal futtat egy központi telepítést, amikor új Build-összetevő érhető el. Igény szerint letilthatja az indítást, hogy a központi telepítések manuális végrehajtást igényeljenek.  

12. A kiadási folyamat menüjében válassza a **feladatok** lehetőséget, majd válassza ki a **fejlesztői** szakaszt a legördülő listából. DevOps Projects létrehozott egy kiadási szakaszt, amely létrehoz egy IoT hubot, létrehoz egy IoT Edge eszközt az adott központban, üzembe helyezi a minta modult a Build folyamatból, és kiépíti a virtuális gépet, hogy IoT Edge eszközként fusson. Ha többet szeretne megtudni a CD Azure IoT Edge feladatairól, tekintse meg [Az Azure-folyamatok folyamatos üzembe helyezéshez való konfigurálását](how-to-ci-cd.md#configure-continuous-deployment)ismertető témakört.

    ![Folyamatos üzembe helyezési feladatok megtekintése](media/how-to-devops-project/dev-release.png)

13. A jobb oldalon válassza a **kiadások megtekintése**lehetőséget. Ebben a nézetben a kiadások előzményei jelennek meg.

14. Válassza ki a kiadás nevét a további információk megtekintéséhez.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti Azure App Service és az egyéb kapcsolódó erőforrásokat, amelyeket akkor hozott létre, ha már nincs rá szükség. Használja a **delete** funkciót a DevOps projects irányítópulton.

## <a name="next-steps"></a>Következő lépések
* Ismerkedjen meg az Azure DevOps Azure IoT Edgeával kapcsolatos feladatokkal a [folyamatos integráció és a folyamatos üzembe helyezés Azure IoT Edge](how-to-ci-cd.md)
* A IoT Edge központi telepítésének megismerése az [egyes eszközök IoT Edge központi telepítések megismeréséhez](module-deployment-monitoring.md)
* Végigvezeti a központi telepítések létrehozásához, frissítéséhez vagy törléséhez szükséges lépéseket a [IoT Edge modulok nagy léptékű üzembe helyezéséhez és figyeléséhez](how-to-deploy-monitor.md).
