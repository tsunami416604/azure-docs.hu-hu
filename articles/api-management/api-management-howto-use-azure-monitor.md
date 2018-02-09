---
title: "A közzétett API-k monitorozása az Azure API Management szolgáltatásban | Microsoft Docs"
description: "Az API-k Azure API Management szolgáltatásban való monitorozásához kövesse az oktatóanyag lépéseit."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: db1ed08c4d4c9e9abd525ec13f5511da82ee1fe4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="monitor-published-apis"></a>A közzétett API-k monitorozása

Az Azure Monitor Azure-szolgáltatás használatával egyetlen eszközön keresztül monitorozhatja Azure-erőforrásait. Az Azure Monitorral az egyes Azure-erőforrásoktól, például az API Managementtől az Azure-ba érkező mérőszámokat és naplókat jelenítheti meg, kérdezheti le, irányíthatja át, archiválhatja, illetve különböző műveleteket is végezhet velük. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tevékenységnaplók megtekintése
> * Diagnosztikai naplók megtekintése
> * Az API-k mérőszámainak megtekintése 
> * Riasztási szabály beállítása a jogosulatlan API-hívások esetére

A következő videó bemutatja, hogyan monitorozhatja az API Managementet az Azure Monitor használatával. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>Előfeltételek

+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="diagnostic-logs"></a>Tevékenységnaplók megtekintése

A tevékenységnaplók betekintést engednek az API Management-szolgáltatásokban végrehajtott műveletekbe. A tevékenységnaplók segítségével az API Management-szolgáltatásokban végrehajtott írási műveletek (PUT, POST, DELETE) kapcsán megállapíthatja, hogy a „ki, mit és mikor” hajtott végre. 

> [!NOTE]
> A tevékenységnaplók az olvasási műveleteket (GET), illetve a klasszikus közzétételi portálon vagy az eredeti felügyeleti API-k használatával végzett műveleteket nem tartalmazzák.

A tevékenységnaplók az API Management szolgáltatásban, az összes Azure-erőforrás naplói pedig az Azure Monitorban érhetők el. 

A tevékenységnaplók megtekintése:

1. Válassza ki az APIM-szolgáltatáspéldányt.
2. Kattintson a **Tevékenységnapló** gombra.

## <a name="view-diagnostic-logs"></a>Diagnosztikai naplók megtekintése

A diagnosztikai naplók rengeteg információt tartalmaznak a műveletekkel és a hibákkal kapcsolatban, amelyek felülvizsgálati és hibaelhárítási célból egyaránt fontosak lehetnek. A diagnosztikai naplók különböznek a tevékenységnaplóktól. A tevékenységnaplók az Azure-erőforrásokon végrehajtott műveletekkel kapcsolatos információkat tartalmaznak. A diagnosztikai naplókban az erőforrás által végrehajtott műveletekkel kapcsolatos információk találhatók meg.

A diagnosztikai naplók elérése:

1. Válassza ki az APIM-szolgáltatáspéldányt.
2. Kattintson a **Diagnosztikai napló** gombra.

## <a name="view-metrics-of-your-apis"></a>Az API-k metrikáinak megtekintése

Az API Management percenként biztosít mérőszámokat, így közel valós idejű képet ad az API-k állapotáról. Az alábbiakban néhány rendelkezésre álló mérőszámról adunk összefoglaló tájékoztatást:

* Kapacitás (előzetes verzió): az APIM-szolgáltatások magasabb/alacsonyabb verzióra váltásával kapcsolatos döntések meghozatalát segíti elő. A mérőszám percentként keletkezik, és az átjáró a jelentés pillanatában érvényes kapacitását tükrözi. A mérőszám értéke a 0–100 tartományban mozog, és az átjáró erőforrásainak, például a processzornak és a memóriának a kihasználtsága alapján számítódik ki.
* Összes átjárókérés: az API-lekérdezések száma az adott időszakban. 
* Sikeres átjárókérések: a sikert jelző HTTP-válaszkódot kapott API-kérések száma, beleértve a 304-es, a 307-es, valamint a 301-nél alacsonyabb (például 200-as) válaszkódokat. 
* Sikertelen átjárókérések: a hibát jelző HTTP-válaszkódot kapott API-kérések száma, beleértve a 400-as, valamint az 500-nál magasabb válaszkódokat.
* Jogosulatlan átjárókérések: a 401-es, 403-as és 429-es HTTP-válaszkódot kapott API-kérések száma. 
* Egyéb átjárókérések: az előző kategóriákba nem tartozó (például 418-as) HTTP-válaszkódot kapott API-kérések száma.

A mérőszámok elérése:

1. Válassza a lap alján lévő menü **Metrika** elemét.
2. A legördülő listából válassza ki a megtekinteni kívánt mérőszámokat (több mérőszámot is hozzáadhat). 
    
    Például válassza az **Összes átjárókérés** és a **Sikertelen átjárókérések** mérőszámot az elérhető mérőszámok listájából.
3. A diagram az API-hívások teljes számát mutatja, és a sikertelen API-hívások számát is megjeleníti. 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Riasztási szabály beállítása jogosulatlan hívások esetére

A mérőszámok és tevékenységnaplók alapján beállíthatja, hogy milyen riasztásokat szeretne kapni. Az Azure Monitor segítségével konfigurálhat riasztásokat, amelyek aktiválása esetén a követező műveletek végrehajtására kerülhet sor:

* E-mail-értesítés küldése
* Webhook meghívása
* Egy Azure Logic App-alkalmazás meghívása

A riasztások konfigurálása:

1. Válassza a lap alján lévő menü **Riasztási szabályok** elemét.
2. Válassza a **Metrikariasztás hozzáadása** lehetőséget.
3. Adja meg a riasztás **Nevét**.
4. A monitorozni kívánt mérőszámként válassza a **Jogosulatlan átjárókérések** mérőszámot.
5. Jelölje be az **E-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak** beállítást.
6. Kattintson az **OK** gombra.
7. Próbálja API-kulcs nélkül meghívni a Conference API-t. Az adott API Management-szolgáltatás tulajdonosaként egy figyelmeztető e-mailt kap. 

    > [!TIP]
    > A riasztási szabály egy webhookot vagy egy Azure Logic Apps-alkalmazást is meghívhat az aktiválásakor.

    ![riasztás-beállítása](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Tevékenységnaplók megtekintése
> * Diagnosztikai naplók megtekintése
> * Az API-k mérőszámainak megtekintése 
> * Riasztási szabály beállítása a jogosulatlan API-hívások esetére

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Hívások nyomon követése](api-management-howto-api-inspector.md)
