---
title: Az Azure IoT Edge-modul összeállítását |} A Microsoft Docs
description: Ismerje meg, hogyan egy manifest nasazení kijelenti, mely modulok üzembe helyezéséhez, hogyan kell őket telepíteni, és közöttük üzenet útvonalak létrehozása.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a65eb029dbf10b194bd28bf7ad82f5aa839338a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990620"
---
# <a name="learn-how-to-use-deployment-manifests-to-deploy-modules-and-establish-routes"></a>Ismerje meg, hogyan telepítési jegyzékek használatával hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat

Minden IoT Edge-eszközön fut, legalább két modul: $edgeAgent és $edgeHub, amely az IoT Edge-futtatókörnyezet alkotó. Ezek szabványos két mellett minden IoT Edge-eszköz tetszőleges számú folyamatok végrehajtásához több modul is futtatható. Ha ezeket a modulokat egyszerre eszközre telepíti, melyik modulokat részét képezik, és kommunikáljanak egymással deklarálnia úgy kell. 

A *manifest nasazení* leíró JSON-dokumentumok:

* Az Edge agent, hogyan minden egyes modul kell létrehozni és felügyelni útmutatást, valamint a tároló rendszerképét az egyes modulok, a privát tárolójegyzékek hozzáférési hitelesítő adatokat tartalmazó konfigurációját.
* Az Edge hub, köztük hogyan flow az üzeneteket a modulok között, és végül az IoT Hub konfigurációját.
* Szükség esetén a kívánt tulajdonságainak az ikermodulokkal.

Minden IoT Edge-eszközök egy manifest nasazení konfigurálni kell. Egy újonnan telepített IoT Edge-futtatókörnyezet egy hibakódot, amíg nem egy érvényes jegyzékfájl konfigurált jelentések. 

Az Azure IoT Edge-oktatóanyagok és a egy manifest nasazení haladjon végig a varázsló az Azure IoT Edge-portálon létre. A manifest nasazení programozott módon, REST vagy az IoT Hub szolgáltatási SDK segítségével is alkalmazhat. További információkért lásd: [megismerheti az IoT Edge-telepítések][lnk-deploy].

## <a name="create-a-deployment-manifest"></a>Hozzon létre egy manifest nasazení

Magas szinten a manifest nasazení egy IoT Edge-eszközön üzembe helyezett IoT Edge-modulok a moduliker kívánt tulajdonságainak konfigurálása. Ezek a modulok két mindig találhatók: `$edgeAgent`, és `$edgeHub`.

A csak az IoT Edge-futtatókörnyezet (az ügynök és hub) tartalmazó manifest nasazení je platná.

A jegyzékfájl Ez a struktúra követi:

