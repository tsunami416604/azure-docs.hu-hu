---
title: Az Azure Application Insights rendelkezésre állási tesztek megoldása | Microsoft Docs
description: Webtesztek hibakeresése az Azure Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: ee64a8af35f938def94e369bdb400fed6e2798c0
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146593"
---
# <a name="troubleshooting"></a>Hibaelhárítás

Ez a cikk segítséget nyújt a rendelkezésre állás figyelése során esetlegesen előforduló gyakori hibák elhárításához.

## <a name="ssltls-errors"></a>SSL/TLS-hibák

|Tünet/hibaüzenet| Lehetséges okok|
|--------|------|
|Nem hozható létre SSL/TLS biztonságos csatorna  | Az SSL verziója. Csak a TLS 1,0, 1,1 és 1,2 támogatottak. **A SSLv3 nem támogatott.**
|TLS 1.2 rekord réteg: Riasztás (szint: Végzetes, leírás: Rossz rekord MAC)| [További információért](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)lásd a StackExchange szálat.
|A sikertelen URL-cím egy CDN (Content Delivery Network) | Ezt a CDN helytelen konfigurációja okozhatja |  

### <a name="possible-workaround"></a>Lehetséges Áthidaló megoldás

* Ha a problémát tapasztaló URL-címek mindig függő erőforrások, ajánlott letiltani a webes teszthez tartozó **függő kérelmek elemzését** .

## <a name="test-fails-only-from-certain-locations"></a>A teszt csak bizonyos helyekről sikertelen

|Tünet/hibaüzenet| Lehetséges okok|
|----|---------|
|A kapcsolódási kísérlet sikertelen volt, mert a csatlakoztatott fél egy adott idő elteltével nem válaszolt megfelelően.  | Bizonyos helyszíneken lévő tesztelési ügynököket tűzfal blokkolja.|
|    |Bizonyos IP-címek átirányítása a (terheléselosztó, Geo Traffic Manager, Azure Express Route) használatával történik. 
|    |Ha az Azure ExpressRoute-t használja, vannak olyan helyzetek, amikor a csomagokat el lehet dobni azokban az esetekben, amikor [aszimmetrikus útválasztás történik](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Időszakos tesztelési hiba a protokoll megsértése miatt

|Tünet/hibaüzenet| Lehetséges okok| Lehetséges megoldások |
|----|---------|-----|
|A kiszolgáló protokoll megsértését véglegesítette. Szakasz = ResponseHeader details = CR után az LF utasításnak kell lennie | Ez akkor fordul elő, ha a rendszer hibásan formázott fejléceket észlel. Előfordulhat, hogy egyes fejlécek nem használják a CRLF a sor végére, ami sérti a HTTP-specifikációt. Application Insights érvényesíti ezt a HTTP-specifikációt, és a helytelen formátumú fejlécekkel nem tud válaszokat.| a. A hibás kiszolgálók kijavításához vegye fel a kapcsolatot a webhely gazdagépének szolgáltatójával/CDN-szolgáltatóval. <br> b. Ha a sikertelen kérelmek erőforrások (például a stíluslapok, a képek, a parancsfájlok), akkor érdemes lehet letiltani a függő kérelmek elemzését. Ne feledje, hogy ha ezt megteszi, elveszíti a fájlok rendelkezésre állásának figyelését).

