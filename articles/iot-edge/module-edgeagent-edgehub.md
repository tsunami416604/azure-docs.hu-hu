---
title: Az Azure IoT EdgeAgent és EdgeHub leírása |} A Microsoft Docs
description: Tekintse át az adott tulajdonságok és azok értékei a edgeAgent és edgeHub ikermodulokkal
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 259d61125828ee487b74daa525f3635cfa592ce7
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017704"
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Az Edge agentet és az Edge hub ikermodulokkal tulajdonságai

Az Edge agentet és az Edge hub az IoT Edge-futtatókörnyezet alkotó két modult. Minden modul hajt végre milyen feladatokat kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md). 

Ez a cikk a kívánt tulajdonságok és a futtatókörnyezet ikermodulokkal jelentett tulajdonságait. A modulok IoT Edge-eszközökön üzembe helyezése további információkért lásd: [központi telepítési és figyelési][lnk-deploy].

## <a name="edgeagent-desired-properties"></a>EdgeAgent desired tulajdonságai

Az Edge Agent ikermodulja nevezzük `$edgeAgent` és koordinálja a kommunikációt az Edge agent fut egy eszközön, és az IoT Hub között. A kívánt tulajdonságok vannak beállítva, a manifest nasazení alkalmazása az adott eszközön egy egyetlen eszközök vagy ipari méretekben központi telepítésének részeként esetén. 

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| sémaverzióval | "1.0" kell lennie | Igen |
| Runtime.Type | Kell lennie a "docker" | Igen |
| runtime.settings.minDockerVersion | Állítsa be a manifest nasazení által megkövetelt minimális Docker verzióra | Igen |
| runtime.settings.loggingOptions | Az Edge agent tároló a naplózási beállításokat tartalmazó sztringesített JSON. [Docker naplózási lehetőségek][lnk-docker-logging-options] | Nem |
| runtime.settings.registryCredentials<br>. {registryId} .username | A tároló-beállításjegyzék felhasználóneve. Az Azure Container Registry esetében a felhasználónév általában a beállításjegyzék nevét.<br><br> A tárolójegyzék hitelesítő adatainak szükségesek, amelyek nem nyilvános modul képeket. | Nem |
| runtime.settings.registryCredentials<br>. {registryId} .password | A tárolóregisztrációs adatbázis jelszava. | Nem |
| runtime.settings.registryCredentials<br>. {registryId} .address | A tárolóregisztrációs adatbázis címe. Az Azure Container Registry esetében a címe általában *{registryname}.azurecr.IO/Azure-vote-Front:V1*. | Nem |  
| systemModules.edgeAgent.type | Kell lennie a "docker" | Igen |
| systemModules.edgeAgent.settings.image | Az URI-ját az Edge agent képe. Az Edge agent jelenleg nem lehet frissíteni az magát. | Igen |
| systemModules.edgeAgent.settings<br>.createOptions | Az Edge agent tároló létrehozását, a beállításokat tartalmazó sztringesített JSON. [Docker-létrehozási beállítások][lnk-docker-create-options] | Nem |
| systemModules.edgeAgent.configuration.id | Az üzemelő példány, amelyre ez a modul telepítve azonosítója. | Ez a tulajdonság értéke az IoT Hub alkalmazásakor a jegyzékfájlban egy üzemelő példány használatával. Nem része egy manifest nasazení. |
| systemModules.edgeHub.type | Kell lennie a "docker" | Igen |
| systemModules.edgeHub.status | "Futnia kell" | Igen |
| systemModules.edgeHub.restartPolicy | Kell lennie a "mindig" | Igen |
| systemModules.edgeHub.settings.image | Az URI-ját az Edge hub képe. | Igen |
| systemModules.edgeHub.settings<br>.createOptions | Az Edge hub tároló létrehozását, a beállításokat tartalmazó sztringesített JSON. [Docker-létrehozási beállítások][lnk-docker-create-options] | Nem |
| systemModules.edgeHub.configuration.id | Az üzemelő példány, amelyre ez a modul telepítve azonosítója. | Ez a tulajdonság értéke az IoT Hub alkalmazásakor a jegyzékfájlban egy üzemelő példány használatával. Nem része egy manifest nasazení. |
| modules.{moduleId}.version | Egy felhasználó által megadott karakterlánc, amely a modul verzióját. | Igen |
| modulok. {moduleId} .type | Kell lennie a "docker" | Igen |
| modulok. {moduleId} .status | {"fut" \| "leállított"} | Igen |
| modulok. {moduleId} .restartPolicy | {"soha" \| "a – nem sikerült" \| "a – nem megfelelő állapotú" \| "mindig"} | Igen |
| modules.{moduleId}.settings.image | A modul kép URI Azonosítóját. | Igen |
| modules.{moduleId}.settings.createOptions | A modul tároló létrehozását, a beállításokat tartalmazó sztringesített JSON. [Docker-létrehozási beállítások][lnk-docker-create-options] | Nem |
| modules.{moduleId}.configuration.id | Az üzemelő példány, amelyre ez a modul telepítve azonosítója. | Ez a tulajdonság értéke az IoT Hub alkalmazásakor a jegyzékfájlban egy üzemelő példány használatával. Nem része egy manifest nasazení. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent jelentett tulajdonságok

