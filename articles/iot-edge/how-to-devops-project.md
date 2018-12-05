---
title: CI/CD-folyamat létrehozása az IoT Edge-hez az Azure DevOps Projects (előzetes verzió) segítségével |} A Microsoft Docs
description: Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. Ez segít az Azure IoT Edge néhány gyors lépéssel tetszőleges alkalmazást elindíthat.
author: shizn
manager: ''
ms.author: xshi
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a394951de833ee8c01bb4a385c5ebb126ebd3534
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882592"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Az IoT Edge-hez az Azure DevOps Projects (előzetes verzió) segítségével a CI/CD-folyamat létrehozása

Folyamatos integrációs (CI) és az IoT Edge-alkalmazás folyamatos továbbítás (CD) beállítása az DevOps Projects segítségével. DevOps-projektek egyszerűbbé teszi a kezdeti konfigurálása az Azure-folyamatok készítése és kiadása folyamat.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

DevOps-projektek az Azure DevOps hoz létre a CI/CD-folyamat. Hozzon létre egy új Azure DevOps szervezetet, vagy használjon egy már meglévő szervezet. A DevOps Projects is létrehoz az Azure-erőforrások tetszőleges Azure-előfizetésben.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása** ikonra a bal oldali navigációs sávon, és keressen a **DevOps Projects**.  

1.  Kattintson a **Létrehozás** gombra.

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Az Azure IoT Edge modul(ok) megírható [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) és [Java](tutorial-java-module.md). Válassza ki a választott nyelven egy új alkalmazás elindításához. Ennek megfelelően kiválaszthatja **.NET**, **Node.js**, **Python**, **C**, vagy **Java**, és kattintson a **Tovább**.

    ![Nyelv kiválasztása](./media/how-to-devops-project/select-language.png)

2. Válassza ki **egyszerű IoT (előzetes verzió)**, és kattintson a **tovább**.

    ![Válassza ki az IoT](media/how-to-devops-project/select-iot.png)

3. Válassza ki **IoT Edge**, és kattintson a **tovább**.

    ![Válassza ki az IoT Edge](media/how-to-devops-project/select-iot-edge.png)

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Hozzon létre egy új ingyenes Azure DevOps szervezetet, vagy válasszon egy már meglévő szervezet.

    a. Válasszon egy nevet a projektnek. 

    b. Válassza ki az Azure-előfizetést és helyet, válasszon egy nevet az alkalmazáshoz, és válassza **kész**.  

    ![Válassza ki a fejlesztés és üzemeltetés](media/how-to-devops-project/select-devops.png)

