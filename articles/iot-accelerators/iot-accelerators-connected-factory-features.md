---
title: Csatlakoztatott Factory-megoldási funkciók – Azure | Microsoft Docs
description: Ez a cikk áttekintést nyújt az előre konfigurált csatlakoztatott gyár megoldás szolgáltatásairól, például a felhő-irányítópultról, a szabályokról és a riasztásokról.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820114"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Mi a Connected Factory IoT megoldás-gyorsító?

A csatlakoztatott gyár a Microsoft Azure Industrial IoT-referenciájának olyan implementációja, amely nyílt forráskódú megoldásként van csomagolva. Egy kereskedelmi termék kiindulási pontként használható. A csatlakoztatott gyári megoldás előre összeállított verzióját üzembe helyezheti Azure-előfizetésében az [Azure IoT megoldás-gyorssegédek](https://www.azureiotsolutions.com/#solutions/types/CF)szolgáltatásban.

![Csatlakoztatott gyári megoldás irányítópultja](./media/iot-accelerators-connected-factory-features/dashboard.png)

A csatlakoztatott gyári megoldás gyorsító [kódja elérhető a githubon](https://github.com/Azure/azure-iot-connected-factory).

A csatlakoztatott gyár a következő funkciókat tartalmazza:

## <a name="industrial-device-interoperability"></a>Ipari eszközök együttműködési képessége

- Csatlakozás ipari objektumokhoz OPC UA-interfészen keresztül.
- A szimulált éles vonalakat (OPC UA-kiszolgálókat futtatva Docker-tárolókban) az élő telemetria megjelenítéséhez használhatja.
- Böngésszen az OPC UA-kiszolgálók OPC UA-információs modelljében egy Felhőbeli irányítópulton.

## <a name="remote-management"></a>Távfelügyelet

- OPC UA-objektumait a felhőalapú irányítópultról konfigurálhatja (metódusok hívása, adatok írása és olvasása).
- OPC UA-objektumokból származó telemetriai adatok közzététele és a közzététel visszavonása felhőalapú irányítópultról.

## <a name="cloud-dashboard"></a>Felhőalapú irányítópult

- Telemetria-előnézetek megtekintése közvetlenül egy Felhőbeli irányítópulton.
- A telemetriai adatok trendjeinek megtekintése, és korrelációk létrehozása A Time Series Insights irányítópultjának használatával.
- Lásd: számított általános berendezések hatékonysága (OEE) és fő teljesítménymutatók (KPI-k) a Felhőbeli irányítópulton.
- Megtekintheti az ipari eszközök hierarchiáit egy fa-topológiában, valamint egy interaktív térképen.
- Riasztások megtekintése, nyugtázása és lezárása egy Felhőbeli irányítópulton.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- A [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) nagy mennyiségű idősoros adatsorozat tárolására, megjelenítésére és lekérdezésére készült. A csatlakoztatott gyár ezt a szolgáltatást használja.
- A csatlakoztatott gyár integrálva van ezzel a szolgáltatással, amely lehetővé teszi, hogy az eszköz adatainak alapos, valós idejű elemzését végezze el.

## <a name="rules-and-alerts"></a>Szabályok és riasztások

[Küszöbérték-alapú szabályok konfigurálása a riasztásokhoz](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Teljes körű biztonság

- Biztonsági engedélyek konfigurálása felhasználókhoz szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
- A végpontok közötti titkosítás az OPC UA hitelesítéssel (X. 509 tanúsítványok használatával) és biztonsági jogkivonatokkal valósítható meg.

## <a name="customizability"></a>Testreszabhatóság

- Testre szabhatja a megoldást, hogy megfeleljen az adott üzleti követelményeknek.
- Teljes körű megoldás forrása – a GitHubon elérhető forráskód. Tekintse meg az [előre konfigurált csatlakoztatott gyár megoldás](https://github.com/Azure/azure-iot-connected-factory) -tárházat.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a csatlakoztatott Factory megoldás-gyorssegédről, tekintse meg a gyors üzembe helyezési [kísérlet felhőalapú megoldását az ipari IoT-eszközök kezeléséhez](quickstart-connected-factory-deploy.md)című témakörben.
