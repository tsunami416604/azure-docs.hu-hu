---
title: IoT-központ létrehozása az Azure Portalon | Microsoft dokumentumok
description: Azure IoT-központok létrehozása, kezelése és törlése az Azure Portalon keresztül. A tarifacsomagokra, a méretezésre, a biztonságra és az üzenetküldési konfigurációra vonatkozó információkat tartalmazza.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: c43c142b22709d42416b2dd14dfc78812970916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284732"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>IoT-központ létrehozása az Azure Portalon

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet IoT-központokat az [Azure Portal](https://portal.azure.com)használatával.

Az oktatóanyag lépései használatához Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Az IoT hub beállításainak módosítása

Módosíthatja egy meglévő IoT hub beállításait, miután az IoT Hub ablaktáblából létrehozott.

![Az IoT-központ beállításait bemutató képernyőkép](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Íme néhány az IoT hubhoz beállított tulajdonságok közül:

**Díjszabás és méretezés:** Ezzel a tulajdonsággal áttelepítheti egy másik szintre, vagy beállíthatja az IoT Hub-egységek számát. 

**Műveletek figyelése**: Kapcsolja be vagy ki a különböző figyelési kategóriákat, például az eszközről a felhőbe irányuló üzenetekhez vagy a felhőből az eszközre irányuló üzenetekhez kapcsolódó események naplózását.

**IP-szűrő:** Adja meg az Ip-címek azon tartományát, amelyet az IoT hub elfogad vagy elutasít.

**Tulajdonságok**: A máshol másolható és használható tulajdonságok listáját adja meg, például az erőforrás-azonosítót, az erőforráscsoportot, a helyet és így tovább.

### <a name="shared-access-policies"></a>Megosztott elérési házirendek

A megosztott hozzáférési házirendek listáját a Beállítások szakasz **Megosztott hozzáférési házirendjei** re kattintva is megtekintheti vagy **módosíthatja.** Ezek a házirendek határozzák meg az eszközök és szolgáltatások ioT Hubhoz való csatlakozáshoz szükséges engedélyeit. 

Kattintson a **Hozzáadás** gombra a **Megosztott hozzáférés házirendjének hozzáadása** panel megnyitásához.  Megadhatja az új házirend nevét és a házirendhez társítani kívánt engedélyeket, ahogy az az alábbi ábrán látható:

![Megosztott hozzáférési házirend hozzáadásáról készült képernyőkép](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* A **rendszerleíró adatbázis olvasási** és **beállításjegyzék-írási** házirendjei olvasási és írási hozzáférési jogokat biztosítanak az identitásbeállítás-beállításjegyzékhez. Ezeket az engedélyeket a háttérbeli felhőszolgáltatások használják az eszközidentitások kezelésére. Az írási lehetőség kiválasztása automatikusan az olvasási lehetőséget választja.

* A **Service connect** házirend engedélyt ad a szolgáltatás végpontjainak elérésére. Ezt az engedélyt a háttérbeli felhőszolgáltatások használják az eszközökről érkező üzenetek küldésére és fogadására, valamint az iker- és ikermodul-adatok frissítésére és olvasására.

* Az **Eszközcsatlakoztatási** szabályzat engedélyeket ad az IoT Hub eszközoldali végpontok használatával üzenetek küldésére és fogadására. Ezt az engedélyt az eszközök az IoT hubról érkező üzenetek küldésére és fogadására, az iker- és ikermodul-adatok frissítésére és olvasására, valamint a fájlfeltöltések végrehajtására használják.

A **Létrehozás gombra** kattintva adja hozzá ezt az újonnan létrehozott házirendet a meglévő listához.

Az adott engedélyek által biztosított hozzáférésről az [IoT Hub engedélyei című](./iot-hub-devguide-security.md#iot-hub-permissions)témakörben talál további információt.

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Üzenet-útválasztás IoT-központhoz

Kattintson az **Üzenetküldés csoport** **Üzenetküldés** gombjára az Üzenettovábbítás ablaktábla megtekintéséhez, ahol útvonalakat és egyéni végpontokat adhat meg a hubhoz. [Az üzenet-útválasztás](iot-hub-devguide-messages-d2c.md) lehetővé teszi az eszközökről a végpontokra történő adatküldés módjának kezelését. Az első lépés egy új útvonal hozzáadása. Ezután hozzáadhat egy meglévő végpontot az útvonalhoz, vagy létrehozhat egy újat a támogatott típusok közül, például a blob storage.Then you can add a existing endpoint to the route, or create a new one of the types supported, such as blob storage. 

![Üzenettovábbítás ablaktábla](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Útvonalak

Az Útvonalak az Üzenetútválasztás ablaktábla első lapja. Új útvonal hozzáadásához kattintson a +**Hozzáadás**gombra. A következő képernyő jelenik meg. 

![Új útvonal hozzáadásáról készült képernyőkép](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Nevezze el a központot. A névnek egyedinek kell lennie az adott csomópont útvonallistájában. 

A **Végpont**listában választhat egyet a legördülő listából, vagy hozzáadhat egy újat. Ebben a példában egy tárfiók és a tároló már elérhető. Végpontként való hozzáadásához kattintson a Végpont legördülő menü +**Hozzáadás** gombjára, és válassza a **Blob Storage lehetőséget.** A következő képernyőn látható, ahol a tárfiók és a tároló van megadva.

![Az útválasztási szabály tárolási végpontjának hozzáadását ábrázoló képernyőkép](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Kattintson **a Tároló kiválasztása** a tárfiók és a tároló kiválasztásához. Miután kiválasztotta ezeket a mezőket, visszatér a Végpont ablaktáblába. Használja a többi mező alapértelmezéseit, és a **Létrehozás programot** használja a tárfiók végpontjának létrehozásához és az útválasztási szabályokhoz való hozzáadásához.

Adatforrás **Data source**esetén válassza az Eszköztelemetriai üzenetek lehetőséget. 

Ezután adjon hozzá egy útválasztási lekérdezést. Ebben a példában az üzeneteket, `level` amelyek egy `critical` alkalmazás tulajdonság hívott egy egyenlő értékű a tárfiókba.

![Új útválasztási szabály mentését bemutató képernyőkép](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Az útválasztási szabály mentéséhez kattintson a **Mentés** gombra. Visszatér az Üzenetútválasztás ablaktáblához, és megjelenik az új útválasztási szabály.

### <a name="custom-endpoints"></a>Egyéni végpontok

Kattintson az **Egyéni végpontok** fülre. A már létrehozott egyéni végpontok jelennek meg. Itt új végpontokat adhat hozzá, vagy törölheti a meglévő végpontokat. 

> [!NOTE]
> Ha töröl egy útvonalat, az nem törli az útvonalhoz rendelt végpontokat. Végpont törléséhez kattintson az Egyéni végpontok fülre, jelölje ki a törölni kívánt végpontot, és kattintson a Törlés gombra.
>

Az egyéni végpontokról a [Reference - IoT hub végpontok.](iot-hub-devguide-endpoints.md)

Legfeljebb 10 egyéni végpontot definiálhat egy IoT-központhoz. 

Ha teljes példát szeretne látni az egyéni végpontok útválasztással való használatára, olvassa el az [Üzenetküldés az IoT Hubbal című témakört.](tutorial-routing.md)

## <a name="find-a-specific-iot-hub"></a>Adott IoT-központ megkeresése

Az alábbiakban két módon találhat meg egy adott IoT-központot az előfizetésében:

1. Ha ismeri azt az erőforráscsoportot, amelyhez az IoT-központ tartozik, kattintson az **Erőforráscsoportok**elemre, majd válassza ki az erőforráscsoportot a listából. Az erőforráscsoport képernyője a csoport összes erőforrását megjeleníti, beleértve az IoT-központokat is. Kattintson arra a hubra, amelyet keres.

2. Kattintson a **Minden erőforrás** elemre. A **Minden erőforrás** ablaktáblán van egy legördülő `All types`lista, amely alapértelmezés szerint a. Kattintson a legördülő lista, törölje . `Select all` Találd meg `IoT Hub` és nézd meg. Kattintson a legördülő listára a bezárásához, és a bejegyzések szűrve lesznek, és csak az IoT-központok jelennek meg.

## <a name="delete-the-iot-hub"></a>Az IoT-központ törlése

Egy iot hub törléséhez keresse meg a törölni kívánt IoT-központot, majd kattintson a **Törlés** gombra az IoT hub neve alatt.

## <a name="next-steps"></a>További lépések

Az Azure IoT Hub kezeléséről az alábbi hivatkozásokra kattintva olvashat bővebben:

* [Üzenettovábbítás az IoT Hubbal](tutorial-routing.md)
* [IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)