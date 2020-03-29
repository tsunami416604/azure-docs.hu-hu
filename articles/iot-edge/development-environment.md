---
title: Azure IoT Edge fejlesztési környezet | Microsoft dokumentumok
description: Ismerje meg a támogatott rendszereket és a külső fejlesztési eszközöket, amelyek segítenek az IoT Edge-modulok létrehozásában
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96078be20e8048e481a994fefc169e48ab1d8459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511093"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Készítse elő fejlesztési és tesztelési környezetét az IoT Edge számára

Az Azure IoT Edge a meglévő üzleti logikát a peremhálózaton működő eszközökre helyezi át. Az alkalmazások és a számítási feladatok [ioT Edge-modulokként](iot-edge-modules.md)való futtatására való előkészítéséhez tárolóként kell megépítenie őket. Ez a cikk útmutatást nyújt a fejlesztői környezet konfigurálásához, hogy sikeresen hozhasson létre egy IoT Edge-megoldást. Miután beállította a fejlesztői környezetet, [megtudhatja, hogyan fejlesztheti saját IoT Edge-moduljait.](module-development.md)

Bármely IoT Edge-megoldásban legalább két gépet kell figyelembe venni. Az egyik maga az IoT Edge-eszköz (vagy eszközök), amely az IoT Edge-modult futtatja. A másik a fejlesztői gép, amely a modulok létrehozásához, teszteléséhez és üzembe helyezéséhez használható. Ez a cikk elsősorban a fejlesztőgépre összpontosít. Tesztelési célokra a két gép azonos lehet. Az IoT Edge-et futtathatja a fejlesztői gépen, és modulokat telepíthet hozzá.

## <a name="operating-system"></a>Operációs rendszer

Az Azure IoT Edge a [támogatott operációs rendszerek](support.md)meghatározott készletén fut. Az IoT Edge fejlesztéséhez használhatja a legtöbb operációs rendszert, amely képes futtatni egy tárolómotort. A tárolómotor a fejlesztői gépen a modulok tárolóként való felépítésének követelménye, és leküldéses egy tároló-beállításjegyzékbe.

