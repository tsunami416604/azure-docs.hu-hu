---
title: Az Azure IoT peremhálózati modul-összeállítást |} Microsoft Docs
description: Ismerje meg, hogyan egy üzembe helyezési jegyzék deklarálja telepítése melyik modulokat, hogyan telepheti őket, és közöttük üzenet útvonalak létrehozásához.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 209f159d9003838edb36728828758b76730118ff
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098464"
---
# <a name="learn-how-to-use-deployment-manifests-to-deploy-modules-and-establish-routes"></a>Üzembe helyezési jegyzékben elmaradt modulok telepítése, és létrehozza a útvonalak használata

Minden egyes IoT peremhálózati eszköz futtatja legalább két modulokat: $edgeAgent és $edgeHub, az IoT-Edge futásidejű alkotó. Ezeket a szabványos két mellett minden IoT peremhálózati eszköz futtatható több modul folyamatok tetszőleges számú végrehajtásához. Ezek a modulok egyszerre egy eszközhöz telepíti, meg kell tudni, melyik modulokat, és hogyan léphetnek kapcsolatba egymással deklarálható. 

A *üzembe helyezési jegyzék* JSON-dokumentumok, amelyeket ismerteti:

* A konfiguráció a peremhálózati ügynök, beleértve a tároló-lemezkép minden modul, a hitelesítő adatok hozzáférési személyes tárolót nyilvántartó és hogyan modulokhoz kell létrehozni és felügyelt vonatkozó utasításokat.
* A peremhálózati hub, beleértve, hogyan üzenetek flow modulok között, és végül az IoT-központ konfigurációját.
* Szükség esetén a kívánt tulajdonságai a modul twins.

Minden IoT peremhálózati eszköz egy üzembe helyezési jegyzék konfigurálni kell. Egy újonnan telepített IoT peremhálózati futásidejű jelentést készít egy hibakódot, amíg a egy érvényes jegyzékfájl konfigurálva. 

Az Azure IoT peremhálózati oktatóanyagok hoz egy üzembe helyezési jegyzék haladjon végig a varázsló az Azure IoT peremhálózati portálon létre. Egy üzembe helyezési jegyzék programozott módon, REST- vagy a IoT Hub SDK is alkalmazhat. További információkért lásd: [megértéséhez IoT peremhálózati központi telepítések][lnk-deploy].

## <a name="create-a-deployment-manifest"></a>Hozzon létre egy üzembe helyezési jegyzék

Magas szinten az üzembe helyezési jegyzék konfigurálja az IoT peremhálózati modulok IoT peremhálózati eszköz a telepített egy modul iker kívánt tulajdonságait. Ezek a modulok két mindig találhatók: `$edgeAgent`, és `$edgeHub`.

Érvénytelen egy üzembe helyezési jegyzék, amely csak az IoT-Edge futásidejű (ügynök és hub) tartalmazza.

A jegyzékfájl Ez a struktúra követi:

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
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

Meg kell adnia az IoT-Edge futásidejű a modulok telepítése a központi telepítés. Minden modul konfigurációjának és kezelésének információi belül kerül a **$edgeAgent** szükséges tulajdonságai. Ezt az információt a peremhálózati ügynök önmagát konfigurációs paramétereket tartalmazza. 

Vagy szerepelnie kell függvénykötésnek tulajdonságok teljes listáját lásd: [a peremhálózati ügynök és a peremhálózati hub tulajdonságainak](module-edgeagent-edgehub.md).

A $edgeAgent tulajdonságok hajtsa végre ezt a struktúrát:

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
            "{module1}": { // optional
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

### <a name="condition"></a>Állapot
A feltétel nem kötelező megadni egy útvonal deklarációjában. Ha azt szeretné, a gyűjtő összes üzenetek átadása a forrás, hagyja a **ahol** záradék teljesen. Vagy használhatja a [IoT-központ lekérdezési nyelv] [ lnk-iothub-query] szűrése az egyes üzenetek vagy üzenettípusok, amelyek megfelelnek a következő feltételt.

Az üzeneteket, amelyek megfelelnek az IoT-Edge modulok között formázott ugyanaz, mint az üzeneteket, amelyek az eszközök és az Azure IoT-központ között. Az összes üzenet JSON-ként formázott, és rendelkezik **systemProperties**, **appProperties**, és **törzs** paraméterek. 

Összes három paraméter a következő szintaxissal körül lekérdezéseket hozhat létre: 

* A Rendszertulajdonságok: `$<propertyName>` vagy `{$<propertyName>}`
* Alkalmazás tulajdonságai: `<propertyName>`
* Törzs tulajdonságai: `$body.<propertyName>` 

Az üzenet tulajdonságainak lekérdezéséhez használatáról, tekintse meg a [útvonalak lekérdezési kifejezések eszközről a felhőbe üzenet](../iot-hub/iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).

IoT peremhálózati jellemző példa az, amikor egy levél eszközről egy átjáróeszköz érkező üzenetek szűréséhez. Modulok érkező üzenetek rendszer tulajdonságot tartalmaz **connectionModuleId**. Ezért az üzenetek útválasztását a levél eszközök közvetlenül az IoT hubhoz, használja a következő útvonal modul üzenetek:

```sql
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Fogadó
A fogadó határozza meg, ahol az üzenetek küldése történik-e. Ez a következő értékek egyike lehet:

| Fogadó | Leírás |
| ---- | ----------- |
| `$upstream` | Az üzenet küldése az IoT hubhoz |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | A bemeneti üzenet küldése `{input}` modul `{moduleId}` |

Az IoT él, legalább egyszeri garanciákat nyújt. A peremhálózati hub helyileg, ha egy olyan útvonalat nem lehet kézbesíteni az üzenetet a fogadó tárolja az üzeneteket. Például ha a biztonsági központ nem tud csatlakozni az IoT-központ vagy a cél modul nincs csatlakoztatva.

Biztonsági központ tárolja az üzeneteket a megadott idő a `storeAndForwardConfiguration.timeToLiveSecs` tulajdonsága a [peremhálózati hub szükséges tulajdonságok](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Adja meg, vagy a kívánt tulajdonságainak frissítése 

Az üzembe helyezési jegyzék kívánt tulajdonságokat adhat meg a modul iker minden modul telepítve azon az IoT-peremhálózati eszközön. Ha az üzembe helyezési jegyzékben meg van adva a kívánt tulajdonságokkal, bármelyik kívánt tulajdonságot jelenleg a modul iker felülírja.

Ha egy modul iker kívánt tulajdonságok az üzembe helyezési jegyzékben nincs megadva, az IoT-központ nem módosítják a modul iker bármely olyan módon, és állítsa be a kívánt tulajdonságokat programozott módon lesz.

Ugyanazt a mechanizmust, amely lehetővé teszi az eszköz twins módosítása modul twins módosítására szolgálnak. További információkért lásd: a [eszköz iker – útmutató fejlesztőknek](../iot-hub/iot-hub-devguide-device-twins.md).   

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
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "{password}",
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
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
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
