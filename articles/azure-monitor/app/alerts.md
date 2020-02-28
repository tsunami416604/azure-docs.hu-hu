---
title: Riasztások beállítása az Azure Application Insights
description: Értesítést kaphat a lassú válaszidő, a kivételek és a webalkalmazás más teljesítmény-és használati változásairól.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 80759c94d7cc5b60b6e38a34b85fb64c3c18fd2e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666717"
---
# <a name="set-alerts-in-application-insights"></a>Riasztások beállítása Application Insightsban

Az [Azure Application Insights][start] riasztást küld a webalkalmazás teljesítmény-és használati metrikáinak változásairól. 

Application Insights figyeli az élő alkalmazást a [különböző platformokon][platforms] , hogy segítsen a teljesítménnyel kapcsolatos problémák diagnosztizálásában és a használati minták értelmezésében.

Több típusú riasztás létezik:

* A [**metrikai riasztások**](../../azure-monitor/platform/alerts-metric-overview.md) jelzik, ha egy metrika egy adott időszak küszöbértékét (például a válaszadási időt, a kivételek számát, a CPU-használatot vagy a lapok nézeteit) haladja meg.
* A [**naplózási riasztások**](../../azure-monitor/platform/alerts-unified-log.md) olyan riasztások leírására szolgálnak, amelyekben a riasztási jel egy egyéni Kusto-lekérdezésen alapul.
* A [**webes tesztek**][availability] arról tájékoztatnak, hogy a webhely nem érhető el az interneten, vagy lassan válaszol. [További információk][availability].
* Az előjelzéses [**diagnosztika**](../../azure-monitor/app/proactive-diagnostics.md) automatikusan be van állítva, hogy jelezze a szokatlan teljesítménybeli mintákat.

## <a name="set-a-metric-alert"></a>Metrika riasztásának beállítása

Nyissa meg a riasztási szabályok lapot, majd használja az Add (Hozzáadás) gombot.

![A riasztási szabályok lapon válassza a riasztás hozzáadása elemet. Állítsa be az alkalmazást erőforrásként a mértékhez, adja meg a riasztás nevét, és válassza ki a metrikát.](./media/alerts/01-set-metric.png)

* Adja meg az erőforrást a többi tulajdonság előtt. **Válassza ki az "(összetevők)" erőforrást** , ha riasztásokat szeretne beállítani a teljesítményre vagy a használati metrikára vonatkozóan.
* A riasztáshoz megadott névnek egyedinek kell lennie az erőforráscsoport (nem csak az alkalmazás) belül.
* Ügyeljen arra, hogy az egység, amelyben meg kell adnia a küszöbértéket.
* Ha bejelöli az "e-mail-tulajdonosok..." jelölőnégyzetet, a rendszer e-mailben küldi el a riasztásokat az erőforráscsoporthoz hozzáférő összes felhasználónak. Ezen személyek kibontásához adja hozzá őket az [erőforráscsoporthoz vagy az előfizetéshez](../../azure-monitor/app/resources-roles-access-control.md) (nem az erőforráshoz).
* Ha a "további e-mailek" lehetőséget választja, akkor a rendszer riasztásokat küld a felhasználóknak vagy csoportoknak (függetlenül attól, hogy bejelölte-e az "e-mail-tulajdonosok..." Box). 
* Ha olyan webalkalmazást állított be, amely válaszol a riasztásokra, állítson be egy [webhook-címeket](../../azure-monitor/platform/alerts-webhooks.md) . Ha a riasztás aktiválva van, és a megoldás megoldódott, a rendszer mindkettőt hívja meg. (De vegye figyelembe, hogy a lekérdezési paramétereket nem a webhook-tulajdonságok továbbítják.)
* A riasztást letilthatja vagy engedélyezheti: a felül található gombokat tekintheti meg.

*Nem jelenik meg a riasztás hozzáadása gomb.*

* Szervezeti fiókot használ? Beállíthat riasztásokat, ha tulajdonosi vagy közreműködői hozzáférése van ehhez az alkalmazás-erőforráshoz. Tekintse meg a Access Control lapot. [Ismerje meg a hozzáférés-vezérlést][roles].

> [!NOTE]
> A riasztások panelen láthatja, hogy már van beállítva riasztás: [proaktív diagnosztika](../../azure-monitor/app/proactive-failure-diagnostics.md). Az automatikus riasztás egy adott mérőszámot, a kérelmek meghibásodási arányát figyeli. Ha úgy dönt, hogy letiltja a proaktív riasztást, nem kell beállítania a saját riasztást a kérelem meghibásodási arányára.
> 
> 

## <a name="see-your-alerts"></a>Riasztások megtekintése
E-mailt kap, ha egy riasztás megváltoztatja az inaktív és az aktív közötti állapotot. 

Az egyes riasztások aktuális állapota a riasztási szabályok lapon látható.

A riasztások legördülő listájában a legutóbbi tevékenységek összegzése látható:

![Riasztások legördülő lista](./media/alerts/010-alert-drop.png)

Az állapot változásainak előzményei a tevékenység naplójában vannak:

