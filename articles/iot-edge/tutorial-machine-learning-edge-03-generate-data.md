---
title: 'Oktatóanyag: az eszközbeállítások szimulálása – Azure IoT Edge & Machine Learning'
description: 'Oktatóanyag: hozzon létre olyan virtuális eszközöket, amelyek szimulált telemetria hoznak létre, amelyek később a Machine learning-modellek betanítására használhatók.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9b3b36cf5a0f426f2081179d08cf2f093a233fff
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665933"
---
# <a name="tutorial-generate-simulated-device-data"></a>Oktatóanyag: szimulált eszköz-adatértékek előállítása

> [!NOTE]
> Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. Ha ezt a cikket közvetlenül megérkezett, javasoljuk, hogy kezdje a sorozat [első cikkével](tutorial-machine-learning-edge-01-intro.md) a legjobb eredmények érdekében.

Ebben a cikkben a gépi tanulási betanítási adatok segítségével szimuláljuk a telemetria küldő eszközt a IoT Hub. Ahogy az a bevezetésben is szerepel, ez a teljes körű oktatóanyag a [Turbofan motor-degradációs szimulációs adatkészletet](https://c3.nasa.gov/dashlink/resources/139/) használja, hogy szimulálja a repülőgép-hajtóművekből származó adatok betanítását és tesztelését.

A kapcsolódó readme. txt fájlból tudjuk, hogy:

* Az adatgyűjtés több többváltozós idősorozatból áll.
* Az egyes adatkészletek betanítási és tesztelési részhalmazokra vannak osztva
* Minden idősorozat egy másik motortól származik
* Az egyes motorok a kezdeti kopás és a gyártási variáció különböző mértékével kezdődnek

Ebben az oktatóanyagban egy egyetlen adatkészlet (FD003) betanítási adatkészletét használjuk.

A valóságban minden motor egy független IoT-eszköz lenne. Ha nem áll rendelkezésre elérhető internetkapcsolattal rendelkező turbofan-motor, akkor ezekhez az eszközökhöz beépített szoftver áll rendelkezésére.

A szimulátor egy olyan C# program, amely a IoT hub API-k használatával programozott módon regisztrálja a virtuális eszközöket a IoT hub. Ezután elolvasjuk az egyes eszközök adatait a NASA által megadott adatkészletből, és egy szimulált IoT-eszköz használatával elküldhetik az IoT hub-ra. Az oktatóanyag ezen részének minden kódja megtalálható az adattár DeviceHarness könyvtárában.

A DeviceHarness projekt egy olyan .NET Core- C# projekt, amely négy osztályból áll:

* **Program:** A felhasználói bevitel és a teljes koordináció kezeléséért felelős végrehajtási pont.
* **TrainingFileManager:** felelős a kiválasztott adatfájl olvasásához és elemzéséhez.
* **CycleData:** az üzenet formátumára konvertált fájlok egyetlen sorát jelöli.
* **TurbofanDevice:** felelős egy olyan IoT-eszköz létrehozásához, amely egyetlen eszközhöz (idősorozat) felel meg az adatban, és a IoT-eszközön keresztül továbbítja azokat a IoT hub.

A cikkben leírt feladatok elvégzése körülbelül 20 percet vesz igénybe.

Ennek a lépésnek a működésével egyenértékűnek kell lennie az eszközök fejlesztői és a felhőalapú fejlesztők által.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>A Visual Studio Code konfigurálása és a DeviceHarness-projekt létrehozása

1. Nyisson meg egy távoli asztali munkamenetet a virtuális géphez, ahogy az az előző cikkben látható.

1. Nyissa meg a Visual Studio Code-ot.

1. A Visual Studio Code-ban válassza a **fájl** > **mappa megnyitása...** lehetőséget.

1. A **mappa** szövegmezőbe írja be `C:\source\IoTEdgeAndMlSample\DeviceHarness`, majd kattintson a **mappa kiválasztása** gombra.

   Ha a kimeneti ablakban OmniSharp hibák jelennek meg, el kell távolítania a C# bővítményt, be kell állítania és újra meg kell nyitnia a vs Code-ot, telepítenie kell a C# bővítményt, majd újra kell töltenie az ablakot.

1. Mivel első alkalommal használja a bővítményeket ezen a gépen, egyes bővítmények frissítik és telepítik a függőségeiket. A rendszer kérheti a bővítmény frissítését. Ha igen, válassza az **Újratöltés ablak**lehetőséget.

1. A rendszer kérni fogja a szükséges eszközök hozzáadását a DeviceHarness. Válassza az **Igen** lehetőséget a hozzáadásához.

   * Az értesítés néhány másodpercig is eltarthat.
   * Ha kihagyta ezt az értesítést, tekintse meg a "harang" ikont a jobb alsó sarokban.

   ![VS Code bővítmény előugró ablak](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. A csomag függőségeinek visszaállításához válassza a **visszaállítás** lehetőséget.

   ![VS Code Restore prompt](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Ellenőrizze, hogy a környezet megfelelően van-e beállítva egy Build, `Ctrl + Shift + B` vagy **Terminal** > **futtatási felépítési feladat**aktiválásával.

1. A rendszer felszólítja, hogy válassza ki a futtatni kívánt Build-feladatot. Válassza a **Létrehozás**lehetőséget.

1. A Build lefut, és sikert jelző üzenetet küld.

   ![Sikeres kimeneti üzenet létrehozása](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Ezt kiépítheti az alapértelmezett felépítési feladattal. Ehhez válassza a **Terminal** > az **alapértelmezett Build feladat konfigurálása...** lehetőséget, és válassza a **Létrehozás** elemet a parancssorból.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Kapcsolódás IoT Hub és DeviceHarness futtatása

Most, hogy létrehoztuk a projektet, kapcsolódjon az IoT hub-hoz a kapcsolati sztring eléréséhez, és figyelje az adatgenerálás folyamatát.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Bejelentkezés az Azure-ba a Visual Studio Code-ban

1. Jelentkezzen be az Azure-előfizetésbe a Visual Studio Code-ban a parancs paletta megnyitásával, `Ctrl + Shift + P` vagy **tekintse meg** > **Command paletta...** .

1. A parancssorban keresse meg és válassza az **Azure: bejelentkezés**lehetőséget.

1. Megnyílik egy böngészőablak, amely kéri a hitelesítő adatok megadását. Amikor átirányítja egy sikerességi lapra, lezárhatja a böngészőt.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Az IoT hub csatlakoztatása és a hub kapcsolati karakterláncának beolvasása

1. A Visual Studio Code Explorer alsó részén kattintson az **Azure IoT hub-eszközök** keretre a kibontásához.

1. A kibontott keretben kattintson a **IoT hub kiválasztása**elemre.

1. Ha a rendszer kéri, válassza ki az Azure-előfizetését, majd az IoT hubot.

1. Kattintson az **Azure IoT hub-eszközök** keretre, majd a további műveletekért kattintson a **...** lehetőségre. Válassza a **másolás IoT hub a kapcsolatok karakterlánca**lehetőséget.

   ![IoT Hub-kapcsolatok karakterláncának másolása](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>A DeviceHarness projekt futtatása

1. Válassza a > **terminál** **megtekintése** lehetőséget a Visual Studio Code-terminál megnyitásához.

   Ha nem jelenik meg a kérdés, kattintson az ENTER gombra.

1. Adja meg a `dotnet run` a terminálon.

1. Amikor a rendszer kéri a IoT Hub kapcsolódási karakterláncot, illessze be az előző szakaszban másolt kapcsolódási karakterláncot.

1. Az **Azure IoT hub eszközök** keretében kattintson a frissítés gombra.

   ![IoT Hub eszközök listájának frissítése](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Vegye figyelembe, hogy az eszközök bekerülnek a IoT Hubba, és az eszközök zöld színnel jelennek meg, jelezve, hogy az eszközön keresztül történik az adatküldés.

1. Megtekintheti az adott hubhoz küldött üzeneteket, ha a jobb gombbal rákattint bármelyik eszközre, és kiválasztja a **beépített esemény-végpont figyelése**lehetőséget. Az üzenetek a Visual Studio Code kimenet paneljén jelennek meg.

1. A figyelés leállításához kattintson az **Azure IoT hub Toolkit** kimenet paneljére, és válassza a **beépített esemény-végpont figyelésének leállítása**lehetőséget.

1. Hagyja, hogy az alkalmazás végrehajtsa a befejezést, ami néhány percet vesz igénybe.

## <a name="check-iot-hub-for-activity"></a>Tevékenység IoT Hubának keresése

A DeviceHarness által elküldett adatai az IoT hubhoz mentek. A Azure Portal használatával könnyen ellenőrizhető, hogy az adatközpont elérte-e az adott központot.

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon az IoT hubhoz.

1. Az Áttekintés oldalon látnia kell, hogy a rendszer elküldte az információkat az adott hubhoz:  

   ![Eszköz Felhőbeli üzeneteinek megtekintése IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Az Azure Storage-ban tárolt adatellenőrzés

Az IoT hub számára elküldett adatait a rendszer átirányítja az előző cikkben létrehozott tárolóba. Nézzük meg a Storage-fiókban lévő adatgyűjtést.

1. Az Azure Portalon lépjen a tárfiókra.

1. A Storage-fiók navigátorában válassza a **Storage Explorer (előzetes verzió)** lehetőséget.

1. A Storage Explorerben válassza a **blob-tárolók** , majd a **devicedata**lehetőséget.

1. A tartalom ablaktáblán kattintson az IoT hub nevére, majd az év, a hónap, a nap és az óra mappára. Ekkor több, a perceket jelképező mappa jelenik meg, amikor az adatgyűjtés megtörténik.

   ![Mappák megtekintése a blob Storage-ban](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kattintson az egyik mappára a partícióhoz tartozó **00** és **01** adatfájlok kereséséhez.

1. A fájlok formátuma [Avro](https://avro.apache.org/) formátumban történik, de az egyik fájlra duplán kattintva megnyílik egy másik böngésző lap, és részben megjelennek az adathalmazok. Ha ehelyett a program kéri, hogy nyissa meg a fájlt egy programban, válassza a VS Code lehetőséget, és helyesen fog megjelenni.

1. Az adatolvasásra és az adatelemzésre jelenleg nincs szükség. ezt a következő cikkben fogjuk elvégezni.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy .NET Core-projektet használtunk a virtuális eszközök készletének létrehozásához és az eszközökön keresztüli adatküldéshez a IoT Hub és egy Azure Storage-tárolóban. Ez a projekt szimulál egy olyan valós helyzetet, amelyben a fizikai eszközök az érzékelőket, az üzemeltetési beállításokat, a meghibásodási jeleket és a módokat, és így tovább, egy IoT Hub és egy, a kurátori tárolóba kerülnek. Ha elegendő adatokat gyűjtöttek be, azt használjuk olyan modellek betanításához, amelyek megjósolják az eszköz hátralévő hasznos élettartamát (RUL), amelyet a következő cikkben fogunk bemutatni.

Folytassa a következő cikkel, amellyel betaníthat egy gépi tanulási modellt az adatelemzéssel.

> [!div class="nextstepaction"]
> [Azure Machine Learning modell betanítása és üzembe helyezése](tutorial-machine-learning-edge-04-train-model.md)
