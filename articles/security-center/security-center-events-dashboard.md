---
title: Figyelés és az Azure Security Centerben a biztonsági események feldolgozása |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a Security Center események irányítópultja az Azure virtuális gépek és a nem Azure-beli számítógépekről a biztonsági események megtekintéséhez.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: rkarlin
ms.openlocfilehash: 5b50e9fea8edd9d73a1d382ae4135edb4e4241ec
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967827"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Figyelés és az Azure Security Centerben a biztonsági események feldolgozása
Az események irányítópultja egy áttekintést nyújt az idő függvényében, és a figyelmet igénylő jelentős események listája összegyűjtött biztonsági események száma.  

> [!NOTE]
> Ez a funkció használatához a munkaterület legyen a Log Analytics 2-es verzió fut, és a Security Center Standard szintű. Tekintse meg a Security Center [díjszabását ismertető lapon](security-center-pricing.md) további információ a Standard szintre.
>
>

## <a name="what-is-a-security-event"></a>Mit jelent a biztonsági eseményt?
A Security Center használja a Microsoft Monitoring Agent a gyűjtése a különböző biztonsági konfigurációkat, és eseményként kapcsolódó gépekről származó, és ezeket az eseményeket az Ön munkaterületeire tárolja. Az ilyen adatok többek között: operációs rendszer naplói (Windows-eseménynaplók), futó folyamatok, és a Security Center integrált biztonsági megoldások eseményeit. A Microsoft Monitoring Agent az összeomlási memóriaképeket is átmásolja a munkaterületeire.

## <a name="events-processed-dashboard"></a>A feldolgozott események irányítópult
Fér hozzá a **események** a Security Center főmenüjébe, vagy a Security Center irányítópultján **áttekintése** panelen.  

![A feldolgozott események irányítópult][1]

A **események** alatt csempe **a Security Center** események tárfiókba kerülnek a Security Center az Azure virtuális gépek és a nem Azure-beli számítógépek számát jeleníti meg.

A **események irányítópultja** az események listája és a feldolgozott események időtúllépések száma nyújt áttekintést.

 ![Irányítópult][2]

 Az irányítópult felső részén a trendek a múlt héten feldolgozott összes eseményt. Az alsó felére, az irányítópult jelentős események és az összes esemény típus szerint sorolja fel:

 - **Jelentős események** például a Security Center által nyújtott esemény lekérdezések és esemény lekérdezések létrehozása, és adja hozzá. Az irányítópulton is száma, az egyes jelentős események gyors betekintést biztosít.
 - **Összes esemény típus szerint** az eseménytípusok, amely fogadja és az egyes számát jeleníti meg. Esemény típusa példák SecurityEvent, CommonSecurityLog, WindowsFirewall és W3CIISLog.

> [!NOTE]
> Jelentős események tartalmazzák [webes alapkonfiguráció-értékelés](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). A webes alapkonfiguráció-értékelés célja a potenciálisan sebezhető webkiszolgáló-beállítások megkeresése.

## <a name="view-processed-event-details"></a>Feldolgozott esemény részleteinek megtekintése
1. Alatt a **a Security Center** fő menüjéből válassza **események**.
2. A **események irányítópultja** munkaterület-választójában jelenítse meg. Ha csak egy munkaterületet, a munkaterület-választó nem jelenik meg. Ha egynél több munkaterületet, akkor kell válasszon ki egy munkaterületet a feldolgozott esemény részleteinek megtekintése. Válasszon egy munkaterületet a listából, ha egynél több munkaterületet.

  ![Munkaterület][3]

3. A **események irányítópultja** megnyitása, amelyen látható a kijelölt munkaterületre vonatkozó esemény részleteit. A jelentős események és az összes esemény típus szerint tekintheti meg.  Ebben a példában a kiválasztott **jelentős események**.

  ![Jelentős esemény][4]

4. A munkaterület az adatok további esemény típusának kiválasztásával lekérdezhető. Ebben a példában a kiválasztott **SecurityEvent**.

  ![Esemény típusának kiválasztása][5]

