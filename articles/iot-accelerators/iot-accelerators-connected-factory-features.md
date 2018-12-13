---
title: Csatlakoztatott gyár megoldás funkciókat – Azure |} A Microsoft Docs
description: Az előre konfigurált csatlakoztatott gyár megoldás funkcióinak áttekintése.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: af2a2c84f9eb420a7ca9a8bd5909cbf856d29a5e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309195"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Mi az csatlakoztatott gyári IoT-megoldásgyorsítók?

Csatlakoztatott gyár a Microsoft Azure ipari IoT-referenciaarchitektúra, mint a nyílt forráskódú megoldás csomagolt megvalósítását. Használhatja kiindulási pontként egy kereskedelmi termékhez. Egy előre elkészített verzióját a csatlakoztatott gyár megoldás üzembe helyezhető az Azure-előfizetése [Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com/#solutions/types/CF).

![Csatlakoztatott gyár megoldás irányítópultja](./media/iot-accelerators-connected-factory-features/dashboard.png)

Az Okosgyár-megoldásgyorsító [kódja a Githubon elérhető](https://github.com/Azure/azure-iot-connected-factory).

Csatlakoztatott gyár a következő szolgáltatásokat tartalmazza:

## <a name="industrial-device-interoperability"></a>Együttműködés a ipari eszközök

- Az OPC UA-illesztővel ipari eszközök csatlakozhat.
- A szimulált gyártósort (OPC UA kiszolgálókat futtató Docker-tárolókban) segítségével megtekintheti az élő telemetria belőlük.
- Az OPC UA információs modell, egy felhőbeli irányítópultról OPC UA-kiszolgálók tallózása

## <a name="remote-management"></a>Távfelügyelet

- Az OPC UA eszközöket a felhő Irányítópultról (hívás módszerek, és adatokat ír) konfigurálása.
- Tegye közzé, és az OPC UA eszközöket egy felhőalapú irányítópultról küldött telemetriai adatok közzétételét.

## <a name="cloud-dashboard"></a>A cloud irányítópultja

- Telemetria előzetes verziók közvetlenül egy felhőbeli irányítópult megtekintése.
- A telemetriai adatok megtekinthessék a szalagfelhasználási tendenciákat, és hozzon létre a Time Series Insights Explorerben irányítópulttal összefüggéseket.
- Tekintse meg a számított általános berendezések hatékonyságát (OEE) és a fő teljesítménymutatók (KPI-k) egy felhőalapú irányítópultról.
- Ipari eszközök hierarchiák fa topológia, valamint egy olyan interaktív térkép a.
- Megtekintheti, tudomásul veszi, és zárja be a riasztások a felhőalapú irányítópultról.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Az Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) tárolására, jelenítenek meg, és nagy mennyiségű idősorozat-adatok lekérdezése épül. Csatlakoztatott Factory használja ezt a szolgáltatást.
- Csatlakoztatott gyár integrálható, ezzel lehetővé téve a szolgáltatás hajtsa végre az adatok részletes, valós idejű elemzése.

## <a name="rules-and-alerts"></a>Szabályok és értesítések

[Szabályok küszöbalapú riasztások konfigurálása](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Teljes körű biztonság

- Szerepköralapú hozzáférés-vezérlés (RBAC) használó felhasználók számára a biztonsági engedélyek konfigurálásáról.
- Végpontok közötti titkosítást OPC UA-hitelesítést (X.509-tanúsítványok használatával), valamint a biztonsági jogkivonatok segítségével van megvalósítva.

## <a name="customizability"></a>Testreszabhatóság

- Testre szabhatja a megoldás speciális üzleti igények kielégítése érdekében.
- Teljes megoldás-forráskódja elérhető a Githubon. Tekintse meg a [csatlakoztatott gyár megoldás](https://github.com/Azure/azure-iot-connected-factory) tárház.

## <a name="next-steps"></a>További lépések

További információ az előre konfigurált csatlakoztatott gyár megoldás a következő cikkek elolvasásával:

* [Előre konfigurált csatlakoztatott gyár megoldás – bemutató](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Átjáró telepítése csatlakoztatott gyárhoz]( iot-accelerators-connected-factory-gateway-deployment.md)
