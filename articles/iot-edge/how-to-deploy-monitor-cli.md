---
title: Modulok üzembe helyezése az Azure CLI használatával – Azure IoT Edge
description: Az Azure CLI IoT-bővítményének használatával automatikus üzembe helyezéseket hozhat létre az IoT Edge-eszközök csoportjai számára
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9152b38a0155b610f39f7de239bcc377ad96be5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271472"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Az IoT Edge-modulok üzembe helyezése és figyelése az Azure CLI használatával

Hozzon létre egy **IoT Edge automatikus központi telepítést** az Azure parancssori felülethasználatával, hogy egyszerre több eszközön is kezelje a folyamatban lévő központi telepítéseket. Az IoT Edge automatikus üzembe helyezései az IoT Hub [automatikus eszközfelügyeleti](/azure/iot-hub/iot-hub-automatic-device-management) szolgáltatásának részét képezik. A központi telepítések olyan dinamikus folyamatok, amelyek lehetővé teszik több modul több eszközre történő üzembe helyezését, a modulok állapotának és állapotának nyomon követését, és szükség esetén a módosításokat.

További információ: [Az IoT Edge automatikus központi telepítései egyetlen eszközökön vagy nagy méretekben](module-deployment-monitoring.md)című témakörben található.

Ebben a cikkben az Azure CLI és az IoT-bővítmény beállítása. Ezután megtudhatja, hogyan telepítheti a modulokat az IoT Edge-eszközök készletére, és figyelheti a folyamatot a rendelkezésre álló CLI-parancsok használatával.

