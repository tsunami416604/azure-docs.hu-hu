---
title: "Figyelés és az Azure Security Center biztonsági események feldolgozását |} Microsoft Docs"
description: "Ismerje meg, hogyan használhatja a Security Center események irányítópult biztonsági eseményeket az Azure virtuális gépek és az-Azure számítógépek."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: terrylan
ms.openlocfilehash: 367067874b167268bd690a9e0b55412e92e08122
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Figyelés és az Azure Security Center biztonsági események feldolgozása
Az események irányítópult áttekintést összegyűjtött idő és a figyelmet a jelentősebb események figyelmet igénylő biztonsági események száma.  

> [!NOTE]
> Ez a funkció használatához a munkaterület kell Naplóelemzési 2-es verzióját futtató és a Security Center Standard csomagra. Tekintse meg a Security Center [árképzést ismertető oldalra](security-center-pricing.md) további információt a Standard csomagra.
>
>

## <a name="what-is-a-security-event"></a>Mi az, hogy a biztonsági esemény?
A Security Center által használt különböző biztonsági gyűjthet a Microsoft Monitoring Agent a kapcsolódó konfigurációk és események a gépekről és a munkaterületek tárolja ezeket az eseményeket. Az ilyen adatok többek között: operációs rendszer naplóit (Windows eseménynaplóiban keresse meg), futó folyamatok, és a Security Center integrált biztonsági megoldások származó események. A Microsoft Monitoring Agent az összeomlási memóriaképeket is átmásolja a munkaterületeire.

## <a name="events-processed-dashboard"></a>A feldolgozott események irányítópult
Elérheti a **események** irányítópult a Security Center fő menüből vagy a Security Center **áttekintése** panelen.  

![A feldolgozott események irányítópult][1]

A **események** a csempén **Security Center** események összekapcsolását Security Center az Azure virtuális gépek és az-Azure számítógépek számát jeleníti meg.

A **események irányítópult** a feldolgozott események túlóra és események áttekintése.

 ![Irányítópult][2]

 Az irányítópult felső részén trendeket a múlt héten feldolgozott összes eseményt. Az alsó fele az irányítópult sorolja fel a figyelmet a jelentősebb és típus szerint minden események:

 - **Fontos események** közé tartozik, amely a Security Center esemény lekérdezések és hoz létre, és adja hozzá az esemény lekérdezések. Az irányítópult is révén gyors képet kaphat a figyelmet a jelentősebb események száma.
 - **Összes esemény típus szerint** a beérkező típusait és az egyes számát jeleníti meg. Eseménytípus példák SecurityEvent, CommonSecurityLog, WindowsFirewall és W3CIISLog.

> [!NOTE]
> Fontos események [webalkalmazás-alapkonfiguráció értékelése](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). A webes alapkonfiguráció-értékelés célja a potenciálisan sebezhető webkiszolgáló-beállítások megkeresése.

## <a name="view-processed-event-details"></a>Feldolgozott esemény részleteinek megtekintéséhez
1. Az a **Security Center** fő menüjében válassza **események**.
2. A **események irányítópult** munkaterület választó hibaoldallal nyílhat meg. Ha csak egy munkaterületen, a munkaterület választó nem jelenik meg. Ha egynél több munkaterületen, válassza ki a munkaterületen a feldolgozott esemény részleteinek megtekintéséhez szeretné. Válassza a listából egy munkaterület, ha egynél több munkaterületen.

  ![Munkaterület-lista][3]

3. A **események irányítópult** megnyitja a kijelölt munkaterülethez esemény részleteit jeleníti meg. A figyelmet a jelentősebb és minden események típus szerinti tekintheti meg.  Ebben a példában a jelenleg választott **figyelmet a jelentősebb események**.

  ![Fontos esemény][4]

4. További adatok a munkaterületen jelölje ki az esemény is kereshet. Ebben a példában a jelenleg választott **SecurityEvent**.

  ![Egy esemény típusának kiválasztása][5]

