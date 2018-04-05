---
title: IoT Hub létrehozása az Azure-portál használatával |} Microsoft Docs
description: Hogyan létrehozására, kezelésére és törlése az Azure IoT hub az Azure portálon keresztül. Tarifacsomagok, a méretezés, a biztonsági, és a konfigurációs üzenetküldésre kapcsolatos adatokat tartalmaz.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 6ffde076caff6217bf6255c9294eca63d3e39b85
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Létrehoz egy IoT-központot, az Azure portál használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk ismerteti:

* Hol találhatók az IoT-központ szolgáltatás az Azure portálon.
* Megtudhatja, hogyan hozhatja létre és kezelheti az IoT-központok.

## <a name="where-to-find-the-iot-hub-service"></a>Hol található az IoT-központ szolgáltatás

Az IoT-központ szolgáltatás a következő helyeken a portálon találja meg:

* Válasszon **+ új**, majd válassza a **az eszközök internetes hálózatát**.
* Válassza ki a piactér **az eszközök internetes hálózatát**.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az IoT-központ a következő módszerekkel hozhat létre:

* A **+ új** lehetőség megnyitja a panel az alábbi képernyőfelvételen látható. Az IoT hub létrehozása ezzel a módszerrel, és a piactéren keresztül lépései megegyeznek.
* Válassza ki a piactér **létrehozása** az alábbi képernyőfelvételen látható panel megnyitásához.

A következő szakaszok ismertetik a számos lépést kelljen IoT hub létrehozása:

### <a name="choose-the-name-of-the-iot-hub"></a>Válassza ki az IoT hub nevét

Létrehoz egy IoT-központot, adjon nevet az IoT-központot. Ez a név minden IoT-központok között egyedinek kell lennie.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>A tarifacsomag választható

Választhatja ki több rétegek szolgáltatások attól függően, hogy hány kívánt és hány üzenetek küldése az / nap-megoldáson keresztül. Ingyenes szint készült tesztelésében és értékelésében. Ez lehetővé teszi, hogy a csatlakoztatva kell lennie az IoT hub és 8000 üzenet naponta legfeljebb 500 eszközök. Minden Azure-előfizetést hozhat létre egy IoT-központ ingyenes szint. 

Réteg beállításokkal kapcsolatos részletekért lásd: [kiválasztása a megfelelő IoT-központ réteg](iot-hub-scaling.md).

### <a name="iot-hub-units"></a>IoT hub-egységek

Napi egység értesítésenként engedélyezett üzenetek száma attól függ, hogy a központ tarifacsomag. Például ha azt szeretné, hogy az IoT hub érkező üzenetek 700 000 támogatásához, választja két S1 réteg egység.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Erőforráscsoport és a felhőalapú eszköz

Módosíthatja az IoT-központ a partíciók száma. A partíciók alapértelmezett értéke 4, akkor egy másik számot a legördülő listából.

Nem kell explicit módon hozzon létre egy üres erőforráscsoportot. Amikor létrehoz egy erőforrást, válassza ki vagy hozzon létre egy új, vagy használjon egy meglévő erőforráscsoportot.

![][5]

### <a name="choose-subscription"></a>Válassza ki az előfizetést

Az Azure IoT Hub automatikusan az Azure-előfizetéseket a felhasználói fiókhoz kapcsolódó sorolja fel. Lehetősége van az Azure-előfizetés társítása az IoT-központ számára.

### <a name="choose-the-location"></a>Válassza ki azt a helyet

A hely lehetőséget a régiókban, ahol az IoT-központ lehetőség a listáját jeleníti meg.

### <a name="create-the-iot-hub"></a>Az IoT hub létrehozása

Ha az összes előző lépést, létrehozhat az IoT-központ. Kattintson a **létrehozása** a háttér-folyamat létrehozása és telepítése az IoT-központ a kiválasztott beállításokkal.

Az IoT hub létrehozása, mivel némi időre van a háttér-központi telepítés a megfelelő hely kiszolgálójára futtatásához néhány percet is igénybe vehet.

## <a name="change-the-settings-of-the-iot-hub"></a>Az IoT hub beállításainak módosítása

Módosíthatja egy meglévő IoT-központ beállításait az IoT Hub panel a létrehozása után.

![][8]

**Megosztott hozzáférési házirendek**: ezek a házirendek meghatározása csatlakozni az IoT Hub eszközöket és szolgáltatásokat engedélyeit. Ezek a házirendek eléréséhez kattintson **megosztott elérési házirendek** alatt **általános**. Ezen a panelen módosíthatja a meglévő házirendeket, vagy adjon hozzá egy új házirendet.

### <a name="create-a-policy"></a>Szabályzat létrehozása

