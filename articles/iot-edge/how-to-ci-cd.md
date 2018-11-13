---
title: Az Azure IoT Edge folyamatos integrációs és folyamatos üzembe helyezés |} A Microsoft Docs
description: A folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge – áttekintés
author: shizn
manager: ''
ms.author: xshi
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a110c0a938e56c8ac276e0efed22ea3af23f111a
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578535"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge-ben

Ez a cikk bemutatja, hogyan lehet a a folyamatos integráció és folyamatos üzembe helyezési funkcióival az Azure DevOps-szolgáltatásokkal és a Microsoft Team Foundation Server (TFS) hozhat létre, tesztelheti és gyorsan és hatékonyan helyezhetnek üzembe alkalmazásokat az Azure IoT Edge-ben. 

Ez a cikk azt ismerteti, hogyan lehet:
* Hozzon létre, és ellenőrizze a minta az IoT Edge-megoldás.
* Az Azure IoT Edge-bővítmény telepítése az Azure fejlesztők és üzemeltetők számára.
* Állítsa be a folyamatos integrációs (CI) a megoldás létrehozásához.
* Állítsa be a folyamatos készregyártás (CD) a megoldás üzembe helyezése, és megtekintheti a válaszokat.

A jelen cikkben ismertetett lépések 30 percet vesz igénybe.

