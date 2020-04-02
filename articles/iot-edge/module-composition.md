---
title: Modul & útvonalak üzembe helyezése telepítési jegyzékekkel – Azure IoT Edge
description: Megtudhatja, hogy a központi telepítési jegyzékfájl hogyan deklarálja, hogy mely modulokat kell telepíteni, hogyan telepítheti őket, és hogyan hozhat létre közöttük üzenetútvonalakat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a4b90d8b6fe67de26c8e652e0dc5b62cc27023f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545633"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Ismerje meg, hogyan telepíthet modulokat és hozhat létre útvonalakat az IoT Edge-ben

Minden IoT Edge-eszköz legalább két modult futtat: $edgeAgent és $edgeHub, amelyek az IoT Edge-futásidő részét képezik. Az IoT Edge-eszköz több további modult futtathat tetszőleges számú folyamathoz. A központi telepítési jegyzékfájl segítségével tájékoztassa az eszközről, hogy mely modulokat kell telepítenie, és hogyan konfigurálhatja őket az együttműködésre.

A *központi telepítési jegyzékfájl* egy JSON-dokumentum, amely a következőket írja le:

* Az **IoT Edge ügynök** modul iker, amely három összetevőből áll:
  * A tárolórendszerkép az eszközön futó minden modulhoz.
  * A modullemezképeket tartalmazó magántároló-nyilvántartások eléréséhez szükséges hitelesítő adatok.
  * Az egyes modulok létrehozásának és kezelésének módjára vonatkozó utasítások.
* Az **IoT Edge hub** modul iker, amely magában foglalja, hogyan üzenetek között a modulok között, és végül az IoT Hub.
* A kívánt tulajdonságait minden további modul twins (nem kötelező).

Minden IoT Edge-eszközt üzembe helyezési jegyzékfájllal kell konfigurálni. Egy újonnan telepített IoT Edge futásidejű jelenti a hibakódot, amíg konfigurált egy érvényes jegyzékfájl.

Az Azure IoT Edge oktatóanyagokban egy központi telepítési jegyzékfájl létrehozása az Azure IoT Edge portálon egy varázsló. A központi telepítési jegyzékfájl programozott módon is alkalmazhat rest vagy az IoT Hub service SDK használatával. További információ: [IoT Edge-telepítések ismertetése.](module-deployment-monitoring.md)

## <a name="create-a-deployment-manifest"></a>Központi telepítési jegyzékfájl létrehozása

Magas szinten a központi telepítési jegyzékfájl a modul ikreik listáját, amelyek a kívánt tulajdonságokkal vannak konfigurálva. A központi telepítési jegyzékfájl egy IoT Edge-eszköz (vagy eszközök egy csoportját) közli, hogy mely modulokat kell telepíteni, és hogyan konfigurálhatja őket. A központi telepítési jegyzékek tartalmazzák az egyes ikermodulok *kívánt tulajdonságait.* Az IoT Edge-eszközök jelentés vissza az egyes modulok *jelentett tulajdonságait.*

Minden telepítési jegyzékben két modulra van szükség: `$edgeAgent`és `$edgeHub`a . Ezek a modulok az IoT Edge futtatótér részét képezik, amely kezeli az IoT Edge-eszközt és a rajta futó modulokat. Ezekről a modulokról további információt [az IoT Edge futásidő és az architektúra ismertetése](iot-edge-runtime.md)című témakörben talál.

A két futásidejű modul mellett legfeljebb 20 saját modult adhat hozzá egy IoT Edge-eszközön való futtatáshoz.

Egy központi telepítési jegyzékfájl, amely csak az IoT Edge futásidejű (edgeAgent és edgeHub) érvényes.

A telepítési jegyzékek ezt a struktúrát követik:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Modulok konfigurálása

