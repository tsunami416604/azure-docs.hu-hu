---
title: "Az Azure IoT peremhálózati modul-összeállítást |} Microsoft Docs"
description: "Ismerje meg, mi az Azure IoT peremhálózati modulok kerül, és hogyan felhasználhatók"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4b59a715919e38e68c3b7518932617e9950940e3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Megérteni, hogyan használható a IoT peremhálózati modulok, konfigurálva, és használja fel újra – előzetes

Minden egyes IoT peremhálózati eszköz futtatja legalább két modulokat: $edgeAgent és $edgeHub, az IoT-Edge futásidejű alkotó. Ezeket a szabványos két mellett minden IoT peremhálózati eszköz futtatható több modul folyamatok tetszőleges számú végrehajtásához. Ezek a modulok egyszerre egy eszközhöz telepíti, meg kell oly módon, hogy melyik modulokat-e adva hogyan léphetnek kapcsolatba egymással. 

A *üzembe helyezési jegyzék* JSON-dokumentumok, amelyeket ismerteti:

* Mely IoT peremhálózati modulokat kell telepíteni, azok létrehozását és kezelését beállítások mellett.
* A peremhálózati hub, beleértve, hogyan üzenetek flow modulok között, és végül az IoT-központ konfigurációját.
* Másik lehetőségként értékek beállítása a modul twins kívánt tulajdonságait konfigurálhatja az egyes modul alkalmazásokat.

Minden IoT peremhálózati eszköz egy üzembe helyezési jegyzék konfigurálni kell. Egy újonnan telepített IoT peremhálózati futásidejű jelentést készít egy hibakódot, amíg a egy érvényes jegyzékfájl konfigurálva. 

Az Azure IoT peremhálózati oktatóanyagok hoz egy üzembe helyezési jegyzék haladjon végig a varázsló az Azure IoT peremhálózati portálon létre. Egy üzembe helyezési jegyzék programozott módon, REST- vagy a IoT Hub SDK is alkalmazhat. Tekintse meg [telepítés és figyelés] [ lnk-deploy] IoT peremhálózati központi telepítések további információt.

## <a name="create-a-deployment-manifest"></a>Hozzon létre egy üzembe helyezési jegyzék

Magas szinten az üzembe helyezési jegyzék konfigurálja az IoT peremhálózati modulok IoT peremhálózati eszköz a telepített egy modul iker kívánt tulajdonságait. Ezek a modulok két mindig találhatók: a peremhálózati ügynök és a peremhálózati központ.

Érvénytelen egy üzembe helyezési jegyzék, amely csak az IoT-Edge futásidejű (ügynök és hub) tartalmazza.

A jegyzékfájl Ez a struktúra követi:

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Modulok konfigurálása

Létrehozó modulokat, amelyet központilag telepíteni szeretne a kívánt tulajdonságait, mellett kell tájékoztatása az IoT-Edge futásidejű a telepítést. Minden modul konfigurációjának és kezelésének információi belül kerül a **$edgeAgent** szükséges tulajdonságai. Ezt az információt a peremhálózati ügynök önmagát konfigurációs paramétereket tartalmazza. 

Vagy szerepelnie kell függvénykötésnek tulajdonságok teljes listáját lásd: [a peremhálózati ügynök és a peremhálózati hub tulajdonságainak](module-edgeagent-edgehub.md).

