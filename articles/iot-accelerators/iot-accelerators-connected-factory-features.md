---
title: Csatlakoztatott gyári megoldásfunkciók - Azure | Microsoft dokumentumok
description: Ez a cikk a Csatlakoztatott gyár előre konfigurált megoldás, például a felhőalapú irányítópult, a szabályok és a riasztások funkcióinak áttekintését ismerteti.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820114"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Mi a Connected Factory IoT megoldásgyorsító?

A Connected Factory a Microsoft Azure Industrial IoT referenciaarchitektúrájának implementációja, amely nyílt forráskódú megoldásként van csomagolva. Használhatja, mint a kiindulási pont egy kereskedelmi termék. A Connected Factory megoldás előre elkészített verzióját telepítheti az Azure [IoT-megoldásgyorsítókból](https://www.azureiotsolutions.com/#solutions/types/CF)az Azure-előfizetésbe.

![Csatlakoztatott gyári megoldás irányítópultja](./media/iot-accelerators-connected-factory-features/dashboard.png)

A Connected Factory megoldásgyorsító [kódja elérhető a GitHubon.](https://github.com/Azure/azure-iot-connected-factory)

A Connected Factory a következő funkciókat tartalmazza:

## <a name="industrial-device-interoperability"></a>Ipari eszközök interoperabilitása

- Csatlakozzon ipari eszközökhöz opc UA felülettel.
- Használja a szimulált termelési sorok (futó OPC UA-kiszolgálók Docker-tárolók) élő telemetriai adatok at tőlük.
- Böngésszen az OPC UA-kiszolgálók OPC UA-információs modelljében egy felhőalapú irányítópultról.

## <a name="remote-management"></a>Távfelügyelet

- Konfigurálja az OPC UA-eszközeit a felhőirányítópultról (hívási módszerek, olvasási és írási adatok).
- Telemetriai adatok közzététele és közzétételének visszavonása az OPC UA-eszközök egy felhőirányítópulton.

## <a name="cloud-dashboard"></a>Felhőalapú irányítópult

- Telemetriai előnézetek közvetlenül a felhőirányítópulton.
- A Time Series Insights Explorer irányítópultján megtekintheti a telemetriai adatok trendjeit, és korrelációkat hozhat létre.
- Tekintse meg a számított általános berendezéshatékonysági (OEE) és fő teljesítménymutatókat (KPI-k) egy felhőalapú irányítópultról.
- Megtekintheti az ipari eszközhierarchiákat egy fatopológiában, valamint egy interaktív térképen.
- Riasztások megtekintése, nyugtázása és bezárása egy felhőalapú irányítópultról.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Az Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) nagy mennyiségű idősorozat-adatok tárolására, megjelenítésére és lekérdezésére készült. A Connected Factory ezt a szolgáltatást használja.
- A Connected Factory integrálódik ezzel a szolgáltatással, amely lehetővé teszi az eszközadatok mély, valós idejű elemzését.

## <a name="rules-and-alerts"></a>Szabályok és riasztások

[A riasztásokhoz küszöbérték-alapú szabályok konfigurálása.](iot-accelerators-connected-factory-configure.md)

## <a name="end-to-end-security"></a>Végpontok között a biztonság

- A szerepköralapú hozzáférés-vezérlés (RBAC) használatával a felhasználók biztonsági engedélyeinek konfigurálása.
- A végpontok közötti titkosítás opc UA-hitelesítéssel (X.509-es tanúsítványokkal) és biztonsági jogkivonatokkal valósítja meg.

## <a name="customizability"></a>Testreszabhatóság

- A megoldás testreszabása az adott üzleti követelményeknek megfelelően.
- A GitHubon elérhető teljes körű megoldás forráskód. Tekintse meg a [Csatlakoztatott gyár előre konfigurált megoldástár.](https://github.com/Azure/azure-iot-connected-factory)

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Connected Factory megoldásgyorsítóról, olvassa el a Rövid útmutató Egy felhőalapú megoldás sal az [ipari IoT-eszközök kezelését.](quickstart-connected-factory-deploy.md)
