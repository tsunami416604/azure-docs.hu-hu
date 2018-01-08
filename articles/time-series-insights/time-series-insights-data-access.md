---
title: "Férhessen hozzá és felügyelhesse Azure idő adatsorozat Insights biztonságának konfigurálása |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan biztonsági és engedélyek beállítása a felügyeleti hozzáférési házirendek és az adat-hozzáférési szabályzatok Azure idő adatsorozat Insights biztonságossá."
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: c7d4079c9106226e0d07aa97c4a52c16ddb257c3
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Adathozzáférés biztosítása egy Time Series Insights-környezethez az Azure Portal segítségével

A Time Series Insights-környezetek két különböző típusú hozzáférési házirenddel rendelkeznek:

* Felügyeleti hozzáférési házirendek
* Adathozzáférési házirendek

Mindkét házirend különféle engedélyeket biztosít az Azure Active Directory rendszerbiztonsági tagjai (felhasználók és alkalmazások) számára egy adott környezetre vonatkozóan. A résztvevők (felhasználók és alkalmazások) az active directory (más néven az Azure-bérlőhöz) az a környezet tartalmazó előfizetéssel társított kell tartoznia.

A felügyeleti hozzáférési házirendek a környezet konfigurálásához kapcsolódó engedélyeket biztosítanak, például:
*   A környezet létrehozása vagy törlése, eseményforrások, referencia-adatkészletek; valamint
*   Az adathozzáférési házirendek felügyelete.

Az adathozzáférési házirendek a következőkhöz biztosítanak engedélyeket: adatlekérdezések kiadása, referenciaadatok módosítása a környezetben, valamint a környezethez társított mentett lekérdezések és perspektívák megosztása.

A házirendek két típusa lehetővé teszi a környezet felügyeletéhez történő hozzáférés és a környezetben található adatokhoz való hozzáférés teljes mértékű szétválasztását. Például is lehet, hogy a környezet tulajdonosa/létrehozó a rendszer eltávolítja az adatok elérése a környezet beállításával. Ezenkívül felhasználók és a szolgáltatások, hogy mely adatokat olvasni a környezet adható nem érhető el a környezet konfigurációjától.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása
Kövesse az alábbi adatok hozzáférést egy felhasználó egyszerű lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a idő adatsorozat Insights környezetében. Típus **Time Series** a a **keresési** mezőbe. Válassza ki **adatsorozat környezet** a keresési eredmények között. 

3. Válassza ki az Azure Time Series Insights környezetet a listából.
   
4. Válassza ki **adat-hozzáférési házirendjeit**, majd jelölje be **+ Hozzáadás**.
  ![Kezelheti a idő adatsorozat Insights forrás - környezet](media/data-access/getstarted-grant-data-access1.png)

5. Válassza ki **felhasználó kijelölése**.  Keresse meg a felhasználói név vagy e-mail cím található a felhasználó hozzá szeretné adni. Kattintson a **válasszon** a kijelölés megerősítéséhez. 

   ![A Time Series Insights-forrás felügyelete – hozzáadás](media/data-access/getstarted-grant-data-access2.png)

6. Válassza ki **válassza szerepkör**. Válassza ki a megfelelő felhasználói szerepkört a felhasználó számára:
   - Válassza ki **közreműködő** Ha szeretné-e a környezet más felhasználókkal perspektívák referenciaadatok és mentett megosztás lekérdezések és módosításának engedélyezése. 
   - Máskülönben válassza **olvasó** engedélyezése a felhasználó adatait a környezetben, és a személyes (megosztott) lekérdezések mentéséhez a környezetben.

   Válassza ki **Ok** szerepkör kiválasztásának megerősítésére.

   ![A Time Series Insights-forrás felügyelete – felhasználó kiválasztása](media/data-access/getstarted-grant-data-access3.png)

8. Válassza ki **Ok** a a **felhasználói szerepkör kiválasztása** lap.

   ![A Time Series Insights-forrás felügyelete – szerepkör kiválasztása](media/data-access/getstarted-grant-data-access4.png)

9. A **adat-hozzáférési házirendjeit** lap felsorolja a felhasználók és az egyes felhasználók szerepkör(ök).

   ![A Time Series Insights-forrás felügyelete – eredmények](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>További lépések
* Ismerje meg, [egy Eseményközpontba eseményforrás felvétele az Azure idő adatsorozat Insights környezet](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Események küldése](time-series-insights-send-events.md) esemény forrását.
* A környezet megtekintése [idő adatsorozat Insights explorer](https://insights.timeseries.azure.com).
