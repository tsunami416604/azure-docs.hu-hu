---
title: IoT Hub létrehozása az Azure portal használatával |} A Microsoft Docs
description: Hogyan létrehozása, kezelése és törlése az Azure IoT hubs az Azure Portalon keresztül. Tarifacsomagok, a méretezés, biztonság, és a konfigurációs messaging kapcsolatos információkat tartalmaz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: 6f070462dca6e835504a33014a7ed48e9949f874
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432632"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Hozzon létre egy IoT hubra az Azure portal használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet az IoT hub használata a [az Azure portal](https://portal.azure.com).

Ebben az oktatóanyagban a lépéseket használatához Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Az IoT hub beállításainak módosítása

A beállítások egy meglévő IoT hub az IoT Hub panel a létrehozása után módosíthatja.

![Képernyőfelvétel: az IoT hub beállításai](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Íme néhány az IoT hub beállítható tulajdonságokat:

**Díjszabás és méretezés**: Ez a tulajdonság segítségével migrálása másik tarifacsomagra vagy csak az IoT Hub-egységek. 

**Műveletek figyelése**: A különböző figyelési kategóriák be és ki, például az eszköz a felhőbe irányuló üzenetek vagy a felhőből az eszközre irányuló üzenetek kapcsolatos események naplózását.

**IP-szűrő**: Adjon meg egy IP-címtartományt, amely elfogadását, vagy az IoT hub által elutasítva.

**Tulajdonságok**: Tulajdonságok listáját jeleníti meg, hogy másolja, majd használja máshol, például az erőforrás-azonosító, erőforráscsoport, helye és így tovább.

### <a name="shared-access-policies"></a>Megosztott elérési házirendek

Is megtekintheti vagy módosíthatja a megosztott elérési szabályzatok listáján, kattintva **megosztott elérési házirendek** a a **beállítások** szakaszban. Ezek a szabályzatok az IoT hubhoz való csatlakozáshoz eszközökön és szolgáltatásokon engedélyeinek megadásához. 

Kattintson a **Hozzáadás** megnyitásához a **hozzáadása egy megosztott elérési házirendet** panelen.  Az új házirend nevét és az engedélyeket, társítani szeretné az ehhez a szabályzathoz, az alábbi ábrán látható módon adhat meg:

![Képernyőfelvétel: a megosztott elérési szabályzat hozzáadása](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* A **jegyzék olvasása** és **jegyzék írása** házirendek jogosultságokat olvasási és írási hozzáférést biztosít az identitásjegyzékhez. Ezek az engedélyek segítségével háttérbeli felhőszolgáltatásokat eszközidentitások kezelése. Az írási lehetőség kiválasztása automatikusan úgy dönt, az olvasási lehetőség.

* A **szolgáltatás csatlakozása** házirend engedélyt ad a Szolgáltatásvégpontok eléréséhez. Ezt az engedélyt az üzenetek küldése és fogadása eszközökön is frissítése és az eszköz iker és modul ikereszköz adatok olvasása a háttér-felhőszolgáltatások használják.

* A **eszköz csatlakozni** házirend engedélyt ad az IoT Hub eszközoldali végpontokon használatával üzenetek küldése és fogadása. Ez az engedély küld és fogad üzeneteket egy IoT hubot, frissítése és eszköz ikereszköz és modul ikereszköz adatok olvasását és fájlt töltsön fel az eszközök használják.

Kattintson a **létrehozás** hozzáadása az újonnan létrehozott szabályzat a meglévő listára.

További részletes információ a konkrét engedélyeket által biztosított hozzáférést: [az IoT Hub engedélyekkel](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="message-routing-for-an-iot-hub"></a>IoT hub üzenet-útválasztás

Kattintson a **üzenet-útválasztása** alatt **üzenetkezelés** , a üzenet-útválasztása ablaktáblája, ahol megadhatja a útvonalak és az egyedi végpontok hub. [Üzenet-útválasztása](iot-hub-devguide-messages-d2c.md) lehetővé teszi, hogy milyen adatokat is küld az eszközök a végpontok kezeléséhez. Az első lépéseként adjon hozzá egy új útvonalat. Majd adja hozzá az útvonal egy meglévő végpontot, vagy hozzon létre egy újat a típusú támogatott, például a blob storage-bA. 

![Üzenet-útválasztási panel](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Útvonalak

Útvonalak a üzenet-útválasztása panelen az első lapra. Adjon hozzá egy új útvonalat, kattintson a +**Hozzáadás**. A következő képernyő jelenik meg. 

![Képernyőfelvétel: az új útvonal hozzáadása](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Adjon nevet a központnak. A név a lista az adott hub útvonalak egyedinek kell lennie. 

A **végpont**, válasszon egyet a legördülő listából, vagy vegyen fel egy másikat. Ebben a példában egy tárfiókot és tárolót már rendelkezésre állnak. -Végpontként hozzáadni őket, kattintson a +**Hozzáadás** mellett a végpont legördülő listából, és válasszon **Blob Storage**. Az alábbi képernyőn látható, hogy hol vannak megadva a tárfiókot és tárolót.

![Képernyőfelvétel: a storage-végpont az útválasztási szabály hozzáadása](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Kattintson a **tároló kiválasztása** válassza ki a tárfiókot és tárolót. Ha kiválasztotta ezeket a mezőket, akkor a végpont panelre adja vissza. Használja az alapértelmezett értékeket a többi mező és **létrehozás** hozhat létre a storage-fiók végpontját, és adja hozzá az útválasztási szabályokat.

A **adatforrás**, válassza ki az eszköz Telemetriai üzeneteket. 

Ezután adjon hozzá egy útválasztási lekérdezés. Ebben a példában egy alkalmazás nevű tulajdonsággal rendelkező üzenetek `level` egyenlő értékű `critical` kapcsolódóak pedig az a tárfiók.

![Képernyőkép: egy új útválasztási szabály mentése](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Kattintson a **mentése** az útválasztási szabály mentéséhez. Az üzenet-útválasztása ablaktáblán adja vissza, és az új útválasztási szabály jelenik meg.

### <a name="custom-endpoints"></a>Egyéni végpontok

Kattintson a **egyéni végpontok** fülre. Láthatja, hogy már létrehozott bármely egyéni végpontok. Itt új végpontok hozzáadása vagy a meglévő végpontok törlése. 

> [!NOTE]
> Ha töröl egy útvonalat, nem törli a végpontok útvonal rendelve. Végpont törlése az egyéni végpontok lapon, válassza ki a törölni kívánt végpontot, és kattintson a Törlés gombra.
>

További információ az egyéni végpontokról [referencia – IoT hub-végpontok](iot-hub-devguide-endpoints.md).

IoT hub legfeljebb 10 egyéni végpontok határozhatja meg. 

Egy teljes példa bemutatja, hogyan használja az egyedi végpontok az Útválasztás, olvassa el [üzenetet az IoT Hub útválasztás](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Egy adott IoT hub található

Az alábbiakban két módon találhatja meg. egy adott IoT hubot az előfizetésében:

1. Ha tudja, hogy az erőforrást, amely az IoT hub tartozik csoportjában kattintson a **erőforráscsoportok**, majd válassza ki az erőforráscsoportot a listából. Az erőforrás csoport képernyőn látható összes erőforrást benne, beleértve az IoT hubokat. Kattintson a hub, amelynek a találatokat.

2. Kattintson a **összes erőforrás**. Az a **összes erőforrás** ablaktáblán egy legördülő listája, amely alapértelmezés szerint a `All types`. Kattintson a legördülő listában, törölje a jelet `Select all`. Keresés `IoT Hub` , és ellenőrizze, hogy. Kattintson a gombra kattintva zárja be azt a legördülő listában, és a bejegyzések lesznek szűrve, csak az IoT-központok megjelenítése.

## <a name="delete-the-iot-hub"></a>Az IoT hub törlése

Az Iot hub törléséhez keresse meg az IoT hub szeretne törölni, majd kattintson a **törlése** gomb alatti az IoT hub nevére.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Az IoT Hub üzenet-útválasztása](tutorial-routing.md)
* [Az IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)