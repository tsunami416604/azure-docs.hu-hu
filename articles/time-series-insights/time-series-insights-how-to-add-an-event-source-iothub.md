---
title: IoT hub-eseményforrás hozzáadása – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá egy IoT hub-eseményforrás a Time Series Insights-környezethez.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: e564bb6be22b3cee07fca2acd8a4d3ef91698111
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006891"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>IoT hub-esemény forrásának hozzáadása a Time Series Insights-környezethez

Ez a cikk azt ismerteti, hogyan használható a Azure Portal egy olyan eseményforrás hozzáadásához, amely adatokat olvas be az Azure IoT Hubból a Azure Time Series Insights környezetbe.

> [!NOTE]
> A cikkben szereplő utasítások a Azure Time Series Insights GA-ra és az előzetes verziójú környezetek Time Series Insightsra vonatkoznak.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Time Series Insights környezetet](time-series-insights-update-create-environment.md).
* Hozzon létre egy [IoT hubot a Azure Portal használatával](../iot-hub/iot-hub-create-through-portal.md).
* Az IoT hub-nak aktív üzenetküldési eseményekkel kell rendelkeznie.
* Hozzon létre egy dedikált fogyasztói csoportot az IoT hub-ban ahhoz a Time Series Insights-környezethez, amelyről a alkalmazást használni szeretné. Minden egyes Time Series Insights-eseményforrás rendelkeznie kell a saját dedikált fogyasztói csoportot, amely bármely más fogyasztók nincs megosztva. Ha több olvasók ugyanabban a fogyasztói csoportban lévő események felhasználásához, minden olvasók valószínűleg hibák. Részletekért tekintse meg az [Azure IoT hub fejlesztői útmutatóját](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Fogyasztói csoport hozzáadása az IoT hub-hoz

Az alkalmazások fogyasztói csoportokat használnak az adatok Azure-IoT Hubból való lekéréséhez. Ha megbízhatóan szeretné beolvasni az IoT hub adatait, adjon meg egy dedikált fogyasztói csoportot, amelyet csak ez a Time Series Insights-környezet használ.

Új fogyasztói csoport hozzáadása az IoT hub-hoz:

1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg az IoT hubot.

1. A **Beállítások**területen válassza a **beépített végpontok**lehetőséget, majd válassza ki az **események** végpontot.

   [![a beépített végpontok lapon kattintson az események gombra.](media/time-series-insights-how-to-add-an-event-source-iothub/1-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/1-iot-hub.png#lightbox)

1. A **fogyasztói csoportok**területen adjon meg egy egyedi nevet a fogyasztói csoport számára. Az új eseményforrás létrehozásakor használja ugyanazt a nevet a Time Series Insights-környezetben.

1. Kattintson a **Mentés** gombra.

## <a name="add-a-new-event-source"></a>Adjon hozzá egy új esemény forrása

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **Minden erőforrás** elemet. Válassza ki az Azure Time Series Insights-környezetet.

1. Alatt **környezeti topológia**válassza **eseményforrások**, majd válassza ki **Hozzáadás**.

   [![válassza az eseményforrás lehetőséget, majd kattintson a Hozzáadás gombra.](media/time-series-insights-how-to-add-an-event-source-iothub/2-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/2-add-event-source.png#lightbox)

1. Az **új eseményforrás** ablaktáblán az **eseményforrás neve**mezőben adjon meg egy egyedi nevet a Time Series Insights környezetben. Adja meg például az **Event-stream**értéket.

1. A **forrás**mezőben válassza a **IoT hub**lehetőséget.

1. Válassza ki az **importálási beállítás**értékét:

   * Ha az egyik előfizetésében már van egy IoT hub, válassza a **IoT hub használata az elérhető előfizetések közül**lehetőséget. Ez a lehetőség akkor a legegyszerűbb megközelítés.
   
     [![válassza a beállítások lehetőséget az új esemény forrása ablaktáblán](media/time-series-insights-how-to-add-an-event-source-iothub/3-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/3-select-an-import-option.png#lightbox)

    * A következő táblázat azokat a tulajdonságokat ismerteti, amelyek szükségesek az **elérhető előfizetések IoT Hubának használata az elérhető előfizetések közül** :

       [![új eseményforrás ablaktábla – az elérhető előfizetések IoT Hub használata az elérhető előfizetések között beállításhoz megadott tulajdonságok](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png#lightbox)

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetést | Az előfizetés, amelyhez a kívánt IOT hub tartozik. |
       | IoT hub neve | A kiválasztott IOT hub neve. |
       | IoT hub-házirend neve | Válassza ki a megosztott elérési házirendet. A megosztott hozzáférési szabályzatot az IoT hub beállításai lapon találja. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. Az eseményforrás megosztott hozzáférési házirendjének rendelkeznie *kell* a **szolgáltatás csatlakozási** engedélyeivel. |
       | IoT hub-házirend kulcsa | A kulcs előre van feltöltve. |

    * Ha az IoT hub kívül esik az előfizetéseken, vagy ha a speciális beállításokat szeretné választani, válassza a **IoT hub beállítások manuális**megadása lehetőséget.

      A következő táblázat ismerteti a **IoT hub beállítások manuális**megadása kötelező tulajdonságait:

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés azonosítója | Az előfizetés, amelyhez a kívánt IOT hub tartozik. |
       | Erőforráscsoport | Az erőforráscsoport neve, amelyben az IoT hub létrejött. |
       | IoT hub neve | Az IoT hub neve. Az IoT hub létrehozásakor meg kell adni az IoT hub nevét. |
       | IoT hub-házirend neve | A megosztott elérési házirendet. A megosztott hozzáférési szabályzatot az IoT hub beállításai lapon hozhatja létre. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. Az eseményforrás megosztott hozzáférési házirendjének rendelkeznie *kell* a **szolgáltatás csatlakozási** engedélyeivel. |
       | IoT hub-házirend kulcsa | A Azure Service Bus névtér hozzáférésének hitelesítéséhez használt megosztott elérési kulcs. Itt adhatja meg az elsődleges vagy másodlagos kulcsot. |

    * Mindkét beállítás a következő konfigurációs beállításokat használja:

       | Tulajdonság | Leírás |
       | --- | --- |
       | IoT hub fogyasztói csoport | Az IoT hub eseményeinek olvasására szolgáló fogyasztói csoport. Kifejezetten ajánljuk, hogy használjon dedikált fogyasztói csoportot az eseményforrást. |
       | Eseményszerializációs formátum | JSON-ban jelenleg csak akkor érhető el szerializálási formátum. Az esemény üzeneteinek ebben a formátumban kell lenniük, vagy egyetlen adattal sem lehet olvasni. |
       | Időbélyegző-tulajdonság neve | Az érték meghatározásához meg kell ismernie az IoT hub számára küldött üzenet formátumát. Ez az érték a **neve** az állapotüzenet-adatokat használja, mint az esemény időbélyegzője kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen hagyja, a **eseményt sorba idő** az a Eseménynapló timestamp forrás szolgál. |


1. Adja hozzá az IoT hub-hoz hozzáadott dedikált Time Series Insights fogyasztói csoport nevét.

1. Kattintson a **Létrehozás** gombra.

1. Az eseményforrás létrehozása után a Time Series Insights automatikusan elindítja az adatfolyamot a környezetbe.

## <a name="next-steps"></a>Következő lépések

* [Adathozzáférési házirendek meghatározása](time-series-insights-data-access.md) védi az adatait.

* [Események küldése](time-series-insights-send-events.md) esemény forrását.

* A környezet eléréséhez a [Time Series Insights explorer](https://insights.timeseries.azure.com).