Adja meg, hogy az IoT Edge futásidejű telepíti a modulokat a központi telepítésben. Az IoT Edge-ügynök a futásidejű összetevő, amely kezeli az IoT Edge-eszközök telepítését, frissítéseit és állapotjelentését. Ezért a $edgeAgent modul iker tartalmazza a konfigurációs és felügyeleti információkat az összes modul. Ez az információ magában foglalja az IoT Edge-ügynök konfigurációs paramétereit.

A tulajdonságok teljes listáját, amely lehet vagy kell felvenni, lásd: [Tulajdonságok az IoT Edge-ügynök és az IoT Edge hub.](module-edgeagent-edgehub.md)

A $edgeAgent tulajdonságok követik ezt a struktúrát:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Útvonalak deklarálása

Az IoT Edge hub kezeli a modulok, az IoT Hub és a levéleszközök közötti kommunikációt. Ezért a $edgeHub modul iker tartalmaz egy kívánt tulajdonság ot *útvonalak,* amely deklarálja, hogyan üzeneteket a központi telepítésen belül. Ugyanazon a központi telepítésen belül több útvonal is lehet.

Az útvonalak a kívánt **$edgeHub** a következő szintaxissal lesznek deklarálva:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Minden útvonalhoz szükség van egy forrásra és egy fogadóra, de a feltétel egy választható darab, amely az üzenetek szűrésére használható.

### <a name="source"></a>Forrás

A forrás határozza meg, hogy az üzenetek honnan származnak. Az IoT Edge modulokról vagy levéleszközökről irányíthatja az üzeneteket.

Az IoT SDK-k használatával a modulok deklarálhatnak adott kimeneti várólistákat az üzeneteikhez a ModuleClient osztály használatával. A kimeneti várólisták nem szükségesek, de több útvonal kezeléséhez hasznosak. A leaf-eszközök az IoT SDK-k DeviceClient osztályával ugyanúgy küldhetnek üzeneteket az IoT Edge átjáróeszközeinek, mint az IoT Hubnak. További információ: [Az Azure IoT Hub SDK-k megértése és használata.](../iot-hub/iot-hub-devguide-sdks.md)

A forrástulajdonság a következő értékek bármelyike lehet:

| Forrás | Leírás |
| ------ | ----------- |
| `/*` | Minden eszközről felhőbe irányuló üzenet vagy ikermódosítási értesítés bármely modulról vagy levéleszközről |
| `/twinChangeNotifications` | Bármilyen ikerváltozás (jelentett tulajdonságok) bármely modulból vagy levéleszközből |
| `/messages/*` | Bármely eszközről felhőbe irányuló üzenet, amelyet egy modul küld valamilyen kimeneten vagy egyáltalán nem kimeneten keresztül, vagy egy levéleszköz |
| `/messages/modules/*` | A modul által egy vagy több kimeneten keresztül küldött eszközről felhőbe küldött üzenet |
| `/messages/modules/<moduleId>/*` | Egy adott modul által küldött eszközről felhőbe irányuló üzenet, amely et egy vagy egyáltalán nem |
| `/messages/modules/<moduleId>/outputs/*` | Egy adott modul által egy adott kimeneten keresztül küldött eszközről felhőbe küldött üzenet |
| `/messages/modules/<moduleId>/outputs/<output>` | Bármely eszközről felhőbe irányuló üzenet, amelyet egy adott modul küld egy adott kimeneten keresztül |

### <a name="condition"></a>Állapot

A feltétel nem kötelező az útvonal-deklarációban. Ha a forrástól a fogadóba szeretné átadni az összes üzenetet, hagyja ki teljesen a **WHERE** záradékot. Vagy használhatja az [IoT Hub lekérdezési nyelv](../iot-hub/iot-hub-devguide-routing-query-syntax.md) szűrése bizonyos üzenetek vagy üzenettípusok, amelyek megfelelnek a feltételnek. Az IoT Edge-útvonalak nem támogatják az üzenetek szűrését ikercímkék vagy tulajdonságok alapján.

Az IoT Edge-ben a modulok között átadott üzenetek ugyanúgy vannak formázva, mint az eszközök és az Azure IoT Hub között átadott üzenetek. Minden üzenet JSON formátumú, és **rendszertulajdonságokkal**, **appTulajdonságokkal**és **törzsparaméterekkel** rendelkezik.

