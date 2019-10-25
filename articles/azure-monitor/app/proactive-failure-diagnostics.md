---
title: Intelligens észlelés – meghibásodási rendellenességek, Application Insights | Microsoft Docs
description: Riasztást küld a webalkalmazásnak küldött sikertelen kérelmek arányának szokatlan változásairól, és diagnosztikai elemzéseket is biztosít. Nincs szükség konfigurációra.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.openlocfilehash: f8b8318a16b36593d2fbaf08bcbc19156dc96006
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820586"
---
# <a name="smart-detection---failure-anomalies"></a>Intelligens észlelés – hibák rendellenességei
A [Application Insights](../../azure-monitor/app/app-insights-overview.md) automatikusan értesíti Önt a közel valós időben, ha a webalkalmazása rendellenes növekedést okoz a sikertelen kérelmek arányában. Ez a művelet szokatlanul megnövekszik a HTTP-kérelmek vagy a sikertelenként jelentett függőségi hívások gyakorisága. A kérelmek esetében a sikertelen kérelmek általában a 400-es vagy magasabb szintű hibakódok. A probléma osztályozásának és diagnosztizálásának elősegítése érdekében az értesítésben a hibák és a kapcsolódó telemetria jellemzőinek elemzése szerepel. További diagnosztizálásra a Application Insights portálra mutató hivatkozások is rendelkezésre állnak. A szolgáltatásnak nincs szüksége beállításra és konfigurációra, mivel gépi tanulási algoritmusokat használ a normál meghibásodási arány előrejelzéséhez.

Ez a szolgáltatás bármely, a felhőben vagy a saját kiszolgálókon üzemeltetett webalkalmazásra működik, amely kérést vagy függőségi telemetria hoz létre – például ha olyan feldolgozói szerepkörrel rendelkezik, amely meghívja a [TrackRequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) vagy a [TrackDependency ()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)metódust.

Miután beállította [Application Insights a projekthez](../../azure-monitor/app/app-insights-overview.md), és biztosította, hogy az alkalmazás egy bizonyos minimális telemetria állít elő, a hibák észlelésének intelligens észlelése 24 órát vesz igénybe az alkalmazás normál működésének megismerése előtt, és küldhet riasztások.

Íme egy példa a riasztásra.

