---
title: Az IoT-központ eseményforrást hozzáadása Azure idő adatsorozat Insights |} Microsoft Docs
description: Ez a cikk ismerteti, amely az IoT-központ az idő adatsorozat Insights-környezethez csatlakozik egy eseményforrás hozzáadása
ms.service: time-series-insights
services: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: 01ab5017a5f16a0c46ea2bc600cef6bbf81de963
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652525"
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Az IoT-központ eseményforrást felvétele idő adatsorozat Insights környezet
A cikkből megtudhatja, hogyan használható az Azure-portálon olvassa be az adatokat az IoT-központ idő adatsorozat Insights környezetébe eseményforrást hozzáadása.

## <a name="prerequisites"></a>Előfeltételek
- Hozzon létre egy idő adatsorozat Insights környezetet. További információkért lásd: [Azure idő adatsorozat Insights környezet létrehozása](time-series-insights-get-started.md) 
- Létrehoz egy IoT-központot. Az IoT-központok további információkért lásd: [létrehoz egy IoT-központot, az Azure portál használatával](../iot-hub/iot-hub-create-through-portal.md)
- Az IoT Hub küldési aktív üzenet események rendelkeznie kell.
- Hozzon létre egy dedikált fogyasztói csoportot az IoT hubon idő adatsorozat Insight környezetre való felhasználását. Minden alkalommal adatsorozat Insights eseményforrás saját dedikált fogyasztói csoportot, amelyek nincsenek megosztva, más fogyasztóval rendelkeznie kell. Ha több olvasók ugyanazt a felhasználói csoportban lévő események felhasználásához, minden olvasók valószínűleg tekintse meg a hibákat. További információkért lásd: a [IoT Hub fejlesztői útmutató](../iot-hub/iot-hub-devguide.md).

## <a name="add-a-new-event-source"></a>Új esemény-forrás hozzáadása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a meglévő idő adatsorozat Insights környezetben. Kattintson a **összes erőforrás** a menüben, az Azure portál bal oldalán. Válassza ki az Azure Time Series Insights-környezetet.

