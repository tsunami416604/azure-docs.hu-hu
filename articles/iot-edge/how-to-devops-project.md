---
title: CI/CD-folyamat az Azure DevOps Projects - az Azure IoT Edge segítségével |} A Microsoft Docs
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. Ez segít az Azure IoT Edge néhány gyors lépéssel tetszőleges alkalmazást elindíthat.
author: shizn
manager: ''
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 57279a4e92b1f42505003f12d41c8203aa5603d4
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54881869"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Az IoT Edge-hez az Azure DevOps Projects (előzetes verzió) segítségével a CI/CD-folyamat létrehozása

Folyamatos integrációs (CI) és az IoT Edge-alkalmazás folyamatos továbbítás (CD) beállítása az DevOps Projects segítségével. DevOps-projektek egyszerűbbé teszi a kezdeti konfigurálása az Azure-folyamatok készítése és kiadása folyamat.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

DevOps-projektek az Azure DevOps hoz létre a CI/CD-folyamat. Hozzon létre egy új Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. A DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása**, és keressen **DevOps Projects**.  

1.  Kattintson a **Létrehozás** gombra.

## <a name="create-a-new-application-pipeline"></a>Hozzon létre egy új alkalmazás folyamatot 

1. Az Azure IoT Edge modul(ok) megírható [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) és [Java](tutorial-java-module.md). Válassza ki a választott nyelven egy új alkalmazás elindításához: **.NET**, **Node.js**, **Python**, **C**, vagy **Java**. A folytatáshoz kattintson a **Tovább** gombra.

   ![Válassza ki a nyelvet és a egy új alkalmazás létrehozása](./media/how-to-devops-project/select-language.png)

2. Válassza ki **egyszerű IoT (előzetes verzió)** az alkalmazási keretrendszer, és válassza ki, **tovább**.

   ![Egyszerű IoT keretrendszer kiválasztása](media/how-to-devops-project/select-iot.png)

3. Válassza ki **IoT Edge** az Azure-szolgáltatás, amely telepíti az alkalmazást, és válassza ki, **tovább**.

   ![Válassza ki az IoT Edge szolgáltatás](media/how-to-devops-project/select-iot-edge.png)

4. Hozzon létre egy új ingyenes Azure DevOps szervezetet, vagy válasszon egy már meglévő szervezet.

   1. Adja meg a projekt nevét. 

   2. Válassza ki az Azure DevOps-szervezetben. Ha egy már meglévő szervezet nem rendelkezik, válassza ki a **további beállítás** hozzon létre egy újat. 

   3. Válassza ki az Azure-előfizetését.

   4. A projekt neve által létrehozott IoT Hub nevét használja, vagy adja meg a saját.

   5. Válassza ki **további beállítás** konfigurálása az Azure-erőforrások, amelyek a DevOps Projects hoz létre az Ön nevében.

   6. Válassza ki **kész** a projekt létrehozásának befejezéséhez. 

   ![Nevezze el, és az alkalmazás létrehozása](media/how-to-devops-project/select-devops.png)