> [!NOTE]
> Előfordulhat, hogy az URL-cím nem sikerül a HTTP-fejlécek nyugodt érvényesítését biztosító böngészőkön. A hiba részletes leírását a következő blogbejegyzésben találja: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Gyakori hibaelhárítási kérdések

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>A webhely jól néz ki, de a tesztek sikertelenek? Miért Application Insights a riasztás?

   * A tesztben engedélyezve vannak a **függő kérelmek** elemzése? Ez szigorú ellenőrzési eredményeket eredményez, például szkripteket, képeket stb. Előfordulhat, hogy az ilyen típusú hibák nem észlelhetők a böngészőben. Ellenőrizze az összes képet, szkriptet, stíluslapot és a lap által betöltött többi fájlt. Ha bármelyikük meghibásodik, a teszt sikertelenként lesz jelentve, még akkor is, ha a fő HTML-oldal probléma nélkül betöltődik. Ha a tesztet ilyen erőforrás-hibákra szeretné leegyszerűsíteni, egyszerűen törölje a függő kérelmek elemzését a tesztelési konfigurációból.

   * Annak érdekében, hogy csökkentse a zaj esélyét az átmeneti hálózati visszavertség stb. esetében, ellenőrizze, hogy be van-e jelölve az újrapróbálkozások tesztelési hibákhoz beállítás Azt is megteheti, hogy több helyről is teszteli, és ennek megfelelően kezeli a riasztási szabály küszöbértékét, hogy megakadályozza az indokolatlan riasztásokat okozó helyadatok

   * Kattintson bármelyik piros pontra a rendelkezésre állási élményben, vagy a keresési Explorer bármely rendelkezésre állási hibája alapján, és tekintse meg, hogy miért jelentettük be a hibát. A teszt eredménye, valamint a korrelált kiszolgálóoldali telemetria (ha engedélyezve van) segít megérteni, miért nem sikerült a teszt. Az átmeneti problémák gyakori okai a hálózati vagy a kapcsolatok problémái.

   * A teszt időkorlátja? 2 perc múlva megszakítjuk a teszteket. Ha a ping vagy a többlépéses teszt 2 percnél hosszabb időt vesz igénybe, a rendszer hibát jelez. Érdemes lehet többre feltörni a tesztet, amely rövidebb időtartamokban is elvégezhető.

   * Az összes helyszín hibát jelzett, vagy csak néhányat? Ha csak egyes jelentett hibák történtek, a hálózati/CDN-problémák miatt előfordulhat. A piros pontokra kattintva könnyebben megismerheti, miért jelentettek hibát a hely.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Nem kaptam meg e-mailt, amikor a riasztást aktiváltam, vagy megoldottam vagy mindkettőt?

A klasszikus riasztások konfigurációjában ellenőrizze, hogy az e-mailek közvetlenül szerepelnek-e a listában, vagy a terjesztési lista az értesítések fogadására van konfigurálva. Ha igen, akkor a terjesztési lista konfigurációjában ellenőrizze, hogy fogadhat-e külső e-maileket. Ellenőrizze azt is, hogy a levelezési rendszergazdája rendelkezhet-e a problémát okozó házirendekkel.

### <a name="i-did-not-receive-the-webhook-notification"></a>Nem kaptam meg a webhook-értesítést?

Győződjön meg arról, hogy a webhook-értesítést fogadó alkalmazás elérhető, és sikeresen feldolgozza a webhook-kérelmeket. További információért tekintse meg [ezt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) a témakört.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Időnkénti tesztelési hiba történt egy protokoll megsértésével kapcsolatban?

A „protokollmegsértés... A CR karakter után LF karakternek kell következnie” hiba a kiszolgáló (vagy a függőségek) problémáját jelzi. Ez akkor történik, amikor hibás formátumú fejlécek vannak beállítva a válaszban. Ezt a terheléselosztók vagy a CDN-ek okozhatják. Konkrétan előfordulhat, hogy egyes fejlécek nem használják a CRLF a sor végére, ami megsérti a HTTP-specifikációt, ezért a .NET webkérési szinten nem végez ellenőrzést. Vizsgálja meg, hogy milyen válaszokat tartalmaz a helyszíni fejlécek.

> [!NOTE]
> Előfordulhat, hogy az URL-cím nem sikerül a HTTP-fejlécek nyugodt érvényesítését biztosító böngészőkön. A hiba részletes leírását a következő blogbejegyzésben találja: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nem látok kapcsolódó kiszolgálóoldali telemetria a tesztelési hibák diagnosztizálásához? *

Ha a kiszolgálóoldali alkalmazásához be van állítva az Application Insights, akkor ezt okozhatja az, hogy [mintavételezés](../../azure-monitor/app/sampling.md) van folyamatban. Válasszon másik rendelkezésre állási eredményt.

### <a name="can-i-call-code-from-my-web-test"></a>Meghívhatok egy kódot a webes tesztből?

Nem. A .webtest fájlnak tartalmaznia kell a teszt lépéseit. Nem hívhat meg más teszteket, és nem használhat hurkokat. Azonban számos beépülő modul van, amely hasznos lehet.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Van különbség a „webes tesztek” és a „rendelkezésre állási tesztek” között?

