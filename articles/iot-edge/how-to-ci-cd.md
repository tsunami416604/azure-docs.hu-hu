---
title: Folyamatos integráció & folyamatos üzembe helyezés - Azure IoT Edge
description: Folyamatos integráció és folyamatos üzembe helyezés beállítása – Azure IoT Edge az Azure DevOps és az Azure Pipelines segítségével
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b99e83a8e71b13183c76321c7076b85a212f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510974"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge-ben

Az Azure IoT Edge-alkalmazásaival könnyedén alkalmazhat DevOps-t az Azure IoT Edge-feladatok beépített Azure IoT Edge-feladataival az Azure Pipelines-ban. Ez a cikk bemutatja, hogyan használhatja az Azure Pipelines folyamatos integrációs és folyamatos üzembe helyezési funkcióit az alkalmazások gyors és hatékony létrehozásához, teszteléséhez és üzembe helyezéséhez az Azure IoT Edge-ben.

![Diagram - CI és CD ágak fejlesztési és termelési](./media/how-to-ci-cd/cd.png)

Ebben a cikkben megtudhatja, hogyan használhatja a beépített Azure IoT Edge-feladatok at Azure Pipelines két folyamat ot az IoT Edge-megoldáshoz. Négy művelet használható az Azure IoT Edge-feladatokban.