3. Az a **környezet topológia** elemcsoportban kattintson **eseményforrások**.
   ![Esemény adatforrásokat + Hozzáadás](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Kattintson a **+Hozzáadás** gombra.

5. Adjon meg egy **Eseményforrás neve** egyedi idő adatsorozat Insights ebben a környezetben, mint **esemény-adatfolyam**.

   ![Töltse ki az első három paramétereket a képernyőn.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. Válassza ki a **forrás** , **IoT-központ**.

7. Válassza ki a megfelelő **beállítás importálása**. 
   - Válasszon **az elérhető előfizetések IoT Hubjának használata** Ha már rendelkezik egy meglévő IoT-központ az egyik előfizetése. Ez a legegyszerűbb módszer.
   - Válasszon **adja meg az IoT Hub beállításainak manuális** amikor az IoT-központ az előfizetésekhez külső, vagy válassza a speciális beállításokat szeretne. 

8. Ha be van jelölve a **az elérhető előfizetések IoT Hubjának használata** beállítást, a következő táblázat ismerteti, minden szükséges tulajdonság:

   ![Előfizetés és az Event hub-adatok](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Tulajdonság | Leírás |
   | --- | --- |
   | Előfizetés azonosítója | Válassza ki az előfizetést, ahol ez az IoT-központ hozták létre.
   | Az IoT-központ nevét | Válassza ki az IoT Hub nevét.
   | Az IoT-központ házirend neve | Válassza ki a megosztott elérési házirendet, amely az IoT-központ beállítások lapján található. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. A megosztott hozzáférési házirend a eseményforrás *kell* rendelkezik **service csatlakozás** engedélyek.
   | Az IoT-központ házirend kulcs | A kulcs van előre feltöltve.
   | Az IoT-központ fogyasztói csoportot | A rendszer kiolvassa az eseményeket az IoT hubból fogyasztói csoportot. Ajánlott az eseményforrás dedikált fogyasztói csoportot használja.
   | Eseményszerializációs formátum | JSON-ja jelenleg csak a rendelkezésre álló szerializálást. Az eseményüzeneteket a következő formátumban kell lennie, vagy nincsenek adatok olvashatók. |
   | Időbélyeg-tulajdonság neve | Ez az érték határozza meg, az üzenet adataihoz, az IoT hubhoz küldött üzenet formátuma tisztában kell. Ez az érték a **neve** az üzenet adataihoz, az esemény időbélyegzője használni kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen marad, a **esemény sorba helyezni időtartam** belül az esemény az esemény időbélyegzője forrás használatos. |

9. Ha be van jelölve a **adja meg az IoT Hub beállításainak manuális** beállítást, a következő táblázat ismerteti, minden szükséges tulajdonság:

   | Tulajdonság | Leírás |
   | --- | --- |
   | Előfizetés azonosítója | Az előfizetés, amely ezt az IoT-központ létrehozta.
   | Erőforráscsoport | A erőforráscsoport-név, amely ezt az IoT-központ készült.
   | Az IoT-központ nevét | Az IoT Hub neve. Az IoT Hub létrehozása után, akkor nevet is adott neki.
   | Az IoT-központ házirend neve | A megosztott elérési házirendet, amelyet létrehozhat az IoT-központ beállítások lapon. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. A megosztott hozzáférési házirend a eseményforrás *kell* rendelkezik **service csatlakozás** engedélyek.
   | Az IoT-központ házirend kulcs | A megosztott elérési kulcsot a Service Bus-névtér való hozzáférés hitelesítéséhez használt. Ide írja be az elsődleges vagy másodlagos kulcsot.
   | Az IoT-központ fogyasztói csoportot | A rendszer kiolvassa az eseményeket az IoT hubból fogyasztói csoportot. Ajánlott az eseményforrás dedikált fogyasztói csoportot használja.
   | Eseményszerializációs formátum | JSON-ja jelenleg csak a rendelkezésre álló szerializálást. Az eseményüzeneteket a következő formátumban kell lennie, vagy nincsenek adatok olvashatók. |
   | Időbélyeg-tulajdonság neve | Ez az érték határozza meg, az üzenet adataihoz, az IoT hubhoz küldött üzenet formátuma tisztában kell. Ez az érték a **neve** az üzenet adataihoz, az esemény időbélyegzője használni kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen marad, a **esemény sorba helyezni időtartam** belül az esemény az esemény időbélyegzője forrás használatos. |

10. Válassza ki **létrehozása** hozzáadása az új esemény forrását.

   ![Kattintson a Létrehozás gombra](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   Az eseményforrás létrehozása után a Time Series Insights automatikusan megkezdi az adatok streamelését a környezetbe.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Az IoT Hub egy felhasználói csoport hozzáadása
Alkalmazások az fogyasztói csoportok segítségével olvasnak be adatokat Azure IoT-központok. Adjon meg egy dedikált fogyasztói csoportot idő adatsorozat Insights környezet csak, megbízhatóan adatokat olvasni az IoT Hub általi használatra.

Egy új felhasználói csoport hozzáadása az IoT Hub, kövesse az alábbi lépéseket:
1. Keresse meg az Azure-portálon, és nyissa meg az IoT Hub.

2. Az a **Messaging** elemcsoportban válasszon **végpontok**. 

   ![Egy felhasználói csoport hozzáadása](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Válassza ki a **események** végpontot, és a **tulajdonságok** lap megnyitásakor.

4. Az a **fogyasztói csoportok** fejlécre, adjon meg egy új egyedi nevet a fogyasztói csoportot. Az azonos név használata idő adatsorozat Insights környezetben, egy új eseményforrás létrehozása során.

5. Válassza ki **mentése** menteni az új fogyasztói csoportot.

## <a name="next-steps"></a>További lépések
- [Adja meg az adat-hozzáférési házirendjeit](time-series-insights-data-access.md) az adatok védelmét.
- [Események küldése](time-series-insights-send-events.md) esemény forrását.
- A környezet az elérni a [idő adatsorozat Insights explorer](https://insights.timeseries.azure.com).
