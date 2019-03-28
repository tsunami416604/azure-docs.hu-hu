---
title: Az Azure Application Gateway webalkalmazási tűzfal bemutatása
description: Ez a cikk a webalkalmazási tűzfal (WAF) áttekintést nyújt az Application Gateway számára
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 830513a03bd65ca14cb0938ae599a676f1bb3bca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518184"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Az Azure Application Gateway webalkalmazási tűzfal

Az Azure Application Gateway webalkalmazási tűzfal (WAF), amely központi védelmet a webalkalmazásoknak a gyakori biztonsági rések ellen nyújt kínál. Webalkalmazások egyre inkább rosszindulatú támadásoknak, amelyek gyakran ismert biztonsági rések kihasználására vonatkozik. SQL-injektálás vagy többhelyes scripting a leggyakoribb támadások közé tartoznak.

Ilyen támadások megakadályozása az alkalmazás kódjában nagyobb kihívást jelent. Ehhez szigorúan betartandó, javítását és az alkalmazás topológiájának több rétegén figyelése is szükség. Központosított webalkalmazási tűzfal egyszerűbbé sokkal egyszerűbbek. A WAF is biztonságfelügyeletet rendszergazdái fenyegetések és a behatolás elleni védelem.

WAF-megoldás reagálhasson gyorsabb biztonsági fenyegetéseket azáltal, hogy központilag kijavítja az ismert biztonsági réseket, biztonságossá tétele minden egyes webalkalmazás helyett. A meglévő alkalmazásátjárókat egyszerűen fire üzenőfal-kompatibilis alkalmazásátjárók konvertálhatók.

