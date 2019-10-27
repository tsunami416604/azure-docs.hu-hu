---
title: Automatikus központi telepítések létrehozása a parancssorból – Azure IoT Edge | Microsoft Docs
description: Az Azure CLI-hez készült IoT-bővítmény használatával automatikus központi telepítéseket hozhat létre IoT Edge eszközök csoportjaihoz
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7ba38e1aa7196263b0ac64a6c92984cc3e7416a6
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964800"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>IoT Edge-modulok üzembe helyezése és figyelése az Azure CLI használatával

Hozzon létre egy **IoT Edge automatikus központi telepítést** az Azure parancssori felület használatával, amellyel egyszerre több eszközön is kezelheti a folyamatban lévő központi telepítéseket. A IoT Edge automatikus központi telepítései a IoT Hub [automatikus Eszközkezelő](/azure/iot-hub/iot-hub-automatic-device-management) funkciójának részét képezik. A központi telepítések olyan dinamikus folyamatok, amelyek lehetővé teszik több modul üzembe helyezését több eszközön, nyomon követni a modulok állapotát és állapotát, és szükség esetén módosításokat hajthat végre. 

További információ: [IoT Edge automatikus központi telepítésének ismertetése egyetlen eszközön vagy nagy méretekben](module-deployment-monitoring.md).

Ebben a cikkben az Azure CLI-t és a IoT-bővítményt kell beállítania. Ezután megtudhatja, hogyan telepíthet modulokat IoT Edge eszközök készletére, és hogyan figyelheti meg a folyamatot az elérhető CLI-parancsokkal.

## <a name="cli-prerequisites"></a>A CLI előfeltételei

* Egy [IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében. 
* [IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) a telepített IoT Edge futtatókörnyezettel rendelkező eszközöket.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI-verziójának 2.0.24 vagy újabbnak kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* Az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan zajlik az adatforgalom a modulok és a modul kívánt tulajdonságai között. További információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).

A modulok Azure CLI használatával történő üzembe helyezéséhez mentse a központi telepítési jegyzéket. txt fájlként. A következő szakaszban a fájl elérési útját kell használnia, amikor a parancs futtatásával alkalmazza a konfigurációt az eszközre. 

Íme egy alapszintű üzembe helyezési jegyzék egy modullal, például:

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
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
              }
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
                "createOptions": "{}"
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
            "route": "FROM /* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {}
      }
    }
  }
}
```

## <a name="identify-devices-using-tags"></a>Címkék használatával azonosíthatja az eszközöket

A központi telepítés létrehozása előtt meg kell határozni, hogy mely eszközöket kívánja érinteni. A Azure IoT Edge a **címkével** rendelkező eszközöket azonosítja az eszköz Twin-ben. Minden eszköz több címkével is rendelkezhet, amelyeket bármilyen módon meghatározhat a megoldásához. Ha például egy intelligens épületből álló campusot kezel, a következő címkéket adhatja hozzá egy eszközhöz:

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

További információ az eszközök ikrekről és címkékről: [az eszközök összevetése és használata az IoT Hubban](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

A eszközöket üzembe helyezheti a céleszköz olyan központi telepítés létrehozásával, amely az üzembe helyezési jegyzékből és egyéb paraméterekből áll. 

Központi telepítés létrehozásához használja az az [IOT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) parancsot:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

A központi telepítési Create parancs a következő paramétereket veszi figyelembe: 

* **--Deployment-ID** – az IoT hub-ban létrehozandó központi telepítés neve. Adja meg az üzembe helyezést egy egyedi névvel, amely akár 128 kisbetűt is tartalmazhat. Kerülje a szóközöket, és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
* **--hub-Name** -annak az IoT hub-nek a neve, amelyben a központi telepítés létrejön. A hubhoz a jelenlegi előfizetésben kell lennie. Módosítsa a jelenlegi előfizetését az `az account set -s [subscription name]` paranccsal.
* **--Content** -filepath az üzembe helyezési jegyzékhez tartozó JSON-ra. 
* **--labels** – Címkék hozzáadása az üzemelő példányok nyomon követéséhez. A címkék név, érték párok, amelyek leírják az üzemelő példányt. A címkék a nevek és az értékek JSON-formázását végzik. Például: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **– cél – feltétel** – adja meg a cél feltételt annak meghatározásához, hogy mely eszközök lesznek megcélozva a központi telepítéssel. A feltétel a Device Twin-címkék vagy az eszközök Twin jelentett tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumával. Például `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--priority** – pozitív egész szám. Abban az esetben, ha két vagy több üzemelő példány ugyanarra az eszközre irányul, a prioritáshoz tartozó legmagasabb numerikus értékkel rendelkező üzemelő példány érvényes lesz.

