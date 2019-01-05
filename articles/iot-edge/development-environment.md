---
title: Az Azure IoT Edge-fejlesztőkörnyezetet |} A Microsoft Docs
description: További információ a támogatott rendszerek és a belső fejlesztői eszközöket, amelyek segítenek az IoT Edge modulok létrehozása
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f3d1fb393138aeb590bc0645d6abe93652a52a5b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052581"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>A fejlesztési és tesztelési környezet előkészítése az IoT Edge-hez

Az Azure IoT Edge a peremen működő eszközökre helyezi át a meglévő üzleti logikát. Az alkalmazások és számítási feladatok futtatásához előkészítése [IoT Edge-modulok](iot-edge-modules.md), azokat tárolókként létrehozásához szükséges. Ez a cikk nyújt útmutatást a fejlesztési környezet konfigurálása, hogy sikeresen hozhat létre egy IoT Edge-megoldás. Ha már rendelkezik, állítsa be a fejlesztési környezetet, majd további hogyan [saját IoT Edge-modulok fejlesztése](module-development.md).

Minden IoT Edge-megoldásban nincsenek kell figyelembe venni legalább két gépet. Az egyik megoldás az IoT Edge-eszköz (vagy eszközök) magát, amely futtatja az IoT Edge-modul. A másik pedig a fejlesztői gépen, amellyel létrehozására, tesztelésére és hogyan helyezhet üzembe modulokat. Ez a cikk elsősorban a fejlesztői gépen foglalkozik. Tesztelési célokra, a két számítógép lehet ugyanaz. IoT Edge futtatása a fejlesztői gépen, és a modulok történő üzembe helyezéséhez.

## <a name="operating-system"></a>Operációs rendszer

Az Azure IoT Edge futtat egy meghatározott készletének [támogatott operációs rendszerek](support.md). Az IoT Edge fejlesztéséhez használhatja a legtöbb operációs rendszer futtatható egy tároló-motor. A tároló motor akkor hozhat létre a modulok tárolókként, és azok leküldése egy tároló-beállításjegyzéket, a fejlesztői gépen szükséges. 

