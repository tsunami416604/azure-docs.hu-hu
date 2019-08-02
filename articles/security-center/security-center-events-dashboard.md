---
title: Biztonsági események figyelése és feldolgozása a Azure Security Centerban | Microsoft Docs
description: Ismerje meg, hogyan használhatja a Security Center Events irányítópultját az Azure-beli virtuális gépekről és a nem Azure-beli számítógépekről származó biztonsági események megtekintéséhez.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: rkarlin
ms.openlocfilehash: 389aaee621251890cd3f75744a94b9c9b29c5695
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662401"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Biztonsági események figyelése és feldolgozása Azure Security Center
Az események irányítópult áttekintést nyújt az idő múlásával összegyűjtött biztonsági események számáról, valamint a jelentős események listájáról, amelyekhez szükség lehet a beavatkozásra.  

> [!NOTE]
> A biztonsági események irányítópultja a 2019. július 31-én megszűnt. További információ és alternatív szolgáltatások: [Security Center szolgáltatások kivonása (július 2019)](security-center-features-retirement-july2019.md#menu_events).

## <a name="what-is-a-security-event"></a>Mi az a biztonsági esemény?
A Security Center a Microsoft monitoring Agent használatával gyűjti össze a különböző biztonsági jellegű konfigurációkat és eseményeket a gépekről, és ezeket az eseményeket a munkaterülete (ke) ben tárolja. Ilyen adatok például a következők: az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok és a Security Center integrált biztonsági megoldások eseményei. A Microsoft Monitoring Agent az összeomlási memóriaképeket is átmásolja a munkaterületeire.

## <a name="requirements"></a>Követelmények
A szolgáltatás használatához a munkaterületnek Log Analytics 2-es verziójúnak kell lennie, és a Security Center Standard szintűnek kell lennie. A standard szintű [](security-center-pricing.md) csomaggal kapcsolatos további információkért tekintse meg a Security Center díjszabási oldalát.

## <a name="events-processed-dashboard"></a>Feldolgozott események irányítópultja
Az **események** irányítópultot a Security Center főmenüjéből vagy Security Center **Áttekintés** paneljéről érheti el.  

![Feldolgozott események irányítópultja][1]

A **Security Center** alatti **események** csempén az Azure-beli virtuális gépekről és a nem Azure-beli számítógépekről Security Center áramló események száma látható.

Az **események irányítópult** áttekintést nyújt a túlóra feldolgozott eseményeinek számáról és az események listájáról.

 ![Irányítópult][2]

 Az irányítópult felső fele az összes feldolgozott eseményt feldolgozza az elmúlt héten. Az irányítópult alsó fele a jelentős eseményeket és az összes eseményt listázza a következő típus szerint:

 - **Jelentős események** közé tartoznak az Security Center által biztosított és a létrehozott és a hozzájuk kapcsolódó lekérdezések. Az irányítópult gyors áttekintést nyújt az egyes jelentős események számáról.
 - Az **összes esemény típus szerint** a fogadott eseménytípus, valamint az egyes típusok darabszáma látható. Ilyen típusú események: SecurityEvent, CommonSecurityLog, WindowsFirewall és W3CIISLog.

> [!NOTE]
> Jelentős események közé tartozik a webes alapkonfiguráció- [értékelés](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). A webes alapkonfiguráció-értékelés célja a potenciálisan sebezhető webkiszolgáló-beállítások megkeresése.

## <a name="view-processed-event-details"></a>Feldolgozott esemény részleteinek megtekintése
1. A **Security Center** Főmenüben válassza az **események**lehetőséget.
2. Előfordulhat, hogy az **események irányítópult** -munkaterület-választója meg van nyitva. Ha csak egy munkaterülettel rendelkezik, ez a munkaterület-választó nem jelenik meg. Ha több munkaterülettel rendelkezik, ki kell választania egy munkaterületet a feldolgozott esemény részleteinek megtekintéséhez. Ha több munkaterülettel rendelkezik, válasszon ki egy munkaterületet a listából.

   ![Munkaterület-lista][3]

3. Megnyílik az **események irányítópult** , amely a kiválasztott munkaterülethez tartozó esemény részleteit jeleníti meg. A jelentős eseményeket és az összes eseményt típus szerint tekintheti meg.  Ebben a példában a **jelentős eseményeket**jelöltük ki.

   ![Jelentős esemény][4]

4. Az esemény típusának kiválasztásával a munkaterület alatt több adat is lekérdezhető. Ebben a példában a **SecurityEvent**-t választottuk.

   ![Esemény típusának kiválasztása][5]

5. Megnyílik a **naplóbeli keresés** , amely további részleteket mutat be az esemény típusától függően.

   ![Naplókeresés][6]

## <a name="add-a-notable-event"></a>Jelentős esemény hozzáadása
A Security Center beépített jelentős eseményeket biztosít. A [Kusto lekérdezési nyelv](../log-analytics/log-analytics-search-reference.md)használatával jelentős eseményeket adhat hozzá a saját lekérdezése alapján. Egy jelentős esemény felvételéhez visszatérünk az **események irányítópultra** .

1. Válassza a **jelentős esemény hozzáadása**elemet.

   ![Jelentős esemény hozzáadása][7]

2. **Adja hozzá az egyéni jelentős esemény** megnyitását.  A **megjelenítendő név**mezőben adja meg a jelentős esemény nevét. A **keresési lekérdezés**területen adja meg az esemény lekérdezését.

   ![Adja meg a lekérdezést][8]

4. Kattintson az **OK** gombra.

## <a name="update-your-workspace-for-events-processing"></a>Munkaterületek frissítése az események feldolgozásához
A munkaterületnek Log Analytics 2-es verziójúnak kell lennie, és a Security Center Standard csomagjában kell lennie, hogy az események feldolgozását a Security Centerban használja. Az **események irányítópult** -munkaterület választó azonosítja azokat a munkaterületeket, amelyek nem felelnek meg a követelményeknek.

![A munkaterület nem felel meg a követelményeknek][9]

Ha a munkaterület sora:

- A tartalmaz **frissítést igényel** – frissítenie kell a munkaterületet log Analytics 2-es verzióra
- **Frissítési tervet** tartalmaz – frissítenie kell a munkaterületet Security Center Standard szintjére.
- Üres – a munkaterület megfelel a követelményeknek, és kiválasztja a munkaterületet az irányítópulton

> [!NOTE]
> Az események **irányítópult**alatt az **események** oszlop az egyes munkaterületeken lévő események mennyiségét jelzi.  Ez az oszlop egyes munkaterületek esetében üres, mert az adott munkaterületre Security Center ingyenes szintet alkalmaz. Az ingyenes szinten Security Center az eseményeket gyűjti, de az eseményeket nem Azure Monitor naplókba menti, és nem érhetők el az irányítópulton.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Munkaterület frissítése Log Analytics 2. verzióra
1. Válasszon ki egy **frissítést igénylő**munkaterületet.
2. Megnyílik a **Keresés frissítése** . Válassza a **Frissítés most**lehetőséget.

   ![Frissítsen most][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Frissítés Security Center Standard szintre
1. Válasszon ki egy **frissítési tervtel**rendelkező munkaterületet.
2. Megnyílik az **események irányítópult** . Válassza az **események kipróbálása irányítópult**lehetőséget.

   ![Az irányítópult kipróbálása][11]

3. A **speciális biztonság**bevezetése területen válassza ki a frissíteni kívánt munkaterületet.
4. A **díjszabás**területen válassza a **standard**lehetőséget.
5. Kattintson a **Mentés** gombra.

   ![Frissítés a standard szintre][12]

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használhatja a Security Center esemény-irányítópultját. Ha többet szeretne megtudni arról, hogyan működik az irányítópult, és hogyan írhat saját események lekérdezéseit, tekintse meg a következőt:

- [Mi az Azure Monitor naplók?](../log-analytics/log-analytics-overview.md) – Áttekintés Azure Monitor naplókról
- A naplóbeli keresések [ismertetése a Kusto-ben](../log-analytics/log-analytics-log-search-new.md) – leírja, hogyan használja a rendszer a naplóbeli kereséseket Azure monitor naplókon, és olyan fogalmakat biztosít, amelyeket érdemes értelmezni a napló keresése
- [Kusto keresési referenciája](../log-analytics/log-analytics-search-reference.md) – megtudhatja, hogyan írhat saját eseményeket a lekérdezési nyelv használatával a naplóban

A Security Centerról további információt a következő témakörben talál:

- [Security Center áttekintése](security-center-intro.md) – a Security Center főbb képességeinek ismertetése

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
