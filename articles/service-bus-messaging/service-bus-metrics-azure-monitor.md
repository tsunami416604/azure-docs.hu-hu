---
title: Az Azure Service Bus-metrikák az Azure Monitor (előzetes verzió) |} A Microsoft Docs
description: Azure Monitoring segítségével figyelheti a Service Bus-entitások
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: fda6ca26c39d49c0d16c9933cc0a14f0bfea90a9
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54857114"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Az Azure Service Bus-metrikák az Azure Monitor (előzetes verzió)

Service Bus-metrikák teszik lehetővé az Azure-előfizetésében erőforrások állapotát. Metrikai adatok széles skáláját felmérheti a Service Bus-erőforrások, nem csak a névterek szintjén, hanem az entitások szintjén általános állapotát. A statisztikai lehet fontos, mert ezek segítségével figyelheti a Service Bus állapotát. Metrikák is segít kiváltó problémák elhárítása anélkül, hogy forduljon az Azure ügyfélszolgálatához.

Az Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [a Microsoft Azure figyelés](../monitoring-and-diagnostics/monitoring-overview.md) és a [lekérése az Azure Monitor-metrikák .NET-tel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon.

> [!IMPORTANT]
> Ha nem egy entitás végzett minden művelet 2 órán át lett, a metrikák akkor indul el addig, amíg az entitás már nem üresjárati "0", érték megjelenítése.

## <a name="access-metrics"></a>Access-metrikák

Az Azure Monitor hozzáférés metrikák több módot is biztosít. Mindkét hozzáférési metrikák keresztül is a [az Azure portal](https://portal.azure.com), vagy használja az Azure Monitor API-k (REST és .NET) és az elemzési megoldások, például a Log Analytics és az Event Hubs. További információkért lásd: [figyelési adatokat gyűjtött az Azure Monitor](../azure-monitor/platform/data-collection.md).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és elérheti az utolsó 30 nap adatait. Ha szeretne egy hosszabb ideig megőrizni az adatokat, úgy archiválhatók metrikák adatai egy Azure Storage-fiókhoz. Ez az érték van konfigurálva a [diagnosztikai beállítások](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) az Azure monitorban.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés mérőszámok portálon

Az idő függvényében segítségével figyelheti a mérőszámokat a [az Azure portal](https://portal.azure.com). Az alábbi példa bemutatja, hogyan sikeres kérések és a fiók szintjén a bejövő kérelmek megtekintése:

![][1]

Metrikák a névtér keresztül közvetlenül is elérheti. Ehhez válassza ki a névteret, és kattintson a **metrikák (előnézet)**. Az entitás hatókörének szűrt metrikák megjelenítéséhez válassza ki az entitást, és kattintson a **metrikák (előnézet)**.

![][2]

Dimenziók támogató metrikákhoz a kívánt dimenzió értékkel kell szűrni.

## <a name="billing"></a>Számlázás

Metrikák az Azure Monitor használata az előzetes verzió ingyenes. Azonban használ további megoldásokat, amelyek fogadni a mérőszámadatokat, akkor előfordulhat, hogy után kell díjat ezek a megoldások által. Ha például számítjuk fel az Azure Storage által archiválja mérőszámok az Azure Storage-fiók. Emellett a számlázás a Log Analytics által speciális elemzésekre szolgáló Log Analytics-metrikák adatok streamelése az, ha.

Az alábbi mérőszámok segítségével a szolgáltatás állapotának áttekintése. 

> [!NOTE]
> Más néven áthelyezett azt rendszer kivezetése több metrikát. Ehhez szükség lehet, hogy a hivatkozások frissítését. Metrikák, a "elavult" kulcsszó megjelölve a jövőben nem támogatja.

Az összes értékeihez kapnak az Azure Monitor percenként. Az idő részletessége határozza meg az időintervallum, amelynek metrikák jelennek meg. A Service Bus-metrikák támogatott idő időköz 1 perc.

## <a name="request-metrics"></a>Kérelem-metrikák

Számít az adatok és a felügyeleti műveletek kérések száma.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| A bejövő kérések (előnézet) | Egy meghatározott időtartamon belül a Service Bus szolgáltatás felé irányuló kérelmek száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Sikeres kérések (előnézet)|A sikeres kérelmek száma a Service Bus szolgáltatásnak egy meghatározott időtartamon belül.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Kiszolgálóhibák (előzetes verzió)|Egy meghatározott időtartamon belül a Service Bus szolgáltatás hibája miatt nem feldolgozott kérelmek száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Felhasználói hibák (előzetes verzió – tekintse meg a következő szakasz)|Egy meghatározott időtartamon belül a felhasználói hibák miatt nem feldolgozott kérelmek száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Szabályozott kérelmeinek száma (előzetes verzió)|Szabályozott volt, mert túllépte a használati kérelmek száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|

### <a name="user-errors"></a>Felhasználói hibák

A következő két típusú hibák besorolt felhasználói hibáinak száma:

1. Ügyféloldali hibák (a HTTP 400 hibákat adott).
2. Például üzenetek feldolgozása közben jelentkező hibákról [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Üzenet-metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Bejövő üzenetek (előzetes verzió)|Események vagy adott időszakon belül Service Busnak küldött üzenetek számát.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Kimenő üzenetek (előzetes verzió)|Események vagy egy meghatározott időtartamon belül a Service Bus Beérkezett üzenetek száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
| Üzenetek (előzetes verzió) | Az üzenetsor vagy témakör üzeneteinek száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Átlag <br/> Dimenzió: EntityName |
| ActiveMessages (előzetes verzió) | Az üzenetsor vagy témakör aktív üzeneteinek száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Átlag <br/> Dimenzió: EntityName |

## <a name="connection-metrics"></a>Kapcsolati metrika

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Aktív kapcsolatai (előzetes verzió)|A névtér, valamint egy entitására aktív kapcsolatok száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Kapcsolatok megnyitott (előzetes verzió)|Megnyitott kapcsolatok száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Kapcsolat lezárva (előzetes verzió)|Lezárt kapcsolatok száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName |

## <a name="resource-usage-metrics"></a>Erőforrás-használati metrikák

> [!NOTE] 
> A következő metrikák érhetők el, csak a **prémium** szint. 

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Processzorhasználat névterenként (előzetes verzió)|Százalékos processzorhasználat a névteret.<br/><br/> Szervezeti egység: Százalék <br/> Aggregation Type: Maximum <br/> Dimenzió: EntityName|
|Memória mérete névterenként (előzetes verzió)|A névtér százalékos memóriahasználatát.<br/><br/> Szervezeti egység: Százalék <br/> Aggregation Type: Maximum <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Az Azure Service Bus a következő dimenziókat támogatja a mérőszámok az Azure Monitor. A metrikák dimenziók hozzáadása nem kötelező. Ha nem adja hozzá a dimenziók, a metrikák a névterek szintjén vannak megadva. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|EntityName| A Service Bus a névtérben üzenetküldési entitások támogatja.|

## <a name="set-up-alerts-on-metrics"></a>Állítsa be a riasztások a metrikák

1. Az a **metrikák** lapján a **Service Bus-Namespace** lapon jelölje be **riasztások konfigurálása**. 

    ![Metrikák lapon – a riasztások menüből konfigurálása](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Válassza ki **cél kiválasztása**, és a következő műveleteket hajthatja végre a a **válasszon ki egy erőforrást** oldalon: 
    1. Válassza ki **Service Bus-névterek** számára a **szűrés erőforrástípus szerint** mező. 
    2. Válassza ki az előfizetését a **előfizetés-szűrő** mező.
    3. Válassza ki a **service bus-névtér** a listából. 
    4. Válassza a **Done** (Kész) lehetőséget. 
    
        ![Névtér kiválasztása](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Válassza ki **adja meg a feltételeket**, és a következő műveleteket hajthatja végre a a **jellogika konfigurálása** oldalon:
    1. Válassza ki **metrikák** a **típus jelezze**. 
    2. Válasszon egy olyan jelet. Példa: **Hibák (előzetes verzió) szolgáltatás**. 

        ![Válassza ki a kiszolgáló-hibák](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Válassza ki **nagyobb** a **feltétel**.
    2. Válassza ki **teljes** a **idő összesítése**. 
    3. Adja meg **5** a **küszöbérték**. 
    4. Válassza a **Done** (Kész) lehetőséget.    

        ![Adja meg a feltétel](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. A a **létrehozás szabály** lapon, bontsa ki a **riasztás részleteinek megadása**, és hajtsa végre a következőket:
    1. Adjon meg egy **neve** a riasztás. 
    2. Adjon meg egy **leírás** a riasztás.
    3. Válassza ki **súlyossági** a riasztás. 

        ![Riasztás részletei](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. A a **létrehozás szabály** lapon, bontsa ki a **műveleti csoport megadása**, jelölje be **új műveletcsoport**, és a következő műveleteket hajthatja végre a a **hozzáadása művelet csoport lap**. 
    1. Adjon meg egy nevet a műveletcsoport.
    2. Adja meg a műveletcsoport rövid neve. 
    3. Válassza ki előfizetését. 
    4. Válasszon erőforráscsoportot. 
    5. Adja meg a forgatókönyv **e-mailt** a **MŰVELETNÉV**.
    6. Válassza ki **e-mailek és SMS és leküldéses/Hangvétel** a **MŰVELETTÍPUS**. 
    7. Válassza ki **részleteinek szerkesztése**. 
    8. Az a **e-mailek és SMS és leküldéses/Hangvétel** lapon, tegye a következőket:
        1. Válassza ki **E-mail**. 
        2. Írja be a **e-mail-cím**. 
        3. Kattintson az **OK** gombra.

            ![Riasztás részletei](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Az a **műveleti csoport hozzáadása** lapon jelölje be **OK**. 
1. Az a **létrehozás szabály** lapon jelölje be **riasztási szabály létrehozása**. 

    ![Riasztási szabály gomb létrehozása](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>További lépések

Tekintse meg a [Azure Figyelés áttekintése](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


