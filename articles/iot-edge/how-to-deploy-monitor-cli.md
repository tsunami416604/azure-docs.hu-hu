---
title: Üzembe helyezés és monitorozás modulok az Azure IoT Edge (CLI) |} A Microsoft Docs
description: A peremhálózati eszközökön futó modulok kezelése
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/25/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c94a58a19558350c3c20377ce750f6758f688c0d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998508"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Üzembe helyezés és monitorozás az Azure CLI használatával nagy mennyiségű IoT Edge-modulok

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Az Azure IoT Edge segítségével elemzési áthelyezheti az Edge-ben, és a egy felhőbeli felületet biztosít, kezelheti és figyelheti az IoT Edge-eszközök anélkül, hogy mindegyikhez fizikailag el kellene. Távolról kezelheti az eszközöket a funkció fontos egyre, IOT-megoldások nagyobb és összetettebb növekszik. Az Azure IoT Edge célja az üzleti céljaihoz, függetlenül attól, hogy hány eszköz hozzáadása támogatása.

Egyes eszközök kezelése és a modulok üzembe őket egyenként. Azonban ha nagy méretű eszközök módosításokat szeretne, létrehozhat egy **IoT Edge automatikus központi telepítési**, amely az IoT Hub automatikus kezelés része. Központi telepítések dinamikus folyamatokat, amelyek lehetővé teszik, hogy helyezze üzembe egyszerre több modul több eszközön, nyomon követheti az állapotát és a modulok állapotát, és szükség esetén módosítható. 

Ebben a cikkben, állítsa be az Azure CLI és az IoT-bővítmény. Ezután, megtudhatja, hogyan helyezheti üzembe a modulok IoT Edge-eszközök körét, és nyomon követheti az elérhető parancssori felület parancsait használva.

## <a name="cli-prerequisites"></a>Parancssori felület Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében. 
* [IoT Edge-eszközök](how-to-register-device-cli.md) az telepítve van az IoT Edge-futtatókörnyezet.
* [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI 2.0.24-es verzióját kell vagy újabb. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* A [IoT-bővítmény az Azure CLI-vel](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Hogyan alkalmazásjegyzékeket az üzembe helyezési a munkahelyi, és hogyan hozhat létre, azokat kapcsolatos további információkért lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).

Azure CLI-vel modulok üzembe helyezéséhez mentése .txt fájlként helyi manifest nasazení. A fájl elérési útja fogja használni a következő szakaszban, amikor futtatja a parancsot a alkalmazni a konfigurációt az eszközre. 

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

Központi telepítés létrehozásához, akkor megadhatja, mely eszközöket szeretné befolyásolni. Az Azure IoT Edge használatával eszközök azonosítja **címkék** az ikereszközben. Minden eszköz rendelkezhet több címkét, és meghatározhatja azokat bármilyen módon, amely logikus a megoldáshoz. Például ha Ön kezeli a telephelyi intelligens épületek, előfordulhat, hogy hozzá a következő címkék eszköz:

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

Ikereszközök és címkékkel kapcsolatos további információkért lásd: [ikereszközök megismerése és használata az IoT Hub][lnk-device-twin].

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

Manifest nasazení, valamint a többi paraméter áll egy központi telepítés létrehozása modulok üzembe a céleszközökre. 

A következő paranccsal hozzon létre egy központi telepítést:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int]
   ```

* **– üzembe helyezési azonosító** -központi telepítés jön létre az IoT hub nevét. Adjon meg egy egyedi nevet, amely legfeljebb 128 kisbetűk használata a központi telepítés. Kerülje a tárolóhelyek és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
* **– címkék** -címkék nyomon követéséhez az üzemelő példányok hozzáadása. Címkék olyan név, érték párok, melyek az üzemelő példány leírására. Ha például `HostPlatform, Linux` vagy `Version, 3.0.1`
* **– tartalmak** -fájl elérési útja az üzembe helyezés manifest JSON. 
* **---központnév** – az IoT hub, amely létrehozza a központi telepítés nevét. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`
* **--célfeltétel** – adja meg a célként megadott feltétel meghatározásához, hogy mely eszközök érinteni fog a központi telepítés. A feltétel device twin címkék alapján vagy az ikereszköz jelentett tulajdonságait, és meg kell egyeznie a kifejezés formátuma. Ha például `tags.environment='test'` vagy `properties.reported.devicemodel='4000x'`. 
* **--prioritású** -pozitív egész szám. Abban az esetben, ha két vagy több üzemelő példány célzott ugyanarra az eszközre, az üzembe helyezés a legnagyobb numerikus értékkel prioritás érvényes lesz.