1. Néhány perc múlva a DevOps Projects irányítópult jelenik meg az Azure Portalon. Egy mintául szolgáló IoT Edge-alkalmazás be van állítva, a szervezet Azure DevOps-tárházban, build hajtja végre, és az alkalmazás központi telepítése az IoT Edge-eszközön. Ezt az irányítópultot a kódtárat a CI/CD-folyamat és az Azure-ban az alkalmazás rálátást biztosít.

    ![DevOps-portál](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Projects egy Git-tárház létrehozott Azure-kódtárak és a GitHub. A tárház megtekintéséhez, és módosítsa a kódokat az alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1. Az irányítópult bal oldalán, a DevOps Projects, jelölje be a hivatkozást a **fő** ágat.  
Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. Az adattárklón URL-címének megtekintéséhez válassza a **Klónozás** lehetőséget a böngésző jobb felső részén. A Git-tárház a VS Code vagy egyéb, Ön által választott eszközökkel klónozhat. A következő néhány lépést a webböngésző segítségével győződjön meg arról, és véglegesítési kód módosítja a közvetlenül a master ággal.

    ![Klónozás](media/how-to-devops-project/clone.png)

1. A bal oldalon, a böngésző, nyissa meg a **modules/FilterModule/module.json** fájlt.

1. Válassza ki **szerkesztése**, majd hajtsa végre a módosítás `"version"` alatt a `"tag"`. Például módosíthatja, hogy `"version": "${BUILD_BUILDID}"` használandó [Azure DevOps hozhat létre változókat](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) az Azure IoT Edge-modul képcímke részeként.

    ![Szerkesztés](media/how-to-devops-project/update-module-json.png)

1. Válassza ki **véglegesítése**, majd mentse a módosításokat.

1. A böngészőben nyissa meg az Azure DevOps Project irányítópultot.  Látni fogja, hogy folyamatban van egy build. A módosítások automatikusan gyártja és a CI/CD-folyamat segítségével telepítve.

    ![Folyamatban](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Vizsgálja meg a CI/CD-folyamat

Az előző lépésben az Azure DevOps Projects automatikusan konfigurálva egy teljes CI/CD-folyamat az IoT Edge-alkalmazás. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A következő lépésekkel Ismerkedjen meg az Azure DevOps-buildelési és kiadási folyamatok.

1. Válassza ki a DevOps Projects irányítópult tetején lévő **hozhat létre folyamatokat**.  
Ez a hivatkozás megnyílik egy böngészőlap, és az Azure DevOps folyamat az új projekt létrehozása.

1. Válassza a **Szerkesztés** elemet.

    ![Szerkesztés](media/how-to-devops-project/click-edit-button.png)

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat. A build hajt végre különböző feladatokat, például a központi telepítésekhez használt források a Git-adattárból, IoT Edge modul rendszerképek létrehozását, IoT Edge-modulok leküldése és közzétételéhez használt kimenetek beolvasása. Többet is megtudni az Azure IoT Edge-feladatok folyamatos integrációhoz, letöltheti [folyamatos integráció konfigurálása Azure folyamatok](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-integration).

    ![CI-feladatok](media/how-to-devops-project/ci.png)

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
Az a **előzmények** panelen láthatja, hogy az útmutató legutóbbi módosításait build auditnapló.  Azure folyamatok nyomon követi, hogy az összeállítási folyamat végzett módosításokat, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válassza az **Eseményindítókat**. A DevOps Projects automatikusan létrejön egy CI eseményindító, és a tárházban minden véglegesítéshez elindul egy új létrehozást.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

1. Válassza ki **kiadási** alatt **folyamatok**. DevOps-projektek az Azure IoT Edge-ben üzembe helyezett megoldások kezelése kiadási folyamatot hoz létre.

    ![Kibocsátási folyamatok](media/how-to-devops-project/release-pipeline.png)

1. Válassza a **Szerkesztés** elemet. A kiadási folyamathoz egy folyamatot, amely meghatározza a kibocsátási folyamat tartalmazza.  

1. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépésekben megvizsgált buildelési folyamat létrehozza a munkadarabhoz használt kimenetet. 

1. Mellett a **Drop** ikonra, válassza ki a **a folyamatos készregyártás eseményindítója**.  
A kibocsátási folyamat rendelkezik egy engedélyezett CD eseményindító, amely futtatja a központi telepítés minden alkalommal, amikor egy új buildösszetevő áll rendelkezésre. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását.  

1. A bal oldalon válassza ki a **feladatok**. A feladatok olyan tevékenységek, amely végrehajtja a központi telepítési folyamat. Ebben a példában a modul lemezképek központi telepítése az Azure IoT Edge-feladat létrehozása. Többet is megtudni az Azure IoT Edge-feladatok a CD-ről, letöltheti [Azure folyamatok konfigurálása a folyamatos üzembe helyezéshez](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-deployment).

    ![CD](media/how-to-devops-project/dev-release.png)

1. A jobb oldalon válassza ki a **verziók megtekintéséhez**. Ebben a nézetben a kiadások előzményei jelennek meg.

1. A kiadások egyik melletti három pontra (...), majd válassza ki és **nyílt**.  
Ismerje meg, például a kiadás összegzését, a társított munkaelemekhez és a tesztek a több menük találhatók meg.

1. Válassza a **Véglegesítéseket**. Ez a nézet megjeleníti az adott központi telepítési társított kód véglegesítéseket. 

1. Válassza a **Naplókat**. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure App Service és a többi kapcsolódó erőforrást, ha többé már nincs szükség rájuk létrehozott törölheti. Használja a **törlése** funkciókat a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések
* További információk a feladatokat az Azure IoT Edge a az Azure DevOps [folyamatos integrációt és folyamatos üzembe helyezés az Azure IoT Edge-ben](how-to-ci-cd.md)
* Megismerheti az IoT Edge üzemelő példány [ismertetése IoT Edge-telepítések egyetlen eszközök vagy ipari méretekben](module-deployment-monitoring.md)
* Lépésről lépésre bemutatjuk létrehozása, frissítése vagy törlése a központi telepítés [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md).
