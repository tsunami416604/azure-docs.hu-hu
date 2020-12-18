---
title: IoT-Plug and Play-híd létrehozása, üzembe helyezése és kiterjesztése | Microsoft Docs
description: Azonosítsa a IoT Plug and Play híd összetevőit. Megtudhatja, hogyan bővítheti a hidat, és hogyan futtathatja IoT-eszközökön, átjárón és IoT Edge modulként.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ece9f62e64eb64b1f34af46b42d57ec583f8f214
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97675873"
---
# <a name="build-deploy-and-extend-the-iot-plug-and-play-bridge"></a>A IoT-Plug and Play-híd létrehozása, üzembe helyezése és kiterjesztése

A IoT Plug and Play-híd lehetővé teszi az átjáróhoz csatlakoztatott meglévő eszközök csatlakoztatását az IoT hubhoz. A Bridge használatával képezhető le a csatlakoztatott eszközökre a IoT Plug and Play-adapterek. A IoT Plug and Play felület határozza meg az eszköz által küldött telemetria, az eszköz és a felhő között szinkronizált tulajdonságokat, valamint azokat a parancsokat, amelyekre az eszköz válaszol. A nyílt forráskódú híd alkalmazást Windows-vagy Linux-átjárón is telepítheti és konfigurálhatja.

Ez a cikk részletesen ismerteti a következőket:

- Egy híd konfigurálása.
- A hidakat új adapterek létrehozásával bővítheti.
- A híd létrehozása és futtatása különböző környezetekben.

A híd használatát bemutató egyszerű példát a következő témakörben talál: [Hogyan csatlakoztatható a IoT Plug and Play Bridge-minta, amely Linux vagy Windows rendszeren fut a IoT hub](howto-use-iot-pnp-bridge.md).

A cikk útmutatásai és mintái az [Azure digitális Twins](../digital-twins/overview.md) és a [IoT Plug and Play](overview-iot-plug-and-play.md)alapvető ismeretét feltételezik.

## <a name="general-prerequisites"></a>Általános előfeltételek

### <a name="supported-os-platforms"></a>Támogatott operációsrendszer-platformok

A következő operációsrendszer-platformok és-verziók támogatottak:

|Platform  |Támogatott verziók  |
|---------|---------|
|Windows 10 |     Minden Windows SKU támogatott. Például: IoT Enterprise, Server, Desktop, IoT Core. *A kamera állapotának figyelése funkció esetén a 20H1 vagy a későbbi Build használata javasolt. Minden egyéb funkció elérhető minden Windows 10-es builden.*  |
|Linux     |Az Ubuntu 18,04-on tesztelt és támogatott, más disztribúciók funkciói nem lettek tesztelve.         |
||

### <a name="hardware"></a>Hardver

- Minden olyan hardveres platform, amely támogatja a fenti operációs rendszerbeli SKU-ket és verziókat.
- A soros, az USB-, a Bluetooth-és a kamera-perifériák és-érzékelők natív módon támogatottak. A IoT Plug and Play híd kiterjeszthető az egyéni perifériák vagy érzékelők támogatására.

### <a name="azure-iot-products-and-tools"></a>Azure IoT-termékek és-eszközök

- **Azure IoT hub** – az eszköz csatlakoztatásához az Azure-előfizetéshez egy [Azure IoT hub](../iot-hub/index.yml) szükséges. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt. Ha nem rendelkezik IoT-hubhoz, [kövesse az alábbi utasításokat, és hozzon létre egyet](../iot-hub/iot-hub-create-using-cli.md). Az alapszintű IoT-hubok nem tartalmazzák a IoT Plug and Play támogatását.
- **Azure IoT Explorer** – a IoT Plug and Play eszközével való interakcióhoz használhatja az Azure IoT Explorer eszközét. [Töltse le és telepítse az Azure IoT Explorer legújabb kiadását](./howto-use-iot-explorer.md) az operációs rendszeréhez. Konfigurálja az Azure IoT Explorer eszközt az IoT hubhoz való kapcsolódáshoz, és keresse meg a *pnpbridge\docs\schemas* mappában található modell-definíciókat a klónozott **IoT-Plug-and-Play-Bridge** használatával.

