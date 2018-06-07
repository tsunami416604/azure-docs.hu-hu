---
title: Az Azure IoT-központ architekturális fogalmak |} Microsoft Docs
description: Ez a cikk be Azure IoT központi architektúrájával kapcsolatos alapvető fogalmakat
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 44408e7b6ad1a068f265bf7b78d973e6aae3001b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628759"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT központi architektúra

Ez a cikk a Microsoft Azure IoT központi architektúra áttekintése.

![Legfelső szintű architektúra](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Eszközök

Eszközök exchange-adatok az Azure IoT központi alkalmazással. Egy eszköz a következőket teheti:

- Mérések, például a telemetriai adatok küldése.
- Beállítások szinkronizálása az alkalmazást.

Az Azure IoT központban egy eszközt az alkalmazással továbbíthatja az adatokat egy eszköz sablonban van beállítva. Eszköz sablonokkal kapcsolatos további információkért lásd: [metaadatok felügyeleti](#metadata-management).

Eszközök hogyan kapcsolódnak az Azure IoT központi alkalmazás kapcsolatos további információkért lásd: [eszközkapcsolatok](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Átjáró

Az Azure IoT központi Azure IoT Hub használja, mint egy átjáró, amely lehetővé teszi az eszköz kapcsolatot. Az IoT-központ lehetővé teszi, hogy:

- A felhőben léptékű adatfeldolgozást.
- Eszközkezelés.
- Biztonságos eszköz kapcsolatot.

Az IoT-központ kapcsolatos további információkért lásd: [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Az Azure IoT-központ eszközkapcsolatok kapcsolatos további információkért lásd: [eszközkapcsolatok](concepts-connectivity.md).

## <a name="data-stores"></a>Adattárolók

Az Azure IoT központi alkalmazás adatot tárol a felhőben. Alkalmazás tárolt adatokat tartalmazza:

- Eszköz sablonok.
- Eszköz identitásokat.
- Eszköz metaadatait.
- Felhasználó- és szerepkör-adatokat.

Azure IoT központi egy adatsorozat tárolására időt használja az eszközök elküldött mérési adatokat. Az analitikai szolgáltatás által használt eszközök idő adatsorozat adatait.

## <a name="analytics"></a>Elemzés

Az analitikai szolgáltatás feladata az alkalmazás által megjelenített egyéni jelentési adatok létrehozásához. Operátor is [testre szabhatja az analytics](howto-create-analytics.md) jelennek meg az alkalmazást. Az analitikai szolgáltatás a beépített [Azure idő adatsorozat Insights](https://azure.microsoft.com/services/time-series-insights/) és a mérési adatokat az eszközök által küldött dolgozza fel.

## <a name="rules-and-actions"></a>Szabályok és műveletek

[Szabályok és a műveletek](howto-create-telemetry-rules.md) munkahelyi szoros együtt az alkalmazáson belül feladatok automatizálására. A jelentéskészítő alapján, például a megadott küszöbértéket meghaladó hőmérséklete telemetriát adhat meg. Azure IoT központi adatfolyam processzort alapján határozza meg, amikor a szabály feltételek. A szabály feltétele teljesül, amikor elindítja a határozzák meg a jelentéskészítő művelet. Például egy műveletet az, hogy egy eszköz hőmérséklete túl magas egy mérnököt értesítése e-mailt küldhet.

## <a name="metadata-management"></a>Metaadatok kezelése

Egy Azure IoT központi alkalmazásban eszköz sablonok a viselkedést, és képes eszköz típusa határozza meg. Például hűtő eszköz sablon meghatározza a telemetriai adatokat egy hűtő elküldi az alkalmazásnak.

![Sablon architektúrája](media/concepts-architecture/template_architecture.png)

Az eszköz sablont:

- **Mérések** adja meg az eszköz telemetriai adatok elküldi az alkalmazásnak.
- **Beállítások** adja meg a konfigurációk operátor állíthatja be.
- **Tulajdonságok** adja meg a metaadatokat, amelyek operátor állíthatja be.
- **Szabályok** automatizálhatja viselkedést az alkalmazás egy eszközről küldött adatok alapján.
- **Irányítópultok** testreszabható nézetek egy eszköz, az alkalmazásban.

Egy alkalmazás rendelkezhet egy vagy több szimulált és valós eszköz minden eszköz sablon alapján.

## <a name="rbac"></a>RBAC

Egy [rendszergazda adhatja meg a hozzáférési szabályok](howto-administer.md) az előre definiált szerepkörök használata az Azure IoT központi alkalmazáshoz. A rendszergazda felhasználókat rendelhet szerepköröket, amelyek meghatározzák, milyen területeken az alkalmazás a felhasználó rendelkezik hozzáférési jogosultsággal.

## <a name="security"></a>Biztonság

Azure IoT központi belül biztonsági szolgáltatások a következők:

- Adattitkosítás átvitel közben, és inaktív.
- Azure Active Directory vagy a Microsoft Account biztosítja a hitelesítést. Kéttényezős hitelesítést is támogatja.
- Teljes bérlők elkülönítését.
- Eszköz biztonság.

## <a name="ui-shell"></a>Felhasználói felület rendszerhéj

A felhasználói felület rendszerhéj a modern, rugalmas, HTML5 webböngésző-alapú alkalmazáshoz.

## <a name="next-steps"></a>További lépések

Most, hogy az Azure IoT központi architektúrája megismerte a javasolt következő lépésre megismeréséhez [eszközkapcsolatok](concepts-connectivity.md) az Azure IoT-központ.