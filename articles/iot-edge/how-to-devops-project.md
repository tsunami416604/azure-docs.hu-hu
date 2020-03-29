---
title: CI/CD-folyamat Azure DevOps-projektekkel – Azure IoT Edge | Microsoft dokumentumok
description: Az Azure DevOps-projektek megkönnyítik az azure-ral való ismerkedést. Segítségével néhány gyors lépésben elindíthatja az Ön által kiválasztott Azure IoT Edge-alkalmazást.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66401678f03ee0043345208eb32560f589829226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510311"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>CI/CD-folyamat létrehozása az IoT Edge számára az Azure DevOps-projektekkel

Konfigurálja a folyamatos integrációt (CI) és a folyamatos kézbesítést (CD) az IoT Edge-alkalmazás DevOps-projektekkel. DevOps-projektek leegyszerűsíti a kezdeti konfigurációegy build- és kiadási folyamat az Azure-folyamatokban.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps-projektek létrehoznak egy CI/CD-folyamatot az Azure DevOps-ban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő szervezetet. DevOps-projektek is létrehoz Azure-erőforrásokat az Azure-előfizetés az Ön által kiválasztott.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali ablaktáblában válassza az **Erőforrás létrehozása**lehetőséget, majd keresse meg a **DevOps-projektek et.**  

1. Kattintson a **Létrehozás** gombra.

## <a name="create-a-new-application-pipeline"></a>Új alkalmazásfolyamat létrehozása