* Kattintson a **Hozzáadás** egy panel megnyitásához. Itt adhatja meg az új házirend nevét és a kívánt engedélyek társítása a házirend, az alábbi ábrán látható módon:

    Nincsenek társítható megosztott szabályzatokról több engedélyeket. A **beállításkulcs olvasása** és **beállításjegyzék írási** házirendek jogosultságokat olvasási és írási hozzáférést biztosít az identitásjegyzékhez. Válassza az írási automatikusan úgy dönt, olvassa el a beállítást.

    A **Service csatlakozás** házirend Szolgáltatásvégpontok elérésére engedélyt **eszközről a felhőbe kap**. A **eszköz csatlakozzon** házirend engedélyt ad az IoT-központ eszközoldali végpontokon használatával üzenetek küldése és fogadása.

* Kattintson a **létrehozása** hozzáadása az újonnan létrehozott házirend a meglévő listájához.

![][10]

## <a name="endpoints"></a>Végpontok

Kattintson a **végpontok** az IoT hub, amely módosítja a végpontok listájának megjelenítéséhez. A végpontok két típusa van: az IoT hub beépített végpontok, és a létrehozása után az IoT hub hozzáadott végpontok.

![][11]

### <a name="built-in-endpoints"></a>Beépített végpontok

Nincsenek a két beépített végpont: **eszköz visszajelzéseire felhő** és **események**.

* **Az eszköz visszajelzései alapján felhő** beállítások: Ezzel a beállítással rendelkezik két subsettings: **eszköz TTL felhő** (idő TTL) és **megőrzési idő** (órákban) üzenetekhez. Az első létrehoz egy IoT-központot, mindkét ezeket a beállításokat kell az alapértelmezett érték egy óra. Ezeket a beállításokat, a csúszkákkal, vagy írja be az értékeket.
* **Események** beállítások: Ezzel a beállítással rendelkezik több subsettings, amelyek némelyike csak olvasható. Az alábbi lista ismerteti ezeket a beállításokat:

  * **Partíciók**: egy alapértelmezett értéke az IoT hub létrehozásakor. Módosíthatja ezt a beállítást a partíciók száma.

  * **Event Hub-kompatibilis nevének és végpontjának**: Ha az IoT-központ jön létre, az Eseményközpont létrehozásakor belső, hogy szükség lehet a hozzáférést bizonyos körülmények között. Nem szabhatja testre az Event Hub-kompatibilis és a végpont értékét, de kattintva másolhatja **másolási**.

  * **Megőrzési idő**: alapértelmezés szerint egy nap beállítva, de ez a legördülő lista használatával módosítható. Ez az érték a nap az eszközről a felhőbe beállítás van.

  * **Felhasználói csoportok**: fogyasztói csoportok lehetővé teszik az üzenetek egymástól függetlenül olvasni az IoT hub több olvasók. Minden IoT-központot egy alapértelmezett felhasználói csoport jön létre. Azonban hozzáadása vagy az IoT-központok ezzel a beállítással a fogyasztói csoportok törlése.

  > [!NOTE]
  > Az alapértelmezett felhasználói csoport nem szerkeszthető, és nem törölhető.

### <a name="custom-endpoints"></a>Egyéni végpontok

Egyéni végpontokat az IoT hub, a portál használatával is hozzáadhat. Az a **végpontok** panelen kattintson **Hozzáadás** megnyitásához tetején a **végpont hozzáadása** panel. Adja meg a szükséges adatokat, majd kattintson a **OK**. Az egyéni végpontjának mostantól szerepel a fő **végpontok** panelen.

![][13]

További tudnivalók az egyéni végpontokat [referencia - IoT-központok végpontjai][lnk-devguide-endpoints].

## <a name="routes"></a>Útvonalak

Kattintson a **útvonalak** hogyan IoT-központ kiszállítja az eszközről a felhőbe üzenetek kezeléséhez.

![][14]

Hozzáadhat útvonalak az IoT hub kattintva **Hozzáadás** tetején a **útvonalak*** panelen írja be a szükséges adatokat, és kattintson a **OK**. Az útvonal majd szerepel a fő **útvonalak** panelen. Útvonalak listájában kattintva szerkesztheti egy útvonalat. Ahhoz, hogy egy olyan útvonalat, kattintson az útvonalak a listában, és állítsa be a **engedélyezve** kapcsolót **ki**. A módosítás mentéséhez kattintson **OK** a panel alján.

![][15]

## <a name="delete-the-iot-hub"></a>Az IoT hub törlése

Az IoT hub törli kattintva tallózással is **Tallózás**, majd kiválasztja a megfelelő hub törlése. Az IoT hub törléséhez kattintson a **törlése** az IoT-központnév gombra.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Tömeges az IoT-eszközök kezelése][lnk-bulk]
* [Az IoT-központ metrikák][lnk-metrics]
* [Figyelési műveletek][lnk-monitor]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]
* [Az IoT-megoldásból az alapoktól biztonságos mentése][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