Néhány perc múlva a DevOps Projects irányítópult jelenik meg az Azure Portalon. Válassza ki a projekt nevére, a folyamat előrehaladását. Szüksége lehet, hogy frissítse az oldalt. Egy mintául szolgáló IoT Edge-alkalmazás be van állítva, a szervezet Azure DevOps-tárházban, build hajtja végre, és az alkalmazás központi telepítése az IoT Edge-eszközön. Ezt az irányítópultot a kódtárat a CI/CD-folyamat és az Azure-ban az alkalmazás rálátást biztosít.

   ![A DevOps-portál alkalmazás megtekintése](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

DevOps-projektek a projekt Git-tárházat az Azure-Adattárakkal hoz létre. Ebben a szakaszban megtekintheti a tárházban, és módosítsa a kódokat az alkalmazáshoz.

1. Nyissa meg a projekt létrehozása az adattárhoz, válassza ki a **Tárházak** a projekt-irányítópult menüjében.  

   ![Nézet tárház jön létre az Azure-Adattárakkal](./media/how-to-devops-project/view-repositories.png)

2. Az alábbi lépések bemutatják, hogy a kódon módosítani kellene a webböngésző használatával. Ha a tárház helyi inkább klónozásához, jelölje be **Klónozás** a felső, az ablak jobb. A megadott URL-cím használata a Visual Studio Code vagy az előnyben részesített fejlesztési eszköz a Git-tárház klónozásához. 

3. Az adattár már tartalmazza a kódot egy nevű modul **SampleModule** a létrehozási folyamat során választott alkalmazás nyelv alapján. Nyissa meg a **modules/SampleModule/module.json** fájlt.

   ![Az Azure-Adattárakkal nyílt module.json fájl](./media/how-to-devops-project/open-module-json.png)

4. Válassza ki **szerkesztése**, majd hajtsa végre a módosítás `"version"` alatt a `"tag"`. Például módosíthatja, hogy `"version": "${BUILD_BUILDID}"` használandó [Azure DevOps hozhat létre változókat](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) az Azure IoT Edge-modul képcímke részeként.

   ![Verzió, fogadja el a build változók szerkesztése](media/how-to-devops-project/update-module-json.png)

5. Válassza ki **véglegesítése**, majd mentse a módosításokat.

6. A böngészőben lépjen vissza a DevOps Projects-irányítópultot az Azure Portalon. Látni fogja, hogy folyamatban van egy build. A módosítások automatikusan gyártja és a CI/CD-folyamat segítségével telepítve.

    ![A folyamatban lévő állapot megtekintése](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Vizsgálja meg a CI/CD-folyamat

Az előző szakaszokban Azure DevOps Projects egy teljes CI/CD-folyamat az IoT Edge-alkalmazás automatikusan konfigurálva. Majd, hogy a buildelési folyamat tesztelte véglegesíti a módosításokat, egy fájlt. Most ismerje meg, és testre szabhatja a folyamat igény szerint. A következő lépésekkel Ismerkedjen meg az Azure DevOps-buildelési és kiadási folyamatok.

1. A DevOps-projekt a build folyamatok megtekintéséhez jelölje ki **hozhat létre folyamatokat** a projekt-irányítópult menüjében. Ez a hivatkozás megnyílik egy böngészőlap, és az Azure DevOps folyamat az új projekt létrehozása.

   ![Nézet Azure folyamatokban folyamatok létrehozása](./media/how-to-devops-project/view-build-pipelines.png)

2. Válassza a **Szerkesztés** elemet.

    ![Felépítési folyamat szerkesztése](media/how-to-devops-project/click-edit-button.png)

3. A megnyíló panelen ellenőrizheti a feladatokat, amelyek akkor jelentkeznek, amikor a buildelési folyamat fut. A létrehozási folyamat hajtja végre különböző feladatokat, például az adatforrások beolvasása a Git-adattárból, IoT Edge modul rendszerképek létrehozását, IoT Edge-modulok leküldése egy tároló-beállításjegyzéket, és a közzététel kiírja a telepítéshez használt. Az Azure IoT Edge-feladatok az Azure DevOps kapcsolatos további információkért lásd: [folyamatos integráció konfigurálása Azure folyamatok](how-to-ci-cd.md#configure-continuous-integration).

4. Válassza ki a **folyamat** fejléc nyissa meg a folyamat részletei a buildelési folyamat elején. Módosítsa a buildelési folyamat nevét a tulajdonságánál.

   ![A folyamat részleteinek szerkesztése](./media/how-to-devops-project/edit-build-pipeline.png)

5. Válassza ki **várólistára & mentése**, majd válassza ki **mentése**.

6. Válassza a buildelési folyamat menüben **eseményindítók** menüjében. A DevOps Projects automatikusan létrejön egy CI eseményindító, és a tárházban minden véglegesítéshez elindul egy új létrehozást.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

7. Válassza a **Megtartást**. A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

8. Válassza ki **előzmények**. Az Előzmények panelen auditnaplót, a build útmutató legutóbbi módosításait tartalmazza. Azure folyamatok nyomon követi, hogy az összeállítási folyamat végzett módosításokat, és lehetővé teszi, hogy verziójának összehasonlítása.

9. Ha befejezte a buildelési folyamat felfedezése keresse meg a megfelelő kiadási folyamathoz. Válassza ki **kiadásokban** alatt **folyamatok**, majd **szerkesztése** folyamat részleteinek megtekintéséhez.

    ![Nézet kibocsátási folyamatok](media/how-to-devops-project/release-pipeline.png)

10. Az **Összetevők** alatt válassza az **Elvetést**. A forrás, az összetevő figyeli, a kimenet a buildelési folyamat, az előző lépésekben vizsgálni. 

11. Mellett a **Drop** ikonra, válassza ki a **a folyamatos készregyártás eseményindítója** néz ki egy Villám. A kibocsátási folyamat engedélyezte az eseményindító, amely futtatja a központi telepítés minden alkalommal, amikor egy új buildösszetevő áll rendelkezésre. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását.  

12. A kibocsátási folyamat menüben válassza **feladatok** majd válassza ki a **fejlesztési** fázis a legördülő listából. A DevOps Projects létrehoz egy kiadási fázisában, amely létrehoz egy IoT hubot, hoz létre az IoT Edge-eszköz az adott hub, üzembe helyezi a mintamodul az összeállítási folyamatból, és kiosztja a virtuális gép futtatása az IoT Edge-eszköz. További információ az Azure IoT Edge-feladatok a CD-re, lásd: [Azure folyamatok konfigurálása a folyamatos üzembe helyezéshez](how-to-ci-cd.md#configure-continuous-deployment).

   ![Folyamatos üzembe helyezés feladatok megtekintése](media/how-to-devops-project/dev-release.png)

13. A jobb oldalon válassza ki a **verziók megtekintéséhez**. Ebben a nézetben a kiadások előzményei jelennek meg.

14. Válassza ki a nevét, a kiadás további információinak megtekintéséhez.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure App Service és a többi kapcsolódó erőforrást, ha többé már nincs szükség rájuk létrehozott törölheti. Használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések
* További információk a feladatokat az Azure IoT Edge a az Azure DevOps [folyamatos integrációt és folyamatos üzembe helyezés az Azure IoT Edge-ben](how-to-ci-cd.md)
* Megismerheti az IoT Edge üzemelő példány [ismertetése IoT Edge-telepítések egyetlen eszközök vagy ipari méretekben](module-deployment-monitoring.md)
* Lépésről lépésre bemutatjuk létrehozása, frissítése vagy törlése a központi telepítés [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md).
