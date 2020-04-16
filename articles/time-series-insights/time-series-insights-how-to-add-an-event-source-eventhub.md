---
title: Event Hubs eseményforrás hozzáadása – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg, hogyan adhat hozzá egy Azure Event Hubs-eseményforrást a Time Series Insights-környezethez.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 021ac5fccf4d694895ab9941bd46dd2388f49af9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407463"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Eseményközpont-eseményforrás hozzáadása a Time Series Insights-környezethez

Ez a cikk ismerteti, hogyan használhatja az Azure Portalon egy eseményforrás, amely beolvassa az adatokat az Azure Event Hubs az Azure Time Series Insights környezetben.

> [!NOTE]
> A cikkben ismertetett lépések a Time Series Insights GA és a Time Series Insights előzetes verzióira egyaránt vonatkoznak.

## <a name="prerequisites"></a>Előfeltételek

- Hozzon létre egy Time Series Insights-környezetet az [Azure Time Series Insights-környezet létrehozása című, az Azure Time Series Insights-környezetben leírtak szerint.](./time-series-insights-update-create-environment.md)
- Eseményközpont létrehozása. Olvassa [el Az Eseményközpontok névtere és egy eseményközpont létrehozása az Azure Portal használatával című olvasni](../event-hubs/event-hubs-create.md)című olvasni.
- Az eseményközpontnak aktív üzeneteseményeket kell küldenie. Megtudhatja, hogy [miként küldhet eseményeket az Azure Event Hubs-ba a .](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)
- Hozzon létre egy dedikált fogyasztói csoportot az eseményközpontban, amelyből a Time Series Insights-környezet felhasználható. Minden Time Series Insights eseményforrásnak saját dedikált fogyasztói csoporttal kell rendelkeznie, amely nincs megosztva más fogyasztókkal. Ha több olvasó használja fel az ugyanabból a fogyasztói csoportból származó eseményeket, akkor valószínűleg minden olvasó hibát mutat. Eseményközpontonként legfeljebb 20 fogyasztói csoport lehet. További részletekért olvassa el az [Event Hubs programozási útmutatóját.](../event-hubs/event-hubs-programming-guide.md)

### <a name="add-a-consumer-group-to-your-event-hub"></a>Fogyasztói csoport hozzáadása az eseményközponthoz

Az alkalmazások fogyasztói csoportok használatával adatokat kérnek le az Azure Event Hubs-ból. Az eseményközpontból származó adatok megbízható olvasásához biztosítson egy dedikált fogyasztói csoportot, amelyet csak ez a Time Series Insights-környezet használ.

