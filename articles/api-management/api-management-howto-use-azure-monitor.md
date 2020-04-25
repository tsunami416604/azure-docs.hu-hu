---
title: A közzétett API-k monitorozása az Azure API Management szolgáltatásban | Microsoft Docs
description: Az API-k Azure API Management szolgáltatásban való monitorozásához kövesse az oktatóanyag lépéseit.
services: api-management
author: vladvino
manager: cfowler
ms.service: api-management
ms.workload: mobile
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: bee93cf84f4beda0684127102942447630219881
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128845"
---
# <a name="monitor-published-apis"></a>A közzétett API-k monitorozása

Az Azure Monitorral az egyes Azure-erőforrásoktól az Azure-ba érkező metrikákat vagy naplókat jelenítheti meg, kérdezheti le, irányíthatja át, archiválhatja, illetve különböző műveleteket is végezhet velük.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tevékenységnaplók megtekintése
> * Erőforrás-naplók megtekintése
> * Az API-k mérőszámainak megtekintése 
> * Riasztási szabály beállítása a jogosulatlan API-hívások esetére

A következő videó bemutatja, hogyan monitorozhatja az API Managementet az Azure Monitor használatával. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Az API-k metrikáinak megtekintése

Az API Management percenként biztosít mérőszámokat, így közel valós idejű képet ad az API-k állapotáról. Az alábbiakban néhány rendelkezésre álló mérőszámról adunk összefoglaló tájékoztatást:

* Kapacitás: segít döntéseket hozni a APIM-szolgáltatások frissítésével/lefokozásával kapcsolatban. A mérőszám percentként keletkezik, és az átjáró a jelentés pillanatában érvényes kapacitását tükrözi. A mérőszám értéke a 0–100 tartományban mozog, és az érték számítása az átjáró erőforrásai, például a processzor és a memória kihasználtsága alapján történik.
* Összes átjárókérés: az API-lekérdezések száma az adott időszakban. 
* Sikeres átjárókérések: a sikert jelző HTTP-válaszkódot kapott API-kérések száma, beleértve a 304-es, a 307-es, valamint a 301-nél alacsonyabb (például 200-as) válaszkódokat.
* Sikertelen átjárókérések: a hibát jelző HTTP-válaszkódot (a 400-as, valamint az 500-nál magasabb válaszkódokat is beleértve) kapott API-kérések száma.
* Jogosulatlan átjárókérések: a 401-es, 403-as és 429-es HTTP-válaszkódot kapott API-kérések száma.
* Egyéb átjárókérések: az előző kategóriákba nem tartozó (például 418-as) HTTP-válaszkódot kapott API-kérések száma.

![metrikadiagram](./media/api-management-azure-monitor/apim-monitor-metrics.png)

A mérőszámok elérése:

1. Válassza a lap alján lévő menü **Metrika** elemét.

    ![metrics](./media/api-management-azure-monitor/api-management-metrics-blade.png)

1. A legördülő listából válassza ki a megtekinteni kívánt mérőszámokat. Például: **kérelmek**. 
1. A diagram az API-hívások teljes számát mutatja,
1. A diagram a **kérelmek** metrikájának méretei alapján szűrhető. Kattintson például a **szűrő hozzáadása**lehetőségre, válassza a **háttérbeli válasz kódja**lehetőséget, és adja meg a 500 értéket. Most a diagramon az API-háttérbeli sikertelen kérelmek száma látható.   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Riasztási szabály beállítása jogosulatlan hívások esetére

A mérőszámok és tevékenységnaplók alapján beállíthatja, hogy milyen riasztásokat szeretne kapni. Az Azure Monitor segítségével konfigurálhat riasztásokat, amelyek aktiválása esetén a követező műveletek végrehajtására kerülhet sor:

* E-mail-értesítés küldése
* Webhook meghívása
* Egy Azure Logic App-alkalmazás meghívása

A riasztások konfigurálása:

1. Válassza ki a **riasztások** elemet az oldal alján található menüsorban.

    ![riasztások](./media/api-management-azure-monitor/alert-menu-item.png)

2. Kattintson egy **új riasztási szabályra** ehhez a riasztáshoz.
3. Kattintson a **feltétel hozzáadása**elemre.
4. Válassza a **metrikák** lehetőséget a jel típusa legördülő listából.
5. Válassza a **jogosulatlan átjáró kérése** lehetőséget a figyelni kívánt jelként.

    ![riasztások](./media/api-management-azure-monitor/signal-type.png)