```json
{
    "modulesContent": {
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

Ossza meg az IoT Edge-futtatókörnyezet, az üzembe helyezés a modulok telepítése kell. Minden modul konfigurálással és felügyelettel kapcsolatos belül kerül a **$edgeAgent** kívánt tulajdonságot. Ez az információ az Edge agent magát a konfigurációs paramétereket tartalmazza. 

Vagy bele kell foglalni tulajdonságok teljes listáját lásd: [az Edge agentet és az Edge hub tulajdonságainak](module-edgeagent-edgehub.md).

Ez a struktúra hajtsa végre a $edgeAgent tulajdonságai:

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

## <a name="declare-routes"></a>Útvonalak deklarálása

Edge hubot lehetővé teszi a deklaratív irányíthatja a modulok között, és modulok és az IoT Hub közötti üzenetek. Az Edge hub az összes kommunikációt kezeli, ezért a belül kerül az útvonal-információkat a **$edgeHub** kívánt tulajdonságot. Több útvonal ugyanazon környezetben is rendelkezhet.

Útvonalak vannak deklarálva a **$edgeHub** kívánt tulajdonságot a következő szintaxissal:

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

Minden útvonal van szüksége, a forrás és a egy fogadó, de a feltétel egy választható elem használatával szűrik az üzeneteket. 


### <a name="source"></a>Forrás
A forrás Megadja, hogy honnan származnak az üzeneteket. Azt a következő értékek egyike lehet:

| Forrás | Leírás |
| ------ | ----------- |
| `/*` | Bármely eszköz vagy a modul az összes eszköz – felhő üzenetek |
| `/messages/*` | Bármely eszköz-felhő által küldött üzenet egy eszköz vagy egy modul át néhány vagy nincs kimenet |
| `/messages/modules/*` | Bármely eszköz-felhő által küldött üzenet egy modul át néhány vagy nincs kimenet |
| `/messages/modules/{moduleId}/*` | Bármely eszköz-felhő által küldött üzenet {moduleId} nem output parancsmaggal |
| `/messages/modules/{moduleId}/outputs/*` | Bármely eszköz-felhő által küldött üzenet {moduleId} néhány output parancsmaggal |
| `/messages/modules/{moduleId}/outputs/{output}` | Bármilyen eszközről a felhőbe üzenet elküldve {moduleId} a {kimeneti} |

### <a name="condition"></a>Állapot
A feltétel nem kötelező útvonal határozza meg. Ha azt szeretné, a fogadó üzenetek átadása a forrás, csak hagyja ki a **ahol** záradék teljes egészében. Vagy használhatja a [IoT Hub lekérdezési nyelv] [ lnk-iothub-query] szűrése az egyes üzenetek vagy üzenettípust, amely megfelel a feltételnek.

Az, hogy az IoT Edge moduljai közötti üzenetek ugyanaz, mint az, hogy az eszközök és az Azure IoT Hub közötti üzenetek vannak formázva. Összes üzenet formázott JSON-fájlként, és rendelkezik **systemProperties**, **appProperties**, és **törzs** paramétereket. 

Lekérdezések köré mindhárom paraméterrel a következő szintaxissal hozhat létre: 

* A Rendszertulajdonságok: `$<propertyName>` vagy `{$<propertyName>}`
* Alkalmazás tulajdonságai: `<propertyName>`
* Törzs tulajdonságai: `$body.<propertyName>` 

Az üzenet tulajdonságainak lekérdezések létrehozásával kapcsolatos további példákért lásd [útvonalak lekérdezési kifejezések eszköz – felhő üzenetek](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Adott IoT Edge-ben például akkor, ha levél eszközről átjáróeszköz érkezett üzenetek szűrését. Származó modulok üzeneteket tartalmaznak rendszer tulajdonsággal **connectionModuleId**. Ezért üzenetek továbbítását a levél eszközökről közvetlenül az IoT hubhoz, használja a következő útvonal kizárása a modul üzeneteket:

```sql
FROM /messages/\* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink (Fogadó)
A fogadó határozza meg, ahol az üzenetek érkeznek. Azt a következő értékek egyike lehet:

| Sink (Fogadó) | Leírás |
| ---- | ----------- |
| `$upstream` | Az üzenet elküldéséhez az IoT hubhoz |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | A bemeneti üzenet küldése `{input}` modul `{moduleId}` |

IoT Edge: legalább egyszeri garanciákat nyújt. Az Edge hub tárolja az üzeneteket, helyileg abban az esetben egy útvonalat a fogadó nem lehet kézbesíteni az üzenetet. Például ha az Edge hub nem tud csatlakozni az IoT Hub, vagy a célként megadott modul nem kapcsolódik.

Edge hub az üzeneteket, legfeljebb a megadott ideig tárolja a `storeAndForwardConfiguration.timeToLiveSecs` tulajdonságát a [Edge hubot kívánt tulajdonságok](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Adja meg, vagy az eszköz kívánt tulajdonságainak frissítése 

Manifest nasazení az IoT Edge-eszközön üzembe helyezett minden egyes modul az ikermodul kívánt tulajdonságokat adhat meg. Ha a kívánt tulajdonságok meg van adva, a központi telepítési jegyzékfájlban, felülírja a jelenleg a az ikermodul kívánt tulajdonságokat.

Manifest nasazení nem ad meg egy ikermodul kívánt tulajdonságok, ha az IoT Hub az ikermodul semmilyen módon nem módosítják, és állítsa be a kívánt tulajdonságokat programozott módon lesz.

Ugyanazt a mechanizmust, amelyek lehetővé teszik, hogy módosítsa az ikereszközök ikermodulokkal módosítására szolgálnak. További információkért lásd: a [device twin fejlesztői útmutató](../iot-hub/iot-hub-devguide-device-twins.md).   

## <a name="deployment-manifest-example"></a>Jegyzékfájl központi telepítés példája

A központi telepítési jegyzékfájl JSON-dokumentumok egy példát.

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

* Vagy $edgeAgent és $edgeHub szereplő tulajdonságok teljes listáját lásd: [az Edge agentet és az Edge hub tulajdonságainak](module-edgeagent-edgehub.md).

* Most, hogy megismerte, hogyan IoT Edge-modulok használata esetén [megismerhesse a követelményeket és az eszközök IoT Edge-modulok][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-routing-query-syntax.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
