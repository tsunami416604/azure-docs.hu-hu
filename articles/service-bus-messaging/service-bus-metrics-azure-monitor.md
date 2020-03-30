---
title: Az Azure Service Bus metrikák az Azure Monitorban| Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan használhatja az Azure Monitor szolgáltatásbusz-entitások (várólisták, témakörök és előfizetések) figyelésére.
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 227dfaff211eb60c5c2b25b5c76ecc82b6ce3edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240783"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Az Azure Service Bus metrikák az Azure Monitorban

A Service Bus-metrikák az Azure-előfizetés erőforrásainak állapotát biztosítják. A metrikák adatainak gazdag készletével felmérheti a Service Bus-erőforrások általános állapotát, nem csak a névtér szintjén, hanem az entitás szintjén is. Ezek a statisztikák fontosak lehetnek, mivel segítenek a Service Bus állapotának figyelésében. Metrikák is segít elhárítani a kiváltó problémákat anélkül, hogy kapcsolatba lépni az Azure-támogatás.

Az Azure Monitor egységes felhasználói felületeket biztosít a különböző Azure-szolgáltatások figyeléséhez. További információ: [Figyelés a Microsoft Azure-ban](../monitoring-and-diagnostics/monitoring-overview.md) és az [Azure Monitor metrikák lekérése a .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) minta a GitHubon.

> [!IMPORTANT]
> Ha 2 órán keresztül nem volt interakció egy entitással, a metrikák a "0" értéket jelenítik meg értékként, amíg az entitás már nem tétlen.

## <a name="access-metrics"></a>Hozzáférési mutatók