Ha a fejlesztői gépén nem futtatható az Azure IoT Edge, továbbra is ebben a cikkben megismerheti [tesztelőeszközök](#testing-tools) , amelyekkel a tesztelési és hibakeresési helyileg. 

A fejlesztői gépén operációs rendszere nem kell megegyeznie az IoT Edge-eszköz operációs rendszerének. Az operációs rendszer, konzisztens fejlesztési számítógép és az IoT Edge-eszköz között kell lennie. Például egy Windows-gépen modulok fejlesztése és üzembe helyezése Linux rendszerű eszköz. A Windows-gépen kell futtatni a modulok a Linux rendszerű eszköz hozhat létre Linux-tárolókat. 

## <a name="container-engine"></a>Container-motor

A központi IoT Edge fogalma, hogy távolról telepíthet az üzleti és a felhő logikai eszközök csomagolás tárolókba. Tárolók létrehozása, szüksége egy tároló-motor a fejlesztői gépen. 

Az egyetlen támogatott container-motor IoT Edge-eszközökön a termelési Moby. Azonban bármely tároló motor kompatibilis a nyílt tároló-kezdeményezéshez, például a Dockert, az IoT Edge-modul lemezképek létrehozásának alkalmas állapotban. 

## <a name="development-tools"></a>Fejlesztési eszközök

A Visual Studio és a Visual Studio Code rendelkeznek kiegészítő IoT Edge-megoldások fejlesztéséhez. Ezek a bővítmények adja meg a nyelvspecifikus sablonok létrehozásához és üzembe helyezése az új IoT Edge-forgatókönyveket. Az Azure IoT Edge-bővítmények code, elkészítheti, telepítheti, és az IoT Edge-megoldások hibakeresése a Visual Studio és a Visual Studio Code segítséget. Teljes IoT Edge megoldás, amely tartalmazza a több modul is létrehozhat, és a bővítményeket automatikusan frissítheti a jegyzékfájl a központi telepítési sablont minden egyes új modul hozzáadásával. A bővítményekkel is kezelheti a Visual Studio vagy Visual Studio Code belül az IoT-eszközökről. Modulok telepítése egy eszközön, az állapot figyelése és üzenetek megtekintése, amikor azok megérkeznek az IoT Hub. Mindkét bővítmény használata a [IoT EdgeHub fejlesztőeszközt](#iot-edgehub-dev-tool) engedélyezése a helyi rendszert futtató és a modulok a fejlesztői gépen, valamint a hibakeresés. 

Ha inkább az egyéb szerkesztők vagy a parancssori felületen fejlesztéshez, az Azure IoT Edge fejlesztőeszközt parancsokat kínálja, így fejlesztheti és tesztelheti a parancssorból. Hozzon létre új IoT Edge helyzeteket, modul lemezképeket, szimulátort modulok futtatását és az IoT hubnak küldött üzenetek figyeléséhez. 

### <a name="visual-studio-code-extension"></a>Visual Studio Code-bővítmény

Az Azure IoT Edge-bővítmény a Visual Studio Code biztosít az IoT Edge programozási nyelvek C, beleértve a beépített modul sablonok C#, Java, Node.js, Python, valamint az Azure functions C#. 

További információ és letöltése: [Azure IoT-eszközök a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Az IoT Edge-bővítményeket mellett, előfordulhat, hogy hasznos fejlesztéséhez további bővítmények telepítéséhez. Használhatja például [Docker-támogatás a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) a lemezképek, a tárolók és a beállításjegyzékek kezeléséhez. Ezenkívül az összes fő támogatott nyelvek rendelkeznek a Visual Studio Code, amely segíthet a modulok kidolgozása során. 

#### <a name="prerequisites"></a>Előfeltételek

Az egyes nyelvekhez és a szolgáltatások modul sablonok rendelkezik a projekt mappák létrehozása a Visual Studio Code-dal fejlesztői gépen való szükséges előfeltételeket.

| A modul sablon | Előfeltétel |
| --------------- | ------------ |
| Azure Functions | [A .NET core SDK 2.1-es](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [A .NET core SDK 2.1-es](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [A JAVA_HOME környezeti változó beállítása](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven 3](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Az Azure IoT Edge Node.js modult generator](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [a pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Cookiecutteru](https://cookiecutter.readthedocs.io/en/latest/installation.html) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-2017-extension"></a>Visual Studio 2017-bővítmény

A Azure IoT Edge-eszközök Visual Studióhoz készült adja meg az IoT Edge, a modul sablon épülő C#. 

További információ és letöltése: [Azure IoT Edge-eszközök Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

### <a name="iot-edge-dev-tool"></a>IoT Edge fejlesztési eszközt

Az Azure IoT Edge fejlesztőeszközt egyszerűbbé teszi a parancssori képességek az IoT Edge fejlesztői útmutatójában. Ez az eszköz a CLI-parancsok használatával fejlesztése, hibakeresése és tesztelése a modulok biztosít. Az IoT Edge fejlesztői eszköz együttműködik a fejlesztői rendszerhez manuálisan telepítette a függőségeket a gépen, vagy az IoT Edge fejlesztői tárolót használ-e. 

További információ és első lépések: [IoT Edge fejlesztői eszköz wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Tesztelőeszközök

Számos tesztelési eszköz szimulálása az IoT Edge-eszközök és a hatékony hibakeresés modulok segítő létezik. Az alábbi táblázatban láthatók az eszközök közötti magas szintű összehasonlítása, és ezután a az egyes szakaszok ismertetik az egyes eszközök pontosabban. 

Csak az IoT Edge-futtatókörnyezet éles környezetekben üzemelő példányok esetében támogatott, de a következő eszközök lehetővé teszik szimulálása, vagy egyszerűen hozzon létre IoT Edge fejlesztési és tesztelési célú eszközöket. Ezek az eszközök nem egymást kölcsönösen kizáró, de egy teljes körű fejlesztői élmény együtt is képes működni. 

| Eszköz | Más néven | Támogatott platformok | A következőkre alkalmas |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub fejlesztési eszközt  | iotedgehubdev | Windows, Linux, MacOS | Modulok hibakeresése egy eszközt szimulál. |
| IoT Edge fejlesztői tároló | a Microsoft/iotedgedev | Windows, Linux, MacOS | Fejlesztés a függőségek telepítése nélkül. |
| IoT Edge-futtatókörnyezet-tárolóban | iotedgec | Windows, Linux, MacOS, ARM | Az eszközön, amely nem támogatja a futtatókörnyezet tesztelése. |
| IoT Edge-eszköz tároló | toolboc/azure-iot-edge-eszköz-tároló | Windows, Linux, MacOS, ARM | Egy olyan forgatókönyvet tesztel az sok nagy mennyiségű IoT Edge-eszközökön. |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub fejlesztési eszközt

Az Azure IoT EdgeHub fejlesztőeszközt fejlesztése és hibakeresése helyi élményt nyújt. Az eszköz segít kezdő, IoT Edge-modulok nélkül az IoT Edge-futtatókörnyezet, hogy hozhat létre, fejlesztéséhez, teszteléséhez, futtassa, és hibakeresése az IoT Edge-modulok és a helyi megoldások. Nem kell rendszerképek leküldése egy tároló-beállításjegyzéket, és telepítse őket a tesztelési eszköz.

Az IoT EdgeHub fejlesztőeszközt úgy lett kialakítva, a Visual Studio és a Visual Studio Code-bővítmények, valamint az IoT Edge fejlesztői eszközzel párhuzamosan működjön. IT támogatja a belső ciklus fejlesztési, valamint a külső hurok tesztelés, így integrálható túl a DevOps-eszközök. 

További információ és telepítéséhez lásd: [Azure IoT EdgeHub fejlesztőeszközt](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>IoT Edge fejlesztői tároló

Az Azure IoT Edge fejlesztői tároló egy Docker-tároló, amely rendelkezik az IoT Edge-fejlesztéshez szükséges összes függőséget. Ez a tároló megkönnyíti a kezdéshez bármelyik sablonnyelvi szeretne fejlesztéshez, beleértve a C#, Python, Node.js és Java. Telepítenie kell csak egy tároló-motor, mint a Docker vagy Moby, a fejlesztői gépére a tároló lekéréséhez. 

További információkért lásd: [Azure IoT Edge fejlesztői tároló](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge-futtatókörnyezet-tárolóban

Az IoT Edge-futtatókörnyezet egy tárolóban, amely az eszköz kapcsolati karakterláncát környezeti változóban teljes futtatókörnyezetet biztosít. Ez a tároló lehetővé teszi, hogy tesztelje IoT Edge-modulok és forgatókönyvek, amelyek nem támogatják a modul natív módon, például a MacOS rendszeren. A modul tárolón kívüli indul üzembe helyezett modulokat. Ha szeretné, hogy a futásidejű és bármely üzembe helyezett modulok létezniük ugyanazt a tárolót, fontolja meg ehelyett az IoT Edge-eszköz tároló.

További információkért lásd: [Azure IoT Edge-tárolóban futó](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>IoT Edge-eszköz tároló

Az IoT Edge-eszköz tároló egy teljes körű IoT Edge-eszköz, készen áll a tároló motorral bármely gépen indítható. Az eszköz tároló tartalmaz az IoT Edge-futtatókörnyezet és a egy tároló-motor magát. A tároló minden példánya egy teljes körűen használható önálló kiépítési IoT Edge-eszköz. Az eszköz tároló támogatja a modulok, a távoli hibakeresés mindaddig, amíg a hálózati útvonal a modulnak van. Az eszköz tároló jó nagy számú IoT Edge-eszközök ipari méretekben forgatókönyvek vagy a fejlesztési és üzemeltetési folyamatok tesztelése gyors létrehozásakor. Ezen kívül támogatja az üzembe helyezés a kubernetesszel helm-n keresztül. 

További információkért lásd: [Azure IoT Edge-eszköz tároló](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>DevOps-eszközök

Ha készen áll a széles körű éles szituációhoz kínál megoldásokat, ipari méretekben fejleszthet, beleértve az automation, figyelés és folyamatok zökkenőmentes szoftverfőmérnöke modern fejlesztési és üzemeltetési alapelvek előnyeit. IoT Edge van extensions DevOps-eszközök, többek között az Azure DevOps, DevOps-projektek az Azure és a Jenkins támogatásához. Ha azt szeretné, meglévő adatcsatornáinak testreszabásához, vagy egy másik fejlesztési és üzemeltetési eszközzel például CircleCI vagy TravisCI, megteheti a parancssori felület szolgáltatásait az IoT Edge fejlesztési eszközt.

További információkat, útmutatást és példákat tekintse meg a következő lapokon:
* [Folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge-ben](how-to-ci-cd.md)
* [A CI/CD-folyamat létrehozása az IoT Edge-hez az Azure DevOps Projects segítségével](how-to-devops-project.md)
* [Az Azure IoT Edge Jenkins beépülő modulja](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge fejlesztési és üzemeltetési GitHub-adattár](https://github.com/toolboc/IoTEdge-DevOps)
