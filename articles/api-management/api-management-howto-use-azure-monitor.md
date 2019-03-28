---
title: A közzétett API-k monitorozása az Azure API Management szolgáltatásban | Microsoft Docs
description: Az API-k Azure API Management szolgáltatásban való monitorozásához kövesse az oktatóanyag lépéseit.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 7f2fe6fc3ba3ae515d372fb5a794e46897bad115
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517946"
---
# <a name="monitor-published-apis"></a>A közzétett API-k monitorozása

Az Azure Monitorral az egyes Azure-erőforrásoktól az Azure-ba érkező metrikákat vagy naplókat jelenítheti meg, kérdezheti le, irányíthatja át, archiválhatja, illetve különböző műveleteket is végezhet velük.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tevékenységnaplók megtekintése
> * Diagnosztikai naplók megtekintése
> * Az API-k mérőszámainak megtekintése 
> * Riasztási szabály beállítása a jogosulatlan API-hívások esetére

A következő videó bemutatja, hogyan monitorozhatja az API Managementet az Azure Monitor használatával. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Hajtsa végre a következő rövid útmutatót: [Az Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).
+ Ezenkívül hajtsa végre a következő oktatóanyaggal: [Az első API importálása és közzététele](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Az API-k metrikáinak megtekintése

Az API Management percenként biztosít mérőszámokat, így közel valós idejű képet ad az API-k állapotáról. Az alábbiakban néhány rendelkezésre álló mérőszámról adunk összefoglaló tájékoztatást:

* Kapacitás (előzetes verzió): az APIM-szolgáltatások magasabb/alacsonyabb verzióra váltásával kapcsolatos döntések meghozatalát segíti elő. A mérőszám percentként keletkezik, és az átjáró a jelentés pillanatában érvényes kapacitását tükrözi. A mérőszám értéke a 0–100 tartományban mozog, és az érték számítása az átjáró erőforrásai, például a processzor és a memória kihasználtsága alapján történik.
* Összes átjárókérés: az API-lekérdezések száma az adott időszakban. 
* Sikeres átjárókérések: a sikert jelző HTTP-válaszkódot kapott API-kérések száma, beleértve a 304-es, a 307-es, valamint a 301-nél alacsonyabb (például 200-as) válaszkódokat.
* Sikertelen átjárókérések: a hibát jelző HTTP-válaszkódot (a 400-as, valamint az 500-nál magasabb válaszkódokat is beleértve) kapott API-kérések száma.
* Jogosulatlan átjárókérések: a 401-es, 403-as és 429-es HTTP-válaszkódot kapott API-kérések száma.
* Egyéb átjárókérések: az előző kategóriákba nem tartozó (például 418-as) HTTP-válaszkódot kapott API-kérések száma.

![metrikadiagram](./media/api-management-azure-monitor/apim-monitor-metrics.png)

A mérőszámok elérése:

1. Válassza a lap alján lévő menü **Metrika** elemét.

    ![metrics](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. A legördülő listából válassza ki a megtekinteni kívánt mérőszámokat. Például: **Sikeres átjárókérések**. További mérőszámokat is hozzáadhat a diagramhoz.
3. A diagram a sikeres API-hívások teljes számát mutatja.

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Riasztási szabály beállítása jogosulatlan hívások esetére

A mérőszámok és tevékenységnaplók alapján beállíthatja, hogy milyen riasztásokat szeretne kapni. Az Azure Monitor segítségével konfigurálhat riasztásokat, amelyek aktiválása esetén a követező műveletek végrehajtására kerülhet sor:

* E-mail-értesítés küldése
* Webhook meghívása
* Egy Azure Logic App-alkalmazás meghívása

A riasztások konfigurálása:

1. Válassza a lap alján lévő menüsáv **Riasztások (klasszikus)** elemét.

    ![riasztások](./media/api-management-azure-monitor/api-management-alert-rules-blade.png)

2. Válassza a **Metrikariasztás hozzáadása** lehetőséget.
3. Adja meg a riasztás **Nevét**.
4. A monitorozni kívánt mérőszámként válassza a **Jogosulatlan átjárókérések** mérőszámot.
5. Jelölje be az **E-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak** beállítást.
6. Kattintson az **OK** gombra.
7. Próbálja API-kulcs nélkül meghívni a Conference API-t. Az adott API Management-szolgáltatás tulajdonosaként egy figyelmeztető e-mailt kap. 

    > [!TIP]
    > A riasztási szabály egy webhookot vagy egy Azure Logic Apps-alkalmazást is meghívhat az aktiválásakor.

    ![riasztás-beállítása](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="activity-logs"></a>Tevékenységnaplók

A tevékenységnaplók betekintést engednek az API Management-szolgáltatásokban végrehajtott műveletekbe. A tevékenységnaplók segítségével az API Management-szolgáltatásokban végrehajtott írási műveletek (PUT, POST, DELETE) kapcsán megállapíthatja, hogy a „ki, mit és mikor” hajtott végre.

> [!NOTE]
> A tevékenységnaplók az olvasási (GET) műveleteket, illetve az Azure Portalon vagy az eredeti felügyeleti API-k használatával végzett műveleteket nem tartalmazzák.

A tevékenységnaplók az API Management szolgáltatásban, az összes Azure-erőforrás naplói pedig az Azure Monitorban érhetők el. 

![tevékenységnaplók](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

A tevékenységnaplók megtekintése:

1. Válassza ki az APIM-szolgáltatáspéldányt.
2. Kattintson a **Tevékenységnapló** gombra.

    ![tevékenységnapló](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Válassza ki a kívánt szűrési hatókört, és kattintson az **Alkalmaz** elemre.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

A diagnosztikai naplók rengeteg információt tartalmaznak a műveletekkel és a hibákkal kapcsolatban, amelyek felülvizsgálati és hibaelhárítási célból egyaránt fontosak lehetnek. A diagnosztikai naplók különböznek a tevékenységnaplóktól. A tevékenységnaplók az Azure-erőforrásokon végrehajtott műveletekkel kapcsolatos információkat tartalmaznak. A diagnosztikai naplókban az erőforrás által végrehajtott műveletekkel kapcsolatos információk találhatók meg.

Diagnosztikai naplók konfigurálása:

1. Válassza ki az APIM-szolgáltatáspéldányt.
2. Kattintson a **Diagnosztikai beállítások** elemre.

    ![diagnosztikai naplók](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Kattintson a **Diagnosztika bekapcsolása** elemre. Diagnosztikai naplókat és mérőszámokat tárfiókba archiválhatja, azok streamelésére az Eseményközpontok felé, vagy küldhet nekik az Azure Monitor naplóira. 

Az API Management jelenleg különálló API-kérelmekről kínál óránként kötegelt diagnosztikai naplókat, amelyek bejegyzései a következő mintát követik:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Tulajdonság  | Típus | Leírás |
| ------------- | ------------- | ------------- |
| isRequestSuccess | logikai | Akkor igaz, ha a befejezett HTTP-kérelem válaszának állapotkódja a 2xx vagy 3xx tartományon belülre esik. |
| time | dátum-idő | Az átjárótól érkező HTTP-kérelem megérkezésének időbélyegzője |
| operationName | sztring | A „Microsoft.ApiManagement/GatewayLogs” állandó érték |
| category | sztring | A „GatewayLogs” állandó érték |
| durationMs | egész szám | A kérelem átjáróhoz való megérkezése és a teljes válasz elküldése között eltelt ezredmásodpercek száma |
| callerIpAddress | sztring | Az átjáró közvetlen hívójának IP-címe (közvetítő is lehet) |
| correlationId | sztring | Az API Management által hozzárendelt HTTP-kérelem egyedi azonosítója |
| location | sztring | Az Azure-régió neve, ahol a kérelmet feldolgozó átjáró található |
| httpStatusCodeCategory | sztring | Http-válasz állapotkódjának kategóriája: Sikeres (301 vagy kevesebb 304 vagy 307), jogosulatlan (401, 403, 429), hibás (400, 500 és 600 között), egyéb |
| resourceId | sztring | Id of the API Management resource /SUBSCRIPTIONS/\<subscription>/RESOURCEGROUPS/\<resource-group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/\<name> |
| properties | objektum | Az aktuális kérelem tulajdonságai |
| method | sztring | A bejövő kérelem HTTP-metódusa |
| url | sztring | A bejövő kérelem URL-címe |
| clientProtocol | sztring | A bejövő kérelem HTTP-protokolljának verziója |
| responseCode | egész szám | Az ügyfélnek küldött HTTP-válasz állapotkódja |
| backendMethod | sztring | A háttérrendszernek küldött kérelem HTTP-metódusa |
| backendUrl | sztring | A háttérrendszernek küldött kérelem URL-címe |
| backendResponseCode | egész szám | A háttérrendszertől érkezett HTTP-válasz kódja |
| backendProtocol | sztring | A háttérrendszernek küldött kérelem HTTP-protokolljának verziója | 
| requestSize | egész szám | A kérelem feldolgozása során az ügyféltől érkezett bájtok száma | 
| responseSize | egész szám | A kérelem feldolgozása során az ügyfélnek küldött bájtok száma | 
| cache | sztring | Az API Management-gyorsítótár kérelemfeldolgozásban való részvételének állapota (pl. találat, tévesztés, nincs) | 
| cacheTime | egész szám | Az API Management-gyorsítótár I/O-folyamatával töltött teljes idő ezredmásodpercben (csatlakozás, bájtok küldése és fogadása) | 
| backendTime | egész szám | A háttérrendszer I/O-folyamatával töltött teljes idő ezredmásodpercben (csatlakozás, bájtok küldése és fogadása) | 
| clientTime | egész szám | Az ügyfél I/O-folyamatával töltött teljes idő ezredmásodpercben (csatlakozás, bájtok küldése és fogadása) | 
| apiId | sztring | Az aktuális kérelem API-entitásazonosítója | 
| operationId | sztring | Az aktuális kérelem műveleti entitásának azonosítója | 
| productId | sztring | Az aktuális kérelem termékentitásának azonosítója | 
| userId | sztring | Az aktuális kérelem felhasználói entitásának azonosítója | 
| apimSubscriptionId | sztring | Az aktuális kérelem előfizetési entitásának azonosítója | 
| backendId | sztring | Az aktuális kérelem háttérentitásának azonosítója | 
| LastError | objektum | A legutóbbi kérelemfeldolgozási hiba | 
| elapsed | egész szám | A kérelem átjáróhoz való megérkezése és a hiba felbukkanása között eltelt ezredmásodpercek száma | 
| source | sztring | A hibát okozó házirend vagy belső feldolgozáskezelő neve | 
| scope | sztring | A hibát okozó házirendet tartalmazó szabályzatdokumentum hatóköre | 
| section | sztring | A hibát okozó szabályzatot tartalmazó szabályzatdokumentum szakasza | 
| reason | sztring | A hiba oka | 
| message | sztring | Hibaüzenet | 

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