## <a name="configure-a-bridge"></a>Híd konfigurálása

A híd két módon konfigurálható:

- Ha a híd natív módon fut egy IoT-eszközön vagy átjárón, használja a konfigurációs fájlt. Ez a forgatókönyv Linux vagy Windows rendszerű gépet is használhat.
- Ha a híd IoT Edge modulként fut a IoT Edge futtatókörnyezetben, használja a modul Twin kívánt tulajdonságát. Ez a forgatókönyv azt feltételezi, hogy a IoT Edge futtatókörnyezet Linux-környezetben van tárolva.

### <a name="configuration-file"></a>Konfigurációs fájl

Ha Plug and Play híd natív módon fut egy IoT-eszközön vagy átjárón, akkor a következő konfigurációs fájlt használja:

- Szerezze be a IoT Central vagy IoT Hub a kapcsolatok adatait.
- Konfigurálja azokat az eszközöket, amelyeken a IoT Plug and Play felületek közzé vannak téve.

A következő lehetőségek egyikével adja meg a konfigurációs fájlt a hídhoz:

- Adja át a konfigurációs fájl elérési útját parancssori paraméterként a híd végrehajtható fájljához.
- Használja a *pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* mappában található fájl meglévő *config.js* .

A [konfigurációs fájl sémája](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) elérhető a GitHub-tárházban.

> [!TIP]
> Ha a VS Code-ban szerkeszt egy híd-konfigurációs fájlt, akkor ezt a sémafájl segítségével ellenőrizheti.

### <a name="iot-edge-module-configuration"></a>IoT Edge modul konfigurálása

Ha a híd IoT Edge modulként fut egy IoT Edge futtatókörnyezetben, a konfigurációs fájl a felhőből lesz frissítve a `PnpBridgeConfig` kívánt tulajdonságra. A híd megvárja a tulajdonság frissítését, mielőtt konfigurálja az adaptereket és összetevőket.

## <a name="extend-the-bridge"></a>A híd kiterjesztése

A híd képességeinek kibővítéséhez saját híd-adaptereket hozhat létre.

A híd adaptereket használ a következőhöz:

- Kapcsolat létesítése egy eszköz és a felhő között.
- Az adatáramlás engedélyezése egy eszköz és a felhő között.
- Eszközkezelés engedélyezése a felhőből.

Minden híd-adapternek a következőket kell tennie:

- Hozzon létre egy digitális Twins-felületet.
- A felület használatával a felhőalapú funkciókhoz, például a telemetria, a tulajdonságokhoz és a parancsokhoz köthető az eszközökön.
- Vezérlő-és adatkommunikációt hozhat létre az eszköz hardverével vagy belső vezérlőprogram szolgáltatásával.

Az egyes hidak-adapterek egy adott típusú eszközzel működnek, attól függően, hogy az adapter hogyan kapcsolódik az eszközhöz, és hogyan kommunikál az eszközzel. Akkor is, ha az eszközzel folytatott kommunikáció kézfogási protokollt használ, a Bridge-adapterek több módon is rendelkezhetnek az eszközről származó adatok értelmezéséhez. Ebben az esetben a Bridge-adapter a konfigurációs fájlban található adapter információit használja az *interfész konfigurációjának* meghatározásához, amelyet az adapternek használnia kell az adatok elemzéséhez.

Az eszközzel folytatott kommunikációhoz a Bridge-adapter egy kommunikációs protokollt használ, amelyet a mögöttes operációs rendszer vagy az eszköz gyártója biztosít az eszköz és az API-k.

