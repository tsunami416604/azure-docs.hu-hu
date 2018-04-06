---
title: Az Azure IoT EdgeAgent és EdgeHub referencia |} Microsoft Docs
description: Tekintse át a megadott tulajdonságok és azok értékei a edgeAgent és edgeHub modul twins számára
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0971d5bba59ce3c7b1a6409ef3248f33a41e37c9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>A peremhálózati ügynök és a peremhálózati hub modul twins tulajdonságait

A peremhálózati ügynök és a peremhálózati hub is két az IoT-Edge futásidejű alkotó modulok. Minden modul végrehajt milyen feladatokat kapcsolatos további információkért lásd: [megismerése az Azure IoT peremhálózati futásidejű és az architektúra](iot-edge-runtime.md). 

Ez a cikk a kívánt tulajdonságokat és a futásidejű modul twins jelentett tulajdonságait tartalmazza. Lásd: [központi telepítési és figyelési] [ lnk-deploy] IoT peremeszközök modul telepítéséről további információt.

## <a name="edgeagent-desired-properties"></a>Szükségeskonfiguráció-EdgeAgent tulajdonságai

A modul iker az Edge ügynök nevezik `$edgeAgent` és koordinálja a peremhálózati ügynök fut egy eszközön, és az IoT-központ közötti kommunikáció. Kívánt tulajdonságai vannak beállítva, amikor egy üzembe helyezési jegyzék alkalmazza az adott eszközön single-eszköz vagy a skála központi telepítésének részeként. 

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
| modules.{moduleId}.version | Ez a modul verziója megfelelő, felhasználó által definiált karakterláncot. | Igen |
| modules.{moduleId}.type | "Docker" lehet. | Igen |
| modules.{moduleId}.restartPolicy | {"soha" \| "a-sikertelen" \| "meg-a nem megfelelő" \| "always"} | Igen |
| modules.{moduleId}.settings.image | A modul lemezkép URI. | Igen |
| modules.{moduleId}.settings.createOptions | A modul tároló létrehozásához a beállításokat tartalmazó stringified JSON. [Docker-beállítások létrehozása][lnk-docker-create-options] | Nem |
| modules.{moduleId}.configuration.id | A telepítésben Ez a modul telepített azonosítója. | Ezt állítja be az IoT-központ alkalmazásakor a jegyzékfájlban központi telepítéssel. Nem része egy üzembe helyezési jegyzékben. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent jelentett tulajdonságai

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
| lastDesiredVersion | Az egész utolsó hivatkozik a kívánt tulajdonságokkal dolgozza fel a peremhálózati ügynök verzióját. |
| lastDesiredStatus.code | Ez az az állapotkódot az Edge ügynök által látott utolsó kívánt tulajdonságokkal hivatkozik. Megengedett értékek: `200` sikeres, `400` Érvénytelen konfiguráció `412` érvénytelen séma verziója `417` a kívánt tulajdonságai nincsenek megadva, `500` sikertelen |
| lastDesiredStatus.description | Az állapot szöveges leírása |
| DeviceHealth | `healthy` Ha az összes modul futási állapotát `running` vagy `stopped`, `unhealthy` egyéb |
| configurationHealth.{deploymentId}.health | `healthy` Ha a futási állapotát a központi telepítés {deploymentId} által beállított összes modul `running` vagy `stopped`, `unhealthy` egyéb |
| runtime.platform.OS | Jelentéskészítés az eszközön futó operációs rendszer |
| runtime.platform.architecture | A Processzor architektúrájától Reporting az eszközön |
| systemModules.edgeAgent.runtimeStatus | A jelzett állapot peremhálózati ügynök: {"fut" \| "nem megfelelő"} |
| systemModules.edgeAgent.statusDescription | A peremhálózati ügynök jelentett állapotát a leírását. |
| systemModules.edgeHub.runtimeStatus | Biztonsági központ aktuális állapota: {"fut" \| "leállt" \| "sikertelen" \| "leállítási" \| "nem megfelelő"} |
| systemModules.edgeHub.statusDescription | Biztonsági központ sérült állapotba, ha az aktuális állapotát a leírását. |
| systemModules.edgeHub.exitCode | Ha kilépett, a peremhálózati hub tároló által jelentett a kilépési kód |
| systemModules.edgeHub.startTimeUtc | Ha a biztonsági központ utolsó elindításának ideje |
| systemModules.edgeHub.lastExitTimeUtc | Ha biztonsági központ utolsó kilépett idő |
| systemModules.edgeHub.lastRestartTimeUtc | Biztonsági központ utolsó újraindításakor idő |
| systemModules.edgeHub.restartCount | Ez a modul az újraindítási házirend részeként újraindult ennyiszer. |
| modules.{moduleId}.runtimeStatus | A modul aktuális állapota: {"fut" \| "leállt" \| "sikertelen" \| "leállítási" \| "nem megfelelő"} |
| modules.{moduleId}.statusDescription | A modul sérült állapotba, ha az aktuális állapotát a leírását. |
| modules.{moduleId}.exitCode | Ha kilépett, a modul tároló által jelentett a kilépési kód |
| modules.{moduleId}.startTimeUtc | Ha a modul utolsó elindításának ideje |
| modules.{moduleId}.lastExitTimeUtc | Ha a modul utolsó kilépett idő |
| modules.{moduleId}.lastRestartTimeUtc | Ha a modul utolsó újraindítása idő |
| modules.{moduleId}.restartCount | Ez a modul az újraindítási házirend részeként újraindult ennyiszer. |

