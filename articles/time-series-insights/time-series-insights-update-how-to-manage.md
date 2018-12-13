---
title: Az Azure Time Series előzetes management – hogyan építhet ki és felügyelhet az Azure Time Series előzetes verziójára. | Microsoft Docs
description: Megértse, hogyan építhet ki és felügyelhet az Azure Time Series Insights előzetes verziója.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1834a59ff675f109267a406e3c2b03411e3bb50b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273508"
---
# <a name="how-to-provision-and-manage-azure-time-series-insights-preview"></a>Hogyan építhet ki és felügyelhet az Azure Time Series Insights előzetes verziója

Ez a dokumentum ismerteti, hogyan hozhat létre és kezelése az Azure Time Series Insights – előzetes környezet használatával a [az Azure portal](https://portal.azure.com/).

## <a name="overview"></a>Áttekintés

Alább olvasható kapcsolatos kiépítése az Azure Time Series Insights – előzetes environment rövid áttekintése:

* Az Azure Time Series Insights előzetes verziója környezetek **PAYG** környezetekben.
  * A létrehozási folyamat részeként meg kell adnia egy Time Series azonosítóját. Time Series azonosítók legfeljebb három kulcsot is lehet. További információért olvassa el a Time Series ID kiválasztásával kapcsolatos [kiválasztása a Time Series ID](./time-series-insights-update-how-to-id.md).
  * Amikor üzembe helyezi az Azure Time Series Insights – előzetes környezet, hozzon létre két Azure-erőforrások:

    * Az Azure Time Series Insights – előzetes környezet  
    * Azure Storage-általános célú V1 fiók
  
    Ismerje meg, [a környezet tervezésével](./time-series-insights-update-plan.md).

    >[!IMPORTANT]
    > V2-fiókok használó ügyfeleink számára ne engedélyezze a ritka elérésű vagy archív tulajdonságai a storage-fiókot fog használni.

* Minden egyes Azure Time Series Insights – előzetes környezet (opcionális) társítható egy esemény forrását. Olvasási [egy Event Hub-forrás hozzáadása](./time-series-insights-how-to-add-an-event-source-eventhub.md) és [egy IoT Hub-forrás hozzáadása](./time-series-insights-how-to-add-an-event-source-iothub.md) további információt.
  * Ez a lépés során egy időbélyeg ID tulajdonsága és a egy egyedi felhasználói csoport biztosítja. Ezzel biztosítja, hogy a környezet férhet hozzá a megfelelő események.

* Kiépítés befejeződése után módosíthatja a hozzáférési házirendek és más környezeti attribútumok suite az üzleti követelményeinek.

## <a name="new-environment-creation"></a>Az új környezet létrehozása

Az alábbi lépések bemutatják, hogyan hozhat létre az Azure Time Series Insights – előzetes környezet:

1. Válassza ki a **PAYG** gomb alatt a **Termékváltozat** menü. Egy környezet nevét adja meg, melyik előfizetést csoporthoz, és melyik erőforráscsoport. Ezután válassza ki a környezetet üzemeltetni, a támogatott helyét.

1. Adjon meg egy Time Series-azonosító

    >[!NOTE]
    > * A Time Series-azonosító, kis-és nagybetűket, és nem módosítható (nem módosítható beállítása után).
    > * Time Series azonosítók legfeljebb 3 kulcsok is lehet.
    > * További információk a olvassa el a Time Series ID kiválasztásával [kiválasztása a Time Series ID](./time-series-insights-update-how-to-id.md).

1. Hozzon létre egy Azure Storage-fiókot egy Azure Storage-fiók neve kiválasztásával, és a egy replikációs választott kijelölése. Ez így automatikusan létrehoz egy Azure Storage általános célú V1 fiók. A korábban kiválasztott Azure Time Series Insights – előzetes környezet ugyanabban a régióban jön.

    ![Hozzon létre egy Azure Time Series Insights-példányt.][1]

1. Szükség esetén az eseményforrás is hozzáadhat.

   * Time Series Insights támogatja [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) és [az Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) közül. Csak akkor adhat hozzá egyetlen eseményforrás környezet létrehozáskor történik, míg további eseményforrás később is hozzáadhat. Érdemes annak érdekében, az Azure Time Series Insights – előzetes-példány látható az összes esemény egyedi fogyasztói csoport létrehozása. Válasszon egy meglévő fogyasztói csoportot, vagy hozzon létre egy új fogyasztói csoportot, az eseményforrás hozzáadása során.

   * Is ki kell jelölnie a megfelelő időbélyeg-tulajdonság. Alapértelmezés szerint az Azure Time Series Insights használja az üzenet sorba helyezésekor minden egyes esemény forrását.

     > [!TIP]
     > Az üzenet sorba helyezésekor nem feltétlenül a legjobb konfigurált beállítást, a batch-esemény vagy előzményadatokat forgatókönyvek feltöltés. Ellenőrizze, hogy ellenőrizze a döntést, vagy használjon egy időbélyeg-tulajdonság az ezekben az esetekben.

    ![Eseményforrás hozzáadása a példányhoz.][2]

1. Áttekintés és létrehozás

    ![Eseményforrás hozzáadása a példányhoz.][3]

Győződjön meg arról, hogy a környezet van kiépítve a kívánt beállításokat.

## <a name="management"></a>Kezelés

Akkor tudja felügyelni az Azure Time Series Insights – előzetes környezet, az Azure portal használatával. A jelentősebb eltérések kezelését az alábbiakban egy **PAYG** Azure Time Series Insights – előzetes környezet helyett egy S1 vagy s2 szintű környezetben az Azure Portal használatával:

* Az Azure Portalon *áttekintése* panel az Azure Time Series Insightsban kivételével változatlan marad a következő módon:
  * Kapacitás eltávolítva, mivel a fogalom esetében nem releváns **PAYG** környezetekben.
  * A Time Series azonosító tulajdonsággal bővült. Meghatározza, hogy az adatok particionálásáról.
  * Referencia-adatkészletekhez el lesznek távolítva.
  * A megjelenő URL-CÍMÉT arra utasítja, hogy a [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).
  * Az Azure Storage-fiók neve szerepel a listán.

* Az Azure Portal *konfigurálása* panel az Azure Time Series Insights – előzetes eltávolították, hiszen nincs is **PAYG** környezetek amelyek nem konfigurálhatók.

* Az Azure Portal *referencia* adatok panel az Azure Time Series Insights – előzetes eltávolították, mivel a referenciaadatok nem részét képező **PAYG** környezetekben.

![Ellenőrizze a példány.][4]

## <a name="next-steps"></a>További lépések

Olvasási [a környezet tervezésével](./time-series-insights-update-plan.md).

Olvasási [egy Event Hub-forrás hozzáadása](./time-series-insights-how-to-add-an-event-source-eventhub.md).

Olvasási [egy IoT Hub-forrás hozzáadása](./time-series-insights-how-to-add-an-event-source-iothub.md).

<!-- Images -->
[1]: media/v2-update-manage/manage_one.png
[2]: media/v2-update-manage/manage_two.png
[3]: media/v2-update-manage/manage_three.png
[4]: media/v2-update-manage/manage_four.png