5. **Naplófájl-keresési** nyílik meg az esemény típusát a további részletek.

  ![Naplókeresés][6]

## <a name="add-a-notable-event"></a>Vegye fel a figyelmet a jelentősebb esemény
A Security Center out-of-az-box figyelmet a jelentősebb események biztosít. Fontos események alapján saját lekérdezés használatával adhat hozzá a [Log Analytics lekérdezési nyelv](../log-analytics/log-analytics-search-reference.md). Még visszatérünk a **események irányítópult** a figyelmet a jelentősebb esemény felvételéhez.

1. Válassza ki **vegye fel a figyelmet a jelentősebb esemény**.

  ![Vegye fel a figyelmet a jelentősebb esemény][7]

2. **Vegye fel a figyelmet a jelentősebb bekövetkező egyéni esemény** nyílik meg.  A **megjelenített név**, adjon meg egy nevet a figyelmet a jelentősebb esemény. A **keresési lekérdezés**, adja meg a lekérdezés a következő eseményhez.

  ![Adja meg a lekérdezés][8]

4. Kattintson az **OK** gombra.

## <a name="update-your-workspace-for-events-processing"></a>Az események feldolgozását a munkaterület frissítése
A munkaterület kell Naplóelemzési 2-es verzióját futtató és a Security Center Standard csomagra esemény feldolgozása a Security Center használatához. A **események irányítópult** munkaterület választó, amelyek nem felelnek meg ezek a követelmények munkaterületek azonosítja.

![Munkaterület nem felel meg a követelményeknek][9]

Ha a munkaterületet sor:

- Tartalmaz **frissítéséhez szükséges** -frissítenie kell a munkaterület 2-es szolgáltatáshoz
- Tartalmaz **frissítési terv** – frissítenie kell a munkaterületen a Security Center szabványos réteghez
- Üres - munkaterület megfelel a követelményeknek, és egy munkaterület kiválasztása elérheti az irányítópultot

> [!NOTE]
> A **események irányítópult**, a **események** oszlopban látható, hogy az egyes munkaterületeken események mennyisége.  Ebben az oszlopban nem üres egyes munkaterületek esetén, mert a Security Center ingyenes szint alkalmazott munkaterületet. Ingyenes szint alatt a Security Center eseményeket gyűjt, de az események nincsenek mentve a Naplóelemzési, és nem érhetők el az irányítópulton.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>A 2-es verzióját a Naplóelemzési munkaterület frissítése
1. A munkaterület kiválasztása, amely **FRISSÍTÉST IGÉNYEL**.
2. **Frissítés keresés** nyílik meg. Válassza ki **frissítés most**.

  ![Frissítés most][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Váltson Standard csomagra biztonsági központ
1. Válassza ki a munkaterület **frissítési terv**.
2. **Események irányítópult** nyílik meg. Válassza ki **próbálja események irányítópult**.

  ![Próbálja meg az irányítópult][11]

3. A **speciális biztonsági bevezetési**, válassza ki a frissíteni kívánt munkaterületre.
4. A **árazás**, jelölje be **szabványos**.
5. Kattintson a **Mentés** gombra.

  ![Váltson Standard csomagra][12]

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használható a Security Center esemény irányítópult. További információ az irányítópulton működése, és a saját esemény lekérdezéseket írhat lásd:

- [Mi az a Log Analytics?](../log-analytics/log-analytics-overview.md) – A Naplóelemzési áttekintése
- [Understanding napló keres a Naplóelemzési](../log-analytics/log-analytics-log-search-new.md) - ismerteti napló keresés használata a Naplóelemzési és a naplófájl-keresési létrehozása előtt kell értelmezni fogalmat
- [A Naplóelemzési hivatkozás keresése](../log-analytics/log-analytics-search-reference.md) – saját esemény lekérdezést a lekérdezési nyelv a naplóban írásának ismertetése

Security Centerrel kapcsolatos további tudnivalókért lásd:

- [A Security Center áttekintése](security-center-intro.md) – Describes Security Center főbb képességek

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
