---
title: 'Oktatóanyag: modell betanítása és üzembe helyezése Machine Learning Azure IoT Edge'
description: Ebben az oktatóanyagban egy gépi tanulási modellt fog betanítani Azure Machine Learning használatával, majd becsomagolja a modellt Azure IoT Edge modulként üzembe helyezhető tároló-képként.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5f576d28d30907f3834600d0a6a5c152025cf912
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133523"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Oktatóanyag: Azure Machine Learning modell betanítása és üzembe helyezése

> [!NOTE]
> Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. Ha ezt a cikket közvetlenül megérkezett, javasoljuk, hogy kezdje a sorozat [első cikkével](tutorial-machine-learning-edge-01-intro.md) a legjobb eredmények érdekében.

Ebben a cikkben a következő feladatokat hajtjuk végre:

* A Machine learning-modellek betanításához használjon Azure Notebooks.
* Csomagolja ki a betanított modellt tároló képként.
* Telepítse a tároló lemezképét Azure IoT Edge modulként.

A Azure Notebooks kihasználja az Azure Machine Learning munkaterület előnyeit, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használt alapvető blokk.

A cikkben ismertetett lépéseket általában az adatszakértők végzik.

## <a name="set-up-azure-notebooks"></a>Azure Notebooks beállítása

A két Jupyter jegyzetfüzet és a támogató fájlok üzemeltetéséhez Azure Notebooks használjuk. Itt egy Azure Notebooks projektet hozunk létre és konfigurálunk. Ha nem használt Jupyter és/vagy Azure Notebooks, néhány bevezető dokumentum:

* Rövid útmutató **:** [Jegyzetfüzet létrehozása és megosztása](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Oktatóanyag:** [Jupyter-jegyzetfüzet létrehozása és futtatása Python-val](../notebooks/tutorial-create-run-jupyter-notebook.md)

Az Azure notebookok használata konzisztens környezetet biztosít a gyakorlathoz.

> [!NOTE]
> A beállítás után a Azure Notebooks szolgáltatás bármely gépről elérhető. A telepítés során a fejlesztési virtuális gépet kell használnia, amely rendelkezik az összes szükséges fájllal.

### <a name="create-an-azure-notebooks-account"></a>Azure Notebooks fiók létrehozása

A Azure Notebooks használatához létre kell hoznia egy fiókot. Az Azure notebook-fiókok függetlenek az Azure-előfizetéstől.

1. Navigáljon az [Azure-jegyzetfüzetekhez](https://notebooks.azure.com).

1. Kattintson a **Bejelentkezés** gombra a lap jobb felső sarkában.

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjával (Azure Active Directory) vagy a személyes fiókjával (Microsoft-fiókkal).

1. Ha korábban még nem használta Azure Notebooks, a rendszer felszólítja, hogy adjon hozzáférést a Azure Notebooks alkalmazáshoz.

1. Hozzon létre egy felhasználói azonosítót a Azure Notebookshoz.

### <a name="upload-jupyter-notebook-files"></a>Jupyter notebook-fájlok feltöltése

A mintaként szolgáló jegyzetfüzet-fájlokat egy új Azure Notebooks projektbe fogjuk feltölteni.

1. Az új fiókjának felhasználói lapján válassza a **saját projektek** lehetőséget a felső menüsorban.

1. Az **új projekt létrehozása** párbeszédpanelen adjon meg egy olyan **projekt nevét** , amely a **projekt azonosítóját**is automatikusan megalkotja.

1. Hagyja üresen a **nyilvános** és a **readme** jelölését, mert nincs szükség arra, hogy a projekt nyilvános legyen, vagy hogy legyen egy readme.

1. Kattintson a **Létrehozás** gombra.

1. Válassza a **feltöltés** lehetőséget (a felfelé mutató nyíl ikont), és válassza **a számítógép**lehetőséget.

1. Válassza a **fájlok kiválasztása**lehetőséget.

1. Navigáljon a **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Jelölje ki a listában az összes fájlt, majd kattintson a **Megnyitás**gombra.

1. Válassza a **feltöltés** lehetőséget a feltöltés megkezdéséhez, majd válassza a **kész** lehetőséget a folyamat befejezése után.

### <a name="azure-notebook-files"></a>Azure notebook-fájlok

Tekintsük át a Azure Notebooks-projektbe feltöltött fájlokat. Az oktatóanyag ezen részében szereplő tevékenységek két jegyzetfüzet-fájlra terjednek ki, amelyek néhány kiegészítő fájlt használnak.

* **01 – turbofan\_regresszió. ipynb:** Ez a jegyzetfüzet a Machine Learning szolgáltatás munkaterületen hozza létre és futtatja a Machine learning-kísérletet. A jegyzetfüzet széles körben a következő lépéseket hajtja végre:

  1. Az eszköz által létrehozott Azure Storage-fiókból származó adatok letöltése.
  1. Felderíti és előkészíti az adatképzést és az osztályozó ad-modellt.
  1. Értékelje ki a modellt a kísérletből egy tesztelési adatkészlet használatával (teszt\_FD003. txt).
  1. Közzéteszi a legjobb osztályozó modellt a Machine Learning szolgáltatás munkaterületen.

* **02 – turbofan\_\_Model üzembe helyezése. ipynb:** Ez a jegyzetfüzet az előző jegyzetfüzetben létrehozott modellt használja, és felhasználja egy Azure IoT Edge eszközre való üzembe helyezésre kész tároló-rendszerkép létrehozásához. A jegyzetfüzet a következő lépéseket hajtja végre:

  1. Létrehoz egy pontozási parancsfájlt a modellhez.
  1. Létrehoz egy tároló-rendszerképet a Machine Learning szolgáltatás munkaterületen mentett osztályozó modell használatával.
  1. Üzembe helyezi a rendszerképet az Azure Container instance webszolgáltatásában.
  1. A webszolgáltatás használatával ellenőrzi a modellt, és a rendszerkép a várt módon működik. Az érvényesített lemezkép a IoT Edge eszközre lesz telepítve az oktatóanyag [egyéni IoT Edge modulok létrehozása és telepítése](tutorial-machine-learning-edge-06-custom-modules.md) részében.

* **Teszt\_FD003. txt:** Ez a fájl tartalmazza azokat az adatkészleteket, amelyeket a rendszer a betanított osztályozó ellenőrzésekor használ. Úgy döntöttünk, hogy a tesztet az eredeti pályázathoz megadott módon használjuk, az egyszerűség kedvéért.

* **RUL\_FD003. txt:** Ez a fájl tartalmazza az egyes eszközök utolsó ciklusának RUL a test\_FD003. txt fájlban. Tekintse meg a readme. txt fájlt, valamint a "C:\\forrás\\IoTEdgeAndMlSample\\\\Turbofan" című témakört, amely az információk részletes ismertetését tartalmazza.

* **Utils.py:** Python segédprogram-függvényeket tartalmaz az adatokkal való munkavégzéshez. Az első jegyzetfüzet a függvények részletes magyarázatát tartalmazza.

* **Readme.MD:** A jegyzetfüzetek használatát leíró readme.  

## <a name="run-azure-notebooks"></a>Azure Notebooks futtatása

Most, hogy létrejött a projekt, futtathatja a jegyzetfüzeteket. 

1. A projekt lapon válassza a **01-turbofan\_regresszió. ipynb**lehetőséget.

    ![Válassza ki az első futtatandó jegyzetfüzetet](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Ha a rendszer kéri, válassza a Python 3,6 kernel elemet a párbeszédpanelen, és válassza a **kernel beállítása**lehetőséget.

1. Ha a jegyzetfüzet **nem megbízhatóként**jelenik meg, kattintson a jegyzetfüzet jobb felső sarkában található **nem megbízható** elemre. Amikor megjelenik a párbeszédpanel, válassza a **megbízhatóság**lehetőséget.

1. A jegyzetfüzetben görgessen le a **globális tulajdonságok megadása** című témakört követő cellára, amely a kóddal `AZURE_SUBSCRIPTION_ID =`, majd adja meg az Azure-előfizetése, beállításai és erőforrásai értékeit.

    ![Globális tulajdonságok beállítása a jegyzetfüzetben](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Futtassa ezt a cellát az eszköztáron a **Futtatás** lehetőség kiválasztásával.

    Amikor egy cella fut, a szögletes zárójelek közé ([\*]) mutató csillagot jeleníti meg. Ha a cella művelete befejeződött, a csillag helyére egy szám és egy kapcsolódó kimenet is megjelenhet. Egy jegyzetfüzet cellái egymás után, egyszerre csak egy futhatnak.

    Kövesse a jegyzetfüzet utasításait. A **cella** menü futtatási beállítások parancsát is használhatja, `Ctrl` + `Enter` egy cella futtatásához, és `Shift` + `Enter`, hogy egy cellát futtasson, és folytassa a következő cellával.

    > [!TIP]
    > A konzisztens cella-műveletek esetében ne futtassa ugyanazt a jegyzetfüzetet a böngészőben több lapon.

1. Görgessen le a cellára, amely azonnal követi a **Munkaterület létrehozása** áttekintés szövegét, és futtatja ezt a cellát. A cella kimenetében keresse meg a hivatkozást, amely arra utasítja Önt, hogy jelentkezzen be a hitelesítésbe. 

    ![Bejelentkezési kérés az eszköz hitelesítéséhez](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Nyissa meg a hivatkozást, és adja meg a megadott kódot. Ez a bejelentkezési eljárás hitelesíti a Jupyter-jegyzetfüzetet az Azure-erőforrások eléréséhez az Microsoft Azure platformfüggetlen parancssori felületen keresztül.  

    ![Alkalmazás hitelesítése az eszköz megerősítésekor](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Ezen a ponton a többi cella is futtatható. Optimális az összes cella futtatása, hogy a cellákban lévő kód szekvenciálisan fusson. Válassza az **összes futtatása** lehetőséget a **cella** menüből. Görgessen vissza a jegyzetfüzetbe, és tekintse át a cella műveleteinek befejezését.

    Az **Adatfelderítési** szakaszban áttekintheti a cellákat az **érzékelő-és RUL** alszakaszban, amelyek az scatterplots renderelését teszik lehetővé.

    ![Érzékelő-olvasási scatterplots](media/tutorial-machine-learning-edge-04-train-model/sensor-readings.png)

1. Mentse a jegyzetfüzetet, és térjen vissza a projekt lapra a jegyzetfüzet jobb felső sarkában található projekt nevére kattintva, vagy lépjen vissza a böngészőben.

1. Nyissa meg a **02-turbofan\_telepítse\_Model. ipynb** , majd ismételje meg az eljárás lépéseit a második jegyzetfüzet futtatásához.

1. Mentse a jegyzetfüzetet, és térjen vissza a projekt lapra a jegyzetfüzet jobb felső sarkában található projekt nevére kattintva, vagy lépjen vissza a böngészőben.

### <a name="verify-success"></a>Sikeres ellenőrzés

Annak ellenőrzéséhez, hogy a jegyzetfüzetek sikeresen befejeződtek-e, ellenőrizze, hogy néhány elem létrejött-e.

1. A Azure Notebooks projekt lapon válassza a **rejtett elemek megjelenítése** lehetőséget, hogy megjelenjenek a ponttal kezdődő elemek.

1. Ellenőrizze, hogy a következő fájlok lettek-e létrehozva:

    | Fájl | Leírás |
    | --- | --- |
    | ./aml_config/.azureml/config.JSON | A Azure Machine Learning-munkaterület létrehozásához használt konfigurációs fájl. |
    | ./aml_config/model_config. JSON | Konfigurációs fájl, amelyre telepíteni kell a modellt a **turbofanDemo** Machine learning munkaterületen az Azure-ban. |
    | myenv. YML| Információt nyújt az üzembe helyezett Machine Learning modell függőségeiről.|

1. Ellenőrizze a Azure Portal, hogy a **turboFanDemo** Machine learning munkaterület létezik-e az erőforráscsoporthoz.

### <a name="debugging"></a>Hibakeresés

A jegyzetfüzetbe beszúrt Python-utasítások hibakereséshez, főként a `print()` parancshoz. Ha olyan változókat vagy objektumokat lát, amelyek nincsenek definiálva, akkor futtassa azokat a cellákat, amelyeken először van deklarálva vagy példányban.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben két, Azure Notebooks futó Jupyter-jegyzetfüzetet használtunk a turbofan-eszközökből származó adatok használatára a hátralévő hasznos élettartam (RUL) osztályozó, az osztályozó modellként való mentéséhez, a tároló lemezképének létrehozásához, valamint a lemezkép webes se-ként való üzembe helyezéséhez és teszteléséhez. rvice.

IoT Edge-eszköz létrehozásához folytassa a következő cikkel.

> [!div class="nextstepaction"]
> [IoT Edge eszköz konfigurálása](tutorial-machine-learning-edge-05-configure-edge-device.md)
