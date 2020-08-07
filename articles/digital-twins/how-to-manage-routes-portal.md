---
title: Végpontok és útvonalak kezelése (portál)
titleSuffix: Azure Digital Twins
description: 'Lásd: a végpontok és az események útvonalának beállítása és kezelése az Azure digitális Twins-adataihoz a Azure Portal használatával.'
author: v-lakast
ms.author: v-lakast
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7d563c7706529c6f3e280f7d138c0d6ba0dfc849
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902197"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Végpontok és útvonalak kezelése az Azure Digital Twins-ban (portál)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Az Azure Digital Twins-ban átirányíthatja az [eseményekre vonatkozó értesítéseket](how-to-interpret-event-data.md) az alsóbb rétegbeli szolgáltatásokhoz vagy a csatlakoztatott számítási erőforrásokhoz. Ezt úgy teheti meg, hogy először beállítja az eseményeket fogadó **végpontokat** . Ezután létrehozhat olyan [**esemény-útvonalakat**](concepts-route-events.md) , amelyekkel megadhatja, hogy az Azure Digital Twins mely eseményeit adja meg a végpontok.

Ez a cikk végigvezeti a végpontok és útvonalak létrehozásának folyamatán a [Azure Portal](https://portal.azure.com)használatával.

A végpontokat és útvonalakat a [EventRoutes API](how-to-use-apis-sdks.md)-kkal, a [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)-val vagy az [Azure digitális Twins parancssori](how-to-use-cli.md)felületével is kezelheti. A jelen cikk a portál helyett ezeket a mechanizmusokat használó verziója [*: útmutató: végpontok és útvonalak kezelése (API-k és parancssori felület)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Előfeltételek

* Szüksége lesz egy **Azure-fiókra** ( [itt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)akár ingyenesen is beállíthatja)
* Azure-beli **digitális Twins-példányra** lesz szüksége az Azure-előfizetésében. Ha még nem rendelkezik példányokkal, létrehozhat egyet a következő útmutató lépéseit követve [*: példány és hitelesítés beállítása*](how-to-set-up-instance-scripted.md). A telepítésből származó alábbi értékekkel a cikk későbbi részében használhatja a következő értékeket:
    - Példány neve
    - Erőforráscsoport

Ezeket a részleteket a [Azure Portal](https://portal.azure.com) a példány beállítása után találhatja meg. Jelentkezzen be a portálra, és keresse meg a példány nevét a portál keresési sávján.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Képernyőkép: Azure Portal keresési sáv.":::

Válassza ki a példányát az eredmények közül, és tekintse meg a példány részletek lapját:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Képernyőkép a ADT-példány részleteiről." border="false":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Végpont létrehozása az Azure Digital Twins számára

Ezek a példányok által létrehozható támogatott típusú végpontok:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Szolgáltatásbusz](../service-bus-messaging/service-bus-messaging-overview.md)

További információ a különböző végpontok típusairól: [*választás az Azure Messaging Services között*](../event-grid/compare-messaging-services.md).

Ahhoz, hogy egy végpontot az Azure digitális Ikrekhöz lehessen kapcsolni, a végponthoz használt Event Grid-témakör, az Event hub vagy a Service Bus már léteznie kell. 

### <a name="create-an-event-grid-endpoint"></a>Event Grid végpont létrehozása

**Előfeltétel**: hozzon létre egy Event Grid-témakört a Event Grid *Egyéni események* rövid útmutatójának [ *Egyéni témakör létrehozása* című szakaszában](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) ismertetett lépéseket követve.

Miután létrehozta a témakört, összekapcsolhatja azt az Azure Digital ikrek szolgáltatásban az Azure Digital Twins példányának oldaláról a [Azure Portalban](https://portal.azure.com) (a példány a nevét a portál keresési sávjába írja be).

A példány menüben válassza a _végpontok_lehetőséget. Ezután az alábbi *végpontok* lapról válassza a *+ végpont létrehozása*lehetőséget. 

A megnyíló *végpont létrehozása* oldalon létrehozhat egy _Event Grid_ típusú végpontot a megfelelő választógomb kiválasztásával. Fejezze be a további részleteket: adjon meg egy nevet a végpontnak a _név_ mezőben, válassza ki az _előfizetését_ a legördülő listából, és válassza ki az előre létrehozott _Event Grid témakört_ a harmadik legördülő menüből.

Ezután hozza létre a végpontot a _Save (Mentés_) parancs megnyomásával.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Képernyőkép: Event Grid típusú végpont létrehozása.":::

Ha ellenőrizni szeretné, hogy a végpont sikeresen létrejött-e, tekintse meg az értesítés ikont a felső Azure Portal sávban: 

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Képernyőkép az értesítésről a végpont létrehozásának ellenőrzéséhez." border="false":::

Megtekintheti az Azure Digital Twins-példány *végpontok* lapján létrehozott végpontot is.

Ha a végpont létrehozása sikertelen, figyelje meg a hibaüzenetet, és néhány perc múlva próbálkozzon újra.

Az Event Grid témakör az Azure Digital Twins-beli végpontként érhető el, a _név mezőben megadott_ név alatt. Ezt a nevet általában egy **esemény-útvonal**céljaként fogja használni, amelyet később fog létrehozni [ebben a cikkben](#event-routes).

### <a name="create-an-event-hubs-endpoint"></a>Event Hubs végpont létrehozása

**Előfeltételek**: 
* Szüksége lesz egy _Event Hubs névtérre_ és egy _Event hub_-ra. Hozzon létre mindkettőt a Event Hubs [*Event hub*](../event-hubs/event-hubs-create.md) -útmutató létrehozása című témakör lépéseit követve.
* Szüksége lesz egy _engedélyezési szabályra_. Ennek létrehozásához tekintse meg a Event Hubs a [*hozzáférés engedélyezése Event Hubs erőforrásokhoz a megosztott hozzáférési aláírások használatával*](../event-hubs/authorize-access-shared-access-signature.md) című cikket.

Nyissa meg az Azure Digital Twins-példány részletek lapját a [Azure Portalban](https://portal.azure.com) (a nevét megkeresheti a portál keresési sávjában).

A példány menüben válassza a _végpontok_lehetőséget. Ezután az alábbi *végpontok* lapról válassza a *+ végpont létrehozása*lehetőséget. 

A megnyíló *végpont létrehozása* oldalon létrehozhat egy _Event hub_ típusú végpontot a megfelelő választógomb kiválasztásával. Adja meg a végpont nevét a _név_ mezőben. Ezután válassza ki az _előfizetését_, és az előre létrehozott _Event hub-névteret_, az _Event hub_-t és az _engedélyezési szabályt_ a megfelelő legördülő listából.

Ezután hozza létre a végpontot a _Save (Mentés_) parancs megnyomásával.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub.png" alt-text="Képernyőkép: Event Hubs típusú végpont létrehozása.":::

Ha ellenőrizni szeretné, hogy a végpont sikeresen létrejött-e, tekintse meg az értesítés ikont a felső Azure Portal sávban. 

Ha a végpont létrehozása sikertelen, figyelje meg a hibaüzenetet, és néhány perc múlva próbálkozzon újra.

Az Event hub mostantól végpontként érhető el az Azure digitális Twins-ban, a _név_ mezőben megadott név alatt. Ezt a nevet általában egy **esemény-útvonal**céljaként fogja használni, amelyet később fog létrehozni [ebben a cikkben](#event-routes).

### <a name="create-a-service-bus-endpoint"></a>Service Bus végpont létrehozása

**Előfeltételek**: 
* Szüksége lesz egy _Service Bus névtérre_ és egy _Service Bus témakörre_. Hozza létre mindkét műveletet a Service Bus [*témakörök és előfizetések létrehozása*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) című rövid útmutató lépéseivel. Nem kell befejeznie a [*létrehozási előfizetéseket a témakör*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md#create-subscriptions-to-the-topic) szakaszhoz.
* Szüksége lesz egy _engedélyezési szabályra_. Ennek létrehozásához tekintse meg a Service Bus [*hitelesítési és engedélyezési*](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) cikket.

Nyissa meg az Azure Digital Twins-példány részletek lapját a [Azure Portalban](https://portal.azure.com) (a nevét megkeresheti a portál keresési sávjában).

A példány menüben válassza a _végpontok_lehetőséget. Ezután az alábbi *végpontok* lapról válassza a *+ végpont létrehozása*lehetőséget. 

A megnyíló *végpont létrehozása* oldalon létrehozhat egy _Service Bus_ típusú végpontot a megfelelő választógomb kiválasztásával. Adja meg a végpont nevét a _név_ mezőben. Ezután válassza ki az _előfizetését_, és az előre létrehozott _Service Bus névteret_, _Service Bus témakört_és az _engedélyezési szabályt_ a megfelelő legördülő listából.

Ezután hozza létre a végpontot a _Save (Mentés_) parancs megnyomásával.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-service-bus.png" alt-text="Képernyőkép: Service Bus típusú végpont létrehozása.":::

Ha ellenőrizni szeretné, hogy a végpont sikeresen létrejött-e, tekintse meg az értesítés ikont a felső Azure Portal sávban. 

Ha a végpont létrehozása sikertelen, figyelje meg a hibaüzenetet, és néhány perc múlva próbálkozzon újra.

A Service Bus témakör az Azure digitális Twins-beli végpontként érhető el, a _név mezőben megadott_ név alatt. Ezt a nevet általában egy **esemény-útvonal**céljaként fogja használni, amelyet később fog létrehozni [ebben a cikkben](#event-routes).

## <a name="event-routes"></a>Eseményútvonalak

Az Azure digitális Twins-ból egy végpontba küldött adatok tényleges elküldéséhez meg kell határoznia egy **esemény-útvonalat**. Ezek az útvonalak lehetővé teszik a fejlesztők számára, hogy az események áramlását a rendszeren és az alárendelt szolgáltatásokon keresztül továbbítsák. További információk az esemény-útvonalakról a [*fogalmakban: az Azure Digital Twins eseményeinek továbbítása*](concepts-route-events.md).

**Előfeltétel**: a cikk korábbi részében leírtak szerint hozzon létre végpontokat, mielőtt továbblép az útvonal létrehozásához. Ha befejezte a végpontok beállítását, folytassa az esemény-útvonal létrehozásával.

>[!NOTE]
>Ha nemrég telepítette a végpontokat, ellenőrizze, hogy készen állnak-e a telepítésre, **mielőtt** új esemény-útvonalra próbálja használni őket. Ha nem tudja beállítani az útvonalat, mert a végpontok nem állnak készen, várjon néhány percet, és próbálkozzon újra.

### <a name="create-an-event-route"></a>Esemény útvonalának létrehozása 

Az események útvonalának definíciója az alábbi elemeket tartalmazza:
* A használni kívánt útvonal neve
* A használni kívánt végpont neve
* Egy szűrő, amely meghatározza, hogy mely eseményeket küldi a rendszer a végpontnak.
    - Ha le szeretné tiltani az útvonalat, hogy ne küldjön eseményeket, használja a`false`
    - Ha olyan útvonalat szeretne engedélyezni, amely nem rendelkezik adott szűréssel, használja a szűrő értékét`true`
    - A más típusú szűrők részleteiről az alábbi, az [*Események szűrése*](#filter-events) című szakaszban olvashat.

Egyetlen útvonalon több értesítés és eseménytípus is kijelölhető.

Az esemény-útvonal létrehozásához nyissa meg az Azure Digital Twins-példány részletek lapját a [Azure Portalban](https://portal.azure.com) (a példány megkereséséhez írja be a nevét a portál keresési sávjába).

A példány menüben válassza az _esemény útvonalak_elemet. Ezután az alábbi *esemény-útvonalak* lapon válassza az *+ esemény-útvonal létrehozása*lehetőséget. 

A megnyíló *esemény-útvonal létrehozása* oldalon válassza a minimum:
* A _név_ mezőben adja meg az útvonal nevét.
* Az útvonal létrehozásához használni kívánt _végpont_ 

Ahhoz, hogy az útvonal engedélyezve legyen, legalább **egy esemény-útválasztási szűrőt is hozzá** kell adnia `true` . (Az alapértelmezett érték kihagyása `false` esetén az útvonalat hozza létre, de a rendszer nem küldi el az eseményeket.) Ehhez állítsa be a kapcsolót a _speciális szerkesztő_ számára az engedélyezéshez, majd írja `true` be a *szűrőt a szűrő* mezőbe.

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Képernyőkép a példányhoz tartozó esemény-útvonal létrehozásáról." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

Ha elkészült, nyomja meg a _Save (Mentés_ ) gombot az esemény útvonalának létrehozásához.

### <a name="filter-events"></a>Események szűrése

A fentiekben leírtak szerint az útvonalak **szűrő** mezővel rendelkeznek. Ha az útvonalon található szűrő értéke, a rendszer `false` nem küld eseményeket a végpontnak. 

A minimális szűrő engedélyezése után a `true` végpontok számos eseményt kapnak az Azure Digital ikrekből:
* Az Azure Digital Twins szolgáltatás API-ját használó [digitális Twins](concepts-twins-graph.md) telemetria
* A Twin Property változási értesítései az Azure Digital Twins-példányon található bármelyik Twin tulajdonság változásakor
* Életciklussal kapcsolatos események, az ikrek vagy kapcsolatok létrehozásakor vagy törlésekor
* Modell-változási események, az Azure digitális Twins-példányban konfigurált [modellek](concepts-models.md) hozzáadása vagy törlése

Megadhatja az elküldött események típusát egy konkrétabb szűrő definiálásával.

Ha esemény-útvonal létrehozása közben szeretne hozzáadni egy eseményt, az esemény útvonalának *létrehozása* lap _esemény útvonalának szűrése_ szakaszát használhatja. 

Kiválaszthat néhány alapszintű általános szűrési lehetőséget, vagy a speciális szűrési beállítások használatával írhat saját egyéni szűrőket is.

#### <a name="use-the-basic-filters"></a>Az egyszerű szűrők használata

Az alapszintű szűrők használatához bontsa ki az _eseménytípus_ lehetőséget, majd jelölje be a végpontnak elküldeni kívánt eseményekhez tartozó jelölőnégyzeteket. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Képernyőkép egy alapszintű szűrővel rendelkező esemény-útvonal létrehozásáról. Az események jelölőnégyzetének bejelölése.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Ezzel automatikusan feltölti a szűrő szövegmezőt a kiválasztott szűrő szövegével:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Képernyőkép egy alapszintű szűrővel rendelkező esemény-útvonal létrehozásáról. Az automatikusan feltöltött szűrő szövegének megjelenítése az események kiválasztása után.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>A speciális szűrők használata

Másik lehetőségként használhatja a speciális szűrő lehetőséget is a saját egyéni szűrők írására.

Ha speciális szűrési beállításokkal szeretne eseményvezérelt útvonalat létrehozni, állítsa be a _speciális szerkesztő_ kapcsolóját, hogy engedélyezze azt. Ezután megírhatja a saját események szűrőit a *szűrő* mezőbe:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Képernyőkép egy speciális szűrővel rendelkező esemény-útvonal létrehozásáról.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Itt láthatók a támogatott útválasztási szűrők. A *szűrési szöveg sémája* oszlopban szereplő részletek a szűrő mezőbe beírható szöveg.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Következő lépések

További információ a fogadott üzenetek különböző típusairól:
* [*Útmutató: az események értelmezése*](how-to-interpret-event-data.md)
