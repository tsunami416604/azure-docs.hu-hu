---
title: Folyamatos integráció és folyamatos üzembe helyezés – Azure IoT Edge |} A Microsoft Docs
description: Folyamatos integráció és folyamatos üzembe helyezés – Azure IoT Edge segítségével az Azure DevOps, Azure-folyamatok beállítása
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 196d08f47ddfdbb86b8e96ae0e5ca3d3e3e5917e
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886764"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge-ben

Egyszerűen az Azure IoT Edge-alkalmazásokkal az Azure folyamatokban a beépített Azure IoT Edge-feladatok fogadnak el fejlesztési és üzemeltetési. Ez a cikk bemutatja, hogyan segítségével a folyamatos integráció és folyamatos üzembe helyezési funkcióival az Azure-folyamatok létrehozása, tesztelése és gyorsan és hatékonyan helyezhetnek üzembe alkalmazásokat az Azure IoT Edge-ben. 

Ebből a cikkből megismerheti, hogyan használható a beépített Azure IoT Edge-feladatok Azure folyamatokhoz két folyamatot az IoT Edge-megoldás létrehozásához. Az első a programkód szükséges, és létrehozza a megoldást, a modul rendszerképeket küldhetne a tárolóregisztrációs adatbázis és a egy manifest nasazení létrehozása. A második telepíti a modulokat célzott IoT Edge-eszközökön.  

