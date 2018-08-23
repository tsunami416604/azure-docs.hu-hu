---
title: IoT Hub létrehozása az Azure portal használatával |} A Microsoft Docs
description: Hogyan létrehozása, kezelése és törlése az Azure IoT hubs az Azure Portalon keresztül. Tarifacsomagok, a méretezés, biztonság, és a konfigurációs messaging kapcsolatos információkat tartalmaz.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 0b03ae434e93dbab45235fe67c499497e1257064
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42059479"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Hozzon létre egy IoT hubra az Azure portal használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk ismerteti:

* Hogyan találhatja meg az IoT Hub szolgáltatás az Azure Portalon.
* Hogyan hozhat létre, és az IoT hub kezelése.

## <a name="where-to-find-the-iot-hub-service"></a>Az IoT Hub szolgáltatás megkeresése

Az IoT Hub szolgáltatás a portálon a következő helyeken találhatja meg:

* Válasszon **+ új**, majd válassza a **IOT-**.
* Válassza ki a piactéren **IOT-**.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az IoT hub a következő módszerekkel hozhat létre:

* A **+ új** lehetőség megnyitja a panelt, az alábbi képernyőfelvételen látható. Az IoT hub létrehozásához – ezt a módszert, és a Marketplace-en keresztül lépései megegyeznek.

* Válassza ki a piactéren **létrehozás** az alábbi képernyőfelvételen látható a panel megnyitásához.

A következő szakaszok ismertetik a több lépés végrehajtásával hozzon létre egy IoT hubot.

### <a name="choose-the-name-of-the-iot-hub"></a>Válassza ki az IoT hub nevét

Hozzon létre egy IoT hubot, adjon nevet az IoT hubon. Ez a név az összes IoT-központok között egyedinek kell lennie.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Válassza ki a tarifacsomagot

Választhat több szinten funkciókat attól függően, hogy hány szeretne, és hány üzenetek küldése naponta a megoldáson keresztül. Az ingyenes szintjének célja, tesztelés és értékelés céljából használják. Lehetővé teszi a 500 eszközt az IoT hubhoz, és legfeljebb napi 8000 üzenet továbbítását kell csatlakoztatni. Minden Azure-előfizetés hozhat létre egy IoT Hub ingyenes szintjéhez. 

A többi szint lehetőség részleteiért lásd: [a megfelelő IoT Hub-csomag kiválasztása](iot-hub-scaling.md).

### <a name="iot-hub-units"></a>IoT hub-egységek

/ Egység / nap engedélyezett üzenetek száma attól függ, hogy a hub tarifacsomagot. Például ha azt szeretné, hogy támogatja a bejövő forgalom 700 000 üzenetet az IoT hub, választhatja azt két S1 szintű egységre.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Eszköz-felhőbe típusú partíciók és erőforráscsoport

Módosíthatja az IoT hub a partíciók számát. A partíciók számának alapértelmezett érték 4; egy másik számot választhat a legördülő listából.

Nem kell explicit módon hozzon létre egy üres erőforráscsoportot. Amikor létrehoz egy erőforrást, válassza ki vagy hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot.

![Képernyőfelvétel: a központ létrehozása az Azure Portalon](./media/iot-hub-create-through-portal/location1.png)

### <a name="choose-subscription"></a>Válasszon előfizetést

Az Azure IoT Hub automatikus sorolja fel az Azure-előfizetést a felhasználói fiók össze van kapcsolva. Kiválaszthatja, hogy az Azure-előfizetés társítása az IoT hub segítségével.

### <a name="choose-the-location"></a>Válassza ki a helyet

A hely beállítást a régióban, ahol az IoT Hub elérhető listáját tartalmazza.

### <a name="create-the-iot-hub"></a>Az IoT hub létrehozása

Ha végzett az összes előző lépést, az IoT hub hozhat létre. Kattintson a **létrehozás** a háttérrendszer folyamat létrehozása és üzembe helyezése az IoT hub a kiválasztott beállításokkal.

Az IoT hub létrehozása, amely szükséges idő a háttérrendszer központi telepítés a megfelelő hely kiszolgálójára futtatása néhány percet vesz igénybe.

## <a name="change-the-settings-of-the-iot-hub"></a>Az IoT hub beállításainak módosítása
<!--robinsh these screenshots are out of date -->

Az IoT Hub panel a létrehozása után módosíthatja egy meglévő IoT hubbal beállításait.

![Képernyőfelvétel: az IoT hub beállításai](./media/iot-hub-create-through-portal/portal-settings.png)

**Megosztott hozzáférési házirendek**: ezek a szabályzatok az IoT hubhoz való csatlakozáshoz eszközöket és szolgáltatásokat engedélyeinek megadásához. Ezekkel a szabályzatokkal elérheti kattintva **megosztott elérési házirendek** alatt **általános**. Ezen a panelen módosíthatja a meglévő házirendeket, vagy adjon hozzá egy új házirendet.

### <a name="create-a-policy"></a>Szabályzat létrehozása

