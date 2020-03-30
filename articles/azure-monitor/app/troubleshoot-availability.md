---
title: Az Azure Application Insights rendelkezésre állási tesztjeinek hibáival kapcsolatos hibák | Microsoft dokumentumok
description: Webes tesztek az Azure Application Insights ban– problémamegoldás. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2019
ms.reviewer: sdash
ms.openlocfilehash: f135aa6c0a4a55f8a42fd858572cc811e25b27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671103"
---
# <a name="troubleshooting"></a>Hibaelhárítás

Ez a cikk segít a rendelkezésre állásfigyelés használata során előforduló gyakori problémák elhárításában.

## <a name="ssltls-errors"></a>SSL/TLS hibák

|Tünet/hibaüzenet| Lehetséges okok|
|--------|------|
|Nem lehet létrehozni az SSL/TLS biztonságos csatornát  | SSL verzió. Csak a TLS 1.0, 1.1 és 1.2 támogatott. **Az SSLv3 nem támogatott.**
|TLSv1.2 Record Layer: Riasztás (Szint: Végzetes, Leírás: Bad Record MAC)| További információt a StackExchange-szál című [témakörben talál.](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)
|A meghibásodott URL-cím egy CDN (Content Delivery Delivery Network) | Ennek oka lehet a CDN helytelen konfigurálása |  

### <a name="possible-workaround"></a>Lehetséges megoldás

* Ha a problémát tapasztaló URL-címek mindig a függő erőforrások, ajánlott letiltani **az elemzésfüggő kérelmek** a webes teszt.

## <a name="test-fails-only-from-certain-locations"></a>A teszt csak bizonyos helyekről sikertelen

|Tünet/hibaüzenet| Lehetséges okok|
|----|---------|
|A csatlakozási kísérlet nem sikerült, mert a csatlakoztatott fél egy idő után nem válaszolt megfelelően  | Bizonyos helyeken a tesztügynököket tűzfal blokkolja.|
|    |Bizonyos IP-címek átirányítása a (terheléselosztók, földrajzi forgalomkezelők, Az Azure Express Route útvonalon keresztül történik).) 
|    |Az Azure ExpressRoute használata esetén vannak olyan forgatókönyvek, ahol a csomagok eldobhatók azokban az esetekben, amikor [aszimmetrikus útválasztás történik.](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)|

## <a name="test-failure-with-a-protocol-violation-error"></a>Teszthiba protokollmegsértési hibával

|Tünet/hibaüzenet| Lehetséges okok| Lehetséges megoldások |
|----|---------|-----|
|A kiszolgáló protokollmegsértéset követett el. Section=ResponseHeader Detail=CR kell követnie LF | Ez akkor fordul elő, ha hibás fejléceket észlel. Pontosabban előfordulhat, hogy egyes fejlécek nem a CRLF-et használják a sor végének jelzésére, ami sérti a HTTP-specifikációt. Az Application Insights kényszeríti ezt a HTTP-specifikációt, és hibásan formázott fejlécekkel sikertelen választ ad.| a. Lépjen kapcsolatba a weboldal gazdaszolgáltatójával / CDN szolgáltatóval a hibás szerverek javításához. <br> b. Abban az esetben, ha a sikertelen kérelmek erőforrások (pl. stílusfájlok, képek, parancsfájlok), érdemes lehet letiltani a függő kérelmek elemzését. Ne feledje, ha ezt elveszíti a képességét, hogy figyelemmel kíséri a rendelkezésre álló fájlokat).

