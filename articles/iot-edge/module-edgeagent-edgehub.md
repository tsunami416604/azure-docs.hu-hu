---
title: Az ügynök és a hub modul twins tulajdonságai – Azure IoT Edge
description: Tekintse át az edgeAgent és edgeHub modul twins adott tulajdonságait és értékeit
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f2d6603c264c9da3f2700f460a8c61b24681fac6
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546184"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Az IoT Edge-ügynök és az IoT Edge hub modul twins tulajdonságai

Az IoT Edge-ügynök és az IoT Edge hub két modul, amelyek az IoT Edge-futásidejű. Az egyes futásidejű modulok felelősségi köréről az [Azure IoT Edge-futásidő és az architektúra ismertetése](iot-edge-runtime.md)című témakörben talál további információt.

Ez a cikk a kívánt tulajdonságokat és jelentett tulajdonságait a futásidejű modul twins. A modulok IoT Edge-eszközökön való üzembe helyezéséről további információt [a Modulok üzembe helyezése és az IoT Edge-ben való útvonalak létrehozása című témakörben talál.](module-composition.md)

Az ikermodul a következőket tartalmazza:

* **A kívánt tulajdonságokat.** A megoldás háttérkezelője beállíthatja a kívánt tulajdonságokat, és a modul képes olvasni őket. A modul értesítést kaphat a kívánt tulajdonságok változásairól is. A kívánt tulajdonságok a jelentett tulajdonságokkal együtt használják a modul konfigurációjának vagy feltételeinek szinkronizálásához.

* **Jelentett tulajdonságok**. A modul beállíthatja a jelentett tulajdonságokat, és a megoldás háttérrendszer képes olvasni és lekérdezni őket. A jelentett tulajdonságok a kívánt tulajdonságokkal együtt használatosak a modul konfigurációjának vagy feltételeinek szinkronizálásához.

## <a name="edgeagent-desired-properties"></a>EdgeAgent kívánt tulajdonságai

