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
ms.custom: seodec18
ms.openlocfilehash: 39edbc77215d3a4f6477beae3be9d7d47cbba4f0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540922"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Üzembe helyezés és monitorozás az Azure CLI használatával nagy mennyiségű IoT Edge-modulok

Hozzon létre egy **IoT Edge automatikus központi telepítési** az Azure parancssori felületének használatával sok eszköz a folyamatban lévő központi telepítések felügyeletéhez szükséges egyszerre. Az IoT Edge-hez az automatikus központi telepítések részét képezik a [automatikus kezelés](/azure/iot-hub/iot-hub-automatic-device-management) az IoT Hub szolgáltatást. Központi telepítések dinamikus folyamatokat, amelyek lehetővé teszik több eszközökre telepíti központilag a több modul, nyomon követheti az állapotát és a modulok állapotát, és szükség esetén módosítható. 

További információkért lásd: [automatikus telepítések megismerheti az IoT Edge egy eszközök vagy ipari méretekben](module-deployment-monitoring.md).

Ebben a cikkben, állítsa be az Azure CLI és az IoT-bővítmény. Ezután megtudhatja hogyan helyezheti üzembe a modulok IoT Edge-eszközök körét, és nyomon követheti az elérhető parancssori felület parancsait használva.

## <a name="cli-prerequisites"></a>Parancssori felület Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében. 
* [IoT Edge-eszközök](how-to-register-device-cli.md) az telepítve van az IoT Edge-futtatókörnyezet.
* [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI 2.0.24-es verzióját kell vagy újabb. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* A [IoT-bővítmény az Azure CLI-vel](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. További információkért lásd: [megtudhatja, hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat az IoT Edge](module-composition.md).

Azure CLI-vel modulok üzembe helyezéséhez mentése .txt fájlként helyi manifest nasazení. A fájl elérési útja a következő szakaszban használnia, ha futtatja a parancsot a alkalmazni a konfigurációt az eszközre. 

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
            "tempSensor": {
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
      "tempSensor": {
        "properties.desired": {}
      }
    }
  }
}
```

## <a name="identify-devices-using-tags"></a>Címkék használatával eszközök azonosítása

Központi telepítés létrehozásához, akkor megadhatja, mely eszközöket szeretné befolyásolni. Az Azure IoT Edge használatával eszközök azonosítja **címkék** az ikereszközben. Minden eszköz rendelkezhet, amelyeket bármilyen módon, amely logikus a megoldás több címkét. Például ha Ön kezeli a telephelyi intelligens épületek, előfordulhat, hogy hozzá a következő címkék eszköz:

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

Ikereszközök és címkékkel kapcsolatos további információkért lásd: [ikereszközök megismerése és használata az IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

Manifest nasazení, valamint a többi paraméter áll egy központi telepítés létrehozása modulok üzembe a céleszközökre. 

Használja a [az iot edge üzemelő példány létrehozása](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) paranccsal hozza létre a központi telepítés:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

A központi telepítés létrehozása parancs vesz igénybe az alábbi paraméterekkel: 

* **– üzembe helyezési azonosító** -központi telepítés jön létre az IoT hub nevét. Adjon meg egy egyedi nevet, amely legfeljebb 128 kisbetűk használata a központi telepítés. Kerülje a tárolóhelyek és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
* **---központnév** – az IoT hub, amely létrehozza a központi telepítés nevét. A központ az aktuális előfizetésben kell lennie. Az aktuális előfizetés módosítása a `az account set -s [subscription name]` parancsot.
* **– tartalmak** -fájl elérési útja az üzembe helyezés manifest JSON. 
* **– címkék** -címkék nyomon követéséhez az üzemelő példányok hozzáadása. Címkék olyan név, érték párok, melyek az üzemelő példány leírására. Címkék igénybe a JSON formázását neveket és értékeket. Például: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--célfeltétel** – adja meg a célként megadott feltétel meghatározásához, hogy mely eszközök érinteni fog a központi telepítés. A feltétel device twin címkék alapján vagy az ikereszköz jelentett tulajdonságait, és meg kell egyeznie a kifejezés formátuma. Például: `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--prioritású** -pozitív egész szám. Abban az esetben, ha két vagy több üzemelő példány célzott ugyanarra az eszközre, az üzembe helyezés a legnagyobb numerikus értékkel prioritás érvényes lesz.

