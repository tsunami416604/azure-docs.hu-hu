---
title: Integráció más szolgáltatásokkal
titleSuffix: Azure Digital Twins
description: A bejövő és a kimenő forgalomra vonatkozó követelmények megismerése az Azure digitális Twins üzembe helyezése során.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: e266e9f8738a0c7d06c1f3b3f7213ad807e179fc
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737172"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Az Azure Digital Twins integrálása más szolgáltatásokkal

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Az Azure digitális Twins jellemzően más szolgáltatásokkal együtt használható. Az [**Event Routes**](concepts-route-events.md)használatával az Azure Digital Twins olyan felsőbb rétegbeli szolgáltatásokból származó adatokhoz jut, mint például a [IoT hub](../iot-hub/about-iot-hub.md), amely a telemetria és az értesítések továbbítására szolgál. Az Azure Digital Twins a tárolás, a munkafolyamat-integráció, az elemzés és más felhasználás céljából is továbbíthatja az adattovábbítást a későbbi szolgáltatásokhoz. 

## <a name="data-ingress"></a>Bejövő adatforgalom

Az Azure Digital Twins IoT Hubból származó adatokkal is elvégezhető. Így telemetria gyűjthet a környezetében található fizikai eszközökről, és feldolgozhatja ezeket az adatokat a felhőben található Azure digitális Twins gráf használatával.

Az Azure digitális Twins nem rendelkezik beépített IoT Hub. Használhat egy meglévő IoT Hub, amelyet jelenleg éles környezetben, vagy üzembe helyezhet egy újat. Ez teljes hozzáférést biztosít a IoT Hub összes eszköz-felügyeleti képességeihez.

Ha IoT Hubről szeretne adatot bevenni az Azure digitális Twinsba, használjon [Azure-függvényt](../azure-functions/functions-overview.md). További információ erről a mintázatról [: telemetria beolvasása a IoT hubból](how-to-ingest-iot-hub-data.md), vagy próbálja ki magát az [Azure Digital Twins oktatóanyagában: teljes körű megoldás összekötése](tutorial-end-to-end.md).

## <a name="data-egress-services"></a>Adatforgalom-szolgáltatások

Az Azure Digital Twins képes az adatküldés a csatlakoztatott **végpontokra**. A támogatott végpontok a következőket tehetik:
* [Eseményközpont](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

A végpontokat a felügyeleti API-k vagy a Azure Portal használatával csatlakoztatják az Azure Digital Twins szolgáltatáshoz. További információ a végpontok Azure digitális Ikrekhez való csatlakoztatásáról [: végpontok és útvonalak kezelése](how-to-manage-routes.md).

Számos más szolgáltatás is van, ahol érdemes lehet végső soron az adatait, például az [Azure Storage](../storage/common/storage-introduction.md) -t vagy a [Time Series Insightst](../time-series-insights/time-series-insights-update-overview.md)irányítani. Ahhoz, hogy az Ön adatait az alábbi szolgáltatásokhoz küldje, csatolja a rendeltetési szolgáltatást egy végponthoz.

Ha például az [Azure Maps](../azure-maps/about-azure-maps.md) is használja, és szeretné összekapcsolni a helyet az Azure Digital Twins [Twin gráfmal](concepts-twins-graph.md), a Azure functions a Event Grid használatával a telepítésben lévő összes szolgáltatás közötti kommunikációt is létrehozhat.

## <a name="next-steps"></a>További lépések

További információ a végpontokról és az útválasztási eseményekről a külső szolgáltatásokra:
* [Fogalmak: az Azure Digital Twins eseményeinek továbbítása](concepts-route-events.md)

Ismerje meg, hogyan állíthatja be az Azure digitális Twins-t az adatok IoT Hubból való betöltéséhez:
* [Útmutató: telemetria beolvasása IoT Hubból](how-to-ingest-iot-hub-data.md)
