---
title: IoT hub-eseményforrás hozzáadása – Azure Time Series Insights | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá egy IoT hub-eseményforrást a Time Series Insights-környezethez.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: 3ea73e2ca20faea30294bc5d5e1788415095c39f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905368"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>IoT-központ eseményforrás hozzáadása a Time Series Insights-környezethez

Ez a cikk ismerteti, hogyan használhatja az Azure Portalon egy eseményforrás, amely beolvassa az adatokat az Azure IoT Hub az Azure Time Series Insights-környezetben.

> [!NOTE]
> A cikkben található utasítások az Azure Time Series Insights GA-ra és a Time Series Insights előzetes verziójú környezetekre egyaránt vonatkoznak.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Time Series Insights-környezetet.](time-series-insights-update-create-environment.md)
* Hozzon létre egy [IoT-központot az Azure Portal használatával.](../iot-hub/iot-hub-create-through-portal.md)
* Az IoT hub kell aktív üzenet események küldése.
* Hozzon létre egy dedikált fogyasztói csoportot az IoT-központban a Time Series Insights-környezetben, amelyből felhasználható. Minden Time Series Insights eseményforrásnak saját dedikált fogyasztói csoporttal kell rendelkeznie, amely nincs megosztva más fogyasztókkal. Ha több olvasó használja fel az ugyanabból a fogyasztói csoportból származó eseményeket, akkor valószínűleg minden olvasó hibát mutat. További részletekért olvassa el az [Azure IoT Hub fejlesztői útmutatóját.](../iot-hub/iot-hub-devguide.md)

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Fogyasztói csoport hozzáadása az IoT-központhoz

Az alkalmazások fogyasztói csoportok használatával adatokat kérnek le az Azure IoT Hubból. Az IoT-központból származó adatok megbízható olvasásához adjon meg egy dedikált fogyasztói csoportot, amelyet csak ez a Time Series Insights-környezet használ.

Új fogyasztói csoport hozzáadása az IoT-központhoz:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg az IoT-központot.

1. A **Beállítások csoportban**válassza **a Beépített végpontok**lehetőséget, majd válassza az **Események** végpontot.

   [![A beépített végpontok lapon válassza az Események gombot](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. A **Fogyasztói csoportok**csoportban adja meg a fogyasztói csoport egyedi nevét. Új eseményforrás létrehozásakor használja ugyanezt a nevet a Time Series Insights-környezetben.

1. Kattintson a **Mentés** gombra.

## <a name="add-a-new-event-source"></a>Új eseményforrás hozzáadása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali menüben válassza ki a **Minden erőforrás** elemet. Válassza ki az Azure Time Series Insights-környezetet.

1. A **Beállítások csoportban**válassza **az Eseményforrások**lehetőséget, majd a **Hozzáadás**lehetőséget.

   [![Válassza az Eseményforrások lehetőséget, majd kattintson a Hozzáadás gombra](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. Az **Új esemény** forrásablaktáblájában **az Esemény forrásnevéhez**adja meg a Time Series Insights környezetben egyedi nevet. Írja be például **az eseményfolyamot.**

1. A **Forrás csoportban**válassza az **IoT Hub lehetőséget.**

1. Válasszon egy értéket az **Importálás beállításhoz:**

   * Ha már rendelkezik egy IoT hub az egyik előfizetésében, válassza az **IoT Hub használata az elérhető előfizetések.** Ez a lehetőség a legegyszerűbb megközelítés.
   
     [![Beállítások megadása az Új eseményforrás ablaktáblán](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * Az alábbi táblázat ismerteti azokat a tulajdonságokat, amelyek szükségesek az **IoT Hub használata elérhető előfizetésekből** beállítás:

       [![Új eseményforrás-ablaktábla – Az Elérhető előfizetésekből az IoT Hub használata beállításban beállított tulajdonságok](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés | Az előfizetés, amelyhez a kívánt iot hub tartozik. |
       | IoT-központ neve | A kijelölt iot hub neve. |
       | IoT hub házirend neve | Válassza ki a megosztott hozzáférési szabályzatot. A megosztott hozzáférési szabályzat az IoT hub beállítások lapon található. Minden megosztott hozzáférési házirendrendelkezik névvel, a beállított engedélyekkel és hozzáférési kulcsokkal. Az eseményforrás megosztott hozzáférési szabályzatának **szolgáltatáscsatlakozási** engedélyekkel *kell* rendelkeznie. |
       | IoT hub házirendkulcs | A kulcs előre ki van töltve. |

    * Ha az IoT hub az előfizetésein kívül található, vagy ha speciális beállításokat szeretne választani, válassza **az IoT Hub beállításainak manuális megadása lehetőséget.**

      Az alábbi táblázat az **IoT Hub-beállítások manuális megadása**szükséges tulajdonságait ismerteti:

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés azonosítója | Az előfizetés, amelyhez a kívánt iot hub tartozik. |
       | Erőforráscsoport | Az erőforráscsoport neve, amelyben az IoT hub jött létre. |
       | IoT-központ neve | Az IoT hub neve. Az IoT hub létrehozásakor megadta az IoT hub nevét. |
       | IoT hub házirend neve | A megosztott hozzáférési szabályzat. A megosztott hozzáférési szabályzat az IoT hub beállítások lapon. Minden megosztott hozzáférési házirendrendelkezik névvel, a beállított engedélyekkel és hozzáférési kulcsokkal. Az eseményforrás megosztott hozzáférési szabályzatának **szolgáltatáscsatlakozási** engedélyekkel *kell* rendelkeznie. |
       | IoT hub házirendkulcs | A megosztott hozzáférési kulcs, amely az Azure Service Bus névtérhez való hozzáférés hitelesítésére szolgál. Itt adhatja meg az elsődleges vagy másodlagos kulcsot. |

    * Mindkét lehetőség a következő konfigurációs lehetőségeket osztja meg:

       | Tulajdonság | Leírás |
       | --- | --- |
       | IoT hub fogyasztói csoport | A fogyasztói csoport, amely beolvassa az eseményeket az IoT hubból. Javasoljuk, hogy egy dedikált fogyasztói csoportot használjon az eseményforráshoz. |
       | Eseményszerializációs formátum | Jelenleg a JSON az egyetlen elérhető szerializálási formátum. Az eseményüzeneteknek ebben a formátumban kell lenniük, különben nem olvashatók adatok. |
       | Időbélyeg-tulajdonság neve | Ennek az értéknek a meghatározásához meg kell értenie az IoT hubnak küldött üzenetadatok üzenetformátumát. Ez az érték az eseményidőbélyegként használni kívánt üzenetadatokadott eseménytulajdonságának **neve.** Az érték nem a di. Ha üresen hagyja, az **esemény enqueue idő** az esemény forrásában az esemény időbélyegként lesz használva. |


1. Adja hozzá a dedikált Time Series Insights fogyasztói csoport nevét, amely et az IoT hubhoz adott hozzá.

1. Kattintson a **Létrehozás** gombra.

1. Az eseményforrás létrehozása után a Time Series Insights automatikusan elindítja az adatok streamelését a környezetbe.

## <a name="next-steps"></a>További lépések

* Az adatok védelméhez határozza meg az [adatelérési házirendeket.](time-series-insights-data-access.md)

* [Események küldése](time-series-insights-send-events.md) az eseményforrásra.

* A [Time Series Insights felfedezőjében](https://insights.timeseries.azure.com)érheti el környezetét.
