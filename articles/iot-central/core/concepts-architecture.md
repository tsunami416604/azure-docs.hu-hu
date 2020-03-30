---
title: Architekturális fogalmak az Azure IoT Centralban | Microsoft dokumentumok
description: Ez a cikk az Azure IoT Central architektúrájával kapcsolatos legfontosabb fogalmakat ismerteti
author: dominicbetts
ms.author: dobett
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 12ad231d81b6c134ebb8d4902b3f95c978e9622d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271641"
---
# <a name="azure-iot-central-architecture"></a>Az Azure IoT Central architektúrája



Ez a cikk áttekintést nyújt a Microsoft Azure IoT Central architektúráról.

![Felső szintű architektúra](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Eszközök

Az eszközök adatokat cserélnek az Azure IoT Central alkalmazással. Az eszköz:

- Mérések, például telemetriai adatok küldése.
- A beállítások szinkronizálása az alkalmazással.

Az Azure IoT Centralban az adatokat, amelyek egy eszköz kicserélheti az alkalmazással egy eszközsablonban van megadva. Az eszközsablonokról további információt a [Metaadat-kezelés](#metadata-management)című témakörben talál.

Ha többet szeretne tudni arról, hogy az eszközök hogyan kapcsolódnak az Azure IoT Central alkalmazáshoz, olvassa el az [Eszközkapcsolat](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-eszközök

Az [Azure IoT SDK-k](https://github.com/Azure/azure-iot-sdks)használatával létrehozott eszközök mellett [azure IoT Edge-eszközöket](../../iot-edge/about-iot-edge.md) is csatlakoztathat egy IoT Central-alkalmazáshoz. Az IoT Edge lehetővé teszi a felhőintelligencia és az egyéni logika közvetlen futtatását az IoT Central által felügyelt IoT-eszközökön. Az IoT Edge futásidejű lehetővé teszi, hogy:

- Telepítse és frissítse a számítási feladatokat az eszközön.
- Az IoT Edge biztonsági szabványainak karbantartása az eszközön.
- Győződjön meg arról, hogy az IoT Edge-modulok mindig futnak.
- Modul állapotának jelentése a felhőbe a távoli figyeléshez.
- Az alsóbb rétegbeli eszközök és az IoT Edge-eszközök közötti kommunikáció kezelése, az IoT Edge-eszközön lévő modulok, valamint az IoT Edge-eszköz és a felhő közötti kommunikáció.

![Azure IoT Central az Azure IoT Edge-el](./media/concepts-architecture/iotedge.png)

Az IoT Central a következő képességeket engedélyezi az IoT Edge-eszközökhöz:

- Az IoT Edge-eszközök képességeit leíró eszközsablonok, például:
  - Üzembe helyezési jegyzékek feltöltési képesség, amely segít kezelni a jegyzékfájl egy eszközpark.
  - Az IoT Edge-eszközön futó modulok.
  - Az egyes modulok által küldött telemetriai adatok.
  - Az egyes modulok által tulajdonságjelentést tesz.
  - Az egyes modulok által adott parancsok.
  - Az IoT Edge átjáróeszköz-képességi modell és az alsóbb rétegbeli eszközképességi modell közötti kapcsolatok.
  - Felhőalapú tulajdonságok, amelyek nem az IoT Edge-eszközön vannak tárolva.
  - Az IoT Central alkalmazás részét képező testreszabások, irányítópultok és űrlapok.

  További információkért tekintse meg az [Azure IoT Edge-eszközök csatlakoztatása egy Azure IoT Central alkalmazás](./concepts-iot-edge.md) cikket.

- IoT Edge-eszközök nagy méretekben történő kiépítése az Azure IoT-eszközkiépítési szolgáltatás használatával
- Szabályok és intézkedések.
- Egyéni irányítópultok és elemzések.
- Telemetriai adatok folyamatos exportálása Az IoT Edge-eszközökről.

### <a name="iot-edge-device-types"></a>IoT Edge-eszköztípusok

Az IoT Central az IoT Edge-eszköztípusokat a következőképpen sorolja be:

- Levél eszközök. Egy IoT Edge-eszköz lehet alsóbb rétegbeli eszközök, de ezek az eszközök nincsenek kiépítve az IoT Central.
- Átjáróeszközök alsóbb rétegbeli eszközökkel. Mind az átjáró-eszköz, mind az alsóbb rétegbeli eszközök az IoT Centralban vannak kiépítve

![IoT Central ioT edge áttekintésével](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge-minták

Az IoT Central a következő IoT Edge-eszközmintákat támogatja:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge mint levéleszköz

![IoT Edge mint levéleszköz](./media/concepts-architecture/edgeasleafdevice.png)

Az IoT Edge-eszköz ki van építve az IoT Central és az alsóbb rétegbeli eszközök és a telemetriai adatok az IoT Edge-eszköz ből érkezőként jelenik meg. Az IoT Edge-eszközhöz csatlakoztatott alsóbb rétegbeli eszközök nincsenek kiépítve az IoT Centralban.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>Az IoT Edge átjáróeszköz identitással rendelkező alsóbb rétegbeli eszközökhöz csatlakozik

![IoT Edge alsóbb réteges eszközidentitással](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Az IoT Edge-eszköz ki van építve az IoT Central ban az IoT Edge-eszközhöz csatlakoztatott alsóbb rétegbeli eszközökkel együtt. Futásidejű támogatás az alsóbb rétegbeli eszközök az átjárón keresztül kiépítése jelenleg nem támogatott.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>Az IoT Edge átjáróeszköz, amely az IoT Edge-átjáró által biztosított identitással csatlakozik az alsóbb rétegbeli eszközökhöz

![IoT Edge identitás nélküli alsóbb rétegű eszközzel](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Az IoT Edge-eszköz ki van építve az IoT Central ban az IoT Edge-eszközhöz csatlakoztatott alsóbb rétegbeli eszközökkel együtt. Az alsóbb rétegbeli eszközök identitását biztosító és az alsóbb rétegbeli eszközök üzembe helyezhető üzembe helyezhető ek támogatása jelenleg nem támogatott. Ha saját identitásfordítási modult hoz, az IoT Central támogatja ezt a mintát.

## <a name="cloud-gateway"></a>Felhőátjáró

Az Azure IoT Central az Azure IoT Hubot felhőalapú átjáróként használja, amely lehetővé teszi az eszközkapcsolatot. Az IoT Hub engedélyezi:

- Adatok betöltése nagy méretekben a felhőben.
- Eszközkezelés.
- Biztonságos eszközkapcsolat.

Ha többet szeretne megtudni az IoT Hubról, olvassa el az [Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub/)

Ha többet szeretne megtudni az eszközkapcsolatról az Azure IoT Centralban, olvassa el [az Eszközkapcsolat](concepts-get-connected.md).

## <a name="data-stores"></a>Adattárolók

Az Azure IoT Central a felhőben tárolja az alkalmazásadatokat. A tárolt alkalmazásadatok a következőket tartalmazzák:

- Eszközsablonok.
- Eszközidentitások.
- Eszköz metaadatai.
- Felhasználói és szerepköradatok.

Az Azure IoT Central idősorozat-tárolót használ az eszközökről küldött mérési adatokhoz. Idősorozat-adatok az elemzési szolgáltatás által használt eszközökről.

## <a name="analytics"></a>Elemzés

Az elemzési szolgáltatás felelős az alkalmazás által megjelenített egyéni jelentési adatok létrehozásáért. Az operátor testreszabhatja az alkalmazásban megjelenített [elemzéseket.](howto-create-analytics.md) Az elemzési szolgáltatás az [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) ra épül, és feldolgozza az eszközökről küldött mérési adatokat.

## <a name="rules-and-actions"></a>Szabályok és műveletek

[A szabályok és műveletek](tutorial-create-telemetry-rules.md) szorosan együttműködnek az alkalmazáson belüli feladatok automatizálása érdekében. A szerkesztő az eszköz telemetriai adatai alapján definiálhat szabályokat, például a meghatározott küszöbértéket meghaladó hőmérséklet alapján. Az Azure IoT Central egy adatfolyam-processzor t használja annak meghatározására, hogy a szabályfeltételek mikor teljesülnek. Ha egy szabályfeltétel teljesül, a szerkesztő által meghatározott műveletet indít el. Egy művelet például e-mailt küldhet, amelyben értesíti a mérnököt, hogy az eszköz hőmérséklete túl magas.

## <a name="metadata-management"></a>Metaadatok kezelése

Az Azure IoT Central-alkalmazásokban az eszközsablonok határozzák meg az eszköztípusok viselkedését és képességeit. Például egy hűtőszekrény eszköz sablon határozza meg a telemetriai adatokat egy hűtőszekrény küld az alkalmazásnak.

![Sablon architektúra](media/concepts-architecture/template-architecture.png)

Egy IoT Central alkalmazáseszköz-sablon tartalmazza:

- **Az eszközképességi modellek** határozzák meg az eszköz képességeit, például az általa küldött telemetriai adatokat, az eszköz állapotát meghatározó tulajdonságokat és az eszköz által válaszoló parancsokat. Az eszközképességek egy vagy több felületbe vannak rendezve. Az eszközképességi modellekről az [IoT Plug and Play (előzetes verzió)](../../iot-pnp/overview-iot-plug-and-play.md) dokumentációjában olvashat bővebben.
- **A felhőtulajdonságok** az iot central áruházak tulajdonságait határozzák meg az eszközhöz. Ezek a tulajdonságok csak az IoT Central ban vannak tárolva, és soha nem kerülnek elküldésre egy eszközre.
- **A nézetek** határozzák meg az irányítópultokat, és a szerkesztő által létrehozott űrlapokat, amelyek lehetővé teszik az üzemeltető számára az eszközök figyelése és kezelése.
- A testreszabások lehetővé **teszik,** hogy a szerkesztő felülbírálja az eszközképességi modell egyes definícióit, hogy azok relevánsabbak legyenek az IoT Central alkalmazás számára.

Egy alkalmazás rendelkezhet egy vagy több szimulált és valós eszközök alapján minden eszköz sablon.

## <a name="data-export"></a>Adatexportálás

Egy Azure IoT Central-alkalmazásban [folyamatosan exportálhatja az adatokat](howto-export-data.md) a saját Azure Event Hubs és Azure Service Bus-példányaiba. Az adatokat rendszeres időközönként exportálhatja az Azure Blob tárfiókjába is. Az IoT Central exportálhatja a méréseket, az eszközöket és az eszközsablonokat.

## <a name="batch-device-updates"></a>Kötegeszköz-frissítések

Egy Azure IoT Central-alkalmazásban [létrehozhat és futtathat feladatokat](howto-run-a-job.md) a csatlakoztatott eszközök kezeléséhez. Ezek a feladatok lehetővé teszik az eszköz tulajdonságainak vagy beállításainak tömeges frissítését, illetve parancsok futtatását. Létrehozhat például egy feladatot, amely növeli a ventilátor sebességét több hűtött automatánál.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

A [rendszergazda hozzáférési szabályokat definiálhat](howto-manage-users-roles.md) egy Azure IoT Central alkalmazáshoz az előre definiált szerepkörök egyikével vagy egy egyéni szerepkör létrehozásával. A szerepkörök határozzák meg, hogy a felhasználó milyen területeken férhet hozzá a felhasználóhoz, és milyen műveleteket hajthat végre.

## <a name="security"></a>Biztonság

Az Azure IoT Central biztonsági funkciói a következők:

- Az adatok átvitel közben és inaktív módon titkosítva vannak.
- A hitelesítést az Azure Active Directory vagy a Microsoft-fiók biztosítja. A kétfaktoros hitelesítés támogatott.
- Teljes bérlői elkülönítés.
- Eszközszintű biztonság.

## <a name="ui-shell"></a>Felhasználói felület i héja

A felhasználói felület héja egy modern, érzékeny, HTML5 böngésző alapú alkalmazás.
A rendszergazda egyéni témák alkalmazásával testreszabhatja az alkalmazás felhasználói felületét, és módosíthatja a súgóhivatkozásokat, hogy a saját egyéni súgóforrásaira mutasson. Ha többet szeretne megtudni a felhasználói felület testreszabásáról, olvassa [el az Azure IoT Central UI testreszabása](howto-customize-ui.md) című cikket.

Az operátor személyre szabott alkalmazás-irányítópultokat hozhat létre. Több irányítópultja is lehet, amelyek különböző adatokat jelenítenek meg, és váltanak közöttük.

## <a name="next-steps"></a>További lépések

Most, hogy már megismerkedett az Azure IoT Central architektúrájával, a javasolt következő lépés az [eszközkapcsolat](concepts-get-connected.md) megismerése az Azure IoT Centralban.
