---
title: IoT Hub-eseményforrás hozzáadása Azure Time Series Insights |} A Microsoft Docs
description: Ez a cikk ismerteti, amely csatlakozik az IoT Hub a Time Series Insights-környezetbe eseményforrás hozzáadása
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 3168fb6cf6e9b08fe4d2de921179129db241d153
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847167"
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>IoT Hub-eseményforrás hozzáadása a Time Series Insights-környezet

Ez a cikk ismerteti, amely adatokat olvas az IoT hubra az Azure Time Series Insights (TSI) környezetbe eseményforrás hozzáadása az Azure portal használatával.

> [!NOTE]
> Ezek az utasítások a TSI a GA és TSI előzetes környezetekben egyaránt érvényesek.

## <a name="prerequisites"></a>Előfeltételek

* A TSI-környezetet hozhat létre. További információkért lásd: [Azure TSI környezet létrehozása](time-series-insights-update-create-environment.md).
* IoT Hub létrehozása Az IoT-központok további információkért lásd: [hozzon létre egy IoT hubra az Azure portal használatával](../iot-hub/iot-hub-create-through-portal.md).
* Az IoT Hub küld a rendszer aktív üzenetek esemény rendelkeznie kell.
* Az IoT Hub a TSI-környezethez való felhasználásához hozzon létre egy dedikált fogyasztói csoportot. Minden egyes TSI eseményforrás rendelkeznie kell a saját dedikált fogyasztói csoportot, amelyek nincsenek megosztva, bármely más fogyasztók számára. Ha több olvasók ugyanabban a fogyasztói csoportban lévő események felhasználásához, minden olvasók valószínűleg hibák. További információkért lásd: a [IoT Hub fejlesztői útmutatójának](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Az IoT Hub fogyasztói csoport hozzáadása

Adatok lekérése az Azure IoT hub fogyasztói csoportok alkalmazások használják. Adja meg a TSI környezet csak, megbízhatóan adatokat olvasni az IoT Hub által használt, dedikált fogyasztói csoportot.

Az IoT Hub egy új felhasználói csoport hozzáadásához kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg és nyissa meg az IoT hubnak.
1. Alatt a **beállítások** szakaszban kattintson **beépített végpontokról**.

   ![Egy IoT Hub][1]

1. Válassza ki a **események** végpont, és a **tulajdonságok** lap megnyitásakor.

1. Alatt a **fogyasztói csoportok** fejléc, adjon meg egy új egyedi nevet a fogyasztói csoportot. A TSI környezetben használja ezt a nevet egy új eseményforrás létrehozása során.

1. Válassza ki **mentése** menteni az új felhasználói csoportot.

## <a name="add-a-new-event-source"></a>Adjon hozzá egy új esemény forrása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő TSI-környezetbe. Kattintson a **összes erőforrás** az Azure portal bal oldali menüben. Válassza ki a TSI-környezetet.

1. Alatt a **környezeti topológia** szakaszban kattintson **eseményforrások**.

   ![Az IoT Hub két][2]

1. Kattintson a **+Hozzáadás** gombra.

1. Adjon meg egy **eseményforrás nevének** egyedi a TSI-környezethez, mint például **esemény-adatfolyam**.

   ![Az IoT Hub három][3]

1. Válassza ki a **forrás** , **az IoT Hub**.

1. Válassza ki a megfelelő **importálási beállítás**.

   * Válasszon **használja az IoT Hub a rendelkezésre álló előfizetések** Ha már rendelkezik egy meglévő IoT hubbal egy előfizetés. Ez a legegyszerűbb megközelítés.
   * Válasszon **adja meg az IoT Hub beállításainak manuális** amikor az IoT Hub külső az előfizetésekhez, vagy adja meg a speciális beállításokat szeretné.

1. Ha be van jelölve a **használja az IoT Hub a rendelkezésre álló előfizetések** beállítást, az alábbi táblázat azt ismerteti, minden kötelező tulajdonság:

   ![Az IoT Hub négy][4]

   | Tulajdonság | Leírás |
   | --- | --- |
   | Előfizetés azonosítója | Válassza ki az előfizetést, amelyben ez az IoT Hub létrehozása történt.
   | Az IoT Hub nevét | Válassza ki az IoT Hub nevét.
   | Az IoT Hub-házirend neve | Válassza ki a megosztott elérési házirendet, amely az IoT Hub beállításai lapon található. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. A megosztott hozzáférési szabályzatot az eseményforrás *kell* rendelkezik **szolgáltatás csatlakozása** engedélyeket.
   | Az IoT Hub házirendjének kulcsa | A kulcs van töltve.
   | Az IoT Hub fogyasztói csoport | A fogyasztói csoportot, olvassa az eseményeket az IoT hubról. Azt javasoljuk, használjon dedikált fogyasztói csoportot az eseményforrás a.
   | Eseményszerializációs formátum | JSON-ja jelenleg csak akkor érhető el szerializálásának. Az eseményt üzenetek a következő formátumban kell lennie, vagy az adatok nem olvashatók. |
   | Időbélyegző-tulajdonság neve | Ez az érték határozza meg, meg kell megismerheti az IoT hubra küldött üzenet adatokat üzenet formátumát. Ez az érték a **neve** az állapotüzenet-adatokat használja, mint az esemény időbélyegzője kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen hagyja, a **eseményt sorba idő** belül az eseményt az esemény időbélyegzője forrás szolgál. |

1. Ha be van jelölve a **adja meg az IoT Hub beállításainak manuális** beállítást, az alábbi táblázat azt ismerteti, minden kötelező tulajdonság:

   | Tulajdonság | Leírás |
   | --- | --- |
   | Előfizetés azonosítója | Az előfizetést, amelyhez a IoT Hub létrehozása.
   | Erőforráscsoport | Az IoT Hub létrehozása, amelyekben ez erőforráscsoport nevét.
   | Az IoT Hub nevét | Az IoT Hub nevére. Ha létrehozott egy IoT hubot, akkor is rendelte hozzá egy egyedi nevet.
   | Az IoT Hub-házirend neve | A megosztott elérési házirendet, amely az IoT Hub beállításai lapon hozható létre. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. A megosztott hozzáférési szabályzatot az eseményforrás *kell* rendelkezik **szolgáltatás csatlakozása** engedélyeket.
   | Az IoT Hub házirendjének kulcsa | A közös hozzáférési kulcs segítségével hitelesíti a hozzáférést a Service Bus-névteret. Ide írja be az elsődleges vagy másodlagos kulcsot.
   | Az IoT Hub fogyasztói csoport | A fogyasztói csoportot, olvassa az eseményeket az IoT hubról. Azt javasoljuk, használjon dedikált fogyasztói csoportot az eseményforrás a.
   | Eseményszerializációs formátum | JSON-ja jelenleg csak akkor érhető el szerializálásának. Az eseményt üzenetek a következő formátumban kell lennie, vagy az adatok nem olvashatók. |
   | Időbélyegző-tulajdonság neve | Ez az érték határozza meg, meg kell megismerheti az IoT hubra küldött üzenet adatokat üzenet formátumát. Ez az érték a **neve** az állapotüzenet-adatokat használja, mint az esemény időbélyegzője kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen hagyja, a **eseményt sorba idő** belül az eseményt az esemény időbélyegzője forrás szolgál. |

1. Adja hozzá a dedikált TSI fogyasztói csoport neve az IoT Hub korábban hozzáadott.

1. Válassza ki **létrehozás** hozzáadni az új esemény forrását.

   ![Az IoT Hub öt][5]

1. Az eseményforrás létrehozása után a Time Series Insights automatikusan megkezdi az adatok streamelését a környezetbe.

## <a name="next-steps"></a>További lépések

* [Adathozzáférési házirendek meghatározása](time-series-insights-data-access.md) védi az adatait.
* [Események küldése](time-series-insights-send-events.md) esemény forrását.
* A környezet eléréséhez a [Time Series Insights explorer](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png
[2]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png
[3]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png
[4]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png
[5]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png