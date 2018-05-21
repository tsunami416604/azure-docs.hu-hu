---
title: Gyári megoldás szolgáltatásokat – Azure csatlakoztatott |} Microsoft Docs
description: Az előre konfigurált csatlakoztatott gyári-megoldás funkcióinak áttekintése.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: 7ee86a59e7c47406ea1a890f1a9d1eb2ed4ad772
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="what-is-azure-iot-suite-connected-factory"></a>Mi az Azure IoT Suite csatlakoztatott gyári?

Csatlakoztatott gyári a Microsoft Azure ipari IoT referencia-architektúrában, mint a nyílt forráskódú megoldás csomagolt megvalósítása. Használhatja kiindulási pontként egy kereskedelmi termékhez. A csatlakoztatott gyári megoldás előre elkészített verziója telepítheti be az Azure-előfizetése [Azure IoT Suite](https://www.azureiotsuite.com/#solutions/types/CF).

![Csatlakoztatott gyári megoldás irányítópultja](./media/iot-accelerators-connected-factory-features/dashboard.png)

Csatlakoztatott gyári az alábbi szolgáltatásokat tartalmazza:

## <a name="industrial-device-interoperability"></a>Ipari eszköz együttműködés

- Csatlakozás ipari eszközök OPC EE-illesztővel.
- A szimulált gyártási sorok (futó OPC EE-kiszolgálók Docker-tárolókban lévő) segítségével tekintse meg élő telemetriai belőlük.
- Keresse meg a OPC EE információs modell egy felhőalapú irányítópultról OPC EE-kiszolgálók.

## <a name="remote-management"></a>Távfelügyelet

- Konfigurálja a OPC EE-eszközöket, a felhő Irányítópultról (módszereket, olvassa el, és adatírás hívás).
- Tegye közzé, és visszavonni a OPC EE-eszközöket egy felhőalapú irányítópultról telemetriai adatait.

## <a name="cloud-dashboard"></a>Felhő irányítópult

- Telemetria előzetes közvetlenül egy felhőalapú irányítópult megtekintése.
- Trendek megtekintése a "telemetrikus" adatok és az idő adatsorozat Insights Explorer irányítópulttal korrelációk létrehozása.
- Tekintse meg a számított általános berendezések hatékonyságát (OEE) és fő teljesítménymutatók (KPI-k) a felhő irányítópulton.
- Ipari eszköz hierarchiák fa topológia, valamint a interaktív térkép.
- Nézetben tudomásul, és a felhő irányítópultról riasztások bezárása.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Az Azure idő adatsorozat Insights](../time-series-insights/time-series-insights-overview.md) épül, tárolás, megjeleníteni és nagy mennyiségű idősorozat adatok lekérdezése. A kapcsolat gyári használja ezt a szolgáltatást.
- Csatlakoztatott gyári integrálható, ez a szolgáltatás, amely lehetővé teszi, hajtsa végre a részletes, valós idejű elemző eszköz adatait.

## <a name="rules-and-alerts"></a>Szabályok és értesítések

[Küszöbérték-alapú szabályok riasztások konfigurálása](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Végpontok közötti biztonságot

- Biztonsági engedélyek konfigurálása a felhasználók a szerepköralapú hozzáférés-vezérlést (RBAC).
- Végpontok közötti titkosítását OPC EE-hitelesítés (X.509-tanúsítványokat használ), valamint a biztonsági jogkivonatok segítségével van megvalósítva.

## <a name="customizability"></a>Testreszabhatóság miatt

- [Testre szabhatja](../iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md) a megoldás speciális üzleti követelményeinek megfelelően.
- Teljes körű megoldás-forráskód elérhető a Githubon. Tekintse meg a [csatlakoztatott gyári előre konfigurált megoldás](https://github.com/Azure/azure-iot-connected-factory) tárházba.

## <a name="next-steps"></a>További lépések

További információ a csatlakoztatott gyári előre konfigurált megoldásról elolvasni a következő cikkeket:

* [Csatlakoztatott gyári előre konfigurált megoldás forgatókönyv](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Átjáró telepítése csatlakoztatott gyárhoz]( iot-accelerators-connected-factory-gateway-deployment.md)