Az Azure Monitor többféle módon érheti el a metrikákat. Metrikák at érhet el az [Azure Portalon](https://portal.azure.com)keresztül, vagy használhatja az Azure Monitor API-k (REST és .NET) és elemzési megoldások, például az Azure Monitor naplók és az Event Hubs. További információ: [Metrics in Azure Monitor.](../azure-monitor/platform/data-platform-metrics.md)

A mérőszámok alapértelmezés szerint engedélyezve vannak, és a legutóbbi 30 napnyi adathoz férhet hozzá. Ha hosszabb ideig kell megőriznie az adatokat, archiválhatja a metrikák adatait egy Azure Storage-fiókba. Ez az érték az Azure Monitor [diagnosztikai beállításaiban](../azure-monitor/platform/diagnostic-settings.md) van konfigurálva.

## <a name="access-metrics-in-the-portal"></a>Hozzáférési mutatók a portálon

Az [Azure Portalon](https://portal.azure.com)idővel figyelheti a metrikákat. A következő példa bemutatja, hogyan tekintheti meg a sikeres kérelmeket és a bejövő kérelmeket a fiók szintjén:

![][1]

A metrikákat közvetlenül is elérheti a névtéren keresztül. Ehhez jelölje ki a névteret, majd kattintson **a Metrikák gombra.** Az entitás hatókörére szűrt metrikák megjelenítéséhez jelölje ki az entitást, majd kattintson a **Metrikák gombra.**

![][2]

A dimenziókat támogató mutatók esetében a kívánt dimenzióértékkel kell szűrnie.

## <a name="billing"></a>Számlázás

Metrikák és riasztások az Azure Monitor on riasztási alapon. Ezeknek a díjaknak elérhetőnek kell lenniük a portálon, amikor a riasztás be van állítva, és mielőtt menti. 

A metrikaadatokat betöltési adatokat betöltési további megoldásokat közvetlenül ezek a megoldások számláznak. Például az Azure Storage által számlázott, ha archiválja metrikák adatok egy Azure Storage-fiók. A Log Analytics akkor is számláz, ha metrikák adatait továbbítja a Log Analytics szolgáltatásba speciális elemzésre.

Az alábbi mutatók áttekintést nyújtanak a szolgáltatás állapotáról. 

> [!NOTE]
> Több mutatót is elavulttal ítunk, mivel azok más néven kerülnek áthelyezésre. Ehhez szükség lehet a hivatkozások frissítésére. Az "elavult" kulcsszóval jelölt mérőszámok nem támogatottak a jövőben.

Az összes metrikaértékek percenként elküldésre kerülnek az Azure Monitornak. Az idő részletessége határozza meg azt az időintervallumot, amelyhez a metrikák értékei megjelennek. A támogatott időintervallum az összes Service Bus metrikák 1 perc.

## <a name="request-metrics"></a>Mérőszámok kérése

Megszámolja az adat- és kezelési műveletek kérelmeinek számát.

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
| Bejövő kérések| A Service Bus szolgáltatáshoz egy adott időszakban benyújtott kérések száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Sikeres kérelmek|A Service Bus szolgáltatáshoz egy adott időszakban végrehajtott sikeres kérelmek száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Kiszolgálói hibák|A Service Bus szolgáltatás egy adott időszakban hiba miatt nem feldolgozott kérelmek száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Felhasználói hibák (lásd a következő alszakaszt)|A megadott időszakban felhasználói hibák miatt fel nem dolgozott kérelmek száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Szabályozott kérelmek|A használat túllépése miatt szabályozott kérelmek száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|

### <a name="user-errors"></a>Felhasználói hibák

A következő két típusú hiba minősül felhasználói hibának:

1. Ügyféloldali hibák (HTTP-ben ez 400 hiba).
2. Az üzenetek feldolgozása közben előforduló hibák, például [a MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Üzenetmérő számok

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
|Bejövő üzenetek|A Service Bus-nak egy adott időszakban küldött események vagy üzenetek száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Kimenő üzenetek|A Service Bus-tól egy adott időszakban fogadott események vagy üzenetek száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
| Üzenetek| A várólistában/témakörben lévő üzenetek száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag <br/> Dimenzió: Entitásnév |
| ActiveMessages| A várólistában/témakörben lévő aktív üzenetek száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag <br/> Dimenzió: Entitásnév |
| Kézbesített üzenetek| Kézbesítetlen levelek száma egy várólistában/témakörben. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag <br/>Dimenzió: Entitásnév |
| Ütemezett üzenetek| A várólistában/témakörben lévő ütemezett üzenetek száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag  <br/> Dimenzió: Entitásnév |

> [!NOTE]
> A következő mutatók értékei az időponthoz való értékek. Előfordulhat, hogy a közvetlenül az adott időpontután felhasznált bejövő üzenetek nem jelennek meg ezekben a mutatókban. 
> - Üzenetek
> - Aktív üzenetek 
> - Kézbesített üzenetek 
> - Ütemezett üzenetek 

## <a name="connection-metrics"></a>Csatlakozási mutatók

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
|ActiveConnections|A névtérés az entitás aktív kapcsolatainak száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|

## <a name="resource-usage-metrics"></a>Erőforrás-használati mutatók

> [!NOTE] 
> A következő metrikák csak a **prémium szinthez** érhetők el. 
> 
> A prémium szintű névtér kimaradásainak figyeléséhez fontos mérőszámok a következők: **névtérenkénti PROCESSZOR-használat** és **névtérenkénti memóriaméret**. [Riasztások beállítása](../azure-monitor/platform/alerts-metric.md) ezekhez a metrikákhoz az Azure Monitor használatával.
> 
> A másik mérőszám, amelyet figyelhet: **szabályozott kérelmek**. Ez nem lehet probléma, bár mindaddig, amíg a névtér marad a memóriában, a CPU, és a közvetített kapcsolatok korlátait. További információ: [Szabályozás az Azure Service Bus prémium csomagjában](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
|Névtérenkénti processzorhasználat|A névtér processzorhasználatának százalékos aránya.<br/><br/> Egység: Százalék <br/> Összesítés típusa: Maximum <br/> Dimenzió: Entitásnév|
|Memóriaméret-használat névtérenként|A névtér memóriahasználatának százalékos aránya.<br/><br/> Egység: Százalék <br/> Összesítés típusa: Maximum <br/> Dimenzió: Entitásnév|

## <a name="metrics-dimensions"></a>Metrikadimenziók

Az Azure Service Bus a következő dimenziókat támogatja az Azure Monitor metrikák. Dimenziók hozzáadása a metrikákhoz nem kötelező. Ha nem ad hozzá dimenziókat, a mérőszámok a névtér szintjén vannak megadva. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|Entitásnév| A Service Bus támogatja a névtér ben lévő üzenetküldési entitásokat.|

## <a name="set-up-alerts-on-metrics"></a>Riasztások beállítása a mérőszámokon

1. A **Service Bus-névtér** lap **Metrikák** lapján válassza a **Riasztások konfigurálása**lehetőséget. 

    ![Metrikák lap – Riasztások konfigurálása menü](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Válassza a **Cél kiválasztása** lehetőséget, és végezze el a következő műveleteket az **Erőforrás kiválasztása** lapon: 
    1. Válassza ki a **Szervizbusz-névterek** lehetőséget a **Szűrő erőforrástípus szerint** mezőhöz. 
    2. Válassza ki az előfizetést a **Szűrés előfizetés szerint** mezőhöz.
    3. Válassza ki a **szolgáltatásbusz névterét** a listából. 
    4. Válassza a **Done** (Kész) lehetőséget. 
    
        ![Névtér kijelölése](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Válassza **a Feltétel hozzáadása**lehetőséget, és tegye a következő műveleteket a **Jellogika konfigurálása** lapon:
    1. Válassza ki **a Mérőszámok at** **Signal type**. 
    2. Jelöljön ki egy jelet. Például: **Szolgáltatási hibák**. 

        ![Kiszolgálóhibák kiválasztása](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Válassza **a Nagyobb, mint** a Feltétel **lehetőséget.**
    2. Válassza az **Összeg lehetőséget** az **időösszesítéshez**. 
    3. Írja be az **5** értéket a **küszöbértékmezőbe.** 
    4. Válassza a **Done** (Kész) lehetőséget.    

        ![Feltétel megadása](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. A **Szabály létrehozása** lapon bontsa ki a **Riasztás részleteinek megadása csomópontot,** és tegye a következő műveleteket:
    1. Adja meg a riasztás **nevét.** 
    2. Adja meg a riasztás **leírását.**
    3. Válassza ki a **riasztás súlyosságát.** 

        ![Riasztás részletei](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. A **Szabály létrehozása** lapon bontsa ki a **Műveletcsoport definiálása lehetőséget,** válassza az **Új műveletcsoport**lehetőséget, és végezze el a következő műveleteket a **Műveletcsoport hozzáadása lapon.** 
    1. Adja meg a műveletcsoport nevét.
    2. Adja meg a műveletcsoport rövid nevét. 
    3. Válassza ki előfizetését. 
    4. Válasszon ki egy erőforráscsoportot. 
    5. A forgatókönyvbe írja be az **E-mail küldése** **műveletnevéhez című**részt.
    6. Válassza **az E-mail/SMS/Push/Voice** for **ACTION TYPE lehetőséget.** 
    7. Válassza a **Részletek szerkesztése** lehetőséget. 
    8. Az **E-mail/SMS/Push/Voice** oldalon tegye a következőket:
        1. Válassza **az E-mail**lehetőséget. 
        2. Írja be az **e-mail címet.** 
        3. Válassza **az OK gombot.**

            ![Riasztás részletei](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. A **Műveletcsoport hozzáadása** lapon válassza az **OK gombot.** 
1. A **Szabály létrehozása** lapon válassza a **Figyelmeztetési szabály létrehozása**lehetőséget. 

    ![Riasztási szabály létrehozása gomb](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>További lépések

Tekintse meg az [Azure Monitor áttekintését.](../monitoring-and-diagnostics/monitoring-overview.md)

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


