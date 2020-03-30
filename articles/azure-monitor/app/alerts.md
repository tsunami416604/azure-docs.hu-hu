---
title: Riasztások beállítása az Azure Application Insightsban
description: Értesítést kaphat a lassú válaszidőkről, a kivételekről és a webalkalmazás egyéb teljesítmény- vagy használati változásairól.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295085"
---
# <a name="set-alerts-in-application-insights"></a>Riasztások beállítása az Application Insightsban

[Az Azure Application Insights][start] figyelmezteti a webes alkalmazás teljesítmény- vagy használati metrikák változásaira. 

Az Application Insights az élő alkalmazást számos [platformon][platforms] figyeli, hogy segítsen diagnosztizálni a teljesítménnyel kapcsolatos problémákat és megérteni a használati mintákat.

Többféle riasztás létezik:

* [**Metrikariasztások**](../../azure-monitor/platform/alerts-metric-overview.md) azt adják meg, ha egy metrika átlép egy küszöbértéket egy adott időszakban – például a válaszidők, a kivételek száma, a CPU-használat vagy az oldalmegtekintések.
* [**A naplóriasztások**](../../azure-monitor/platform/alerts-unified-log.md) olyan riasztások leírására szolgálnak, ahol a riasztási jelzés egy egyéni Kusto-lekérdezésen alapul.
* [**A webes tesztek**][availability] azt adják meg, ha webhelye nem érhető el az interneten, vagy lassan válaszol. [További információ][availability].
* [**A proaktív diagnosztika**](../../azure-monitor/app/proactive-diagnostics.md) automatikusan be van állítva, hogy értesítést küldjön a szokatlan teljesítménymintákról.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>A kivételriasztás beállítása egyéni naplókereséssel

Ebben a szakaszban végighaladunk a lekérdezésalapú kivételriasztás beállításán. Ebben a példában tegyük fel, hogy riasztást szeretnénk, ha a sikertelen arány nagyobb, mint 10% az elmúlt 24 órában.

1. Nyissa meg az Application Insight-erőforrást az Azure Portalon.
2. A bal oldalon, a konfigurálása kattintson a **Alert**.

    ![A bal oldalon a click alert konfigurálása csoportban](./media/alerts/1appinsightalert.png)

3. A riasztási lap tetején válassza az **Új riasztási szabály**lehetőséget.

     ![A riasztási lap tetején kattintson az új riasztási szabályra](./media/alerts/2createalert.png)

4. Az erőforrást automatikusan ki kell jelölni. Feltétel beállításához kattintson a **Feltétel hozzáadása gombra.**

    ![Kattintson a Feltétel hozzáadása gombra](./media/alerts/3addcondition.png)

5. A Jellogika konfigurálása lapon válassza az **Egyéni naplókeresés** lehetőséget.

    ![Kattintson az egyéni naplókeresésre](./media/alerts/4customlogsearch.png)

