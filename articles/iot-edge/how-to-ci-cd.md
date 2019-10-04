---
title: Folyamatos integráció és folyamatos üzembe helyezés – Azure IoT Edge |} A Microsoft Docs
description: Folyamatos integráció és folyamatos üzembe helyezés – Azure IoT Edge segítségével az Azure DevOps, Azure-folyamatok beállítása
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e14025a5a7a3e81404498638d6f6f9c5ff18ed58
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650785"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge-ben

Az Azure-folyamatok beépített Azure IoT Edge feladataival könnyedén elvégezheti a DevOps a Azure IoT Edge alkalmazásaiban. Ez a cikk bemutatja, hogyan használhatja az Azure-folyamatok folyamatos integrációját és folyamatos üzembe helyezését az alkalmazások gyors és hatékony létrehozásához, teszteléséhez és üzembe helyezéséhez a Azure IoT Edge. 

![Diagram – a CI és CD ágak fejlesztési és termelési célra](./media/how-to-ci-cd/cd.png)

Ebből a cikkből megtudhatja, hogyan használhatja az Azure-folyamatok beépített Azure IoT Edge feladatait két folyamat létrehozásához a IoT Edge-megoldáshoz. A Azure IoT Edge feladatokban négy műveletet lehet használni.
   - **Azure IoT Edge – a modul lemezképei** felépítik a IoT Edge megoldás kódját, és felépítik a tároló lemezképeit.
   - **Azure IoT Edge – a leküldéses modul képei** leküldik a modul rendszerképeit a megadott tároló-beállításjegyzékbe.
   - **Azure IoT Edge – az üzembe helyezési jegyzékfájl** előállítása üzembe helyezési. template. JSON fájlt és változókat hoz létre, majd létrehozza a végső IoT Edge telepítési jegyzékfájlt.
   - **Azure IoT Edge – a IoT Edge eszközökre való üzembe helyezéssel** IoT Edge központi telepítéseket hozhat létre egy vagy több IoT Edge eszközön.

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

2. Navigáljon a projektben található Azure-folyamatokhoz. Nyissa meg a builds fület, és válassza az **új folyamat**elemet. Ha már rendelkezik Build-folyamatokkal, kattintson az **új** gombra. Ezután válassza az **új létrehozási folyamat**lehetőséget.

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

5. A folyamat előre konfigurálva van egy **Agent Job 1**nevű feladatokkal. Ha három feladatot szeretne felvenni a feladatba, válassza a pluszjelet ( **+** ). **Azure IoT Edge** kétszer, egyszer **másolja a fájlokat** , és **tegye közzé** egyszer a Build-összetevőket. (Vigye a kurzort az egyes feladatok nevére a **Hozzáadás** gomb megjelenítéséhez.)

   ![Azure IoT Edge feladat hozzáadása](./media/how-to-ci-cd/add-iot-edge-task.png)

   Ha mind a négy feladat hozzá lett adva, az ügynök feladata a következő példához hasonlóan néz ki:
    
   ![Három feladat az összeállítási folyamatban](./media/how-to-ci-cd/add-tasks.png)

6. Válassza ki az első **Azure IoT Edge** feladatot a szerkesztéshez. Ez a feladat a megoldás összes modulját felépíti a megadott megcélzott platformra.

   * **Megjelenítendő név**: Fogadja el az alapértelmezett **Azure IoT Edge-Build-modul lemezképeit**.
   * **Művelet**: Fogadja el az alapértelmezett **Build-modul lemezképeit**. 
   * **. template. JSON fájl**: Kattintson a három pontra ( **..** .), és navigáljon a **központi telepítési. template. JSON** fájlhoz az IoT Edge megoldást tartalmazó adattárban. 
   * **Alapértelmezett platform**: Válassza ki a megfelelő platformot a modulok számára a cél IoT Edge eszköz alapján. 
   * **Kimeneti változók**: A kimeneti változók közé tartozik egy hivatkozás neve, amellyel konfigurálható a fájl elérési útja, ahol a rendszer létrehozza a telepítési. JSON fájlt. Adja meg a hivatkozási nevet egy olyan emlékezethez, mint a **Edge**. 

