---
title: "Az Event Hubs eseményforrást felvétele az Azure idő adatsorozat Insights környezet |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan adhat hozzá egy eseményforrás, amely egy Eseményközpontot, az idő adatsorozat Insights-környezethez csatlakozik"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: f6a993b3858cfb94dd9795f5e55f15fa6ec7dcb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-event-hub-event-source"></a>Az Event Hubs forrás hozzáadása

Ez az oktatóanyag bemutatja, hogyan adhat az Azure portál segítségével olvassa be az Eseményközpontban az idő adatsorozat Insights környezethez eseményforrást hozzáadása.

## <a name="prerequisites"></a>Előfeltételek

Az Eseményközpontok hozott létre, és hozzá események írás. Az Event Hubs további információkért lásd: <https://azure.microsoft.com/services/event-hubs/>

> [A fogyasztói csoportok] Minden alkalommal adatsorozat Insights eseményforrás saját dedikált fogyasztói csoportot, amelyek nincsenek megosztva, más fogyasztóval rendelkeznie kell. Ha több olvasók ugyanazt a felhasználói csoportban lévő események felhasználásához, minden olvasók valószínűleg tekintse meg a hibákat. Vegye figyelembe, hogy nincs-e is maximális hossza 20 felhasználói csoportot az Event Hubs egy. További információkért lásd: a [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md).

## <a name="choose-an-import-option"></a>Válassza ki importálása

Az eseményforrás beállításait is meg lehet adni manuálisan, vagy az eseményközpontok az event hubs, amelyek elérhetők a választható ki.
Az a **importálási lehetőség** választó, a következő lehetőségek közül választhat:

* Adja meg az Event Hubs beállításokat manuálisan
* Az elérhető előfizetések Eseményközpontjának használata

### <a name="select-an-available-event-hub"></a>Válassza ki az elérhető Eseményközpontok

Az alábbi táblázat ismerteti az új eseményforrás lapon annak leírását az egyes lehetőségek lehetőséget választva a rendelkezésre álló Eseményközpontban, egy Eseményforrás:

| TULAJDONSÁG NEVE | LEÍRÁS |
| --- | --- |
| Eseményforrás neve | A forrás neve. Ez a név a idő adatsorozat Insights környezeten belül egyedinek kell lennie.
| Forrás | Válasszon **Eseményközpont** egy Eseményközpontba eseményforrás létrehozásához.
| Előfizetés-azonosító | Válassza ki az előfizetést, amelyben az eseményközpont létre lett hozva.
| Service bus-névtér | Válassza ki a Service Bus-névtér, amely tartalmazza az Eseményközpontba.
| Eseményközpont neve | Válassza ki az Eseményközpont nevét.
| Eseményközpont házirend neve | Válassza ki a megosztott elérési házirendet, amely az Event Hub konfigurálása lapon is létrehozható. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. A megosztott hozzáférési házirend a eseményforrás *kell* rendelkezik **olvasási** engedélyek.
| Event hub fogyasztói csoportot | A rendszer kiolvassa az eseményeket az eseményközpontból fogyasztói csoportot. Ajánlott az eseményforrás dedikált fogyasztói csoportot használja.

### <a name="provide-event-hub-settings-manually"></a>Adja meg az Event Hubs beállításokat manuálisan

Az alábbi táblázat ismerteti az új esemény (forrás) lapot annak leírását az egyes tulajdonságai beállításainak megadásakor manuálisan:

| TULAJDONSÁG NEVE | LEÍRÁS |
| --- | --- |
| Eseményforrás neve | A forrás neve. Ez a név a idő adatsorozat Insights környezeten belül egyedinek kell lennie.
| Forrás | Válasszon **Eseményközpont** egy Eseményközpontba eseményforrás létrehozásához.
| Előfizetés-azonosító | Az előfizetés, amelyben az eseményközpont létre lett hozva.
| Erőforráscsoport | Az előfizetés, amelyben az eseményközpont létre lett hozva.
| Service bus-névtér | A Service Bus-névtér az üzenetküldési entitások készletének tárolója. Egy új Eseményközpont létrehozásakor egy Szolgáltatásbusz-névtér is létrejött.
| Eseményközpont neve | Az Eseményközpont neve. Az eseményközpont létrehozásakor meg nevet is adott neki
| Eseményközpont házirend neve | A megosztott elérési házirendet, amely az Event Hub konfigurálása lapon is létrehozható. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. A megosztott hozzáférési házirend a eseményforrás *kell* rendelkezik **olvasási** engedélyek.
| Event hub házirend kulcs | A Service Bus-névtér való hozzáférés hitelesítéséhez használt megosztott hozzáférési kulcs. Ide írja be az elsődleges vagy másodlagos kulcsot.
| Event hub fogyasztói csoportot | A rendszer kiolvassa az eseményeket az eseményközpontból fogyasztói csoportot. Ajánlott az eseményforrás dedikált fogyasztói csoportot használja.

## <a name="next-steps"></a>Következő lépések

1. Egy adat-hozzáférési házirend hozzá a környezethez [Define adat-hozzáférési házirendek](time-series-insights-data-access.md)
1. A környezet az elérni a [idő adatsorozat Insights portálon találja meg](https://insights.timeseries.azure.com)