## <a name="cli-prerequisites"></a>CLI előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésben.
* [IoT Edge-eszközök](how-to-register-device.md#prerequisites-for-the-azure-cli) az IoT Edge futásidejű telepítve.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Az Azure CLI-verziónak legalább 2.0.70-nek kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* Az [Azure CLI IoT-bővítménye.](https://github.com/Azure/azure-iot-cli-extension)

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzékfájl konfigurálása

A központi telepítési jegyzékfájl egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan adatfolyamok a modulok között, és a modul twins kívánt tulajdonságait. További [információ: Ismerje meg, hogyan telepítheti a modulokat, és hogyan hozhat létre útvonalakat az IoT Edge-ben.](module-composition.md)

A modulok üzembe helyezéséhez az Azure CLI használatával mentse a központi telepítési jegyzékfájlt helyileg .txt fájlként. A következő szakaszban lévő fájlelérési utat használja, amikor a parancs futtatásával alkalmazza a konfigurációt az eszközre.

Íme egy egyszerű központi telepítési jegyzékfájl egy modullal példaként:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="layered-deployment"></a>Réteges telepítés

A réteges központi telepítések az automatikus központi telepítés olyan típusai, amelyek egymásra halmozhatók. A réteges központi telepítésekről az [IoT Edge automatikus központi telepítéseinek ismertetése egyetlen eszközökön vagy nagy méretekben](module-deployment-monitoring.md)című témakörben talál.

Réteges központi telepítések hozhatók létre és kezelhetők az Azure CLI, mint bármely automatikus központi telepítés, csak néhány különbség. A réteges központi telepítés létrehozása után ugyanaz az Azure CLI dolgozik a réteges központi telepítések ugyanaz, mint bármely központi telepítés. Réteges központi telepítés létrehozásához `--layered` adja hozzá a jelzőt a létrehozás iparancshoz.

A második különbség a telepítési jegyzék kialakításában van. Míg a szabványos automatikus központi telepítésnek a felhasználói modulok mellett a rendszer futásidejű moduljait is tartalmaznia kell, a réteges központi telepítések csak felhasználói modulokat tartalmazhatnak. Ehelyett a réteges központi telepítések kell egy szabványos automatikus központi telepítés egy eszközön is, a szükséges összetevők minden IoT Edge-eszköz, például a rendszer futásidejű modulok.

Íme egy egyszerű réteges telepítési jegyzékfájl egy modullal példaként:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Az előző példa egy réteges `properties.desired` központi telepítési beállítást mutatott be egy modulhoz. Ha ez a réteges központi telepítés olyan eszközt céloz meg, ahol ugyanazt a modult már alkalmazták, felülírja a meglévő kívánt tulajdonságokat. A kívánt tulajdonságok felülírása helyett új alszakaszt is definiálhat. Példa:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

A modulikák réteges központi telepítésekben történő konfigurálásáról további információt a [Réteges telepítés című](module-deployment-monitoring.md#layered-deployment) témakörben talál.

## <a name="identify-devices-using-tags"></a>Eszközök azonosítása címkék használatával

A központi telepítés létrehozása előtt meg kell adnia, hogy mely eszközöket szeretné befolyásolni. Az Azure IoT Edge azonosítja az eszközöket az **ikereszköz-címkék** használatával. Minden eszköz több címkével is rendelkezhet, amelyeket bármilyen módon meghatároz, amely a megoldásnak van értelme. Ha például intelligens épületek kampuszát kezeli, a következő címkéket veheti fel egy eszközre:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Az ikereszközök és -címkék ről az [Eszközök ikreinek megértése és használata az IoT Hubban](../iot-hub/iot-hub-devguide-device-twins.md)című témakörben talál további információt.

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

A központi telepítési jegyzékből és más paraméterekből álló központi telepítés létrehozásával telepíti a modulokat a céleszközökre.

Az [az iot edge deployment create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) parancsot használja a központi telepítés létrehozásához:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Használja ugyanazt a `--layered` parancsot a jelzővel, hogy hozzon létre egy réteges deploymet.

A központi telepítés létrehozása parancs a következő paramétereket veszi igénybe:

* **--layered** - Egy választható jelző, amely a központi telepítésrétegként azonosítására szolgál.
* **--deployment-id** - Az IoT hubban létrehozandó központi telepítés neve. Adjon a központi telepítésegyedi nevet, amely legfeljebb 128 kisbetűs. Kerülje a szóközöket `& ^ [ ] { } \ | " < > /`és a következő érvénytelen karaktereket: . Szükséges paraméter.
* **--content** - Filepath a telepítési jegyzékJSON. Szükséges paraméter.
* **--hub-name** - Annak az IoT hubnak a neve, amelyben a központi telepítés létre jön. A hubnak az aktuális előfizetésben kell lennie. Módosítsa az aktuális `az account set -s [subscription name]` előfizetést a paranccsal.
* **--címkék** - Címkék hozzáadása az üzemelő példányok nyomon követéséhez. A címkék a név, értékpárok, amelyek leírják a központi telepítést. A címkék JSON-formázást tartalmaznak a nevekhez és értékekhez. Például: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - Adja meg a célfeltételt annak meghatározásához, hogy mely eszközök lesznek megcélozva ezzel a központi telepítéssel.A feltétel az ikercímkéken vagy az ikereszköz jelentett tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumának.Például: `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** - Pozitív egész szám. Abban az esetben, ha két vagy több központi telepítés ugyanarra az eszközre irányul, a legmagasabb numerikus értékkel rendelkező központi telepítés lesz érvényben.
* **--metrics** - Hozzon létre olyan metrikákat, amelyek lekérdezik az edgeHub jelentett tulajdonságait egy központi telepítés állapotának nyomon követéséhez. A metrikák JSON-bemenetet vagy egy fájlelérési utat vesznek igénybe. Például: `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Központi telepítés figyelése

Az [az iot edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) parancs segítségével egyetlen központi telepítés részleteit jelenítheti meg:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

A központi telepítési show parancs a következő paramétereket veszi:

* **--deployment-id** - Az IoT hubban található központi telepítés neve. Szükséges paraméter.
* **--hub-name** - Annak az IoT hubnak a neve, amelyben a központi telepítés létezik. A hubnak az aktuális előfizetésben kell lennie. Váltás a kívánt előfizetésre a paranccsal`az account set -s [subscription name]`

Vizsgálja meg a központi telepítést a parancsablakban.A **metrikák** tulajdonság felsorolja az egyes csomópontok által kiértékelt metrikák számát:

* **targetedCount** – A rendszer metrika, amely meghatározza az eszközök twins az IoT Hub, amelyek megfelelnek a célzási feltételnek.
* **appliedCount** – A rendszermetrika adja meg, hogy hány eszközök, amelyek a központi telepítési tartalom az IoT Hub ban a modul twins alkalmazott eszközök száma.
* **reportedSuccessfulCount** – Egy eszközmetrika, amely megadja az IoT Edge-eszközök számát az IoT Edge-ügyfél futásidejű központi telepítési jelentési sikerességében.
* **reportedFailedCount** – Egy eszközmetrika, amely megadja az IoT Edge-eszközök számát az IoT Edge-ügyfél futásidejű üzembe helyezési jelentési hiba.

Az [az iot edge deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) paranccsal megjelenítheti az egyes metrikák eszközazonosítóinak vagy objektumainak listáját:

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

A telepítési show-metric parancs a következő paramétereket veszi fel:

* **--deployment-id** - Az IoT hubban található központi telepítés neve.
* **--metric-id** - Annak a metrikának a neve, amelynek az `reportedFailedCount`eszközazonosítóinak listáját meg szeretné tekinteni, például .
* **--hub-name** - Annak az IoT hubnak a neve, amelyben a központi telepítés létezik. A hubnak az aktuális előfizetésben kell lennie. Váltson a kívánt `az account set -s [subscription name]`előfizetésre a paranccsal.

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Központi telepítés módosításakor a módosítások azonnal replikálódnak az összes célzott eszközre.

Ha frissíti a célfeltételt, a következő frissítések jelennek meg:

* Ha egy eszköz nem felel meg a régi célfeltételnek, de megfelel az új célfeltételnek, és ez a központi telepítés az adott eszköz legmagasabb prioritása, akkor ez a központi telepítés az eszközre lesz alkalmazva.
* Ha egy olyan eszköz, amely jelenleg futtatja ezt a központi telepítést, már nem felel meg a célfeltételnek, eltávolítja ezt a központi telepítést, és a következő legmagasabb prioritású központi telepítést veszi igénybe.
* Ha egy olyan eszköz, amely jelenleg futtatja ezt a központi telepítést, már nem felel meg a célfeltételnek, és nem felel meg más központi telepítések célfeltételének, akkor nincs változás az eszközön. Az eszköz továbbra is fut a jelenlegi modulok a jelenlegi állapotban, de nem kezeli kebben a központi telepítés ben már nem kezelik. Miután megfelel a célfeltétel bármely más központi telepítés, eltávolítja ezt a központi telepítést, és veszi át az újat.

Nem frissítheti a központi telepítés tartalmát, amely tartalmazza a központi telepítési jegyzékben definiált modulokat és útvonalakat. Ha frissíteni szeretné egy központi telepítés tartalmát, akkor hozzon létre egy új központi telepítést, amely ugyanazokat az eszközöket célozza magasabb prioritással. Egy meglévő modul bizonyos tulajdonságai, beleértve a célfeltételt, a címkéket, a metrikákat és a prioritást.

A központi telepítés frissítéséhez használja az [az iot peremhálózati telepítési frissítési](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) parancsot:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

A központi telepítési frissítés parancs a következő paramétereket veszi igénybe:

* **--deployment-id** - Az IoT hubban található központi telepítés neve.
* **--hub-name** - Annak az IoT hubnak a neve, amelyben a központi telepítés létezik. A hubnak az aktuális előfizetésben kell lennie. Váltás a kívánt előfizetésre a paranccsal`az account set -s [subscription name]`
* **--set** - A központi telepítés egy tulajdonságának frissítése. A következő tulajdonságokat frissítheti:
  * targetCondition - például`targetCondition=tags.location.state='Oregon'`
  * Címkék
  * Prioritás
* **--add** - Új tulajdonság hozzáadása a központi telepítéshez, beleértve a célfeltételeket vagy címkéket.
* **--remove** - Meglévő tulajdonság eltávolítása, beleértve a célfeltételeket vagy címkéket.

## <a name="delete-a-deployment"></a>Központi telepítés törlése

Központi telepítés törlésekor minden eszköz a következő legmagasabb prioritású központi telepítést veszi igénybe. Ha az eszközök nem felelnek meg a célfeltétel bármely más központi telepítés, majd a modulok nem törlődnek, amikor a központi telepítés törlődik.

A központi telepítés törléséhez használja az [az iot edge deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) parancsot:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

A központi telepítés törlése parancs a következő paramétereket veszi igénybe:

* **--deployment-id** - Az IoT hubban található központi telepítés neve.
* **--hub-name** - Annak az IoT hubnak a neve, amelyben a központi telepítés létezik. A hubnak az aktuális előfizetésben kell lennie. Váltás a kívánt előfizetésre a paranccsal`az account set -s [subscription name]`

## <a name="next-steps"></a>További lépések

További információ a [modulok IoT Edge-eszközökre való üzembe helyezéséről.](module-deployment-monitoring.md)