Az Edge agent jelentett tulajdonságok három fő információt tartalmaznak:

1. Utolsó észlelés kívánt tulajdonságok; az alkalmazás állapota
2. A modulok jelenleg fut az eszközön, az Edge agent; által jelentett módon állapota és
3. Jelenleg fut az eszköz kívánt tulajdonságait másolatát.

Az utolsó adat, akkor hasznos, abban az esetben a legújabb kívánt tulajdonságok nem alkalmazása sikeresen futásidőben, és az eszköz továbbra is fut egy előző manifest nasazení.

> [!NOTE]
> A jelentett tulajdonságok az Edge Agent hasznosak lehetnek, a lekérdezhetők a [IoT Hub lekérdezési nyelv] [ lnk-iothub-query] vizsgálhatja a nagy mennyiségű központi telepítések állapotát. Az Edge agent tulajdonságai állapot használatával további információkért lásd: [ismertetése IoT Edge-telepítések egyetlen eszközök vagy ipari méretekben][lnk-deploy].

A következő táblázat nem tartalmazza a kívánt tulajdonságok, másolja az információkat.

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Egész szám lehet a kívánt tulajdonságokkal dolgozza fel az Edge agent verzióját az utolsó hivatkozik. |
| lastDesiredStatus.code | Ez az az állapotkódot az Edge agent által látott utolsó kívánt tulajdonságok címre. Megengedett értékek: `200` sikeres `400` érvénytelen konfigurációt `412` érvénytelen verziójú, `417` a kívánt tulajdonságokkal is üres, `500` sikertelen |
| lastDesiredStatus.description | Az állapot leírása |
| deviceHealth | `healthy` Ha az összes modul futásidejű állapotát `running` vagy `stopped`, `unhealthy` egyéb |
| configurationHealth.{deploymentId}.health | `healthy` Ha a központi telepítés {deploymentId} által beállított összes modul futási állapotát `running` vagy `stopped`, `unhealthy` egyéb |
| runtime.platform.OS | Jelentéskészítés az eszközön futó operációs rendszer |
| Runtime.platform.Architecture | Az architektúra a processzor Reporting az eszközön |
| systemModules.edgeAgent.runtimeStatus | Az Edge agent jelentett állapota: {"fut" \| "nem kifogástalan"} |
| systemModules.edgeAgent.statusDescription | Az Edge agent jelentett állapota szöveges leírása. |
| systemModules.edgeHub.runtimeStatus | Az Edge hub az aktuális állapota: {"fut" \| "leállított" \| "sikertelen" \| "leállítási" \| "nem kifogástalan"} |
| systemModules.edgeHub.statusDescription | Ha nem megfelelő állapotú Edge hub az aktuális állapotát szöveges leírása. |
| systemModules.edgeHub.exitCode | Ha kilépett a kilépési kód az Edge hub tároló által jelentett |
| systemModules.edgeHub.startTimeUtc | Amikor Edge hubot legutóbbi Indítás ideje |
| systemModules.edgeHub.lastExitTimeUtc | Amikor Edge hubot utolsó kilépett idő |
| systemModules.edgeHub.lastRestartTimeUtc | Idő, amikor Edge hubot utolsó újraindítása |
| systemModules.edgeHub.restartCount | Ez a modul újra lett indítva, az újraindítási házirend részeként hányszor. |
| modules.{moduleId}.runtimeStatus | A modul aktuális állapota: {"fut" \| "leállított" \| "sikertelen" \| "leállítási" \| "nem kifogástalan"} |
| modules.{moduleId}.statusDescription | Az aktuális állapotát a modult, ha a nem megfelelő állapotú szöveges leírása. |
| modulok. {moduleId} .exitCode | Ha kilépett a modul tároló által jelentett a kilépési kód |
| modules.{moduleId}.startTimeUtc | Ha a modul legutóbbi Indítás ideje |
| modules.{moduleId}.lastExitTimeUtc | Ha a modul utolsó kilépett idő |
| modules.{moduleId}.lastRestartTimeUtc | Ha a modul utolsó újraindítása idő |
| modules.{moduleId}.restartCount | Ez a modul újra lett indítva, az újraindítási házirend részeként hányszor. |

