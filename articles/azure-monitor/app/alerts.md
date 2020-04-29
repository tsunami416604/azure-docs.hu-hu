---
title: Riasztások beállítása az Azure Application Insights
description: Értesítést kaphat a lassú válaszidő, a kivételek és a webalkalmazás más teljesítmény-és használati változásairól.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295085"
---
# <a name="set-alerts-in-application-insights"></a>Riasztások beállítása Application Insightsban

Az [Azure Application Insights][start] riasztást küld a webalkalmazás teljesítmény-és használati metrikáinak változásairól. 

Application Insights figyeli az élő alkalmazást a [különböző platformokon][platforms] , hogy segítsen a teljesítménnyel kapcsolatos problémák diagnosztizálásában és a használati minták értelmezésében.

Több típusú riasztás létezik:

* A [**metrikai riasztások**](../../azure-monitor/platform/alerts-metric-overview.md) jelzik, ha egy metrika egy adott időszak küszöbértékét (például a válaszadási időt, a kivételek számát, a CPU-használatot vagy a lapok nézeteit) haladja meg.
* A [**naplózási riasztások**](../../azure-monitor/platform/alerts-unified-log.md) olyan riasztások leírására szolgálnak, amelyekben a riasztási jel egy egyéni Kusto-lekérdezésen alapul.
* A [**webes tesztek**][availability] arról tájékoztatnak, hogy a webhely nem érhető el az interneten, vagy lassan válaszol. [További információ][availability].
* Az előjelzéses [**diagnosztika**](../../azure-monitor/app/proactive-diagnostics.md) automatikusan be van állítva, hogy jelezze a szokatlan teljesítménybeli mintákat.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Kivételek riasztásának beállítása egyéni naplók használatával

Ebben a szakaszban bemutatjuk a lekérdezés-alapú kivételek riasztásának beállítását. Ebben a példában tegyük fel, hogy riasztást szeretne kapni, ha a sikertelen sebesség nagyobb, mint 10% az elmúlt 24 órában.

1. Nyissa meg az alkalmazás Insight-erőforrását a Azure Portal.
2. A bal oldalon a konfigurálás alatt kattintson a **riasztás**elemre.

    ![A bal oldali beállításnál kattintson a riasztás elemre.](./media/alerts/1appinsightalert.png)

3. A riasztás lap tetején válassza az **új riasztási szabály**lehetőséget.

     ![A riasztás lap tetején kattintson az új riasztási szabály elemre.](./media/alerts/2createalert.png)

4. Az erőforrást automatikusan ki kell jelölni. Feltétel beállításához kattintson a **feltétel hozzáadása**elemre.

    ![Kattintson a feltétel hozzáadása elemre.](./media/alerts/3addcondition.png)

5. A jel logikai beállítása lapon válassza az **egyéni naplók keresése** lehetőséget.

    ![Kattintson az egyéni naplók keresése elemre.](./media/alerts/4customlogsearch.png)

