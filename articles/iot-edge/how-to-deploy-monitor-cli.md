---
title: Telepítheti és figyelheti a modulok Azure IoT peremhálózati (CLI) |} Microsoft Docs
description: A peremhálózati eszközön futó modulok kezelése
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 705f7bfa62154bff62b2357bd8f33c01e97404d1
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036120"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Üzembe helyezése és figyelése az Azure parancssori felület használatával léptékű IoT Edge-modulok

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Azure IoT peremhálózati lehetővé teszi analytics áthelyezése a peremhálózaton, és a felhő felületet biztosít, így kezelheti és figyelheti az IoT-peremeszközök anélkül, hogy minden egyes fizikailag eléréséhez. Távolról kezelheti az eszközöket képesség, egyre fontosabb az eszközök internetes hálózatát megoldások nagyobb és összetettebb folyamatosan nőnek. Az üzleti céljaihoz, függetlenül attól, hány eszközt ad hozzá Azure IoT peremhálózati terveztek.

Egyes eszközök kezeléséhez és a modulok telepíteni őket egyenként. Azonban ha azt szeretné, nagy léptékű eszközökre módosításokat, létrehozhat egy **IoT peremhálózati automatikus központi telepítési**, amely az IoT hubon automatikus kezelés része. Központi telepítések dinamikus folyamatok, amelyek lehetővé teszik több modul egyszerre több eszközre telepíteni, állapota és a modulok állapotának nyomon, és szükség esetén módosítása. 

Ebben a cikkben az Azure CLI 2.0 és az IoT-bővítmény beállítását ismertetjük. Majd megtanulhatja a modulok telepítése az IoT-Edge eszközök, és használja a rendelkezésre álló parancssori felület parancsait előrehaladásának figyeléséhez.

