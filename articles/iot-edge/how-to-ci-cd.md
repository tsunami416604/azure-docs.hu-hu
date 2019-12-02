---
title: Folyamatos integráció & folyamatos üzembe helyezéssel – Azure IoT Edge
description: Folyamatos integráció és folyamatos üzembe helyezés beállítása – Azure IoT Edge Azure DevOps, Azure-folyamatokkal
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 463de1f49ad8fd21c355395bec3a55d9d40474e6
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666358"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Folyamatos integráció és folyamatos üzembe helyezés Azure IoT Edge

Az Azure-folyamatok beépített Azure IoT Edge feladataival könnyedén elvégezheti a DevOps a Azure IoT Edge alkalmazásaiban. Ez a cikk bemutatja, hogyan használhatja az Azure-folyamatok folyamatos integrációját és folyamatos üzembe helyezését az alkalmazások gyors és hatékony létrehozásához, teszteléséhez és üzembe helyezéséhez a Azure IoT Edge. 

![Diagram – CI és CD ágak fejlesztési és termelési célokra](./media/how-to-ci-cd/cd.png)

Ebből a cikkből megtudhatja, hogyan használhatja az Azure-folyamatok beépített Azure IoT Edge feladatait két folyamat létrehozásához a IoT Edge-megoldáshoz. A Azure IoT Edge feladatokban négy műveletet lehet használni.
   - **Azure IoT Edge – a modul lemezképei felépítik** a IoT Edge megoldás kódját, és felépítik a tároló lemezképeit.
   - **Azure IoT Edge – a leküldéses modul képei** leküldik a modul rendszerképeit a megadott tároló-beállításjegyzékbe.
   - **Azure IoT Edge – az üzembe helyezési jegyzékfájl előállítása** üzembe helyezési. template. JSON fájlt és változókat hoz létre, majd létrehozza a végső IoT Edge telepítési jegyzékfájlt.
   - **Azure IoT Edge – a IoT Edge eszközökre való üzembe helyezéssel** IoT Edge központi telepítéseket hozhat létre egy vagy több IoT Edge eszközön.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Repos-tárház. Ha még nem rendelkezik ilyennel, [létrehozhat egy új git-tárházat a projektben](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Egy IoT Edge-megoldás véglegesítve lett, és leküldve a tárházba. Ha a cikk teszteléséhez új mintavételi megoldást szeretne létrehozni, kövesse a [modulok fejlesztése és hibakeresése a Visual Studio Code](how-to-vs-code-develop-module.md) -ban című témakör lépéseit, illetve [a Visual Studióban található modulok fejlesztését és hibakeresését C# ](how-to-visual-studio-develop-csharp-module.md)ismertető cikket.
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
>További információ: build- [folyamat létrehozása](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Jelentkezzen be az Azure DevOps-szervezetbe (**https:\//dev.Azure.com/{your Organization}/** ), és nyissa meg a IoT Edge megoldás tárházát tartalmazó projektet.

   Ebben a cikkben egy **IoTEdgeRepo**nevű tárházat hoztunk létre. Ez a tárház olyan **IoTEdgeSolution** tartalmaz, amely egy **filtermodule**nevű modul kódját tartalmazza. 

   ![A DevOps-projekt megnyitása](./media/how-to-ci-cd/init-project.png)

2. Navigáljon a projektben található Azure-folyamatokhoz. Nyissa meg a **builds** fület, és válassza az **új folyamat**elemet. Ha már rendelkezik Build-folyamatokkal, kattintson az **új** gombra. Ezután válassza az **új létrehozási folyamat**lehetőséget.

    ![Új buildfolyamat létrehozása](./media/how-to-ci-cd/add-new-build.png)

3. A folyamat létrehozásához kövesse az utasításokat. 

   1. Adja meg az új build-folyamat forrásának adatait. Válassza az **Azure Repos git** lehetőséget forrásként, majd válassza ki azt a projektet, tárházat és ágat, ahol a IoT Edge-megoldás kódja található. Ezután válassza a **Folytatás**lehetőséget. 

      ![Válassza ki a folyamat forrását](./media/how-to-ci-cd/pipeline-source.png)

   2. Sablon helyett válassza az **üres feladatot** . 

      ![Kezdés egy üres folyamattal](./media/how-to-ci-cd/start-with-empty.png)

4. A folyamat létrehozása után a rendszer a folyamat-szerkesztőt veszi át. A folyamat leírásában válassza ki a megfelelő ügynököt a cél platform alapján: 
    
   * Ha a Linux-tárolók platform amd64-es verziójában szeretné létrehozni a modulokat, válassza az **üzemeltetett Ubuntu 1604**

   * Ha a Windows 1809-tárolók esetében szeretné felépíteni a modulokat a platform amd64-ben, akkor a [Windowsban saját üzemeltetésű ügynököt kell beállítania](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Ha a modulokat a platform arm32v7 vagy a arm64 for Linux-tárolók számára szeretné felépíteni, a saját üzemeltetésű [ügynököt Linux rendszeren kell beállítania](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Build-ügynök készletének konfigurálása](./media/how-to-ci-cd/configure-env.png)

5. A folyamat előre konfigurálva van egy **Agent Job 1**nevű feladatokkal. Válassza a pluszjelet ( **+** ) három feladat hozzáadásához a feladathoz: **Azure IoT Edge** kétszer, egyszer **másolja a fájlokat** , és **tegye közzé a Build** -összetevőket. (Vigye a kurzort az egyes feladatok nevére a **Hozzáadás** gomb megjelenítéséhez.)

   ![Azure IoT Edge feladat hozzáadása](./media/how-to-ci-cd/add-iot-edge-task.png)

   Ha mind a négy feladat hozzá lett adva, az ügynök feladata a következő példához hasonlóan néz ki:
    
   ![Három feladat az összeállítási folyamatban](./media/how-to-ci-cd/add-tasks.png)

6. Válassza ki az első **Azure IoT Edge** feladatot a szerkesztéshez. Ez a feladat a megoldás összes modulját felépíti a megadott megcélzott platformra.

   * **Megjelenítendő név**: fogadja el az alapértelmezett **Azure IoT Edge-Build-modul lemezképeit**.
   * **Művelet**: fogadja el az alapértelmezett **Build-modul lemezképeit**. 
   * **. template. JSON fájl**: válassza a három pontot ( **..** .), majd navigáljon a **központi telepítési. template. JSON** fájlhoz az IoT Edge megoldást tartalmazó adattárban. 
   * **Alapértelmezett platform**: válassza ki a megfelelő platformot a modulok számára a cél IoT Edge eszköz alapján. 
   * **Kimeneti változók**: a kimeneti változók közé tartozik egy hivatkozás neve, amellyel konfigurálható a fájl elérési útja, ahol a rendszer létrehozza a telepítési. JSON fájlt. Adja meg a hivatkozási nevet egy olyan emlékezethez, mint a **Edge**. 

7. Válassza ki a második **Azure IoT Edge** feladatot a szerkesztéshez. Ez a feladat leküldi az összes modul lemezképét a kiválasztott tároló-beállításjegyzékbe.

   * **Megjelenítendő név**: a művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. 
   * **Művelet**: a legördülő lista használatával válassza ki a **leküldéses modul lemezképeit**. 
   * **Tároló beállításjegyzékének típusa**: válassza ki a modul lemezképének tárolására használt tároló-beállításjegyzék típusát. Attól függően, hogy melyik beállításjegyzék-típust választja, az űrlap megváltoznak. Ha a **Azure Container Registry**lehetőséget választja, a legördülő listák használatával válassza ki az Azure-előfizetést és a tároló-beállításjegyzék nevét. Ha az **általános Container Registry**lehetőséget választja, akkor az **új** elemre kattintva hozzon létre egy beállításjegyzék-szolgáltatáshoz való kapcsolódást. 
   * **. template. JSON fájl**: válassza a három pontot ( **..** .), majd navigáljon a **központi telepítési. template. JSON** fájlhoz az IoT Edge megoldást tartalmazó adattárban. 
   * **Alapértelmezett platform**: válassza ki ugyanazt a platformot, mint a beépített modul lemezképeit.

   Ha több tároló-nyilvántartóval rendelkezik a modul lemezképének üzemeltetéséhez, ezt a feladatot duplikálni kell, válassza a másik tároló-beállításjegyzék lehetőséget, majd a speciális beállítások **megkerüléséhez a modul (ok)** megkerüléséhez használja az adott beállításjegyzékhez nem tartozó lemezképeket.

8. A szerkesztéshez válassza a **fájlok másolása** feladatot. Ezzel a feladattal másolhat fájlokat az összetevő-előkészítési könyvtárba.

   * **Megjelenítendő név**: fájlok másolása ide: drop mappába.
   * **Tartalom**: ebben a szakaszban két sort kell elhelyezni, `deployment.template.json` és `**/module.json`. Ez a két típusú fájl a IoT Edge üzembe helyezési jegyzék előállításához szükséges bemenet. Az összetevő átmeneti mappájába kell másolni, és közzé kell tenni a kiadási folyamat számára.
   * **Célmappa**: helyezze a változót `$(Build.ArtifactStagingDirectory)`. A leírással kapcsolatos további tudnivalókért lásd: [Build változók](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) .

9. A szerkesztéshez válassza a Build-összetevők **közzététele** feladatot. Adja meg a feladat átmeneti könyvtárának elérési útját a feladathoz, hogy az elérési út közzétehető legyen a kiadási folyamatban.
   
   * **Megjelenítendő név**: közzétételi összetevő: drop.
   * **Közzététel elérési útja**: helyezze a változót `$(Build.ArtifactStagingDirectory)`. A leírással kapcsolatos további tudnivalókért lásd: [Build változók](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) .
   * Összetevő **neve**: drop.
   * Összetevő **közzétételi helye**: Azure-folyamatok.


10. Nyissa meg az **Eseményindítók** lapot, és jelölje be a jelölőnégyzetet a **folyamatos integráció engedélyezéséhez**. Győződjön meg arról, hogy a kódot tartalmazó ág szerepel benne.

    ![Folyamatos integrációs trigger bekapcsolása](./media/how-to-ci-cd/configure-trigger.png)

11. Mentse az új Build-folyamatot **Mentés** gombbal.

Ez a folyamat most úgy van konfigurálva, hogy automatikusan fusson, amikor új kódot küld a tárházba. Az utolsó feladat, amely közzéteszi a folyamat összetevőit, elindítja a kiadási folyamatokat. Folytassa a következő szakasszal a kiadási folyamat felépítéséhez. 

## <a name="configure-continuous-deployment"></a>Folyamatos üzembe helyezés konfigurálása
Ebben a szakaszban létrehoz egy kiadási folyamatot, amely úgy van beállítva, hogy automatikusan fusson, amikor a build-folyamat elveszíti az összetevőket, és az üzembe helyezési naplókat az Azure-folyamatokban fogja megjeleníteni.

Új folyamat létrehozása és új szakasz hozzáadása 

1. A **kiadások** lapon válassza az **+ új folyamat**elemet. Ha már rendelkezik kiadási folyamatokkal, kattintson az **+ új** gombra, és válassza az **+ új kiadási folyamat**lehetőséget.  

    ![Kiadási folyamat hozzáadása](./media/how-to-ci-cd/add-release-pipeline.png)

2. Amikor a rendszer rákérdez a sablon kiválasztására, válassza az **üres feladatokkal**lehetőséget.

    ![Kezdés üres feladatokkal](./media/how-to-ci-cd/start-with-empty-job.png)

3. Az új kiadási folyamat inicializálása egyetlen fázissal, az **1. fázis**néven. Nevezze át az 1. szakaszt a fejlesztéshez **és a** tesztelési környezetként való kezeléséhez. A folyamatos üzembe helyezési folyamatok általában több szakaszból állnak **, beleértve a**fejlesztést, az **előkészítést** és a **gyártási**folyamatot. A DevOps gyakorlat alapján többet is létrehozhat. Az Átnevezés után a szakasz részletei ablak bezárásához. 

4. Csatolja a kiadást a build-folyamat által közzétett Build-összetevőkhöz. Kattintson a **Hozzáadás** az összetevők területén elemre.

   ![Összetevők hozzáadása](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Az **összetevő hozzáadása lapon**válassza a forrás típusa **Build**lehetőséget. Ezután válassza ki a projektet és a létrehozott Build-folyamatot. Ezután válassza a **Hozzáadás** lehetőséget.

   ![Build-összetevő hozzáadása](./media/how-to-ci-cd/add-an-artifact.png)

6. Nyissa meg az összetevő-eseményindítókat, és válassza ki a váltást a folyamatos üzembe helyezési eseményindító engedélyezéséhez. Most új kiadás jön létre minden alkalommal, amikor új Build áll rendelkezésre.

   ![Folyamatos üzembe helyezési trigger konfigurálása](./media/how-to-ci-cd/add-a-trigger.png)

7. A **fejlesztői** fázis előre konfigurálva van egy feladattal és nulla tevékenységgel. A folyamat menüben válassza a **feladatok** lehetőséget, majd válassza ki a **fejlesztői** szakaszt.  Válassza ki a feladat és a feladat darabszámát az ebben a szakaszban található feladatok konfigurálásához.

    ![Fejlesztői feladatok konfigurálása](./media/how-to-ci-cd/view-stage-tasks.png)

8. A **fejlesztői** szakaszban egy alapértelmezett **ügynöki feladatot**kell megjelennie. Megadhatja az ügynök feladatának adatait, de az üzembe helyezési feladat a platformtól eltérő, így az **ügynök-készletben** (vagy a saját maga által felügyelt bármely más ügynökben) **üzemeltetett VS2017** vagy **üzemeltetett Ubuntu 1604** is használható. 

9. Válassza ki a plusz jelet ( **+** ) két feladat hozzáadásához. Keresse meg és adja hozzá a **Azure IoT Edge** kétszer.

    ![Fejlesztési feladatok hozzáadása](./media/how-to-ci-cd/add-task-qa.png)

10. Válassza ki az első **Azure IoT Edge** feladatot, és konfigurálja a következő értékekkel:

    * **Megjelenítendő név**: a művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. 
    * **Művelet**: a legördülő lista használatával válassza ki az **üzembe helyezési jegyzék előállítása**lehetőséget. A művelet értékének módosítása a feladat megjelenítendő nevét is frissíti a megfeleltetéshez.
    * **. template. JSON fájl**: helyezze el az elérési utat `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. Az elérési út közzé lett téve a Build-folyamatból.
    * **Alapértelmezett platform**: válassza ki ugyanazt az értéket a modul lemezképének létrehozásakor.
    * **Kimeneti elérési út**: helyezze el az elérési utat `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. Ez az elérési út a végső IoT Edge telepítési jegyzékfájl.

    Ezek a konfigurációk segítenek lecserélni a modul képurl-címeit a `deployment.template.json` fájlban. Az **üzembe helyezési jegyzék létrehozása** a `deployment.template.json` fájlban megadott pontos értékkel rendelkező változók cseréjét is lehetővé teszi. A VS/VS kódban a tényleges értéket kell megadnia egy `.env` fájlban. Az Azure-folyamatokban a kiadási folyamat változói lapon adja meg az értéket. Váltson át a változók lapra, és konfigurálja a nevet és az értéket az alábbiak szerint.

    * **ACR_ADDRESS**: a Azure Container Registry-címe. 
    * **ACR_PASSWORD**: a Azure Container Registry jelszava.
    * **ACR_USER**: a Azure Container Registry felhasználóneve.

    Ha a projektben más változók is szerepelnek, akkor ebben a lapon megadhatja a nevet és az értéket. Az **üzembe helyezési jegyzék létrehozása** csak `${VARIABLE}` íz esetén ismeri fel a változókat, ügyeljen arra, hogy a `*.template.json`-fájlokban ezt használja.

    ![Változók konfigurálása a kiadási folyamathoz](./media/how-to-ci-cd/configure-variables.png)

10. Válassza ki a második **Azure IoT Edge** feladatot, és konfigurálja a következő értékekkel:

    * **Megjelenítendő név**: a művelet mező megváltozásakor a megjelenítendő név automatikusan frissül. 
    * **Művelet**: a legördülő lista használatával válassza ki a **IoT Edge eszközök üzembe helyezését**. A művelet értékének módosítása a feladat megjelenítendő nevét is frissíti a megfeleltetéshez.
    * **Azure-előfizetés**: válassza ki a IoT Hubt tartalmazó előfizetést.
    * **IoT hub neve**: válassza ki az IoT hubot. 
    * **Válasszon egy/több eszközt**: válassza ki, hogy szeretné-e a kiadási folyamatot egy vagy több eszközre telepíteni. 
      * Ha egyetlen eszközre telepít központilag, adja meg **IoT Edge eszköz azonosítóját**. 
      * Ha több eszközre telepít üzembe helyezést, az eszköz **célját**kell megadnia. A célként megadott feltétel egy szűrő, amely a IoT Hub IoT Edge-eszközeinek felel meg. Ha az eszköz címkéit feltételként szeretné használni, frissítenie kell a megfelelő eszközök címkéit IoT Hub-eszköz Twin használatával. Frissítse a **IoT Edge központi telepítési azonosítót** és a **IoT Edge központi telepítési prioritást** a speciális beállítások között. További információ a központi telepítés több eszközhöz való létrehozásáról: [IoT Edge automatikus központi telepítések ismertetése](module-deployment-monitoring.md).
    * Bontsa ki a speciális beállítások, majd a **IoT Edge központi telepítési azonosító**elemet, helyezze a változót `$(System.TeamProject)-$(Release.EnvironmentName)`. Ez a projekt és a kiadás nevét a IoT Edge telepítési azonosítójával képezi le.

11. A **Mentés** gombra kattintva mentheti a módosításokat az új kiadási folyamatba. Térjen vissza a folyamat nézethez a menüből válassza a **folyamat** lehetőséget. 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>IoT Edge CI/CD ellenőrzése a létrehozási és kiadási folyamatokkal

A létrehozási feladatok elindításához leküldheti a véglegesítést a forráskód-adattárba, vagy manuálisan is aktiválhatja azt. Ebben a szakaszban manuálisan indítja el a CI/CD folyamatot a működésének ellenőrzéséhez. Ezután ellenőrizze, hogy az üzembe helyezés sikeres volt-e.

1. Navigáljon a cikk elején létrehozott összeállítási folyamathoz. 

2. A létrehozási folyamat során kiválthat egy felépítési feladatot úgy, hogy kijelöli a **várólista** gombot a következő képernyőképen látható módon.

    ![Manuális trigger](./media/how-to-ci-cd/manual-trigger.png)

3. A folyamat előrehaladásának megtekintéséhez válassza ki a Build feladatot. Ha az összeállítási folyamat sikeresen befejeződött, elindítja a kiadást a **fejlesztői** fázisba. 

    ![Naplók létrehozása](./media/how-to-ci-cd/build-logs.png)

4. A sikeres **fejlesztői** kiadás IoT Edge központi telepítést hoz létre IoT Edge eszközök megcélzásához.

    ![Kiadás a dev-be](./media/how-to-ci-cd/pending-approval.png)

5. A kiadási naplók megjelenítéséhez kattintson a **fejlesztői** fázis elemre.

    ![Kiadási naplók](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>Következő lépések
* IoT Edge DevOps – ajánlott eljárásokat ismertető példa az [Azure DevOps-projektben IoT Edge](how-to-devops-project.md)
* A IoT Edge központi telepítésének megismerése az [egyes eszközök IoT Edge központi telepítések megismeréséhez](module-deployment-monitoring.md)
* Végigvezeti a központi telepítések létrehozásához, frissítéséhez vagy törléséhez szükséges lépéseket a [IoT Edge modulok nagy léptékű üzembe helyezéséhez és figyeléséhez](how-to-deploy-monitor.md).
