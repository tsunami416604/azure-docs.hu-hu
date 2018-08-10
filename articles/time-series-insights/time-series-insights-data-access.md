---
title: Eléréséhez és kezeléséhez az Azure Time Series Insights biztonságának konfigurálása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhat egy felügyeleti hozzáférés és az engedélyek szabályzatok és az adat-hozzáférési szabályzatok az Azure Time Series Insights secure.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 97c9480d6f2b75d83252bfb6410d7b5f946757ef
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630653"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Adathozzáférés biztosítása egy Time Series Insights-környezethez az Azure Portal segítségével

A Time Series Insights-környezetek két különböző típusú hozzáférési házirenddel rendelkeznek:

* Felügyeleti hozzáférési házirendek
* Adathozzáférési házirendek

Mindkét házirend különféle engedélyeket biztosít az Azure Active Directory rendszerbiztonsági tagjai (felhasználók és alkalmazások) számára egy adott környezetre vonatkozóan. A rendszerbiztonsági tagok (felhasználók és alkalmazások) a környezetet tartalmazó előfizetéshez társított active directory (más néven az Azure-bérlő) kell tartoznia.

A felügyeleti hozzáférési házirendek a környezet konfigurálásához kapcsolódó engedélyeket biztosítanak, például:
*   A környezet létrehozása vagy törlése, eseményforrások, referencia-adatkészletek; valamint
*   Az adathozzáférési házirendek felügyelete.

Az adathozzáférési házirendek a következőkhöz biztosítanak engedélyeket: adatlekérdezések kiadása, referenciaadatok módosítása a környezetben, valamint a környezethez társított mentett lekérdezések és perspektívák megosztása.

A házirendek két típusa lehetővé teszi a környezet felügyeletéhez történő hozzáférés és a környezetben található adatokhoz való hozzáférés teljes mértékű szétválasztását. Például akkor lehet, hogy a környezet tulajdonosa/létrehozója eltávolítják az adatelérés környezet beállításával. Ezenkívül felhasználók és szolgáltatások, hogy mely adatokat olvasni a környezet nyújtható nem érhető el a környezet konfigurációjától.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása
Adathozzáférés egy felhasználó rendszerbiztonsági tag az alábbi lépéseket követve:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a Time Series Insights-környezet. Típus **Time Series** a a **keresési** mezőbe. Válassza ki **Time Series Environment** a keresési eredmények között. 

3. Válassza ki az Azure Time Series Insights környezetet a listából.
   
4. Válassza ki **az adathozzáférési házirendek**, majd **+ Hozzáadás**.
  ![A Time Series Insights-forrás felügyelete – környezet](media/data-access/getstarted-grant-data-access1.png)

5. Válassza ki **felhasználó kiválasztása**.  Keresse meg a felhasználói név vagy e-mail cím a hozzáadni kívánt felhasználó található. Kattintson a **kiválasztása** a kijelölés megerősítéséhez. 

   ![A Time Series Insights-forrás felügyelete – hozzáadás](media/data-access/getstarted-grant-data-access2.png)

6. Válassza ki **szerepkör kiválasztása**. Válassza ki a felhasználó a megfelelő hozzáférés-szerepkör:
   - Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival. 
   - Ellenkező esetben válassza **olvasó** lehetővé teszik a felhasználói adatokat lekérdezni a környezetben, és személyes (nem megosztott) lekérdezéseket mentéséhez a környezetben.

   Válassza ki **Ok** szerepkör kiválasztásának megerősítéséhez.

   ![A Time Series Insights-forrás felügyelete – felhasználó kiválasztása](media/data-access/getstarted-grant-data-access3.png)

8. Válassza ki **Ok** a a **felhasználói szerepkör kiválasztása** lapot.

   ![A Time Series Insights-forrás felügyelete – szerepkör kiválasztása](media/data-access/getstarted-grant-data-access4.png)

9. A **az adathozzáférési házirendek** lap felsorolja a felhasználók és a szerepkör minden felhasználóhoz.

   ![A Time Series Insights-forrás felügyelete – eredmények](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>További lépések
* Ismerje meg, [Event Hub-eseményforrás hozzáadása Azure Time Series Insights-környezete](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Események küldése](time-series-insights-send-events.md) esemény forrását.
* A környezet megtekintése a [Time Series Insights explorer](https://insights.timeseries.azure.com).