6. Az egyéni naplók keresése lapon adja meg a lekérdezést a "keresési lekérdezés" mezőben. Ebben a példában az alábbi Kusto-lekérdezést fogjuk használni.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Írja be a lekérdezést a keresési lekérdezés mezőbe](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Ezeket a lépéseket más típusú lekérdezés-alapú riasztásokra is alkalmazhatja. A Kusto lekérdezési nyelvével kapcsolatos további információkért tekintse meg a [Kusto első lépések doc](https://docs.microsoft.com/azure/kusto/concepts/) vagy ez az [SQL to Kusto Cheat Sheet](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. A "riasztás logikája" alatt válassza ki, hogy az eredmények vagy a metrika mértékének megfelelően van-e kiválasztva. Ezután válassza ki a feltételt (nagyobb, mint, egyenlő, kisebb, mint) és egy küszöbértéket. Ha megváltoztatja ezeket az értékeket, észreveheti, hogy a feltétel előnézet mondata megváltozik. Ebben a példában a "egyenlő" lehetőséget használjuk.

    ![A riasztási logika területen válasszon a (z) és a feltétel alapján megadott beállítások közül, majd írjon be egy küszöbértéket.](./media/alerts/6alertlogic.png)

8. A "kiértékelés alapja" alatt állítsa be az időszakot és a gyakoriságot. Az itt megadott időszaknak egyeznie kell azzal az értékkel, amelyet a fenti lekérdezés időszakára tettünk. Ezután kattintson a **kész**gombra.

    ![Állítsa be az időszakot és a gyakoriságot alulra, majd kattintson a kész gombra.](./media/alerts/7evaluate.png)

9. Az általunk létrehozott feltételt a becsült havi költséggel látjuk. Alább a ["műveleti csoportok"](../platform/action-groups.md) alatt létrehozhat egy új csoportot, vagy kijelölhet egy meglévőt. Ha szeretné, testreszabhatja a műveleteket.

    ![kattintson a kiválasztás vagy Létrehozás gombra a művelet csoportban.](./media/alerts/8actiongroup.png)

10. Végül adja meg a riasztás részleteit (riasztási szabály neve, leírás, súlyosság). Ha elkészült, kattintson a lenti **riasztási szabály létrehozása** elemre.

    ![A riasztás részletei területen adja meg a riasztási szabály nevét, írja be a leírást, és válasszon ki egy súlyosságot.](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Leiratkozás a klasszikus riasztási értesítő e-mailekről

Ez a szakasz a **klasszikus rendelkezésre állási riasztásokra**, a **klasszikus Application Insights metrikai riasztásokra**, valamint a **klasszikus meghibásodási rendellenességekre vonatkozó riasztásokra**vonatkozik.

E-mail-értesítéseket kap a klasszikus riasztásokról, ha a következők valamelyike érvényes:

* Az e-mail-cím a riasztási szabály beállításai között található értesítő e-mail címzettjei mezőben szerepel.

* Az e-mailes értesítések küldésének lehetősége az előfizetéshez tartozó bizonyos szerepköröket birtokló felhasználók számára aktiválódik, és az adott Azure-előfizetéshez tartozó megfelelő szerepkört kell tárolnia.

![Riasztási értesítés képernyőképe](./media/alerts/alert-notification.png)

A biztonság és az adatvédelem jobb szabályozása érdekében általában azt javasoljuk, hogy explicit módon adja meg az értesítés címzettjeit a klasszikus riasztásokhoz az **értesítő e-mail címzettjei** mezőben. A visszamenőleges kompatibilitás érdekében az összes olyan felhasználót értesíteni kell, amely bizonyos szerepkörökkel rendelkezik.

Egy adott riasztási szabály által létrehozott e-mail értesítések lemondásához távolítsa el az e-mail címét az **értesítő e-mail címzettjei** mezőből.

Ha az e-mail-címe explicit módon nem szerepel a listában, javasoljuk, hogy tiltsa le a bizonyos szerepkörök összes tagjának automatikus értesítését, hanem azon felhasználói e-mailek listázása, akiknek a riasztási szabályra vonatkozó értesítéseket kell kapniuk az értesítő e-mail címzettjei mezőben.

## <a name="who-receives-the-classic-alert-notifications"></a>Kik kapják meg a (klasszikus) riasztási értesítéseket?

Ez a szakasz csak a klasszikus riasztásokra vonatkozik, és segít optimalizálni a riasztási értesítéseket, így biztosítva, hogy csak a kívánt címzettek kapják meg az értesítéseket. Ha többet szeretne megtudni a [klasszikus riasztások](../platform/alerts-classic.overview.md) és az új riasztási élmény közötti különbségről, tekintse meg a [riasztások áttekintése című cikket](../platform/alerts-overview.md). A riasztási értesítések vezérléséhez az új riasztások felületén használjon [műveleti csoportokat](../platform/action-groups.md).

* A klasszikus riasztási értesítések esetében javasoljuk, hogy adott címzetteket használjon.

* A Application Insights metrikákkal kapcsolatos riasztásokhoz (beleértve a rendelkezésre állási metrikákat is), a **csoportos** küldés jelölőnégyzetét, ha engedélyezve van, a tulajdonos, közreműködő vagy olvasó szerepkörrel rendelkező felhasználóknak küldi el az előfizetést. _Minden_ olyan felhasználó, aki hozzáféréssel rendelkezik az előfizetéshez, a Application Insights erőforrás hatókörben van, és értesítést fog kapni.

> [!NOTE]
> Ha jelenleg a **tömeges/csoportos** jelölőnégyzetet használja, és letiltja, akkor nem fogja tudni visszaállítani a változást.

Ha a felhasználókat a szerepköreik alapján kell értesítenie, használja az új riasztási élmény/közel valós idejű riasztásokat. A [műveleti csoportokkal](../platform/action-groups.md)e-mailes értesítéseket állíthat be a felhasználók számára a közreműködő/tulajdonos/olvasó szerepkörök bármelyikével (egyetlen lehetőségként nem kombinálva).

## <a name="automation"></a>Automation
* [A riasztások beállításának automatizálása a PowerShell használatával](../../azure-monitor/app/powershell-alerts.md)
* [Webhookok használata a riasztásokra való válaszadás automatizálására](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Lásd még
* [Rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md)
* [Riasztások beállításának automatizálása](../../azure-monitor/app/powershell-alerts.md)
* [Proaktív diagnosztika](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

