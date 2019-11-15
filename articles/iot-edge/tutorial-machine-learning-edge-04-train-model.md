---
title: 'Oktatóanyag: modell betanítása és üzembe helyezése Machine Learning Azure IoT Edge'
description: 'Oktatóanyag: gépi tanulási modell betanítása Azure Machine Learning használatával, majd a modell becsomagolása Azure IoT Edge modulként üzembe helyezhető tároló lemezképként.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8cb3d0329c41256823a66fc3276711c43da61cdd
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112468"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Oktatóanyag: Azure Machine Learning modell betanítása és üzembe helyezése

> [!NOTE]
> Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. Ha ezt a cikket közvetlenül megérkezett, javasoljuk, hogy kezdje a sorozat [első cikkével](tutorial-machine-learning-edge-01-intro.md) a legjobb eredmények érdekében.

Ebben a cikkben a Azure Notebooks először a Machine learning-modellek betanítására használjuk a Azure Machine Learning, majd a modellt egy Azure IoT Edge modulként üzembe helyezhető tároló lemezképként csomagoljuk. A Azure Notebooks kihasználja az Azure Machine Learning munkaterület előnyeit, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használt alapvető blokk.

Az oktatóanyag ezen részében szereplő tevékenységek két jegyzetfüzetben vannak megosztva.

* **01 – turbofan\_regresszió. ipynb:** Ez a jegyzetfüzet végigvezeti a modellek betanításának és közzétételének lépésein Azure Machine Learning használatával. A lépések széles körben a következők:

  1. A betanítási adatgyűjtés letöltése, előkészítése és megismerése
  2. A szolgáltatás munkaterület használata gépi tanulási kísérlet létrehozásához és futtatásához
  3. A modell eredményeinek kiértékelése a kísérletből
  4. A legjobb modell közzététele a szolgáltatás munkaterületen

* **02 – turbofan\_\_Model üzembe helyezése. ipynb:** Ez a jegyzetfüzet az előző jegyzetfüzetben létrehozott modellt használja, és felhasználja egy Azure IoT Edge eszközre való üzembe helyezésre kész tároló-rendszerkép létrehozásához.

  1. Pontozási parancsfájl létrehozása a modellhez
  2. A rendszerkép létrehozása és közzététele
  3. A rendszerkép üzembe helyezése webszolgáltatásként az Azure Container instance szolgáltatásban
  4. A webszolgáltatással ellenőrizheti a modellt, és a rendszerkép a várt módon működik.

A cikkben ismertetett lépéseket általában az adatszakértők végzik.

## <a name="set-up-azure-notebooks"></a>Azure Notebooks beállítása

A két Jupyter jegyzetfüzet és a támogató fájlok üzemeltetéséhez Azure Notebooks használjuk. Itt egy Azure Notebooks projektet hozunk létre és konfigurálunk. Ha nem használt Jupyter és/vagy Azure Notebooks, néhány bevezető dokumentum:

* Rövid útmutató **:** [Jegyzetfüzet létrehozása és megosztása](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Oktatóanyag:** [Jupyter-jegyzetfüzet létrehozása és futtatása Python-val](../notebooks/tutorial-create-run-jupyter-notebook.md)

Csakúgy, mint a fejlesztői virtuális gép előtt, az Azure notebookok használata konzisztens környezetet biztosít a gyakorlathoz.

> [!NOTE]
> A beállítás után a Azure Notebooks szolgáltatás bármely gépről elérhető. A telepítés során a fejlesztői virtuális gépet kell használnia, amely az összes szükséges fájllal rendelkezik.

### <a name="create-an-azure-notebooks-account"></a>Azure Notebooks fiók létrehozása

Az Azure notebook-fiókok függetlenek az Azure-előfizetéstől. A Azure Notebooks használatához létre kell hoznia egy fiókot.

1. Navigáljon az [Azure-jegyzetfüzetekhez](https://notebooks.azure.com).

2. Kattintson a **Bejelentkezés** elemre az oldal jobb felső sarkában.

3. Jelentkezzen be a munkahelyi vagy iskolai fiókjával (Azure Active Directory) vagy a személyes fiókjával (Microsoft-fiókkal).

4. Ha korábban még nem használta Azure Notebooks, a rendszer felszólítja, hogy adjon hozzáférést a Azure Notebooks alkalmazáshoz.

5. Hozzon létre egy felhasználói azonosítót a Azure Notebookshoz.

### <a name="upload-jupyter-notebooks-files"></a>Jupyter-jegyzetfüzetek fájljainak feltöltése

Ebben a lépésben új Azure Notebooks projektet hozunk létre, és fájlokat töltünk fel rá. Pontosabban a feltöltött fájlok a következők:

* **01-turbofan\_regresszió. ipynb**: Jupyter notebook-fájl, amely végigvezeti az eszköz-hám által az Azure Storage-fiókból generált adatok letöltésének folyamatán. az adatosztályozó képzésének feltárása és előkészítése; a modell betanítása; tesztelje az adatokat a test\_FD003. txt fájlban található tesztelési adatkészlet használatával; végül mentse az osztályozó modellt a Machine Learning szolgáltatás munkaterületen.

* **02 – turbofan\_\_Model üzembe helyezése. ipynb:** Jupyter notebook, amely végigvezeti Önt a Machine Learning szolgáltatás munkaterületen mentett osztályozó modell használatának folyamatán a tároló rendszerképének létrehozásához. A rendszerkép létrehozása után a jegyzetfüzet végigvezeti a lemezkép webszolgáltatásként való üzembe helyezésének folyamatán, hogy ellenőrizni tudja, hogy a várt módon működik-e. Az érvényesített lemezkép a IoT Edge eszközre lesz telepítve az oktatóanyag [egyéni IoT Edge modulok létrehozása és telepítése](tutorial-machine-learning-edge-06-custom-modules.md) részében.

* **Teszt\_FD003. txt:** Ez a fájl tartalmazza azokat az adatkészleteket, amelyeket a rendszer a betanított osztályozó ellenőrzésekor használ. Úgy döntöttünk, hogy az eredeti versenyhez megadott tesztelési adatmennyiséget használjuk a példa egyszerűsége érdekében.

* **RUL\_FD003. txt:** Ez a fájl tartalmazza az egyes eszközök utolsó ciklusának RUL a test\_FD003. txt fájlban. Tekintse meg a **readme. txt** **fájlt, valamint a "** C:\\forrás\\IoTEdgeAndMlSample\\\\Turbofan" című témakört, amely az információk részletes ismertetését tartalmazza.

* **Utils.py:** Python segédprogram-függvényeket tartalmaz az adatokkal való munkavégzéshez. Az első jegyzetfüzet a függvények részletes magyarázatát tartalmazza.

* **Readme.MD:** A jegyzetfüzetek használatát leíró readme.

Hozzon létre egy új projektet, és töltse fel a fájlokat a jegyzetfüzetbe.

1. Válassza a **saját projektek** lehetőséget a felső menüsorban.

1. Válassza az **+ új projekt**lehetőséget. Adjon meg egy nevet és egy azonosítót. A projektnek nem kell nyilvánosnak lennie, vagy nem kell megadnia a readme-t.

1. Válassza a **feltöltés** lehetőséget, és válassza **a számítógép**lehetőséget.

1. Válassza a **fájlok kiválasztása**lehetőséget.

1. Navigáljon a **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Jelölje ki az összes fájlt, majd kattintson a **Megnyitás**gombra.

1. Válassza a **feltöltés** lehetőséget a feltöltés megkezdéséhez, majd válassza a **kész** lehetőséget a folyamat befejezése után.

## <a name="run-azure-notebooks"></a>Azure Notebooks futtatása

Most, hogy létrejött a projekt, futtassa a **01-turbofan\_regresszió. ipynb** notebookot.

1. A turbofan projekt lapon válassza a **01-turbofan\_regresszió. ipynb**elemet.

    ![Válassza ki az első futtatandó jegyzetfüzetet](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Ha a rendszer kéri, válassza a Python 3,6 kernel elemet a párbeszédpanelen, és válassza a **kernel beállítása**lehetőséget.

3. Ha a jegyzetfüzet **nem megbízhatóként**jelenik meg, kattintson a jegyzetfüzet jobb felső sarkában található **nem megbízható** elemre. Amikor megjelenik a párbeszédpanel, válassza a **megbízhatóság**lehetőséget.

4. Kövesse a jegyzetfüzet utasításait.

    * `Ctrl + Enter` egy cellát futtat.
    * `Shift + Enter` futtat egy cellát, és a következő cellára navigál.
    * Amikor egy cella fut, a szögletes zárójelek közé tartozik a csillag, például: **[\*]** . Ha a művelet befejeződött, a csillag helyére egy szám és egy kapcsolódó kimenet is tartozhat. Mivel a cellák gyakran az előzőek munkájára épülnek, egyszerre csak egy cella futhat.

5. Ha befejezte a **01-turbofan\_regresszió. ipynb** jegyzetfüzet futtatását, térjen vissza a projekt lapra.

6. Nyissa meg a **02-turbofan\_telepítse\_Model. ipynb** , majd ismételje meg a szakasz lépéseit a második jegyzetfüzet futtatásához.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben két, Azure Notebooks futó Jupyter-jegyzetfüzetet használtunk a turbofan-eszközökből származó adatok használatára a hátralévő hasznos élettartam (RUL) osztályozó, az osztályozó modellként való mentéséhez, a tároló lemezképének létrehozásához, valamint a lemezkép webes se-ként való üzembe helyezéséhez és teszteléséhez. rvice.

IoT Edge-eszköz létrehozásához folytassa a következő cikkel.

> [!div class="nextstepaction"]
> [IoT Edge eszköz konfigurálása](tutorial-machine-learning-edge-05-configure-edge-device.md)