A felhővel való interakcióhoz a Bridge-adapter az Azure IoT Device C SDK által biztosított API-kat használja a telemetria küldéséhez, a digitális dupla felületek létrehozásához, a tulajdonságok küldéséhez, valamint a tulajdonságok frissítéseihez és parancsaihoz tartozó visszahívási függvények létrehozásához.

### <a name="create-a-bridge-adapter"></a>Híd-adapter létrehozása

A híd a [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) felületen definiált API-k megvalósításához egy híd adaptert vár:

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

Ebben az illesztőfelületben:

- `PNPBRIDGE_ADAPTER_CREATE` létrehozza az adaptert, és beállítja az illesztőfelület-felügyeleti erőforrásokat. Az adapterek globális adapter-paraméterekkel is hivatkozhatnak az adapterek létrehozásához. Ezt a függvényt egyszer kell meghívni egyetlen adapterhez.
- `PNPBRIDGE_COMPONENT_CREATE` létrehozza a digitális Twin-ügyfél felületeit, és összekapcsolja a visszahívási funkciókat. Az adapter kezdeményezi a kommunikációs csatornát az eszközre. Az adapter beállíthatja az erőforrásokat, hogy engedélyezze a telemetria folyamatot, de a telemetria csak a `PNPBRIDGE_COMPONENT_START` hívása után induljon el. Ezt a függvényt egyszer kell meghívni a konfigurációs fájl minden egyes illesztőfelület-összetevőjénél.
- `PNPBRIDGE_COMPONENT_START` a rendszer úgy hívja, hogy a Bridge-adapter megkezdje a telemetria továbbítását az eszközről a digitális kettős ügyfél felé. Ezt a függvényt egyszer kell meghívni a konfigurációs fájl minden egyes illesztőfelület-összetevőjénél.
- `PNPBRIDGE_COMPONENT_STOP` leállítja a telemetria folyamatot.
- `PNPBRIDGE_COMPONENT_DESTROY` megsemmisíti a digitális kettős ügyfelet és a hozzá tartozó felületi erőforrásokat. Ezt a függvényt egyszer kell meghívni a konfigurációs fájl minden egyes illesztőfelület-összetevőjénél, ha a híd le van szakítva, vagy végzetes hiba történik.
- `PNPBRIDGE_ADAPTER_DESTROY` a Bridge-adapter erőforrásainak tisztítása.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Bridge Core interakció a Bridge-adapterekkel

A következő lista ismerteti, hogy mi történik a híd indításakor:

1. A híd indításakor a Bridge adapter-kezelő a konfigurációs fájlban definiált összes illesztőfelület-összetevőn áttekinti a megfelelő adaptert, és meghívja azokat `PNPBRIDGE_ADAPTER_CREATE` . Az adapter a globális adapterek konfigurációs paramétereit használva állíthatja be az erőforrásokat a különböző *illesztőfelület-konfigurációk* támogatásához.
1. A Bridge Manager a megfelelő Bridge-adapter hívásával kezdeményezi a kezelőfelület létrehozását a konfigurációs fájlban lévő minden eszközön `PNPBRIDGE_COMPONENT_CREATE` .
1. Az adapter megkapja a csatoló összetevőhöz tartozó opcionális adapterek konfigurációs beállításait, és ezeket az információkat használja az eszközhöz való csatlakozás beállításához.
1. Az adapter létrehozza a digitális Twin-ügyfél felületeit, és összekapcsolja a visszahívási függvényeket a tulajdonságok frissítéseihez és parancsaihoz. Az eszközök kapcsolatainak létrehozása nem blokkolja a visszahívások visszaküldését, miután a digitális Twin Interface létrehozása sikeresen megtörtént. Az aktív eszköz kapcsolata független a híd által létrehozott aktív felületi ügyféllel. Ha egy kapcsolat meghiúsul, az adapter feltételezi, hogy az eszköz inaktív. A híd adapter dönthet úgy, hogy újrapróbálkozik ezzel a kapcsolattal.
1. Miután a Bridge adapter-kezelő a konfigurációs fájlban megadott összes illesztőfelület-összetevőt létrehozta, regisztrálja az összes felületet az Azure IoT Hub. A regisztráció blokkoló, aszinkron hívás. A hívás befejezésekor elindítja a visszahívást a Bridge-adapteren, amely elkezdheti a felhőből a tulajdonságok és a parancsok visszahívásának kezelését.
1. A Bridge adapter-kezelő ezután meghívja az `PNPBRIDGE_INTERFACE_START` egyes összetevőket, és a Bridge-adapter elindítja a digitális Twin-ügyfélnek a telemetria jelentését.