A lekérdezéseket a következő szintaxissal a három paraméter bármelyike köré építheti:

* Rendszertulajdonságok: `$<propertyName>` vagy`{$<propertyName>}`
* Alkalmazás tulajdonságai:`<propertyName>`
* A test tulajdonságai:`$body.<propertyName>`

Az üzenettulajdonságoklekérdezéseinek létrehozásáról az [Eszközről a felhőbe irányuló üzenetútvonalak lekérdezési kifejezéseit című témakörben találja.](../iot-hub/iot-hub-devguide-routing-query-syntax.md)

Egy példa, amely az IoT Edge-re jellemző, ha szűrni szeretné az átjáróeszközhöz egy levéleszközről érkező üzeneteket. A modulokból érkező üzenetek közé tartozik a **connectionModuleId**nevű rendszertulajdonság. Ha tehát közvetlenül az IoT Hubra szeretné átirányítani az üzeneteket a levéleszközökről, használja a következő útvonalat a modulüzenetek kizárásához:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink (Fogadó)

A fogadó határozza meg, hogy az üzenetek et hová küldjék. Csak a modulok és az IoT Hub fogadhat üzeneteket. Az üzenetek nem irányíthatók más eszközökre. Nincsenek helyettesítő beállítások a fogadó tulajdonságban.

A fogadó tulajdonság a következő értékek bármelyike lehet:

| Sink (Fogadó) | Leírás |
| ---- | ----------- |
| `$upstream` | Az üzenet küldése az IoT Hubnak |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Az üzenet elküldése egy adott modul egy adott bemenetére |

Az IoT Edge legalább egyszer garantálja. Az IoT Edge hub tárolja az üzeneteket helyileg abban az esetben, ha egy útvonal nem tudja kézbesíteni az üzenetet a fogadó. Például ha az IoT Edge hub nem tud csatlakozni az IoT Hubhoz, vagy a célmodul nem csatlakozik.

Az IoT Edge hub tárolja az üzeneteket `storeAndForwardConfiguration.timeToLiveSecs` az [IoT Edge hub kívánt tulajdonságainak](module-edgeagent-edgehub.md)tulajdonságában megadott időpontig.

## <a name="define-or-update-desired-properties"></a>Kívánt tulajdonságok definiálása vagy frissítése

A központi telepítési jegyzékfájl megadja a kívánt tulajdonságokat az IoT Edge-eszközre telepített minden egyes modulhoz. A központi telepítési jegyzékben kívánt tulajdonságok felülírják a modul ikerelemben jelenleg lévő kívánt tulajdonságokat.

Ha nem adja meg a modul iker kívánt tulajdonságait a központi telepítési jegyzékben, az IoT Hub semmilyen módon nem módosítja a modul iker. Ehelyett programozott módon állíthatja be a kívánt tulajdonságokat.

Ugyanazokat a mechanizmusokat, amelyek lehetővé teszik az eszköz ikrek módosítását, a modulikrek módosítására használják. További információt a [modul ikerfejlesztői útmutatójában talál.](../iot-hub/iot-hub-devguide-module-twins.md)

## <a name="deployment-manifest-example"></a>Példa telepítési jegyzékfájlra

A következő példa bemutatja, hogyan nézhet ki egy érvényes telepítési jegyzékfájl-dokumentum.

```json
{
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
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
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
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
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
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>További lépések

* A $edgeAgent és $edgeHub beilleszthető vagy bekell, hogy szerepeljen tulajdonságok teljes listáját, [lásd: Az IoT Edge-ügynök és az IoT Edge hub tulajdonságai.](module-edgeagent-edgehub.md)

* Most, hogy már tudja, hogyan használják az IoT Edge-modulokat, [ismerje meg az IoT Edge-modulok fejlesztésére vonatkozó követelményeket és eszközöket.](module-development.md)