5. **Naplóbeli keresés** megnyílik a további részleteket az esemény típusa.

  ![Naplókeresés][6]

## <a name="add-a-notable-event"></a>Jelentős esemény hozzáadása
A Security Center-a-beépített jelentős események biztosít. Jelentős események alapján saját lekérdezés használatával adhat hozzá a [Log Analytics lekérdezési nyelvre](../log-analytics/log-analytics-search-reference.md). Még visszatérünk a **események irányítópultja** jelentős esemény hozzáadása.

1. Válassza ki **jelentős esemény hozzáadása**.

  ![Jelentős esemény hozzáadása][7]

2. **Egyéni jelentős esemény hozzáadása** nyílik meg.  A **megjelenítendő név**, adjon meg egy nevet a jelentős eseményt. A **keresési lekérdezés**, írja be a lekérdezést az esemény.

  ![Írja be a lekérdezést][8]

4. Kattintson az **OK** gombra.

## <a name="update-your-workspace-for-events-processing"></a>Események feldolgozása a munkaterület frissítése
A munkaterület legyen a Log Analytics 2-es verzió fut, és a Security Center Standard szintű, az események feldolgozása a Security Center használatával lehet. A **események irányítópultja** munkaterület-választójában jelenítse azonosítja a munkaterületek, amelyek nem felelnek meg a követelménynek.

![Munkaterület nem felel meg a követelményeknek][9]

Ha a munkaterület sor:

- Tartalmaz **frissítés szükséges** -frissítenie kell a munkaterületet a Log Analytics 2-es verzió
- Tartalmaz **csomag frissítése** – munkaterület frissítése a Security Center Standard szintre kell
- Üres – a munkaterület megfelel a követelményeknek, és a egy munkaterület kiválasztásával megnyílik az irányítópulton

> [!NOTE]
> A **események irányítópultja**, a **események** oszlop azt jelzi, hogy az egyes munkaterületeken események.  Ez az oszlop a Security Center ingyenes érvényes az adott munkaterület azért egyes munkaterületek esetén üres. Az ingyenes szint alatt a Security Center összegyűjti az eseményeket, de az események mentése nem történik meg a Log Analyticsben, és nem érhetők el az irányítópulton.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Munkaterület frissítése a Log Analytics 2-es verzió
1. Válasszon ki egy munkaterületet, amely **FRISSÍTÉST IGÉNYEL**.
2. **Keresés a frissítés** nyílik meg. Válassza ki **Frissítsen most**.

  ![Frissítsen most][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>A Security Center Standard csomagra
1. Válasszon ki egy munkaterületet a **csomag frissítése**.
2. **Események irányítópultja** nyílik meg. Válassza ki **próbálja események irányítópultja**.

  ![Irányítópult kipróbálása][11]

3. A **speciális biztonsági megoldás előkészítése**, válassza ki a frissíteni kívánt munkaterületet.
4. A **díjszabási**válassza **Standard**.
5. Kattintson a **Mentés** gombra.

  ![Standard csomagra][12]

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használhatja a Security Center esemény irányítópultján. Az irányítópult működésével kapcsolatos további, és a saját esemény lekérdezéseket írni, lásd:

- [Mi az a Log Analytics?](../log-analytics/log-analytics-overview.md) – Áttekintés a Log Analytics
- [Naplókereséseit ismertető a Log Analytics](../log-analytics/log-analytics-log-search-new.md) – ismerteti a naplókeresések a Log Analytics használata, és megadja a fogalmakat, amelyek előtt létrehozni egy naplókeresést értendő
- [A log Analytics keresési referenciáját bemutató](../log-analytics/log-analytics-search-reference.md) – ismerje meg, hogyan írhat saját esemény-lekérdezéseket a lekérdezési nyelv segítségével napló

A Security Centerrel kapcsolatos további tudnivalókért lásd:

- [A Security Center – áttekintés](security-center-intro.md) – elküldéséhez a Security Center legfontosabb képességei

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