### <a name="design-guidelines"></a>Tervezési irányelvek

Új Bridge-adapter fejlesztésekor kövesse az alábbi irányelveket:

- Határozza meg, hogy mely eszközök támogatottak, és hogy az adaptert használó összetevők felületének definíciója hogyan néz ki.
- Határozza meg, hogy az adapternek milyen illesztőfelület-és globális paramétereket kell meghatároznia a konfigurációs fájlban.
- Azonosítsa az összetevő tulajdonságainak és parancsainak támogatásához szükséges alacsony szintű eszköz-kommunikációt.
- Határozza meg, hogy az adapternek hogyan kell elemezni a nyers adatait az eszközről, és át kell alakítania azokat a telemetria, amelyeket a IoT Plug and Play Interface definition határoz meg.
- Implementálja a korábban ismertetett híd-adapter felületet.
- Adja hozzá az új adaptert az adapter jegyzékfájlhoz, és hozza létre a hidat.

### <a name="enable-a-new-bridge-adapter"></a>Új híd adapter engedélyezése

Az adapterek a hídon való engedélyezéséhez adjon hozzá egy hivatkozást a [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> A Bridge-adapter visszahívásait egymás után hívja meg a rendszer. Az adapternek nem szabad letiltani a visszahívást, mert ez megakadályozza, hogy a híd magja előrehaladást érjen el.

### <a name="sample-camera-adapter"></a>Minta kamera adapter

A [kamera-adapter információi](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) az Ön által engedélyezhető minta kamera-adaptert ismertetik.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>A híd létrehozása és futtatása IoT-eszközön vagy átjárón

| Platform | Támogatott |
| :-----------: | :-----------: |
| Windows |  Yes |
| Linux | Yes |

### <a name="prerequisites"></a>Előfeltételek

A szakasz elvégzéséhez telepítenie kell a következő szoftvereket a helyi gépre:

- Olyan fejlesztési környezet, amely támogatja a C++ fordítását, például a [Visual studiót (közösségi, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/) – ügyeljen rá, hogy a NuGet csomagkezelő összetevőt és az **asztali fejlesztést c++ számítási feladatokkal** telepítse a Visual Studio telepítésekor.
- [CMAK](https://cmake.org/download/) – a CMAK telepítésekor válassza a **CMAK hozzáadása a rendszerútvonalhoz** lehetőséget.
- Ha Windows rendszert használ, le kell töltenie a [windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk)-t is.

### <a name="source-code"></a>Forráskód

A [IoT Plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) -tárház klónozása a helyi gépre:

```cmd/sh
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Várható, hogy az előző parancs futtatása több percet is igénybe vehet.

> [!NOTE]
> Ha problémákba ütközik a `git submodule update` Windows hibája miatt, próbálja meg a következő parancsot a probléma megoldásához: `git config --system core.longpaths true` .

### <a name="build-the-bridge-on-windows"></a>A híd létrehozása Windows rendszeren

Nyissa meg a **VS 2019 fejlesztői parancssort** , és navigáljon ahhoz a mappához, amely a klónozott tárházat tartalmazza, és futtassa a következő parancsokat:

```cmd
cd pnpbridge\scripts\windows

build.cmd
```

Várható, hogy az előző parancs futtatása több percet is igénybe vehet.

Szükség esetén megnyithatja a generált *pnpbridge\cmake\ pnpbridge_x86 \ azure_iot_pnp_bridge. SLN* Solution fájlt a Visual Studióban a kód szerkesztéséhez, újraépítéséhez és hibakereséséhez.

> [!TIP]
> Ez a projekt a CMAK használatával létrehozza a projektfájl-fájlokat. Előfordulhat, hogy a Visual Studióban a projektben végrehajtott módosítások elvesznek, ha a megfelelő CMAK-fájlok nem frissülnek.

### <a name="build-the-bridge-on-linux"></a>A híd létrehozása Linuxon

A bash rendszerhéj használatával navigáljon a klónozott tárházat tartalmazó mappához, és futtassa a következő parancsokat:

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>Az általános érzékelők konfigurálása

Módosítsa a következő paramétereket a `pnp_bridge_connection_parameters` csomópont alatt található *config.js* fájljában a *IOT-Plug-and-Play-bridge\pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* mappában, vagy a Windows vagy a *IOT-Plug-and-Play-bridge/pnpbridge/CMAK/pnpbridge_linux \ src/pnpbridge/Samples/Console* mappában a Windows rendszerben:

Ha kapcsolati karakterláncot használ az IoT hubhoz való kapcsolódáshoz:

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> Az `symmetric_key` értéknek meg kell egyeznie az SAS-kulccsal a kapcsolatok karakterláncában.

Ha a DPS használatával csatlakozik az IoT hubhoz vagy IoT Central alkalmazáshoz:

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

Tekintse át a konfigurációs fájl többi részét, és ellenőrizze, hogy melyik illesztőfelület-összetevők és globális paraméterek vannak konfigurálva ebben a példában.

### <a name="start-the-bridge-in-windows"></a>A Windows-híd elindítása

Indítsa el a hidat úgy, hogy a parancssorban futtatja:

```cmd
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> Ha más helyről szeretne konfigurációs fájlt használni, adja át az elérési utat parancssori paraméterként a híd futtatásakor.
  
> [!TIP]
> Ha beépített kamerával vagy USB-kamerával csatlakozik a számítógéphez, elindíthat egy kamerát használó alkalmazást, például a beépített **kamera** alkalmazást. Ha a **kamera** alkalmazás fut, a Bridge konzol kimenete megjeleníti a figyelési statisztikát és a képkockák sebességét a digitális Twin felületen keresztül az IoT hub számára.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>A híd létrehozása és futtatása IoT Edge modulként

| Platform | Támogatott |
| :-----------: | :-----------: |
| Windows |  No |
| Linux | Yes |

### <a name="prerequisites"></a>Előfeltételek

A szakasz elvégzéséhez szüksége lesz egy ingyenes vagy standard szintű Azure IoT hub-ra. Az IoT hub létrehozásával kapcsolatban lásd: [IoT hub létrehozása](../iot-hub/iot-hub-create-through-portal.md).

A jelen szakaszban ismertetett lépések feltételezik, hogy a következő fejlesztési környezettel rendelkezik egy Windows 10-es gépen. Ezek az eszközök lehetővé teszik IoT Edge modul felépítését és üzembe helyezését IoT Edge eszközön:

- A Linux rendszerhez készült Windows alrendszer (WSL), amely Ubuntu 18,04 LTS rendszert futtat. További információért lásd a Windows [10-es Linux-telepítési útmutatót](https://docs.microsoft.com/windows/wsl/install-win10)a Windows 10 rendszerhez című témakört.
- A Windowshoz készült Docker Desktop a WSL 2 használatára van konfigurálva. További információ: [Docker Desktop WSL 2 backend](https://docs.docker.com/docker-for-windows/wsl/).
- A Windows-környezetben a következő három bővítménnyel telepített [Visual Studio Code](https://code.visualstudio.com/docs/setup/windows) :

  - [Távoli fejlesztői bővítmény csomagja](https://aka.ms/vscode-remote/download/extension) – ez a bővítmény lehetővé teszi, hogy kódot hozzon létre és futtasson a 2. WSL.
  - [Docker a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) -hoz – ezt a bővítményt engedélyezni kell a vs Code **WSL: Ubuntu-18,04** környezetben.
  - A [Visual Studio Code-hoz készült Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) – ezt a bővítményt engedélyezni kell a vs Code **WSL: Ubuntu-18,04** környezetben.

- Futtassa a következő parancsokat a WSL 2 környezetben a szükséges build-eszközök telepítéséhez:

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- Az Azure [CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest&preserve-view=true) a WSL 2 környezetben van telepítve az Azure-erőforrások kezeléséhez.

  > [!TIP]
  > Ha szeretné, futtathatja a parancsokat azon `az` a [Azure Cloud Shellon](https://shell.azure.com/) , ahol a parancssori felület előre telepítve van.

### <a name="create-an-iot-edge-device"></a>IoT Edge-eszköz létrehozása

Az itt található parancsok egy Azure-beli virtuális gépen futó IoT Edge eszközt hoznak létre. Ha egy IoT Edge eszközt futtat egy virtuális gépen, akkor a telepítés teszteléséhez hasznos, ha nem fér hozzá valódi eszközhöz.

Ha IoT Edge eszköz regisztrációját szeretné létrehozni az IoT hub-ban, futtassa a következő parancsokat a WSL 2 környezetben. A `az login` parancs használatával jelentkezzen be az Azure-előfizetésbe:

```bash
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

A következő parancsokkal hozhat létre egy Azure-beli virtuális gépet, amelyen telepítve van a IoT Edge futtatókörnyezet. Módosítsa a helyőrzőket megfelelő értékekkel:

```bash
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

Most már fut a IoT Edge futtatókörnyezet egy virtuális gépen. A következő paranccsal ellenőrizheti, hogy a **$edgeAgent** és a **$edgeHub** fut-e az eszközön:

```bash
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> A virtuális gép futása után számlázunk. Ne felejtse el leállítani, ha nem használja, és nem távolítja el, ha elkészült.

### <a name="download-the-source-code"></a>Forráskód letöltése

A következő lépésekben létrehozza a Docker-rendszerképet a WSL 2 környezetben. A **IOT Plug and Play-Bridge** adattárának klónozásához futtassa a következő parancsokat egy megfelelő MAPPÁBAN található WSL 2 bash-rendszerhéjban:

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Várható, hogy az előző parancs futtatása több percet is igénybe vehet.

### <a name="update-the-dockerfile"></a>A *Docker* frissítése

Indítsa el a VS Code-ot, nyissa meg a parancssort, írja be a *távoli WSL: Nyissa meg a mappát a WSL-ben*, majd nyissa meg a klónozott tárházat tartalmazó *IOT-Plug-and-Play-Bridge* mappát.

Nyissa meg a *pnpbridge\Dockerfile.amd64* fájlt. Szerkessze a környezeti változók definícióit az alábbiak szerint:

```dockerfile
ENV IOTHUB_DEVICE_CONNECTION_STRING="{Add your device connection string here}"
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> Az alábbi paranccsal kérheti le az eszköz kapcsolódási karakterláncát: `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

Más architektúrák esetében minta- *Dockerfiles* vannak.

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>A IoT Plug and Play Bridge-modul rendszerképének összeállítása

A VS Code-ban nyissa meg a parancssort, és írja be a Docker-beállításjegyzékeket *: Jelentkezzen be a Docker CLI*-be, majd válassza ki az Azure-előfizetését és a tároló Ez a parancs lehetővé teszi a Docker számára a tároló-beállításjegyzékhez való kapcsolódást és a modul rendszerképének feltöltését.

Nyissa meg a *pnpbridge/module.js* fájlt. Adja hozzá `{your container registry name}.azurecr.io/iotpnpbridge` a tároló-rendszerkép tárházát, és `v1` a verziót.

A VS Code-ban kattintson a jobb gombbal a *pnpbridge/module.js* fájlra az **Intéző** nézetben, válassza a **IoT Edge modul létrehozása és leküldése** lehetőséget, és válassza az **amd64** lehetőséget platformként.

A VS Code-ban a **Docker** nézetben böngészheti a tároló beállításjegyzékének tartalmát, amely mostantól tartalmazza a **iotpnpbridge: v1-amd64** modul rendszerképét.

### <a name="create-a-container-registry"></a>Container Registry létrehozása

Egy IoT Edge eszköz letölti a modul lemezképeit egy tároló-beállításjegyzékből. Ez a példa egy Azure Container registryt használ.

Hozzon létre egy Azure Container registryt a **Bridge-Edge-Resources** erőforráscsoporthoz. Ezután engedélyezze a rendszergazdai hozzáférést a tároló-beállításjegyzékhez, és szerezze be azokat a hitelesítő adatokat, amelyeknek a IoT Edge eszköznek le kell töltenie a modul lemezképeit:

```bash
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>Az üzembe helyezési jegyzék létrehozása

Az IoT Edge telepítési jegyzék a IoT Edge eszközön telepítendő modulokat és konfigurációs értékeket határozza meg.

A VS Code-ban nyissa meg a *pnpbridge/deployment.template.jsfájlt a* következő fájlon:

- Frissítse a `registryCredentials` értéket az előző parancs értékeivel. Az `address` érték így néz ki `{your container registry name}.azurecr.io` .
- Frissítse a `image` értékét `ModulePnpBridge` . A modul képe a következőképpen néz ki: `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` .
- A `PnPBridgeConfig` CO2-észlelési adapter konfigurálásához cserélje le a-t a következő JSON-ra:

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  Mentse a módosításokat.

A VS Code-ban kattintson a jobb gombbal a *pnpbridge/deployment.template.js* fájlra az **Intéző** nézetben, válassza a **IoT Edge üzembe helyezési jegyzék előállítása** lehetőséget. Ez a parancs létrehozza a *pnpbridge/config/deployment.amd64.js* az üzembe helyezési jegyzékben.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>A híd üzembe helyezése a IoT Edge eszközön

A VS Code-ban nyissa meg a parancssort, írja be az *Azure IoT hub: válassza a IoT hub lehetőséget*, majd válassza ki az előfizetését és az IoT hubot.

A VS Code-ban kattintson a jobb gombbal a *pnpbridge/config/deployment.amd64.js* fájlra az **Explorer** nézetben, válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget, majd válassza a **Bridge-Edge-Device** elemet.

Az eszközön található modulok állapotának megtekintéséhez futtassa a következő parancsot:

```bash
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

A futó modulok listája mostantól tartalmazza azt a **ModulePnpBridge** -modult, amely a CO2-észlelési adapter használatára van konfigurálva.

### <a name="tidy-up"></a>Kitakarítás

A virtuális gép és a tároló beállításjegyzékének az Azure-előfizetésből való eltávolításához futtassa a következő parancsot:

```bash
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>Mappa szerkezete

*pnpbridge\deps\azure-IOT-SDK-c-PnP*: git almodulok, amelyek tartalmazzák a c SDK-hoz készült Azure IOT Device SDK-t.

*pnpbridge\scripts*: szkriptek létrehozása.

*pnpbridge\src*: forráskód a IoT Plug and Play Bridge Core-hoz.

*pnpbridge\src\adapters*: forráskód különböző IoT Plug and Play Bridge-adapterekhez.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a IoT Plug and Play-hídra, látogasson el a [IoT Plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub-tárházra.