1. Az Azure IoT Edge-modul(ok) [c#](tutorial-csharp-module.md), [node.js,](tutorial-node-module.md) [python,](tutorial-python-module.md) [c](tutorial-c-module.md) és [java](tutorial-java-module.md)nyelven írhatók. Új alkalmazás indításához válassza ki a kívánt nyelvet: **.NET**, **Node.js**, **Python,** **C**vagy **Java**. A folytatáshoz kattintson a **Tovább** gombra.

   ![Új alkalmazás létrehozásához válasszon nyelvet](./media/how-to-devops-project/select-language.png)

2. Válassza **az egyszerű IoT-t** az alkalmazáskeretrendszerként, majd a **Tovább**gombot.

   ![Egyszerű IoT-keretrendszer kiválasztása](media/how-to-devops-project/select-iot.png)

3. Válassza az **IoT Edge lehetőséget** az alkalmazást üzembe helyező Azure-szolgáltatásként, majd válassza a **Tovább**gombot.

   ![IoT Edge szolgáltatás kiválasztása](media/how-to-devops-project/select-iot-edge.png)

4. Hozzon létre egy új, ingyenes Azure DevOps-szervezetet, vagy válasszon egy meglévő szervezetet.

   1. Adja meg a projekt nevét.

   2. Válassza ki az Azure DevOps-szervezetet. Ha nem rendelkezik meglévő szervezettel, új beállítás létrehozásához válassza a **További beállítások** lehetőséget.

   3. Válassza ki az Azure-előfizetését.

   4. Használja a projekt neve által létrehozott IoT Hub nevet, vagy adja meg a sajátját.

   5. Fogadja el az alapértelmezett helyet, vagy válasszon egyet a magához közel.

   6. Válassza a **További beállítások lehetőséget** a DevOps-projektek által az Ön nevében létrehozott Azure-erőforrások konfigurálásához.

   7. A projekt létrehozásának befejezéséhez válassza a **Kész** lehetőséget.

   ![Alkalmazás elnevezése és létrehozása](media/how-to-devops-project/select-devops.png)

Néhány perc múlva a DevOps-projektek irányítópultja megjelenik az Azure Portalon. A folyamat megtekintéséhez válassza ki a projekt nevét. Előfordulhat, hogy frissítenie kell az oldalt. Egy minta IoT Edge-alkalmazás van beállítva egy tárházban az Azure DevOps-szervezet, egy build végrehajtása, és az alkalmazás telepítve van az IoT Edge-eszközön. Ez az irányítópult betekintést nyújt a kódtárházba, a CI/CD-folyamatba és az Azure-beli alkalmazásba.

   ![Alkalmazás megtekintése az Azure Portalon](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps-projektek git-tárházat hoztak létre a projekthez az Azure-tárházakban. Ebben a szakaszban megtekintheti a tárházat, és kódmódosításokat hajtvégre az alkalmazásban.

1. A projekthez létrehozott tárház hoz a projekt irányítópultjának Tárolók parancsában válassza az **Adattárak** lehetőséget.  

   ![Az Azure-adattárakban létrehozott tárház megtekintése](./media/how-to-devops-project/view-repositories.png)

2. A következő lépések végighaladnak a webböngésző segítségével a kód módosításhoz. Ha ehelyett helyileg szeretné klónozni a tárházat, válassza a **Klónozás** lehetőséget az ablak jobb felső részén. A megadott URL-cím használatával klónozhatja a Git-tárházat a Visual Studio-kódban vagy a kívánt fejlesztőeszközben.

3. A tárház már tartalmazza a **FilterModule** nevű modul kódját a létrehozási folyamatban kiválasztott alkalmazásnyelv alapján. Nyissa meg a **modules/FilterModule/module.json** fájlt.

   ![A module.json fájl megnyitása az Azure Repos-ban](./media/how-to-devops-project/open-module-json.png)

4. Figyelje meg, hogy ez a fájl [az Azure DevOps buildváltozóit](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) használja a **verzióparaméterben.** Ez a konfiguráció biztosítja, hogy a modul új verziója jön létre minden alkalommal, amikor egy új build fut.

## <a name="examine-the-cicd-pipeline"></a>A CI/CD-folyamat vizsgálata

Az előző szakaszokban az Azure DevOps-projektek automatikusan konfigurálta a teljes CI/CD-folyamatot az IoT Edge-alkalmazáshoz. Most fedezze fel és szabja testre a folyamatot, ha szükséges. Az alábbi lépésekkel ismerkedjen meg az Azure DevOps buildelési és kiadási folyamataival.

1. A DevOps-projekt buildfolyamatainak megtekintéséhez válassza a **Folyamatok létrehozása lehetőséget** a projekt irányítópultjának menüjében. Ez a hivatkozás megnyit egy böngészőlapot, és az Azure DevOps build-folyamat az új projekthez.

   ![Buildfolyamatok megtekintése az Azure-folyamatokban](./media/how-to-devops-project/view-build-pipelines.png)

2. Válassza a **Szerkesztés** elemet.

    ![Build-folyamat szerkesztése](media/how-to-devops-project/click-edit-button.png)

3. A megnyíló panelen megvizsgálhatja a buildfolyamat futásakor bekövetkező feladatokat. A buildfolyamat különböző feladatokat hajt végre, például a Git-tárházból származó források lekérése, Az IoT Edge modullemezképek létrehozása, az IoT Edge-modulok lekérése egy tároló beállításjegyzékbe, és az üzembe helyezésekhez használt kimenetek közzététele. Ha többet szeretne megtudni az Azure IoT Edge-feladatokról az Azure DevOps-ban, olvassa [el az Azure-folyamatok konfigurálása a folyamatos integrációhoz című témakört.](how-to-ci-cd.md#configure-continuous-integration)

4. A **folyamat** részleteinek megnyitásához válassza ki a folyamat fejlécét a buildfolyamat tetején. Módosítsa a buildelési folyamat nevét egy közérthetőbb névre.

   ![A folyamat részleteinek szerkesztése](./media/how-to-devops-project/edit-build-pipeline.png)

5. Válassza **& várólista mentése**, majd a **Mentés**lehetőséget.

6. Válassza **az Eseményindítók lehetőséget** a buildelési folyamat menüből. DevOps-projektek automatikusan létrehozott egy CI-eseményindítót, és minden véglegesítést a tárház hoz egy új buildet.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

7. Válassza a **Megtartást**. A forgatókönyvtől függően megadhatja, hogy bizonyos számú buildet megtartjon vagy eltávolítson.

8. Válassza **az Előzmények**lehetőséget. Az előzménypanel a build legutóbbi változásainak naplózási nyomvonalát tartalmazza. Az Azure Pipelines nyomon követi a buildelőfolyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

9. Ha végzett a buildfolyamat feltárásával, keresse meg a megfelelő kiadási folyamatot. Válassza **a Folyamatok csoportban a Kiadások** lehetőséget, majd a **Szerkesztés** lehetőséget a folyamat részleteinek megtekintéséhez. **Pipelines**

    ![Kiadási folyamat megtekintése](media/how-to-devops-project/release-pipeline.png)

10. Az **Összetevők** alatt válassza az **Elvetést**. A összetevő által figyelő forrás az előző lépésekben megvizsgált buildfolyamat kimenete.

11. A **Legördülő** ikon mellett válassza a Villámcsapásnak tűnő **Folyamatos üzembe helyezési eseményindítót.** Ez a kiadási folyamat engedélyezte az eseményindítót, amely minden alkalommal futtat egy központi telepítést, amikor új buildösszetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy az üzemelő példányok manuális végrehajtást igényelhessenek.  

12. A kiadási folyamat menüjében válassza a **Feladatok** lehetőséget, majd válassza ki a **fejlesztési** fázist a legördülő listából. DevOps-projektek létrehozott egy kiadási szakaszt az Ön számára, amely létrehoz egy IoT hub, létrehoz egy IoT Edge-eszközt a hubban, telepíti a mintamodult a build-folyamatból, és egy virtuális gépet rendel az IoT Edge-eszközként való futtatáshoz. Ha többet szeretne megtudni az Azure IoT Edge CD-s feladatairól, olvassa [el az Azure Pipelines konfigurálása folyamatos üzembe helyezéshez](how-to-ci-cd.md#configure-continuous-deployment)című témakört.

    ![Folyamatos üzembe helyezési feladatok megtekintése](media/how-to-devops-project/dev-release.png)

13. A jobb oldalon válassza a **Kiadások megtekintése**lehetőséget. Ebben a nézetben a kiadások előzményei jelennek meg.

14. A kiadás nevének kiválasztásával további információkat tekinthet meg róla.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti az Azure App Service-t és más kapcsolódó erőforrásokat, amelyeket akkor hozott létre, amikor már nincs rájuk szüksége. Használja a **Törlés** funkciót a DevOps-projektek irányítópulton.

## <a name="next-steps"></a>További lépések

* Ismerje meg az Azure IoT Edge feladatait az Azure DevOps-on [folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge-ben](how-to-ci-cd.md)
* Ismerje meg az IoT Edge üzembe helyezését [az IoT Edge-telepítések megismerése egyetlen eszközökön vagy nagy méretekben](module-deployment-monitoring.md)
* Az [IoT Edge-modulok nagy méretekben történő](how-to-deploy-monitor.md)telepítésével és figyelésével végigvezeti a központi telepítés lépéseit.