Az Application Gateway WAF alapján [Core szabály beállítása (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0-s vagy 2.2.9-es verzióinak a a nyílt Web Application Security Project (OWASP). A WAF automatikusan frissíti a szerepeltetni kívánt új rések elleni védelem nincs szükség további konfigurálásra.

![Application Gateway WAF-diagram](./media/waf-overview/WAF1.png)

Az Application Gateway egy alkalmazáskézbesítési vezérlőt (ADC) gyűjtéséhez. Secure Sockets Layer (SSL)-lezárást, cookie-alapú munkamenet-affinitást, Ciklikus időszeleteléses terheléselosztást, tartalomalapú útválasztást, valamint több webhely és biztonsági fejlesztések kínál.

Application Gateway biztonsági fejlesztések között szerepel az SSL-házirend kezelése és a végpontok közötti SSL-támogatásról. Alkalmazás biztonsági támasztják Application Gateway WAF integrálását. Együttes használata a webes alkalmazások gyakori biztonsági rések elleni védelmet biztosít. Továbbá tartalmaz egy egyszerű konfigurálása központi helyen kezelheti.

## <a name="benefits"></a>Előnyök

Ez a szakasz ismerteti, amelyek az Application Gateway és a WAF-ja használatának legfontosabb előnyei.

### <a name="protection"></a>Védelem

* Megvédik a webalkalmazásokat a webes biztonsági résektől és támadásoktól háttér-kód módosítása nélkül.

* Több webalkalmazást védhet egy időben. Application Gateway-példány üzemeltetésére is képes, akár 20 webhelyet egy webalkalmazás-tűzfal által védett.

### <a name="monitoring"></a>Figyelés

* Valós idejű WAF-napló használatával figyelheti a támadásokat a webalkalmazások ellen. A naplók integrálva vannak [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) nyomon követése a WAF-riasztások, és könnyedén figyelheti a trendeket.

* Az Application Gateway WAF integrálva van az Azure Security Center. A Security Center az Azure-erőforrások biztonsági állapotának egy központi áttekintést nyújt a.

### <a name="customization"></a>Testreszabás

* Testre szabható a WAF-szabályok és az alkalmazás követelményeinek, és hamis pozitív jelzések elkerülése.

## <a name="features"></a>Szolgáltatások

- SQL-injection protection.
- Többhelyes webhelyközi védelme.
- Például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos más gyakori webes támadások elleni védelem.
- HTTP protokoll megsértése elleni védelem.
- Elleni HTTP protokollanomáliák, például hiányzó gazdagép-felhasználói ügynök, és fogadja el a fejlécek.
- Robotprogramok, webbejárók és képolvasók elleni védelem.
- Gyakori alkalmazások konfigurációs hibáinak észlelése (például Apache, IIS).
- Konfigurálható kérés mérete korlátozza az alsó és felső korlátja.
- Kizárási listák segítségével bizonyos attribútumainak WAF során hagyja ki. Ilyenek például az Active Directory-beszúrt jogkivonatokat, amelyek a hitelesítéshez, vagy a beírt jelszavak.

### <a name="core-rule-sets"></a>Alapvető szabálykészletek

Az Application Gateway támogatja a két szabálykészletek, CRS 3.0 és CRS 2.2.9. Ezek a szabályok megvédik a webalkalmazásokat a kártékony tevékenységek.

Az Application Gateway WAF van előzetesen beállítva CRS 3.0 alapértelmezés szerint. De lehetősége van, használja helyette a CRS 2.2.9. CRS 3.0-s csökkenti a vakriasztások CRS 2.2.9 képest. Emellett [testre szabhatja a szabályokat, hogy illeszkedjen az igényeihez](application-gateway-customize-waf-rules-portal.md).

A WAF az alábbi internetes biztonsági rések ellen nyújt védelmet:

- SQL-injection attacks
- Többhelyes parancsfájlok futtatására
- Egyéb gyakori támadásoktól, például parancsinjektálás, HTTP való HTTP-válasz felosztása és távolifájl-beszúrásos kérése
- HTTP protokoll megsértése
- HTTP protocol anomalies, such as missing host user-agent and accept headers
- Robotprogramok, webbejárók és képolvasók
- Alkalmazások gyakori konfigurációs hibáinak (például Apache, IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0-s 13 szabálycsoportot tartalmaz, az alábbi táblázatban látható módon. Minden csoport több szabály, amely letiltható tartalmazza.

|Szabálycsoport|Leírás|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Zárolási legördülő metódusok (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|Port- és környezetleolvasók ellen|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Protokoll és a kódolási problémák ellen|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|A fejlécinjektálás, a kéréscsempészet és a válaszfelosztás ellen|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Fájlok és elérési utak elleni védelem|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Távolifájl-beszúrásos (RFI) támadások elleni védelem|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Újra programkód távoli védelme|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|PHP-injektálási támadások ellen védelmet|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Többhelyes parancsfájl-kezelési támadásokkal szembeni védelem érdekében|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|SQL-injektálási támadások ellen védelmet|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Munkamenet-rögzítési támadások elleni védelem|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 10 szabálycsoportot tartalmaz, az alábbi táblázatban látható módon. Minden csoport több szabály, amely letiltható tartalmazza.

|Szabálycsoport|Leírás|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|A protokollsértések (például érvénytelen karakterek vagy egy a GET-kéréstörzs) elleni védelem|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Helytelen fejléc-információk ellen|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Argumentumok vagy fájlok, amelyek a korlátozásokat meghaladó elleni védelem|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|A korlátozott metódusok, fejlécek és fájltípusok ellen|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Az internetes webbejárók és képolvasók elleni védelem|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Általános támadások (például a munkamenet-rögzítés, távolifájl-beszúrásos és PHP-injektálás) ellen védelmet biztosító|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|SQL-injektálási támadások ellen védelmet|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Többhelyes parancsfájl-kezelési támadásokkal szembeni védelem érdekében|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Elérési út-átjárási támadások elleni védelem|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Backdoor trójai programok ellen|

### <a name="waf-modes"></a>WAF-üzemmódok

Az Application Gateway WAF beállítható úgy, hogy futtassa a következő két módban:

* **Észlelés üzemmód**: Figyeli, és naplózza az összes fenyegetés riasztás. Az Application Gateway naplózási diagnosztikáját bekapcsolása a **diagnosztikai** szakaszban. Meg kell győződjön meg arról is, hogy a WAF-napló kiválasztva, és engedélyezve van. Webalkalmazási tűzfal nem blokkolja a bejövő kéréseket, ha az észlelési üzemmódban működik.
* **Megelőzés üzemmód**: Blokkok támadásokkal szemben, amely észleli a szabályokat. A támadó egy "403-as jogosulatlan hozzáférés" kivételt kap, és a kapcsolat megszakad. Megelőzés üzemmód rögzíti az ilyen támadásokat a WAF-naplókban.

### <a name="anomaly-scoring-mode"></a>Anomáliadetektálási pontozás mód
 
OWASP eldöntötte, hogy a forgalom blokkolására a két mód áll: A hagyományos és Anomáliadetektálási pontozási módról.

Hagyományos módban függetlenül bármilyen más szabály megegyezik bármilyen szabálynak megfelelő forgalom számít ki. Ebben a módban is könnyen áttekinthető. De hány szabályai megegyeznek egy adott kéréshez információ hiánya egy korlátozás. Tehát Anomáliadetektálási pontozási mód jelent meg. Ez az alapértelmezett érték az OWASP 3. *x*.

Anomáliadetektálási pontozási módban minden olyan szabálynak megfelelő forgalom nem azonnal letiltva a tűzfal megelőzés üzemmódban van. Szabályok rendelkezik egy bizonyos súlyossága: *Kritikus fontosságú*, *hiba*, *figyelmeztetés*, vagy *értesítés*. Adott súlyossági hatással van a kérést, az Anomáliadetektálás pontszámot nevezett által használt számérték. Ha például egy *figyelmeztetés* egyezés járul hozzá a pontszámot 3 szabály. Egy *kritikus* szabály egyezést járul hozzá az 5.

Nincs forgalom blokkolása a Anomáliadetektálási pontszám 5 küszöbértékét. Így egyetlen *kritikus* szabály egyezést is megfelel az Application Gateway WAF egy kérelmet, még akkor is, a megelőzés üzemmód blokkolása. Azonban egy *figyelmeztetés* szabály egyezést csak növeli a nem elég letiltja a forgalmat saját maga által 3 pontszám, Anomáliadetektálás.

> [!NOTE]
> Ez az üzenet, amelyet a rendszer a WAF-szabály megegyezik a forgalom tartalmazza a skálázásiművelet-értéke "Blocked." Azonban a forgalom valóban csak egy Anomáliadetektálási pontszám, az 5-ből letiltott vagy újabb.  

### <a name="waf-monitoring"></a>WAF-figyelés

Fontos az alkalmazásátjáró állapotának folyamatos figyelése. Integráció az Azure Security Center az Azure Monitor, támogatja a WAF és az általa védett alkalmazások állapotát, és az Azure Monitor naplózza.

![Az Application Gateway WAF diagnosztikai ábrája](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway-naplók integrálva vannak az [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md). Ez lehetővé teszi, hogy diagnosztikai információk, beleértve a WAF-riasztások és -naplók nyomon követése. Ezt a képességet a érheti el a **diagnosztikai** lapján a portálon, vagy közvetlenül az Azure Monitor az Application Gateway erőforrás. Naplók engedélyezésével kapcsolatos további tudnivalókért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[A Security Center](../security-center/security-center-intro.md) segít megelőzését, észlelését és háríthatja el a fenyegetéseket. Jobb láthatóságával és kézben tartásával az Azure-erőforrások biztosít. Application Gateway-példány [integrálhatók a Security Centerrel](application-gateway-integration-security-center.md). A Security Center átvizsgálja a környezetet védelem nélküli webalkalmazásokat keresve. Azt is javasoljuk az Alkalmazásátjáró WAF-nak a sebezhető erőforrások védelmét. A Security Centerből közvetlenül a tűzfalat hoz létre. Ezek a WAF-példányok a Security Center integrált. Általuk küldött riasztások, valamint állapotinformációkat a Security centerhez a jelentéskészítéshez.

![A Security Center áttekintési ablaka](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Naplózás

Az Application Gateway WAF részletes jelentéseket, amely észleli az egyes fenyegetési biztosít. A naplózás integrálva van az Azure Diagnostics naplóival. Riasztások a .json formátumban vannak rögzítve. Ezek a naplók integrálhatók az [naplózza az Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![Application Gateway-diagnosztika a windows-naplók](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Application Gateway WAF – A termékváltozat díjszabása

Az Application Gateway WAF egy új érhető el a Termékváltozat. Ez a Termékváltozat csak az Azure Resource Manager üzembe helyezési modell, a klasszikus üzemi modellben nem érhető el. Emellett a WAF Termékváltozat csak közepes és nagy méretű az Application Gateway-példány méretek a tartalmaz. Az Application Gateway minden korlátokat a WAF termékváltozatra is érvényesek.

Díjszabás átjáró példány óradíjat számítunk fel, és az adatfeldolgozási díj alapul. [Application Gateway díjszabása](https://azure.microsoft.com/pricing/details/application-gateway/) a normál Termékváltozat díjaitól eltér a WAF Termékváltozat. Az adatfeldolgozási díjak azonosak. Nem számítunk szabály vagy a szabály fel díjat. Az ugyanazon a webalkalmazási tűzfal mögött található több webalkalmazás védheti meg. Nem támogatja a több alkalmazást kell fizetnie.

## <a name="next-steps"></a>További lépések

Lásd: [webalkalmazási tűzfal konfigurálása Application gatewayen](tutorial-restrict-web-traffic-powershell.md).
