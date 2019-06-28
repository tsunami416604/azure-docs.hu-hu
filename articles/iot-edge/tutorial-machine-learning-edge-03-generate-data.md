---
title: Hozhat létre szimulált eszköz adatokat – Machine Learning az Azure IoT Edge |} A Microsoft Docs
description: Hozzon létre a virtuális eszközök, amelyek létrehozzák a szimulált telemetria egy gépi tanulási modell betanításához később használható.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a93b1128fe1ea0e03efc9060f2c3c4a93145f838
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432860"
---
# <a name="tutorial-generate-simulated-device-data"></a>Oktatóanyag: Szimulált eszközadatok létrehozása

> [!NOTE]
> Ez a cikk egy sorozat része az IoT Edge-ben az Azure Machine Learning használatával kapcsolatos oktatóanyagot. Ha érkezett, ez a cikk közvetlenül, javasoljuk, hogy először a [először cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredmények.

Ez a cikk gépi tanulási képzés a telemetriai adatok küldését az IoT Hub eszköz szimulálása az adatokat használjuk. Amint azt a bevezetés, teljes körű az oktatóanyagban a [Turbofan engine degradation szimuláció adatkészlet](https://c3.nasa.gov/dashlink/resources/139/) szimulálása az adatok közül, repülőgép-hajtóművek betanítására és tesztelésére.

A kísérő Readme.txt fájlt a tudjuk, hogy:

* Az adatok több többváltozós idősorozat áll.
* Minden adathalmaz tanítási és tesztelési részhalmazainak van felosztva.
* Minden egyes van egy másik motor
* Mindegyik motor kezdeti motorkopási és a gyártási variation különböző fokú kezdődik.

Ebben az oktatóanyagban használ a betanítási adatok részhalmazát egyetlen adatkészlet (FD003).

A valóságban ez mindegyik motor lenne egy független IoT-eszköz. Feltéve, hogy nem kell az internethez csatlakozó turbofan motorok rendelkezésre álló egy gyűjteményt, amelyet ki fogunk építeni egy szoftverfrissítési stand-in ezekhez az eszközökhöz.

A szimulátor van egy C# program, amely az IoT Hub API-k használatával programozott módon IoT Hub-regisztráció virtuális eszközök. Azt az egyes eszközök az adatok beolvasása a NASA által biztosított adatok részhalmazát és küldje el az IoT hubra szimulált IoT-eszköz használatával. Az oktatóanyag ezen részében a kódot a tárház DeviceHarness könyvtárában található.

A DeviceHarness projekt egy .NET core-projektet a írt C# álló négy osztályok:

* **A program:** A belépési pont végrehajtási felhasználói bevitel és általános koordináció kezeléséért felelős.
* **TrainingFileManager:** olvasása és elemzése a kijelölt adatok fájl felelős.
* **CycleData:** jelöli az adatok egy fájlban egyetlen sor üzenetet formátumra alakítja át.
* **TurbofanDevice:** létrehozásáért IoT-eszközzel, amely megfelel egy adott eszköz (idősorozat) az adatok és az IoT hub az IoT-Device-n keresztül az adatraktárba.

Ebben a cikkben leírt körülbelül 20 percet vesz igénybe, végrehajtásához.

A való életből vett megfelelője ebben a lépésben a munka volna valószínűleg hajtható végre eszköz a fejlesztők és a fejlesztők számára.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurálja a Visual Studio Code-ot, és hozza létre DeviceHarness projektet

1. Nyissa meg a virtuális gépet egy távoli asztali munkamenetet, ahogyan az az előző cikkben is.

1. Nyissa meg a Visual Studio Code-ot.

1. Válassza ki a Visual Studio Code- **fájl** > **mappa megnyitása...** .

1. A a **mappa** szövegmezőbe írja be `C:\source\IoTEdgeAndMlSample\DeviceHarness` , és kattintson a **mappa kiválasztása** gombra.

   Ha OmniSharp hibák jelennek meg a kimeneti ablakban, távolíthatja el kell a C# bővítményt, zárja be és nyissa meg újra a VS Code, telepítse a C# bővítményt, és ezután töltse be újra az ablakban.

1. Bővítmények ezen a gépen az első alkalommal használja, mivel néhány bővítmény frissíti, a függőségek telepítéséhez. A rendszer felkérheti bővítmény frissítése. Ha igen, válassza ki a **Reload Window**.

1. Adja hozzá a szükséges objektumokat a DeviceHarness kéri. Válassza ki **Igen** adja hozzá őket.

   * Az értesítés jelenik meg néhány másodpercig is eltarthat.
   * Ha lemaradt ezt az értesítést, ellenőrizze a "harang" ikonra a jobb alsó sarkában.

   ![A VS Code bővítmény előugró ablak](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Válassza ki **visszaállítása** visszaállítani a csomagfüggőségek.

   ![A VS Code visszaállítási kérés](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Ellenőrizze, hogy a környezet megfelelően állíthatja be egy build aktiválása `Ctrl + Shift + B` vagy **terminálon** > **létrehozása feladat futtatása**.

1. Válassza ki az összeállítási feladat futtatását kéri. Válassza ki **összeállítása**.

1. A build fut, és kiírja a sikert jelző üzenet.

   ![Sikeres kimenet üzenet létrehozása](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Ezt az alapértelmezett összeállítási feladat létrehozása kiválasztásával **terminálon** > **alapértelmezett konfigurálása a feladat létrehozása...**  választva **összeállítása** a parancssorból.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Csatlakoztatása az IoT hubhoz, és futtassa a DeviceHarness

Most, hogy a projekt létrehozásához, csatlakozni az IoT hub eléréséhez a kapcsolati karakterláncot, és az adat-előállítás előrehaladásának figyeléséhez.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Jelentkezzen be az Azure-bA a Visual Studio Code-ban

1. Jelentkezzen be az Azure-előfizetését a Visual Studio Code-ban nyissa meg a parancskatalógust `Ctrl + Shift + P` vagy **nézet** > **Parancskatalógus...** .

1. A Rákérdezés keresést, és válassza a **Azure: Jelentkezzen be a**.

1. Egy böngészőablakban megnyitja, és kéri a hitelesítő adatait. Amikor a rendszer átirányítja egy sikeres megerősítés oldal, bezárhatja a böngészőt.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Az IoT hub csatlakozik, és az eseményközpont kapcsolati karakterlánc

1. A Visual Studio Code Explorerben alsó részén válassza a **Azure IoT Hub-eszközök** keret annak kibontásához.

1. A kibontott keretet, kattintson a **válassza ki az IoT Hub**.

1. Amikor a rendszer kéri, válassza ki az Azure-előfizetés és az IoT hubbal.

1. Kattintson az a **Azure IoT Hub-eszközök** kerete, és kattintson a **...**  további műveletekről. Válassza ki **másolási IoT Hub kapcsolati karakterláncra**.

   ![Az IoT Hub kapcsolati karakterlánc másolása](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>A DeviceHarness projekt futtatása

1. Válassza ki **nézet** > **terminál** , nyissa meg a Visual Studio Code terminált.

   Ha nem jelenik meg egy parancssort, válassza ki az ENTER billentyűt.

1. Adja meg `dotnet run` billentyűparancsot a terminálon.

1. Ha a rendszer kéri az IoT Hub kapcsolati karakterláncra, illessze be az előző szakaszban másolt kapcsolati karakterláncra.

1. Az a **Azure IoT Hub-eszközök** kerete, kattintson a frissítés gombra.

   ![Refresh IoT Hub device list](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Vegye figyelembe, hogy a rendszer hozzáadja az IoT hub és az, hogy az eszközök megjelennek zöld jelzi, hogy adatokat küld a rendszer ezen eszköz segítségével.

1. Megtekintheti a jobb gombbal kattint, bármely eszközön, és válassza a hubnak küldött üzeneteket **figyelési D2C üzenet Start**. Az üzenetek jelennek meg a Visual Studio Code-ban a tesztkimenet ablaktáblán.

1. Leállíthatja a figyelést parancsra kattintva a **Azure IoT Hub-eszközkészlet** kimeneti ablaktáblában, és válassza a **Stop Monitoring D2C üzenet**.

1. Lehetővé teszi az alkalmazás, befejezését, ami eltarthat néhány percig.

## <a name="check-iot-hub-for-activity"></a>Ellenőrizze az IoT Hub tevékenység

A DeviceHarness által küldött adatok hiba történt az IoT hubnak. Győződjön meg arról, hogy elérte-e az adatok a hub, az Azure portal használatával történő könnyebbé vált.

1. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg az IoT hubot.

1. Az Áttekintés lapon kell megjelennie, hogy küldte-e adatokat a hubhoz:  

   ![Eszköz megtekintése a felhőbe irányuló üzenetek IoT hubban](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Az Azure Storage-adatok érvényesítése

A kapott az IoT hubnak küldött adatok a storage-tárolóba, az előző cikkben létrehozott lett átirányítva. Tekintsük át az adatok a storage-fiókban.

1. Az Azure Portalon lépjen a tárfiókra.

1. Válassza ki a storage-fiók Navigátor **Tártallózó (előzetes verzió)** .

1. Válassza ki a storage explorer **Blobtárolók** majd **devicedata**.

1. A tartalom ablaktáblában kattintson a mappa neve: az IoT hub, majd az év, hónap, nap és óra. Látni fogja a perc, ha az adatok írása volt jelölő több mappát.

   ![Mappák megtekintése a blob storage-ban](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kattintson egy ezek a mappák feliratú adatfájlok **00** és **01** megfelelő partícióra.

1. A fájlok nyelven írták [Avro](https://avro.apache.org/) formátumban, de duplán kattint egy ezeket a fájlokat egy új böngészőlapon nyissa meg, és adatok részben jelennek meg. Ha Ehelyett nyissa meg a fájlt a program a rendszer kéri, használhatja a VS Code, és megfelelően alakul.

1. Nem kell próbál meg olvasni, vagy az adatok értelmezéséhez most; a következő cikkben tesszük azt.

## <a name="next-steps"></a>További lépések

Ez a cikk egy .NET Core-projektet használtuk virtuális eszközök csoportját hozhatja létre és adatokat küldeni azokat az eszközöket az IoT hubon keresztül és a egy Azure Storage-tárolóba. Ez a projekt szimulálja a valós életből vett helyzet, ahol a fizikai eszközökön adatküldés érzékelőinek adatai, a működési beállításait, a hiba jelek és a mód, és így tovább, az IoT Hub és meghajtóbetűjeltől egy válogatott storage is beleértve. Elég adatokat összegyűjtötte-e, ha használjuk azt, hogy a fennmaradó hasznos élettartamának (RUL) az eszközhöz, amely a következő cikkben bemutatjuk modelleket taníthat be.

A következő cikk betanításához egy gépi tanulási modellt az adatok továbbra is.

> [!div class="nextstepaction"]
> [Betanítását és üzembe helyezése az Azure Machine Learning-modell](tutorial-machine-learning-edge-04-train-model.md)