Az IoT Edge-ügynök ikermodulját hívják meg, `$edgeAgent` és koordinálja az eszközön futó IoT Edge-ügynök és az IoT Hub közötti kommunikációt. A kívánt tulajdonságok vannak beállítva, ha egy központi telepítési jegyzékfájl egy adott eszközön egy eszköz részeként vagy egy-nagy méretű központi telepítés részeként.

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| sémaverzió | "1.0"-nak kell lennie. | Igen |
| runtime.type | Kell "docker" | Igen |
| runtime.settings.minDockerVersion | Állítsa be a központi telepítési jegyzékfájl által igényelt minimális Docker-verzióra | Igen |
| runtime.settings.loggingBeállítások | Az IoT Edge-ügynök tároló naplózási beállításait tartalmazó karakterláncos JSON. [Docker naplózási beállításai](https://docs.docker.com/engine/admin/logging/overview/) | Nem |
| runtime.settings.registryHitelesítő adatok<br>. {registryId}.felhasználónév | A tárolóbeállítás-jegyzék felhasználóneve. Az Azure Container Registry esetében a felhasználónév általában a rendszerleíró adatbázis neve.<br><br> A rendszerleíró adatbázis hitelesítő adatai minden privát modullemezhez szükségesek. | Nem |
| runtime.settings.registryHitelesítő adatok<br>. {registryId}.password | A tároló beállításjegyzékének jelszava. | Nem |
| runtime.settings.registryHitelesítő adatok<br>. {registryId}.address | A tárolóbeállításjegyzék címe. Az Azure Container Registry esetében a cím általában *{registry name}.azurecr.io*. | Nem |  
| systemModules.edgeAgent.type | Kell "docker" | Igen |
| systemModules.edgeAgent.settings.image | Az IoT Edge-ügynök rendszerképének URI-ja. Jelenleg az IoT Edge-ügynök nem tudja frissíteni magát. | Igen |
| systemModules.edgeAgent.settings<br>.createOptions | Az IoT Edge-ügynök tároló létrehozásának beállításait tartalmazó karakterláncos JSON. [Docker létrehozási beállításai](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nem |
| systemModules.edgeAgent.configuration.id | A modult telepítő központi telepítés azonosítója. | Az IoT Hub beállítja ezt a tulajdonságot, amikor a jegyzékfájl központi telepítés használatával van alkalmazva. Nem része a központi telepítési jegyzéknek. |
| systemModules.edgeHub.type | Kell "docker" | Igen |
| systemModules.edgeHub.status | Kell "futás" | Igen |
| systemModules.edgeHub.restartPolicy | "Mindig" kell lennie. | Igen |
| systemModules.edgeHub.settings.image | Az IoT Edge hub képének URI-ja. | Igen |
| systemModules.edgeHub.settings<br>.createOptions | Az IoT Edge hub tároló létrehozásának beállításait tartalmazó karakterláncos JSON. [Docker létrehozási beállításai](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nem |
| systemModules.edgeHub.configuration.id | A modult telepítő központi telepítés azonosítója. | Az IoT Hub beállítja ezt a tulajdonságot, amikor a jegyzékfájl központi telepítés használatával van alkalmazva. Nem része a központi telepítési jegyzéknek. |
| Modulok. {moduleId}.version | A modul verzióját jelképező, felhasználó által definiált karakterlánc. | Igen |
| Modulok. {moduleId}.típus | Kell "docker" | Igen |
| Modulok. {moduleId}.status | {"futás" \| "leállítva"} | Igen |
| Modulok. {moduleId}.restartPolicy | {"never" \| "on-failure" \| "on-unhealthy" \| "always"} | Igen |
| Modulok. {moduleId}.imagePullPolicy | {"on-create" \| "soha"} | Nem |
| Modulok. {moduleId}.env | A modulnak továbbítandó környezeti változók listája. A formátumot veszi`"<name>": {"value": "<value>"}` | Nem |
| Modulok. {moduleId}.settings.image | A modulkép URI-ja. | Igen |
| Modulok. {moduleId}.settings.createOptions | A modultároló létrehozásának lehetőségeit tartalmazó karakterláncos JSON. [Docker létrehozási beállításai](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nem |
| Modulok. {moduleId}.configuration.id | A modult telepítő központi telepítés azonosítója. | Az IoT Hub beállítja ezt a tulajdonságot, amikor a jegyzékfájl központi telepítés használatával van alkalmazva. Nem része a központi telepítési jegyzéknek. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent jelentett tulajdonságai

Az IoT Edge-ügynök jelentett tulajdonságai három fő információt tartalmaznak:

1. Az utolsó látható kívánt tulajdonságok alkalmazásának állapota;
2. Az eszközön jelenleg futó modulok állapota az IoT Edge-ügynök jelentése szerint; És
3. Az eszközön jelenleg futó kívánt tulajdonságok másolata.

Az aktuális kívánt tulajdonságok másolata hasznos annak megállapítására, hogy az eszköz alkalmazta-e a legújabb központi telepítést, vagy még mindig egy korábbi központi telepítési jegyzéket futtat.

> [!NOTE]
> Az IoT Edge-ügynök jelentett tulajdonságai hasznosak, mivel az [IoT Hub lekérdezési nyelvével](../iot-hub/iot-hub-devguide-query-language.md) lekérdezhetők a nagy méretű üzembe helyezések állapotának vizsgálatához. Az IoT Edge-ügynök tulajdonságainak állapothoz való használatáról az [IoT Edge-telepítések ismertetése egyetlen eszközökhöz vagy nagyméretekben című témakörben](module-deployment-monitoring.md)talál további információt.

Az alábbi táblázat nem tartalmazza a kívánt tulajdonságokból másolt információkat.

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Ez az egész szám az IoT Edge-ügynök által feldolgozott kívánt tulajdonságok utolsó verziójára utal. |
| lastDesiredStatus.kód | Ez az állapotkód az IoT Edge-ügynök által látott utolsó kívánt tulajdonságokra hivatkozik. Engedélyezett `200` értékek: `400` Sikeres, Érvénytelen `412` konfiguráció, Érvénytelen sémaverzió, `417` a `500` kívánt tulajdonságok üresek, Sikertelen |
| lastDesiredStatus.description | Az állapot szöveges leírása |
| deviceHealth (készülékEgészség) | `healthy`ha az összes modul futásidejű `running` állapota `stopped` `unhealthy` vagy, vagy , egyébként |
| configurationHealth (configurationHealth) {deploymentId}.health | `healthy`ha a(z) {deploymentId} központi telepítése által beállított `running` `stopped`összes `unhealthy` modul futásidejű állapota vagy , ellenkező esetben |
| runtime.platform.OS | Az eszközön futó operációs rendszer jelentése |
| runtime.platform.architektúra | A processzor architektúrájának jelentése az eszközön |
| systemModules.edgeAgent.runtimeStatus | Az IoT Edge-ügynök jelentett állapota: \| {"futás" "nem kifogástalan"} |
| systemModules.edgeAgent.statusDescription | Az IoT Edge-ügynök jelentett állapotának szöveges leírása. |
| systemModules.edgeHub.runtimeStatus | IoT Edge hub állapota: { \| "running" "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Az IoT Edge hub állapotának szöveges leírása, ha nem megfelelő állapotú. |
| systemModules.edgeHub.exitCode | Az IoT Edge hub tárolója által jelentett kilépési kód, ha a tároló kilép |
| systemModules.edgeHub.startTimeUtc | Az IoT Edge hub utolsó indításának időpontja |
| systemModules.edgeHub.lastExitTimeUtc | Az az idő, amikor az IoT Edge hub utoljára kilépett |
| systemModules.edgeHub.lastRestartTimeUtc | Az IoT Edge hub utolsó újraindításának időpontja |
| systemModules.edgeHub.restartCount fájl | Azon alkalmak száma, amikor ez a modul újraindult az újraindítási házirend részeként. |
| Modulok. {moduleId}.runtimeStatus | A modul állapota: { \| "running" \| "stopped" \| "failed" "backoff" \| "unhealthy" } |
| Modulok. {moduleId}.statusDescription | A modul állapotának szöveges leírása, ha nem megfelelő a megfelelő állapot. |
| Modulok. {moduleId}.exitCode | A modultároló által jelentett kilépési kód, ha a tároló kilép |
| Modulok. {moduleId}.startTimeUtc | A modul utolsó indításának időpontja |
| Modulok. {moduleId}.lastExitTimeUtc | A modul utolsó kilépésének időpontja |
| Modulok. {moduleId}.lastRestartTimeUtc | A modul utolsó újraindításának időpontja |
| Modulok. {moduleId}.restartCount | Azon alkalmak száma, amikor ez a modul újraindult az újraindítási házirend részeként. |

## <a name="edgehub-desired-properties"></a>Az EdgeHub kívánt tulajdonságai

Az IoT Edge hub ikermodulját hívják meg, `$edgeHub` és koordinálja az eszközön futó IoT Edge hub és az IoT Hub közötti kommunikációt. A kívánt tulajdonságok vannak beállítva, ha egy központi telepítési jegyzékfájl egy adott eszközön egy eszköz részeként vagy egy-nagy méretű központi telepítés részeként.

| Tulajdonság | Leírás | A központi telepítési jegyzékben szükséges |
| -------- | ----------- | -------- |
| sémaverzió | "1.0"-nak kell lennie. | Igen |
| Útvonalak. {routeName} | Egy IoT Edge-hub útvonalat jelölő karakterlánc. További információ: [Dedeclare routes](module-composition.md#declare-routes). | Az `routes` elem lehet jelen, de üres. |
| storeAndForwardConfiguration.timeToLiveSecs | Az Az az idő másodpercben, amely alatt az IoT Edge hub megtartja az üzeneteket, ha le választják az útválasztási végpontok, akár Az IoT Hub vagy egy helyi modul. Az érték bármilyen pozitív egész szám lehet. | Igen |

## <a name="edgehub-reported-properties"></a>EdgeHub jelentett tulajdonságai

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Ez az egész szám az IoT Edge hub által feldolgozott kívánt tulajdonságok utolsó verziójára utal. |
| lastDesiredStatus.kód | Az IoT Edge hub által látott utolsó kívánt tulajdonságokra hivatkozó állapotkód. Engedélyezett értékek: `200` `400` Sikeres, Érvénytelen `500` konfiguráció, Sikertelen |
| lastDesiredStatus.description | Az állapot szöveges leírása. |
| Ügyfelek. {device or moduleId}.status | Az eszköz vagy modul csatlakozási állapota. Lehetséges értékek {"csatlakoztatva" \| "leválasztva"}. Csak a modulidentitások lehetnek leválasztott állapotban. Az IoT Edge hubhoz csatlakozó alsóbb rétegbeli eszközök csak akkor jelennek meg, ha csatlakoztatva vannak. |
| Ügyfelek. {device or moduleId}.lastConnectTime | Utoljára csatlakoztatva az eszköz vagy a modul. |
| Ügyfelek. {device or moduleId}.lastDisconnectTime | Az eszköz vagy a modul utolsó leválasztásakor. |

## <a name="next-steps"></a>További lépések

Ha meg szeretné tudni, hogyan használhatja ezeket a tulajdonságokat a központi telepítési jegyzékek létrehozásához, [olvassa el az IoT Edge-modulok használatának, konfigurálásának és újrafelhasználásának ismertetése.](module-composition.md)