7. Válassza ki a második **Azure IoT Edge** feladatot a szerkesztéshez. Ez a feladat leküldi az összes modul lemezképét a kiválasztott tároló-beállításjegyzékbe.

   * **Megjelenítendő név**: A művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. 
   * **Művelet**: A legördülő lista használatával kiválaszthatja a leküldéses **modulok lemezképeit**. 
   * **Tároló beállításjegyzékének típusa**: Válassza ki a modul rendszerképeinek tárolására használt tároló-beállításjegyzék típusát. Attól függően, hogy melyik beállításjegyzék-típust választja, az űrlap megváltoznak. Ha a **Azure Container Registry**lehetőséget választja, a legördülő listák használatával válassza ki az Azure-előfizetést és a tároló-beállításjegyzék nevét. Ha az **általános Container Registry**lehetőséget választja, akkor az **új** elemre kattintva hozzon létre egy beállításjegyzék-szolgáltatáshoz való kapcsolódást. 
   * **. template. JSON fájl**: Kattintson a három pontra ( **..** .), és navigáljon a **központi telepítési. template. JSON** fájlhoz az IoT Edge megoldást tartalmazó adattárban. 
   * **Alapértelmezett platform**: Válassza ki ugyanazt a platformot, mint a beépített modul lemezképeit.

   Ha a modul rendszerképek üzemeltetésére több tároló-beállításjegyzékek, szeretné-e ez a feladat ismétlődő, különböző tároló-beállításjegyzék és a használata **modul(ok) megkerülése** megkerülhetik a lemezképeket, amelyek nem a speciális beállításai között szereplő adott beállításjegyzékhez.

8. A szerkesztéshez válassza a **fájlok másolása** feladatot. Ezzel a feladattal másolhat fájlokat az összetevő-előkészítési könyvtárba.

   * **Megjelenítendő név**: Fájlok másolása ide: Mappa eldobása.
   * **Tartalom**: A szakasz `deployment.template.json` `**/module.json`két sorát helyezi el. Ez a két típusú fájl a IoT Edge üzembe helyezési jegyzék előállításához szükséges bemenet. Az összetevő átmeneti mappájába kell másolni, és közzé kell tenni a kiadási folyamat számára.
   * **Célmappa**: Helyezze a változót `$(Build.ArtifactStagingDirectory)`. A leírással kapcsolatos további tudnivalókért lásd: [Build változók](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) .

9. A szerkesztéshez válassza a Build-összetevők **közzététele** feladatot. Adja meg a feladat átmeneti könyvtárának elérési útját a feladathoz, hogy az elérési út közzétehető legyen a kiadási folyamatban.
   
   * **Megjelenítendő név**: Az összetevő közzététele: drop.
   * **Közzététel elérési útja**: Helyezze a változót `$(Build.ArtifactStagingDirectory)`. A leírással kapcsolatos további tudnivalókért lásd: [Build változók](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) .
   * Összetevő **neve**: drop.
   * Összetevő **közzétételi helye**: Azure-folyamatok.


10. Nyissa meg az eseményindítók lapot, és jelölje be a jelölőnégyzetet a **folyamatos integráció engedélyezéséhez**. Ellenőrizze, hogy az ág a kódot tartalmazó részét képezi.

    ![Kapcsolja be a folyamatos integráció aktiválása](./media/how-to-ci-cd/configure-trigger.png)

11. Mentse az új Build-folyamatot **Mentés** gombbal.

Ez a folyamat most úgy van konfigurálva, hogy automatikusan fusson, amikor új kódot küld a tárházba. Az utolsó feladat, amely közzéteszi a folyamat összetevőit, elindítja a kiadási folyamatokat. Folytassa a következő szakasszal a kiadási folyamat felépítéséhez. 

## <a name="configure-continuous-deployment"></a>Folyamatos üzembe helyezés konfigurálása
Ebben a szakaszban létrehoz egy kiadási folyamatot, amely úgy van beállítva, hogy automatikusan fusson, amikor a build-folyamat elveszíti az összetevőket, és az üzembe helyezési naplókat az Azure-folyamatokban fogja megjeleníteni.

Új folyamat létrehozása és új szakasz hozzáadása 

1. Az a **kiadásokban** lapra, majd **+ új adatcsatorna**. Ha már rendelkezik kiadási folyamatokkal, kattintson az **+ új** gombra, és válassza az **+ új kiadási folyamat**lehetőséget.  

    ![Adja hozzá a kibocsátási folyamat](./media/how-to-ci-cd/add-release-pipeline.png)

