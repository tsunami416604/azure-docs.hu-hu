---
title: "Az Azure IoT peremhálózati modul-összeállítást |} Microsoft Docs"
description: "Ismerje meg, mi az Azure IoT peremhálózati modulok kerül, és hogyan felhasználhatók"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6f9ca3d9b0f41210a3f43a8ae505f0a90b130b34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Megérteni, hogyan használható a IoT peremhálózati modulok, konfigurálva, és használja fel újra – előzetes

Azure IoT peremhálózati lehetővé teszi, mielőtt telepítené őket az IoT-peremeszközök több IoT Edge-modul írása. Ez a cikk ismerteti a legfontosabb fogalmak körül létrehozása több IoT Edge-modulok telepítése előtt. 

## <a name="the-deployment-manifest"></a>Az üzembe helyezési jegyzék
A *üzembe helyezési jegyzék* JSON-dokumentumok, amelyeket ismerteti:

* Mely IoT peremhálózati modulokat kell telepíteni, azok létrehozását és a kezelési lehetőségek; együtt
* A peremhálózati hub, hogyan kell flow üzenetek modulok között, valamint modulok és az IoT-központ; leíró konfigurációja
* Másik lehetőségként értékek beállítása a modul twins kívánt tulajdonságait konfigurálhatja az egyes modul alkalmazásokat.

Az Azure IoT peremhálózati oktatóanyagok hoz egy üzembe helyezési jegyzék haladjon végig a varázsló az Azure IoT peremhálózati portálon létre. Egy üzembe helyezési jegyzék programozott módon, REST- vagy a IoT Hub SDK is alkalmazhat. Tekintse meg [telepítés és figyelés] [ lnk-deploy] IoT peremhálózati központi telepítések további információt.

Magas szinten az üzembe helyezési jegyzék konfigurálja a kívánt tulajdonságokkal IoT peremhálózati eszköz a telepített IoT peremhálózati modulok. Ezek a modulok két mindig találhatók: a peremhálózati ügynök és a peremhálózati központ.

A jegyzékfájl Ez a struktúra követi:

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
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

Ez a szakasz végén jelenteni egy üzembe helyezési jegyzék példát.

> [!IMPORTANT]
> Minden IoT peremhálózati eszköz egy üzembe helyezési jegyzék konfigurálni kell. Egy újonnan telepített IoT peremhálózati futásidejű jelentést készít egy hibakódot, amíg a egy érvényes jegyzékfájl konfigurálva. 

### <a name="specify-the-modules"></a>Adja meg a modulok
A modul iker a peremhálózati ügynök kívánt tulajdonságait tartalmazza: a kívánt modulok, a konfigurációs és felügyeleti beállítások, a peremhálózati ügynök konfigurációs paraméterek együtt.

Magas szinten ez a szakasz a jegyzékfájl hivatkozik a modul képeket és az IoT-Edge futásidejű modulok (ügynök Edge és biztonsági központ) és a felhasználó által megadott modulok felügyeleti beállításokat.

Tekintse meg a [szükséges a peremhálózati ügynök tulajdonságainak] [ lnk-edgeagent-desired] ebben a szakaszban a jegyzékfájl részletes leírását.

> [!NOTE]
> A csak az IoT-Edge futásidejű (ügynök és hub) tartalmazó üzembe helyezési jegyzék érvénytelen.


### <a name="specify-the-routes"></a>Adja meg az útvonalakat
Biztonsági központ biztosítja az deklaratív módon irányítja az üzenetek modulokat, valamint modulok és az IoT-központ között.

Útvonalak rendelkezik a következő szintaxist:

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

A *forrás* bármilyen, a következőket:

| Forrás | Leírás |
| ------ | ----------- |
| `/*` | Minden eszközt vagy modul érkező üzenetek eszközről a felhőbe |
| `/messages/*` | Bármely eszközről a felhőbe által küldött üzenet egy eszköz vagy egy modul néhány vagy nincs kimeneti keresztül |
| `/messages/modules/*` | Bármely eszközről a felhőbe által küldött üzenet keresztül néhány vagy nincs kimeneti modul |
| `/messages/modules/{moduleId}/*` | Bármely eszközről a felhőbe által küldött üzenet {moduleId} nincs kimenet |
| `/messages/modules/{moduleId}/outputs/*` | Bármely eszközről a felhőbe által küldött üzenet {moduleId} néhány kimenet |
| `/messages/modules/{moduleId}/outputs/{output}` | Bármely eszközről a felhőbe üzenet {moduleId} a {kimeneti} |