6. Az egyéni naplókeresés lapon írja be a lekérdezést a "Lekérdezés keresése" mezőbe. Ebben a példában az alábbi Kusto lekérdezést fogjuk használni.
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

    ![Lekérdezés beírása a keresési lekérdezés mezőbe](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Ezeket a lépéseket más típusú lekérdezésalapú riasztásokra is alkalmazhatja. Ha többet szeretne megtudni a Kusto lekérdezési nyelv ebből [Kusto első lépések doc](https://docs.microsoft.com/azure/kusto/concepts/) vagy ez [az SQL A Kusto cheat sheet](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. A "Riasztási logika" területen válassza ki, hogy az eredmények száma vagy metrikamérés alapján. Ezután válassza ki a feltételt (nagyobb, egyenlő, kisebb, mint) és egy küszöbértéket. Az értékek módosítása közben észreveheti, hogy a feltétel előnézeti mondata megváltozik. Ebben a példában az "egyenlő".

    ![A Riasztási logika csoportban válasszon a megadott beállítások alapján és a feltétellel, majd írjon be egy küszöbértéket](./media/alerts/6alertlogic.png)

8. Az "Értékelés alapján" területen állítsa be az időszakot és a gyakoriságot. Az itt lévő időszaknak meg kell egyeznie a fenti lekérdezésben az időszakra vonatkozóan feltett értékkel. Ezután kattintson **a kész gombra.**

    ![Állítsa be az időszakot és a gyakoriságot alul, majd kattintson a kész](./media/alerts/7evaluate.png)

9. Most már látjuk a feltételt hoztunk létre a becsült havi költség. Az alábbiakban a ["Műveletcsoportok"](../platform/action-groups.md) alatt új csoportot hozhat létre, vagy kiválaszthat egy meglévőt. Ha szeretné, testreszabhatja a műveleteket.

    ![kattintson a kijelölésre vagy hozzon létre gombokat a műveletcsoportban](./media/alerts/8actiongroup.png)

10. Végül adja meg a riasztás részleteit (riasztási szabály neve, leírása, súlyossága). Ha elkészült, kattintson a **Figyelmeztetési szabály létrehozása** alján.

    ![A riasztás részletei mezőbe írja be a riasztási szabály nevét, írjon leírást, és válasszon súlyosságot](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Hogyan lehet leiratkozni a klasszikus figyelmeztető e-mail értesítésekről

Ez a szakasz **a klasszikus rendelkezésre állási riasztásokra**, **a klasszikus Application Insights-metrikariasztásokra**és a **klasszikus hibaanomáliákriasztásokra vonatkozik.**

E-mail értesítéseket kap ezekről a klasszikus értesítésekről, ha az alábbiak bármelyike érvényes:

* Az e-mail cím a riasztási szabály beállításaiközött található Értesítés e-mail címzettjei mezőben.

* Az a lehetőség, hogy e-mail értesítéseket küldjön a bizonyos szerepkörökkel rendelkező felhasználók az előfizetés aktiválva van, és rendelkezik egy megfelelő szerepkört az adott Azure-előfizetés.

![Értesítésképernyő-képernyőkép](./media/alerts/alert-notification.png)

A biztonság és az adatvédelem jobb szabályozása érdekében általában azt javasoljuk, hogy az **Értesítés e-mail címzettjei** mezőben adja meg kifejezetten a klasszikus riasztások értesítési címzettjeit. A bizonyos szerepköröket betöltő összes felhasználó értesítésének lehetősége a visszamenőleges kompatibilitást biztosítja.

Ha le szeretne iratkozni egy bizonyos figyelmeztető szabály által létrehozott e-mail értesítésekről, távolítsa el az e-mail címét az **Értesítés e-mail címzettjei** mezőből.

Ha az e-mail címe nem szerepel kifejezetten a listában, javasoljuk, hogy tiltsa le azt a lehetőséget, hogy bizonyos szerepkörök minden tagját automatikusan értesítse, és ehelyett sorolja fel az összes olyan felhasználói e-mailt, amelynek értesítést kell kapnia az adott értesítési szabályról az Értesítés e-mailben. címzettek mezőjében.

## <a name="who-receives-the-classic-alert-notifications"></a>Ki kapja meg a (klasszikus) riasztási értesítéseket?

Ez a szakasz csak a klasszikus riasztásokra vonatkozik, és segít optimalizálni a riasztási értesítéseket annak érdekében, hogy csak a kívánt címzettek kapjanak értesítéseket. Ha többet szeretne megtudni a [klasszikus riasztások](../platform/alerts-classic.overview.md) és az új riasztások közötti különbségről, olvassa el a [riasztások áttekintéséről szóló cikket.](../platform/alerts-overview.md) Ha szabályozni szeretné a riasztási értesítést az új riasztások ban, használja a [műveletcsoportokat.](../platform/action-groups.md)

* Azt javasoljuk, hogy a klasszikus riasztási értesítések adott címzettek használatát.

* Az Application Insights-metrikákra vonatkozó riasztások (beleértve a rendelkezésre állási mutatókat is) esetén a **tömeges/csoport** jelölőnégyzet, ha engedélyezve van, elküldi az előfizetésben tulajdonosi, közreműködői vagy olvasói szerepkörrel rendelkező felhasználóknak. Valójában _az Application_ Insights-erőforrás hoz az előfizetéshez hozzáféréssel rendelkező összes felhasználó hatóköre van, és értesítéseket kap.

> [!NOTE]
> Ha jelenleg a **tömeges/csoport** jelölőnégyzetet használja, és letiltja azt, akkor nem tudja visszaállítani a módosítást.

Használja az új riasztási élményt/közel valós idejű riasztásokat, ha a szerepkörük alapján értesítenie kell a felhasználókat. A [műveletcsoportok](../platform/action-groups.md)segítségével beállíthatja az e-mail értesítéseket a közreműködői/tulajdonosi/olvasói szerepkörrel rendelkező felhasználók számára (nem kombinálva egyetlen lehetőségként).

## <a name="automation"></a>Automation
* [A PowerShell használata a riasztások beállításának automatizálásához](../../azure-monitor/app/powershell-alerts.md)
* [Webhookok használata a riasztásokra adott válasz automatizálásához](../../azure-monitor/platform/alerts-webhooks.md)

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