Új fogyasztói csoport hozzáadása az eseményközpontban:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg az eseményközpont-példányt az eseményközpont névterének **Áttekintő** ablaktábláján. Válassza **az Entitások > az Eseményközpontokat,** vagy keresse meg a **példányt**a Név csoportban.

    [![Az eseményközpont névtere megnyitása](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Az eseményközpont-példányban válassza **az Entitások > fogyasztói csoportok lehetőséget.** Ezután válassza **a + Fogyasztói csoport** lehetőséget egy új fogyasztói csoport hozzáadásához. 

   [![Eseményközpont – Fogyasztói csoport hozzáadása](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Ellenkező esetben jelöljön ki egy meglévő fogyasztói csoportot, és ugorjon a következő szakaszra.

1. A **Fogyasztói csoportok** lapon adjon meg egy új egyedi értéket a **Név mezőbe.**  Használja ugyanazt a nevet, amikor új eseményforrást hoz létre a Time Series Insights környezetben.

1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-new-event-source"></a>Új eseményforrás hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő Time Series Insights-környezetet. A bal oldali menüben válassza a **Minden erőforrás**lehetőséget, majd válassza ki a Time Series Insights környezetet.

1. Válassza **az Eseményforrások**lehetőséget, majd a **Hozzáadás**lehetőséget.

   [![Az Eseményforrások csoportban válassza a Hozzáadás gombot.](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Adjon meg egy értéket **az eseményforrás nevéhez,** amely `Contoso-TSI-GA-Event-Hub-ES`egyedi a Time Series Insights környezetben, például .

1. A **Forrás csoportban**válassza az **Eseményközpont**lehetőséget.

1. Válassza ki a megfelelő értékeket **az Importálás beállításhoz:**

   * Ha az egyik előfizetésében van egy meglévő eseményközpont, válassza **az Event Hub használata lehetőséget az elérhető előfizetésekből.** Ez a lehetőség a legegyszerűbb megközelítés.

     [![Eseményforrás importálási beállításának kiválasztása](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  Az alábbi táblázat az Elérhető **előfizetésekből** az Eseményközpont használatához szükséges tulajdonságokat ismerteti:

       [![Előfizetés és eseményközpont részletei](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés | Az előfizetés, amelyhez a kívánt eseményközpont-példány és névtér tartozik. |
       | Event Hubs-névtér | Az az eseményközpont-névtér, amelyhez a kívánt eseményközpont-példány tartozik. |
       | Eseményközpont neve | A kívánt eseményközpont-példány neve. |
       | Az Event Hub házirend-értéke | Válassza ki a kívánt megosztott hozzáférési szabályzatot. A megosztott hozzáférési szabályzatot az eseményközpont **konfigurálása** lapján hozhatja létre. Minden megosztott hozzáférési házirendrendelkezik névvel, a beállított engedélyekkel és hozzáférési kulcsokkal. Az eseményforrás megosztott hozzáférési szabályzatának **olvasási** engedéllyel *kell* rendelkeznie. |
       | Az Eseményközpont házirendkulcsa | A kijelölt Event Hub-házirend-értékből előtöltve. |

    * Ha az eseményközpont az előfizetéseken kívül van, vagy ha speciális beállításokat szeretne megadni, válassza **az Eseményközpont beállításainak manuális megadása lehetőséget.**

       Az alábbi táblázat az **Eseményközpont manuális beállításainak biztosításához** szükséges tulajdonságokat ismerteti:
 
       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés azonosítója | Az előfizetés, amelyhez a kívánt eseményközpont-példány és névtér tartozik. |
       | Erőforráscsoport | Az erőforráscsoport, amelyhez a kívánt eseményközpont-példány és névtér tartozik. |
       | Event Hubs-névtér | Az az eseményközpont-névtér, amelyhez a kívánt eseményközpont-példány tartozik. |
       | Eseményközpont neve | A kívánt eseményközpont-példány neve. |
       | Az Event Hub házirend-értéke | Válassza ki a kívánt megosztott hozzáférési szabályzatot. A megosztott hozzáférési szabályzatot az eseményközpont **konfigurálása** lapján hozhatja létre. Minden megosztott hozzáférési házirendrendelkezik névvel, a beállított engedélyekkel és hozzáférési kulcsokkal. Az eseményforrás megosztott hozzáférési szabályzatának **olvasási** engedéllyel *kell* rendelkeznie. |
       | Az Eseményközpont házirendkulcsa | A szolgáltatásbusz névteréhez való hozzáférés hitelesítésére használt megosztott hozzáférési kulcs. Itt adhatja meg az elsődleges vagy másodlagos kulcsot. |

    * Mindkét lehetőség a következő konfigurációs lehetőségeket osztja meg:

       | Tulajdonság | Leírás |
       | --- | --- |
       | Eseményközpont fogyasztói csoportja | Az eseményközpontot beolvasó fogyasztói csoport. Javasoljuk, hogy egy dedikált fogyasztói csoportot használjon az eseményforráshoz. |
       | Eseményszerializációs formátum | Jelenleg a JSON az egyetlen elérhető szerializálási formátum. Az eseményüzeneteknek ebben a formátumban kell lenniük, különben az adatok nem olvashatók. |
       | Időbélyeg-tulajdonság neve | Ennek az értéknek a meghatározásához meg kell értenie az eseményközpontba küldött üzenetadatok üzenetformátumát. Ez az érték az eseményidőbélyegként használni kívánt üzenetadatokadott eseménytulajdonságának **neve.** Az érték nem a di. Ha üresen hagyja, az **esemény enqueue idő** az esemény forrásában az esemény időbélyegként lesz használva. |

1. Adja hozzá a Time Series Insights fogyasztói csoport nevét, amelyet az eseményközpontba adott hozzá.

1. Kattintson a **Létrehozás** gombra.

   Az eseményforrás létrehozása után a Time Series Insights automatikusan megkezdi az adatok streamelését a környezetbe.

## <a name="next-steps"></a>További lépések

* Az adatok védelméhez határozza meg az [adatelérési házirendeket.](time-series-insights-data-access.md)

* [Események küldése](time-series-insights-send-events.md) az eseményforrásra.

* A [Time Series Insights felfedezőjében](https://insights.timeseries.azure.com)érheti el környezetét.