## <a name="edgehub-desired-properties"></a>Szükségeskonfiguráció-EdgeHub tulajdonságai

A modul a két biztonsági központ nevezik `$edgeHub` és koordinálja a peremhálózati hub fut egy eszközön, és az IoT-központ közötti kommunikáció. Kívánt tulajdonságai vannak beállítva, amikor egy üzembe helyezési jegyzék alkalmazza az adott eszközön single-eszköz vagy a skála központi telepítésének részeként. 

| Tulajdonság | Leírás | Az üzembe helyezési jegyzékben kötelező |
| -------- | ----------- | -------- |
| sémaverzióval | "1.0" lehet. | Igen |
| útvonalak. a(z) {routeName} | Egy karakterlánc, amely egy peremhálózati hub útvonal. | A `routes` elem létezik, de üres lehet. |
| storeAndForwardConfiguration.timeToLiveSecs | Biztonsági központ tartja üzenetek esetén leválasztott útválasztási végpontok, például bontotta a kapcsolatot az IoT-központ vagy a helyi modul másodpercben | Igen |

## <a name="edgehub-reported-properties"></a>EdgeHub jelentett tulajdonságai

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Az egész utolsó hivatkozik a kívánt tulajdonságokkal dolgozza fel a peremhálózati hub verzióját. |
| lastDesiredStatus.code | Ez az az állapotkód: a biztonsági központ által látott utolsó kívánt tulajdonságokkal hivatkozik. Megengedett értékek: `200` sikeres, `400` Érvénytelen konfiguráció `500` sikertelen |
| lastDesiredStatus.description | Az állapot szöveges leírása |
| ügyfelek. {eszköz vagy modul identitás} .status | A kapcsolat állapota az eszköz vagy modul. A lehetséges értékek {"kapcsolódó" \| "leválasztott"}. Csak a modul azonosítókat leválasztott állapotban lehet. Peremhálózati központi csatlakozás eszközöket jelennek meg, csak a csatlakozáskor. |
| ügyfelek. {eszköz vagy modul identitás} .lastConnectTime | Utolsó idő az eszköz vagy a csatlakoztatott modul |
| ügyfelek. {eszköz vagy modul identitás} .lastDisconnectTime | Utolsó idő az eszköz vagy modul kapcsolata megszakadt |

## <a name="next-steps"></a>További lépések

Ezeket a tulajdonságokat az lehetővé teszi ki üzembe helyezési jegyzékben elmaradt, lásd: [megérteni, hogyan IoT peremhálózati modulok használják, konfigurálhatók, és használja fel újra](module-composition.md).

<!--links -->
[lnk-deploy]: module-deployment-monitoring.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