A feltétel által támogatott bármely feltétel lehet a [IoT-központ lekérdezési nyelv] [ lnk-iothub-query] az IoT-központ útválasztási szabályokat.

A gyűjtő a következők egyike lehet:

| a fogadó | Leírás |
| ---- | ----------- |
| `$upstream` | Az üzenet küldése az IoT hubhoz |
| `BrokeredEndpoint(/modules/{moduleId}/inputs/{input})` | A bemeneti üzenet küldése `{input}` modul`{moduleId}` |

Fontos megjegyezni, hogy él hub: legalább egyszeri garanciákat nyújt, ami azt jelenti, hogy üzenetek helyben lesz tárolva abban az esetben egy útvonalat nem lehet kézbesíteni az üzenetet a fogadó, pl. a peremhálózati hub nem lehet csatlakozni az IoT-központ vagy a cél modul nincs csatlakoztatva.

Biztonsági központ tárolja az üzeneteket a megadott idő a `storeAndForwardConfiguration.timeToLiveSecs` az Edge központnak tulajdonság szükséges tulajdonságai.

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>A modul iker kívánt tulajdonságainak megadása

Az üzembe helyezési jegyzék adhatja meg a modul a két ügynök Edge szakaszában megadott felhasználói modulok mindegyikének kívánt tulajdonságait.

Ha az üzembe helyezési jegyzékben meg van adva a kívánt tulajdonságokkal, bármelyik kívánt tulajdonságot jelenleg a modul iker felülírja.

Ha egy modul iker kívánt tulajdonságok az üzembe helyezési jegyzékben nincs megadva, az IoT-központ nem módosítják a modul iker bármely olyan módon, és állítsa be a kívánt tulajdonságokat programozott módon lesz.

### <a name="deployment-manifest-example"></a>Manifest központi telepítés példája

Ez egy példa egy központi telepítési jegyzék JSON-dokumentum.

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

## <a name="reference-edge-agent-module-twin"></a>Útmutató: Biztonsági ügynök modul iker

A modul iker az Edge ügynök nevezik `$edgeAgent` és koordinálja a peremhálózati ügynök fut egy eszközön, és az IoT-központ közötti kommunikáció.
Kívánt tulajdonságai vannak beállítva, amikor egy üzembe helyezési jegyzék alkalmazza az adott eszközön single-eszköz vagy a skála központi telepítésének részeként. Lásd: [központi telepítési és figyelési] [ lnk-deploy] IoT peremeszközök modul telepítéséről további információt.

### <a name="edge-agent-twin-desired-properties"></a>Peremhálózati szükséges iker tulajdonságai

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| sémaverzióval | "1.0" lehet. | Igen |
| Runtime.Type | "Docker" lehet. | Igen |
| runtime.settings.minDockerVersion | Ez az üzembe helyezési jegyzék által megkövetelt minimális Docker verzió beállítása | Igen |
| runtime.settings.loggingOptions | A naplózási beállításokat, a peremhálózati ügynök tároló tartalmazó stringified JSON. [Docker naplózási beállítások][lnk-docker-logging-options] | Nem |
| systemModules.edgeAgent.type | "Docker" lehet. | Igen |
| systemModules.edgeAgent.settings.image | Az URI-je a peremhálózati ügynök képe. A peremhálózati ügynök jelenleg nem tudja frissíteni a saját magát. | Igen |
| systemModules.edgeAgent.settings.createOptions | A peremhálózati ügynök tároló létrehozásához a beállításokat tartalmazó stringified JSON. [Docker-beállítások létrehozása][lnk-docker-create-options] | Nem |
| systemModules.edgeAgent.configuration.id | A telepítésben Ez a modul telepített azonosítója. | Ezt állítja be az IoT-központ alkalmazásakor a jegyzékfájlban központi telepítéssel. Nem része egy üzembe helyezési jegyzékben. |
| systemModules.edgeHub.type | "Docker" lehet. | Igen |
| systemModules.edgeHub.status | A "fut" rendelkezik | Igen |
| systemModules.edgeHub.restartPolicy | Kell lennie a "mindig" | Igen |
| systemModules.edgeHub.settings.image | Az él központnak a lemezkép URI. | Igen |
| systemModules.edgeHub.settings.createOptions | A peremhálózati hub tároló létrehozásához a beállításokat tartalmazó stringified JSON. [Docker-beállítások létrehozása][lnk-docker-create-options] | Nem |
| systemModules.edgeHub.configuration.id | A telepítésben Ez a modul telepített azonosítója. | Ezt állítja be az IoT-központ alkalmazásakor a jegyzékfájlban központi telepítéssel. Nem része egy üzembe helyezési jegyzékben. |
| modulok. {moduleId} .version | Ez a modul verziója megfelelő, felhasználó által definiált karakterláncot. | Igen |
| modulok. {moduleId} .type | "Docker" lehet. | Igen |
| modulok. {moduleId} .restartPolicy | {"soha" \| "a-sikertelen" \| "az-a nem megfelelő" \| "always"} | Igen |
| modulok. {moduleId}.settings.image | A modul lemezkép URI. | Igen |
| modulok. {moduleId}.settings.createOptions | A modul tároló létrehozásához a beállításokat tartalmazó stringified JSON. [Docker-beállítások létrehozása][lnk-docker-create-options] | Nem |
| modulok. {moduleId}.configuration.id | A telepítésben Ez a modul telepített azonosítója. | Ezt állítja be az IoT-központ alkalmazásakor a jegyzékfájlban központi telepítéssel. Nem része egy üzembe helyezési jegyzékben. |

