---
title: EdgeAgent és EdgeHub kívánt tulajdonságok referencia – Azure IoT Edge |} A Microsoft Docs
description: Tekintse át az adott tulajdonságok és azok értékei a edgeAgent és edgeHub ikermodulokkal
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ab45a6bde9ead69a7ea23dd095de84b8ff01334
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456705"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>A IoT Edge-ügynök és az IoT Edge hub-modulok ikrek tulajdonságai

A IoT Edge ügynök és a IoT Edge hub két, a IoT Edge futtatókörnyezetet alkotó modul. További információ arról, hogy az egyes modulok milyen feladatokat végeznek el: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md). 

Ez a cikk a kívánt tulajdonságok és a futtatókörnyezet ikermodulokkal jelentett tulajdonságait. A modulok IoT Edge eszközökön való telepítésével kapcsolatos további információkért lásd: [modulok üzembe helyezése és útvonalak létrehozása IoT Edgeokban](module-composition.md).

A modulok Twin-tartalma: 

* **Kívánt tulajdonságok**. A megoldás háttere beállíthatja a kívánt tulajdonságokat, a modul pedig elolvashatja őket. A modul a kívánt tulajdonságok változásairól is fogadhat értesítéseket. A kívánt tulajdonságok a jelentett tulajdonságokkal együtt használhatók a modul konfigurációjának vagy feltételeinek szinkronizálásához.

* **Jelentett tulajdonságok**. A modul beállíthatja a jelentett tulajdonságokat, és a megoldás háttere olvasható és kérdezhető le. A jelentett tulajdonságokat a rendszer a kívánt tulajdonságokkal együtt használja a modul konfigurációjának vagy feltételeinek szinkronizálásához. 

## <a name="edgeagent-desired-properties"></a>EdgeAgent desired tulajdonságai