A két kifejezés hasonló értelmű, felcserélhető. A „rendelkezésre állási teszt” általánosabb fogalom, amely az URL-ping tesztek mellett a többlépéses webes teszteket is magában foglalja.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Rendelkezésre állási teszteket szeretnék használni a tűzfal mögött futó belső kiszolgálón.

   Két lehetséges megoldás létezik:

   * Konfigurálhatja úgy a tűzfalat, hogy az engedélyezze a [webes tesztügynökök IP-címeiről](../../azure-monitor/app/ip-addresses.md) érkező bejövő kéréseket.
   * Saját kód megírásával rendszeresen ellenőrizheti a belső kiszolgálót. Futtassa a kódot a tűzfal mögötti tesztkiszolgáló háttérfolyamataként. A tesztelési folyamat az eredményeket a Core SDK-csomag [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API-jával küldheti el az Application Insightsba. Ehhez szükség van arra, hogy a tesztkiszolgáló kimenő hozzáféréssel rendelkezzen az Application Insights betöltési végpontjához, de ez jóval kisebb biztonsági kockázatot jelent a bejövő kérések engedélyezéséhez képest. Az eredmények megjelennek a rendelkezésre állási webes tesztek paneleken, bár a felhasználói élmény kis mértékben le lesz egyszerűsítve a portálon keresztül létrehozott tesztek esetében. Az egyéni rendelkezésre állási tesztek az elemzés, a keresés és a mérőszámok rendelkezésre állási eredményeiként is megjelennek.

### <a name="uploading-a-multi-step-web-test-fails"></a>A többlépéses teszt feltöltése sikertelen

Ennek valamilyen oka lehet:
   * A méretkorlát: 300 KB.
   * A hurkok nem támogatottak.
   * A más webes tesztekre mutató hivatkozások nem támogatottak.
   * Az adatforrások nem támogatottak.

### <a name="my-multi-step-test-doesnt-complete"></a>A többlépéses teszt nem fejeződik be

Egy teszt legfeljebb 100 kérelemből állhat. Emellett a teszt leáll, ha két percnél hosszabb ideig fut.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Hogyan futtathatok tesztet ügyféltanúsítványokkal?

Ez jelenleg nem támogatott.

## <a name="who-receives-the-classic-alert-notifications"></a>Kik kapják meg a (klasszikus) riasztási értesítéseket?

Ez a szakasz csak a klasszikus riasztásokra vonatkozik, és segít optimalizálni a riasztási értesítéseket, így biztosítva, hogy csak a kívánt címzettek kapják meg az értesítéseket. Ha többet szeretne megtudni a [klasszikus riasztások](../platform/alerts-classic.overview.md)közötti különbségről és az új riasztások élményéről, tekintse meg a [riasztások áttekintése című cikket](../platform/alerts-overview.md). A riasztások értesítésének vezérléséhez az új riasztások használatakor használjon [műveleti csoportokat](../platform/action-groups.md).

* A klasszikus riasztási értesítések esetében javasoljuk, hogy adott címzetteket használjon.

* A nem Y helyekről érkező hibákkal kapcsolatos riasztások esetén a **csoportos/csoportos** jelölőnégyzetes beállítás, ha engedélyezve van, a a rendszergazdai/társ-rendszergazdai szerepkörökkel rendelkező felhasználóknak küldi a felhasználókat.  Lényegében az _előfizetés_ _összes_ rendszergazdája értesítést kap.

* Ha engedélyezve van a rendelkezésre állási metrikákkal kapcsolatos riasztások, akkor a **csoportos vagy csoportos** jelölőnégyzet be van jelölve, ha engedélyezve van, az előfizetésben tulajdonos, közreműködő vagy olvasó szerepkörrel rendelkező felhasználók számára küldi el a rendszer. _Minden_ olyan felhasználó, aki hozzáféréssel rendelkezik az előfizetéshez, a Application Insights erőforrás hatókörben van, és értesítést fog kapni. 

> [!NOTE]
> Ha jelenleg a **tömeges/csoportos** jelölőnégyzetet használja, és letiltja, akkor nem fogja tudni visszaállítani a változást.

Ha a felhasználókat a szerepköreik alapján kell értesítenie, használja az új riasztási élmény/közel valós idejű riasztásokat. A [műveleti csoportokkal](../platform/action-groups.md)e-mailes értesítéseket állíthat be a felhasználók számára a közreműködő/tulajdonos/olvasó szerepkörök bármelyikével (egyetlen lehetőségként nem kombinálva).

## <a name="next-steps"></a>További lépések

* [Többlépéses webes tesztelés](availability-multistep.md)
* [URL-ping tesztek](monitor-web-app-availability.md)
