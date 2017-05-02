---
title: "Adathozzáférési házirendek az Azure Time Series Insightsban | Microsoft Docs"
description: "Az oktatóanyagból megismerheti az adathozzáférési házirendek kezelésének módját a Time Series Insightsban"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 5e9ee102c0375c8c1ac0e0ff6bc93989115b7cfa
ms.lasthandoff: 04/25/2017

---

# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Adathozzáférés biztosítása egy Time Series Insights-környezethez az Azure Portal segítségével

A Time Series Insights-környezetek két különböző típusú hozzáférési házirenddel rendelkeznek:

* Felügyeleti hozzáférési házirendek
* Adathozzáférési házirendek

A házirendek mindkét típusa különféle engedélyeket biztosít az Azure Active Directory rendszerbiztonsági tagjai (felhasználók és alkalmazások) számára egy adott környezetre vonatkozóan. A rendszerbiztonsági tagoknak (felhasználóknak és alkalmazásoknak) a környezetet tartalmazó előfizetéshez társított Active Directoryhoz (vagy „Azure-bérlőhöz”) kell tartozniuk.

A felügyeleti hozzáférési házirendek a környezet konfigurálásához kapcsolódó engedélyeket biztosítanak, például:
*    a környezet létrehozása vagy törlése, eseményforrások, referencia-adatkészletek, és
*    az adathozzáférési házirendek felügyelete.

Az adathozzáférési házirendek a következőkhöz biztosítanak engedélyeket: adatlekérdezések kiadása, referenciaadatok módosítása a környezetben, valamint a környezethez társított mentett megosztott lekérdezések és perspektívák.

A házirendek két típusa lehetővé teszi a környezet felügyeletéhez történő hozzáférés és a környezetben található adatokhoz való hozzáférés teljes mértékű szétválasztását. Például beállítható egy olyan környezet, amely esetében a környezet tulajdonosa/létrehozója el van távolítva az adathozzáférésből. Emellett megadható, hogy azok a felhasználók és szolgáltatások, amelyek olvashatják a környezet adatait, ne kapjanak hozzáférést a környezet konfigurációjához.

A következő lépések bemutatják, hogyan biztosítható adathozzáférés egy felhasználó rendszerbiztonsági tag számára:

1.    Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2.    Az Azure Portal bal oldali menüjében kattintson a „Minden erőforrás” lehetőségre.
3.    Válassza ki az Azure Time Series Insights-környezetet.

  ![A Time Series Insights-forrás felügyelete – környezet](media/data-access/getstarted-grant-data-access1.png)

4.    Válassza az „Adatsík-hozzáférés” lehetőséget, majd kattintson a „Hozzáadás” gombra.

  ![A Time Series Insights-forrás felügyelete – hozzáadás](media/data-access/getstarted-grant-data-access2.png)

5.    Kattintson a „Felhasználó kiválasztása” gombra.
6.    Keresse meg és válassza ki a felhasználót e-mail-cím alapján.
7.    Kattintson a „Kiválasztás” gombra a „Felhasználó kiválasztása” panelen.

  ![A Time Series Insights-forrás felügyelete – felhasználó kiválasztása](media/data-access/getstarted-grant-data-access3.png)

8.    Kattintson a „Szerepkör kiválasztása” gombra.
9.    Válassza a „Közreműködő” lehetőséget, ha engedélyezni szeretné a felhasználó számára, hogy módosítsa a referenciaadatokat és megoszthassa a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival. Egyéb esetben válassza az „Olvasó” lehetőséget. Ekkor a felhasználó lekérdezheti a környezet adatait, és személyes (nem megosztott) lekérdezéseket menthet a környezetben.
10.    Kattintson az „OK” gombra a „Szerepkör kiválasztása” panelen.

  ![A Time Series Insights-forrás felügyelete – szerepkör kiválasztása](media/data-access/getstarted-grant-data-access4.png)

11.    Kattintson az „OK” gombra a „Felhasználói szerepkör kiválasztása” panelen.
12.    A következőnek kell megjelennie:

  ![A Time Series Insights-forrás felügyelete – eredmények](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Következő lépések

* [Eseményforrás létrehozása](time-series-insights-add-event-source.md)
* [Események küldése](time-series-insights-send-events.md) az eseményforrásnak
* A környezet megtekintése a [Time Series Insights portálon](https://insights.timeseries.azure.com)