### <a name="edge-agent-twin-reported-properties"></a>Peremhálózati ügynök iker jelentett tulajdonságai

A peremhálózati ügynök jelentett tulajdonságai közé tartozik a három fő adatra:

1. A legutóbbi látható kívánt tulajdonságainak; alkalmazás állapotának
2. A modulok jelenleg fut az eszközön, a peremhálózati ügynök; által jelentett állapota és
3. A kívánt tulajdonságokkal, az eszközön futó másolata.

Az utolsó adat, akkor hasznos, abban az esetben a legújabb kívánt tulajdonságok nem alkalmazása sikeresen megtörtént a futtatókörnyezet, és az eszköz még fut egy előző üzembe helyezési jegyzékben.

> [!NOTE]
> A peremhálózati ügynök jelentett tulajdonságainak hasznosak, a lekérdezhetők a [IoT-központ lekérdezési nyelv] [ lnk-iothub-query] vizsgálja meg a léptékű telepítések állapotát. Tekintse meg [központi telepítések] [ lnk-deploy] ezzel a szolgáltatással kapcsolatos további információt.

A következő táblázat az információt, amely a kívánt tulajdonságokkal átmásolva nem tartalmaz.

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Az int utolsó hivatkozik a kívánt tulajdonságokkal dolgozza fel a peremhálózati ügynök verzióját. |
| lastDesiredStatus.code | Ez az az állapotkódot az Edge ügynök által látott utolsó kívánt tulajdonságokkal hivatkozik. Megengedett értékek: `200` sikeres, `400` Érvénytelen konfiguráció `412` érvénytelen séma verziója `417` a kívánt tulajdonságai nincsenek megadva, `500` sikertelen |
| lastDesiredStatus.description | Az állapot szöveges leírása |
| DeviceHealth | `healthy`Ha az összes modul futási állapotát `running` vagy `stopped`, `unhealthy` egyéb |
| configurationHealth. {deploymentId} .health | `healthy`Ha a futási állapotát a központi telepítés {deploymentId} által beállított összes modul `running` vagy `stopped`, `unhealthy` egyéb |
| runtime.platform.OS | Jelentéskészítés az eszközön futó operációs rendszer |
| Runtime.platform.Architecture | A Processzor architektúrájától Reporting az eszközön |
| systemModules.edgeAgent.runtimeStatus | A jelzett állapot peremhálózati ügynök: {"fut" \| "nem megfelelő"} |
| systemModules.edgeAgent.statusDescription | A peremhálózati ügynök jelentett állapotát a leírását. |
| systemModules.edgeHub.runtimeStatus | Biztonsági központ aktuális állapota: {"fut" \| "leállt" \| "sikertelen" \| "leállítási" \| "nem megfelelő"} |
| systemModules.edgeHub.statusDescription | Biztonsági központ sérült állapotba, ha az aktuális állapotát a leírását. |
| systemModules.edgeHub.exitCode | Ha kilépett, a peremhálózati hub tároló által jelentett a kilépési kód |
| systemModules.edgeHub.startTimeUtc | Ha a biztonsági központ utolsó elindításának ideje |
| systemModules.edgeHub.lastExitTimeUtc | Ha biztonsági központ utolsó kilépett idő |
| systemModules.edgeHub.lastRestartTimeUtc | Biztonsági központ utolsó újraindításakor idő |
| systemModules.edgeHub.restartCount | Ez a modul az újraindítási házirend részeként újraindult ennyiszer. |
| modulok. {moduleId} .runtimeStatus | A modul aktuális állapota: {"fut" \| "leállt" \| "sikertelen" \| "leállítási" \| "nem megfelelő"} |
| modulok. {moduleId} .statusDescription | A modul sérült állapotba, ha az aktuális állapotát a leírását. |
| modulok. {moduleId} .exitCode | Ha kilépett, a modul tároló által jelentett a kilépési kód |
| modulok. {moduleId} .startTimeUtc | Ha a modul utolsó elindításának ideje |
| modulok. {moduleId} .lastExitTimeUtc | Ha a modul utolsó kilépett idő |
| modulok. {moduleId} .lastRestartTimeUtc | Ha a modul utolsó újraindítása idő |
| modulok. {moduleId} .restartCount | Ez a modul az újraindítási házirend részeként újraindult ennyiszer. |