Ha a fejlesztői gép nem tudja futtatni az Azure IoT Edge-et, folytassa ebben a cikkben a helyi tesztelést és hibakeresést segítő [tesztelési eszközök](#testing-tools) ről.

A fejlesztői gép operációs rendszerének nem kell egyeznie az IoT Edge-eszköz operációs rendszerével. A tároló operációs rendszerének azonban konzisztensnek kell lennie a fejlesztői gép és az IoT Edge-eszköz között. Például modulokat fejleszthet egy Windows-gépen, és telepítheti őket egy Linux-eszközre. A Windows-gépnek Linux-tárolókat kell futtatnia a Linux-eszköz moduljainak létrehozásához.

## <a name="container-engine"></a>Konténermotor

Az IoT Edge központi koncepciója az, hogy távolról telepítheti az üzleti és a felhőbeli logikát az eszközökre a tárolókba csomagolva. Konténerek létrehozásához szüksége van egy tárolómotorra a fejlesztői gépen.

Az IoT Edge-eszközök egyetlen támogatott tárolómotorja a Moby. Azonban az Open Container Initiative-vel kompatibilis tárolómotorok, például a Docker, képesek IoT Edge-modullemezképek készítésére.

## <a name="development-tools"></a>Fejlesztési eszközök

A Visual Studio és a Visual Studio Code egyaránt rendelkezik bővítménybővítményeket az IoT Edge-megoldások fejlesztéséhez. Ezek a bővítmények nyelvspecifikus sablonokat biztosítanak az új IoT Edge-forgatókönyvek létrehozásához és üzembe helyezéséhez. Az Azure IoT Edge-bővítmények a Visual Studio és a Visual Studio-kód segítségével kódolhatja, hozhat létre, üzembe helyezheti és hibakeresést az IoT Edge-megoldások. Létrehozhat egy teljes IoT Edge-megoldást, amely több modult tartalmaz, és a bővítmények automatikusan frissítik a központi telepítési jegyzéksablont minden új modulhozzáadásával. A bővítményekkel az IoT-eszközöket a Visual Studio vagy a Visual Studio-kód ból is kezelheti. Modulok üzembe helyezése egy eszközre, az állapot figyelése, és az üzenetek megtekintése, ahogy megérkeznek az IoT Hub. Mindkét bővítmény az [IoT EdgeHub fejlesztői eszközt](#iot-edgehub-dev-tool) használja a modulok helyi futtatásának és hibakeresésének engedélyezéséhez a fejlesztői gépen is.

Ha más szerkesztőkkel vagy a CLI-vel szeretne fejleszteni, az Azure IoT Edge fejlesztői eszköz parancsokat biztosít, hogy a parancssorból fejleszthet és tesztelhet. Új IoT Edge-forgatókönyveket hozhat létre, modulképeket hozhat létre, modulokat futtathat egy szimulátorban, és figyelheti az IoT Hubnak küldött üzeneteket.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-bővítmény

A Visual Studio-kód Azure IoT Edge bővítménye olyan programozási nyelvekre épülő IoT Edge-modulsablonokat biztosít, mint a C, C#, Java, Node.js és Python, valamint a C#-ben lévő Azure-függvények.

További információkért és letöltésért lásd: [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Az IoT Edge-bővítmények mellett hasznos lehet további bővítmények telepítése a fejlesztéshez. A [Docker-támogatás a Visual Studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) használatával például kezelheti a rendszerképeket, tárolókat és nyilvántartásokat. Emellett az összes főbb támogatott nyelv rendelkezik a Visual Studio-kód bővítményeivel, amelyek segíthetnek a modulok fejlesztésében.

#### <a name="prerequisites"></a>Előfeltételek

Egyes nyelvek és szolgáltatások modulsablonjai olyan előfeltételekkel rendelkeznek, amelyek szükségesek a projektmappák létrehozásához a fejlesztői gépen a Visual Studio-kóddal.

| Modulsablon | Előfeltétel |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C# | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE fejlesztői készlet 10](https://aka.ms/azure-jdks) <li> [A JAVA_HOME környezeti változó beállítása](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js modulgenerátor](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019-es bővítmény

Az Azure IoT Edge-eszközök a Visual Studio egy IoT Edge modul sablon c# és c alapú.

További információkért és letöltésért lásd: [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) vagy [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>IoT Edge fejlesztői eszköz

Az Azure IoT Edge fejlesztői eszköz leegyszerűsíti az IoT Edge-fejlesztést parancssori képességekkel. Ez az eszköz CLI-parancsokat biztosít a modulok fejlesztéséhez, hibakereséséhez és teszteléséhez. Az IoT Edge fejlesztői eszköz együttműködik a fejlesztési rendszer, függetlenül attól, hogy manuálisan telepítette a függőségeket a gépen, vagy az IoT Edge fejlesztői tárolót használja.

További információkért és a kezdéshez lásd: [IoT Edge fejlesztői eszköz wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Vizsgálati eszközök

Számos tesztelési eszköz létezik az IoT Edge-eszközök vagy a hibamodulok hatékonyabb szimulálásához. Az alábbi táblázat az eszközök magas szintű összehasonlítását mutatja be, majd az egyes szakaszok pontosabban ismertetik az egyes eszközöket.

Csak az IoT Edge-futásidejű éles környezetben támogatott, de a következő eszközök lehetővé teszik, hogy szimulálja, vagy egyszerűen hozzon létre IoT Edge-eszközök fejlesztési és tesztelési célokra. Ezek az eszközök nem zárják ki egymást, de együtt dolgozhatnak a teljes fejlesztési élmény érdekében.

| Eszköz | Néven | Támogatott platformok | A következőkre alkalmas |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub fejlesztői eszköz  | iotedgehubdev | Windows, Linux, MacOS | A modulok hibakereséséhez eszköz szimulálása. |
| IoT Edge fejlesztői tároló | microsoft/iotedgedev | Windows, Linux, MacOS | Függőségek telepítése nélkül fejleszt. |
| IoT Edge futásidejű egy tárolóban | iotedgec | Windows, Linux, MacOS, ARM | Tesztelés olyan eszközön, amely nem támogatja a futásidejűt. |
| IoT Edge-eszköztároló | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Egy forgatókönyv tesztelése számos IoT Edge-eszközzel nagy méretekben. |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub fejlesztői eszköz

Az Azure IoT EdgeHub fejlesztői eszköz helyi fejlesztési és hibakeresési élményt nyújt. Az eszköz segít az IoT Edge-modulok elindításában az IoT Edge futásideje nélkül, így helyileg hozhat létre, fejleszthet, tesztelhet, futtathat és debugolhatja az IoT Edge-modulokat és -megoldásokat. Nem kell leadni a rendszerképeket egy tároló beállításjegyzékbe, és telepíteni őket egy eszközre tesztelésre.

Az IoT EdgeHub fejlesztői eszköz úgy lett kialakítva, hogy a Visual Studio és a Visual Studio Code-bővítményekkel, valamint az IoT Edge fejlesztői eszközzel párhuzamosan működjön. Támogatja a belső hurok fejlesztését, valamint a külső hurok tesztelését, így integrálható a DevOps eszközökkel is.

További információkért és a telepítésért lásd: [Azure IoT EdgeHub fejlesztői eszköz.](https://pypi.org/project/iotedgehubdev/)

### <a name="iot-edge-dev-container"></a>IoT Edge fejlesztői tároló

Az Azure IoT Edge-fejlesztői tároló egy Docker-tároló, amely rendelkezik az IoT Edge-fejlesztéshez szükséges összes függőséget. Ez a tároló megkönnyíti a kezdéshez, bármelyik nyelvet szeretne fejleszteni, beleértve a C#, Python, Node.js és java. Mindössze egy tárolómotort kell telepítenie, például a Docker t vagy a Mobyt, hogy a tárolót a fejlesztői gépre húzza.

További információ: [Azure IoT Edge dev container.](https://hub.docker.com/r/microsoft/iotedgedev/)

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge futásidejű egy tárolóban

Az IoT Edge-futásidejű egy tárolóban biztosít egy teljes futásidejű, amely az eszköz kapcsolati karakterlánc, mint egy környezeti változó. Ez a tároló lehetővé teszi az IoT Edge-modulok és forgatókönyvek tesztelését egy olyan rendszeren, amely nem támogatja a futásidejű natívan, például a MacOS. A telepített modulok a futásidejű tárolón kívül indulnak el. Ha azt szeretné, hogy a futásidejű és az üzembe helyezett modulok ugyanabban a tárolóban létezzenek, fontolja meg az IoT Edge-eszköztárolót.

További információ: [Az Azure IoT Edge futtatása egy tárolóban.](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)

### <a name="iot-edge-device-container"></a>IoT Edge-eszköztároló

Az IoT Edge-eszköztároló egy teljes IoT Edge-eszköz, amely készen áll arra, hogy bármely, tárolómotorral rendelkező gépen elinduljon. Az eszköztároló tartalmazza az IoT Edge futásidejű és egy tárolómotor magát. A tároló minden példánya egy teljesen működőképes, önkiépítési IoT Edge-eszköz. Az eszköztároló támogatja a modulok távoli hibakeresését, feltéve, hogy van hálózati útvonal a modulhoz. Az eszköztároló nagy számú IoT Edge-eszköz gyors létrehozásához használható nagy számú, nagy méretű forgatókönyvek vagy Azure-folyamatok teszteléséhez. Azt is támogatja a telepítést a kubernetes keresztül helm.

További információ: [Azure IoT Edge-eszköztároló.](https://github.com/toolboc/azure-iot-edge-device-container)

## <a name="devops-tools"></a>DevOps-eszközök

Ha készen áll a széles körű éles forgatókönyvekhez készült részletes megoldások kifejlesztésére, használja ki a modern DevOps-alapelveket, beleértve az automatizálást, a figyelést és az egyszerűsített szoftverfejlesztési folyamatokat. Az IoT Edge bővítményei támogatják a DevOps-eszközöket, például az Azure DevOps-t, az Azure DevOps-projekteket és a Jenkins-t. Ha egy meglévő folyamattestreszabásához vagy egy másik DevOps-eszköz, például a CircleCI vagy a TravisCI használatához szeretne testreszabni, ezt megteheti az IoT Edge fejlesztői eszközben található CLI-funkciókkal.

További információkért, útmutatásért és példákért lásd a következő oldalakat:

* [Folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge-ben](how-to-ci-cd.md)
* [CI/CD-folyamat létrehozása az IoT Edge számára az Azure DevOps-projektekkel](how-to-devops-project.md)
* [Azure IoT Edge Jenkins beépülő modul](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub-tárhét](https://github.com/toolboc/IoTEdge-DevOps)