## <a name="monitor-a-deployment"></a>Egy központi telepítésének figyelése

A következő parancs használatával a központi telepítés tartalmát meg:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```
* **– üzembe helyezési azonosító** – a központi telepítés, amely az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`

Vizsgálja meg az üzembe helyezés a parancssori ablakba. A **metrikák** tulajdonság mindegyik metrikát, amely kiértékeli a valamennyi elosztóhoz számát sorolja fel:
* **targetedCount** -egy rendszer mérőszám, amely a célcsoport-kezelési feltételnek megfelelő IoT hub device twins számát adja meg.
* **appliedCount** -rendszer metrika kellett volna a alkalmazni az IoT Hub az ikermodulokkal telepítési tartalmakhoz eszközök számát adja meg.
* **reportedSuccessfulCount** -eszköz, amely a peremhálózati eszközök számát adja meg az üzembe helyezés sikeressége az IoT Edge-ügyfél futtatókörnyezet reporting metrikát.
* **reportedFailedCount** – egy Edge-eszközök számát adja meg a jelentési hiba az IoT Edge-ügyfél futtatókörnyezet a központi telepítésben lévő eszköz metrikát.

Megjelenítheti az eszközazonosítókat listáját vagy objektumok az egyes metrikák a következő paranccsal:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **– üzembe helyezési azonosító** – a központi telepítés, amely az IoT hub nevét.
* **--metrika-id** – a neve, amelynek meg szeretné tekinteni a eszközazonosítókat, listája például a metrikát. `reportedFailedCount`
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Amikor módosít egy központi telepítést, a módosítások azonnal replikálja az összes megcélzott eszközre. 

A célfeltétel frissít, ha elő a következő frissítéseket:
* Ha egy eszköz nem felelt meg a régi célfeltétel, de az új célfeltétel megfelel, és a központi telepítés rendszer a legmagasabb prioritású az eszközön, a központi telepítéshez az eszköz van alkalmazva. 
* Ha már nem a központi telepítés aktuálisan futó eszköz megfelel-e a célfeltétel, eltávolítja a központi telepítés és veszi fel a következő legmagasabb prioritású üzembe helyezés. 
* Ha már nem a központi telepítés aktuálisan futó eszköz megfelel-e a célként megadott feltétel, és nem felel meg a célfeltétel más központi telepítések, majd nincs változás történik az eszközön. Az eszköz addig a jelenlegi modulok fut, a jelenlegi állapotuk, de nem felügyelt már a központi telepítés részeként. Megfelel a célfeltétel, bármely más konfigurációért, miután eltávolítja a központi telepítés, és az új kiszolgálón vesz igénybe. 

A következő parancs segítségével frissítse a központi telepítés:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **– üzembe helyezési azonosító** – a központi telepítés, amely az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`
* **– Állítsa** – egy tulajdonság frissítése az üzemelő példányban. Az alábbi tulajdonságok frissíthetők:
    * targetCondition – példa `targetCondition=tags.location.state='Oregon'`
    * címkék 
    * prioritás


## <a name="delete-a-deployment"></a>Üzemelő példányának törlése

Ha töröl egy központi telepítést, a következő legmagasabb prioritású üzembe helyezés az egyik eszközön sem igénybe vehet. Ha az eszközök nem felelnek meg a célfeltétel, bármely más konfigurációért, majd a modulok nem lesznek eltávolítva az üzemelő példány törlése. 

A következő paranccsal üzemelő példányának törlése:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```
* **– üzembe helyezési azonosító** – a központi telepítés, amely az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`

## <a name="next-steps"></a>További lépések

Tudjon meg többet [modulok üzembe helyezéséhez a peremhálózati eszközökre][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
