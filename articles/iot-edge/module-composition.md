---
title: Modul üzembe helyezése & útvonalakon üzembe helyezési jegyzékfájlokkal – Azure IoT Edge
description: Megtudhatja, hogyan deklarálja az üzembe helyezési jegyzék a telepítendő modulokat, hogyan helyezheti üzembe őket, és hogyan hozhat létre egymás között üzenet-útvonalakat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a4b90d8b6fe67de26c8e652e0dc5b62cc27023f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545633"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Ismerje meg, hogyan telepíthet modulokat és hozhat létre útvonalakat az IoT Edge-ben

Minden IoT Edge eszköz legalább két modult futtat: $edgeAgent és $edgeHub, amelyek az IoT Edge futtatókörnyezet részét képezik. IoT Edge eszköz több további modult is futtathat tetszőleges számú folyamat esetén. Az üzembe helyezési jegyzék segítségével adja meg, hogy az eszköz mely modulokat telepítse, és hogyan konfigurálhatja őket a közös munkához.

Az *üzembe helyezési jegyzék* egy JSON-dokumentum, amely a következőket ismerteti:

* A **IoT Edge Agent** modul Twin, amely három összetevőt tartalmaz:
  * Az eszközön futó egyes modulok tárolójának képe.
  * A modul rendszerképeit tartalmazó privát tároló-nyilvántartók eléréséhez szükséges hitelesítő adatok.
  * Útmutató az egyes modulok létrehozásához és kezeléséhez.
* Az **IoT Edge hub** -modul Twin, amelybe beletartozik az üzenetek a modulok közötti áramlása, és végül a IoT hub.
* Az extra modulok kívánt tulajdonságai (opcionális).

Az összes IoT Edge eszközt üzembe helyezési jegyzékkel kell konfigurálni. Egy újonnan telepített IoT Edge Runtime egy hibakódot jelent, amíg érvényes jegyzékfájlt nem konfigurált.