## <a name="monitor-a-deployment"></a>Központi telepítés figyelése

Egy központi telepítés részleteinek megjelenítéséhez használja az az [IOT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) parancsot:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Az üzembe helyezési show parancs a következő paramétereket veszi figyelembe:
* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve.
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A hubhoz a jelenlegi előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`

Vizsgálja meg a központi telepítést a parancsablakban. A **metrikák** tulajdonság felsorolja az egyes hubok által kiértékelt metrikák darabszámát:

* **targetedCount** – a rendszer mérőszáma, amely meghatározza, hogy hány eszköz található az IoT Hubban, amely megfelel a célcsoport-kezelési feltételnek.
* **appliedCount** – a rendszermetrika határozza meg, hogy hány eszközön lett alkalmazva a központi telepítési tartalom az IoT hub-ben a modulba tartozó ikrekre.
* **reportedSuccessfulCount** – az eszköz metrikája, amely meghatározza, hogy hány IoT Edge eszköz szerepel a központi telepítési jelentéskészítés sikerességében a IoT Edge ügyfél futtatókörnyezetében.
* **reportedFailedCount** – az eszköz metrikája, amely meghatározza, hogy hány IoT Edge eszköz szerepel a központi telepítés jelentéskészítési hibájában a IoT Edge ügyfél futtatókörnyezetből.

Az egyes mérőszámokhoz tartozó eszköz-azonosítók vagy objektumok listáját az az [IOT Edge Deployment show-metrika](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) paranccsal lehet megjeleníteni:

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

Az üzembe helyezés megjelenítése-metrika parancs a következő paramétereket veszi figyelembe: 
* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve.
* **--metrika-azonosító** – annak a metrikának a neve, amelyre vonatkozóan meg szeretné jeleníteni az eszközök azonosítóinak listáját, például `reportedFailedCount`
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A hubhoz a jelenlegi előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Amikor módosít egy központi telepítést, a módosítások azonnal replikálódnak az összes megadott eszközre. 

Ha frissíti a célként megadott feltételt, a következő frissítések történnek:

* Ha egy eszköz nem felelt meg a régi célként megadott feltételnek, de megfelel az új célként megadott feltételnek, és ez a központi telepítés az eszköz legmagasabb prioritása, akkor ez a központi telepítés az eszközre lesz alkalmazva. 
* Ha a központi telepítést jelenleg futtató eszköz már nem felel meg a célként megadott feltételnek, akkor eltávolítja ezt a központi telepítést, és a következő legmagasabb prioritású üzemelő példányra kerül. 
* Ha a központi telepítést jelenleg futtató eszköz már nem felel meg a célként megadott feltételnek, és nem felel meg a többi üzemelő példány céljának, akkor az eszközön nem történik változás. Az eszköz továbbra is a jelenlegi állapotukban futtatja az aktuális modulokat, de az üzembe helyezés részeként már nem felügyelhető. Ha teljesíti az egyéb üzemelő példányok célját, eltávolítja ezt az üzembe helyezést, és az újat veszi. 

A központi telepítés frissítéséhez használja az az [IOT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) parancsot:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

A központi telepítés frissítése parancs a következő paramétereket veszi figyelembe:
* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve.
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A hubhoz a jelenlegi előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`
* **--set** -tulajdonság frissítése a központi telepítésben. A következő tulajdonságokat frissítheti:
  * targetCondition – például `targetCondition=tags.location.state='Oregon'`
  * Címkék 
  * prioritású


## <a name="delete-a-deployment"></a>Központi telepítés törlése

Ha töröl egy központi telepítést, minden eszköz a következő legmagasabb prioritású központi telepítésre kerül. Ha az eszközök nem felelnek meg az egyéb üzemelő példányok céljának, akkor a rendszer nem távolítja el a modulokat a központi telepítés törlésekor. 

A központi telepítés törléséhez használja az az [IOT Edge Deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) parancsot:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

Az üzembe helyezés törlése parancs a következő paramétereket veszi figyelembe: 
* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve.
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A hubhoz a jelenlegi előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`

## <a name="next-steps"></a>Következő lépések

További információ a [modulok IoT Edge eszközökön való telepítéséről](module-deployment-monitoring.md).
