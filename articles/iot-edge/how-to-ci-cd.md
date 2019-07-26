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
ms.openlocfilehash: 659a6f5acaac848084ed1e9590a414191542b54a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414632"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge-ben

Az Azure-folyamatok beépített Azure IoT Edge feladataival könnyedén elvégezheti a DevOps a Azure IoT Edge alkalmazásaiban. Ez a cikk bemutatja, hogyan használhatja az Azure-folyamatok folyamatos integrációját és folyamatos üzembe helyezését az alkalmazások gyors és hatékony létrehozásához, teszteléséhez és üzembe helyezéséhez a Azure IoT Edge. 

Ebből a cikkből megtudhatja, hogyan használhatja az Azure-folyamatok beépített Azure IoT Edge feladatait két folyamat létrehozásához a IoT Edge-megoldáshoz. Az első felveszi a kódot, és létrehozza a megoldást, leküldi a modul lemezképeit a tároló-beállításjegyzékbe, és létrehoz egy üzembe helyezési jegyzéket. A második üzembe helyezi a modulokat, hogy megcélozzák IoT Edge eszközöket.  

![Diagram – a CI és CD ágak fejlesztési és termelési célra](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Repos-tárház. Ha még nem rendelkezik ilyennel, [létrehozhat egy új git-](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav)tárházat a projektben.
* Egy IoT Edge-megoldás véglegesítve lett, és leküldve a tárházba. Ha a cikk teszteléséhez új mintavételi megoldást szeretne létrehozni, kövesse a [modulok fejlesztése és hibakeresése a Visual Studio Code](how-to-vs-code-develop-module.md) -ban című témakör lépéseit, illetve [a Visual Studióban található modulok fejlesztését és C# hibakeresését](how-to-visual-studio-develop-csharp-module.md)ismertető cikket.
   * Ehhez a cikkhez mindössze annyit kell tennie, hogy a Visual Studio Code vagy a Visual Studio IoT Edge sablonjai által létrehozott megoldási mappát hozza létre. A továbblépés előtt nem kell ezt a kódot felépíteni, leküldeni, telepíteni vagy hibakeresést végeznie. Ezeket a folyamatokat az Azure-folyamatokban fogja beállítani. 
   * Ha új megoldást hoz létre, először a tárházat klónozással. Ezután a megoldás létrehozásakor megadhatja, hogy közvetlenül a tárház mappájába hozza létre. Egyszerűen véglegesítheti és leküldheti az új fájlokat. 
* Egy tároló-beállításjegyzék, amelyen leküldéses modul képei láthatók. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy külső gyártótól származó beállításjegyzéket is használhat. 
* Egy aktív [IoT hub](../iot-hub/iot-hub-create-through-portal.md) legalább IoT Edge eszközzel a különálló tesztelési és éles üzembe helyezési szakaszok teszteléséhez. Az IoT Edge-eszköz [Linux](quickstart-linux.md) vagy [Windows](quickstart.md) rendszeren való létrehozásához kövesse a rövid útmutató cikkeit


További információ az Azure Repos használatáról: [kód megosztása a Visual Studióval és az Azure repostel](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Folyamatos integráció konfigurálása
Ebben a szakaszban egy új Build-folyamatot hoz létre. Konfigurálja úgy a folyamatot, hogy automatikusan fusson, amikor bejelentkezik a minta IoT Edge-megoldás változásai között, és közzéteszi a létrehozási naplókat.

>[!NOTE]
>Ez a cikk az Azure DevOps vizuális tervezőjét használja. Mielőtt elkezdené az ebben a szakaszban leírt lépéseket, kapcsolja ki az új YAML-folyamat létrehozási élményének előzetes verzióját. 
>1. Az Azure DevOps válassza ki a profil ikont, majd válassza az **előzetes verziójú funkciók**lehetőséget.
>2. Kapcsolja ki az **új YAML-folyamat létrehozási élményét** . 
>
>További információ: build- [folyamat létrehozása](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Jelentkezzen be az Azure DevOps-szervezetbe (**https:\//dev.Azure.com/{your Organization}/** ), és nyissa meg a IoT Edge megoldás tárházát tartalmazó projektet.

   Ebben a cikkben egy **IoTEdgeRepo**nevű tárházat hoztunk létre. Ez a tárház olyan **IoTEdgeSolution** tartalmaz, amely egy **filtermodule**nevű modul kódját tartalmazza. 

   ![A DevOps-projekt megnyitása](./media/how-to-ci-cd/init-project.png)

2. Navigáljon a projektben található Azure-folyamatokhoz. Nyissa  meg a builds fület, és válassza az **új folyamat**elemet. Ha már rendelkezik Build-folyamatokkal, kattintson az **új** gombra. Ezután válassza az **új létrehozási folyamat**lehetőséget.

    ![Új buildfolyamat létrehozása](./media/how-to-ci-cd/add-new-build.png)

3. A folyamat létrehozásához kövesse az utasításokat. 

   1. Adja meg az új build-folyamat forrásának adatait. Válassza az **Azure Repos git** lehetőséget forrásként, majd válassza ki azt a projektet, tárházat és ágat, ahol a IoT Edge-megoldás kódja található. Ezután válassza a **Folytatás**lehetőséget. 

      ![Válassza ki a folyamat forrását](./media/how-to-ci-cd/pipeline-source.png)

   2. Sablon helyett válassza az **üres feladatot** . 

      ![Kezdje egy üres folyamatot](./media/how-to-ci-cd/start-with-empty.png)

4. A folyamat létrehozása után a rendszer a folyamat-szerkesztőt veszi át. A folyamat leírásában válassza ki a megfelelő ügynököt a cél platform alapján: 
    
   * Ha szeretné a modulok a Linux-tárolókhoz tartozó platform amd64 hozhat létre, válassza a **üzemeltetett Ubuntu 1604**

   * Ha a Windows 1809-tárolók esetében szeretné felépíteni a modulokat a platform amd64-ben, akkor a [Windowsban saját](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts)üzemeltetésű ügynököt kell beállítania.

   * Ha a modulokat a platform arm32v7 vagy a arm64 for Linux-tárolók számára szeretné felépíteni, a saját üzemeltetésű [ügynököt Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)rendszeren kell beállítania.
    
     ![Build ügynökkészlet konfigurálása](./media/how-to-ci-cd/configure-env.png)

5. A folyamat előre konfigurálva van egy **Agent Job 1**nevű feladatokkal. Ha három feladatot szeretne felvenni a feladatba, válassza a pluszjelet ( **+** ). **Azure IoT Edge** kétszer, és egyszer **közzé kell tennie a Build** -összetevőket. (Vigye a kurzort az egyes feladatok nevére a **Hozzáadás** gomb megjelenítéséhez.)

   ![Azure IoT Edge feladat hozzáadása](./media/how-to-ci-cd/add-iot-edge-task.png)

   Mindhárom feladat hozzáadásakor az ügynök feladata az alábbi példához hasonlóan néz ki:
    
   ![Három feladat az összeállítási folyamatban](./media/how-to-ci-cd/add-tasks.png)

6. Válassza ki az első **Azure IoT Edge** feladatot a szerkesztéshez. Ez a feladat a megoldás összes modulját felépíti a megadott megcélzott platformra, és létrehozza az üzembe helyezési **. JSON** fájlt, amely tájékoztatja a IoT Edge-eszközöket az üzemelő példány konfigurálásáról.

   * **Megjelenítendő név**: Fogadja el az alapértelmezett **Azure IoT Edge-Build-modul lemezképeit**.
   * **Művelet**: Fogadja el az alapértelmezett **Build-modul lemezképeit**. 
   * **. template. JSON fájl**: Kattintson a három pontra ( **..** .), és navigáljon a **központi telepítési. template. JSON** fájlhoz az IoT Edge megoldást tartalmazó adattárban. 
   * **Alapértelmezett platform**: Válassza ki a megfelelő platformot a modulok számára a cél IoT Edge eszköz alapján. 
   * **Kimeneti változók**: A kimeneti változók közé tartozik egy hivatkozás neve, amellyel konfigurálható a fájl elérési útja, ahol a rendszer létrehozza a telepítési. JSON fájlt. Adja meg a hivatkozási nevet egy olyan emlékezethez, mint a **Edge**. 

7. Válassza ki a második **Azure IoT Edge** feladatot a szerkesztéshez. Ez a feladat leküldi az összes modul lemezképét a kiválasztott tároló-beállításjegyzékbe. Emellett hozzáadja a tároló beállításjegyzékbeli hitelesítő adatait a **Deployment. JSON** fájlhoz, hogy a IoT Edge eszköz hozzáférhessen a modul lemezképéhez. 

   * **Megjelenítendő név**: A művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. 
   * **Művelet**: A legördülő lista használatával kiválaszthatja a leküldéses **modulok lemezképeit**. 
   * **Tároló beállításjegyzékének típusa**: Válassza ki a modul rendszerképeinek tárolására használt tároló-beállításjegyzék típusát. Attól függően, hogy melyik beállításjegyzék-típust választja, az űrlap megváltoznak. Ha a **Azure Container Registry**lehetőséget választja, a legördülő listák használatával válassza ki az Azure-előfizetést és a tároló-beállításjegyzék nevét. Ha az **általános Container Registry**lehetőséget választja, akkor az **új** elemre kattintva hozzon létre egy beállításjegyzék-szolgáltatáshoz való kapcsolódást. 
   * **. template. JSON fájl**: Kattintson a három pontra ( **..** .), és navigáljon a **központi telepítési. template. JSON** fájlhoz az IoT Edge megoldást tartalmazó adattárban. 
   * **Alapértelmezett platform**: Válassza ki ugyanazt a platformot, mint a beépített modul lemezképeit.

   Ha a modul rendszerképek üzemeltetésére több tároló-beállításjegyzékek, szeretné-e ez a feladat ismétlődő, különböző tároló-beállításjegyzék és a használata **modul(ok) megkerülése** megkerülhetik a lemezképeket, amelyek nem a speciális beállításai között szereplő adott beállításjegyzékhez.

8. A szerkesztéshez válassza a Build-összetevők **közzététele** feladatot. Adja meg a felépítési feladat által generált telepítési fájl elérési útját. Állítsa be úgy a **közzétételi** értéket, hogy az megfeleljen a modul felépítése feladatban beállított kimeneti változónak. Például: `$(edge.DEPLOYMENT_FILE_PATH)`. Hagyja meg a többi értéket alapértelmezett értékként. 

9. Nyissa  meg az eseményindítók lapot, és jelölje be a jelölőnégyzetet a **folyamatos integráció engedélyezéséhez**. Ellenőrizze, hogy az ág a kódot tartalmazó részét képezi.

    ![Kapcsolja be a folyamatos integráció aktiválása](./media/how-to-ci-cd/configure-trigger.png)

10. Mentse az új Build-folyamatot **Mentés** gombbal.

Ez a folyamat most úgy van konfigurálva, hogy automatikusan fusson, amikor új kódot küld a tárházba. Az utolsó feladat, amely közzéteszi a folyamat összetevőit, elindítja a kiadási folyamatokat. Folytassa a következő szakasszal a kiadási folyamat felépítéséhez. 

## <a name="configure-continuous-deployment"></a>Folyamatos üzembe helyezés konfigurálása
Ebben a szakaszban létrehoz egy kiadási folyamatot, amely úgy van beállítva, hogy automatikusan fusson, amikor a build-folyamat elveszíti az összetevőket, és az üzembe helyezési naplókat az Azure-folyamatokban fogja megjeleníteni.

Ebben a szakaszban két különböző szakaszt hoz létre, amelyek közül az egyik a tesztelési központi telepítések, az egyik az éles környezetekben. 

### <a name="create-test-stage"></a>Tesztelési fázis létrehozása

Hozzon létre egy új folyamatot, és konfigurálja a minőségbiztosítási (QA) központi telepítések első szakaszát. 

1. Az a **kiadásokban** lapra, majd **+ új adatcsatorna**. Ha már rendelkezik kiadási folyamatokkal, kattintson az **+ új** gombra, és válassza az **+ új kiadási folyamat**lehetőséget.  

    ![Adja hozzá a kibocsátási folyamat](./media/how-to-ci-cd/add-release-pipeline.png)

2. Amikor a rendszer rákérdez a sablon kiválasztására, válassza az **üres**feladatokkal lehetőséget.

    ![Egy üres feladat indítása](./media/how-to-ci-cd/start-with-empty-job.png)

3. Az új kiadási folyamat inicializálása egyetlen fázissal, az **1. fázis**néven. Nevezze át az 1. szakaszt a **QA** -re, és kezelje tesztelési környezetként. Általában a folyamatos üzembe helyezési folyamatoknak több szakasza van. A DevOps gyakorlat alapján többet is létrehozhat. Az Átnevezés után a szakasz részletei ablak bezárásához. 

    ![Tesztelési környezet szakasz létrehozása](./media/how-to-ci-cd/QA-env.png)

4. Csatolja a kiadást a build-folyamat által közzétett Build-összetevőkhöz. Kattintson a **Hozzáadás** összetevők területen.

   ![Adjon hozzá összetevőket](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Az **összetevő hozzáadása lapon**válassza a forrás típusa **Build**lehetőséget. Ezután válassza ki a projektet és a létrehozott Build-folyamatot. Ezután válassza a **Hozzáadás** lehetőséget.

   ![Adja hozzá a buildösszetevőt](./media/how-to-ci-cd/add-an-artifact.png)

6. Nyissa meg az összetevő-eseményindítókat, és válassza ki a váltást a folyamatos üzembe helyezési eseményindító engedélyezéséhez. Most új kiadás jön létre minden alkalommal, amikor új Build áll rendelkezésre.

   ![A folyamatos készregyártás eseményindítója konfigurálása](./media/how-to-ci-cd/add-a-trigger.png)

7. A **QA** -fázis előre konfigurálva van egy feladattal és nulla tevékenységgel. A folyamat menüben válassza a **feladatok** lehetőséget, majd válassza ki a **QA** szakaszt.  Válassza ki a feladat és a feladat darabszámát az ebben a szakaszban található feladatok konfigurálásához.

    ![QA feladatok konfigurálása](./media/how-to-ci-cd/view-stage-tasks.png)

8. A QA szakaszban egy alapértelmezett **ügynöki feladatot**kell látnia. Megadhatja az ügynök feladatának adatait, de az üzembe helyezési feladat a platformtól eltérő, így az **ügynök** -készletben (vagy a saját maga által felügyelt bármely más ügynökben) **üzemeltetett VS2017** vagy **üzemeltetett Ubuntu 1604** is használható. 

9. Válassza a pluszjelet ( **+** ) egy feladat hozzáadásához. **Azure IoT Edge**keresése és hozzáadása. 

    ![Tevékenységek hozzáadása a QA](./media/how-to-ci-cd/add-task-qa.png)

10. Válassza ki az új Azure IoT Edge feladatot, és konfigurálja a következő értékekkel:

    * **Megjelenítendő név**: A művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. 
    * **Művelet**: A legördülő lista használatával válassza ki a **IoT Edge eszközre történő telepítést**. A művelet értékének módosítása a feladat megjelenítendő nevét is frissíti a megfeleltetéshez.
    * **Azure-előfizetés**: Válassza ki az előfizetést, amely tartalmazza a IoT Hub.
    * **IoT hub neve**: Válassza ki az IoT hubnak. 
    * **Válasszon egy/több eszközt**: Adja meg, hogy a kiadási folyamat egy vagy több eszközre legyen-e telepítve. 
      * Ha egyetlen eszközre telepít központilag, adja meg **IoT Edge eszköz azonosítóját**. 
      * Ha több eszközre telepít üzembe helyezést, az eszköz **célját**kell megadnia. A cél feltétel nem egyezik meg az Edge-eszközök az IoT Hub egy szűrőt. Ha azt szeretné, eszköz-címkék használata a feltételt, az IoT Hub ikereszköz a megfelelő eszközök címkék frissíteni szeretné. Frissítse a **IoT Edge központi telepítési azonosítót** és a **IoT Edge központi telepítési prioritást** a speciális beállítások között. További információ a központi telepítés több eszközhöz való létrehozásáról: [IoT Edge automatikus központi telepítések ismertetése](module-deployment-monitoring.md).

11. A **Mentés** gombra kattintva mentheti a módosításokat az új kiadási folyamatba. Térjen vissza a folyamat nézethez a menüből válassza a **folyamat** lehetőséget. 

### <a name="create-production-stage"></a>Termelési fázis létrehozása

Hozzon létre egy második szakaszt a kiadási folyamatában éles környezetben történő üzembe helyezéshez. 

1. A MINŐSÉGBIZTOSÍTÁSi fázis klónozásával a termelés második fázisa is lehet. Vigye a kurzort a QA szakasz fölé, majd kattintson a klónozás gombra. 

    ![Szakasz klónozása](./media/how-to-ci-cd/clone-stage.png)

2. A tulajdonságok megnyitásához válassza a **QA másolata**nevű új szakaszt. Módosítsa a szakasz nevét a **Production értékre**a termeléshez. A szakasz tulajdonságai ablak bezárásához. 

3. A gyártási fázis feladatainak megnyitásához válassza a folyamat menü **feladatok** elemét, majd válassza a **gyártási** fázist. 

4. Válassza ki az üzemi környezethez konfigurálni kívánt Azure IoT Edge feladatot. A központi telepítési beállítások valószínűleg ugyanazok, mint a QA és a PROD esetében, kivéve, ha egy másik eszközt vagy eszközöket kíván használni az éles környezetben. Frissítse az eszköz azonosító mezőjét, vagy az üzemi eszközökhöz tartozó cél és telepítési azonosító mezőket. 

5. Mentse a **Mentés** gombra. Ezután válassza a **folyamat** lehetőséget a folyamat nézetre való visszalépéshez.
    
6. Ez a kiadási folyamat jelenleg konfigurálva van, az összetevők kiépítése elindítja a **QA** szakaszt, majd a **gyártási** fázist minden alkalommal, amikor egy új Build elkészült. Azonban általában bizonyos tesztelési eseteket szeretne integrálni a QA-eszközökre, és manuálisan jóvá kell hagynia az éles környezetben történő üzembe helyezést. A következő lépésekkel hozhat létre jóváhagyási feltételt a gyártási fázishoz:

    1. Nyissa meg a **telepítés előtti feltételek** beállítások panelt.

        ![Nyissa meg a központi telepítés előtti feltételek](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Az **üzembe helyezés előtti jóváhagyások** feltételének bekapcsolása **engedélyezve**értékre. Adjon hozzá egy vagy több felhasználót vagy csoportot a **jóváhagyók** mezőben, és szabja testre a többi kívánt jóváhagyási szabályzatot. A módosítások mentéséhez zárjuk be az üzembe helyezés előtti feltételek panelt.
    
       ![Beállítása feltételek](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Mentse a kiadási folyamatot a **Save (Mentés** ) gombbal. 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>IoT Edge CI/CD kérdezze meg a build és a folyamatok felszabadítása

A fordítási feladatot indításához küldje le a véglegesítés forráskódraktárban vagy is aktiválása manuálisan. Ebben a szakaszban manuálisan indítja el a CI/CD folyamatot a működésének ellenőrzéséhez. Ezután ellenőrizze, hogy az üzembe helyezés sikeres volt-e.

1. Navigáljon a cikk elején létrehozott összeállítási folyamathoz. 

2. A létrehozási folyamat során kiválthat egy felépítési feladatot úgy, hogy kijelöli a **várólista** gombot a következő képernyőképen látható módon.

    ![Manuális eseményindító](./media/how-to-ci-cd/manual-trigger.png)

3. A folyamat előrehaladásának megtekintéséhez válassza ki a Build feladatot. Ha az összeállítási folyamat sikeresen befejeződött, a kiváltja a **minőségbiztosítási** fázis kiadását. 

    ![Buildnaplók](./media/how-to-ci-cd/build-logs.png)

4. A sikeres üzembe helyezés a **QA** -fázisban elindít egy értesítést a jóváhagyónak. Ellenőrizze, hogy az üzembe helyezett modulok sikeresen telepítve vannak-e azon az eszközön vagy eszközökön, amelyet a QA szakaszhoz céloztak. Ezután navigáljon a kiadási folyamathoz, és jóváhagyást adjon a kiadásnak a gyártási fázishoz való ugráshoz, majd **válassza a** **jóváhagyás**lehetőséget. 

    ![Jóváhagyásra váró elemek](./media/how-to-ci-cd/pending-approval.png)

5. Miután a jóváhagyó jóváhagyja ezt a módosítást, is üzembe helyezhető **PROD**.

## <a name="next-steps"></a>További lépések

* Megismerheti az IoT Edge üzemelő példány [ismertetése IoT Edge-telepítések egyetlen eszközök vagy ipari méretekben](module-deployment-monitoring.md)
* Lépésről lépésre bemutatjuk létrehozása, frissítése vagy törlése a központi telepítés [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md).
