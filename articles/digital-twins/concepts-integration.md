---
title: Integráció más szolgáltatásokkal
titleSuffix: Azure Digital Twins
description: A bejövő és a kimenő forgalomra vonatkozó követelmények megismerése az Azure digitális Twins üzembe helyezése során.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 7e360c158c7887109684d13f774cbbda1813373e
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91729134"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Az Azure Digital Twins integrálása más szolgáltatásokkal

Az Azure digitális Twins jellemzően más szolgáltatásokkal együtt használható. Az [**Event Routes**](concepts-route-events.md)használatával az Azure Digital Twins olyan felsőbb rétegbeli szolgáltatásokból származó adatokhoz jut, mint a [IoT Hub](../iot-hub/about-iot-hub.md) vagy a [Logic apps](../logic-apps/logic-apps-overview.md), amelyek a telemetria és az értesítések kézbesítésére szolgálnak. 

Az Azure Digital Twins az olyan alsóbb rétegbeli szolgáltatásokhoz is irányíthatja az adattovábbítást, mint például a [Azure Maps](../azure-maps/about-azure-maps.md) és a [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md), a tárolás, a munkafolyamat-integráció, az elemzés és egyebek terén. 

## <a name="data-ingress"></a>Bejövő adatforgalom

Az Azure Digital Twins bármely szolgáltatás adataival és eseményeivel is elvégezhető –[IoT hub](../iot-hub/about-iot-hub.md), [Logic apps](../logic-apps/logic-apps-overview.md), a saját egyéni szolgáltatása stb.). Így telemetria gyűjthet a környezetében található fizikai eszközökről, és feldolgozhatja ezeket az adatokat a felhőben található Azure digitális Twins gráf használatával.

Az Azure digitális Twins nem rendelkezik beépített IoT Hub. Használhat egy meglévő IoT Hub, amelyet jelenleg éles környezetben, vagy üzembe helyezhet egy újat. Ez teljes hozzáférést biztosít a IoT Hub összes eszköz-felügyeleti képességeihez.

Ha bármilyen forrásból szeretne adatot befogadni az Azure digitális Twinsba, használjon [**Azure-függvényt**](../azure-functions/functions-overview.md). További információ erről a mintázatról [*: telemetria beolvasása a IoT hubból*](how-to-ingest-iot-hub-data.md), vagy próbálja ki magát az Azure Digital Twins [*oktatóanyagában: teljes körű megoldás összekötése*](tutorial-end-to-end.md). 

Azt is megtudhatja, hogyan csatlakoztathatók az Azure digitális Twins egy Logic Apps triggerhez a következő [*útmutatóban: integráció a Logic apps*](how-to-integrate-logic-apps.md)használatával.

## <a name="data-egress-services"></a>Adatforgalom-szolgáltatások

Az Azure Digital Twins képes az adatküldés a csatlakoztatott **végpontokra**. A támogatott végpontok a következőket tehetik:
* [Event Hub](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Szolgáltatásbusz](../service-bus-messaging/service-bus-messaging-overview.md)

A végpontokat a felügyeleti API-k vagy a Azure Portal használatával csatlakoztatják az Azure Digital Twins szolgáltatáshoz. További információ a végpontok Azure digitális Ikrekhez való csatlakoztatásáról [*: végpontok és útvonalak kezelése*](how-to-manage-routes-apis-cli.md).

Számos más szolgáltatás is van, ahol érdemes lehet végső soron az adatait, például az [Azure Storage](../storage/common/storage-introduction.md), a [Azure Maps](../azure-maps/about-azure-maps.md)vagy a [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md). Ahhoz, hogy az Ön adatait az alábbi szolgáltatásokhoz küldje, csatolja a rendeltetési szolgáltatást egy végponthoz.

Ha például az Azure Maps is használja, és szeretné összekapcsolni a helyet az Azure Digital Twins [Twin gráfmal](concepts-twins-graph.md), a Azure Functions a Event Grid használatával a telepítésben lévő összes szolgáltatás közötti kommunikációt is létrehozhat. További információ a következő [ *útmutatóban: az Azure Digital Twins használata Azure Maps beltéri Térkép frissítéséhez*](how-to-integrate-maps.md)

Azt is megtudhatja, hogyan irányíthatja az adatútvonalat hasonló módon a Time Series Insights, a [*How-to: Integration with Time Series Insights*](how-to-integrate-time-series-insights.md)használatával.

## <a name="next-steps"></a>További lépések

További információ a végpontokról és az útválasztási eseményekről a külső szolgáltatásokra:
* [*Fogalmak: az Azure Digital Twins eseményeinek továbbítása*](concepts-route-events.md)

Ismerje meg, hogyan állíthatja be az Azure digitális Twins-t az adatok IoT Hubból való betöltéséhez:
* [*Útmutató: telemetria beolvasása IoT Hubból*](how-to-ingest-iot-hub-data.md)
