---
title: Az Azure webalkalmazási tűzfal bemutatása
titleSuffix: Azure Web Application Firewall
description: Ez a cikk áttekintést nyújt a webalkalmazási tűzfalról (WAF) Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 4ba2772ffb89809c2262880385207a12c74882cc
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067147"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway"></a>Azure webalkalmazási tűzfal az Azure Application Gateway

Az Azure-Application Gateway Azure webalkalmazási tűzfala (WAF) lehetővé teszi a webalkalmazások központosított védelmét a gyakori támadásokkal és biztonsági rések ellen. A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. Az SQL-injektálás és a helyek közötti parancsfájlkezelés a leggyakoribb támadások közé tartozik.

A WAF on Application Gateway az Open Web Application Security Project (OWASP) [alapszintű szabálykészlet (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1, 3,0 vagy 2.2.9 alapul. A WAF automatikusan frissíti az új biztonsági rések elleni védelmet, és nincs szükség további konfigurálásra. 

Az alább felsorolt WAF-funkciók egy WAF szabályzaton belül találhatók. Több házirend is létrehozható, és társítható egy Application Gatewayhoz, az egyes figyelőkhöz vagy egy Application Gateway elérésiút-alapú útválasztási szabályaihoz. Így szükség esetén külön házirendeket hozhat a Application Gateway mögött lévő helyekhez. A WAF-szabályzatokkal kapcsolatos további információkért lásd: [WAF szabályzat létrehozása](create-waf-policy-ag.md).

   > [!NOTE]
   > A helyszíni és az URI-WAF szabályzatok nyilvános előzetes verzióban érhetők el. Ez azt jelenti, hogy ez a funkció a Microsoft kiegészítő használati feltételeinek hatálya alá tartozik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Application Gateway WAF diagram](../media/ag-overview/waf1.png)

Application Gateway Application Delivery Controller (ADC) néven működik. SSL (SSL) megszüntetését, a cookie-alapú munkamenet-affinitást, a ciklikus időszeleteléses terheléselosztást, a tartalom-alapú útválasztást, több webhely üzemeltetését és biztonsági fejlesztéseket biztosít.

Application Gateway biztonsági fejlesztések közé tartozik az SSL-házirend kezelése és a végpontok közötti SSL-támogatás. Az WAF integrációja Application Gatewayba való integrálásával erősíti meg az alkalmazások biztonságát. A kombináció megvédi a webalkalmazásokat a gyakori biztonsági rések ellen. És egy könnyen konfigurálható központi helyet biztosít a felügyelethez.

## <a name="benefits"></a>Előnyök

Ez a szakasz a Application Gateway által nyújtott WAF alapvető előnyeit ismerteti.

### <a name="protection"></a>Védelem

* A webalkalmazások a webes sebezhetőségek és támadások elleni védelem a háttér-programkód módosítása nélkül.

* Egyszerre több webalkalmazást is el kell látni. A Application Gateway egy példánya akár 40 webhelyet is tartalmazhat, amelyeket egy webalkalmazási tűzfal véd.

* Egyéni WAF-szabályzatok létrehozása ugyanazon WAF mögötti különböző helyekhez 

* A webalkalmazások rosszindulatú robotoktól való ellátása az IP hírnévre vonatkozó szabályrendszert (előzetes verzió)

### <a name="monitoring"></a>Figyelés

* Valós idejű WAF-napló használatával figyelheti a webalkalmazások elleni támadásokat. A napló integrálva van [Azure monitor](../../azure-monitor/overview.md) a WAF-riasztások nyomon követésére és a trendek egyszerű figyelésére.

* A Application Gateway WAF integrálva van Azure Security Centersal. Security Center az Azure-erőforrások biztonsági állapotának központi nézetét biztosítja.

### <a name="customization"></a>Testreszabás

* Szabja testre a WAF szabályokat és a szabályok csoportjait az alkalmazás követelményeinek megfelelően, és távolítsa el a téves pozitív értéket.

* Rendeljen hozzá egy WAF házirendet a WAF mögötti minden helyhez, hogy engedélyezze a hely-specifikus konfigurációt

* Egyéni szabályok létrehozása az alkalmazás igényeinek megfelelően

## <a name="features"></a>Szolgáltatások

- SQL-injection protection.
- Helyek közötti parancsfájlok elleni védelem.
- Más gyakori webes támadások elleni védelem, például a parancsok befecskendezése, a HTTP-kérések csempészete, a HTTP-válaszok felosztása és a távoli fájlok bevonása.
- A HTTP protokoll megsértésének védelme.
- A HTTP protokollal kapcsolatos rendellenességek, például a gazdagépek hiányzó felhasználói ügynökének védelme és a fejlécek elfogadása.
- Védelem a keresőrobotok és a képolvasók ellen.
- Az alkalmazások gyakori konfigurációs beállításainak (például az Apache és az IIS) észlelése.
- Konfigurálható kérelmek méretének korlátja alsó és felső korlátokkal.
- A kizárási listák lehetővé teszik bizonyos WAF kiértékelését. Gyakori példa Active Directory beszúrt tokenek használata a hitelesítéshez vagy a jelszó mezőkhöz.
- Egyéni szabályokat hozhat létre az alkalmazások konkrét igényeinek megfelelően.
- Geo-szűrési forgalom, amely engedélyezi vagy letiltja bizonyos országok számára, hogy hozzáférjenek az alkalmazásaihoz. (előzetes verzió)
- Az alkalmazásait a robotoktól a bot-elhárítási szabályokkal védekezhet. (előzetes verzió)

## <a name="waf-policy"></a>WAF szabályzat

A webalkalmazási tűzfal Application Gatewayon való engedélyezéséhez létre kell hoznia egy WAF házirendet. Ez a szabályzat a felügyelt szabályok, az egyéni szabályok, a kizárások és egyéb testreszabások, például a fájlfeltöltés korlátja. 

### <a name="core-rule-sets"></a>Alapvető szabálykészletek

A Application Gateway három szabálykészlet használatát támogatja: a CRS 3,1, a CRS 3,0 és a CRS 2.2.9. Ezek a szabályok védik a webalkalmazásokat a rosszindulatú tevékenységekkel szemben.

További információ: a [webalkalmazási TŰZFAL CRS-szabályait tartalmazó csoportok és szabályok](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Egyéni szabályok

A Application Gateway támogatja az egyéni szabályokat is. Egyéni szabályokkal saját szabályokat hozhat létre, amelyeket a rendszer az WAF-on keresztül áthaladó kérelmek esetében értékel ki. Ezek a szabályok magasabb prioritással rendelkeznek, mint a felügyelt szabálykészlet többi szabálya. Ha teljesülnek a feltételek, a rendszer műveletet végez az engedélyezéshez vagy a blokkoláshoz. 

További információ: [Application Gateway egyéni szabályai.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Bot-enyhítés (előzetes verzió)

A felügyelt robot védelmi szabálykészlet lehetővé teszi, hogy a WAF blokkolják vagy naplózzák az ismert kártékony IP-címekről érkező kéréseket a felügyelt szabályokkal együtt. Az IP-címek forrása a Microsoft Threat Intelligence-hírcsatorna. A Microsoft Threat Intelligence az Intelligens biztonsági gráffal működik, amelyet több szolgáltatás, így az Azure Security Center is használ.

> [!NOTE]
> A robot Protection-szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és az előzetes verziójú szolgáltatói szerződéssel van ellátva. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha a bot Protection engedélyezve van, a rendszer a rosszindulatú robot ügyfelének IP-címeinek megfelelő bejövő kérelmeket naplózza a tűzfal naplójában. További információ az alábbi témakörben található. A WAF-naplókat a Storage-fiókból, az Event hub-ból vagy a log Analyticsből is elérheti. 

### <a name="waf-modes"></a>WAF módok

A Application Gateway WAF a következő két módban való futtatásra konfigurálható:

* **Észlelési mód**: figyeli és naplózza az összes veszélyforrás riasztást. A diagnosztika szakaszban bekapcsolja Application Gateway naplózási **diagnosztikát.** Győződjön meg arról is, hogy a WAF-napló ki van választva és be van kapcsolva. A webalkalmazási tűzfal nem blokkolja a bejövő kérelmeket, ha az észlelési módban működik.
* **Megelőzési mód**: a szabályok által észlelhető behatolások és támadások blokkolása. A támadó "403 jogosulatlan hozzáférés" kivételt kap, és a kapcsolat bezárult. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

> [!NOTE]
> Azt javasoljuk, hogy rövid idő alatt futtasson egy újonnan telepített WAF az észlelési módban az éles környezetben. Ez lehetőséget biztosít a [tűzfalak](../../application-gateway/application-gateway-diagnostics.md#firewall-log) beszerzésére és a kivételek vagy [Egyéni szabályok](./custom-waf-rules-overview.md) frissítésére a megelőzési módra való áttérés előtt. Ez segít csökkenteni a váratlan letiltott forgalom előfordulását.

### <a name="anomaly-scoring-mode"></a>Anomália pontozási mód

A OWASP két módja van annak eldöntésére, hogy le kell-e tiltani a forgalmat: hagyományos mód és anomália pontozási mód.

Hagyományos módban a szabályoknak megfelelő forgalom a többi szabálytól függetlenül tekintendő. Ez a mód könnyen értelmezhető. Ha azonban nincs információ arról, hogy hány szabály felel meg egy adott kérésnek, a korlátozás. Tehát a anomália pontozási mód be lett vezetve. Ez az alapértelmezett érték a OWASP 3 esetében. *x*.

A anomália pontozási módban a szabályoknak megfelelő forgalom nem lesz azonnal letiltva, ha a tűzfal megelőzési módban van. A szabályok bizonyos súlyossággal rendelkeznek: *kritikus*, *hiba*, *Figyelmeztetés*vagy *Figyelmeztetés*. Ez a súlyosság befolyásolja a kérelem numerikus értékét, amelyet az anomália pontszámnak nevezünk. Egy *Figyelmeztetési* szabály például a következőhöz járul hozzá: 3 – a pontszám. Egy *kritikus* szabály egyezése 5.

|Severity  |Érték  |
|---------|---------|
|Kritikus     |5|
|Hiba        |4|
|Figyelmeztetés      |3|
|Megjegyzčs       |2|

Az anomália pontszáma 5 küszöbértéket mutat a forgalom blokkolásához. Tehát egy *kritikus* szabály egyezése elegendő ahhoz, hogy a Application Gateway WAF egy kérést, még megelőzési módban is. Egy *Figyelmeztetési* szabály azonban csak az anomália pontszám 3 értékkel nő, ami önmagában nem elegendő a forgalom blokkolásához.

> [!NOTE]
> Az a naplózott üzenet, amikor egy WAF-szabály megfelel a forgalomnak, tartalmazza a "Letiltva" műveleti értéket. A forgalom azonban valójában csak 5 vagy magasabb anomália esetén van letiltva.  

### <a name="waf-monitoring"></a>WAF-figyelés

Fontos az alkalmazásátjáró állapotának folyamatos figyelése. A WAF és az általa védett alkalmazások állapotának monitorozása Azure Security Center, Azure Monitor és Azure Monitor naplók integrálásával támogatott.

![Application Gateway WAF diagnosztika diagramja](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway naplók integrálva vannak a [Azure Monitorekkel](../../azure-monitor/overview.md). Ez lehetővé teszi a diagnosztikai információk nyomon követését, beleértve a WAF-riasztásokat és-naplókat. Ezt a funkciót a portál Application Gateway erőforrásának **diagnosztika** lapján vagy közvetlenül a Azure monitoron keresztül érheti el. A naplók engedélyezésével kapcsolatos további tudnivalókért tekintse meg a [Application Gateway diagnosztikát](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-intro.md) segít a fenyegetések megelőzésében, észlelésében és megválaszolásában. Az Azure-erőforrások jobb láthatóságát és felügyeletét teszi lehetővé. Application Gateway [integrálva van Security Centersal](../../application-gateway/application-gateway-integration-security-center.md). Security Center megvizsgálja a környezetet a nem védett webalkalmazások észleléséhez. Application Gateway WAF is javasolhatja, hogy megvédje ezeket a sebezhető erőforrásokat. A tűzfalak közvetlenül a Security Centerből hozhatók létre. Ezek a WAF-példányok a Security Centerba vannak integrálva. Riasztásokat és állapotadatok küldését Security Center a jelentéskészítéshez.

![Security Center áttekintése ablak](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

A Microsoft Azure Sentinel egy skálázható, Felhőbeli natív, biztonsági információkkal kapcsolatos esemény-felügyeleti (SIEM) és biztonsági előkészítési (felszárnyaló) megoldás. Az Azure Sentinel intelligens biztonsági elemzési és fenyegetésekkel kapcsolatos intelligenciát biztosít a vállalaton belül, így egyetlen megoldást kínál a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra és a fenyegetésekre való reagálásra.

A beépített Azure WAF Firewall Events munkafüzettel áttekintést kaphat a WAF lévő biztonsági eseményekről. Ilyenek például az események, a megfeleltetett és a blokkolt szabályok, valamint minden más, ami bekerül a tűzfal naplófájljaiba. További információk az alábbi naplózásban. 


![Sentinel](../media/ag-overview/sentinel.png)

#### <a name="logging"></a>Naplózás

Application Gateway WAF részletes jelentéskészítést biztosít az általa észlelt fenyegetésekről. A naplózás integrálva van Azure Diagnostics naplókba. A riasztások. JSON formátumban vannak rögzítve. Ezek a naplók [Azure monitor naplókkal](../../azure-monitor/insights/azure-networking-analytics.md)integrálhatók.

![Application Gateway diagnosztikai naplók Windows](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Application Gateway WAF – A termékváltozat díjszabása

A díjszabási modellek a WAF_v1 és az WAF_v2 SKU esetében eltérőek. További információért tekintse meg a [Application Gateway díjszabási](https://azure.microsoft.com/pricing/details/application-gateway/) oldalát. 

## <a name="next-steps"></a>További lépések

- Első lépések [WAF szabályzat létrehozásával](create-waf-policy-ag.md)
- További információ a [WAF felügyelt szabályairól](application-gateway-crs-rulegroups-rules.md)
- További információ az [Egyéni szabályokról](custom-waf-rules-overview.md)
- Ismerje meg [a webalkalmazási tűzfalat az Azure bejárati ajtaján](../afds/afds-overview.md)