A Azure IoT Edge oktatóanyagokban az üzembe helyezési jegyzéket a Azure IoT Edge portálon elérhető varázsló segítségével hozhatja létre. Az üzembe helyezési jegyzék programozott módon is alkalmazható a REST vagy a IoT Hub Service SDK használatával. További információ: [IoT Edge központi telepítések ismertetése](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Üzembe helyezési jegyzék létrehozása

Az üzembe helyezési jegyzék magas szinten az olyan modulok listája, amelyek a kívánt tulajdonságokkal vannak konfigurálva. Az üzembe helyezési jegyzék egy IoT Edge eszközt (vagy az eszközök egy csoportját) adja meg, mely modulokat kell telepíteni és konfigurálni. Az üzembe helyezési jegyzékek tartalmazzák a különálló modulok *kívánt tulajdonságait* . IoT Edge az eszközök jelentést készítenek az egyes modulok *jelentett tulajdonságairól* .

Minden üzembe helyezési jegyzékfájlban két modulra `$edgeAgent`van szükség `$edgeHub`: és. Ezek a modulok a IoT Edge eszközt és a rajta futó modulokat kezelő IoT Edge futtatókörnyezet részét képezik. További információ ezekről a modulokról: [a IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md).

A két futásidejű modul mellett akár 20 modult is hozzáadhat a saját IoT Edge eszközön való futtatáshoz.

A IoT Edge futtatókörnyezetet (edgeAgent és edgeHub) tartalmazó telepítési jegyzékfájl érvényes.

Az üzembe helyezési jegyzékek ezt a struktúrát követik:

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

Annak meghatározása, hogy a IoT Edge futtatókörnyezet hogyan telepíti a modulokat a telepítésben. A IoT Edge ügynök az a futtatókörnyezet-összetevő, amely egy IoT Edge eszköz telepítésével, frissítésével és állapotával kapcsolatos jelentéskészítést kezeli. Ezért a $edgeAgent modul Twin az összes modul konfigurációs és felügyeleti információit tartalmazza. Ez az információ magában foglalja a IoT Edge-ügynök konfigurációs paramétereit is.

A megadható vagy kötelező tulajdonságok teljes listájáért tekintse meg [a IoT Edge ügynök és a IoT Edge hub tulajdonságait](module-edgeagent-edgehub.md).

A $edgeAgent tulajdonságok ezt a struktúrát követik:

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

Az IoT Edge hub felügyeli a modulok, a IoT Hub és a leveles eszközök közötti kommunikációt. Ezért a $edgeHub modul Twin egy olyan, az *útvonalak* nevű kívánt tulajdonságot tartalmaz, amelyek bemutatják, hogyan jutnak át az üzenetek a központi telepítésbe. Ugyanazon az üzemelő példányon belül több útvonal is lehet.

Az útvonalak a **$edgeHub** kívánt tulajdonságaiban vannak deklarálva a következő szintaxissal:

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

Minden útvonalnak forrásra és fogadóra van szüksége, de a feltétel egy opcionális darab, amely az üzenetek szűrésére használható.

### <a name="source"></a>Forrás

A forrás meghatározza, hogy az üzenetek honnan származnak. A IoT Edge üzeneteket küldhet a modulokból vagy a levelekből származó eszközökről.

A IoT SDK-k használatával a modulok adott kimeneti várólistákat deklarálnak az üzeneteik számára a ModuleClient osztály használatával. A kimeneti várólisták nem szükségesek, de hasznosak lehetnek több útvonal kezeléséhez. A Leaf-eszközök a IoT SDK-k DeviceClient-osztályával ugyanúgy küldhetnek üzeneteket IoT Edge átjáró-eszközökre, mint az üzenetek küldését IoT Hub. További információ: az [Azure IoT hub SDK-k megismerése és használata](../iot-hub/iot-hub-devguide-sdks.md).

A forrás tulajdonság a következő értékek bármelyike lehet:

| Forrás | Leírás |
| ------ | ----------- |
| `/*` | Minden eszközről a felhőbe irányuló üzenet vagy kettős változási értesítés bármely modulból vagy levélből |
| `/twinChangeNotifications` | Bármely modulból vagy levélből származó, kettős változás (jelentett tulajdonságok) |
| `/messages/*` | Bármely, a modul által egy vagy több kimeneten vagy egy levélen keresztül küldött eszközről a felhőbe irányuló üzenet |
| `/messages/modules/*` | Bármely modul által a felhőbe küldött, egy vagy több kimenetet tartalmazó eszközről a felhőbe irányuló üzenet |
| `/messages/modules/<moduleId>/*` | Bármely, egy adott modul által küldött eszközről a felhőbe irányuló üzenet egy vagy több kimeneten keresztül |
| `/messages/modules/<moduleId>/outputs/*` | Egy adott modul által a kimeneten keresztül küldött összes eszközről a felhőbe irányuló üzenet |
| `/messages/modules/<moduleId>/outputs/<output>` | Egy adott modul által az adott kimeneten küldött összes eszközről a felhőbe irányuló üzenet |

### <a name="condition"></a>Állapot

A feltétel nem kötelező az útvonal deklarációjában. Ha az összes üzenetet át szeretné adni a forrásról a fogadónak, csak hagyja ki a **Where** záradékot teljesen. Vagy használhatja a [IoT hub lekérdezési nyelvet](../iot-hub/iot-hub-devguide-routing-query-syntax.md) a feltételnek megfelelő üzenetek vagy üzenetek szűrésére. IoT Edge útvonalak nem támogatják az üzenetek szűrését a Twin címkék vagy tulajdonságok alapján.

A IoT Edge moduljai között továbbított üzenetek ugyanúgy vannak formázva, mint az eszközök és az Azure IoT Hub között továbbított üzenetek. Minden üzenet JSON-ként van formázva, és **systemProperties**, **appProperties**és **szövegtörzs** paraméterekkel rendelkezik.

A következő szintaxissal hozhat létre lekérdezéseket a három paraméter bármelyikén:

* Rendszertulajdonságok `$<propertyName>` : vagy`{$<propertyName>}`
* Alkalmazás tulajdonságai:`<propertyName>`
* Törzs tulajdonságai:`$body.<propertyName>`

Az üzenet tulajdonságaira vonatkozó lekérdezések létrehozásával kapcsolatos példákat az [eszközről a felhőbe irányuló Message Routes lekérdezési kifejezések](../iot-hub/iot-hub-devguide-routing-query-syntax.md)című témakörben talál.

IoT Edgera jellemző példa az, amikor egy levélből álló eszközről szeretne szűrni egy átjáró-eszközre érkező üzeneteket. A modulokból érkező üzenetek közé tartozik egy **connectionModuleId**nevű rendszertulajdonság. Tehát ha a levélből származó üzeneteket közvetlenül a IoT Hubra szeretné irányítani, a következő útvonal használatával zárja ki a modulok üzeneteit:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink (Fogadó)

A fogadó meghatározza az üzenetek küldésének helyét. Csak modulok és IoT Hub fogadhatnak üzeneteket. Az üzenetek nem irányíthatók át más eszközökre. A fogadó tulajdonságban nincsenek helyettesítő karakterek.

A fogadó tulajdonság a következő értékek bármelyike lehet:

| Sink (Fogadó) | Leírás |
| ---- | ----------- |
| `$upstream` | Üzenet küldése IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Az üzenet elküldése egy adott modul megadott bemenetére |

A IoT Edge legalább egyszeri garanciát biztosít. Az IoT Edge hub helyileg tárolja az üzeneteket, ha egy útvonal nem tudja kézbesíteni az üzenetet a fogadónak. Ha például az IoT Edge hub nem tud csatlakozni a IoT Hubhoz, vagy a célként megadott modul nincs csatlakoztatva.

IoT Edge hub a `storeAndForwardConfiguration.timeToLiveSecs` [IoT Edge hub kívánt tulajdonságainak](module-edgeagent-edgehub.md)tulajdonságában megadott időpontig tárolja az üzeneteket.

## <a name="define-or-update-desired-properties"></a>A kívánt tulajdonságok megadása vagy frissítése

Az üzembe helyezési jegyzék a IoT Edge eszközön központilag telepített modulok kívánt tulajdonságait határozza meg. Az üzembe helyezési jegyzékfájl kívánt tulajdonságai felülírják a modulban jelenleg található kívánt tulajdonságokat.

Ha nem ad meg külön modult a központi telepítési jegyzékfájlban, IoT Hub nem módosítja a modult a Twin módon. Ehelyett programozott módon állíthatja be a kívánt tulajdonságokat.

Ugyanazok a mechanizmusok, amelyek lehetővé teszik az eszközök ikrek módosítását, az ikrek moduljának módosítására szolgálnak. További információ: a [modul Twin fejlesztői útmutatója](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Üzembe helyezési jegyzékfájl – példa

Az alábbi példa azt szemlélteti, hogy az érvényes üzembe helyezési jegyzékfájlt tartalmazó dokumentum milyen módon jelenhet meg.

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

* A $edgeAgent és az $edgeHubben felvehető vagy befoglalható tulajdonságok teljes listáját az [IoT Edge ügynök és IoT Edge hub tulajdonságai](module-edgeagent-edgehub.md)részben találja.

* Most, hogy már tudja, hogyan használják IoT Edge modulokat, [Ismerje meg a IoT Edge modulok fejlesztésének követelményeit és eszközeit](module-development.md).
