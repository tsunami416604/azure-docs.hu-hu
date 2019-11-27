---
title: Az automatikus központi telepítés létrehozása a parancssorból – Azure IoT Edge |} A Microsoft Docs
description: Eszközök automatikus csoportok az IoT Edge üzemelő példány létrehozása az IoT-bővítmény, az Azure CLI használatával
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 68cc82733bb264eedb96239e7353ac30224bda64
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457397"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Üzembe helyezés és monitorozás az Azure CLI használatával nagy mennyiségű IoT Edge-modulok

Hozzon létre egy **IoT Edge automatikus központi telepítést** az Azure parancssori felület használatával, amellyel egyszerre több eszközön is kezelheti a folyamatban lévő központi telepítéseket. A IoT Edge automatikus központi telepítései a IoT Hub [automatikus Eszközkezelő](/azure/iot-hub/iot-hub-automatic-device-management) funkciójának részét képezik. A központi telepítések olyan dinamikus folyamatok, amelyek lehetővé teszik több modul üzembe helyezését több eszközön, nyomon követni a modulok állapotát és állapotát, és szükség esetén módosításokat hajthat végre. 

További információ: [IoT Edge automatikus központi telepítésének ismertetése egyetlen eszközön vagy nagy méretekben](module-deployment-monitoring.md).

Ebben a cikkben, állítsa be az Azure CLI és az IoT-bővítmény. Ezután megtudhatja, hogyan telepíthet modulokat IoT Edge eszközök készletére, és hogyan figyelheti meg a folyamatot az elérhető CLI-parancsokkal.

## <a name="cli-prerequisites"></a>Parancssori felület Előfeltételek

* Egy [IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében. 
* [IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) a telepített IoT Edge futtatókörnyezettel rendelkező eszközöket.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI 2.0.24-es verzióját kell vagy újabb. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* Az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. További információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).

Azure CLI-vel modulok üzembe helyezéséhez mentése .txt fájlként helyi manifest nasazení. A következő szakaszban a fájl elérési útját kell használnia, amikor a parancs futtatásával alkalmazza a konfigurációt az eszközre. 

Íme egy modult az alapszintű üzemelő példányhoz jegyzék példaként:

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

## <a name="identify-devices-using-tags"></a>Címkék használatával eszközök azonosítása

Központi telepítés létrehozásához, akkor megadhatja, mely eszközöket szeretné befolyásolni. A Azure IoT Edge a **címkével** rendelkező eszközöket azonosítja az eszköz Twin-ben. Minden eszköz több címkével is rendelkezhet, amelyeket bármilyen módon meghatározhat a megoldásához. Például ha Ön kezeli a telephelyi intelligens épületek, előfordulhat, hogy hozzá a következő címkék eszköz:

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

Manifest nasazení, valamint a többi paraméter áll egy központi telepítés létrehozása modulok üzembe a céleszközökre. 

Központi telepítés létrehozásához használja az az [IOT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) parancsot:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

A központi telepítési Create parancs a következő paramétereket veszi figyelembe: 

* **--Deployment-ID** – az IoT hub-ban létrehozandó központi telepítés neve. Adjon meg egy egyedi nevet, amely legfeljebb 128 kisbetűk használata a központi telepítés. Kerülje a szóközöket, és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
* **--hub-Name** -annak az IoT hub-nek a neve, amelyben a központi telepítés létrejön. A központ az aktuális előfizetésben kell lennie. Módosítsa a jelenlegi előfizetését az `az account set -s [subscription name]` paranccsal.
* **--Content** -filepath az üzembe helyezési jegyzékhez tartozó JSON-ra. 
* **--labels** – Címkék hozzáadása az üzemelő példányok nyomon követéséhez. Címkék olyan név, érték párok, melyek az üzemelő példány leírására. A címkék a nevek és az értékek JSON-formázását végzik. Például: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **– cél – feltétel** – adja meg a cél feltételt annak meghatározásához, hogy mely eszközök lesznek megcélozva a központi telepítéssel. A feltétel a Device Twin-címkék vagy az eszközök Twin jelentett tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumával. Például `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--priority** – pozitív egész szám. Abban az esetben, ha két vagy több üzemelő példány célzott ugyanarra az eszközre, az üzembe helyezés a legnagyobb numerikus értékkel prioritás érvényes lesz.

## <a name="monitor-a-deployment"></a>Egy központi telepítésének figyelése

Egy központi telepítés részleteinek megjelenítéséhez használja az az [IOT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) parancsot:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Az üzembe helyezési show parancs a következő paramétereket veszi figyelembe:
* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve.
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A központ az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`

Vizsgálja meg az üzembe helyezés a parancssori ablakba. A **metrikák** tulajdonság felsorolja az egyes hubok által kiértékelt metrikák darabszámát:

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
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A központ az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Amikor módosít egy központi telepítést, a módosítások azonnal replikálja az összes megcélzott eszközre. 

A célfeltétel frissít, ha elő a következő frissítéseket:

* Ha egy eszköz nem felelt meg a régi célfeltétel, de az új célfeltétel megfelel, és a központi telepítés rendszer a legmagasabb prioritású az eszközön, a központi telepítéshez az eszköz van alkalmazva. 
* Ha már nem a központi telepítés aktuálisan futó eszköz megfelel-e a célfeltétel, eltávolítja a központi telepítés és veszi fel a következő legmagasabb prioritású üzembe helyezés. 
* Ha már nem a központi telepítés aktuálisan futó eszköz megfelel-e a célként megadott feltétel, és nem felel meg a célfeltétel más központi telepítések, majd nincs változás történik az eszközön. Az eszköz addig a jelenlegi modulok fut, a jelenlegi állapotuk, de nem felügyelt már a központi telepítés részeként. Megfelel a célfeltétel, bármely más konfigurációért, miután eltávolítja a központi telepítés, és az új kiszolgálón vesz igénybe. 

A központi telepítés frissítéséhez használja az az [IOT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) parancsot:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

A központi telepítés frissítése parancs a következő paramétereket veszi figyelembe:
* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve.
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A központ az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`
* **--set** -tulajdonság frissítése a központi telepítésben. Az alábbi tulajdonságok frissíthetők:
  * targetCondition – például `targetCondition=tags.location.state='Oregon'`
  * címkék 
  * priority


## <a name="delete-a-deployment"></a>Üzemelő példányának törlése

Ha töröl egy központi telepítést, a következő legmagasabb prioritású üzembe helyezés az egyik eszközön sem igénybe vehet. Ha az eszközök nem felelnek meg a célfeltétel, bármely más konfigurációért, majd a modulok nem lesznek eltávolítva az üzemelő példány törlése. 

A központi telepítés törléséhez használja az az [IOT Edge Deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) parancsot:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

Az üzembe helyezés törlése parancs a következő paramétereket veszi figyelembe: 
* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve.
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A központ az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`

## <a name="next-steps"></a>További lépések

További információ a [modulok IoT Edge eszközökön való telepítéséről](module-deployment-monitoring.md).