## <a name="monitor-a-deployment"></a>Egy központi telepítésének figyelése

Használja a [az iot edge üzembe helyezési show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) parancsot egyetlen központi telepítésének részletei:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Az üzembe helyezés megjelenítése parancs idő szükséges a következő paraméterekkel:
* **– üzembe helyezési azonosító** – a központi telepítés, amely az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`

Vizsgálja meg az üzembe helyezés a parancssori ablakba. A **metrikák** tulajdonság mindegyik metrikát, amely kiértékeli a valamennyi elosztóhoz számát sorolja fel:

* **targetedCount** -egy rendszer mérőszám, amely a célcsoport-kezelési feltételnek megfelelő IoT hub device twins számát adja meg.
* **appliedCount** -rendszer metrika kellett volna a alkalmazni az IoT Hub az ikermodulokkal telepítési tartalmakhoz eszközök számát adja meg.
* **reportedSuccessfulCount** –, amelyek IoT Edge-eszközök számát adja meg az üzemelő IoT Edge-ügyfél futtatókörnyezet sikeressége reporting eszköz metrikát.
* **reportedFailedCount** –, amelyek IoT Edge-eszközök számát adja meg a jelentési hiba az IoT Edge-ügyfél futtatókörnyezet a központi telepítésben lévő eszközök metrikát.

Megjelenítheti az eszköz azonosítóját vagy objektumok listáját az egyes metrikák használatával a [az iot edge üzembe helyezési show-metrika](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) parancsot:

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

A deployment show-metrika parancs a következő paramétereket fogadja: 
* **– üzembe helyezési azonosító** – a központi telepítés, amely az IoT hub nevét.
* **--metrika-id** – a neve, amelynek meg szeretné tekinteni a eszközazonosítókat, listája például a metrikát. `reportedFailedCount`
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Amikor módosít egy központi telepítést, a módosítások azonnal replikálja az összes megcélzott eszközre. 

A célfeltétel frissít, ha elő a következő frissítéseket:

* Ha egy eszköz nem felelt meg a régi célfeltétel, de az új célfeltétel megfelel, és a központi telepítés rendszer a legmagasabb prioritású az eszközön, a központi telepítéshez az eszköz van alkalmazva. 
* Ha már nem a központi telepítés aktuálisan futó eszköz megfelel-e a célfeltétel, eltávolítja a központi telepítés és veszi fel a következő legmagasabb prioritású üzembe helyezés. 
* Ha már nem a központi telepítés aktuálisan futó eszköz megfelel-e a célként megadott feltétel, és nem felel meg a célfeltétel más központi telepítések, majd nincs változás történik az eszközön. Az eszköz addig a jelenlegi modulok fut, a jelenlegi állapotuk, de nem felügyelt már a központi telepítés részeként. Megfelel a célfeltétel, bármely más konfigurációért, miután eltávolítja a központi telepítés, és az új kiszolgálón vesz igénybe. 

Használja a [az iot edge üzemelő példányok frissítése](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) parancsot a telepítés frissítéséhez:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Frissítés telepítési parancsot a következő paramétereket fogadja:
* **– üzembe helyezési azonosító** – a központi telepítés, amely az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`
* **– Állítsa** – egy tulajdonság frissítése az üzemelő példányban. Az alábbi tulajdonságok frissíthetők:
  * targetCondition – példa `targetCondition=tags.location.state='Oregon'`
  * címkék 
  * prioritás


## <a name="delete-a-deployment"></a>Üzemelő példányának törlése

Ha töröl egy központi telepítést, a következő legmagasabb prioritású üzembe helyezés az egyik eszközön sem igénybe vehet. Ha az eszközök nem felelnek meg a célfeltétel, bármely más konfigurációért, majd a modulok nem lesznek eltávolítva az üzemelő példány törlése. 

Használja a [az iot edge-környezet törlése](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) üzemelő példányának törlése parancsot:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

A központi telepítési törlési parancsot a következő paramétereket fogadja: 
* **– üzembe helyezési azonosító** – a központi telepítés, amely az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`

## <a name="next-steps"></a>További lépések

Tudjon meg többet [IoT Edge-eszközökön való üzembe helyezés a modulok](module-deployment-monitoring.md).