> [!NOTE]
> Előfordulhat, hogy az URL-cím nem sikertelen a HTTP-fejlécek nyugodt érvényesítésű böngészőiben. A hiba részletes leírását a következő blogbejegyzésben találja: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Gyakori hibaelhárítási kérdések

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>A webhely megfelelőnek tűnik, de tesztelési hibák láthatók? Miért figyelmeztet az Application Insights?

   * Engedélyezve van a teszt **elemzésfüggő kérelmek?** Ez azt eredményezi, hogy szigorú ellenőrzést források, mint a szkriptek, képek, stb Előfordulhat, hogy az ilyen típusú hibák nem észrevehetők a böngészőben. Ellenőrizze az összes képet, szkriptet, stíluslapot és a lap által betöltött többi fájlt. Ha ezek közül bármelyik sikertelen, a teszt sikertelenként jelenik meg, még akkor is, ha a fő HTML-oldal probléma nélkül betöltődik. Ha a tesztet ilyen erőforrás-hibákra szeretné érzéketlenné tenni, egyszerűen törölje a jelet a tesztkonfiguráció elemzésfüggő kérelmei jelölőnégyzetből.

   * Az átmeneti hálózati blips stb. Több helyről is tesztelheti, és ennek megfelelően kezelheti a riasztási szabály küszöbértékét, hogy megakadályozza a helyspecifikus problémákat, amelyek indokolatlan riasztásokat okoznak.

   * Kattintson a rendelkezésre állási felület egyik piros pontára, vagy a Kereséskezelő bármely rendelkezésre állási hibájára, hogy megtekintse a hiba jelentésének részleteit. A teszt eredménye, valamint a korrelált kiszolgálóoldali telemetriai adatok (ha engedélyezve van) segít megérteni, hogy miért a teszt nem sikerült. Az átmeneti problémák gyakori okai a hálózati vagy csatlakozási problémák.

   * A teszt idő-out? 2 perc után megszakítjuk a teszteket. Ha a ping- vagy többlépéses teszt 2 percnél tovább tart, hibaként jelentjük. Fontolja meg a teszt több olyan ra bontását, amelyek rövidebb időtartamokban is elvégezhetők.

   * Minden hely hibát jelentett, vagy csak néhányat? Ha csak néhány jelentett hiba, annak oka lehet a hálózati/CDN-problémák. Ismét, kattintson a piros pont segít megérteni, hogy miért a hely jelentett hibákat.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Nem kaptam e-mailt, amikor a riasztás aktiválódott, vagy megoldódott, vagy mindkettő?

Ellenőrizze a klasszikus riasztások konfigurációját, hogy az e-mail közvetlenül szerepel-e a listában, vagy egy terjesztési lista van beállítva az értesítések fogadására. Ha ez így van, majd ellenőrizze a terjesztési lista konfigurációját, hogy ellenőrizze, hogy képes-e fogadni a külső e-maileket. Azt is ellenőrizze, hogy a levelezési rendszergazdának vannak-e olyan házirendjei, amelyek ezt a problémát okozhatják.

### <a name="i-did-not-receive-the-webhook-notification"></a>Nem kaptam meg a webhook értesítést?

