---
title: Betanítása és a modell üzembe helyezése – Machine Learning az Azure IoT Edge |} A Microsoft Docs
description: Betanítása egy gépi tanulási modellt az Azure Machine Learning használatával, és a modell egy tárolórendszerképet, mint az Azure IoT Edge-modul telepíthető, majd csomagot.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: af91dc09f8ed68d7bd4f2378c13d99eb67c52e9e
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155649"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Oktatóanyag: Azure Machine Learning-modell betanítása és üzembe helyezése

> [!NOTE]
> Ez a cikk egy sorozat része az IoT Edge-ben az Azure Machine Learning használatával kapcsolatos oktatóanyagot. Ha érkezett, ez a cikk közvetlenül, javasoljuk, hogy először a [először cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredmények.

Ebben a cikkben használjuk az Azure-jegyzetfüzetek először betanítása egy gépi tanulási modellt az Azure Machine Learning használatával, és ehhez a modellhez, amely egy Azure IoT Edge-modul telepíthető egy tárolórendszerképet, majd csomagot. Az Azure-jegyzetfüzetek kihasználhatja az Azure Machine Learning szolgáltatás munkaterületen található, amely egy alapvető blokk kísérletezhet, betanítását és üzembe helyezése a machine learning-modellek segítségével.

Az oktatóanyag ezen részében a tevékenységek között két jegyzetfüzetet vannak osztva.

* **01-turbofan\_regression.ipynb:** Ez a jegyzetfüzet ismerteti a lépéseket, hogy a betanítás, közzététel a modell Azure Machine Learning segítségével. Széles körben a folyamat lépései a következők:

  1. Töltse le, előkészítése és a betanítási adatok megismerése
  2. A szolgáltatás munkaterület segítségével hozhat létre és futtathat a machine learning-kísérletből
  3. A kísérlet modell eredményeinek kiértékelése
  4. A legjobb modellt közzé a szolgáltatás munkaterületén

* **02-turbofan\_deploy\_model.ipynb:** Ez a jegyzetfüzet vesz igénybe a az előző jegyzetfüzetben létrehozott modellt, és készen áll az Azure IoT Edge-eszköz üzembe helyezhető a tárolórendszerkép létrehozásához használja.

  1. A modell egy pontozó szkript létrehozása
  2. Hozzon létre és tehet közzé a lemezképet
  3. A rendszerkép üzembe webszolgáltatásként, amely az Azure-Tárolópéldányon
  4. A webszolgáltatás segítségével ellenőrizheti a modell és a rendszerkép az elvárt módon működnek

A jelen cikkben ismertetett lépések a adatszakértők általában előfordulhat, hogy végre.

## <a name="set-up-azure-notebooks"></a>Állítsa be az Azure-jegyzetfüzetek

Az Azure-jegyzetfüzeteket a két Jupyter-notebookok és a kiegészítő fájlt használjuk. Itt hozunk létre, és egy Azure-jegyzetfüzetek projekt konfigurálása. Ha még nem használta a Jupyter és/vagy Azure notebookok, az alábbiakban néhány bevezető dokumentumok:

* **Gyors útmutató:** [Hozzon létre és osszon meg a notebook](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Oktatóanyag:** [Létrehozása és futtatása a Jupyter notebook Python használatával](../notebooks/tutorial-create-run-jupyter-notebook.md)

Mint a fejlesztői virtuális gépet, mielőtt Azure notebookok használatával biztosítja a gyakorlatban egy egységes környezetet.

> [!NOTE]
> Beállítás után az Azure-jegyzetfüzetek szolgáltatás elérhetők minden olyan gép. A telepítés során a fejlesztési virtuális gépre, amely rendelkezik az összes szükséges fájlt kell használnia.

### <a name="create-an-azure-notebooks-account"></a>Hozzon létre egy jegyzetfüzetet az Azure-fiókot

Azure jegyzetfüzet-fiókok Azure-előfizetésekből függetlenek egymástól. Azure notebookok használata esetén szüksége hozhat létre fiókot.

1. Navigáljon a [Azure notebookok](http://notebooks.azure.com).

2. Kattintson a **bejelentkezés** a lap felső, jobb oldali sarokban.

3. Jelentkezzen be a munkahelyi vagy iskolai fiókkal (Azure Active Directory), vagy a személyes fiókjával (Microsoft Account).

4. Ha még nem használta az Azure-jegyzetfüzetek előtt, a program kéri hozzáférést biztosít az Azure-jegyzetfüzetek alkalmazás.

5. Felhasználói azonosító létrehozása az Azure-jegyzetfüzeteket.

### <a name="upload-jupyter-notebooks-files"></a>A Jupyter notebookok fájlok feltöltése

Ebben a lépésben hozunk létre egy új jegyzetfüzetet az Azure-projektet, és fájlokat feltölteni. Pontosabban a fájlokat, amelyek fel a következők:

* **01-turbofan\_regression.ipynb**: Jupyter notebook fájlt, amely végigvezeti a folyamat, az eszköz kihasználhatja az Azure storage-fiókból; által előállított adatok letöltése az adatok előkészítése az osztályozó által igénybe vett; oktatási elemzését és a a modell; a teszt tesztelése az adatokat a tesztelési adatkészletnél található\_FD003.txt fájlt, és végül mentése az osztályozó által igénybe vett minta a Machine Learning szolgáltatás munkaterületen.

* **02-turbofan\_deploy\_model.ipynb:** Jupyter notebookot, amely végigvezeti a tárolórendszerkép létrehozásához a Machine Learning szolgáltatás munkaterületén mentett osztályozó modellt használja. A lemezkép létrehozása után várható a notebook ismerteti, hogyan bemutatja, hogyan üzembe helyezését a kép webszolgáltatásként, így ellenőrizheti, hogy az megfelelően működik. Az ellenőrzött lemezképet telepíti az Edge-eszközön a a [létrehozása és üzembe helyezése az egyéni IoT Edge-modulok](tutorial-machine-learning-edge-06-custom-modules.md) ebben az oktatóanyagban részét.

* **Test\_FD003.txt:** Ez a fájl tartalmazza az adatokat, állítsa be a betanított osztályozó érvényesítésekor tesztben használjuk. Használja a tesztadatok, ahogyan azt az eredeti verseny, állítsa be az a példában az egyszerűség tesztben választottuk.

* **RUL\_FD003.txt:** Ez a fájl tartalmazza az RUL a legutóbbi ciklus minden eszköz a teszt\_FD003.txt fájlt. Tekintse meg a **readme.txt** és a **kárt propagálás Modeling.pdf** a c: fájlok\\forrás\\IoTEdgeAndMlSample\\adatok\\a Turbofan egy az adatok részletes leírását.

* **Utils.PY:** Python-adatok használata segédprogram funkciót tartalmazza. Az első Jegyzetfüzet az funkciók részletes leírását tartalmazza.

* **README.md:** A jegyzetfüzetek használatát ismertető információs fájlban olvasható.

Hozzon létre egy új projektet, és töltse fel a fájlokat a notebookot.

1. Válassza ki **saját projektek** a felső menüsorban.

1. Válassza ki **+ új projekt**. Adjon meg egy nevet és egy. Létezik, a projekt nem szükséges, hogy egy információs vagy nyilvános.

1. Válassza ki **feltöltése** válassza **a számítógép**.

1. Válassza ki **fájlok kiválasztása**.

1. Navigáljon a **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Válassza ki az összes fájlt, és kattintson a **nyílt**.

1. Válassza ki **feltöltése** feltöltődnek, és válassza ki a **kész** a folyamat befejeződése után.

## <a name="run-azure-notebooks"></a>Az Azure-jegyzetfüzetek futtatása

Most, hogy a projekt létrejött, futtassa a **01-turbofan\_regression.ipynb** notebookot.

1. Válassza ki a turbofan lapot, **01-turbofan\_regression.ipynb**.

    ![Válassza ki az első jegyzetfüzet futtatásához](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Ha a rendszer kéri, válassza a párbeszédpanelen, majd válassza a Python 3.6-os Kernel **beállítása Kernel**.

3. Ha a notebook **nem megbízható**, kattintson a a **nem megbízható** widget felső a notebookot, jobb. Amikor a párbeszédpanel megjelenik, válassza ki a **megbízható**.

4. Kövesse a Notebookban lévő utasításokat.

    * `Ctrl + Enter` egy cella futtatja.
    * `Shift + Enter` egy cella fut, és a következő cella navigál.
    * Egy cella fut, ha rendelkezik a szögletes zárójelek között csillag például **[\*]** . Ha befejeződött, a csillag váltja fel a szám, és megfelelő kimeneti lehet alatt jelennek meg. Cellák gyakran a korábbiakat munkájára hozhat létre, mert csak egy cella futtatható egyszerre.

5. Miután végzett a futtatását a **01-turbofan\_regression.ipynb** jegyzetfüzet, térjen vissza a lapot.

6. Nyissa meg **02-turbofan\_üzembe helyezése\_model.ipynb** , és ismételje meg a lépéseket ebben a szakaszban a második jegyzetfüzet futtatásához.

## <a name="next-steps"></a>További lépések

Ebben a cikkben, az adatok a turbofan eszközökről használja a fennmaradó hasznos élettartama (RUL) besorolás, az osztályozó által igénybe vett mentse őket egy modellt, hozza létre a tárolórendszerképet, és a üzembe helyezéséhez és teszteléséhez a képet a web se használtuk két jegyzetfüzetet az Azure-ban futó Jupyter-notebookok rvice.

Folytassa a következő cikk IoT Edge-eszköz létrehozása.

> [!div class="nextstepaction"]
> [IoT Edge-eszköz konfigurálása](tutorial-machine-learning-edge-05-configure-edge-device.md)