## <a name="edgehub-desired-properties"></a>EdgeHub desired tulajdonságai

Az Edge hub az ikermodul nevezzük `$edgeHub` és koordinálja a kommunikációt az Edge hub fut egy eszközön, és az IoT Hub között. A kívánt tulajdonságok vannak beállítva, a manifest nasazení alkalmazása az adott eszközön egy egyetlen eszközök vagy ipari méretekben központi telepítésének részeként esetén. 

| Tulajdonság | Leírás | Manifest nasazení szükséges |
| -------- | ----------- | -------- |
| sémaverzióval | "1.0" kell lennie | Igen |
| útvonalak. {routeName} | Az Edge hub útvonal képviselő karakterláncot. | A `routes` elem létezik, de üres is lehet. |
| storeAndForwardConfiguration.timeToLiveSecs | Az időtartam másodpercben, amely Edge hubot tartja üzenetek esetén leválasztott útválasztási végpontok, például leválasztja az IoT Hub, vagy a helyi modul | Igen |

## <a name="edgehub-reported-properties"></a>EdgeHub jelentett tulajdonságok

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Egész szám lehet a kívánt tulajdonságok, az Edge hub által feldolgozott verzióját az utolsó hivatkozik. |
| lastDesiredStatus.code | Ez az az állapotkódot az Edge hub által látott utolsó kívánt tulajdonságok címre. Megengedett értékek: `200` sikeres `400` érvénytelen konfigurációt `500` sikertelen |
| lastDesiredStatus.description | Az állapot leírása |
| ügyfelek számára. {eszköz vagy moduleId} .status | A kapcsolat állapota az eszköz vagy a modul. Lehetséges értékei {"csatlakoztatva" \| "leválasztott"}. Csak a modul identitások leválasztott állapotban is lehetnek. Kapcsolódás az Edge hub alsóbb rétegbeli eszközök csak akkor, ha a csatlakoztatott jelennek meg. |
| ügyfelek számára. {eszköz vagy moduleId} .lastConnectTime | Utolsó időpont, amikor az eszköz vagy a csatlakoztatott |
| ügyfelek számára. {eszköz vagy moduleId} .lastDisconnectTime | Utolsó idő az eszköz vagy a modul kapcsolata |

## <a name="next-steps"></a>További lépések

Ezek a tulajdonságok használatával hozhatja létre a központi telepítési jegyzékek kapcsolatban lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).

<!--links -->
[lnk-deploy]: module-deployment-monitoring.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