Ellenőrizze, hogy a webhook-értesítést fogadó alkalmazás elérhető-e, és sikeresen feldolgozza a webhook-kérelmeket. További információkért [lásd ezt.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Az időszakos teszt meghiúsult egy protokollmegsértési hibával?

A „protokollmegsértés... A CR karakter után LF karakternek kell következnie” hiba a kiszolgáló (vagy a függőségek) problémáját jelzi. Ez akkor történik, amikor hibás formátumú fejlécek vannak beállítva a válaszban. Ezt a terheléselosztók vagy a CDN-ek okozhatják. Pontosabban előfordulhat, hogy egyes fejlécek nem használják a CRLF-et a sor végének jelzésére, ami sérti a HTTP-specifikációt, és ezért a .NET WebRequest szintjén nem érvényesíthető. Vizsgálja meg a válasz a direktfejlécek, amelyek megsérthetik.

> [!NOTE]
> Előfordulhat, hogy az URL-cím nem sikertelen a HTTP-fejlécek nyugodt érvényesítésű böngészőiben. A hiba részletes leírását a következő blogbejegyzésben találja: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nem látok kapcsolódó kiszolgálóoldali telemetriai adatokat a teszthibák diagnosztizálására?*

Ha a kiszolgálóoldali alkalmazásához be van állítva az Application Insights, akkor ezt okozhatja az, hogy [mintavételezés](../../azure-monitor/app/sampling.md) van folyamatban. Válasszon másik rendelkezésre állási eredményt.

### <a name="can-i-call-code-from-my-web-test"></a>Meghívhatok egy kódot a webes tesztből?

Nem. A .webtest fájlnak tartalmaznia kell a teszt lépéseit. Nem hívhat meg más teszteket, és nem használhat hurkokat. Azonban számos beépülő modul van, amely hasznos lehet.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Van különbség a „webes tesztek” és a „rendelkezésre állási tesztek” között?

A két kifejezés hasonló értelmű, felcserélhető. A „rendelkezésre állási teszt” általánosabb fogalom, amely az URL-ping tesztek mellett a többlépéses webes teszteket is magában foglalja.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Rendelkezésre állási teszteket szeretnék használni a tűzfal mögött futó belső kiszolgálón.

   Két lehetséges megoldás létezik:

   * Konfigurálhatja úgy a tűzfalat, hogy az engedélyezze a [webes tesztügynökök IP-címeiről](../../azure-monitor/app/ip-addresses.md) érkező bejövő kéréseket.
   * Saját kód megírásával rendszeresen ellenőrizheti a belső kiszolgálót. Futtassa a kódot a tűzfal mögötti tesztkiszolgáló háttérfolyamataként. A tesztelési folyamat az eredményeket a Core SDK-csomag [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API-jával küldheti el az Application Insightsba. Ehhez szükség van arra, hogy a tesztkiszolgáló kimenő hozzáféréssel rendelkezzen az Application Insights betöltési végpontjához, de ez jóval kisebb biztonsági kockázatot jelent a bejövő kérések engedélyezéséhez képest. Az eredmények megjelennek a rendelkezésre álló webes tesztek blades bár a tapasztalat kissé leegyszerűsödik a portálon keresztül létrehozott tesztekhez rendelkezésre álló. Az egyéni rendelkezésreállási tesztek az Analytics, a Keresés és a Metrikák elérhetőségi eredményeiként is megjelennek.

### <a name="uploading-a-multi-step-web-test-fails"></a>A többlépéses teszt feltöltése sikertelen

Ennek néhány oka előfordulhat:
   * A méretkorlát: 300 KB.
   * A hurkok nem támogatottak.
   * A más webes tesztekre mutató hivatkozások nem támogatottak.
   * Az adatforrások nem támogatottak.

### <a name="my-multi-step-test-doesnt-complete"></a>A többlépéses teszt nem fejeződik be

Egy teszt legfeljebb 100 kérelemből állhat. A vizsgálat is leáll, ha két percnél hosszabb ideig tart.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Hogyan futtathatok tesztet ügyféltanúsítványokkal?

Ez jelenleg nem támogatott.

## <a name="who-receives-the-classic-alert-notifications"></a>Ki kapja meg a (klasszikus) riasztási értesítéseket?

Ez a szakasz csak a klasszikus riasztásokra vonatkozik, és segít optimalizálni a riasztási értesítéseket annak érdekében, hogy csak a kívánt címzettek kapjanak értesítéseket. Ha többet szeretne megtudni a [klasszikus riasztások](../platform/alerts-classic.overview.md)és az új riasztások közötti különbségről, olvassa el a [riasztások áttekintéséről szóló cikket.](../platform/alerts-overview.md) Az új riasztások felhasználói felületében a riasztási értesítések szabályozásához [használja a műveletcsoportokat.](../platform/action-groups.md)

* Azt javasoljuk, hogy a klasszikus riasztási értesítések adott címzettek használatát.

* Az Y-helyekről érkező X-ből érkező hibákra vonatkozó riasztások esetén a **tömeges/csoport** jelölőnégyzet, ha engedélyezve van, elküldi a rendszergazdai/társadminisztrátori szerepkörrel rendelkező felhasználóknak.  Lényegében az _előfizetés_ _összes_ rendszergazdája értesítést kap.

* A rendelkezésre állási metrikákra vonatkozó riasztások esetén a **tömeges/csoport** jelölőnégyzet bevan jelölve, ha engedélyezve van, elküldi az előfizetésben tulajdonosi, közreműködői vagy olvasói szerepkörrel rendelkező felhasználóknak. Valójában _az Application_ Insights-erőforrás hoz az előfizetéshez hozzáféréssel rendelkező összes felhasználó hatóköre van, és értesítéseket kap. 

> [!NOTE]
> Ha jelenleg a **tömeges/csoport** jelölőnégyzetet használja, és letiltja azt, akkor nem tudja visszaállítani a módosítást.

Használja az új riasztási élményt/közel valós idejű riasztásokat, ha a szerepkörük alapján értesítenie kell a felhasználókat. A [műveletcsoportok](../platform/action-groups.md)segítségével beállíthatja az e-mail értesítéseket a közreműködői/tulajdonosi/olvasói szerepkörrel rendelkező felhasználók számára (nem kombinálva egyetlen lehetőségként).

## <a name="next-steps"></a>További lépések

* [Többlépéses webes tesztelés](availability-multistep.md)
* [URL-pingtesztek](monitor-web-app-availability.md)
