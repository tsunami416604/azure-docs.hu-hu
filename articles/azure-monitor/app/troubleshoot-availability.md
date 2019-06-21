---
title: Az Azure Application Insights rendelkezésre állási teszt hibáinak elhárítása |} A Microsoft Docs
description: Hibaelhárítás az Azure Application Insights webes teszteket. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 87bc87d7d105d581f0143e87044fb0337c0fd7f6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305138"
---
# <a name="troubleshooting"></a>Hibaelhárítás

Ez a cikk segít rendelkezésre állásának figyelésére szolgáló használatakor előforduló gyakori problémák megoldása.

## <a name="ssltls-errors"></a>Az SSL/TLS-hibák

|Jelenség/hibaüzenet| Lehetséges okok|
|--------|------|
|Nem sikerült létrehozni az SSL/TLS Secure Channel  | SSL-verzió. Csak a TLS 1.0, 1.1 és 1.2-es támogatottak. **SSLv3 nem támogatott.**
|TLSv1.2 rekord réteg: Riasztás (szint: Végzetes, Leírás: Hibás rekord MAC)| Lásd a StackExchange szálat [bővebben](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|URL-cím, amelyet nem az, hogy a CDN (Tartalomkézbesítési hálózat) | Ezt okozhatja egy hibás, a CDN-en |  

### <a name="possible-workaround"></a>Lehetséges megoldás

* Ha az URL-címeket a hibát tapasztaló mindig a tőle függő erőforrások, ajánlott letiltani a **függő kérelmek elemzése** a webes teszt.

## <a name="test-fails-only-from-certain-locations"></a>Teszt sikertelen, csak meghatározott helyekről

|Jelenség/hibaüzenet| Lehetséges okok|
|----|---------|
|Kapcsolódási kísérlet sikertelen volt, mert a csatlakoztatott fél nem válaszolt egy idő után  | Az egyes helyek tesztügynök tűzfal blokkolja.|
|    |Az egyes IP-címek átirányítási (Terheléselosztók, a földrajzi forgalom-kezelők Azure Express Route.) keresztül történik 
|    |Azure ExpressRoute segítségével, hogy vannak-e forgatókönyvek, ahol csomagok törölhetők azokban az esetekben, ahol [az aszimmetrikus útválasztás akkor fordul elő](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Az időszakos teszt meghiúsult egy protokollmegsértési hiba az

|Jelenség/hibaüzenet| Lehetséges okok|
|----|---------|
protokoll megsértése CR kell következnie | Ez akkor fordul elő, amikor hibás formátumú fejlécek vannak észlelt. Kifejezetten néhány fejléc nem a CRLF jelzi a sor végét, ami a HTTP-specifikációnak sérti végén, és ezért lesz az ellenőrzés meghiúsul a .NET WebRequest szintjén.
 || Ez is okozhatja terheléselosztók vagy CDN.

> [!NOTE]
> Az URL nem hiúsul meg, amelyek olyan böngészőkön a HTTP-fejléceket. A hiba részletes leírását a következő blogbejegyzésben találja: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Gyakori hibaelhárítási kérdések

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Webhely megfelelőnek tűnik, de látom tesztelési hibák? Miért van az Application Insights riasztási velem?

   * A teszt rendelkezik **függő kérelmek elemzése** engedélyezve van? Erőforrások, például parancsfájlokat, szigorú ellenőrzés eredményez, amely képeket stb. Előfordulhat, hogy az ilyen típusú hibák nem észrevehető, egy böngészőben. Ellenőrizze az összes képet, szkriptet, stíluslapot és a lap által betöltött többi fájlt. Ha ezek közül bármelyik hibás, a teszt van jelentve, még akkor is, ha a fő HTML-oldal hiba nélkül betölt. Az ilyen erőforrás sikertelen teszt desensitize, csak törölje a jelölést elemezni a függő kérelmeit a konfigurálása.

   * Átmeneti hálózati jelekből stb. származó zajok csökkentéséhez biztosítása a konfigurációs be van jelölve, a teszt hibáinak újrapróbálkozások engedélyezése. Teszt több helyről is, és meggátolja az indokolatlan riasztásokat okozó helyspecifikus problémák elkerülése érdekében megfelelően kezelheti a riasztási szabály küszöbértékét.

   * Kattintson bármelyik a piros pötty a rendelkezésre állási származó, vagy minden rendelkezésre állási hiba a keresési ablak miért azt jelenti, hogy a hiba részleteinek megtekintéséhez. A vizsgálat eredményének, valamint a kapcsolódó kiszolgálóoldali telemetriát (Ha engedélyezve van) segíthet megérteni, hogy miért a teszt sikertelen volt. Átmeneti hibák gyakori okai hálózati és kapcsolati problémák.

   * A teszt időtúllépése volt? Tesztek 2 perc múlva le azt. A ping vagy többlépéses teszt hosszabb, mint 2 percet vesz igénybe, ha azt hibaként tartozik. Vegye figyelembe, hogy a teszt ossza több címkéket, is elvégezheti a rövidebb időtartam.

   * Hiba, vagy csak néhány jelentettek az összes hely? Ha csak néhány jelentett hibák, valószínűleg hálózati/a CDN-hibák miatt. Újra piros pontra kattint segíthet megérteni, hogy miért érdemes az a hely jelentett hibák.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>E nem tudták beszerezni, ha a riasztás aktiválódik, vagy nem oldják fel e-mailt vagy mindkettő?

Ellenőrizze a klasszikus riasztások beállítását, ellenőrizze az e-maileket közvetlenül szerepel a listán, vagy a terjesztési lista értesítések fogadására van beállítva. Ha igen, majd ellenőrizze a terjesztési lista konfigurációját megkaphatja a külső e-mailek megerősítéséhez. A levelezési rendszergazda előfordulhat, hogy van-e bármilyen konfigurált szabályzatok, előfordulhat, hogy a probléma kiváltó is ellenőrizheti.

### <a name="i-did-not-receive-the-webhook-notification"></a>Nem kaptam a webhook értesítést?

Ellenőrizze, hogy az alkalmazás a webhook értesítés érhető el, és sikeresen dolgozza fel a webhook-kérelmeket. Lásd: [ez](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) további információt.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Az időszakos teszt meghiúsult egy protokollmegsértési hiba a?

A „protokollmegsértés... A CR karakter után LF karakternek kell következnie” hiba a kiszolgáló (vagy a függőségek) problémáját jelzi. Ez akkor történik, amikor hibás formátumú fejlécek vannak beállítva a válaszban. Ezt a terheléselosztók vagy a CDN-ek okozhatják. Pontosabban néhány fejléc előfordulhat, hogy nem a CRLF jelzi a sor, amely megsérti a HTTP-specifikációnak, és ezért az ellenőrzés a .NET WebRequest szintjén meghiúsul. Vizsgálja meg a választ, előfordulhat, hogy nem megfelelő fejlécek.

> [!NOTE]
> Az URL nem hiúsul meg, amelyek olyan böngészőkön a HTTP-fejléceket. A hiba részletes leírását a következő blogbejegyzésben találja: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nem látható, hogy minden kapcsolódó kiszolgálóoldali telemetriát teszt hibáinak diagnosztizálása? *

Ha a kiszolgálóoldali alkalmazásához be van állítva az Application Insights, akkor ezt okozhatja az, hogy [mintavételezés](../../azure-monitor/app/sampling.md) van folyamatban. Válasszon ki egy másik rendelkezésre állási eredményt.

### <a name="can-i-call-code-from-my-web-test"></a>Meghívhatok egy kódot a webes tesztből?

Nem. A .webtest fájlnak tartalmaznia kell a teszt lépéseit. Nem hívhat meg más teszteket, és nem használhat hurkokat. Azonban számos beépülő modul van, amely hasznos lehet.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Van különbség a „webes tesztek” és a „rendelkezésre állási tesztek” között?

A két kifejezés hasonló értelmű, felcserélhető. A „rendelkezésre állási teszt” általánosabb fogalom, amely az URL-ping tesztek mellett a többlépéses webes teszteket is magában foglalja.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Rendelkezésre állási teszteket szeretnék használni a tűzfal mögött futó belső kiszolgálón.

   Két lehetséges megoldás létezik:

   * Konfigurálhatja úgy a tűzfalat, hogy az engedélyezze a [webes tesztügynökök IP-címeiről](../../azure-monitor/app/ip-addresses.md) érkező bejövő kéréseket.
   * Saját kód megírásával rendszeresen ellenőrizheti a belső kiszolgálót. Futtassa a kódot a tűzfal mögötti tesztkiszolgáló háttérfolyamataként. A tesztelési folyamat az eredményeket a Core SDK-csomag [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API-jával küldheti el az Application Insightsba. Ehhez szükség van arra, hogy a tesztkiszolgáló kimenő hozzáféréssel rendelkezzen az Application Insights betöltési végpontjához, de ez jóval kisebb biztonsági kockázatot jelent a bejövő kérések engedélyezéséhez képest. Az eredmények nem jelennek meg a rendelkezésre állási webes tesztek paneljein, de rendelkezésre állási eredményként megtekinthetők az Elemzés, a Keresés és a Metrikaböngésző panelen.

### <a name="uploading-a-multi-step-web-test-fails"></a>A többlépéses teszt feltöltése sikertelen

Néhány ok, Ez azért fordulhat elő:
   * A méretkorlát: 300 KB.
   * A hurkok nem támogatottak.
   * A más webes tesztekre mutató hivatkozások nem támogatottak.
   * Az adatforrások nem támogatottak.

### <a name="my-multi-step-test-doesnt-complete"></a>A többlépéses teszt nem fejeződik be

Egy teszt legfeljebb 100 kérelemből állhat. A teszt leáll, ha két percnél tovább fut.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Hogyan futtathatok tesztet ügyféltanúsítványokkal?

Ez jelenleg nem támogatott.

## <a name="who-receives-the-classic-alert-notifications"></a>Ki kapja a (klasszikus) riasztási értesítések?

Ez a szakasz csak klasszikus riasztások vonatkozik, és segít optimalizálni a riasztási értesítések biztosítják, hogy csak a kívánt címzettek megkapják az értesítéseket. Ismerje meg jobban a különbség a [klasszikus riasztások](../platform/alerts-classic.overview.md), majd tekintse át a riasztások új kezelőfelülete a [riasztások áttekintő cikkben](../platform/alerts-overview.md). Riasztás szabályozhatja az új riasztások az értesítési használata élményt [Műveletcsoportok](../platform/action-groups.md).

* A klasszikus riasztási értesítéseket meghatározott címzettek használatát javasoljuk.

* Sikertelen X kívül Y helyen, a riasztások a **tömeges/csoport** jelölőnégyzetet, a beállítást, ha engedélyezve van, küld rendszergazda/társadminisztrátor szerepkörrel rendelkező felhasználók számára.  Lényegében _összes_ rendszergazdái a _előfizetés_ értesítéseket kap.

* A rendelkezésre állási metrikák riasztásaihoz a **tömeges/csoport** jelölőnégyzetet, a beállítás engedélyezve van, ha elküldi az előfizetésben tulajdonosi, közreműködői vagy olvasói szerepkörrel rendelkező felhasználók. Gyakorlatilag _összes_ az előfizetés az Application Insights-erőforráshoz hozzáféréssel rendelkező felhasználók terjed ki, és értesítéseket kap. 

> [!NOTE]
> Ha jelenleg használja a **tömeges/csoport** jelölőnégyzetet, a beállítást, és tiltsa le, nem állíthatja vissza a módosítást.

Az új riasztás élmény/közel valós idejű riasztások használja, ha értesítse a felhasználókat a szerepkörökhöz alapján kell. A [Műveletcsoportok](../platform/action-groups.md), bármelyik (nem egyesíthet egyetlen lehetőségként) közreműködői vagy tulajdonosi vagy olvasó szerepkört konfigurálható e-mail értesítések küldéséhez felhasználók számára.

## <a name="next-steps"></a>További lépések

* [Többlépéses webes tesztelése](availability-multistep.md)
* [URL-ping tesztek](monitor-web-app-availability.md)