A $edgeAgent tulajdonságok hajtsa végre ezt a struktúrát:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
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
            "{module1}": { //optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Útvonalak deklarálható

Biztonsági központ biztosítja az deklaratív módon irányítja az üzenetek modulokat, valamint modulok és az IoT-központ között. A peremhálózati hub minden kommunikáció kezeli, így belül kerül az útvonal-információkat a **$edgeHub** szükséges tulajdonságai. Több útvonal belül az azonos környezetben is.

Útvonalak van deklarálva a **$edgeHub** szükségeskonfiguráció-tulajdonságok a következő szintaxissal:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Minden útvonal kell a forrás- és a fogadó, de a feltétel egy választható adat üzenet szűrésére használható. 


### <a name="source"></a>Forrás
A forrás adja meg, ahol az üzenetek származik. Ez a következő értékek egyike lehet:

| Forrás | Leírás |
| ------ | ----------- |
| `/*` | Minden eszközt vagy modul érkező üzenetek eszközről a felhőbe |
| `/messages/*` | Bármely eszközről a felhőbe által küldött üzenet egy eszköz vagy egy modul néhány vagy nincs kimeneti keresztül |
| `/messages/modules/*` | Bármely eszközről a felhőbe által küldött üzenet keresztül néhány vagy nincs kimeneti modul |
| `/messages/modules/{moduleId}/*` | Bármely eszközről a felhőbe által küldött üzenet {moduleId} nincs kimenet |
| `/messages/modules/{moduleId}/outputs/*` | Bármely eszközről a felhőbe által küldött üzenet {moduleId} néhány kimenet |
| `/messages/modules/{moduleId}/outputs/{output}` | Bármely eszközről a felhőbe üzenet {moduleId} a {kimeneti} |

### <a name="condition"></a>Feltétel
A feltétel nem kötelező megadni egy útvonal deklarációjában. Ha azt szeretné, a gyűjtő összes üzenetek átadása a forrás, hagyja a **ahol** záradék teljesen. Vagy használhatja a [IoT-központ lekérdezési nyelv] [ lnk-iothub-query] szűrése az egyes üzenetek vagy üzenettípusok, amelyek megfelelnek a következő feltételt.

Az Azure IoT-üzenetek JSON-ként formázott és mindig legalább egy **törzs** paraméter. Példa:

```json
"message": {
    "body":{
        "ambient":{
            "temperature": 54.3421,
            "humidity": 25
        },
        "machine":{
            "status": "running",
            "temperature": 62.2214
        }
    },
    "appProperties":{
        ...
    }
}
```

A mintaüzenet miatt számos feltétel adható meg, például:
* `WHERE $body.machine.status != "running"`
* `WHERE $body.ambient.temperature <= 60 AND $body.machine.temperature >= 60`

A feltétel üzenettípusok, például egy átjáró, amely a levél eszközök érkező üzenetek szeretné rendezni is használható. Modulok érkező üzenetek tartalmaznak egy adott tulajdonságra nevű **connectionModuleId**. Ezért az üzenetek útválasztását a levél eszközök közvetlenül az IoT hubhoz, használja a következő útvonal modul üzenetek:
* `FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream`

### <a name="sink"></a>Fogadó
A fogadó határozza meg, ahol az üzenetek küldése történik-e. Ez a következő értékek egyike lehet:

| Fogadó | Leírás |
| ---- | ----------- |
| `$upstream` | Az üzenet küldése az IoT hubhoz |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | A bemeneti üzenet küldése `{input}` modul `{moduleId}` |

Fontos megjegyezni, hogy él hub: legalább egyszeri garanciákat nyújt, ami azt jelenti, hogy üzenetek helyi tárolásának abban az esetben egy útvonalat nem lehet kézbesíteni az üzenetet a fogadó, például a peremhálózati hub nem lehet csatlakozni az IoT-központ vagy a cél modul nincs csatlakoztatva.

Biztonsági központ tárolja az üzeneteket a megadott idő a `storeAndForwardConfiguration.timeToLiveSecs` tulajdonsága a [peremhálózati hub szükséges tulajdonságok](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Adja meg, vagy a kívánt tulajdonságainak frissítése 

Az üzembe helyezési jegyzék kívánt tulajdonságokat adhat meg a modul iker minden modul telepítve azon az IoT-peremhálózati eszközön. Ha az üzembe helyezési jegyzékben meg van adva a kívánt tulajdonságokkal, bármelyik kívánt tulajdonságot jelenleg a modul iker felülírja.

Ha egy modul iker kívánt tulajdonságok az üzembe helyezési jegyzékben nincs megadva, az IoT-központ nem módosítják a modul iker bármely olyan módon, és állítsa be a kívánt tulajdonságokat programozott módon lesz.

Ugyanazt a mechanizmust, amely lehetővé teszi az eszköz twins módosítása modul twins módosítására szolgálnak. Tekintse meg a [eszköz iker – útmutató fejlesztőknek](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins) további tájékoztatást talál.   

## <a name="deployment-manifest-example"></a>Manifest központi telepítés példája

Ez egy példa egy központi telepítési jegyzék JSON-dokumentum.

```json
{
"moduleContent": {
    "$edgeAgent": {
        "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
                "type": "docker",
                "settings": {
                    "minDockerVersion": "v1.25",
                    "loggingOptions": ""
                }
            },
            "systemModules": {
                "edgeAgent": {
                    "type": "docker",
                    "settings": {
                    "image": "microsoft/azureiotedge-agent:1.0-preview",
                    "createOptions": ""
                    }
                },
                "edgeHub": {
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-hub:1.0-preview",
                    "createOptions": ""
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
                    "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                    "createOptions": "{}"
                    }
                },
                "filtermodule": {
                    "version": "1.0",
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
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
                "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
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

* Vagy $edgeAgent és $edgeHub szereplő tulajdonságok teljes listáját lásd: [a peremhálózati ügynök és a peremhálózati hub tulajdonságainak](module-edgeagent-edgehub.md).

* Most, hogy tudja, hogyan IoT biztonsági modulok használata esetén [követelmények és eszközök IoT peremhálózati modulok adattárházzal][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