![Diagram – a CI és CD ágak fejlesztési és termelési célra](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-Adattárakkal adattár. Ha még nincs fiókja, akkor az [hozzon létre egy új Git-adattárat a projekt](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Egy IoT Edge-megoldás véglegesítve, és leküldte a tárházhoz. Ha szeretne létrehozni a teszteléshez, ez a cikk egy új mintamegoldást, kövesse a [fejlesztése és hibakeresése a Visual Studio Code modulok](how-to-vs-code-develop-module.md) vagy [fejlesztése és hibakeresése C# modulok Visual Studióban](how-to-visual-studio-develop-csharp-module.md).
   * Ebben a cikkben szüksége a megoldás mappát az IoT Edge-sablonok, a Visual Studio Code-ot vagy a Visual Studio által létrehozott. Nem kell létrehozása, leküldéses, üzembe helyezése és hibakeresése ezt a kódot, a folytatás előtt. Kell beállítani ezeket a folyamatokat Azure folyamatokban. 
   * Ha egy új megoldás hoz létre, klónozza először a tárház helyi. Ezután amikor a megoldás létrehoz lehet váltani, közvetlenül a tárház mappában hozza létre. Egyszerűen véglegesítse és küldje le az új fájlok onnan. 
* Egy tárolóregisztrációs adatbázisba, ahol küldhet képek modul. Használhat [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy egy külső beállításjegyzék. 
* Az aktív [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) a legalább a külön tesztelési és éles üzembe helyezési fázisok tesztelési IoT Edge-eszközökön. A rövid útmutató cikkek az IoT Edge-eszköz létrehozásához kövesse [Linux](quickstart-linux.md) vagy [Windows](quickstart.md)


Az Azure-kódtárak használatával kapcsolatos további információkért lásd: [megosztani a kódot a Visual Studio és az Azure-Adattárakkal](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Folyamatos integráció konfigurálása
Ebben a szakaszban létrehoz egy új build folyamatot. Állítsa be a folyamat során, módosítania kellene a minta az IoT Edge-megoldás beadása és közzététele a build-naplók automatikus futtatásához.

>[!NOTE]
>Ez a cikk az Azure DevOps vizuális Tervező használja. Mielőtt végrehajtaná a jelen szakaszban ismertetett lépéseket, kapcsolja ki az új YAML folyamat létrehozása élményt biztosít az előzetes verziójú funkció. 
>1. Az Azure DevOps, jelölje be a profil ikonjára, majd válassza ki **előzetes verziójú funkciók**.
>2. Kapcsolja be **új YAML folyamat-létrehozási folyamatának** ki. 
>
>További információkért lásd: [buildelési folyamat létrehozása](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Jelentkezzen be az Azure DevOps-szervezet ( **https://dev.azure.com/{your szervezet} /**), és nyissa meg a projekt, amely az IoT Edge-megoldás adattár tartalmazza.

   Ebben a cikkben létrehozott egy tárház nevű **IoTEdgeRepo**. A tárház tartalmaz **IoTEdgeSolution** amely rendelkezik a kódot egy modul nevű **filtermodule**. 

   ![Nyissa meg a DevOps-projekt](./media/how-to-ci-cd/init-project.png)

2. Keresse meg a projekt Azure folyamatok. Nyissa meg a **buildek** lapot, és válasszon **új adatcsatorna**. Vagy, ha már rendelkezik hozhat létre folyamatokat, válassza ki a **új** gombra. Válassza a **új buildelési folyamat**.

    ![Új buildfolyamat létrehozása](./media/how-to-ci-cd/add-new-build.png)

3. Kövesse az utasításokat a folyamat létrehozásához. 

   1. Adja meg az új buildelési folyamat az adatforrások információit. Válassza ki **Azure Git-Adattárakkal** forrásaként, majd válassza ki a projektet, a tárházat és a fiókiroda, ahol az IoT Edge-megoldás kód megtalálható. Ezután válassza ki **Folytatás**. 

      ![Válassza ki az adatcsatorna forrása](./media/how-to-ci-cd/pipeline-source.png)

   2. Válassza ki **üres feladat** sablon helyett. 

      ![Kezdje egy üres folyamatot](./media/how-to-ci-cd/start-with-empty.png)

4. Ha a folyamat létrejött, ekkor megnyílik a folyamatszerkesztőt. A folyamat leírását válassza a megfelelő ügynökkészlet a célplatformnak megfelelően: 
    
    * Ha szeretné a modulok a Linux-tárolókhoz tartozó platform amd64 hozhat létre, válassza a **üzemeltetett Ubuntu 1604**

    * Ha szeretné a modulok Windows 1809 tárolókhoz platform amd64 hozhat létre, akkor [állítsa be a helyi Windows-ügynök](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

    * Ha szeretné a modulok a Linux-tárolókhoz tartozó platform arm32v7 hozhat létre, akkor [Linux helyi ügynök beállítása](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
    ![Build ügynökkészlet konfigurálása](./media/how-to-ci-cd/configure-env.png)

5. A folyamat előre konfigurált tartalmaz egy nevű feladatot **ügynöki feladat 1**. Válassza a pluszjelet (**+**) három feladatok hozzáadása a feladathoz: **Az Azure IoT Edge** kétszer, és **összeállítása összetevők közzététele** után. (A neve, az egyes feladatok megtekintéséhez fölé a **Hozzáadás** gombra.)

   ![Az Azure IoT Edge-feladat hozzáadása](./media/how-to-ci-cd/add-iot-edge-task.png)

   Összes három hozzáadja a tevékenységeket, amikor az ügynöki feladat a következő példához hasonlóan néz ki:
    
   ![A létrehozási folyamat három feladatok](./media/how-to-ci-cd/add-tasks.png)

6. Válassza ki az első **Azure IoT Edge** feladat szerkesztheti. Ez a feladat buildek-a célplatformot a megoldás összes modult, hogy megad, akkor állít elő a **deployment.json** fájlt, amely arra kéri az IoT Edge-eszközök konfigurálása a központi telepítést.

   * **Megjelenített név**: Fogadja el az alapértelmezett **Azure IoT Edge - modul lemezképek Build**.
   * **A művelet**: Fogadja el az alapértelmezett **modul lemezképeket**. 
   * **.template.json file**: Kattintson a három pontra (**...** ), és keresse meg a **deployment.template.json** , amely tartalmazza az IoT Edge-megoldás a tárházban található fájl. 
   * **Alapértelmezett platform**: Válassza ki a megfelelő platform a modulok IoT Edge-eszköz a célon. 
   * **Változók kimeneti**: A kimeneti változói tartalmazzák, amelyek segítségével konfigurálhatja a fájl elérési útját a deployment.json fájlt hoz létre, hivatkozási nevét. Állítsa a hivatkozás nevet valami könnyen megtalálható, például **edge**. 

7. Válassza ki a második **Azure IoT Edge** feladat szerkesztheti. Ez a feladat összes modul rendszerkép leküldéses a kiválasztott tároló-beállításjegyzékbe. Hozzáadja a container registry bejelentkezési adatait, a **deployment.json** fájlt úgy, hogy az IoT Edge-eszköz hozzáférhet a modul lemezképeket. 

   * **Megjelenített név**: A megjelenítendő név a művelet Mezőváltozások automatikusan frissül. 
   * **A művelet**: A legördülő lista segítségével válassza ki a **modul rendszerképeket**. 
   * **Tárolóregisztrációs adatbázis típusa**: Válassza ki a tárolóregisztrációs adatbázis, amely a modul lemezképeket fogja használni. Attól függően, melyik beállításjegyzék-típust választja, a képernyő módosításokat. Ha úgy dönt, **Azure Container Registry**, használja a legördülő listákban válassza ki az Azure-előfizetés és a tárolóregisztrációs adatbázis nevét. Ha úgy dönt, **általános Container Registry**válassza **új** beállításjegyzék szolgáltatás kapcsolat létrehozásához. 
   * **.template.json file**: Kattintson a három pontra (**...** ), és keresse meg a **deployment.template.json** , amely tartalmazza az IoT Edge-megoldás a tárházban található fájl. 
   * **Alapértelmezett platform**: Válassza ki a beépített modul rendszerképek ugyanarra a platformra.

   Ha a modul rendszerképek üzemeltetésére több tároló-beállításjegyzékek, szeretné-e ez a feladat ismétlődő, különböző tároló-beállításjegyzék és a használata **modul(ok) megkerülése** megkerülhetik a lemezképeket, amelyek nem a speciális beállításai között szereplő adott beállításjegyzékhez.

8. Válassza ki a **összeállítása összetevők közzététele** szerkeszteni, a feladat. Adja meg az üzembe helyezési fájl az összeállítási feladat által létrehozott fájl elérési útját. Állítsa be a **közzététele elérési út** megfelelően az összeállítási modul feladat a megadott kimeneti változó értékét. Például: `$(edge.DEPLOYMENT_FILE_PATH)`. A többi értéket hagyja az alapértelmezett értéke. 

9. Nyissa meg a **eseményindítók** lapra, és jelölje be a jelölőnégyzetet annak **engedélyezze a folyamatos integrációt**. Ellenőrizze, hogy az ág a kódot tartalmazó részét képezi.

    ![Kapcsolja be a folyamatos integráció aktiválása](./media/how-to-ci-cd/configure-trigger.png)

10. Mentse az új buildelési folyamat az **mentése** gombra.

Ez a folyamat most már automatikusan futnak, amikor új kód leküldése a tárház van konfigurálva. Az utolsó feladatban, a folyamat összetevők közzététele elindítja a kibocsátási folyamat. Folytassa a következő szakaszban hozhat létre a kiadási folyamathoz. 

## <a name="configure-continuous-deployment"></a>Folyamatos üzembe helyezés konfigurálása
Ebben a szakaszban létrehoz egy kiadási folyamatot, amely automatikusan futnak, amikor a buildelési folyamat csökken összetevők van beállítva, és meg fog jelenni telepítési naplók a Azure folyamatok.

Ez a szakasz két különböző szakaszaiban, egyet a tesztelési célú telepítéseket és éles környezetekben üzemelő példányok hoz létre. 

### <a name="create-test-stage"></a>Vizsgálat szakasz létrehozása

Hozzon létre egy új folyamatot, és konfigurálja az első szakasz minőségi minőségellenőrzés központi telepítésekhez. 

1. Az a **kiadásokban** lapra, majd **+ új adatcsatorna**. Vagy, ha már rendelkezik kiadási folyamatokat, válassza ki a **+ új** gombra, majd **+ új kibocsátásában**.  

    ![Adja hozzá a kibocsátási folyamat](./media/how-to-ci-cd/add-release-pipeline.png)

2. Amikor a rendszer kéri, válassza ki a sablont, a kezdéshez válasszon egy **üres feladat**.

    ![Egy üres feladat indítása](./media/how-to-ci-cd/start-with-empty-job.png)

3. Az új kiadási folyamatot inicializálja a one stagebeállításnál, nevű **1. fázis**. Az 1. fázis átnevezése **QA** és a egy tesztkörnyezetet, kezelje azt. Általában a folyamatos üzembe helyezési folyamatok rendelkezik több szakaszt. Több a fejlesztési és üzemeltetési eljárások alapján is létrehozhat. Ha átnevezi, zárja be a fázis részletei ablak megnyitásához. 

    ![Tesztelési környezet szakasz létrehozása](./media/how-to-ci-cd/QA-env.png)

4. A kiadás a build-összetevőket a buildelési folyamat által közzétett társítani. Kattintson a **Hozzáadás** összetevők területen.

   ![Adjon hozzá összetevőket](./media/how-to-ci-cd/add-artifacts.png)  
    
5. A **összetevő-weblap hozzáadása**, válassza ki az adatforrás típusa **összeállítása**. Ezután válassza ki a projektet, és létrehozott buildelési folyamat. Ezután válassza a **Hozzáadás** lehetőséget.

   ![Adja hozzá a buildösszetevőt](./media/how-to-ci-cd/add-an-artifact.png)

6. Nyissa meg az összetevő eseményindítók, és válassza ki a folyamatos készregyártás eseményindítója engedélyezéséhez a váltógombot. Most egy új kiadási hoz létre minden alkalommal, amikor egy új létrehozást érhető el.

   ![A folyamatos készregyártás eseményindítója konfigurálása](./media/how-to-ci-cd/add-a-trigger.png)

7. A **QA** fázis előre konfigurálva vannak egy feladatot és nulla feladatokat. A folyamat menüből válassza ki a **feladatok** majd válassza ki a **QA** szakaszban.  Válassza ki a feladatok konfigurálása az ebben a szakaszban a feladatok és tevékenységek száma.

    ![QA feladatok konfigurálása](./media/how-to-ci-cd/view-stage-tasks.png)

8. A QA fázisban kell megjelennie egy alapértelmezett **ügynöki feladat**. Konfigurálhatja az ügynöki feladat részleteit, de a központi telepítési feladatokat és nagybetűk megkülönböztetése nélkül platformmegbízhatósági, így használhatja **Hosted VS2017** vagy **üzemeltetett Ubuntu 1604** a a **ügynökkészlet**(vagy bármely más, a saját maga által felügyelt ügynök). 

9. Válassza a pluszjelet (**+**) hozzáadása egy feladat. Keresse meg és adja hozzá **Azure IoT Edge**. 

    ![Tevékenységek hozzáadása a QA](./media/how-to-ci-cd/add-task-qa.png)

10. Válassza ki az új Azure IoT Edge-feladat, és konfigurálja a következő értékeket:

   * **Megjelenített név**: A megjelenítendő név a művelet Mezőváltozások automatikusan frissül. 
   * **A művelet**: A legördülő lista segítségével válassza ki a **üzembe helyezés az IoT Edge-eszköz**. A művelet értékének módosítása frissíti a feladat megjelenítendő neve megfelelő is.
   * **Azure-előfizetés**: Válassza ki az előfizetést, amely az IoT Hub tartalmaz.
   * **Az IoT Hub nevét**: Válassza ki az IoT hubnak. 
   * **Válassza ki egyetlen/több eszközt**: Válassza ki, hogy a kiadási folyamathoz egy vagy több eszközön való üzembe helyezéséhez. 
      * Ha egy eszközre telepít, adja meg a **IoT Edge-eszköz azonosítója**. 
      * Ha több eszközre telepít, adja meg az eszköz **feltétel cél**. A cél feltétel nem egyezik meg az Edge-eszközök az IoT Hub egy szűrőt. Ha azt szeretné, eszköz-címkék használata a feltételt, az IoT Hub ikereszköz a megfelelő eszközök címkék frissíteni szeretné. Frissítés a **IoT Edge üzemelő példány azonosítója** és **IoT Edge üzembe helyezési prioritás** a speciális beállításokban. Egy üzemelő példánya több eszközön létrehozásával kapcsolatos további információkért lásd: [automatikus ismertetése IoT Edge-telepítések](module-deployment-monitoring.md).

11. Válassza ki **mentése** a új kiadási folyamathoz a módosítások mentéséhez. Térjen vissza a folyamat nézet kiválasztásával **folyamat** a menüből. 

### <a name="create-production-stage"></a>Hozzon létre a termelési fázisban

Hozzon létre egy második szakasz a kibocsátási folyamat az éles környezet. 

1. Győződjön meg a második szakaszban az éles környezetben a QA szakaszban klónozásával. Vigye a kurzort a QA szakaszban, majd válassza ki a Klónozás gombra. 

    ![Szakasz klónozása](./media/how-to-ci-cd/clone-stage.png)

2. Válassza ki az új szakasz nevű **másolási, QA**, majd a tulajdonságainak megnyitásához. Módosítsa a nevet fázis **PROD**, éles üzemi környezetek részei. A fázis tulajdonságok ablak bezárásához. 

3. Az ÉLES fázis feladatok megnyitásához válassza **feladatok** a folyamat menüben majd válassza a **PROD** szakaszban. 

4. Válassza a konfigurálása az éles környezetben az Azure IoT Edge-feladatot. A központi telepítési beállítások ugyanazok, valószínűleg QA és az ÉLES, azzal a különbséggel, hogy egy másik eszközre vagy az eszközök éles környezetben szeretné. Frissítse az eszköz-azonosító mező, vagy a célként megadott feltétel és a telepítési azonosító mezőket éles eszközökhöz. 

5. Mentse a a **mentése** gombra. Majd **folyamat** a folyamat nézethez való visszatéréshez.
    
6. Buildösszetevő aktiválják a módszer a kibocsátási folyamat jelenleg úgy van beállítva, a **QA** szakaszban, majd **PROD** minden alkalommal, amikor elkészül egy új build előkészítéséhez. Azonban általában kívánják integrálni az egyes esetek a QA eszközökön, és manuális jóváhagyásáról az üzembe helyezés éles üzemi környezetek részei. Az alábbi lépések segítségével hozzon létre egy jóváhagyási feltétel az ÉLES szakaszhoz:

    1. Nyissa meg a **központi telepítés előtti feltételek** beállítások panelen.

        ![Nyissa meg a központi telepítés előtti feltételek](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Váltás a **központi telepítés előtti jóváhagyások** azzal a feltétellel **engedélyezve**. Egy vagy több felhasználók vagy csoportok hozzáadása a **jóváhagyók** mezőben, majd testre szabhatja az összes olyan jóváhagyási házirendet, amelyeket szeretne. Szeretné menteni a módosításokat, zárja be a központi telepítés előtti feltételek panelen.
    
       ![Beállítása feltételek](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. A kibocsátási folyamat a Mentés a **mentése** gombra. 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>IoT Edge CI/CD kérdezze meg a build és a folyamatok felszabadítása

A fordítási feladatot indításához küldje le a véglegesítés forráskódraktárban vagy is aktiválása manuálisan. Ebben a szakaszban manuálisan fogja aktiválni a CI/CD-folyamat teszteléséhez, működik-e. Ellenőrizze, hogy az üzembe helyezés sikeres.

1. Keresse meg a buildelési folyamat, amelyet ez a cikk elején hozott létre. 

2. Is aktiválhatja a fordítási feladatot a buildelési folyamat kiválasztásával a **várólista** gombra az alábbi képernyőképen látható módon.

    ![Manuális eseményindító](./media/how-to-ci-cd/manual-trigger.png)

3. Válassza ki a fordítási feladatot, és tekintse meg a folyamat előrehaladását. Ha a buildelési folyamat sikeresen befejeződött, elindítja egy üzembehelyezési **QA** szakaszban. 

    ![Buildnaplók](./media/how-to-ci-cd/build-logs.png)

4. A sikeres üzembe helyezés **QA** fázis a jóváhagyó értesítést aktivál. Győződjön meg arról, hogy a modulok sikeresen telepítve az eszközön vagy a QA szakaszhoz célzott eszközök. Ezután lépjen a kibocsátási folyamat és a kiadás az ÉLES szakaszba kiválasztásával nyissa meg a jóváhagyási a **PROD** gombra, és válassza **jóváhagyás**. 

    ![Jóváhagyásra váró elemek](./media/how-to-ci-cd/pending-approval.png)

5. Miután a jóváhagyó jóváhagyja ezt a módosítást, is üzembe helyezhető **PROD**.

## <a name="next-steps"></a>További lépések

* Megismerheti az IoT Edge üzemelő példány [ismertetése IoT Edge-telepítések egyetlen eszközök vagy ipari méretekben](module-deployment-monitoring.md)
* Lépésről lépésre bemutatjuk létrehozása, frissítése vagy törlése a központi telepítés [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md).
