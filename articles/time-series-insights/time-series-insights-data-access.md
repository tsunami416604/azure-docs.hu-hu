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
ms.openlocfilehash: 7cb5dc5b170103f98d56abc920f36dd85f855961
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364819"
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

[!INCLUDE [iot-tsi-data-access](../../includes/iot-tsi-data-access.md)]

## <a name="next-steps"></a>További lépések

* Ismerje meg, [Event Hub-eseményforrás hozzáadása Azure Time Series Insights-környezete](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Események küldése](time-series-insights-send-events.md) esemény forrását.
* A környezet megtekintése a [Time Series Insights explorer](https://insights.timeseries.azure.com).
