---
title: "IoT Hub létrehozása az Azure-portál használatával |} Microsoft Docs"
description: "Hogyan létrehozására, kezelésére és törlése az Azure IoT hub az Azure portálon keresztül. Tarifacsomagok, a méretezés, a biztonsági, és a konfigurációs üzenetküldésre kapcsolatos adatokat tartalmaz."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: dobett
ms.openlocfilehash: bca7eea5f44bbed3b784b56edaac235161b43e5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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

Négy szintek közül választhat: **szabad**, **szabványos 1** és **szabványos 2**, és **Standard S3**. Ingyenes szint lehetővé teszi, hogy a csatlakoztatva kell lennie az IoT-központot, és legfeljebb 8000 üzenet naponta csak 500 eszközök.

**Standard szintű, S1**: S1 kiadás nagyszámú eszközöket, hogy mindegyik generál kis mennyiségű adatokat az IoT-megoldások használata. Az S1 kiadás minden egységével naponta 400 ezer üzenetet lehet továbbítani az összes csatlakoztatott eszközön.

**Standard S2**: S2 verzióját használja, amelyben eszközök nagy mennyiségű adat készítése az IoT-megoldások. S2 kiadás tárolóegységekhez lehetővé teszi, hogy minden csatlakoztatott eszközön közötti naponta 6 millió üzeneteket.

**Standard S3**: S3 verzióját használja, amely nagy mennyiségű adat az IoT-megoldások. S3 kiadás tárolóegységekhez lehetővé teszi, hogy akár 300 millió üzenetek / nap közötti minden csatlakoztatott eszközön.

![][4]

> [!NOTE]
> Az IoT-központ csak egy ingyenes hub / Azure-előfizetés teszi lehetővé.

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

### <a name="create-a-policy"></a>Házirend létrehozása

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

### <a name="custom-endpoints"></a>Egyéni végpontokat

Egyéni végpontokat az IoT hub, a portál használatával is hozzáadhat. Az a **végpontok** panelen kattintson **Hozzáadás** megnyitásához tetején a **végpont hozzáadása** panel. Adja meg a szükséges adatokat, majd kattintson a **OK**. Az egyéni végpontjának mostantól szerepel a fő **végpontok** panelen.

![][13]

További tudnivalók az egyéni végpontokat [referencia - IoT-központok végpontjai][lnk-devguide-endpoints].

## <a name="routes"></a>Útvonalak

Kattintson a **útvonalak** hogyan IoT-központ kiszállítja az eszközről a felhőbe üzenetek kezeléséhez.

![][14]

Hozzáadhat útvonalak az IoT hub kattintva **Hozzáadás** tetején a **útvonalak*** panelen írja be a szükséges adatokat, és kattintson a **OK**. Az útvonal majd szerepel a fő **útvonalak** panelen. Útvonalak listájában kattintva szerkesztheti egy útvonalat. Ahhoz, hogy egy olyan útvonalat, kattintson az útvonalak a listában, és állítsa be a **engedélyezve** kapcsolót **ki**. A módosítás mentéséhez kattintson **OK** a panel alján.

![][15]

## <a name="pricing-and-scale"></a>Díjszabás és méretezés

Egy meglévő IoT-központot árképzési keresztül módosítható a **árazás** beállításait, a következő kivételekkel:

* Az aktuális megvalósításában egy IoT hubot szabad SKU nem rétegek egy olyan címre változtassák a fizetős SKU, vagy fordítva.
* Csak lehet egy ingyenes szint IoT-központ az Azure-előfizetésben.

![][12]

Áthelyezheti egy magasabb az alacsonyabb szint csak akkor, ha adott napon küldött üzenetek száma túllépi a kvótát, az alacsonyabb szinten. Például ha naponta üzenetek száma meghaladja a 400000, majd az IoT hub rétegét módosíthatja. Azonban ha megváltoztatja az S1 réteghez majd az IoT hub folyamatban van az adott nap.

## <a name="delete-the-iot-hub"></a>Az IoT hub törlése

Az IoT hub törli kattintva tallózással is **Tallózás**, majd kiválasztja a megfelelő hub törlése. Az IoT hub törléséhez kattintson a **törlése** az IoT-központnév gombra.

## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Tömeges az IoT-eszközök kezelése][lnk-bulk]
* [Az IoT-központ metrikák][lnk-metrics]
* [Figyelési műveletek][lnk-monitor]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Egy eszköz szimulálva IoT oldala][lnk-iotedge]
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
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
