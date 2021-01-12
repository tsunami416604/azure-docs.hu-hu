---
title: 'Oktatóanyag: modell betanítása és üzembe helyezése Machine Learning Azure IoT Edge'
description: Ebben az oktatóanyagban egy gépi tanulási modellt fog betanítani Azure Machine Learning használatával, majd becsomagolja a modellt Azure IoT Edge modulként üzembe helyezhető tároló-képként.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2cc96db88d9a2aec02de5e2fc4ed18b445972e7b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121138"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Oktatóanyag: Azure Machine Learning modell betanítása és üzembe helyezése

Ebben a cikkben a következő feladatokat hajtjuk végre:

* A Machine learning-modellek betanításához használjon Azure Machine Learning Studio.
* Csomagolja ki a betanított modellt tároló képként.
* Telepítse a tároló lemezképét Azure IoT Edge modulként.

A Azure Machine Learning Studio a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használt alapvető blokk.

A cikkben ismertetett lépéseket általában az adatszakértők végzik.

Az oktatóanyag ezen szakaszában a következőket sajátíthatja el:

> [!div class="checklist"]
> * Hozzon létre Jupyter-jegyzetfüzeteket Azure Machine Learning-munkaterület a Machine learning-modellek betanításához.
> * Tárolóba helyezése a betanított gépi tanulási modellt.
> * Hozzon létre egy Azure IoT Edge modult a tároló Machine learning modellből.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. A sorozat minden cikke az előző cikkben található munkára épül. Ha ezt a cikket közvetlenül megérkezett, tekintse meg az [első cikket](tutorial-machine-learning-edge-01-intro.md) a sorozatban.

## <a name="set-up-azure-machine-learning"></a>Azure Machine Learning beállítása 

A két Jupyter jegyzetfüzet és a támogató fájlok üzemeltetéséhez Azure Machine Learning Studio használjuk. Itt egy Azure Machine Learning projektet hozunk létre és konfigurálunk. Ha nem használt Jupyter és/vagy Azure Machine Learning Studio, néhány bevezető dokumentum:

* **Jupyter notebookok:** [Jupyter notebookok használata a Visual Studio Code-ban](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine learning:** [a Jupyter-jegyzetfüzetek Azure Machine Learningának első lépései](../machine-learning/tutorial-1st-experiment-sdk-setup.md)


> [!NOTE]
> A beállítás után a Azure Machine Learning szolgáltatás bármely gépről elérhető. A telepítés során a fejlesztési virtuális gépet kell használnia, amely rendelkezik az összes szükséges fájllal.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>A Visual Studio Code-bővítmény telepítése Azure Machine Learning
A fejlesztési virtuális gépen a VS Code-nak telepítve kell lennie a bővítménynek. Ha egy másik példányon fut, telepítse újra a bővítményt az itt leírtak szerint [.](../machine-learning/tutorial-setup-vscode-extension.md)

### <a name="create-an-azure-machine-learning-account"></a>Azure Machine Learning fiók létrehozása  
Az erőforrások kiépítéséhez és az Azure-beli számítási feladatok futtatásához be kell jelentkeznie az Azure-fiók hitelesítő adataival.

1. A Visual Studio Code-ban nyissa meg a parancssort a menüsávban található parancssáv **megtekintése** lehetőség kiválasztásával  >   . 

1. A `Azure: Sign In` bejelentkezési folyamat elindításához írja be a parancsot a parancssorba. A bejelentkezés befejezéséhez kövesse az utasításokat. 

1. Hozzon létre egy Azure ML Compute-példányt a számítási feladatok futtatásához. A Command raklap használatával adja meg a parancsot `Azure ML: Create Compute` . 
1. Válassza ki az Azure-előfizetését
1. Válassza az **+ új Azure ml-munkaterület létrehozása** lehetőséget, és írja be a nevet `turbofandemo` .
1. Válassza ki azt az erőforráscsoportot, amelyet ehhez a bemutatóhoz használt.
1. Meg kell tudnia tekinteni a munkaterület létrehozásának előrehaladását a VS Code ablak jobb alsó sarkában: **Munkaterület létrehozása: turobofandemo** (ez eltarthat egy-két percet is). 
1. Várjon, amíg a munkaterület létrehozása sikeresen megtörtént. Azt mondják, hogy az **Azure ml-munkaterület turbofandemo létre lett hozva**.


### <a name="upload-jupyter-notebook-files"></a>Jupyter Notebook fájlok feltöltése

A mintaként szolgáló jegyzetfüzet-fájlokat egy új Azure ML-munkaterületre fogjuk feltölteni.

1. Navigáljon a ml.azure.com elemre, és jelentkezzen be.
1. Válassza ki a Microsoft címtárát, az Azure-előfizetést és az újonnan létrehozott Azure ML-munkaterületet.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Válassza ki az Azure ML-munkaterületét." :::

1. Miután bejelentkezett az Azure ML-munkaterületre, navigáljon a **jegyzetfüzetek** szakaszra a bal oldali menüben.
1. Válassza a **saját fájlok** fület.

1. **Feltöltés** kiválasztása (a felfelé mutató nyíl ikon) 


1. Navigáljon a **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Jelölje ki a listában az összes fájlt, majd kattintson a **Megnyitás** gombra.

1. Jelölje be a **Megbízom a fájlok tartalmában** jelölőnégyzetet.

1. Válassza a **feltöltés** lehetőséget a feltöltés megkezdéséhez, majd válassza a **kész** lehetőséget a folyamat befejezése után.

### <a name="jupyter-notebook-files"></a>Jupyter Notebook fájlok

Tekintsük át az Azure ML-munkaterületre feltöltött fájlokat. Az oktatóanyag ezen részében szereplő tevékenységek két jegyzetfüzet-fájlra terjednek ki, amelyek néhány kiegészítő fájlt használnak.

* **01 – turbofan \_ regresszió. ipynb:** ez a jegyzetfüzet a Machine learning szolgáltatás munkaterületet használja a Machine learning-kísérletek létrehozásához és futtatásához. A jegyzetfüzet széles körben a következő lépéseket hajtja végre:

  1. Az eszköz által létrehozott Azure Storage-fiókból származó adatok letöltése.
  1. Felderíti és előkészíti az adatgyűjtést, majd az adatosztályozó modell betanítására használja fel azokat.
  1. Értékelje ki a modellt a kísérletből egy tesztelési adatkészlet használatával (teszt \_FD003.txt).
  1. Közzéteszi a legjobb osztályozó modellt a Machine Learning szolgáltatás munkaterületen.

* **02 – turbofan \_ üzembe helyezési \_ modell. ipynb:** ez a jegyzetfüzet az előző jegyzetfüzetben létrehozott modellt hozza létre, és felhasználja egy Azure IoT Edge eszközre való üzembe helyezésre kész tároló lemezkép létrehozásához. A jegyzetfüzet a következő lépéseket hajtja végre:

  1. Létrehoz egy pontozási parancsfájlt a modellhez.
  1. Létrehoz egy tároló-rendszerképet a Machine Learning szolgáltatás munkaterületen mentett osztályozó modell használatával.
  1. Üzembe helyezi a rendszerképet az Azure Container instance webszolgáltatásában.
  1. A webszolgáltatás használatával ellenőrzi a modellt, és a rendszerkép a várt módon működik. Az érvényesített lemezkép a IoT Edge eszközre lesz telepítve az oktatóanyag [egyéni IoT Edge modulok létrehozása és telepítése](tutorial-machine-learning-edge-06-custom-modules.md) részében.

* **Test \_FD003.txt:** ez a fájl tartalmazza azokat az adatkészleteket, amelyeket a rendszer a betanított osztályozó ellenőrzésekor használ. Úgy döntöttünk, hogy a tesztet az eredeti pályázathoz megadott módon használjuk, az egyszerűség kedvéért.

* **RUL \_FD003.txt:** ez a fájl a tesztelésiFD003.txt fájlban lévő egyes eszközök utolsó ciklusának hátralévő hasznos élettartamát (RUL) tartalmazza \_ . Az \\ \\ \\ információk részletes ismertetéséhez tekintse meg a readme.txt és a kár propagálására Modeling.pdf fájlokat a C: Source IoTEdgeAndMlSample- \\ Turbofan.

* **Utils.py:** Python segédprogram-függvényeket tartalmaz az adatokkal való munkavégzéshez. Az első jegyzetfüzet a függvények részletes magyarázatát tartalmazza.

* **Readme.MD:** A jegyzetfüzetek használatát leíró readme.  

## <a name="run-jupyter-notebooks"></a>Jupyter-notebookok futtatása

Most, hogy létrehozta a munkaterületet, futtathatja a jegyzetfüzeteket. 

1. A **saját fájlok** lapon válassza a **01-turbofan \_ regresszió. ipynb** elemet.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Válassza ki az első futtatandó jegyzetfüzetet. ":::

1. Ha a jegyzetfüzet **nem megbízhatóként** jelenik meg, kattintson a jegyzetfüzet jobb felső sarkában található **nem megbízható** elemre. Amikor megjelenik a párbeszédpanel, válassza a **megbízhatóság** lehetőséget.

1. A legjobb eredmény érdekében olvassa el az egyes cellák dokumentációját, és futtassa azt egyenként. Kattintson a **Futtatás** elemre az eszköztáron. Később, célszerű több cella futtatására is. A frissítés és az elavultság figyelmeztetéseit figyelmen kívül hagyhatja.

    Amikor egy cella fut, a szögletes zárójelek ([]) közötti csillagot jeleníti meg \* . Ha a cella művelete befejeződött, a csillag helyére egy szám és egy kapcsolódó kimenet is megjelenhet. Egy jegyzetfüzet cellái egymás után, egyszerre csak egy futhatnak.

    A **cella** menü futtatási Beállítások parancsával is `Ctrl`  +  `Enter` futtathat egy cellát, és `Shift`  +  `Enter` egy cellát is futtathat a következő cellába.

    > [!TIP]
    > A konzisztens cella-műveletek esetében ne futtassa ugyanazt a jegyzetfüzetet a böngészőben több lapon.

1. A **globális tulajdonságok megadása** útmutatást követő cellában írja be az Azure-előfizetése, beállításai és erőforrásai értékeit. Ezután futtassa a cellát.

    ![Globális tulajdonságok beállítása a jegyzetfüzetben](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Az előző cellában, a **munkaterület részletei** között, a Futtatás után keresse meg a hivatkozást, amely arra utasítja Önt, hogy jelentkezzen be a hitelesítésbe:

    ![Bejelentkezési kérés az eszköz hitelesítéséhez](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Nyissa meg a hivatkozást, és adja meg a megadott kódot. Ez a bejelentkezési eljárás hitelesíti a Jupyter-jegyzetfüzetet az Azure-erőforrások eléréséhez az Microsoft Azure platformfüggetlen parancssori felületen keresztül.  

    ![Alkalmazás hitelesítése az eszköz megerősítésekor](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Az **eredményeket** tartalmazó cellában másolja ki az értéket a FUTTATÁSi azonosítóból, és illessze be a futtatási azonosítóhoz **az alábbi cellában található futtatási** azonosítóra.

   ![A futtatási azonosító másolása cellák között](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Futtassa a többi cellát a jegyzetfüzetben.

1. Mentse a jegyzetfüzetet, és térjen vissza a projekt lapra.

1. Nyissa meg a **02 – turbofan \_ üzembe helyezés \_ Model. ipynb** , és futtassa az egyes cellákat. A **konfiguráláshoz a munkaterületet** követő cellában kell bejelentkeznie.

1. Mentse a jegyzetfüzetet, és térjen vissza a projekt lapra.

### <a name="verify-success"></a>Sikeres ellenőrzés

Annak ellenőrzéséhez, hogy a jegyzetfüzetek sikeresen befejeződtek-e, ellenőrizze, hogy néhány elem létrejött-e.

1. Az Azure ML jegyzetfüzetek **saját fájlok** lapján válassza a **frissítés** lehetőséget.

1. Ellenőrizze, hogy a következő fájlok lettek-e létrehozva:

    | Fájl | Leírás |
    | --- | --- |
    | ./aml_config/.azureml/config.jsbekapcsolva | A Azure Machine Learning-munkaterület létrehozásához használt konfigurációs fájl. |
    | ./aml_config/model_config.jsbekapcsolva | Konfigurációs fájl, amelyre telepíteni kell a modellt a **turbofanDemo** Machine learning munkaterületen az Azure-ban. |
    | myenv. YML| Információt nyújt az üzembe helyezett Machine Learning modell függőségeiről.|

1. Ellenőrizze, hogy létrejöttek-e az alábbi Azure-erőforrások. Néhány erőforrás neve véletlenszerű karakterekkel van hozzáfűzve.

    | Azure-erőforrás | Name |
    | --- | --- |
    | Machine Learning munkaterület | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Applications-ismeretek | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | Tárolás | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Hibakeresés

A jegyzetfüzetbe beillesztheti a hibakereséshez használható Python-utasításokat, például a `print()` parancsot az értékek megjelenítéséhez. Ha olyan változókat vagy objektumokat lát, amelyek nincsenek definiálva, akkor futtassa azokat a cellákat, amelyeken először van deklarálva vagy példányban.

Előfordulhat, hogy törölnie kell a korábban létrehozott fájlokat és az Azure-erőforrásokat, ha újra meg kell ismételni a jegyzetfüzeteket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag egy olyan készlet része, amelyben az egyes cikkek az előzőekben végzett munkára épülnek. Várjon, amíg az összes erőforrást el nem végezte, amíg el nem végzi a végleges oktatóanyagot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben két, az Azure ML Studio-ban futó Jupyter-jegyzetfüzetet használtunk a turbofan-eszközökből származó adatok használatára a hátralévő hasznos élettartamot (RUL) osztályozó, az osztályozó modellként való mentéséhez, a tároló lemezképének létrehozásához, valamint a lemezkép webszolgáltatásként való üzembe helyezéséhez és teszteléséhez.

IoT Edge-eszköz létrehozásához folytassa a következő cikkel.

> [!div class="nextstepaction"]
> [IoT Edge-eszköz konfigurálása](tutorial-machine-learning-edge-05-configure-edge-device.md)