Az IoT Edge-ügynökhöz tartozó modul neve `$edgeAgent`, és koordinálja az eszközön és IoT Hubon futó IoT Edge ügynök közötti kommunikációt. A kívánt tulajdonságok vannak beállítva, a manifest nasazení alkalmazása az adott eszközön egy egyetlen eszközök vagy ipari méretekben központi telepítésének részeként esetén. 

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| sémaverzióval | "1.0" kell lennie | Igen |
| Runtime.Type | Kell lennie a "docker" | Igen |
| runtime.settings.minDockerVersion | Állítsa be a manifest nasazení által megkövetelt minimális Docker verzióra | Igen |
| runtime.settings.loggingOptions | Egy sztringesített JSON, amely a IoT Edge Agent tároló naplózási beállításait tartalmazza. [Docker naplózási beállításai](https://docs.docker.com/engine/admin/logging/overview/) | Nem |
| runtime.settings.registryCredentials<br>. {registryId} .username | A tároló-beállításjegyzék felhasználóneve. Az Azure Container Registry esetében a felhasználónév általában a beállításjegyzék nevét.<br><br> A tárolójegyzék hitelesítő adatainak szükségesek, amelyek nem nyilvános modul képeket. | Nem |
| runtime.settings.registryCredentials<br>. {registryId} .password | A tárolóregisztrációs adatbázis jelszava. | Nem |
| runtime.settings.registryCredentials<br>. {registryId} .address | A tárolóregisztrációs adatbázis címe. Azure Container Registry esetén a címnek általában *{Registry Name}. azurecr. IO nevűnek*kell lennie. | Nem |  
| systemModules.edgeAgent.type | Kell lennie a "docker" | Igen |
| systemModules.edgeAgent.settings.image | A IoT Edge ügynök rendszerképének URI-ja. Jelenleg a IoT Edge ügynök nem tudja frissíteni magát. | Igen |
| systemModules.edgeAgent.settings<br>.createOptions | Egy sztringesített JSON, amely a IoT Edge ügynök tárolójának létrehozására vonatkozó beállításokat tartalmazza. [Docker-létrehozási beállítások](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nem |
| systemModules.edgeAgent.configuration.id | Az üzemelő példány, amelyre ez a modul telepítve azonosítója. | IoT Hub beállítja ezt a tulajdonságot, ha a jegyzékfájlt központi telepítés használatával alkalmazza a rendszer. Nem része egy manifest nasazení. |
| systemModules.edgeHub.type | Kell lennie a "docker" | Igen |
| systemModules.edgeHub.status | "Futnia kell" | Igen |
| systemModules.edgeHub.restartPolicy | Kell lennie a "mindig" | Igen |
| systemModules.edgeHub.settings.image | Az IoT Edge hub rendszerképének URI-ja. | Igen |
| systemModules.edgeHub.settings<br>.createOptions | Az IoT Edge hub-tároló létrehozására vonatkozó beállításokat tartalmazó sztringesített JSON. [Docker-létrehozási beállítások](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nem |
| systemModules.edgeHub.configuration.id | Az üzemelő példány, amelyre ez a modul telepítve azonosítója. | IoT Hub beállítja ezt a tulajdonságot, ha a jegyzékfájlt központi telepítés használatával alkalmazza a rendszer. Nem része egy manifest nasazení. |
| modules.{moduleId}.version | Egy felhasználó által megadott karakterlánc, amely a modul verzióját. | Igen |
| modulok. {moduleId} .type | Kell lennie a "docker" | Igen |
| modulok. {moduleId} .status | {"fut" \| "leállítva"} | Igen |
| modulok. {moduleId} .restartPolicy | {"soha" \| "sikertelen" \| "nem kifogástalan" állapotú "\|" mindig "} | Igen |
| modulok. {moduleId}. imagePullPolicy | {"on-create" \| "soha"} | Nem |
| modules.{moduleId}.settings.image | A modul kép URI Azonosítóját. | Igen |
| modules.{moduleId}.settings.createOptions | A modul tároló létrehozását, a beállításokat tartalmazó sztringesített JSON. [Docker-létrehozási beállítások](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nem |
| modules.{moduleId}.configuration.id | Az üzemelő példány, amelyre ez a modul telepítve azonosítója. | IoT Hub beállítja ezt a tulajdonságot, ha a jegyzékfájlt központi telepítés használatával alkalmazza a rendszer. Nem része egy manifest nasazení. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent jelentett tulajdonságok

A IoT Edge ügynök által jelentett tulajdonságok három fő információt tartalmaznak:

1. Utolsó észlelés kívánt tulajdonságok; az alkalmazás állapota
2. Az eszközön jelenleg futó modulok állapota a IoT Edge ügynök által jelentett módon; és
3. Jelenleg fut az eszköz kívánt tulajdonságait másolatát.

Ez az utolsó adat, az aktuálisan kívánt tulajdonságok másolata, hasznos lehet megállapítani, hogy az eszköz alkalmazta-e a legújabb kívánt tulajdonságokat, vagy továbbra is fut-e a korábbi telepítési jegyzék.

> [!NOTE]
> A IoT Edge ügynök jelentett tulajdonságai hasznosak lehetnek, mivel lekérdezhető a [IoT hub lekérdezési nyelvvel](../iot-hub/iot-hub-devguide-query-language.md) , hogy kivizsgálják a központi telepítések állapotát a skálán. További információ a IoT Edge ügynök tulajdonságainak állapotáról: [IoT Edge központi telepítések ismertetése egyetlen eszközhöz vagy nagy méretekben](module-deployment-monitoring.md).

A következő táblázat nem tartalmazza a kívánt tulajdonságok, másolja az információkat.

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Ez az egész szám a IoT Edge ügynök által feldolgozott kívánt tulajdonságok utolsó verziójára hivatkozik. |
| lastDesiredStatus.code | Ez az állapotkód a IoT Edge ügynök által látott utolsó kívánt tulajdonságokra utal. Megengedett értékek: `200` siker, `400` Érvénytelen konfiguráció, `412` érvénytelen a séma verziója, `417` a kívánt tulajdonságok üresek, `500` sikertelen |
| lastDesiredStatus.description | Az állapot leírása |
| deviceHealth | `healthy`, ha az összes modul futtatókörnyezeti állapota `running` vagy `stopped`, `unhealthy` másként |
| configurationHealth.{deploymentId}.health | `healthy`, ha az üzemelő példány által beállított összes modul futtatókörnyezeti állapota ({deploymentId}) `running` vagy `stopped`, `unhealthy` másként |
| runtime.platform.OS | Jelentéskészítés az eszközön futó operációs rendszer |
| Runtime.platform.Architecture | Az architektúra a processzor Reporting az eszközön |
| systemModules.edgeAgent.runtimeStatus | IoT Edge ügynök jelentett állapota: {"\|" sérült "} |
| systemModules.edgeAgent.statusDescription | A IoT Edge ügynök jelentett állapotának szöveges leírása. |
| systemModules.edgeHub.runtimeStatus | IoT Edge hub állapota: {"a (z)" \| "leállítva" \| "nem sikerült" \| "leállítási" \| "nem megfelelő állapotú"} |
| systemModules.edgeHub.statusDescription | IoT Edge hub állapotának szöveges leírása, ha a sérült. |
| systemModules.edgeHub.exitCode | Az IoT Edge hub-tároló által jelentett kilépési kód, ha a tároló kilép |
| systemModules.edgeHub.startTimeUtc | IoT Edge hub utolsó indításának ideje |
| systemModules.edgeHub.lastExitTimeUtc | Az IoT Edge hub utolsó kilépésének időpontja |
| systemModules.edgeHub.lastRestartTimeUtc | IoT Edge hub legutóbbi újraindításakor eltöltött idő |
| systemModules.edgeHub.restartCount | Ez a modul újra lett indítva, az újraindítási házirend részeként hányszor. |
| modules.{moduleId}.runtimeStatus | A (z) {"Running" \| "\|" leállítva a következő modul állapota: "\|" leállítási "\|" nem megfelelő "} |
| modules.{moduleId}.statusDescription | A modul állapotának szöveges leírása, ha a sérült. |
| modulok. {moduleId} .exitCode | A modul tárolója által jelentett kilépési kód, ha a tároló kilép |
| modules.{moduleId}.startTimeUtc | Ha a modul legutóbbi Indítás ideje |
| modules.{moduleId}.lastExitTimeUtc | Ha a modul utolsó kilépett idő |
| modules.{moduleId}.lastRestartTimeUtc | Ha a modul utolsó újraindítása idő |
| modules.{moduleId}.restartCount | Ez a modul újra lett indítva, az újraindítási házirend részeként hányszor. |

## <a name="edgehub-desired-properties"></a>EdgeHub desired tulajdonságai

Az IoT Edge hub különálló moduljának neve `$edgeHub`, és koordinálja az eszközön futó IoT Edge hub és IoT Hub közötti kommunikációt. A kívánt tulajdonságok vannak beállítva, a manifest nasazení alkalmazása az adott eszközön egy egyetlen eszközök vagy ipari méretekben központi telepítésének részeként esetén. 

| Tulajdonság | Leírás | Manifest nasazení szükséges |
| -------- | ----------- | -------- |
| sémaverzióval | "1.0" kell lennie | Igen |
| útvonalak. {routeName} | Egy IoT Edge hub-útvonalat jelölő sztring. További információ: [útvonalak deklarálása](module-composition.md#declare-routes). | A `routes` elem lehet jelen, de üres. |
| storeAndForwardConfiguration.timeToLiveSecs | Az az időtartam (másodpercben), ameddig IoT Edge hub megtartja az üzeneteket, ha leválasztják az útválasztási végpontokat, függetlenül attól, hogy IoT Hub vagy egy helyi modulról. Az érték bármilyen pozitív egész szám lehet. | Igen |

## <a name="edgehub-reported-properties"></a>EdgeHub jelentett tulajdonságok

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Ez az egész szám az IoT Edge hub által feldolgozott kívánt tulajdonságok utolsó verziójára utal. |
| lastDesiredStatus.code | Az IoT Edge hub által látott utolsó kívánt tulajdonságokra hivatkozó állapotkód. Megengedett értékek: `200` sikeres, `400` Érvénytelen konfiguráció, `500` sikertelen |
| lastDesiredStatus.description | Az állapot szövegének leírása. |
| ügyfelek számára. {eszköz vagy moduleId} .status | A kapcsolat állapota az eszköz vagy a modul. Lehetséges értékek: {"Connected" \| "leválasztva"}. Csak a modul identitások leválasztott állapotban is lehetnek. Az IoT Edge hubhoz csatlakozó alsóbb rétegbeli eszközök csak akkor jelennek meg, ha csatlakoztatva vannak. |
| ügyfelek számára. {eszköz vagy moduleId} .lastConnectTime | Az eszköz vagy modul csatlakozásának legutóbbi időpontja. |
| ügyfelek számára. {eszköz vagy moduleId} .lastDisconnectTime | Az eszköz vagy modul leválasztásának legutóbbi időpontja. |

## <a name="next-steps"></a>További lépések

Ha szeretné megtudni, hogyan használhatja ezeket a tulajdonságokat az üzembe helyezési jegyzékek kiépítéséhez, tekintse meg a [IoT Edge modulok használatának, konfigurálásának és](module-composition.md)újbóli használatának megismerését ismertető témakört.