![Példa intelligens észlelési riasztásra, amely a fürt elemzési hibáját mutatja](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Alapértelmezés szerint a példánál rövidebb formátumú e-maileket kap. [Erre a részletes formátumra azonban váltani](#configure-alerts)lehet.
>
>

Figyelje meg, hogy a következőket mutatja be:

* A hiba aránya a normál alkalmazás működéséhez képest.
* Hány felhasználót érintenek, hogy tudja, mennyit kell aggódnia.
* A hibákhoz tartozó jellemző minta. Ebben a példában egy adott válasz kódja, a kérelem neve (művelet) és az alkalmazás verziója szerepel. Ezzel azonnal megtudhatja, hol kezdjen el keresni a kódban. Más lehetőségek lehetnek egy adott böngésző vagy ügyféloldali operációs rendszer.
* A jellemző hibákkal társított kivételek, naplók és függőségi hibák (adatbázisok vagy más külső összetevők).
* Közvetlenül a kapcsolódó keresésekre mutató hivatkozásokat Application Insightsban található telemetria.

## <a name="failure-anomalies-v2"></a>Hiba anomália v2
Mostantól elérhető a meghibásodási rendellenességek riasztási szabályának új verziója. Ez az új verzió az új Azure-riasztási platformon fut, és számos javítást vezet be a meglévő verzióra.

### <a name="whats-new-in-this-version"></a>Újdonságok ebben a verzióban?
- Problémák gyorsabb észlelése
- Több művelet – a riasztási szabály egy "Application Insights Smart Detection" nevű társított [műveleti csoporttal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) jön létre, amely e-mail-és webhook-műveleteket tartalmaz, és kiterjeszthető további műveletek elindítására, ha a riasztás tüzet okoz.
- Célzottabb értesítések – a riasztási szabály által küldött e-mail-értesítések alapértelmezés szerint az előfizetés figyelési olvasója és a közreműködő szerepkörök figyelése céljából lesznek elküldve. Erről további információt [itt](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification)talál.
- Egyszerűbb konfigurálás az ARM-sablonok használatával – [itt](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)talál példát.
- Általános riasztási séma támogatása – a riasztási szabályból küldött értesítések a [Common Alert sémát](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)követik.
- Egységesített e-mail-sablon – a riasztási szabály e-mail-értesítéseinek konzisztens megjelenési & érzik a többi riasztási típust. Ha ezt a módosítást választja, a hiba miatti hibák észlelésére vonatkozó riasztások részletes diagnosztikai információkkal való lekérése már nem érhető el.

### <a name="how-do-i-get-the-new-version"></a>Hogyan lekérni az új verziót?
- Az újonnan létrehozott Application Insights erőforrások mostantól a hiba-rendellenességek riasztási szabályának új verziójával vannak kiépítve.
- Meglévő Application Insights erőforrások a hiba rendellenességének klasszikus változatával a riasztási szabály a [klasszikus riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)kivonási folyamatának részeként az új verziót fogja kapni az új riasztási platformra való Migrálás után.

> [!NOTE]
> A hibát okozó rendellenességek új verziója a riasztási szabály ingyenes marad. Emellett a társított "Application Insights Smart Detection" műveleti csoport által aktivált e-mail-és webhook-műveletek is díjmentesek.
> 
> 

## <a name="benefits-of-smart-detection"></a>Az intelligens észlelés előnyei
A szokásos [metrikus riasztások](../../azure-monitor/app/alerts.md) jelzik, hogy probléma lehet. Az intelligens észlelés azonban elindítja a diagnosztikai munkát, és sok olyan elemzést végez, amelyet egyébként nem kell elvégeznie. Az eredményeket szépen csomagolja, és segít a probléma gyökerének gyors megszerzésében.

## <a name="how-it-works"></a>Működési elv
Az intelligens észlelés figyeli az alkalmazástól kapott telemetria, és különösen a hibák sebességét. Ez a szabály számolja a kérelmek számát, amelyeken a `Successful request` tulajdonság hamis, valamint azon függőségi hívások száma, amelyek esetében a `Successful call` tulajdonság hamis. A kérelmek esetében alapértelmezés szerint `Successful request == (resultCode < 400)` (hacsak nem írt egyéni kódot a saját [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) -hívások [szűréséhez](../../azure-monitor/app/api-filtering-sampling.md#filtering) vagy létrehozásához). 

Az alkalmazás teljesítménye jellemző viselkedési mintázatot tartalmaz. Bizonyos kérelmek vagy függőségi hívások hajlamosabbak a többitől eltérő hibákra; a teljes meghibásodási arány pedig a terhelés növekedésével járhat. Az intelligens észlelés a gépi tanulás használatával keresi meg ezeket a rendellenességeket.

Ahogy a telemetria bekerül Application Insights a webalkalmazásból, az intelligens észlelés összehasonlítja a jelenlegi viselkedést az elmúlt néhány napban látható mintázatokkal. Ha a korábbi teljesítményhez képest rendellenesen megnőtt a meghibásodási arány, a rendszer egy elemzést indít el.

Az elemzések indításakor a szolgáltatás a sikertelen kérelemhez tartozó fürt elemzését hajtja végre, hogy megpróbálja azonosítani a hibákat jellemző értékek mintáját. A fenti példában az elemzés felderítette, hogy a legtöbb hiba egy adott eredmény kódjára, a kérelem nevére, a kiszolgáló URL-címére és a szerepkör-példányra vonatkozik. Ezzel szemben az elemzés azt észlelte, hogy az ügyfél operációs rendszerének tulajdonsága több érték között van elosztva, ezért nem szerepel a listáján.

Ha a szolgáltatás ezen telemetria-hívásokkal van kialakítva, az elemző kivételt és függőségi hibát keres az általa azonosított fürt kéréseivel kapcsolatban, valamint egy példát a kérelmekhez társított nyomkövetési naplókra.

Az eredményül kapott elemzést riasztásként küldi el a rendszer, ha nem konfigurálta.

A [manuálisan beállított riasztásokhoz](../../azure-monitor/app/alerts.md)hasonlóan ellenőrizheti a riasztás állapotát, és konfigurálhatja a Application Insights erőforrás riasztások paneljén. Más riasztásokkal ellentétben azonban nem kell beállítania vagy konfigurálnia az intelligens észlelést. Ha szeretné, letilthatja vagy módosíthatja a cél e-mail-címeit.

### <a name="alert-logic-details"></a>Riasztási logika részletei

A riasztásokat a szabadalmaztatott gépi tanulási algoritmus váltja ki, így nem tudjuk megosztani a pontos megvalósítás részleteit. Ezzel megértettük, hogy néha többet kell tudnia az alapul szolgáló logikai működéséről. Az elsődleges tényezők, amelyekkel megállapítható, hogy a riasztást ki kell-e indítani: 

* A kérelmek/függőségek sikertelen százalékos arányának elemzése 20 percen belül egy gördülő időablakban.
* Az elmúlt 20 perc hibájának az elmúlt 40 percben és az elmúlt hét napban való összehasonlítása, valamint olyan jelentős eltérések keresése, amelyek meghaladják a szórást.
* A minimális meghibásodási arányra vonatkozó adaptív korlát használata, amely az alkalmazás kérelmek/függőségek mennyisége alapján változik.

## <a name="configure-alerts"></a>Riasztások konfigurálása
Letilthatja az intelligens észlelést, módosíthatja az e-mail-címzetteket, létrehozhat egy webhookot, vagy megadhatja a részletesebb riasztási üzeneteket.

Nyissa meg a riasztások lapot. A hibákkal kapcsolatos rendellenességek beletartoznak a manuálisan beállított riasztásokkal együtt, és láthatja, hogy jelenleg a riasztási állapotban van-e.

![Az Áttekintés lapon kattintson a riasztások csempére. Vagy bármely metrika lapon kattintson a riasztások gombra.](./media/proactive-failure-diagnostics/021.png)

A beállításhoz kattintson a riasztásra.

![Konfiguráció](./media/proactive-failure-diagnostics/032.png)

Figyelje meg, hogy letilthatja az intelligens észlelést, de nem törölheti (vagy létrehozhat egy újat).

#### <a name="detailed-alerts"></a>Részletes riasztások
Ha a "részletesebb diagnosztika beolvasása" lehetőséget választja, az e-mail további diagnosztikai adatokat tartalmaz. Előfordulhat, hogy a probléma csak az e-mailben tárolt adatok alapján diagnosztizálható.

Kisebb a kockázata annak, hogy a részletesebb riasztás bizalmas adatokat tartalmazhat, mert kivételeket és nyomkövetési üzeneteket tartalmaz. Ez azonban csak akkor fordulhat elő, ha a kód bizalmas adatokat is engedélyez az üzenetekben.

## <a name="triaging-and-diagnosing-an-alert"></a>Riasztás osztályozásakor és diagnosztizálása
A riasztás azt jelzi, hogy a sikertelen kérelmek arányának rendellenes növekedése észlelhető. Valószínű, hogy probléma merült fel az alkalmazással vagy a környezettel kapcsolatban.

A kérések és a felhasználók számának százalékos aránya alapján eldöntheti, hogy milyen sürgős a probléma. A fenti példában a 22,5%-os meghibásodási arány a normál 1%-os arányt hasonlítja össze, ami azt jelzi, hogy valami rossz történik. Másfelől azonban csak 11 felhasználót érintettek. Ha ez volt az alkalmazása, azt is megtudhatja, hogy milyen súlyos.

Sok esetben a probléma gyorsan diagnosztizálható a kérelem nevétől, a kivételtől, a függőségi hibáktól és a megadott nyomkövetési adatoktól.

Vannak más jelek is. Például a függőségi hibák aránya ebben a példában ugyanaz, mint a kivételek aránya (89,3%). Ez azt sugallja, hogy a kivétel közvetlenül a függőségi hibától függ, így egyértelmű képet ad arról, hogy hol érdemes megkeresni a kódot.

A további vizsgálathoz az egyes szakaszokban található hivatkozások a megfelelő kérelmekre, kivételekre, függőségekre vagy nyomkövetésekre szűrt [keresési oldalra](../../azure-monitor/app/diagnostic-search.md) mutatnak. Vagy megnyithatja a [Azure Portalt](https://portal.azure.com), navigáljon az alkalmazás Application Insights erőforrásához, és nyissa meg a hibák panelt.

Ebben a példában a "függőségi hibák megtekintése" hivatkozásra kattintva megnyílik a Application Insights Search (keresés) panel. Itt látható az az SQL-utasítás, amely az alapvető ok példáját tartalmazza: a NULLák a kötelező mezőkben szerepelnek, és nem adták át az ellenőrzést a mentési művelet során.

![Diagnosztikai keresés](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Legutóbbi riasztások áttekintése

A legutóbbi riasztáshoz kattintson az **intelligens észlelés** lehetőségre:

![Riasztások összegzése](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Mi a különbség...
A meghibásodási rendellenességek intelligens észlelése kiegészíti a Application Insights hasonló, de különböző funkcióit.

* A [metrikai riasztásokat](../../azure-monitor/app/alerts.md) Ön állítja be, és a metrikák széles skáláját figyeli, például a CPU-kihasználtságot, a kérelmek sebességét, az oldal betöltési idejét stb. Ezekkel a riasztással figyelmeztetheti Önt, például ha további erőforrásokat kell hozzáadnia. Ezzel szemben a hibás anomáliák intelligens észlelése a kritikus metrikák (jelenleg csak a sikertelen kérelmek aránya) kis körét fedi le, amely a webalkalmazás sikertelen kérelmi sebességének közel valós időben történő bejelentésére szolgál normál viselkedés.

    Az intelligens észlelés automatikusan módosítja a küszöbértéket az érvényben lévő feltételekre válaszul.

    Az intelligens észlelés elindítja a diagnosztikai munkát.
* A [teljesítménybeli rendellenességek intelligens észlelése](proactive-performance-diagnostics.md) a gépi intelligenciával észleli a mérőszámokban szokatlan mintákat, és nincs szükség konfigurációra. A meghibásodási rendellenességek intelligens észlelése miatt azonban a teljesítménybeli rendellenességek intelligens észlelésének célja, hogy megkeresse a használat sokrétű, esetlegesen kiszolgált szegmenseit, például egy adott böngésző adott lapjain. Az elemzés naponta történik, és ha bármilyen eredmény található, valószínűleg sokkal kevésbé sürgős, mint a riasztás. Ezzel szemben a hibákra vonatkozó anomáliák elemzése folyamatosan történik a bejövő telemetria, és perceken belül értesítést kap, ha a kiszolgáló meghibásodási aránya nagyobb a vártnál.

## <a name="if-you-receive-a-smart-detection-alert"></a>Ha intelligens észlelési riasztást kap
*Miért kaptam ezt a riasztást?*

* A sikertelen kérelmek arányának rendellenes emelkedését észlelte a korábbi időszak normál alapértékéhez képest. A hibák és a kapcsolódó telemetria elemzése után úgy gondoljuk, hogy probléma merült fel.

*Az értesítésben feltétlenül probléma merült fel?*

* Megpróbálunk riasztást kapni az alkalmazások megszakadásával vagy romlásával kapcsolatban, de csak teljes mértékben tisztában lehet az alkalmazásra és a felhasználókra gyakorolt hatásokkal.

*Srácok, hogy megnézzük az adataimat?*

* Nem. A szolgáltatás teljesen automatikus. Csak Ön kapja meg az értesítéseket. Az adatai [magánjellegűek](../../azure-monitor/app/data-retention-privacy.md).

*Elő kell fizetnünk erre a riasztásra?*

* Nem. Minden kérelem telemetria küldő alkalmazás rendelkezik az intelligens észlelési riasztási szabállyal.

*Lemondható vagy lekérhetem a munkatársaknak küldött értesítéseket?*

* Igen, a riasztási szabályok területen kattintson az intelligens észlelési szabályra a konfigurálásához. Letilthatja a riasztást, vagy megváltoztathatja a riasztás címzettjeit.

*Elfelejtettem az e-mailt. Hol találhatók az értesítések a portálon?*

* A tevékenység naplófájljaiban. Az Azure-ban nyissa meg a Application Insights erőforrást az alkalmazáshoz, majd válassza a tevékenységek naplóit.

*Néhány riasztás az ismert problémákról szól, és nem szeretném fogadni őket.*

* A rendszer riasztást küld a várakozó fájlok elnyomásáról.

## <a name="next-steps"></a>Következő lépések
Ezek a diagnosztikai eszközök segítenek megvizsgálni a telemetria az alkalmazásból:

* [Metrika-kezelő](../../azure-monitor/app/metrics-explorer.md)
* [Keresési ablak](../../azure-monitor/app/diagnostic-search.md)
* [Analitika – hatékony lekérdezési nyelv](../../azure-monitor/log-query/get-started-portal.md)

Az intelligens észlelések teljesen automatikusak. De lehet, hogy néhány riasztást szeretne beállítani?

* [Manuálisan konfigurált metrikai riasztások](../../azure-monitor/app/alerts.md)
* [Rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md)
