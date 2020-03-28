---
title: 'Oktatóanyag: Modell betanítása és üzembe helyezése – Machine Learning az Azure IoT Edge-en'
description: Ebben az oktatóanyagban egy gépi tanulási modellt fog betanítani az Azure Machine Learning használatával, majd a modellt egy tárolórendszerképként csomagolja be, amely azure IoT Edge-modulként telepíthető.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57630b789233dd23e61398f445b434e4ba08b48e
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80236011"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Oktatóanyag: Betanítása és üzembe helyezése egy Azure Machine Learning-modell

> [!NOTE]
> Ez a cikk az Azure Machine Learning IoT Edge-en való használatával kapcsolatos oktatóanyag sorozatának része. Ha megérkezett ezt a cikket közvetlenül, javasoljuk, hogy kezdődik az [első cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredményt.

Ebben a cikkben a következő feladatokat tesszük:

* Az Azure Notebookok használatával gépi tanulási modell betanítása.
* Csomagolja a betanított modellt tárolórendszerképként.
* Telepítse a tárolórendszerképet egy Azure IoT Edge-modulként.

Az Azure-jegyzetfüzetek kihasználják az Azure Machine Learning-munkaterületet, egy alapvető blokkot, amelyet gépi tanulási modellek kísérletezésére, betanítására és üzembe helyezésére használnak.

A cikkben ismertetett lépéseket általában adatszakértők hajtják végre.

## <a name="set-up-azure-notebooks"></a>Az Azure-jegyzetfüzetek beállítása

Az Azure Notebookok a két Jupyter-jegyzetfüzet és a támogató fájlok üzemeltetéséhez használjuk. Itt hozunk létre és konfigurálunk egy Azure Notebooks projektet. Ha nem használta a Jupyter t és/vagy az Azure Notebooks-t, az alábbiakban néhány bevezető dokumentumot olvashat:

* **Rövid útmutató:** [Jegyzetfüzet létrehozása és megosztása](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Oktatóanyag:** [Jupyter-jegyzetfüzet létrehozása és futtatása Pythonnal](../notebooks/tutorial-create-run-jupyter-notebook.md)

Az Azure-jegyzetfüzetek használata biztosítja a konzisztens környezetet a gyakorlat.

> [!NOTE]
> Beállítása után az Azure Notebooks szolgáltatás bármely gépről elérhető. A telepítés során érdemes használni a fejlesztői virtuális gép, amely rendelkezik az összes szükséges fájlokat.

### <a name="create-an-azure-notebooks-account"></a>Azure Notebook-fiók létrehozása

Az Azure-jegyzetfüzetek használatához létre kell hoznia egy fiókot. Az Azure Notebook-fiókok függetlenek az Azure-előfizetésektől.

1. Nyissa meg az [Azure-jegyzetfüzeteket.](https://notebooks.azure.com)

1. Kattintson **a lap** jobb felső sarkában a Bejelentkezés gombra.

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával (Azure Active Directory) vagy személyes fiókjával (Microsoft-fiók).

1. Ha korábban még nem használta az Azure Notebooks alkalmazást, a rendszer kéri, hogy adjon hozzáférést az Azure Notebooks alkalmazáshoz.

1. Hozzon létre egy felhasználói azonosítót az Azure Notebooks számára.

### <a name="upload-jupyter-notebook-files"></a>Jupyter notebook fájlok feltöltése

A mintajegyzetfüzet-fájlokat egy új Azure Notebook-projektbe töltjük fel.

1. Az új fiók felhasználói lapján válassza a felső menüsor **Saját projektek** lehetőséget.

1. Új projekt hozzáadása a **+** gomb kiválasztásával.

1. Az **Új projekt létrehozása** párbeszédpanelen adja meg a **Projekt nevét.** 

1. Hagyja **a Nyilvános** és a **README-t** bejelölve, mivel nincs szükség arra, hogy a projekt nyilvános legyen, vagy hogy olvasmánya legyen.

1. Kattintson a **Létrehozás** gombra.

1. Válassza a **Feltöltés** (a felfelé mutató nyíl) lehetőséget, majd a **Számítógépből**lehetőséget.

1. Válassza **a Fájlok kiválasztása**lehetőséget.

1. Keresse meg a **C:\source\IoTEdgeAndMlSample\AzureNotebooks mappát.** Jelölje ki a listában az összes fájlt, és kattintson a **Megnyitás gombra.**

1. Jelölje be a **Megbízható a fájlok tartalmát** mezőbe.

1. A feltöltés megkezdéséhez válassza a **Feltöltés** lehetőséget, majd a Folyamat befejezése után válassza a **Kész** lehetőséget.

### <a name="azure-notebook-files"></a>Azure notebook-fájlok

Tekintse át az Azure Notebooks projektbe feltöltött fájlokat. Az oktatóanyag ezen részében található tevékenységek két jegyzetfüzetfájlra terjednek ki, amelyek néhány támogató fájlt használnak.

* **01-turbofan\_regression.ipynb:** Ez a jegyzetfüzet a Machine Learning szolgáltatás munkaterületét használja egy gépi tanulási kísérlet létrehozásához és futtatásához. A jegyzetfüzet nagyjából a következő lépéseket teszi:

  1. Adatokletöltésaz Azure Storage-fiókból, amelyet az eszköz kábelkötege hozott létre.
  1. Feltárja és előkészíti az adatokat, majd az adatok at használja az osztályozó modell betanításához.
  1. Értékelje ki a modellt a kísérletből\_egy tesztadatkészlet (Teszt FD003.txt).
  1. A legjobb osztályozó modell közzététele a Machine Learning szolgáltatás munkaterületén.

* **02-turbofan\_\_telepíteni model.ipynb:** Ez a jegyzetfüzet az előző jegyzetfüzetben létrehozott modellt használja egy Azure IoT Edge-eszközre üzembe helyezhető tárolórendszerkép létrehozásához. A jegyzetfüzet a következő lépéseket hajtja végre:

  1. Létrehoz egy pontozási parancsfájlt a modellhez.
  1. A Machine Learning szolgáltatás munkaterületén mentett osztályozó modell használatával egy tárolólemezképet hoz létre.
  1. A rendszerképet webszolgáltatásként telepíti az Azure Container Instance szolgáltatásban.
  1. A webszolgáltatás segítségével ellenőrzi a modell és a rendszerkép a várt módon működik. Az érvényesített rendszerkép lesz telepítve az IoT Edge-eszköz az [útmutató egyéni IoT Edge-modulok létrehozása és üzembe helyezése.](tutorial-machine-learning-edge-06-custom-modules.md)

* **Az\_FD003.txt teszt:** Ez a fájl tartalmazza azokat az adatokat fogjuk használni, mint a mi teszt készlet érvényesítése során a képzett osztályozó. Úgy döntöttünk, hogy a tesztadatokat az eredeti versenyhez hasonlóan használjuk, mint az egyszerűség kedvéért.

* **RUL\_FD003.txt:** Ez a fájl tartalmazza a Teszt\_FD003.txt fájlban lévő egyes eszközök utolsó ciklusának hátralévő hasznos élettartamát (RUL). Az adatok részletes magyarázatát lásd\\a C: forrás\\IoTEdgeAndMlSample\\adatok\\Turbofan fájljában található readme.txt és a Kárszaporítási modellezés.pdf fájlokban.

* **Utils.py:** Python segédprogram-függvényeket tartalmaz az adatokkal való munkához. Az első jegyzetfüzet a függvények részletes magyarázatát tartalmazza.

* **README.md:** A jegyzetfüzetek használatát leíró olvasmény.  

## <a name="run-azure-notebooks"></a>Azure-jegyzetfüzetek futtatása

Most, hogy a projekt létrejött, futtathatja a jegyzetfüzeteket. 

1. A projekt oldalán válassza a **01-turbofan\_regression.ipynb lehetőséget.**

    ![Első ként futtatandó jegyzetfüzet kiválasztása](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Ha a jegyzetfüzet **nem megbízhatóként**szerepel, kattintson a jegyzetfüzet jobb felső részén található **Nem megbízható** widgetre. Amikor megjelenik a párbeszédpanel, válassza a **Megbízhatóság**lehetőséget.

1. A legjobb eredmény érdekében olvassa el az egyes sejtek dokumentációját, és futtassa egyenként. Válassza a **Futtatás** lehetőséget az eszköztáron. Később, meg fogja találni, hogy célszerű futtatni több sejt. Figyelmen kívül hagyhatja a frissítési és eprítási figyelmeztetéseket.

    Amikor egy cella fut, csillagjelenik meg a szögletes zárójelek között ([\*]). Amikor a cella működése befejeződött, a csillagot egy szám váltja fel, és megfelelő kimenet jelenhet meg. A jegyzetfüzet cellái egymás után épülnek fel, és egyszerre csak egy futtatható.

    A **Cell** menü `Ctrl`  +  `Enter` futási beállításaival futtathat egy `Shift`  +  `Enter` cellát, futtathat egy cellát, és továbbléphet a következő cellára.

    > [!TIP]
    > Az egységes cellaműveletek érdekében ne futassza ugyanazt a jegyzetfüzetet a böngésző több lapjáról.

1. A **globális tulajdonságok beállítása** utasításokat követő cellában írja be az Azure-előfizetés, a beállítások és az erőforrások értékeit. Akkor futtassa a cellát.

    ![Globális tulajdonságok beállítása a jegyzetfüzetben](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. A **Munkaterület részleteit**megelőző cellában a futtatás után keresse meg azt a hivatkozást, amely a hitelesítéshez bejelentkezésre utasítja:

    ![Bejelentkezési kérdés az eszköz hitelesítésére](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Nyissa meg a hivatkozást, és írja be a megadott kódot. Ez a bejelentkezési eljárás hitelesíti a Jupyter-jegyzetfüzetet az Azure-erőforrások eléréséhez a Microsoft Azure platformfüggetlen parancssori felülethasználatával.  

    ![Alkalmazás hitelesítése az eszköz megerősítésén](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Az azt megelőző cellában **Az eredmények feltárása**, másolja az értéket a futtatási azonosítóból, és illessze be a futtatási azonosítóhoz a futtatást követő cellába. **Reconstitute a run**

   ![A futtatási azonosító másolása a cellák között](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Futtassa a jegyzetfüzet többi celláját.

1. Mentse a jegyzetfüzetet, és térjen vissza a projektlapra.

1. Nyílt **\_02-turbofan\_telepíteni model.ipynb** és fuss minden cellában. A munkaterület konfigurálása című cellában való hitelesítéshez be kell **jelentkeznie.**

1. Mentse a jegyzetfüzetet, és térjen vissza a projektlapra.

### <a name="verify-success"></a>Sikeresség ellenőrzése

Annak ellenőrzéséhez, hogy a jegyzetfüzetek sikeresen befejeződtek-e, ellenőrizze, hogy néhány elem létrejött-e.

1. Az Azure Notebooks projektlapján válassza **a Rejtett elemek megjelenítése** lehetőséget, hogy egy időszakkal kezdődő elemnevek jelenjenek meg.

1. Ellenőrizze, hogy a következő fájlok jöttek-e létre:

    | Fájl | Leírás |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Az Azure Machine Learning-munkaterület létrehozásához használt konfigurációs fájl. |
    | ./aml_config/model_config.json | Konfigurációs fájl, amely et kell telepíteni a modellt a **turbofanDemo** Machine Learning munkaterületen az Azure-ban. |
    | myenv.yml| Az üzembe helyezett Machine Learning-modell függőségeiről nyújt tájékoztatást.|

1. Ellenőrizze, hogy a következő Azure-erőforrások jöttek-e létre. Egyes erőforrások nevei véletlenszerű karaktereket tartalmaznak.

    | Azure-erőforrás | Név |
    | --- | --- |
    | Gépi tanulási munkaterület | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Alkalmazások elemzési adatai | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Hibakeresés

Python-utasítások beszúrása a jegyzetfüzetbe hibakereséshez, `print()` például az értékek megjelenítésére szolgáló parancs. Ha nem definiált változókat vagy objektumokat lát, futtassa azokat a cellákat, amelyekben először deklarálják vagy példányosítják őket.

Előfordulhat, hogy törölnie kell a korábban létrehozott fájlokat és az Azure-erőforrásokat, ha újra kell csinálnia a jegyzetfüzeteket.

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Azure Notebooks-ban futó két Jupyter-jegyzetfüzetet használtuk a turbóventilátoros eszközök ből származó adatok használatához a fennmaradó hasznos élettartam (RUL) osztályozó betanításához, az osztályozó modellként való mentéséhez, egy tárolórendszerkép létrehozásához, valamint a lemezkép webként való üzembe helyezéséhez és teszteléséhez Szolgáltatás.

Folytassa a következő cikket egy IoT Edge-eszköz létrehozásához.

> [!div class="nextstepaction"]
> [IoT Edge-eszköz konfigurálása](tutorial-machine-learning-edge-05-configure-edge-device.md)