## <a name="reference-edge-hub-module-twin"></a>Útmutató: Peremhálózati hub modul iker

A modul a két biztonsági központ nevezik `$edgeHub` és koordinálja a peremhálózati hub fut egy eszközön, és az IoT-központ közötti kommunikáció.
Kívánt tulajdonságai vannak beállítva, amikor egy üzembe helyezési jegyzék alkalmazza az adott eszközön single-eszköz vagy a skála központi telepítésének részeként. Lásd: [központi telepítések] [ lnk-deploy] IoT peremeszközök modul telepítéséről további információt.

### <a name="edge-hub-twin-desired-properties"></a>Biztonsági központ szükséges iker tulajdonságai

| Tulajdonság | Leírás | Az üzembe helyezési jegyzékben kötelező |
| -------- | ----------- | -------- |
| sémaverzióval | "1.0" lehet. | Igen |
| útvonalak. a(z) {routeName} | Egy karakterlánc, amely egy peremhálózati hub útvonal. | A `routes` elem létezik, de üres lehet. |
| storeAndForwardConfiguration.timeToLiveSecs | Biztonsági központ szolgáltatás a másodpercben üzenetek esetén leválasztott útválasztási végpontok, pl. bontotta a kapcsolatot az IoT-központ vagy a helyi modul | Igen |

### <a name="edge-hub-twin-reported-properties"></a>Biztonsági központ iker jelentett tulajdonságai

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Az int utolsó hivatkozik a kívánt tulajdonságokkal dolgozza fel a peremhálózati hub verzióját. |
| lastDesiredStatus.code | Ez az az állapotkód: a biztonsági központ által látott utolsó kívánt tulajdonságokkal hivatkozik. Megengedett értékek: `200` sikeres, `400` Érvénytelen konfiguráció `500` sikertelen |
| lastDesiredStatus.description | Az állapot szöveges leírása |
| ügyfelek. {eszköz vagy modul identitás} .status | A kapcsolat állapota az eszköz vagy modul. A lehetséges értékek {"kapcsolódó" \| "leválasztott"}. Csak a modul azonosítókat leválasztott állapotban lehet. Peremhálózati központi csatlakozás eszközöket jelennek meg, csak a csatlakozáskor. |
| ügyfelek. {eszköz vagy modul identitás} .lastConnectTime | Utolsó idő az eszköz vagy a csatlakoztatott modul |
| ügyfelek. {eszköz vagy modul identitás} .lastDisconnectTime | Utolsó idő az eszköz vagy modul kapcsolata megszakadt |

## <a name="next-steps"></a>Következő lépések

Most, hogy tudja, hogyan IoT biztonsági modulok használata esetén [követelmények és eszközök IoT peremhálózati modulok adattárházzal][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