![Az Áttekintés lapon kattintson a beállítások elemre, majd a naplók elemre.](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>A riasztások működése
* A riasztás három állapottal rendelkezik: "soha nem aktivált", "aktivált" és "megoldva". Az aktiválva érték azt jelenti, hogy a megadott feltétel igaz, a legutóbbi kiértékeléskor.
* Egy értesítés akkor jön létre, amikor egy riasztás megváltoztatja az állapotot. (Ha a riasztási feltétel már a riasztás létrehozásakor is igaz, akkor előfordulhat, hogy nem kap értesítést, amíg a feltétel hamisra nem kerül.)
* Minden értesítés létrehoz egy e-mailt, ha bejelölte az e-maileket vagy a megadott e-mail-címeket. Tekintse meg az értesítések legördülő listát is.
* A rendszer minden alkalommal kiértékel egy riasztást, ha egy metrika érkezik, de nem.
* A kiértékelés az előző időszakban összesíti a metrikát, majd összehasonlítja a küszöbértékkel az új állapot meghatározásához.
* A kiválasztott időszak határozza meg, hogy a metrikák milyen időközönként legyenek összesítve. Nem befolyásolja, hogy a riasztás milyen gyakran van kiértékelve: Ez a mérőszámok érkezésének gyakoriságával függ.
* Ha egy adott mérőszámhoz nem érkezik adat egy ideig, a hézag eltérő hatással van a riasztás kiértékelésére és a metrika-kezelő diagramjaira. Ha a metrika-kezelőben nem látható az adatok a diagram mintavételi intervallumának hosszabb ideig, a diagram értéke 0. Az azonos metrikán alapuló riasztások azonban nem lesznek újraértékelve, és a riasztás állapota változatlan marad. 
  
    Amikor az adatvesztés megérkezik, a diagram vissza nem nulla értékre vált. A riasztás a megadott időszakra vonatkozóan rendelkezésre álló adatértékek alapján értékeli ki. Ha az adott időszakban az új adatpont az egyetlen elérhető, az összesítés az adott adatponton alapul.
* A riasztások gyakran villognak a riasztások és a kifogástalan állapotok között, még akkor is, ha hosszú időtartamot állít be. Ez akkor fordulhat elő, ha a metrika értéke a küszöbérték körül mozog. Nincs hiszterézis a küszöbértékben: a riasztásra való áttérés ugyanazon az értéken történik, mint a kifogástalanra váltás.

## <a name="what-are-good-alerts-to-set"></a>Mik azok a jó riasztások, amelyeket be kell állítani?
Ez az alkalmazástól függ. A kezdéshez érdemes túl sok mérőszámot beállítani. Töltsön le egy kis időt a metrikák diagramjaira az alkalmazás futása közben, hogy úgy érezze, hogyan viselkedik a szokásos módon. Ezzel a gyakorlattal megtalálhatja a teljesítményének javítására szolgáló módszereket. Ezután állítson be riasztásokat, amelyekkel megtudhatja, ha a metrikák a normál zónán kívül esnek. 

Népszerű riasztások például a következők:

* A [böngésző metrikái][client], különösen a böngésző **lapjainak betöltési ideje**jó a webes alkalmazásokhoz. Ha a lap számos szkripttel rendelkezik, a **böngésző kivételeit**kell megkeresnie. A mérőszámok és riasztások beszerzéséhez be kell állítania a [weblapok figyelését][client].
* Kiszolgáló **válaszideje** a webalkalmazások kiszolgálói oldalán. A riasztások beállítása mellett tartsa szem előtt ezt a metrikát annak megállapításához, hogy a magas kérelmek díjszabása aránytalanul változik-e: a variáció azt jelezheti, hogy az alkalmazás elfogyott az erőforrásokból. 
* **Kiszolgálói kivételek** – ha meg szeretné tekinteni őket, [további beállításokra](../../azure-monitor/app/asp-net-exceptions.md)van szükség.

Ne feledkezzen meg arról, hogy a [proaktív sikertelenség-diagnosztika](../../azure-monitor/app/proactive-failure-diagnostics.md) automatikusan figyeli azt a sebességet, amellyel az alkalmazás a hibakódokkal kapcsolatos kérelmekre válaszol.

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

Ha az e-mail-címe nem szerepel explicit módon, javasoljuk, hogy tiltsa le a bizonyos szerepkörök összes tagjának automatikus értesítését, hanem azon felhasználói e-mailek listázása, akiknek a riasztási szabályhoz értesítést kell kapniuk az értesítő e-mailben címzettek mező.

## <a name="who-receives-the-classic-alert-notifications"></a>Kik kapják meg a (klasszikus) riasztási értesítéseket?

Ez a szakasz csak a klasszikus riasztásokra vonatkozik, és segít optimalizálni a riasztási értesítéseket, így biztosítva, hogy csak a kívánt címzettek kapják meg az értesítéseket. Ha többet szeretne megtudni a [klasszikus riasztások](../platform/alerts-classic.overview.md) és az új riasztási élmény közötti különbségről, tekintse meg a [riasztások áttekintése című cikket](../platform/alerts-overview.md). A riasztási értesítések vezérléséhez az új riasztások felületén használjon [műveleti csoportokat](../platform/action-groups.md).

* A klasszikus riasztási értesítések esetében javasoljuk, hogy adott címzetteket használjon.

* A Application Insights metrikákkal kapcsolatos riasztásokhoz (beleértve a rendelkezésre állási metrikákat is), a **csoportos** küldés jelölőnégyzetét, ha engedélyezve van, a tulajdonos, közreműködő vagy olvasó szerepkörrel rendelkező felhasználóknak küldi el az előfizetést. _Minden_ olyan felhasználó, aki hozzáféréssel rendelkezik az előfizetéshez, a Application Insights erőforrás hatókörben van, és értesítést fog kapni.

> [!NOTE]
> Ha jelenleg a **tömeges/csoportos** jelölőnégyzetet használja, és letiltja, akkor nem fogja tudni visszaállítani a változást.

Ha a felhasználókat a szerepköreik alapján kell értesítenie, használja az új riasztási élmény/közel valós idejű riasztásokat. A [műveleti csoportokkal](../platform/action-groups.md)e-mailes értesítéseket állíthat be a felhasználók számára a közreműködő/tulajdonos/olvasó szerepkörök bármelyikével (egyetlen lehetőségként nem kombinálva).

## <a name="automation"></a>Automatizálás
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

