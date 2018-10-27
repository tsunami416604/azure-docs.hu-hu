---
title: Az Azure IoT Central architekturális fogalmak |} A Microsoft Docs
description: Ez a cikk bemutatja az Azure IoT Central architektúrájával kapcsolatos alapfogalmak
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 564ea3efe35a1054b8c905cff4b7f4c739cc9216
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156374"
---
# <a name="azure-iot-central-architecture"></a>Az Azure IoT Central architektúrája

Ez a cikk a Microsoft Azure IoT Central-architektúra áttekintése.

![Legfelső szintű architektúra](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Eszközök

Eszközök exchange-adatok az Azure IoT Central alkalmazáshoz. Egy eszköz a következőket teheti:

- Például a telemetriai adatok mérések küldése.
- Beállítások szinkronizálása az alkalmazását.

Az Azure IoT Central egy eszközt az alkalmazással is exchange-adatok egy eszköz sablonban megadott. Eszköz sablonokkal kapcsolatos további információkért lásd: [metaadatok felügyeleti](#metadata-management).

Hogyan eszközök csatlakoztatása az Azure IoT Central alkalmazásnak kapcsolatos további információkért lásd: [eszközkapcsolatok](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Átjáró

Az Azure IoT Central egy felhőátjáróhoz, amely lehetővé teszi az eszköz csatlakoztatása Azure IoT Hub használja. Az IoT Hub a következőket:

- A felhőbeli nagy mennyiségű adatbetöltés.
- Eszközök kezelése.
- Biztonságos kapcsolat az eszközzel.

Az IoT Hub kapcsolatos további információkért lásd: [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Eszköz csatlakoztatása az Azure IoT Central kapcsolatos további információkért lásd: [eszközkapcsolatok](concepts-connectivity.md).

## <a name="data-stores"></a>Adattárolók

Az Azure IoT Central alkalmazásadat tárol a felhőben. Alkalmazásadatok tárolása tartalmazza:

- Eszköz sablonok.
- Eszközidentitások.
- Eszköz metaadatait.
- Felhasználó és szerepkör-adatokat.

Az Azure IoT Central egy alkalommal sorozat a mérési adatokat az eszközök által küldött használ. Idősorozat-adatok az analytics szolgáltatás által használt eszközökről.

## <a name="analytics"></a>Elemzés

Az elemzési szolgáltatás, amely megjeleníti az alkalmazás egyéni jelentésadatok generálása felelős. Az operátor is [testre szabhatja az analytics](howto-create-analytics.md) az alkalmazásban jeleníthető meg. Az elemzés szolgáltatás a beépített [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) és a mérési adatokat az eszközök által küldött dolgozza fel.

## <a name="rules-and-actions"></a>Szabályok és műveletek

[Szabályok és műveletek](howto-create-telemetry-rules.md) szorosan együtt, automatizálhatja a feladatokat az alkalmazásban lévő munkahelyi. A jelentéskészítő adhat meg szabályokat, például a hőmérséklet egy megadott küszöbértéket meghaladó eszköz telemetriai adatokon alapul. Az Azure IoT Central egy adatfolyam-feldolgozó alapján határozza meg, ha teljesülnek-e a szabály feltételeit. Amikor egy szabály a feltétel teljesül, elindítja a szerkesztő által meghatározott műveletet. Például egy művelet küldhet egy e-mailben értesíteni mérnökként, hogy egy eszköz hőmérséklete túl magas.

## <a name="metadata-management"></a>Metaadatok kezelése

Azure IoT Central alkalmazáshoz eszközsablonok viselkedését és különböző típusú eszköz képesség határoz meg. Például hűtő eszköz sablont adja meg a telemetriát küld egy hűtőszekrények alkalmazására.

![Sablon-architektúra](media/concepts-architecture/template_architecture.png)

Egy eszköz sablonban:

- **Mértékek** adja meg az eszköz telemetriai adatokat küld az alkalmazáshoz.
- **Beállítások** adja meg a konfigurációk, az operátornak állíthatja be.
- **Tulajdonságok** adja meg a metaadatokat, amelyek az operátornak állíthatja be.
- **Szabályok** automatizálhatja az eszközről küldött adatok alapján az alkalmazás viselkedését.
- **Az irányítópultok** olyan eszköz, az alkalmazás testre szabható nézetek.

Egy alkalmazás egy vagy több szimulált és valódi eszközön minden egyes eszköz sablon alapján is rendelkezik.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

Egy [rendszergazdai hozzáférési szabályokat definiálhat](howto-administer.md) az előre definiált szerepkörök használatával az Azure IoT Central alkalmazáshoz. A rendszergazda felhasználókat rendelhet szerepköröket, amelyek meghatározzák, milyen területeken, az alkalmazás a felhasználónak hozzáférése van.

## <a name="security"></a>Biztonság

Azure IoT Central belül biztonsági szolgáltatások a következők:

- Az átvitel során, míg az inaktív adatok titkosítása.
- Hitelesítés Azure Active Directory vagy a Microsoft Account biztosítunk. A kétfaktoros hitelesítést is támogatja.
- Teljes bérlők elkülönítését.
- Eszköz biztonság.

## <a name="ui-shell"></a>Felhasználói felület rendszerhéj

A felhasználói felület shell egy modern, rugalmas, HTML5-alapú böngészőalapú alkalmazás.

## <a name="next-steps"></a>További lépések

Most, hogy az Azure IoT Central architektúrája megismerkedett a javasolt következő lépésre kapcsolatos [eszközkapcsolatok](concepts-connectivity.md) az Azure IoT Central.