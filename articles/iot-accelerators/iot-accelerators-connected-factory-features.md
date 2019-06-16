---
title: Csatlakoztatott gyár megoldás funkciókat – Azure |} A Microsoft Docs
description: Az előre konfigurált csatlakoztatott gyár megoldás funkcióinak áttekintése.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: 2a11640959a8c7fdd0d238aba92698eb47934969
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080450"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Mi az csatlakoztatott gyári IoT-megoldásgyorsítók?

Csatlakoztatott gyár a Microsoft Azure ipari IoT-referenciaarchitektúra, mint a nyílt forráskódú megoldás csomagolt megvalósítását. Használhatja kiindulási pontként egy kereskedelmi termékhez. Egy előre elkészített verzióját a csatlakoztatott gyár megoldás üzembe helyezhető az Azure-előfizetése [Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com/#solutions/types/CF).

![Csatlakoztatott gyár megoldás irányítópultja](./media/iot-accelerators-connected-factory-features/dashboard.png)

Az Okosgyár-megoldásgyorsító [kódja a Githubon elérhető](https://github.com/Azure/azure-iot-connected-factory).

Csatlakoztatott gyár a következő szolgáltatásokat tartalmazza:

## <a name="industrial-device-interoperability"></a>Ipari eszközök együttműködési képessége

- Csatlakozás ipari objektumokhoz OPC UA-interfészen keresztül.
- A szimulált gyártósort (OPC UA kiszolgálókat futtató Docker-tárolókban) segítségével megtekintheti az élő telemetria belőlük.
- Az OPC UA információs modell, egy felhőbeli irányítópultról OPC UA-kiszolgálók tallózása

## <a name="remote-management"></a>Távfelügyelet

- OPC UA-objektumait a felhőalapú irányítópultról konfigurálhatja (metódusok hívása, adatok írása és olvasása).
- OPC UA-objektumokból származó telemetriai adatok közzététele és a közzététel visszavonása felhőalapú irányítópultról.

## <a name="cloud-dashboard"></a>Felhőalapú irányítópult

- Telemetria előzetes verziók közvetlenül egy felhőbeli irányítópult megtekintése.
- A telemetriai adatok trendjeinek megtekintése, és korrelációk létrehozása A Time Series Insights irányítópultjának használatával.
- Tekintse meg a számított általános berendezések hatékonyságát (OEE) és a fő teljesítménymutatók (KPI-k) egy felhőalapú irányítópultról.
- Ipari eszközök hierarchiák fa topológia, valamint egy olyan interaktív térkép a.
- Megtekintheti, tudomásul veszi, és zárja be a riasztások a felhőalapú irányítópultról.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Az Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) tárolására, jelenítenek meg, és nagy mennyiségű idősorozat-adatok lekérdezése épül. Csatlakoztatott Factory használja ezt a szolgáltatást.
- Csatlakoztatott gyár integrálható, ezzel lehetővé téve a szolgáltatás hajtsa végre az adatok részletes, valós idejű elemzése.

## <a name="rules-and-alerts"></a>Szabályok és értesítések

[Szabályok küszöbalapú riasztások konfigurálása](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Teljes körű biztonság

- Biztonsági engedélyek konfigurálása felhasználókhoz szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
- Végpontok közötti titkosítást OPC UA-hitelesítést (X.509-tanúsítványok használatával), valamint a biztonsági jogkivonatok segítségével van megvalósítva.

## <a name="customizability"></a>Testreszabhatóság

- Testre szabhatja a megoldás speciális üzleti igények kielégítése érdekében.
- Teljes megoldás-forráskódja elérhető a Githubon. Tekintse meg a [csatlakoztatott gyár megoldás](https://github.com/Azure/azure-iot-connected-factory) tárház.

## <a name="next-steps"></a>További lépések

Az Okosgyár-megoldásgyorsító kapcsolatos további információkért tekintse meg a rövid útmutatóban [ipari IoT-eszközök kezeléséhez felhőalapú megoldások](quickstart-connected-factory-deploy.md).