* **Azure IoT Edge – Build module rendszerképek** az IoT Edge-megoldás kódját, és létrehozza a tárolórendszerképeket.
* **Azure IoT Edge – Leküldéses modul lemezképek** leküldéses modullemezt a megadott tároló beállításjegyzékbe.
* **Azure IoT Edge – A telepítési manifest létrehozása** vesz egy deployment.template.json fájlt és a változókat, majd létrehozza a végső IoT Edge-telepítési jegyzékfájl.
* **Azure IoT Edge – Az IoT Edge-eszközöküzembe helyezése** segít az IoT Edge-telepítések létrehozásában egy/több IoT Edge-eszközökre.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Repos tárház. Ha nem rendelkezik ilyentel, [létrehozhat egy új Git-tártavetőket a projektben.](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav)
* Egy IoT Edge-megoldás véglegesítve és leküldéses a tárház. Ha új mintamegoldást szeretne létrehozni a cikk teszteléséhez, kövesse a Modulok fejlesztése és hibakeresése című, [a Visual Studio-kódban](how-to-vs-code-develop-module.md) vagy a [C# modulok fejlesztése és hibakeresése](how-to-visual-studio-develop-csharp-module.md)a Visual Studio alkalmazásban című részben leírt lépéseket.

   Ebben a cikkben mindössze az IoT Edge-sablonok által a Visual Studio-kódban vagy a Visual Studio-ban létrehozott megoldásmappára van szüksége. A folytatás előtt nem kell megépítenie, leküldéses, üzembe helyeznie vagy hibakeresést adhatja. Ezeket a folyamatokat az Azure-folyamatokban állíthatja be.

   Ha új megoldást hoz létre, először klónozza a tárházat helyileg. Ezután a megoldás létrehozásakor kiválaszthatja, hogy közvetlenül a tárházban hozza létre. Könnyedén véglegesítheti és lenyomhatja az új fájlokat.

* Egy tároló-beállításjegyzék, ahol leküldéses modul lemezképek. Használhatja [az Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy egy külső gyártó rendszerleíró adatbázis.
* Egy aktív [IoT hub](../iot-hub/iot-hub-create-through-portal.md) legalább IoT Edge-eszközök tesztelésére a külön tesztelési és éles üzembe helyezési fázisok. A rövid útmutató cikkek et követve létrehozhat egy IoT Edge-eszközt [Linuxon](quickstart-linux.md) vagy [Windowsrendszeren](quickstart.md)

Az Azure Repos használatáról a [Kód megosztása a Visual Studióval és az Azure Reposokkal](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts) című témakörben talál további információt.

## <a name="configure-continuous-integration"></a>A folyamatos integráció konfigurálása

Ebben a szakaszban hozzon létre egy új buildfolyamat. Állítsa be a folyamat automatikusan fut, amikor beadja a minta IoT Edge-megoldás módosításait, és közzéteszi a buildnaplókat.

>[!NOTE]
>Ez a cikk az Azure DevOps vizuális tervezőt használja. Mielőtt kövesse az ebben a szakaszban leírt lépéseket, kapcsolja ki az új YAML-folyamat létrehozási felületének előnézeti funkcióját.
>
>1. Az Azure DevOps-ban válassza a profil ikonját, majd a **Funkciók előnézete**lehetőséget.
>2. Kapcsolja ki **az új YAML-folyamat létrehozási felületét.**
>
>További információ: [Buildpipeline létrehozása.](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline)

1. Jelentkezzen be az Azure DevOps-szervezetbe (**\/https: /dev.azure.com/{a szervezet),** és nyissa meg az IoT Edge-megoldástárját tartalmazó projektet.

   Ebben a cikkben létrehoztunk egy **IoTEdgeRepo**nevű tárházat. Ez a tárház tartalmazza az **IoTEdgeSolution nevű** modul kódját. **filtermodule**

   ![A DevOps-projekt megnyitása](./media/how-to-ci-cd/init-project.png)

2. Keresse meg az Azure-folyamatokat a projektben. Nyissa meg a **Builds** lapot, és válassza az **Új folyamat lehetőséget.** Vagy ha már rendelkezik buildfolyamatokkal, válassza az **Új** gombot. Ezután válassza **az Új összeállítási folyamat lehetőséget**.

    ![Új buildfolyamat létrehozása](./media/how-to-ci-cd/add-new-build.png)

3. A folyamat létrehozásához kövesse az utasításokat.

   1. Adja meg az új buildfolyamat forrásadatait. Válassza ki az **Azure Repos Git** forrásként, majd válassza ki a projekt, a tárház és az ág, ahol az IoT Edge-megoldás kód található. Ezután válassza a **Folytatás gombot.**

      ![A folyamatforrás kiválasztása](./media/how-to-ci-cd/pipeline-source.png)

   2. Sablon helyett válassza az **Üres feladat** lehetőséget.

      ![Kezdje üres folyamattal](./media/how-to-ci-cd/start-with-empty.png)

4. A folyamat létrehozása után a folyamat szerkesztője kerül a folyamatszerkesztőhöz. A folyamat leírásában válassza ki a megfelelő ügynökkészletet a célplatform alapján:

   * Ha a modulokat linuxos tárolók platformjában szeretné megépíteni, válassza a **Hosted Ubuntu 1604 lehetőséget.**

   * Ha a modulokat a Windows 1809-tárolók platformjában szeretné létrehozni, be kell [állítania a windowsos saját üzemeltetésű ügynököt.](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts)

   * Ha szeretné építeni a modulokat platform kar32v7 vagy arm64 Linux konténerek, be kell [állítani a saját üzemeltetésű ügynök Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).

     ![Buildügynök-készlet konfigurálása](./media/how-to-ci-cd/configure-env.png)

5. A folyamat előre konfigurálva van az **1.Agent job 1**nevű feladattal. Válassza ki a**+** pluszjel ( ) három feladatot a feladathoz: **Az Azure IoT Edge** kétszer, fájlok **másolása** egyszer és **közzétételi összetevők** egyszer. (A **Hozzáadás** gombra mutató egérmutatót az egyes feladatok neve fölé viszi.)

   ![Azure IoT Edge-feladat hozzáadása](./media/how-to-ci-cd/add-iot-edge-task.png)

   Ha mind a négy feladatot hozzáadja, az ügynök idopont a következő példához hasonlóan néz ki:

   ![Három feladat a buildfolyamatban](./media/how-to-ci-cd/add-tasks.png)

6. Válassza ki az első **Azure IoT Edge-feladatot** a szerkesztéshez. Ez a feladat a megadott célplatformmal együtt létrehozza a megoldás összes modulját.

   * **Megjelenítendő név**: Fogadja el az alapértelmezett **Azure IoT Edge – Build modulképeket.**
   * **Művelet**: Fogadja el az alapértelmezett **Build modulképeket.**
   * **.template.json fájl**: Jelölje ki a három pontot (**...**), és keresse meg a **deployment.template.json** fájlt az IoT Edge-megoldást tartalmazó tárházban.
   * **Alapértelmezett platform:** Válassza ki a modulok megfelelő platformját a cél IoT Edge-eszköz alapján.
   * **Kimeneti változók**: A kimeneti változók tartalmaznak egy hivatkozásnevet, amely segítségével konfigurálhatja a fájl elérési útját, ahol a deployment.json fájl jön létre. Állítsa be a hivatkozás nevét, hogy valami emlékezetes, mint **a széle**.

7. Válassza ki a második **Azure IoT Edge-feladatot** a szerkesztéshez. Ez a feladat leküldéses az összes modullemezt a tároló beállításjegyzékbe, amely kiválasztott.

   * **Megjelenítendő név**: A műveletmező változásakor a megjelenítendő név automatikusan frissül.
   * **Művelet**: A legördülő listával válassza a **Leküldéses modul képeit.**
   * **Tároló beállításjegyzék-típusa**: Válassza ki a modullemezképek tárolásához használt tárolóbeállítás-jegyzék típusát. A választott beállításjegyzék-típustól függően az űrlap megváltozik. Ha az Azure Container Registry lehetőséget **választja,** a legördülő listák segítségével válassza ki az Azure-előfizetést és a tároló beállításjegyzékének nevét. Ha az Általános tároló beállításjegyzék lehetőséget **választja,** a beállításjegyzék-szolgáltatás kapcsolatának létrehozásához válassza az **Új** lehetőséget.
   * **.template.json fájl**: Jelölje ki a három pontot (**...**), és keresse meg a **deployment.template.json** fájlt az IoT Edge-megoldást tartalmazó tárházban.
   * **Alapértelmezett platform:** Válassza ki ugyanazt a platformot, mint a beépített modulképek.

   Ha több tárolóbeállításjegyzéket is üzemeltet a modullemezképek üzemeltetéséhez, meg kell kettőznie ezt a feladatot, ki kell választania a különböző tárolóbeállításjegyzéket, és a speciális beállításokban a **Bypass modul(oka)t** kell használnia a nem az adott beállításjegyzékhez tartozó lemezképek megkerüléséhez.

8. A szerkesztéshez jelölje ki a **Fájlok másolása** feladatot. Ezzel a feladattal fájlokat másolhat a műtermék átmeneti könyvtárába.

   * **Megjelenítendő név**: Fájlok másolása a következőbe: Drop mappa.
   * **Tartalom:** Két sort helyezzen `deployment.template.json` `**/module.json`ebbe a szakaszba, és . Ez a két típusú fájlok a bemenetek ioT Edge központi telepítési jegyzékfájl létrehozásához. A műtermék átmeneti mappájába kell másolni, és közzé kell tenni a kiadási folyamathoz.
   * **Célmappa**: Tegye `$(Build.ArtifactStagingDirectory)`a változót . A leírásról a [Változók összeállítása](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) című témakörben olvashat.

9. A szerkesztéshez jelölje ki a **Build-összetevők közzététele** feladatot. Adja meg a műtermék átmeneti könyvtárának elérési útját a feladathoz, hogy az elérési út közzétehesse a folyamat kiadásához.

   * **Megjelenítendő név**: Műtermék közzététele: csepp.
   * **Közzétételi elérési út** `$(Build.ArtifactStagingDirectory)`: Tegye a változót . A leírásról a [Változók összeállítása](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) című témakörben olvashat.
   * **Műtermék neve**: drop.
   * **Műtermék közzétételi hely:** Azure-folyamatok.

10. Nyissa meg az **Eseményindítók** lapot, és jelölje be a **folyamatos integráció engedélyezéséhez**jelölőnégyzetet. Győződjön meg arról, hogy a kódot tartalmazó ág szerepel a csomagban.

    ![A folyamatos integrációs eseményindító bekapcsolása](./media/how-to-ci-cd/configure-trigger.png)

11. Mentse az új buildfolyamatot a **Mentés** gombbal.

Ez a folyamat most úgy van beállítva, hogy automatikusan fusson, amikor új kódot atárba leküldéses. Az utolsó feladat, a folyamatösszetevők közzététele, egy kiadási folyamatot indít el. Folytassa a következő szakaszra a kiadási folyamat létrehozásához.

## <a name="configure-continuous-deployment"></a>Folyamatos üzembe helyezés konfigurálása

Ebben a szakaszban hozzon létre egy kiadási folyamat, amely automatikusan fut, amikor a build-folyamat leállítja az összetevőket, és az Azure-folyamatok ban központi telepítési naplók jelennek meg.

Új folyamat létrehozása és új szakasz hozzáadása

1. A **Felengedések** lapon válassza a **+ Új folyamat lehetőséget.** Ha már rendelkezik kiadási folyamatokkal, válassza a **+ Új** gombot, és válassza a + Új kiadási **folyamat lehetőséget**.  

    ![Kiadási folyamat hozzáadása](./media/how-to-ci-cd/add-release-pipeline.png)

2. Amikor a rendszer egy sablon kiválasztását kéri, válassza az **Üres feladattal**való kezdést.

    ![Kezdje üres feladattal](./media/how-to-ci-cd/start-with-empty-job.png)

3. Az új kiadási folyamat egy fázissal inicializálódik, **az**1. Az 1. **dev** A folyamatos üzembe helyezési folyamatok általában több szakaszból állnak, beleértve **a fejlesztési,** **átmeneti** és **prod fázisokat.** A DevOps-gyakorlat alapján további. Az átnevezésután zárja be a színpad i.

4. Kapcsolja össze a kiadást a build-folyamat által közzétett buildösszetevőkkel. Kattintson a **Hozzáadás** az összetevők területén gombra.

   ![Összetevők hozzáadása](./media/how-to-ci-cd/add-artifacts.png)  

5. A **Műtermék hozzáadása lapon**válassza a **Build**forrástípust. Ezután válassza ki a projektet és a létrehozott buildfolyamatot. Ezután válassza a **Hozzáadás** lehetőséget.

   ![Buildösszetevő hozzáadása](./media/how-to-ci-cd/add-an-artifact.png)

6. Nyissa meg a műtermék-eseményindítókat, és válassza ki a kapcsolót a folyamatos üzembe helyezési eseményindító engedélyezéséhez. Most egy új kiadás jön létre minden alkalommal, amikor egy új build érhető el.

   ![Folyamatos üzembe helyezési eseményindító konfigurálása](./media/how-to-ci-cd/add-a-trigger.png)

7. A **fejlesztési** szakasz előre konfigurálva van egy feladattal és nulla feladattal. A folyamat menüben válassza a **Feladatok** lehetőséget, majd válassza ki a **fejlesztési** fázist.  Válassza ki a feladatot és a feladatok számát a feladatok konfigurálásához ebben a fázisban.

    ![Fejlesztési feladatok konfigurálása](./media/how-to-ci-cd/view-stage-tasks.png)

8. A **fejlesztési** szakaszban egy alapértelmezett **ügynöki feladatnak**kell lennie. Konfigurálhatja az ügynökfeladat részleteit, de a telepítési feladat platformon nem érzékeny, így használhatja a **Hosted VS2017** vagy a **Hosted Ubuntu 1604** az **ügynökkészletben** (vagy bármely más, saját maga által kezelt ügynök).

9. Két feladat hozzáadásához válassza ki a pluszjelet (**+**). Kétszer keresse meg és adja hozzá **az Azure IoT Edge-et.**

    ![Feladatok hozzáadása a fejlesztéshez](./media/how-to-ci-cd/add-task-qa.png)

10. Válassza ki az első **Azure IoT** Edge-feladatot, és konfigurálja a következő értékekkel:

    * **Megjelenítendő név**: A műveletmező változásakor a megjelenítendő név automatikusan frissül.
    * **Művelet**: A legördülő lista segítségével válassza **a Telepítési jegyzék létrehozása**lehetőséget. A műveletérték módosítása a feladat megjelenítendő nevét is frissíti.
    * **.template.json fájl**: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`Tegye az elérési utat . Az elérési út a buildfolyamatból kerül közzétételre.
    * **Alapértelmezett platform:** Válassza ki ugyanazt az értéket a modulképek létrehozásakor.
    * **Kimeneti útvonal**: `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`Tegye az elérési utat . Ez az elérési út a végső IoT Edge központi telepítési jegyzékfájl.

    Ezek a konfigurációk segítenek a modulkép URL-címének cseréjében a `deployment.template.json` fájlban. A **központi telepítési jegyzékfájl létrehozása** is segít lecserélni a `deployment.template.json` változókat a fájlban megadott pontos értékre. A VS/VS kód mezőben a `.env` fájl tényleges értékét adja meg. Az Azure-folyamatokban állítsa be az értéket a Kiadási folyamat változók lapon. Move to Variables (Áthelyezés a változók lapra, és konfigurálja a név és az érték a következőképpen.

    * **ACR_ADDRESS**: Az Azure Container Registry-cím.
    * **ACR_PASSWORD**: Az Azure Container Registry jelszava.
    * **ACR_USER**: Az Azure Container Registry felhasználóneve.

    Ha a projektben más változók is szerepelnek, ezen a lapon adhatja meg a nevet és az értéket. A **központi telepítési jegyzékfájl létrehozása** csak `${VARIABLE}` akkor ismeri fel a változók íz, győződjön meg róla, hogy használja ezt a `*.template.json` fájlokat.

    ![Változók konfigurálása a kiadási folyamathoz](./media/how-to-ci-cd/configure-variables.png)

11. Válassza ki a második **Azure IoT** Edge-feladatot, és konfigurálja a következő értékekkel:

    * **Megjelenítendő név**: A műveletmező változásakor a megjelenítendő név automatikusan frissül.
    * **Művelet**: A legördülő listával válassza **az IoT Edge-eszközök üzembe helyezését.** A műveletérték módosítása a feladat megjelenítendő nevét is frissíti.
    * **Azure-előfizetés:** Válassza ki az IoT Hubot tartalmazó előfizetést.
    * **IoT Hub-név:** Válassza ki az IoT hub.
    * **Válassza ki az egy/több eszközt:** Válassza ki, hogy a kiadási folyamatot egy vagy több eszközre kívánja-e telepíteni.
      * Ha egyetlen eszközre telepíti, adja meg az **IoT Edge-eszköz azonosítóját.**
      * Ha több eszközre telepít, adja meg az eszköz **célfeltételét.** A célfeltétel egy szűrő, amely megfelel az IoT Edge-eszközök ioT Hub egy készletének. Ha azt szeretné, hogy az eszközcímkék feltételként, frissítenie kell a megfelelő eszközök címkék IoT Hub-eszköz iker. Frissítse az **IoT Edge központi telepítési azonosítóés** **az IoT Edge telepítési prioritása** a speciális beállításokat. A több eszköz központi telepítésének létrehozásáról az [IoT Edge automatikus központi telepítéseinek ismertetése](module-deployment-monitoring.md)című témakörben talál további információt.
    * Bontsa ki a Speciális beállítások csomópontot, `$(System.TeamProject)-$(Release.EnvironmentName)`válassza **az IoT Edge telepítési azonosítója**lehetőséget, helyezze a változót. Ez leképezi a projekt et és a kiadás nevét az IoT Edge központi telepítési azonosítójával.

12. Válassza a **Mentés** lehetőséget az új kiadási folyamat módosítási adatainak mentéséhez. Térjen vissza a folyamatnézetbe a menü **Folyamat** parancsának kiválasztásával.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>IoT Edge CI/CD ellenőrzése a build- és kiadási folyamatokkal

Build-feladat indításához leküldéses véglegesítésforráskód-tárházba, vagy manuálisan is elindíthatja azt. Ebben a szakaszban manuálisan elindítja a CI/CD-folyamatot annak teszteléséhez, hogy működik-e. Ezután ellenőrizze, hogy a központi telepítés sikeres-e.

1. Keresse meg a cikk elején létrehozott buildfolyamatot.

2. A buildelési folyamatban a **várólista** gombra kattintva indíthat el buildelési feladatot, mint a következő képernyőképen.

    ![Kézi eseményindító](./media/how-to-ci-cd/manual-trigger.png)

3. Válassza ki a build feladat nézni annak előrehaladását. Ha a buildfolyamat sikeresen befejeződött, a **fejlesztési** fázisban egy kiadást indít el.

    ![Naplók létrehozása](./media/how-to-ci-cd/build-logs.png)

4. A sikeres **fejlesztési** kiadás létrehozza az IoT Edge-telepítést az IoT Edge-eszközök célzásához.

    ![Kiadás a fejlesztési](./media/how-to-ci-cd/pending-approval.png)

5. Kattintson **a fejlesztési** szakaszra a kiadási naplók megtekintéséhez.

    ![Kiadási naplók](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>További lépések

* Az IoT Edge DevOps ajánlott eljárások minta az [Azure DevOps Project ioT Edge-hez](how-to-devops-project.md)
* Ismerje meg az IoT Edge üzembe helyezését [az IoT Edge-telepítések megismerése egyetlen eszközökön vagy nagy méretekben](module-deployment-monitoring.md)
* Az [IoT Edge-modulok nagy méretekben történő](how-to-deploy-monitor.md)telepítésével és figyelésével végigvezeti a központi telepítés lépéseit.
