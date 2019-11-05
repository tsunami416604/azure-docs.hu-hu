---
title: Építészeti fogalmak az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk bemutatja az Azure architektúrával kapcsolatos főbb fogalmakat IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3dacea5c1d77bfead85e42ae312bfafbe70d8ca1
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990383"
---
# <a name="azure-iot-central-architecture-preview-features"></a>Azure IoT Central architektúra (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez a cikk áttekintést nyújt a Microsoft Azure IoT Central architektúráról.

![Legfelső szintű architektúra](media/concepts-architecture-pnp/architecture.png)

## <a name="devices"></a>Eszközök

Az eszközök az Azure IoT Central alkalmazással cserélik át az adatait. Az eszköz A következőket teheti:

- Mérések küldése, például telemetria.
- Szinkronizálhatja a beállításokat az alkalmazással.

Az Azure IoT Central-ban az eszköz által az alkalmazással megadható adatcsere megadására kerül egy eszköz sablonjában. További információ az eszközök sablonjairól: [metaadatok kezelése](#metadata-management).

Ha többet szeretne megtudni arról, hogy az eszközök hogyan csatlakoznak az Azure IoT Central-alkalmazáshoz, tekintse meg az [eszköz kapcsolatát](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="cloud-gateway"></a>Felhőbeli átjáró

Az Azure IoT Central az Azure IoT Hubt használja Felhőbeli átjáróként, amely lehetővé teszi az eszközök kapcsolódását. IoT Hub a következőket teszi lehetővé:

- Adatfeldolgozás a felhőben.
- Eszközkezelés.
- Biztonságos eszközök kapcsolata.

További információ a IoT Hubről: [Azure IoT hub](https://docs.microsoft.com/azure/iot-hub/).

Ha többet szeretne megtudni az Azure IoT Central eszközével való kapcsolatról, tekintse meg az [eszköz kapcsolatát](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)ismertető témakört.

## <a name="data-stores"></a>Adattárak

Az Azure IoT Central az alkalmazásadatok felhőben való tárolását. Az alkalmazásadatok tárolt alkalmazásai a következők:

- Eszközök sablonjai.
- Eszköz identitásai.
- Eszköz metaadatai.
- Felhasználói és szerepköri adat.

Az Azure IoT Central egy idősorozat-tárolót használ az eszközökről elküldett mérési adatokhoz. Az idősorozat-adatok az analitikai szolgáltatás által használt eszközökről.

## <a name="analytics"></a>Elemzés

Az elemzési szolgáltatás feladata az alkalmazás által megjelenített egyéni jelentéskészítési adat létrehozása. Az operátor [testreszabhatja az](howto-create-analytics.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) alkalmazásban megjelenített elemzéseket. Az elemzési szolgáltatás a [Azure Time Series Insightsra](https://azure.microsoft.com/services/time-series-insights/) épül, és feldolgozza az eszközökről továbbított mérési adatok mennyiségét.

## <a name="rules-and-actions"></a>Szabályok és műveletek

A [szabályok és műveletek](howto-create-telemetry-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) szorosan együttműködnek az alkalmazáson belüli feladatok automatizálásához. A Builder olyan szabályokat határozhat meg, amelyek az eszköz telemetria alapulnak, például a megadott küszöbértéket meghaladó hőmérsékletet. Az Azure IoT Central egy stream-processzor használatával határozza meg, hogy mikor teljesülnek a szabályok feltételei. Ha a szabály feltétele teljesül, a rendszer elindítja a szerkesztő által definiált műveletet. Egy művelet elküldheti például egy e-mailt, amely értesíti a mérnöket arról, hogy az eszköz hőmérséklete túl magas.

## <a name="metadata-management"></a>Metaadatok kezelése

Egy Azure IoT Central-alkalmazásban az eszközök sablonjai határozzák meg az eszközök típusának viselkedését és képességeit. Egy hűtőszekrény-eszköz sablonja például meghatározza azt a telemetria, amelyet a hűtőszekrény küld az alkalmazásnak.

![Sablon architektúrája](media/concepts-architecture-pnp/template-architecture.png)

IoT Central előnézet alkalmazás-eszköz sablonja:

- Az **eszköz képességeinek modelljei** határozzák meg az eszköz képességeit, például az általa küldött telemetria, az eszköz állapotát meghatározó tulajdonságokat, valamint azokat a parancsokat, amelyekre az eszköz válaszol. Az eszköz képességei egy vagy több interfészbe vannak rendezve. Az eszköz képességeinek modelljeivel kapcsolatos további információkért tekintse meg a [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) dokumentációját.
- A **felhő tulajdonságai** határozzák meg az adott eszközhöz tartozó tulajdonságokat IoT Central. Ezek a tulajdonságok csak IoT Central tárolódnak, és soha nem lesznek elküldve egy eszközre.
- A **nézetek** határozzák meg azokat az irányítópultokat és űrlapokat, amelyeket a készítő hoz létre, hogy az operátor figyelje és felügyelje az eszközöket.
- A **testreszabások** lehetővé teszik, hogy a szerkesztő felülbírálja az eszköz képességeinek modelljét, hogy azok jobban megfeleljenek a IoT Central alkalmazásnak.

Egy alkalmazáshoz egy vagy több szimulált és valós eszköz tartozhat az egyes eszközök alapján.

## <a name="data-export"></a>Adatexportálás

Egy Azure IoT Central alkalmazásban [folyamatosan exportálhatja az adatait](howto-export-data-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) a saját azure-Event Hubs és Azure Service Bus példányaiba. Az Azure Blob Storage-fiókjába rendszeres időközönként is exportálhatja adatait. IoT Central exportálhat méréseket, eszközöket és az eszközök sablonjait.

## <a name="batch-device-updates"></a>Batch-eszközök frissítései

Egy Azure IoT Central alkalmazásban [létrehozhat és futtathat feladatokat](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) a csatlakoztatott eszközök kezeléséhez. Ezek a feladatok lehetővé teszik az eszközök tulajdonságainak vagy beállításainak tömeges frissítését, illetve parancsok futtatását. Létrehozhat például egy feladatot, amellyel növelheti a ventilátor sebességét több hűtött árusító gép esetében is.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

A rendszergazdák az előre definiált szerepkörök használatával meghatározhatják az Azure IoT Central-alkalmazások [hozzáférési szabályait](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) . A rendszergazdák olyan szerepkörökhöz rendelhetnek felhasználókat, amelyek meghatározzák az alkalmazás azon területeit, amelyekhez a felhasználó hozzáfér.

## <a name="security"></a>Biztonság

Az Azure IoT Central biztonsági funkciói a következők:

- Az adatok továbbítása és nyugalmi állapotban vannak titkosítva.
- A hitelesítés Azure Active Directory vagy Microsoft-fiókkal történik. A kétfaktoros hitelesítés támogatott.
- Teljes bérlő elkülönítése.
- Az eszköz szintjének biztonsága.

## <a name="ui-shell"></a>FELHASZNÁLÓIFELÜLET-rendszerhéj

A felhasználói felületi rendszerhéj egy modern, rugalmas, HTML5-alapú böngészőalapú alkalmazás.
A rendszergazdák egyéni témák alkalmazásával szabhatják testre az alkalmazás felhasználói felületét, és módosíthatják a Súgó hivatkozásait, hogy az egyéni súgó erőforrásaira mutassanak. További információ a felhasználói felület testreszabásáról: [Az Azure IoT Central felhasználói felületének testreszabása](howto-customize-ui.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

Az operátorok személyre szabott alkalmazás-irányítópultokat hozhatnak létre. Több irányítópultot is beállíthat, amelyek különböző adathalmazokat jelenítenek meg, és válthatnak egymás között.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure-IoT Central architektúráját, a javasolt következő lépés az [eszköz kapcsolatának](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) megismerése az Azure IoT Centralban.