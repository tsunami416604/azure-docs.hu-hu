---
title: 'Oktatóanyag: Szimulált eszközadatok létrehozása – Machine Learning az Azure IoT Edge-en'
description: Hozzon létre olyan virtuális eszközöket, amelyek szimulált telemetriai adatokat hoznak létre, amelyek később gépi tanulási modell betanítására használhatók.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8f7a971315183e867ae06b58801d5855f90462a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76722373"
---
# <a name="tutorial-generate-simulated-device-data"></a>Oktatóanyag: Szimulált eszközadatok létrehozása

> [!NOTE]
> Ez a cikk az Azure Machine Learning IoT Edge-en való használatával kapcsolatos oktatóanyag sorozatának része. Ha megérkezett ezt a cikket közvetlenül, javasoljuk, hogy kezdődik az [első cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredményt.

Ebben a cikkben gépi tanulási betanítási adatokat használunk az Azure IoT Hubra telemetriai adatokat küldő eszköz szimulálására. Amint azt a bevezetőben, ez a bemutató használja a [Turbofan motor degradációs szimulációs adatkészlet](https://c3.nasa.gov/dashlink/resources/139/) szimulálni adatokat egy sor repülőgép motorok képzés és tesztelés.

A mi kísérleti forgatókönyv, tudjuk, hogy:

* Az adatok több többváltozós idősorozatból állnak.
* Minden adatkészlet betanítási és tesztelési részhalmazokra van osztva.
* Minden idősorozat egy másik motorból származik.
* Minden motor különböző kezdeti kopási és gyártási variációkkal kezdődik.

Ebben az oktatóanyagban egyetlen adatkészlet (FD003) betanítási adatkészletének betanítási adatait használjuk.

A valóságban minden motor egy független IoT-eszköz lenne. Feltételezve, hogy nem rendelkezik az internethez csatlakoztatott turbóventilátoros motorok gyűjteményével, egy szoftveres stand-int építünk ezekhez az eszközökhöz.

A szimulátor egy C# program, amely az IoT Hub API-k at használja a virtuális eszközök programozási regisztrációjára az IoT Hubsegítségével. Ezután a NASA által biztosított adathalmazból minden egyes eszköz adatait felolvassuk, és egy szimulált IoT-eszköz használatával elküldjük az IoT hubra. Az oktatóanyag ezen részének összes kódja megtalálható a tárház DeviceHarness könyvtárában.

A DeviceHarness projekt egy C# nyelven írt .NET alapprojekt, amely négy osztályból áll:

* **Program:** A felhasználói bevitel kezeléséért és az általános koordinációért felelős végrehajtási belépési pont.
* **TrainingFileManager:** Felelős a kijelölt adatfájl olvasásáért és elemzési adataiért.
* **Ciklusadatok:** Egyetlen adatsort jelöl üzenetformátumra konvertált fájlban.
* **TurbofanDevice:** Egyetlen eszköznek (idősorozatnak) megfelelő IoT-eszköz létrehozásáért és az adatok IoT Hubba való továbbításáért felelős.

Az ebben a cikkben ismertetett feladatok végrehajtása körülbelül 20 percet vesz igénybe.

Az ebben a lépésben végzett munkával egyenértékű valós összeegyezeket valószínűleg eszközfejlesztők és felhőfejlesztők végzik el.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>A Visual Studio-kód konfigurálása és a DeviceHarness projekt létrehozása

1. Nyisson meg egy távoli asztali munkamenetet a fejlesztői virtuális gépszámára.

1. A Visual Studio kódjában nyissa meg a `C:\source\IoTEdgeAndMlSample\DeviceHarness` mappát.

1. Mivel első alkalommal használ bővítményeket ezen a számítógépen, egyes bővítmények frissítik és telepítik a függőségeiket. Előfordulhat, hogy a rendszer kéri a bővítmény frissítését. Ha igen, válassza **az Ablak újratöltése**lehetőséget.

   Ha az OmniSharp hibák megjelennek a kimeneti ablakban, el kell távolítania a C# kiterjesztést.

1. A rendszer kérni fogja, hogy adja hozzá a szükséges eszközöket a DeviceHarness számára. A hozzáadásukhoz válassza az **Igen** lehetőséget.

   * Az értesítés megjelenése néhány másodpercet is igénybe vehet.
   * Ha nem fogadott el erről az értesítésről, ellenőrizze a csengő ikonját a jobb alsó sarokban.

   ![VS Code kiterjesztés felugró ablak](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. A csomagfüggőségek visszaállításához válassza a **Visszaállítás** lehetőséget.

   ![VS kód visszaállítási parancsa](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Ha nem kapja meg ezeket az értesítéseket, zárja be a `C:\source\IoTEdgeAndMlSample\DeviceHarness`Visual Studio-kódot, törölje a bin és obj könyvtárakat a alkalmazásban, nyissa meg a Visual Studio-kódot, majd nyissa meg újra a DeviceHarness mappát.

1. Ellenőrizze, hogy a környezet megfelelően van-e beállítva egy build, **a Ctrl** + **Shift** + **B**vagy **a Terminálfuttatási** > **buildfeladat elindításával.**

1. A rendszer kéri, hogy válassza ki a futtatni kívánt buildelési feladatot. Válassza **a Build**lehetőséget.

1. A build fut, és sikeres üzenetet ad ki.

   ![Sikeres kimeneti üzenet összeállítása](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Ezt a buildet alapértelmezett buildelési feladatként teheti meg, ha kiválasztja a > **Terminálkonfigurálás alapértelmezett buildelési feladatát,** és a parancssorból a **Build** parancsot választja. **Terminal**

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Csatlakozás az IoT Hubhoz és a DeviceHarness futtatása

Most, hogy rendelkezünk a projektépülettel, csatlakozzon az IoT hubhoz a kapcsolati karakterlánc eléréséhez és az adatgenerálás előrehaladásának figyeléséhez.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Bejelentkezés az Azure-ba a Visual Studio-kódban

1. Jelentkezzen be Azure-előfizetésébe a Visual Studio-kódban a `Ctrl + Shift + P` parancspaletta vagy a **Parancspaletta megtekintése** > **paratelletikán.**

1. Keresse meg az **Azure: Sign In** parancsot.

   Megnyílik egy böngészőablak, és kéri a hitelesítő adatok megadását. Ha a sikeres oldalra irányítja át, bezárhatja a böngészőt.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Csatlakozás az IoT-központhoz és a hub kapcsolati karakterláncának lekérése

1. A Visual Studio Kódkezelő alsó részén válassza ki az **Azure IoT** Hub-keretet a kibontásához.

1. A kibontott keretben kattintson az **IoT-központ kijelölése elemre.**

1. Amikor a rendszer kéri, válassza ki az Azure-előfizetést, majd az IoT-központot.

1. Kattintson **a ...** az Azure **IoT Hub** jobb oldalán további műveletekért. Válassza **az IoT Hub kapcsolati karakterláncának másolása lehetőséget.**

   ![IoT Hub-kapcsolati karakterlánc másolása](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>A DeviceHarness projekt futtatása

1. Válassza **a Terminál megtekintése lehetőséget** > **Terminal** a Visual Studio Code terminál megnyitásához.

   Ha nem jelenik meg a kérdés, nyomja le az Enter billentyűt.

1. A terminálban írja be a következőt: `dotnet run`.

1. Amikor a rendszer kéri az IoT Hub kapcsolati karakterláncát, illessze be az előző szakaszban másolt kapcsolati karakterláncot.

1. Az **Azure IoT Hub-eszközök** keretben kattintson a frissítés gombra.

   ![IoT Hub-eszközlista frissítése](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Vegye figyelembe, hogy az eszközök hozzáadódnak az IoT Hubhoz, és hogy az eszközök zöld színnel jelennek meg annak jelzésére, hogy az adatok küldése az eszközön keresztül történik. Miután az eszközök üzeneteket küldenek az IoT hubra, bontják a kapcsolatot, és kékszínnel jelennek meg.

1. A hubra küldött üzeneteket úgy tekintheti meg, hogy a jobb gombbal bármelyik eszközre kattint, és a **Beépített eseményvégpont indítása parancsot választja.** Az üzenetek a Visual Studio-kód kimeneti ablaktáblájában jelennek meg.

1. Állítsa le a figyelést az **Azure IoT Hub** kimeneti ablaktáblájára kattintva, és válassza a **Beépített eseményvégpont figyelése leállítása**lehetőséget.

1. Hagyja, hogy az alkalmazás fut a befejezésig, amely néhány percet vesz igénybe.

## <a name="check-iot-hub-for-activity"></a>Tevékenység ellenőrzése az IoT Hubban

A DeviceHarness által küldött adatok az IoT hub, ahol ellenőrizheti az Azure Portalon.

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és keresse meg az ehhez az oktatóanyaghoz létrehozott IoT-központot.

1. A bal oldali ablaktábla **menüjében**válassza a Figyelés csoport **Metrikák ( Metrikák**) lehetőséget.

1. A diagramdefiníció lapon kattintson a **Metrikus legördülő listára,** görgessen le a listáról, és válassza az **Útválasztás: a tárolóba szállított adatok**lehetőséget. A diagramnak meg kell jelennie annak a csúcsnak, amikor az adatok at a tárolóba irányították.

   ![A diagram csúcsot jelenít meg, amikor a tárolóba szállított adatok](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Adatok ellenőrzése az Azure Storage-ban

Az IoT hubra küldött adatok at az előző cikkben létrehozott tárolótárolóba irányítottuk. Nézzük meg a tárfiókunk adatait.

1. Az Azure Portalon lépjen a tárfiókra.

1. A tárfiók navigátorában válassza a **Storage Explorer (előzetes verzió)** lehetőséget.

1. A tárolókezelőben válassza a `devicedata`Blob Containers **(Blob Containers)** lehetőséget.

1. A tartalomablaktáblán kattintson az IoT hub nevének mappájára, amelyet év, hónap, nap és óra követ. Az adatok írásának percét több mappa fogja látni.

   ![Mappák megtekintése a blobstorage-ban](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kattintson az egyik ilyen mappák at keresni adatfájlok feliratú **00** és **01** megfelelő partíciót.

1. A fájlok [avro](https://avro.apache.org/) formátumban vannak megírva. Kattintson duplán az egyik ilyen fájlra egy másik böngészőlap megnyitásához és az adatok részleges megjelenítéséhez. Ha a program kéri a fájl megnyitását, válassza a VS-kód lehetőséget, és az helyesen jelenik meg.

1. Nincs szükség arra, hogy megpróbálja elolvasni vagy értelmezni az adatokat most; fogjuk csinálni a következő cikkben.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy .NET Core projektet használtunk a virtuális IoT-eszközök készletének létrehozásához, és adatokat küldhetünk rajtuk keresztül az IoT hubunkba és egy Azure Storage-tárolóba. Ez a projekt egy valós forgatókönyvet szimulál, amelyben a fizikai IoT-eszközök adatokat küldenek egy IoT Hubba, majd tovább egy válogatott tárolóba. Ezek az adatok magukban foglalják az érzékelők leolvasását, a működési beállításokat, a hibajeleket és a módokat és így tovább. Miután elegendő adatot gyűjtöttünk, olyan modellek betanítására használjuk, amelyek előre jelzik az eszköz fennmaradó hasznos élettartamát (RUL). Bemutatjuk ezt a gépi tanulást a következő cikkben.

Folytassa a következő cikket egy gépi tanulási modell betanításához az adatokkal.

> [!div class="nextstepaction"]
> [Azure Machine Learning-modell betanítása és üzembe helyezése](tutorial-machine-learning-edge-04-train-model.md)