6. A **jel logikájának konfigurálása** nézetben adja meg azt a küszöbértéket, amelyet követően a riasztást aktiválni kell, majd kattintson a **kész**gombra.

    ![riasztások](./media/api-management-azure-monitor/threshold.png)

7. Válasszon egy meglévő műveleti csoportot, vagy hozzon létre egy újat. Az alábbi példában a rendszer e-mailt küld a rendszergazdáknak. 

    ![riasztások](./media/api-management-azure-monitor/action-details.png)

8. Adja meg a riasztási szabály nevét és leírását, és válassza ki a súlyossági szintet. 
9. Kattintson a **riasztási szabály létrehozása**gombra.
10. Most próbálja meg a konferencia API-t API-kulcs nélkül meghívni. A rendszer elindítja a riasztást, és e-mailt küld a rendszergazdáknak. 

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

## <a name="resource-logs"></a>Erőforrás-naplók

Az erőforrás-naplók részletes információkat biztosítanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. Az erőforrás-naplók különböznek a tevékenység naplóitól. A tevékenységi naplók betekintést nyújtanak az Azure-erőforrásokon végrehajtott műveletekre. Az erőforrás-naplók betekintést nyújtanak az erőforrás által végrehajtott műveletekre.

Az erőforrás-naplók konfigurálása:

1. Válassza ki az APIM-szolgáltatáspéldányt.
2. Kattintson a **Diagnosztikai beállítások** elemre.

    ![erőforrás-naplók](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Kattintson a **Diagnosztika bekapcsolása** elemre. Archiválhatja az erőforrás-naplókat a metrikákkal együtt egy Storage-fiókba, továbbíthatja őket egy Event hub-ba, vagy elküldheti azokat Azure Monitor naplókba. 

A API Management jelenleg az egyes API-kérésekhez tartozó erőforrás-naplókat (óránként kötegelt feldolgozással) biztosítja, és minden egyes bejegyzés a következő sémával rendelkezik:

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
| time | dátum-idő | Időbélyeg, amikor az átjáró megkezdi a kérelem feldolgozását |
| operationName | sztring | A „Microsoft.ApiManagement/GatewayLogs” állandó érték |
| category | sztring | A „GatewayLogs” állandó érték |
| durationMs | egész szám | Az átjárótól kapott kérelemtől számított ezredmásodpercek száma, amíg a rendszer nem küldi el a teljes időpontra vonatkozó választ. Ide tartozik a clienTime, a cacheTime és a backendTime. |
| callerIpAddress | sztring | Az átjáró közvetlen hívójának IP-címe (közvetítő is lehet) |
| correlationId | sztring | Az API Management által hozzárendelt HTTP-kérelem egyedi azonosítója |
| location | sztring | Az Azure-régió neve, ahol a kérelmet feldolgozó átjáró található |
| httpStatusCodeCategory | sztring | A HTTP-válasz állapotkódjának kategóriája: Sikeres (301 vagy alacsonyabb, 304 vagy 307), Jogosulatlan (401, 403, 429), Hibás (400, 500 és 600 között), Egyéb |
| resourceId | sztring | A API Management erőforrás/SUBSCRIPTIONS/\<-előfizetésének azonosítója\<>/resourcegroups/>/Providers/Microsoft. APIMANAGEMENT/szolgáltatás/\<név> |
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
| elapsed | egész szám | Ennyi ezredmásodperc telt el, amikor az átjáró megkapta a kérést, és a hiba bekövetkezésének pillanata | 
| source | sztring | A hibát okozó házirend vagy belső feldolgozáskezelő neve | 
| scope | sztring | A hibát okozó házirendet tartalmazó szabályzatdokumentum hatóköre | 
| section | sztring | A hibát okozó szabályzatot tartalmazó szabályzatdokumentum szakasza | 
| reason | sztring | A hiba oka | 
| message | sztring | Hibaüzenet | 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Tevékenységnaplók megtekintése
> * Erőforrás-naplók megtekintése
> * Az API-k mérőszámainak megtekintése
> * Riasztási szabály beállítása a jogosulatlan API-hívások esetére

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Hívások nyomon követése](api-management-howto-api-inspector.md)
