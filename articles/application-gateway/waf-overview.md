---
title: Az Azure Application Gateway webalkalmazási tűzfal (WAF) bemutatása
description: Ez a cikk a webalkalmazási tűzfal (WAF) áttekintést nyújt az Application Gateway számára
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 11/16/2018
ms.author: amsriva
ms.openlocfilehash: 9bccc9258a6bd9a6fef4956d0f32cb00dd3c542d
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454259"
---
# <a name="web-application-firewall-waf"></a>Webalkalmazási tűzfal (WAF)

A webalkalmazási tűzfal (WAF) az Application Gateway egyik szolgáltatása, amely központi védelmet nyújt a webalkalmazásoknak a gyakori biztonsági rések ellen.

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Ilyen jellegű támadások között közös SQL-injektálásos támadásokról, és webhelyek közötti, parancsprogramot alkalmazó támadások említsünk. 

Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának több rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. WAF-megoldás is reagálhat a gyorsabb biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett, hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

WAF-szabályai alapján a [alapvető OWASP-szabálykészletek](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0-s vagy 2.2.9-es verzióinak. Automatikusan frissíti szerepeltetni kívánt új rések elleni védelem nincs szükség további konfigurálásra.

![imageURLroute](./media/waf-overview/WAF1.png)

Az Application Gateway egy alkalmazáskézbesítési vezérlőt (ADC), működik, és SSL-lezárást, cookie-alapú munkamenet-affinitást, Ciklikus időszeleteléses terheléselosztást, tartalomalapú útválasztást, valamint több webhely és biztonsági fejlesztések üzemeltetésének kínálja.

Az Application Gateway által kínált biztonsági fejlesztések között SSL-házirend kezelése, valamint teljes körű SSL-támogatás. Az ADC-ajánlatba közvetlenül integrált WAF (webalkalmazási tűzfal) tovább erősíti az alkalmazásbiztonságot. Ez egy könnyen konfigurálható központi helyet biztosít a webalkalmazások kezeléséhez és a gyakori webes biztonsági rések elleni védelemhez.

## <a name="benefits"></a>Előnyök

Az alábbiak az Application Gateway és a webalkalmazási tűzfal használatának legfontosabb előnyei:

### <a name="protection"></a>Védelem

* A háttérkód módosítása nélkül védheti a webalkalmazásokat a webes biztonsági résektől és támadásoktól.

* Egyszerre több webalkalmazást védhet egy Application Gateway mögött. Az Application Gateway akár 20 webhely üzemeltetését is támogatja egyetlen átjáró mögött, amelyek a WAF segítségével mind védhetők a webes támadásokkal szemben.

### <a name="monitoring"></a>Figyelés

* Valós idejű WAF-naplók segítségével követheti nyomon a webalkalmazást fenyegető támadásokat. A WAF-riasztások és -naplók nyomon követése, illetve a tendenciák kimutatása érdekében ezek a naplók integrálva vannak az [Azure Monitorral](../monitoring-and-diagnostics/monitoring-overview.md).

* WAF integrálva van az Azure Security Center. Az Azure Security Center egyetlen központi helyen jeleníti meg minden Azure-erőforrás biztonsági állapotát.

### <a name="customization"></a>Testreszabás

* A WAF-szabályok és -szabálycsoportok testreszabhatók az alkalmazás követelményeinek való megfelelés, illetve a téves riasztások kiszűrése érdekében.

## <a name="features"></a>Szolgáltatások

- SQL injection protection
- Adatbázisközi hely webhelyközi védelme
- Például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás gyakori webes támadások védelem
- HTTP protokoll megsértése elleni védelem
- Protection against HTTP protocol anomalies such as missing host user-agent and accept headers
- Robotprogramok, webbejárók és képolvasók elleni védelem
- Gyakori alkalmazások konfigurációs hibáinak észlelése (például Apache, IIS, és így tovább)

### <a name="public-preview-features"></a>Nyilvános előzetes verziójú funkciók

A jelenlegi WAF nyilvános előzetes Termékváltozat az alábbi szolgáltatásokat tartalmazza:

- **Kérelem méretbeli korlátokat** -webalkalmazási tűzfal lehetővé teszi a felhasználóknak kérelem méretbeli korlátokat belül alsó és felső határai konfigurálása.
- **Kizárási listák** -WAF kizárási listák engedélyezése a felhasználók számára bizonyos attribútumainak WAF során hagyja ki. Ilyenek például az Active Directory beszúrt jogkivonatokat, amelyek a hitelesítéshez, vagy a beírt jelszavak.

A WAF nyilvános előzetes verzióban kapcsolatos további információkért lásd: [Web application firewall kérelem méretbeli korlátokat és kizárási listák (nyilvános előzetes verzió)](application-gateway-waf-configuration.md).





### <a name="core-rule-sets"></a>Alapvető szabálykészletek

Az Application Gateway a következő két szabálykészletet támogatja: CRS 3.0 és CRS 2.2.9. Ezek az alapvető szabálykészletek olyan szabályok gyűjteményei, amelyek megvédik a webalkalmazásokat a kártékony tevékenységek ellen.

A webalkalmazási tűzfal alapértelmezés szerint a CRS 3.0-s verziójával van előre konfigurálva, de a 2.2.9-es verzió használata mellett is dönthet. A CRS 3.0-s verziója esetén kevesebb hibás riasztással kell számolnia, mint a 2.2.9-es verziónál. A [szabályok igény szerinti testreszabására](application-gateway-customize-waf-rules-portal.md) is lehetősége van. A webalkalmazási tűzfal többek között a következő gyakori internetes biztonsági rések ellen nyújt védelmet:

- SQL injection protection
- Adatbázisközi hely webhelyközi védelme
- Például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás gyakori webes támadások védelem
- HTTP protokoll megsértése elleni védelem
- Protection against HTTP protocol anomalies such as missing host user-agent and accept headers
- Robotprogramok, webbejárók és képolvasók elleni védelem
- Gyakori alkalmazások konfigurációs hibáinak észlelése (például Apache, IIS stb.)

Szabályok és a hozzájuk tartozó védelmi megoldások részletesebb listáját lásd: [alapvető szabálykészletek](#core-rule-sets).


#### <a name="owasp30"></a>OWASP_3.0

A mellékelt 3.0-s alapvető szabálykészlet 13 szabálycsoportot tartalmaz, ahogy a következő táblázatban is látható. Ezen szabálycsoportok mindegyike több, egyenként letiltható szabályt tartalmaz.

|Szabálycsoport|Leírás|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Metódusok (PUT, PATCH) zárolását szabályokat tartalmaz.|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**| A port- és környezetleolvasók ellen védelmet biztosító szabályokat tartalmaz.|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|A protokoll- és a kódolási problémák ellen védelmet biztosító szabályokat tartalmaz.|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|A fejlécinjektálás, a kéréscsempészet és a válaszfelosztás ellen védelmet biztosító szabályokat tartalmaz.|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|A fájlok és elérési utak elleni támadásoktól védelmet biztosító szabályokat tartalmaz.|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|A távolifájl-beszúrásos (RFI) támadások ellen védelmet biztosító szabályokat tartalmaz.|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|A távolikód-futtatásos támadások ellen védelmet biztosító szabályokat tartalmaz.|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|A PHP-injektálási támadások ellen védelmet biztosító szabályokat tartalmaz.|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|A helyközi, parancsfájlt alkalmazó támadások ellen védelmet biztosító szabályokat tartalmaz.|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Az SQL-injektálási támadások ellen védelmet biztosító szabályokat tartalmaz.|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|A munkamenet-rögzítési támadások ellen védelmet biztosító szabályokat tartalmaz.|

#### <a name="owasp229"></a>OWASP_2.2.9

A mellékelt 2.2.9-es alapvető szabálykészlet 10 szabálycsoportot tartalmaz, ahogy a következő táblázatban is látható. Ezen szabálycsoportok mindegyike több, egyenként letiltható szabályt tartalmaz.

|Szabálycsoport|Leírás|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|A protokollsértések (például érvénytelen karakterek, GET parancs egy kéréstörzzsel stb.) ellen védelmet biztosító szabályokat tartalmaz.|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|A helytelen fejléc-információk ellen védelmet biztosító szabályokat tartalmaz.|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|A korlátozásokat meghaladó argumentumok vagy fájlok ellen védelmet biztosító szabályokat tartalmaz.|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|A korlátozott metódusok, fejlécek és fájltípusok ellen védelmet biztosító szabályokat tartalmaz. |
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Az internetes webbejárók és keresők ellen védelmet biztosító szabályokat tartalmaz.|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Általános támadások (például munkamenet-rögzítés, távolifájl-beszúrás, PHP-injektálás) ellen védelmet biztosító szabályokat tartalmaz.|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Az SQL-injektálási támadások ellen védelmet biztosító szabályokat tartalmaz.|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|A helyközi, parancsfájlt alkalmazó támadások ellen védelmet biztosító szabályokat tartalmaz.|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Az elérésiút-átjárási támadások ellen védelmet biztosító szabályokat tartalmaz.|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|A backdoor trójai programok ellen védelmet biztosító szabályokat tartalmaz.|

### <a name="waf-modes"></a>WAF-üzemmódok

Az Application Gateway WAF az alábbi két üzemmódban való futtatásra konfigurálható:

* **Észlelés üzemmód** – amikor egy naplófájlt használ az észlelési módot, az Application Gateway WAF figyelők és a naplók minden veszélyforrás-riasztást futtatása van konfigurálva. A **Diagnosztika** szakaszban be kell kapcsolni az Application Gateway naplózási diagnosztikáját. Emellett ellenőrizze, hogy a WAF-napló ki van-e választva és be van-e kapcsolva. Az észlelési üzemmódban futtatott webalkalmazási tűzfal nem blokkolja a bejövő kéréseket.
* **Megelőzés üzemmód** – Amikor az Application Gateway WAF megelőzés üzemmódban való futtatásra van konfigurálva, aktívan blokkolja a szabályok által észlelt behatolásokat és támadásokat. A támadó egy 403-as jogosulatlan hozzáférési kivételt kap, és a kapcsolat megszakad. A megelőzés üzemmód továbbra is naplózza az ilyen támadásokat a WAF-naplókban.

### <a name="application-gateway-waf-reports"></a>WAF-figyelés

Fontos az alkalmazásátjáró állapotának folyamatos figyelése. A állapotának monitorozása a webes tűzfal és az általa védett alkalmazások naplózási és integrációs az Azure monitorral, az Azure Security Center, ha az Azure Monitor naplózza.

![diagnosztika](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Az Application Gateway-naplók integrálva vannak az [Azure Monitorral](../monitoring-and-diagnostics/monitoring-overview.md),  ami lehetővé teszi a diagnosztikai információk (például a WAF-riasztások és -naplók) nyomon követését.  Ez a képesség a portál Application Gateway-erőforrásának **Diagnosztika** lapján vagy közvetlenül az Azure Monitor szolgáltatáson keresztül érhető el. Az application gateway diagnosztikai naplóinak engedélyezéséről kapcsolatos további információkért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md)

#### <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](../security-center/security-center-intro.md) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az alkalmazásátjáró most már [integrálható az Azure Security Centerbe](application-gateway-integration-security-center.md). Az Azure Security Center átvizsgálja a környezetet védelem nélküli webalkalmazásokat keresve. Ezek után javasolhatja az alkalmazásátjáró WAF-nak a sebezhető erőforrások védelmét. Közvetlenül létrehozhat alkalmazásátjáró WAF-ot az Azure Security Centerből.  Ezek a WAF-példányok az Azure Security Centerbe integrálódnak, és a jelentésekhez használható riasztásokat, valamint állapotinformációkat fognak visszaküldeni az Azure Security Centerbe.

![1. ábra](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Naplózás

Az Application Gateway WAF részletes jelentéseket biztosít az összes észlelt fenyegetésről. A naplózás integrálva van az Azure Diagnostics naplóival, a riasztások pedig JSON formátumban vannak rögzítve. Ezek a naplók integrálhatók az [naplózza az Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![imageURLroute](./media/waf-overview/waf2.png)

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

A webalkalmazási tűzfal az új WAF termékváltozatban érhető el. Ez a termékváltozat kizárólag az Azure Resource Manager üzembe helyezési modelljében érhető el, a klasszikus üzemi modellben nem. Emellett a WAF Termékváltozat csak közepes és nagy méretű Alkalmazásátjáró-példányokhoz használható a tartalmaz. Az alkalmazásátjárókra vonatkozó összes korlátozás a WAF termékváltozatra is érvényes.

A díjszabás az átjárópéldányok óránkénti díján és az adatfeldolgozási díjon alapul. A WAF termékváltozathoz tartozó óránkénti átjáródíj eltér a normál termékváltozat díjaitól, és az [Application Gateway díjszabását](https://azure.microsoft.com/pricing/details/application-gateway/) ismertető webhelyen tekinthető meg. Az adatfeldolgozási díjak nem változnak. Nincs szabályonként vagy szabálycsoportonként kiszabott díjak. Az ugyanazon a webalkalmazási tűzfal mögött található több webalkalmazás védheti, és több alkalmazásokat támogató nem díjkötelesek.

## <a name="next-steps"></a>További lépések

Miután megismerkedett a WAF, lásd: [webalkalmazási tűzfal konfigurálása Application gatewayen](tutorial-restrict-web-traffic-powershell.md).