* Kattintson a **Hozzáadás** kattintva megnyílik egy panel. Itt adhatja meg az új házirend nevét és az engedélyeket, társítani szeretné az ehhez a szabályzathoz, az alábbi ábrán látható módon:

    Nincsenek számos az engedélyeket, amelyek a megosztott házirendek társítható. A **jegyzék olvasása** és **jegyzék írása** házirendek jogosultságokat olvasási és írási hozzáférést biztosít az identitásjegyzékhez. Az írási lehetőség kiválasztása automatikusan úgy dönt, az olvasási lehetőség.

    A **szolgáltatás csatlakozása** házirend engedélyt ad a Szolgáltatásvégpontok eléréséhez **kap az eszközről a felhőbe**. A **eszköz csatlakozni** házirend engedélyt ad az IoT Hub eszközoldali végpontokon használatával üzenetek küldése és fogadása.

* Kattintson a **létrehozás** hozzáadása az újonnan létrehozott szabályzat a meglévő listára.

   ![Képernyőfelvétel: a megosztott elérési szabályzat hozzáadása](./media/iot-hub-create-through-portal/shared-access-policies.png)

## <a name="endpoints"></a>Végpontok

Kattintson a **végpontok** olyan végpontok, amely módosítja az IoT hub listájának megjelenítéséhez. A végpontok két típusa van: az IoT hub beépített végpont, és adja hozzá az IoT hub létrehozása után végpontokat.

![Képernyőfelvétel: a végpont hozzáadása](./media/iot-hub-create-through-portal/messaging-settings.png)

### <a name="built-in-endpoints"></a>Beépített végpontok

Két beépített végpontja van: **eszköz visszajelzés felhő** és **események**.

* **Eszköz felhasználói visszajelzések felhőalapú** beállítások: Ezzel a beállítással rendelkezik két subsettings: **Cloud, TTL eszköz** (time-to-live) és **megőrzési időtartama** (órákban) az üzenetek. Az első IoT hub létrehozása, ha mindkét ezeket a beállításokat kell az alapértelmezett érték egy óra. Ezeket a beállításokat, a csúszkával, vagy írja be az értékeket.

* **Események** beállítások: Ezzel a beállítással több subsettings írásvédettek, amelyek egy része van. Az alábbi lista ismerteti ezeket a beállításokat:

  * **A partíciók**: alapértelmezett érték van beállítva, amikor az IoT hubot létre kívánja hozni. Ez a beállítás révén a partíciók számának módosítása

  * **Event Hub-kompatibilis névvel és -végpont**: amikor az IoT hub létrehozása, amikor egy Eseményközpont létrejön belsőleg, hogy szükség lehet a hozzáférést bizonyos körülmények között. Nem szabhatja testre az Event Hub-kompatibilis nevének és végpontjának értékeit, de kattintva másolhatja azokat **másolási**.

  * **Megőrzési idő**: alapértelmezés szerint egy nap beállítása, de ezt módosíthatja a legördülő lista használatával. Ez az érték nap az eszközről a felhőbe beállítás szerepel.

  * **Fogyasztói csoportok**: fogyasztói csoportok lehetővé teszik az üzenetek egymástól függetlenül olvasásához az IoT hubról kibővítése több olvasóra. Minden IoT hub egy alapértelmezett felhasználói csoport jön létre. Azonban adja hozzá, vagy a fogyasztói csoportok törlése az IoT hubon, ezt a beállítást használja.

  > [!NOTE]
  > Az alapértelmezett felhasználói csoport nem szerkeszthető és nem törölhető.

### <a name="custom-endpoints"></a>Egyéni végpontok

Az egyedi végpontok is hozzáadhat az IoT hubon a portál használatával. Az a **végpontok** panelen kattintson a **Hozzáadás** megnyitásához tetején a **végpont hozzáadása** panelen. Adja meg a szükséges információkat, majd kattintson a **OK**. Egyéni végpontra megjelenik a fő **végpontok** panelen.

![Egyéni végpont létrehozását bemutató képernyőkép](./media/iot-hub-create-through-portal/endpoint-creation.png)

További információ az egyéni végpontokról [referencia – IoT hub-végpontok]( iot-hub-devguide-endpoints.md).

## <a name="routes"></a>Útvonalak

Kattintson a **útvonalak** hogyan továbbítja az IoT Hub a az eszközt a felhőbe irányuló üzenetek kezeléséhez.

![Képernyőfelvétel: az új útvonal hozzáadása](./media/iot-hub-create-through-portal/routes-list.png)

Kattintson az IoT hubhoz útvonalakat is hozzáadhat **Hozzáadás** felső részén a **útvonalak*** panelen írja be a szükséges adatokat, és kattintson a **OK**. Az útvonal majd szerepel-e a fő **útvonalak** panelen. Egy útvonalat, útvonalak listájában kattintva szerkesztheti. Ahhoz, hogy egy útvonalat, kattintson rá az útvonalak listájában, és állítsa be a **engedélyezve** kapcsolót **ki**. A módosítás mentéséhez kattintson az **OK** a panel alján.

![Képernyőfelvétel: az új útválasztási szabály szerkesztése](./media/iot-hub-create-through-portal/route-edit.png)

## <a name="delete-the-iot-hub"></a>Az IoT hub törlése

Az IoT hub törli kattintva megnyithatja **Tallózás**, majd kiválasztja a megfelelő hub törlése. Az IoT hub törléséhez kattintson a **törlése** gomb alatti az IoT hub nevére.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [IoT-eszközök tömeges felügyelete](iot-hub-bulk-identity-mgmt.md)
* [Az IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
* [Az IoT-megoldás az alapoktól biztonságos mentése](../iot-fundamentals/iot-security-ground-up.md)
