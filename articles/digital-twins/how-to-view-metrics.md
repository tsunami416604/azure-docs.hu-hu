---
title: Metrikák megtekintése Azure Monitor
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan tekintheti meg az Azure Digital Twins mérőszámait Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 7/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c6db571d64b0fd276519f15a3984848e80c4e18a
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387676"
---
# <a name="view-and-understand-azure-digital-twins-metrics"></a>Azure digitális Twins-metrikák megtekintése és megértése

Ezek a mérőszámok az Azure-előfizetésében található Azure Digital Twins-erőforrások állapotáról nyújtanak információt. Az Azure digitális Twins mérőszámai segítenek felmérni az Azure Digital Twins szolgáltatás általános állapotát és a hozzájuk kapcsolódó erőforrásokat. Ezek a felhasználók felé irányuló statisztikák segítenek megtekinteni, hogy mi történik az Azure-beli digitális Ikrekben, és segítsen a problémák kiváltó okának elemzésében anélkül, hogy kapcsolatba kellene lépnie az Azure ügyfélszolgálatával.

A metrikák alapértelmezés szerint engedélyezve vannak. Az Azure digitális Twins mérőszámait a [Azure Portal](https://portal.azure.com)tekintheti meg.

## <a name="how-to-view-azure-digital-twins-metrics"></a>Azure digitális Twins-mérőszámok megtekintése

1. Hozzon létre egy Azure Digital Twins-példányt. Az Azure Digital Twins-példányok beállításával kapcsolatos utasításokat itt találja: a [*példányok és a hitelesítés beállítása*](how-to-set-up-instance-scripted.md).

2. Keresse meg az Azure Digital Twins-példányát a [Azure Portalban](https:/portal.azure.com) (Nyissa meg a lapot, és írja be a nevét a portál keresési sávjába). 

    A példány menüjében válassza a **metrikák**lehetőséget.
   
    :::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="Képernyőfelvétel az Azure Digital Twins metrikáinak oldaláról":::

    Ez az oldal az Azure Digital Twins-példány metrikáit jeleníti meg. A metrikák egyéni nézeteit is létrehozhatja a listából megjeleníteni kívánt elemek kiválasztásával.
    
3. Kiválaszthatja, hogy a metrikák adatait egy Event Hubs-végpontba vagy egy Azure Storage-fiókba küldje el, ehhez kattintson a **diagnosztika beállítások** elemre a menüben, majd **adja hozzá a diagnosztikai beállítást**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="A diagnosztikai beállítások lapot és a hozzáadni kívánt gombot ábrázoló képernyőkép":::

    A folyamattal kapcsolatos további információkért lásd [*: Hibaelhárítás: a diagnosztika beállítása*](troubleshoot-diagnostics.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure digitális Twins-metrikák és azok használatának módjai

Az Azure Digital Twins számos mérőszámot biztosít, amelyek áttekintést nyújtanak a példány állapotáról és a hozzájuk kapcsolódó erőforrásokról. Több mérőszámból is egyesítheti az adatokat, hogy a példány állapotának nagyobb képét fesse. 

Az alábbi táblázatok ismertetik az egyes Azure Digital Twins-példányok által követett mérőszámokat, valamint azt, hogy az egyes mérőszámok hogyan kapcsolódnak a példányok általános állapotához.

#### <a name="api-request-metrics"></a>API-kérelmek metrikái

Az API-kérelmekkel rendelkező metrikák:

| Metrika | Metrika megjelenítendő neve | Egység | Összesítés típusa| Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API-kérések (előzetes verzió) | Darabszám | Összesen | A digitális ikrek olvasási, írási, törlési és lekérdezési műveleteire vonatkozó API-kérések száma. |  Hitelesítés </br>Művelet </br>Protokoll </br>Állapotkód </br>Állapotkód osztálya </br>Állapot szövege |
| ApiRequestsLatency | API-kérelmek késése (előzetes verzió) | Ezredmásodpercben | Átlag | Az API-kérelmek válaszideje. Ez arra az időre vonatkozik, amikor a kérést az Azure Digital Twins fogadja, amíg a szolgáltatás nem küld sikeres/sikertelen eredményt a digitális ikrek olvasási, írási, törlési és lekérdezési műveleteihez. | Hitelesítés </br>Művelet </br>Protokoll |
| ApiRequestsFailureRate | API-kérelmek meghibásodási aránya (előzetes verzió) | Százalék | Átlag | A szolgáltatás által a példányhoz kapott API-kérelmek százalékos aránya (500) a digitális ikrek olvasási, írási, törlési és lekérdezési műveleteinek belső hibája (). | Hitelesítés </br>Művelet </br>Protokoll </br>Állapotkód </br>Állapotkód osztálya </br>Állapot szövege

#### <a name="routing-metrics"></a>Útválasztási metrikák

Az útválasztással elvégezhető mérőszámok:

| Metrika | Metrika megjelenítendő neve | Egység | Összesítés típusa| Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| Útválasztás | Útválasztás (előzetes verzió) | Darabszám | Összesen | Az Azure-szolgáltatásokhoz (például Event hub, Service Bus vagy Event Grid) továbbított üzenetek száma. | Művelet </br>Eredmény |
| RoutingLatency | Útválasztási késés (előzetes verzió) | Ezredmásodpercben | Átlag | Az Azure Digital Twins-ból átirányított esemény között eltelt idő a végponti Azure-szolgáltatásba (például Event hub, Service Bus vagy Event Grid) való közzétételkor. | Művelet </br>Eredmény |
| RoutingFailureRate | Útválasztási hibák aránya (előzetes verzió) | Százalék | Átlag | Az olyan események százalékos aránya, amelyek az Azure digitális Twins-ból egy Endpoint Azure-szolgáltatásba, például az Event hub-ba, a Service Busba vagy a Event Gridra irányítják a hibát. | Művelet </br>Eredmény |

#### <a name="billing-metrics"></a>Számlázási mérőszámok

A számlázással elvégezhető mérőszámok:

>[!NOTE]
> Az előzetes verzióban **a számlázás nulla**. Habár ezek a metrikák továbbra is megjelennek a választható listán, a rendszer nem alkalmazza az előzetes verzióban, és mindaddig nulla marad, amíg a szolgáltatás az előzetes verziónál nem halad.

| Metrika | Metrika megjelenítendő neve | Egység | Összesítés típusa| Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Számlázási API-műveletek (előzetes verzió) | Darabszám | Összesen | Az Azure Digital Twins szolgáltatásban végrehajtott API-kérelmek számának számlázási mérőszáma. | `MeterId` |
| BillingQueryUnits | Számlázási lekérdezési egységek (előzetes verzió) | Darabszám | Összesen | A lekérdezési egységek száma, a szolgáltatások erőforrás-felhasználásának belsőleg számított mértéke, a lekérdezések végrehajtásához felhasználva. A lekérdezési egységek méréséhez rendelkezésre áll egy segítő API is: [QueryChargeHelper Class](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | `MeterId` |
| BillingMessagesProcessed | Feldolgozott számlázási üzenetek (előzetes verzió) | Darabszám | Összesen | Számlázási metrika az Azure digitális Twins-ból külső végpontokra küldött üzenetek számának megadásához. | `MeterId` |

## <a name="dimensions"></a>Dimenziók

A dimenziók segítenek azonosítani a metrikák további részleteit. Az útválasztási metrikák némelyike végponti információkat biztosít. Az alábbi táblázat felsorolja a méretek lehetséges értékeit.

| Dimenzió | Értékek |
| --- | --- |
| Hitelesítés | OAuth |
| Művelet (API-kérelmek esetén) | Microsoft. DigitalTwins/DigitalTwins/delete</br>Microsoft. DigitalTwins/DigitalTwins/Write</br>Microsoft. DigitalTwins/DigitalTwins/READ </br>Microsoft. DigitalTwins/eventroutes/READ </br>Microsoft. DigitalTwins/eventroutes/Write </br>Microsoft. DigitalTwins/eventroutes/delete </br>Microsoft. DigitalTwins/modellek/olvasás </br>Microsoft. DigitalTwins/modellek/írás </br>Microsoft. DigitalTwins/models/delete </br>Microsoft. DigitalTwins/lekérdezés/művelet |
Művelet (útválasztáshoz) | Event Grid </br>Eseményközpont </br>Service Bus |
| Protokoll | HTTPS |
| Eredmény | Success </br>Hiba |
| Állapotkód | 200, 404, 500 és így tovább. |
| Állapotkód osztálya | 2xx, 4xx, 5xx stb. |
| Állapot szövege | Belső kiszolgálóhiba, nem található, és így tovább. |

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure Digital Twins rögzített metrikáinak kezelésével kapcsolatban, tekintse meg a [*Hibaelhárítás: diagnosztika beállítása*](troubleshoot-diagnostics.md)című témakört.

Vagy most, hogy áttekintette az Azure Digital Twins mérőszámait, az alábbi hivatkozásokat követve további információkat tudhat meg az Azure Digital Twins kezeléséről:

* [*Útmutató: az Azure Digital Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md)
* [*Útmutató: egyéni modellek kezelése*](how-to-manage-model.md)
* [*Útmutató: digitális ikrek kezelése*](how-to-manage-twin.md)
* [*Útmutató: a Twin gráf kezelése kapcsolatok használatával*](how-to-manage-graph.md)
* [*Útmutató: végpontok és útvonalak kezelése*](how-to-manage-routes.md)