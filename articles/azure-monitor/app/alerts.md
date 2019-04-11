---
title: Riasztásokat állíthat be az Azure Application Insights |} A Microsoft Docs
description: Lassú válasz a következő alkalommal, kivételek, és más teljesítményére és használati módosításokat a webalkalmazásban értesítést kaphat.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: 8026576478b16b753ba960155c383ffec62c61ce
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469794"
---
# <a name="set-alerts-in-application-insights"></a>Riasztásokat állíthat be az Application insights szolgáltatásban
[Az Azure Application Insights] [ start] riasztja Önt, a webalkalmazásban a teljesítmény vagy a használati metrikák változásai. 

Az Application Insights monitorozza az élő alkalmazás a egy [sokféle Platform] [ platforms] teljesítménybeli problémák diagnosztizálása és a használati mintákról.

A riasztások több típusa is van:

* [**Metrikákhoz kapcsolódó riasztások** ](../../azure-monitor/platform/alerts-metric-overview.md) mondja el, ha egy metrika átlépi egy bizonyos időn – például a válaszidőt, a kivételek számát, a CPU-használat vagy a lapmegtekintések egy küszöbértéket.
* [**Naplóriasztások** ](../../azure-monitor/platform/alerts-unified-log.md) leírására szolgáló riasztások, a riasztási jel egy egyéni Kusto-lekérdezés alapján.
* [**Webes teszteket** ] [ availability] mondja el, ha a hely nem érhető el az interneten, vagy válaszra képes lassan. [További][availability].
* [**Proaktív diagnosztika** ](../../azure-monitor/app/proactive-diagnostics.md) konfigurálása automatikusan megtörténik a szokatlan teljesítmény minták kaphat értesítést.

## <a name="set-a-metric-alert"></a>Metrikariasztás beállítása
Nyissa meg a riasztási szabályok lapot, és használja a Hozzáadás gombra.

![A riasztási szabályok lapon válassza a riasztás hozzáadásához. Állítsa be az alkalmazás és mérhető, adja meg a riasztás nevét, és válassza a metrika az erőforrásnak.](./media/alerts/01-set-metric.png)

* Állítsa be az erőforrást, mielőtt a többi tulajdonságot. **Válassza ki a "(összetevők)" erőforrást** Ha azt szeretné, a teljesítmény vagy a használati metrikákhoz riasztásokat állíthat be.
* A neve, amely engedélyezi a riasztást, az erőforráscsoportot (nem csak az alkalmazás) belül egyedinek kell lennie.
* Ügyeljen arra, hogy vegye figyelembe az egységeket, amelyben kéri, hogy adja meg a küszöbértéket.
* Ha bejelöli a jelölőnégyzetet "E-mail küldése a tulajdonosoknak...", riasztások által küldött e-mailek mindenki számára, aki hozzáfér az ebben az erőforráscsoportban. Bontsa ki a csoport, akik, adja hozzá őket a [erőforráscsoportba vagy előfizetésbe](../../azure-monitor/app/resources-roles-access-control.md) (nem az erőforrás).
* Ha megadja a "További e-mailek", riasztást kap az adott személyek vagy csoportok (függetlenül attól, be van jelölve az "e-mail-tulajdonosok..." mezőben). 
* Állítsa be a [webhook cím](../../azure-monitor/platform/alerts-webhooks.md) Ha meg van adva egy webalkalmazást, amely válaszol a riasztásokra. A riasztás aktiválásakor mind a megoldott nevezzük. (De vegye figyelembe, hogy jelenleg lekérdezési paraméterek nem továbbítódnak tulajdonságokként webhook.)
* Letilthatja a riasztás engedélyezése vagy: a gombok tetején.

*A riasztás hozzáadása gomb nem látható.*

* Szervezeti fiók használatával? Beállíthat riasztásokat, ha rendelkezik tulajdonosi vagy közreműködői hozzáférést az alkalmazás-erőforrást. Vessen egy pillantást a hozzáférés-vezérlés fülre. [További tudnivalók a hozzáférés-vezérlés][roles].

