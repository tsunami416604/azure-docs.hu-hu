---
title: Azure Service Bus metrikák a Azure Monitorban | Microsoft Docs
description: Service Bus entitások figyelése Azure Monitor használatával
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: 6d25bdf6ff8e790466f3a28e3b6043e347d74198
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261859"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Monitor Azure Service Bus metrikák

Service Bus mérőszámok az Azure-előfizetésében lévő erőforrások állapotát adják meg. A metrikai adatok gazdag készletével a Service Bus erőforrásainak általános állapotát, és nem csak a névtér szintjén, hanem az entitás szintjén is mérhető. Ezek a statisztikák fontosak lehetnek, mivel segítenek a Service Bus állapotának figyelésében. Metrikák is segít kiváltó problémák elhárítása anélkül, hogy forduljon az Azure ügyfélszolgálatához.

Az Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [a Microsoft Azure figyelés](../monitoring-and-diagnostics/monitoring-overview.md) és a [lekérése az Azure Monitor-metrikák .NET-tel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon.

> [!IMPORTANT]
> Ha egy entitáshoz 2 órán keresztül nem történt interakció, a metrikák a "0" értéket fogják megmutatni, amíg az entitás már nem tétlen.

## <a name="access-metrics"></a>Access-metrikák

Az Azure Monitor hozzáférés metrikák több módot is biztosít. A mérőszámokat a [Azure Portalon](https://portal.azure.com)keresztül érheti el, vagy használhatja a Azure monitor API-kat (REST és .net) és az elemzési megoldásokat, például a Azure monitor naplókat és Event Hubseket. További információ: [Azure monitor mérőszámai](../azure-monitor/platform/data-platform-metrics.md).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és elérheti az utolsó 30 nap adatait. Ha szeretne egy hosszabb ideig megőrizni az adatokat, úgy archiválhatók metrikák adatai egy Azure Storage-fiókhoz. Ez az érték a Azure Monitor [diagnosztikai beállításaiban](../azure-monitor/platform/diagnostic-settings.md) van konfigurálva.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés mérőszámok portálon

Az idő függvényében segítségével figyelheti a mérőszámokat a [az Azure portal](https://portal.azure.com). Az alábbi példa bemutatja, hogyan sikeres kérések és a fiók szintjén a bejövő kérelmek megtekintése:

![][1]

Metrikák a névtér keresztül közvetlenül is elérheti. Ehhez válassza ki a névteret, majd kattintson a **metrikák**elemre. Az entitás hatókörére szűrt metrikák megjelenítéséhez válassza ki az entitást, majd kattintson a **metrikák**elemre.

![][2]

A dimenziókat támogató metrikák esetében a kívánt dimenzió értékkel kell szűrnie.

## <a name="billing"></a>Számlázás

A Azure Monitor metrikáit és riasztásait riasztási alapon számítjuk fel. Ezeknek a díjaknak elérhetőnek kell lenniük a portálon a riasztás beállításakor és a Mentés előtt. 

A metrikák adatait tartalmazó további megoldásokat közvetlenül ezek a megoldások számlázzák. Ha például számítjuk fel az Azure Storage által archiválja mérőszámok az Azure Storage-fiók. A Log Analytics számlázása akkor is történik, ha a metrikák adatait a speciális elemzéshez Log Analyticsre továbbítják.

Az alábbi mérőszámok segítségével a szolgáltatás állapotának áttekintése. 

> [!NOTE]
> Más néven áthelyezett azt rendszer kivezetése több metrikát. Ehhez szükség lehet, hogy a hivatkozások frissítését. Metrikák, a "elavult" kulcsszó megjelölve a jövőben nem támogatja.

Az összes értékeihez kapnak az Azure Monitor percenként. Az idő részletessége határozza meg az időintervallum, amelynek metrikák jelennek meg. Az összes Service Bus-metrika támogatott időintervalluma 1 perc.

## <a name="request-metrics"></a>Kérelem-metrikák

Számít az adatok és a felügyeleti műveletek kérések száma.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| Bejövő kérések| A Service Bus szolgáltatásnak megadott időszakon keresztül küldött kérések száma. <br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Sikeres kérelmek|A Service Bus szolgáltatásnak adott időszakon keresztül végrehajtott sikeres kérelmek száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Kiszolgálóhibák|A Service Bus szolgáltatás hibája miatt nem feldolgozott kérelmek száma egy adott időszakban.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Felhasználói hibák (lásd a következő szakaszt)|Egy meghatározott időtartamon belül a felhasználói hibák miatt nem feldolgozott kérelmek száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Szabályozott kérelmek|A lekért kérelmek száma, mert túllépte a használatot.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|

### <a name="user-errors"></a>Felhasználói hibák

A következő két típusú hiba van besorolva felhasználói hibaként:

1. Ügyféloldali hibák (HTTP-ben 400-hibák esetén).
2. Az üzenetek feldolgozásakor előforduló hibák, például a [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Üzenet-metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Bejövő üzenetek|A megadott időszakban Service Bus küldött események vagy üzenetek száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Kimenő üzenetek|A Service Bus a megadott időszakban fogadott események vagy üzenetek száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
| Üzenetek| Üzenetsor vagy témakör üzeneteinek száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average <br/> Dimenzió EntityName |
| ActiveMessages| Üzenetsor vagy témakör aktív üzeneteinek száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average <br/> Dimenzió EntityName |
| Kézbesítetlen üzenetek| Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average <br/>Dimenzió EntityName |
| Ütemezett üzenetek| Az üzenetsor/témakör ütemezett üzeneteinek száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average  <br/> Dimenzió EntityName |

## <a name="connection-metrics"></a>Kapcsolati metrika

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|ActiveConnections|A névtér, valamint egy entitására aktív kapcsolatok száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|

## <a name="resource-usage-metrics"></a>Erőforrás-használati metrikák

> [!NOTE] 
> A következő metrikák csak a **prémium** szinttel érhetők el. 

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|CPU-használat/névtér|A névtér százalékos CPU-használata.<br/><br/> Egység Percent <br/> Összesítés típusa: Maximum <br/> Dimenzió EntityName|
|Memória méretének használata névtérben|A névtér százalékos memória-használata.<br/><br/> Egység Percent <br/> Összesítés típusa: Maximum <br/> Dimenzió EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

A Azure Service Bus a Azure Monitor metrikáinak következő dimenzióit támogatja. A metrikák dimenziók hozzáadása nem kötelező. Ha nem adja hozzá a dimenziók, a metrikák a névterek szintjén vannak megadva. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|EntityName| Service Bus támogatja az üzenetküldési entitásokat a névtérben.|

## <a name="set-up-alerts-on-metrics"></a>Riasztások beállítása mérőszámokon

1. A **Service Bus névtér** **mérőszámok** lapján válassza a **riasztások beállítása**lehetőséget. 

    ![Metrikák lap – riasztások beállítása menü](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Jelölje ki a **cél kiválasztása** lehetőséget, majd hajtsa végre a következő műveleteket az **erőforrás kiválasztása** oldalon: 
    1. Válassza ki **Service Bus névtereket** a **szűrés erőforrástípus** mezőben. 
    2. Válassza ki az előfizetését a **szűrés előfizetés alapján** mezőben.
    3. Válassza ki a **Service Bus-névteret** a listából. 
    4. Válassza a **Done** (Kész) lehetőséget. 
    
        ![Névtér kiválasztása](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Válassza a **feltétel hozzáadása**lehetőséget, és hajtsa végre a következő műveleteket a **jel logikai beállítása** lapon:
    1. Válasszon **mérőszámokat** a **jel típusához**. 
    2. Válasszon ki egy jelet. Példa: **Szolgáltatási hibák**. 

        ![Kiszolgálói hibák kiválasztása](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Válasszon **nagyobbat** a **feltételnél**.
    2. Válassza az **összeg** lehetőséget az **idő összesítéséhez**. 
    3. Adja meg az **5** értéket a **küszöbértékhez**. 
    4. Válassza a **Done** (Kész) lehetőséget.    

        ![Feltétel meghatározása](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. A **szabály létrehozása** lapon bontsa ki a **riasztás részleteinek meghatározása**elemet, majd hajtsa végre a következő műveleteket:
    1. Adja meg a riasztás **nevét** . 
    2. Adja meg a riasztás **leírását** .
    3. Válassza ki a riasztás **súlyosságát** . 

        ![Riasztás részletei](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. A **szabály létrehozása** lapon bontsa ki **a műveleti csoport definiálása**elemet, válassza az **új műveleti csoport**lehetőséget, majd hajtsa végre a következő műveleteket a **műveleti csoport hozzáadása lapon**. 
    1. Adja meg a műveleti csoport nevét.
    2. Adja meg a műveleti csoport rövid nevét. 
    3. Válassza ki előfizetését. 
    4. Válasszon ki egy erőforráscsoportot. 
    5. Ehhez a bemutatóhoz írja be a következőt: **E-mail küldése** a **művelet neveként**.
    6. Válassza az **e-mail/SMS/leküldés/hang** lehetőséget a **Művelettípus mezőben**. 
    7. Válassza a **Részletek szerkesztése** lehetőséget. 
    8. Az **e-mail/SMS/leküldés/hang** oldalon hajtsa végre a következő műveleteket:
        1. Válassza az **e-mail**lehetőséget. 
        2. Adja meg az **e-mail-címet**. 
        3. Kattintson az **OK** gombra.

            ![Riasztás részletei](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. A **műveleti csoport hozzáadása** lapon kattintson **az OK gombra**. 
1. A **szabály létrehozása** lapon válassza a **riasztási szabály létrehozása**lehetőséget. 

    ![Riasztási szabály létrehozása gomb](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>További lépések

Lásd: [Azure monitor áttekintése](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