![CI és CD-ről](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Hozzon létre egy minta Azure IoT Edge megoldást a Visual Studio Code használatával

Ebben a szakaszban létrehoz egy mintául szolgáló IoT Edge megoldást tartalmazó egységteszteket, amely az összeállítási folyamat részeként hajthat végre. Ebben a szakaszban található útmutatást követve, előtt hajtsa végre a [egyszerre több modul a Visual Studio Code egy IoT Edge-megoldás fejlesztése](tutorial-multiple-modules-in-vscode.md).

1. A VS Code parancskatalógus, írja be, és futtassa a parancsot **Azure IoT Edge: IoT-Edge új megoldás**. Válassza ki a munkaterület-mappába, adja meg a megoldás nevét (alapértelmezés szerint ez **EdgeSolution**), és hozzon létre egy C# modul (**FilterModule**) Ez a megoldás első felhasználói modulként. Az első modulhoz meg kell adnia a Docker rendszerképadattárat is. Az alapértelmezett lemezképtárban alapul egy helyi Docker-beállításjegyzék (`localhost:5000/filtermodule`). Módosítania azt az Azure Container Registrybe (`<your container registry address>/filtermodule`) vagy a Docker Hub további folyamatos integrációhoz.

    ![Állítsa be az ACR](./media/how-to-ci-cd/acr.png)

2. A VS Code-ablak betölti az IoT Edge-megoldás munkaterülethez. Szükség esetén írja be és futtassa **Azure IoT Edge: hozzáadása IoT Edge-modul** további modulok hozzáadása. Van egy `modules` mappában egy `.vscode` mappát, és a egy központi telepítési jegyzékfájl sablon fájl a gyökérmappában. Minden felhasználó modul kód lesz a mappa almappái `modules`. A `deployment.template.json` az alkalmazásjegyzék központi telepítési sablon. Egyes paraméterek a fájlban a fog értelmezni a `module.json`, amely minden modul mappában van.

3. A minta az IoT Edge-megoldást most már készen áll. Az alapértelmezett C# modul egy függőleges vonal üzenet-modult funkcionál. Az a `deployment.template.json`, látni fogja, ez a megoldás két lehetővé tevő modulokat tartalmaz. Az üzenet akkor jöjjön létre a `tempSensor` modult, és a rendszer kell közvetlenül adatcsatornán keresztül `FilterModule`, majd az IoT hubnak küldött.

4. Ezek a projektek mentse, majd ellenőrizze azt az Azure DevOps, vagy a TFS-adattárba.
    
> [!NOTE]
> Az Azure-kódtárak használatával kapcsolatos további információkért lásd: [megosztani a kódot a Visual Studio és az Azure-Adattárakkal](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-azure-pipeline-for-continuous-integration"></a>Folyamatos integráció konfigurálása Azure folyamat
Ebben a szakaszban létre fog hozni egy build folyamatot, amely automatikusan futnak, amikor ellenőrizheti a módosításokat a minta az IoT Edge-megoldás van beállítva, és megjelenik a build naplók az Azure folyamat.

1. Jelentkezzen be az Azure DevOps-szervezet (**https://**_-fiók_**. visualstudio.com**), és nyissa meg a projekt, ha bejelölte a mintaalkalmazást.

    ![Ellenőrizze a kód](./media/how-to-ci-cd/init-project.png)

1. Látogasson el [Azure folyamat az Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) DevOps Azure Marketplace-en. Kattintson a **regisztráljon ingyenesen** , és kövesse a varázsló az Azure DevOps-szervezet és a letöltés a TFS, a bővítmény telepítéséhez.

    ![A bővítmény telepítése](./media/how-to-ci-cd/install-extension.png)

1. Az Azure DevOps, nyissa meg a **Build & Release** hub és az a **buildek** lapra, majd **+ új adatcsatorna**. Vagy, ha már rendelkezik hozhat létre folyamatokat, válassza ki a **+ új** gombra.

    ![Új adatcsatorna](./media/how-to-ci-cd/add-new-build.png)

1. Ha a rendszer kéri, válassza ki a **Azure DevOps Git** forrás típusa. Ezután válassza ki a projektet, a tárházat és a fiókiroda, ahol a kód megtalálható. Válasszon **továbbra is**.

    ![Válassza ki a git](./media/how-to-ci-cd/select-vsts-git.png)

    A **válasszon ki egy sablont** ablakban válassza a **egy üres folyamatot kezdődnie**.

    ![Válasszon ki egy sablont](./media/how-to-ci-cd/start-with-empty.png)

1. A folyamat-szerkesztőben válassza ki az ügynökkészlet. 
    
    * Ha szeretné a modulok a Linux-tárolókhoz tartozó platform amd64 hozhat létre, válassza a **üzemeltetett Ubuntu 1604**
    * Ha szeretné a modulok Windows-tárolókhoz tartozó platform amd64 hozhat létre, válassza a **Hosted VS2017** 
    * Ha szeretné a modulok a Linux-tárolókhoz tartozó platform arm32v7 összeállítása kell beállítása a saját build-ügynök telepítése kattintva a **kezelés** gombra.
    
    ![A build-ügynök konfigurálása](./media/how-to-ci-cd/configure-env.png)

1. Az ügynök feladatot, kattintson a "+" két feladat hozzáadása a buildelési folyamat. A rendszer az elsőt **Azure IoT Edge**. A másikat pedig a **összeállítása összetevők közzététele**
    
    ![Tevékenységek hozzáadása](./media/how-to-ci-cd/add-tasks.png)

1. Az első **Azure IoT Edge** feladat, frissítse a **megjelenített neve** való **modul létrehozása és leküldése**, és a a **művelet** legördülő listában válassza **És leküldéses**. Az a **Module.json fájl** szövegmezőbe elérési úton kell hozzáadnia. Válassza a **Tárolóregisztrációs adatbázis típusa**, ellenőrizze, hogy konfigurálja, és válassza ki ugyanazt a beállításjegyzékben a code(module.json). Ez a feladat létrehozása és leküldése az összes modult a megoldásban és közzététele a megadott tárolóregisztrációs adatbázisba. Ha a modul el lesz küldve különböző beállításjegyzékek, rendelkezhet több **modul létrehozása és leküldése** feladatokat. Az IoT Edge-megoldás nem áll a kód tárház gyökérkönyvtárában alatt esetben megadhat **elérési útja a peremhálózati megoldás alapvető** a builddefiníció.
    
    ![És leküldéses](./media/how-to-ci-cd/build-and-push.png)

1. A **összeállítása összetevők közzététele** feladatban kell megadni az üzembe helyezési fájl az összeállítási feladat által létrehozott. Állítsa be a **közzététele elérési út** a "config/deployment.json". Ha **elérési útja a peremhálózati megoldás alapvető** az utolsó feladatban lesz az útvonalgyökér csatlakozni. Például, ha a peremhálózati megoldás gyökér elérési útja: ". / edgesolution", akkor a **közzététele elérési út** legyen ". / edgesolution/config/deployment.json". A `deployment.json` fájl felépítési idő alatt jön létre, így biztonságosan figyelmen kívül hagyja a szövegmezőben, hogy a piros hibajelző sorokat. 

    ![Összetevők közzététele](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Nyissa meg a **eseményindítók** lapra, és kapcsolja be a **folyamatos integráció** eseményindító. Ellenőrizze, hogy az ág a kódot tartalmazó részét képezi.

    ![Az eseményindító konfigurálása](./media/how-to-ci-cd/configure-trigger.png)

    Mentse az új buildelési folyamat. Kattintson a **Mentés** gombra.


## <a name="configure-azure-pipeline-for-continuous-deployment"></a>Folyamatos üzembe helyezés konfigurálása Azure-folyamat
Ebben a szakaszban létre fog hozni egy kiadási folyamatot, amely automatikusan futnak, amikor a buildelési folyamat csökken összetevők van beállítva, és a telepítési naplók az Azure-folyamat megjelenik.

1. Az a **kiadásokban** lapra, majd **+ új adatcsatorna**. Vagy, ha már rendelkezik kiadási folyamatokat, válassza ki a **+ új** gombra.  

    ![Adja hozzá a kibocsátási folyamat](./media/how-to-ci-cd/add-release-pipeline.png)

    A **válasszon ki egy sablont** ablakban válassza a **egy üres feladat elindításához.**

    ![Az üres feladat indítása](./media/how-to-ci-cd/start-with-empty-job.png)

2. A kiadási folyamathoz szeretné inicializálni egy szakaszhoz, majd: **1. fázis**. Nevezze át a **1. fázis** való **QA** és a egy tesztkörnyezetet, kezelje azt. Egy tipikus folyamatos üzembe helyezés folyamatban több szakaszok általában létezik, még a fejlesztési és üzemeltetési eljárások alapján is létrehozhat.

    ![Szakasz létrehozása](./media/how-to-ci-cd/QA-env.png)

3. A kiadás összekapcsolása a build-összetevőket. Kattintson a **Hozzáadás** összetevők területen.

    ![Adjon hozzá összetevőket](./media/how-to-ci-cd/add-artifacts.png)  
    
    A **adjon hozzá egy összetevő oldalt**, forrástípus kiválasztása **összeállítása**. Ezután válassza ki a projektet, és létrehozott buildelési folyamat. Kattintson a **hozzáadása**.

    ![Az összetevő hozzáadása](./media/how-to-ci-cd/add-an-artifact.png)

    Nyissa meg a folyamatos készregyártás eseményindítója, így az új kiadás jön létre minden alkalommal, amikor egy új létrehozást érhető el.

    ![Az eseményindító konfigurálása](./media/how-to-ci-cd/add-a-trigger.png)

4. Navigáljon a **QA fázis** és a feladatok konfigurálása az ebben a szakaszban.

    ![QA feladatok konfigurálása](./media/how-to-ci-cd/view-stage-tasks.png)

   Üzembe helyezési feladata a platform és nagybetű nincs megkülönböztetve, ami azt jelenti, választhat **Hosted VS2017** vagy **üzemeltetett Ubuntu 1604** a a **ügynökkészlet** (vagy bármely más ügynök által felügyelt saját kezűleg). Kattintson a "+" és a egy feladat hozzáadása.

    ![Tevékenységek hozzáadása a QA](./media/how-to-ci-cd/add-task-qa.png)

5. Az Azure IoT Edge-feladatban, keresse meg a **művelet** legördülő listában válassza **üzembe helyezés az IoT Edge-eszköz**. Válassza ki a **Azure-előfizetés** bemeneti és a **az IoT Hub nevét**. Megadhat egy IoT Edge **üzemelő példány azonosítója** és az üzembe helyezés **prioritású**. Azt is beállíthatja egy vagy több eszközökre történő telepítéséhez. Ha telepíti, akkor **több eszközön**, meg kell adnia az eszköz **feltétel cél**. A cél feltétel nem egyezik meg az Edge-eszközök az IoT Hub egy szűrőt. Ha azt szeretné, eszköz-címkék használata a feltételt, az IoT Hub ikereszköz a megfelelő eszközök címkék frissíteni szeretné. Fel, hogy több IoT Edge eszközök ellátott, a "qa", majd a feladat konfigurációja az alábbi képernyőképen látható módon kell lennie. 

    ![QA üzembe helyezése](./media/how-to-ci-cd/deploy-to-qa.png)

    Mentse az új kiadási folyamatot. Kattintson a **Mentés** gombra. Majd **folyamat** térhet vissza a folyamatot.

6. A második szakasz olyan éles környezetben. Szeretne hozzáadni egy új "ÉLES" szakaszt, is egyszerűen csak klónozza a "QA" szakaszban és a klónozott fázis átnevezése **PROD**,

    ![Szakasz klónozása](./media/how-to-ci-cd/clone-stage.png)

7. Konfigurálja a tevékenységek az éles környezetben. Fel, hogy több IoT Edge 'prod', a feladat-konfigurációkban frissítheti az "éles", és állítsa be az üzemelő példány azonosítója, mint "üzembe helyezése – éles" Célfeltétel ellátott eszközöket. Kattintson a **Mentés** gombra. Majd **folyamat** térhet vissza a folyamatot.
    
    ![Éles üzembe helyezése](./media/how-to-ci-cd/deploy-to-prod.png)

7. Jelenleg a buildösszetevő is kiváltódik folyamatosan **QA** szakaszban, majd **PROD** szakaszban. De általában néhány vizsgálati eset a QA eszközökön, és manuálisan integrálnia kell hagynia a bits. Később a bits ÉLES környezetbe telepíti. Állítsa be az ÉLES szakaszt a következő: jóváhagyás.

    1. Nyissa meg **központi telepítés előtti feltételek** beállítás panel.

        ![Nyissa meg a központi telepítés előtti feltételek](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Állítsa be **engedélyezve** a **központi telepítés előtti jóváhagyások**. És töltse ki a **jóváhagyók** bemeneti. Ezután kattintson a **Save** (Mentés) gombra.
    
        ![Beállítása feltételek](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. Most a kibocsátási folyamat a következőképpen be van állítva.

    ![Kibocsátási folyamatok](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>IoT Edge CI/CD kérdezze meg a build és a folyamatok felszabadítása

Ebben a szakaszban a build, hogy működik a CI/CD-folyamat vált. Ellenőrizze az eredményt az Azure DevOps-portálon. 

1. A fordítási feladatot indításához küldje le a véglegesítés forráskódraktárban vagy is aktiválása manuálisan. Is aktiválhatja a fordítási feladatot a buildelési folyamat kattintva a **várólista** gombra az alábbi képernyőképen látható módon.

    ![Manuális eseményindító](./media/how-to-ci-cd/manual-trigger.png)

2. Ha a buildelési folyamat sikeresen befejeződött, aktivál egy üzembehelyezési **QA** szakaszban. Keresse meg a folyamat-naplók létrehozása, és megjelenik a következő.

    ![Buildnaplók](./media/how-to-ci-cd/build-logs.png)

3. A sikeres üzembe helyezés **QA** szakaszban lép működésbe a jóváhagyó értesítést. Keresse meg a kibocsátási folyamat, láthatja, hogy a következő. 

    ![Jóváhagyásra váró elemek](./media/how-to-ci-cd/pending-approval.png)


4. Miután a jóváhagyó jóváhagyja ezt a módosítást, is üzembe helyezhető **PROD**.

    ![Éles üzembe helyezése](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>További lépések

* Megismerheti az IoT Edge üzemelő példány [ismertetése IoT Edge-telepítések egyetlen eszközök vagy ipari méretekben](module-deployment-monitoring.md)
* Lépésről lépésre bemutatjuk létrehozása, frissítése vagy törlése a központi telepítés [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md).