> [!NOTE]
> A riasztások panelen láthatja, hogy már van egy riasztási csoport: [Proaktív diagnosztika](../../azure-monitor/app/proactive-failure-diagnostics.md). Az automatikus riasztás figyeli egy adott mérőszám, kérelmek hibaaránya. Kivéve, ha úgy dönt, hogy a proaktív riasztás letiltása, nem kell beállítani a saját riasztás kérelmek hibaaránya.
> 
> 

## <a name="see-your-alerts"></a>A riasztások megtekintéséhez
Közötti inaktív és az aktív kap egy e-mailt, ha egy riasztás módosítások állapotba. 

A riasztási szabályok lapon látható minden egyes riasztás aktuális állapota.

Nincs friss tevékenység a riasztások összegzését legördülő:

![Riasztások kombinált](./media/alerts/010-alert-drop.png)

Állapot változásainak az előzményeit a tevékenységnaplóban van:

![Az Áttekintés lapon kattintson a beállítások, a vizsgálati naplók](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>A riasztások működése
* Riasztás három állapota van: "Soha nem aktiválódik", "Aktiválva" és "Megoldva." Legutóbbi értékelésének volt igaz, aktivált azt jelenti, hogy a megadott feltétel.
* Értesítés jön létre, ha egy riasztás állapota. (Ha a riasztási feltétel, a riasztás létrehozásakor már volt igaz, nem kaphat értesítést mindaddig, amíg a feltétel hamis kerül.)
* Minden értesítés e-mailt állít elő, ha be van jelölve az e-mailek mezőbe, vagy a megadott e-mail-címeket. Az értesítések legördülő listája is megjeleníthető.
* Riasztás minden alkalommal, amikor egy metrika megérkezik, de más módon nem értékeli ki.
* A kiértékelés a metrika összefoglalja az előző időszakban, és összehasonlítja azt a küszöbértéket, az új állapot meghatározásához.
* Az időszak, Ön által választott Megadja azt az időtartamot, amelyen a metrikák vannak összesítve. Ez nincs hatással, milyen gyakran legyen kiértékelve a riasztást: attól függ, hogy a metrikák megérkezését gyakoriságát.
* Ha nincs adat érkezik egy adott metrika egy kis ideig, a térköz rendelkezik riasztás értékelése és a metrika explorer a diagramok különböző hatásokkal. A metrika Explorerben a látható adatok nem hosszabb, mint a mintavételi időközben a diagramot, ha a diagram mutatja a 0 érték. De ugyanazt a metrika alapján riasztás nem újraértékeli, és a riasztási állapot változatlan marad. 
  
    Végül adatok érkeznek, amikor a diagram ugrik vissza egy nullától eltérő értékre. A riasztást kiértékeli a megadott időszakra vonatkozóan rendelkezésre álló adatok alapján. Ha az új adatok pont az egyetlen, az időtartamon belül, az összesítés alapul, csak az, hogy az adatpont.
* Riasztás is elhalványul gyakori riasztási és kifogástalan állapota, között akkor is, ha hosszabb ideig állít be. Ez akkor fordulhat elő, ha a metrika értéke a küszöbérték körül mutat. Nem kapott szerepel a küszöbértéket: áttérés riasztás történik, ha megegyezik az átállás kifogástalanra.

## <a name="what-are-good-alerts-to-set"></a>Mik azok a megfelelő riasztások beállítása
Ez az alkalmazás függ. Első lépésként érdemes nem túl sok metrikák be. Némi időt a mérőszám-diagramok megnézzük, miközben az alkalmazás fut, betekintést nyerhet a viselkedik általában. Ez az eljárás segítségével keresse meg a teljesítmény javítására. Ezután állíthat be riasztásokat mondja el, ha a metrikák nyissa meg a normál zónán kívüli. 

Népszerű riasztások a következők:

* [Böngésző metrikák][client], különösen a böngésző **lapbetöltési idők lapon**, jó webes alkalmazásokhoz. Ha a lapon számos parancsfájlok, kell keresnie az **böngészőkivételek**. Ezek a metrikák és riasztások eléréséhez be kell [weblap figyelési][client].
* **Kiszolgáló válaszideje** webes alkalmazás kiszolgálói oldalára. Riasztások beállítása, valamint ez a mérőszám, ha azt művelettől aránytalanul magas kérelemarányok megtekintéséhez kövesse figyelemmel: variation jelezheti, hogy az alkalmazás fut-e elegendő erőforrással. 
* **Kiszolgálókivételek** – őket, hogy rendelkezik egy elvégzése [további telepítési](../../azure-monitor/app/asp-net-exceptions.md).

Ne feledje, hogy [proaktív hibadiagnosztika arány](../../azure-monitor/app/proactive-failure-diagnostics.md) automatikusan figyelni a sebesség, amellyel az alkalmazás válasza, hiba kód-kérelmekre.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Az egyéni napló keresési kivétel riasztás beállítása

Ebben a szakaszban végigvesszük azokat a lekérdezés alapú kivétel riasztás beállítása. Például tegyük fel, egy riasztás szeretnénk, ha sikertelen aránya nagyobb, mint 10 % az elmúlt 24 órában.

1. Nyissa meg az Application Insights-erőforrást az Azure Portalon.
2. A bal oldalon a konfigurálásához kattintson a **riasztási**.

    ![A bal területen adja meg a riasztás kattintson](./media/alerts/1appinsightalert.png)

3. Válassza ki a riasztási lap tetején lévő **Új riasztási szabály**.

     ![A riasztási lap tetején kattintson az Új riasztási szabály](./media/alerts/2createalert.png)

4. Az erőforrás automatikus beállítás kell lennie. Állítson be feltételt, kattintson a **feltétel hozzáadása**.

    ![Kattintson a feltétel hozzáadása](./media/alerts/3addcondition.png)

5. Válassza ki a konfigurálás jel logikájának lapot **egyéni naplók keresése**

    ![Kattintson az egyéni naplók keresése](./media/alerts/4customlogsearch.png)

6. Az egyéni napló keresési lapon adja meg a lekérdezést a "Keresési lekérdezés" mezőben. Ebben a példában használjuk fel az alábbi Kusto-lekérdezés.
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

    ![Keresési lekérdezés mezőbe írja be a lekérdezést](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Ezeket a lépéseket a többi lekérdezés alapú riasztásokat is alkalmazhat. További információ erről a Kusto lekérdezési nyelv [Kusto-első lépések dokumentumot](https://docs.microsoft.com/azure/kusto/concepts/) vagy ez [SQL Kusto-– Adatlap](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Válassza ki "Alert logic", hogy eredményeket vagy metrikus egység számát alapul. Ezután válassza ki a feltétel (nagyobb, annál kevesebb, mint) és a egy küszöbértéket. Ezek az értékek módosítása, közben, Észreveheti a feltétel előnézete mondat változik. Ebben a példában az "equal" használjuk.

    ![Riasztási logika szerint a beállítások alapján megadott és a feltétel közül választhat, majd írja be a küszöbértéket](./media/alerts/6alertlogic.png)

8. A "Alapján Evaluated" az időtartam és a gyakoriság beállítása. Az időszak itt tárgyaljuk, az érték meg kell egyeznie a lekérdezésben időszak. Kattintson a **kész**.

    ![Időtartam és a gyakoriság beállítása a lap alján, és kattintson a kész](./media/alerts/7evaluate.png)

9. Most láthatjuk a létrehozott a becsült havi költségek feltétel. Alább a ["Műveletcsoportok"](../platform/action-groups.md) hozzon létre egy új csoportot, vagy válasszon ki egy meglévőt. Ha azt szeretné, testre szabható a műveleteket.

    ![Kattintson a select vagy gombok a műveletcsoport létrehozása](./media/alerts/8actiongroup.png)

10. Végül adja hozzá a riasztás részleteinek (riasztási szabály nevét, leírását, súlyosság). Amikor elkészült, kattintson a **riasztási szabály létrehozása** alján.

    ![A riasztás részletei adja meg a riasztási szabály nevét, egy leírást és egy súlyosság kiválasztása](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Hogyan lehet lemondani a klasszikus riasztási e-mail-értesítések

Vonatkozik ez a szakasz **klasszikus rendelkezésre állási riasztások**, **Application Insights metrikákhoz kapcsolódó riasztások**, és a **klasszikus rendellenességek riasztásokkal**.

Klasszikus riasztásokhoz e-mail értesítést azért küldtük Önnek, ha érvényes a következő:

* Az e-mail cím szerepel az értesítési e-mail címzettjei mezőben a riasztási szabály beállításaiban.

* E-mail-értesítések küldése az előfizetés bizonyos szerepköröket betöltő felhasználók számára a beállítás aktiválva van, és a egy megfelelő szerepkört, hogy adott Azure-előfizetés tartja.

![Riasztási értesítés képernyőképe](./media/alerts/alert-notification.png)

A biztonsági és adatvédelmi általában javasoljuk, hogy Ön kifejezetten megad a az értesítés címzettjeinek a klasszikus riasztások jobban szabályozhatja az **értesítési e-mailek címzettjeinek** mező. Arra, hogy bizonyos szerepköröket betöltő összes felhasználó értesítése az előző verziókkal való kompatibilitás biztosítunk.

Mondja le egy bizonyos riasztási szabály által létrehozott e-mail-értesítések, távolítsa el az e-mail címét a **értesítési e-mailek címzettjeinek** mező.

Ha az e-mail-címét nem explicit módon szerepel, azt javasoljuk, hogy tiltsa le az egyes szerepkörök összes tagja automatikusan értesíti, és ki kell kapniuk a riasztási szabályhoz tartozó értesítések az értesítési e-mailben, az összes felhasználói e-mailek ehelyett listázása címzettek mező.

## <a name="who-receives-the-classic-alert-notifications"></a>Ki kapja a (klasszikus) riasztási értesítések?

Ez a szakasz csak klasszikus riasztások vonatkozik, és segít optimalizálni a riasztási értesítések biztosítják, hogy csak a kívánt címzettek megkapják az értesítéseket. Ismerje meg jobban a különbség a [klasszikus riasztások](../platform/alerts-classic.overview.md) és az új riasztások tapasztal, tekintse meg a [riasztások áttekintő cikkben](../platform/alerts-overview.md). A riasztások új kezelőfelülete a riasztási értesítések beállításához használja [Műveletcsoportok](../platform/action-groups.md).

* A klasszikus riasztási értesítéseket meghatározott címzettek használatát javasoljuk.

* A bármely (beleértve a rendelkezésre állási metrikák), az Application Insights-metrikák riasztásaihoz a **tömeges/csoport** jelölőnégyzetet, a beállítást, ha engedélyezve van, felhasználóknak küld az előfizetésben tulajdonos, közreműködő vagy olvasó szerepkört. Gyakorlatilag _összes_ az előfizetés az Application Insights-erőforráshoz hozzáféréssel rendelkező felhasználók terjed ki, és értesítéseket kap.

> [!NOTE]
> Ha jelenleg használja a **tömeges/csoport** jelölőnégyzetet, a beállítást, és tiltsa le, nem állíthatja vissza a módosítást.

Az új riasztás élmény/közel valós idejű riasztások használja, ha értesítse a felhasználókat a szerepkörökhöz alapján kell. A [Műveletcsoportok](../platform/action-groups.md), bármelyik (nem egyesíthet egyetlen lehetőségként) közreműködői vagy tulajdonosi vagy olvasó szerepkört konfigurálható e-mail értesítések küldéséhez felhasználók számára.

## <a name="automation"></a>Automation
* [Riasztások beállítása automatizálása a PowerShell használatával](../../azure-monitor/app/powershell-alerts.md)
* [Webhookok használatával automatizálhatja a riasztásokra való reagálásról](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Lásd még
* [Rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md)
* [Riasztások beállítása automatizálása](../../azure-monitor/app/powershell-alerts.md)
* [Proaktív diagnosztika](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