2. Amikor a rendszer rákérdez a sablon kiválasztására, válassza az **üres**feladatokkal lehetőséget.

    ![Egy üres feladat indítása](./media/how-to-ci-cd/start-with-empty-job.png)

3. Az új kiadási folyamat inicializálása egyetlen fázissal, az **1. fázis**néven. Nevezze át az 1 . szakaszt a fejlesztéshez és a tesztelési környezetként való kezeléséhez. A folyamatos üzembe helyezési folyamatok általában több szakaszbólállnak, beleértve a fejlesztést, az **előkészítést** és a **gyártási**folyamatot. A DevOps gyakorlat alapján többet is létrehozhat. Az Átnevezés után a szakasz részletei ablak bezárásához. 

4. Csatolja a kiadást a build-folyamat által közzétett Build-összetevőkhöz. Kattintson a **Hozzáadás** összetevők területen.

   ![Adjon hozzá összetevőket](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Az **összetevő hozzáadása lapon**válassza a forrás típusa **Build**lehetőséget. Ezután válassza ki a projektet és a létrehozott Build-folyamatot. Ezután válassza a **Hozzáadás** lehetőséget.

   ![Adja hozzá a buildösszetevőt](./media/how-to-ci-cd/add-an-artifact.png)

6. Nyissa meg az összetevő-eseményindítókat, és válassza ki a váltást a folyamatos üzembe helyezési eseményindító engedélyezéséhez. Most új kiadás jön létre minden alkalommal, amikor új Build áll rendelkezésre.

   ![A folyamatos készregyártás eseményindítója konfigurálása](./media/how-to-ci-cd/add-a-trigger.png)

7. A **fejlesztői** fázis előre konfigurálva van egy feladattal és nulla tevékenységgel. A folyamat menüben válassza a **feladatok** lehetőséget, majd válassza ki a **fejlesztői** szakaszt.  Válassza ki a feladat és a feladat darabszámát az ebben a szakaszban található feladatok konfigurálásához.

    ![Fejlesztői feladatok konfigurálása](./media/how-to-ci-cd/view-stage-tasks.png)

8. A **fejlesztői** szakaszban egy alapértelmezett **ügynöki feladatot**kell megjelennie. Megadhatja az ügynök feladatának adatait, de az üzembe helyezési feladat a platformtól eltérő, így az **ügynök** -készletben (vagy a saját maga által felügyelt bármely más ügynökben) **üzemeltetett VS2017** vagy **üzemeltetett Ubuntu 1604** is használható. 

9. Válassza a pluszjelet ( **+** ) a két feladat hozzáadásához. Keresse meg és adja hozzá a **Azure IoT Edge** kétszer.

    ![Fejlesztési feladatok hozzáadása](./media/how-to-ci-cd/add-task-qa.png)

10. Válassza ki az első **Azure IoT Edge** feladatot, és konfigurálja a következő értékekkel:

    * **Megjelenítendő név**: A művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. 
    * **Művelet**: A legördülő listából válassza ki az **üzembe helyezési jegyzék**előállítása lehetőséget. A művelet értékének módosítása a feladat megjelenítendő nevét is frissíti a megfeleltetéshez.
    * **. template. JSON fájl**: Helyezze el az `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`elérési utat. Az elérési út közzé lett téve a Build-folyamatból.
    * **Alapértelmezett platform**: Válassza ki ugyanazt az értéket a modul lemezképének létrehozásakor.
    * **Kimeneti elérési út**: Helyezze el az `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`elérési utat. Ez az elérési út a végső IoT Edge telepítési jegyzékfájl.

    Ezek a konfigurációk segítenek lecserélni a modul képurl-címeit a `deployment.template.json` fájlban. Az **üzembe helyezési jegyzék létrehozása** a `deployment.template.json` fájlban megadott pontos értékkel is segíti a változók cseréjét. A vs/vs kódban a tényleges értéket kell megadnia egy `.env` fájlban. Az Azure-folyamatokban a kiadási folyamat változói lapon állíthatja be a értéket. Váltson a változók lapra, és konfigurálja a nevet és az értéket az alábbiak szerint.

    * **ACR_ADDRESS**: Az Azure Container Registry-címe. 
    * **ACR_PASSWORD**: Azure Container Registry jelszava.
    * **ACR_USER**: Azure Container Registry felhasználónevét.

    Ha a projektben más változók is szerepelnek, akkor ebben a lapon megadhatja a nevet és az értéket. Az **üzembe helyezési jegyzék létrehozása** csak az `${VARIABLE}` íz elemekben ismeri fel a változókat, ügyeljen arra, hogy `*.template.json` a fájlokat használja.

    ![Változók konfigurálása a kiadási folyamathoz](./media/how-to-ci-cd/configure-variables.png)

10. Válassza ki a második **Azure IoT Edge** feladatot, és konfigurálja a következő értékekkel:

    * **Megjelenítendő név**: A művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. 
    * **Művelet**: A legördülő lista használatával válassza ki a **IoT Edge eszközök üzembe helyezését**. A művelet értékének módosítása a feladat megjelenítendő nevét is frissíti a megfeleltetéshez.
    * **Azure-előfizetés**: Válassza ki az előfizetést, amely tartalmazza a IoT Hub.
    * **IoT hub neve**: Válassza ki az IoT hubnak. 
    * **Válasszon egy/több eszközt**: Adja meg, hogy a kiadási folyamat egy vagy több eszközre legyen-e telepítve. 
      * Ha egyetlen eszközre telepít központilag, adja meg **IoT Edge eszköz azonosítóját**. 
      * Ha több eszközre telepít üzembe helyezést, az eszköz **célját**kell megadnia. A célként megadott feltétel egy szűrő, amely a IoT Hub IoT Edge-eszközeinek felel meg. Ha azt szeretné, eszköz-címkék használata a feltételt, az IoT Hub ikereszköz a megfelelő eszközök címkék frissíteni szeretné. Frissítse a **IoT Edge központi telepítési azonosítót** és a **IoT Edge központi telepítési prioritást** a speciális beállítások között. További információ a központi telepítés több eszközhöz való létrehozásáról: [IoT Edge automatikus központi telepítések ismertetése](module-deployment-monitoring.md).
    * Bontsa ki a speciális beállítások, majd a **IoT Edge központi telepítési azonosító**elemet, helyezze a változót `$(System.TeamProject)-$(Release.EnvironmentName)`. Ez a projekt és a kiadás nevét a IoT Edge telepítési azonosítójával képezi le.

11. A **Mentés** gombra kattintva mentheti a módosításokat az új kiadási folyamatba. Térjen vissza a folyamat nézethez a menüből válassza a **folyamat** lehetőséget. 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>IoT Edge CI/CD kérdezze meg a build és a folyamatok felszabadítása

A fordítási feladatot indításához küldje le a véglegesítés forráskódraktárban vagy is aktiválása manuálisan. Ebben a szakaszban manuálisan indítja el a CI/CD folyamatot a működésének ellenőrzéséhez. Ezután ellenőrizze, hogy az üzembe helyezés sikeres volt-e.

1. Navigáljon a cikk elején létrehozott összeállítási folyamathoz. 

2. A létrehozási folyamat során kiválthat egy felépítési feladatot úgy, hogy kijelöli a **várólista** gombot a következő képernyőképen látható módon.

    ![Manuális eseményindító](./media/how-to-ci-cd/manual-trigger.png)

3. A folyamat előrehaladásának megtekintéséhez válassza ki a Build feladatot. Ha az összeállítási folyamat sikeresen befejeződött, elindítja a kiadást a **fejlesztői** fázisba. 

    ![Buildnaplók](./media/how-to-ci-cd/build-logs.png)

4. A sikeres **fejlesztői** kiadás IoT Edge központi telepítést hoz létre IoT Edge eszközök megcélzásához.

    ![Kiadás a dev-be](./media/how-to-ci-cd/pending-approval.png)

5. A kiadási naplók megjelenítéséhez kattintson a **fejlesztői** fázis elemre.

    ![Kiadási naplók](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>További lépések
* IoT Edge DevOps – ajánlott eljárásokat ismertető példa az [Azure DevOps-projektben IoT Edge](how-to-devops-project.md)
* Megismerheti az IoT Edge üzemelő példány [ismertetése IoT Edge-telepítések egyetlen eszközök vagy ipari méretekben](module-deployment-monitoring.md)
* Lépésről lépésre bemutatjuk létrehozása, frissítése vagy törlése a központi telepítés [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md).