## <a name="cli-prerequisites"></a>Parancssori felület Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésben. 
* [Az IoT-peremeszközök](how-to-register-device-cli.md) telepített IoT peremhálózati futtatási idő mellett.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) a saját környezetében. Az Azure CLI 2.0 legalább 2.0.24-es verzióját kell használnia. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* A [IoT-bővítményt az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Egy üzembe helyezési jegyzék konfigurálása

Egy üzembe helyezési jegyzék egy JSON-dokumentumában, hogy melyik modulokat telepíteni, hogyan közötti adatáramlás a modulok, és a modul twins kívánt tulajdonságait ismerteti. Hogyan telepítési módjától munkahelyi és hogyan hozza létre a címzetteket kapcsolatos további információkért lásd: [megérteni, hogyan IoT peremhálózati modulok használják, konfigurálhatók, és használja fel újra](module-composition.md).

Modulok használata Azure CLI 2.0 telepítéséhez mentése .txt fájlként az üzembe helyezési jegyzék helyileg. A fájl elérési útját fogja használni a következő szakaszban, a konfiguráció alkalmazása az eszköz a parancs futtatásakor. 

Íme egy egyszerű üzembe helyezési jegyzék egy modul példa:

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
               },
               "systemModules": {
                 "edgeAgent": {
                   "type": "docker",
                   "settings": {
                     "image": "microsoft/azureiotedge-agent:1.0-preview",
                     "createOptions": "{}"
                   }
                 },
                 "edgeHub": {
                   "type": "docker",
                   "status": "running",
                   "restartPolicy": "always",
                   "settings": {
                     "image": "microsoft/azureiotedge-hub:1.0-preview",
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

Mielőtt létrehozna egy központi telepítést, akkor lehet megadni, mely eszközöket szeretné befolyásolni. Az Azure IoT peremhálózati azonosítja eszközök **címkék** az eszköz iker a. Minden eszköz lehet több címkét, és meghatározhatja azokat bármilyen módon legjobb megoldást. Kezelheti egy egyetemi intelligens épületek, előfordulhat, hogy egy eszközre például hozzáadása a következő címkékkel:

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

Eszköz twins és címkékkel kapcsolatos további információkért lásd: [megértése és használja az IoT Hub eszköz twins][lnk-device-twin].

## <a name="create-a-deployment"></a>Hozzon létre telepítést

Modulok a cél-eszközökre telepít hoz létre a központi telepítéséhez, az üzembe helyezési jegyzék, valamint a más paramétereket tartalmaz. 

A következő parancsot használja a központi telepítés létrehozásához:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int]
   ```

* **--telepítési-azonosító** -a központi telepítések célszámítógépeinek létrejön az IoT hub nevét. Adjon meg legfeljebb 128 kisbetűk egy egyedi nevet a központi telepítés. Elkerülendő, a szóközöket és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
* **--címkék** -nyomon követéséhez a központi telepítések címkék hozzáadása. Címkék: név, érték párok, amelyek a központi telepítés ismertetik. Például `HostPlatform, Linux` vagy `Version, 3.0.1`
* **--tartalom** -fájl elérési útja a központi telepítés manifest JSON. 
* **---központnév** -az IoT hub, amely létrehozza a központi telepítés nevét. A központ az aktuális előfizetésben kell lennie. A parancs a kívánt előfizetés váltani `az account set -s [subscription name]`
* **--cél-feltétel** -adjon meg egy cél feltételt határozza meg, hogy mely eszközök fog ezzel az üzembe helyezéssel. A feltétel alapján eszköz iker címkék vagy eszköz iker tulajdonságok szükséges, és meg kell felelnie a kifejezés formátumban. Például `tags.environment='test'` vagy `properties.desired.devicemodel='4000x'`. 
* **--prioritás** -pozitív egész szám. Abban az esetben, ha két vagy több üzemelő példány célzott ugyanazon az eszközön, a legnagyobb numerikus értéket prioritás a központi telepítés érvényesek.

## <a name="monitor-a-deployment"></a>A figyelő a központi telepítés

Az alábbi parancs segítségével a központi telepítés tartalmának megjelenítése:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```
* **--telepítési-azonosító** -a központi telepítés már szerepel az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. A parancs a kívánt előfizetés váltani `az account set -s [subscription name]`

Vizsgálja meg a központi telepítést, a parancssori ablakban. A **metrikák** tulajdonság minden olyan metrikajelentés minden hubból kiértékelt számát sorolja fel:
* **targetedCount** -IoT-központ, a célcsoport-kezelési feltételnek megfelelő eszköz twins számát megadó rendszer metrikát.
* **appliedCount** -rendszer metrika meghatározza az eszközök rendelkezésére állt-e a központi telepítési tartalom alkalmazza azok modul twins az IoT-központ számát.
* **reportedSuccessfulCount** -a központi telepítés sikeressége az IoT-Edge ügyfél futásidejű reporting peremeszközök számát megadó eszköz metrikát.
* **reportedFailedCount** -, amely meghatározza a peremeszközök számát a telepítésben az IoT-Edge ügyfél futásidejű hiba reporting eszköz metrikát.

Megjelenítheti eszközazonosítók listája vagy objektumok az egyes a metrikák a következő paranccsal:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **--telepítési-azonosító** -a központi telepítés már szerepel az IoT hub nevét.
* **--azonosító a metrika** - a legyen megtekintéséhez a eszközazonosítók, például a mérték neve `reportedFailedCount`
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. A parancs a kívánt előfizetés váltani `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Módosítsa a központi telepítés

Amikor módosít egy központi telepítést, a módosítások azonnal replikálja az összes megcélzott eszköz. 

Ha frissíti a cél feltétel, a következő frissítéseket fordulhat elő:
* Ha egy eszköz nem felelt meg a régi cél feltétel, de az új cél feltétel és a központi telepítés a legmagasabb prioritású az eszköznek, majd a központi telepítés vonatkozik az eszközt. 
* Jelenleg fut a központi telepítés már nem eszköz megfelel-e a célként megadott feltétel, ha eltávolítja a központi telepítés, és időt vesz igénybe, a következő legmagasabb prioritású üzemelő példányon. 
* Ha a jelenleg futó már nem a központi telepítés eszköz megfelel-e a célként megadott feltétel, és nem felel meg a célként megadott feltétel egyéb telepítések, majd nem módosul az eszközön. Az eszköz továbbra is fennáll, az aktuális modulok aktuális állapotban fut, de nem áll az üzembe helyezés többé. Amennyiben az megfelel-e bármilyen más központi telepítési cél feltétele, eltávolítja a központi telepítés, és az új kiszolgálón időt vesz igénybe. 

A központi telepítés frissítéséhez használja a következő parancsot:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **--telepítési-azonosító** -a központi telepítés már szerepel az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. A parancs a kívánt előfizetés váltani `az account set -s [subscription name]`
* **– Állítsa** -tulajdonság a telepítés frissítéséhez. Frissítheti a következő tulajdonságokkal:
    * targetCondition – példa `targetCondition=tags.location.state='Oregon'`
    * címkék 
    * prioritás


## <a name="delete-a-deployment"></a>A központi telepítés törlése

Ha töröl egy központi telepítést, azokat az eszközöket, a következő legmagasabb prioritású telepítésekhez igénybe. Ha az eszközök nem felelnek meg a célként megadott feltétel más központi telepítés, majd a modulok nem törlődnek, amikor a központi telepítést törölték. 

A következő parancs segítségével törölheti a központi telepítés:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```
* **--telepítési-azonosító** -a központi telepítés már szerepel az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. A parancs a kívánt előfizetés váltani `az account set -s [subscription name]`

## <a name="next-steps"></a>További lépések

További információ [modulok telepítése peremeszközök][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
