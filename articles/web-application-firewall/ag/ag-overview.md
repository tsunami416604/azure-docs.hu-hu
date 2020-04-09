---
title: Bevezetés az Azure webalkalmazás-tűzfalba
titleSuffix: Azure Web Application Firewall
description: Ez a cikk áttekintést nyújt az Application Gateway webalkalmazás-tűzfaláról (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 8d43851a6a546bd23881c4d0a72185ca03e63092
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983619"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway"></a>Azure webalkalmazás-tűzfal az Azure Application Gateway-en

Az Azure Web Application Firewall (WAF) az Azure Application Gateway központi védelmet nyújt a webalkalmazások a gyakori biztonsági rések és biztonsági rések. A webalkalmazásokat egyre inkább rosszindulatú támadások célozzák meg, amelyek kihasználják az általánosan ismert biztonsági réseket. Az SQL-injektálás és a helyek közötti parancsfájlok a leggyakoribb támadások közé tartoznak.

A WAF az Application Gateway-en az Open Web Application Security Project (OWASP) 3.1, 3.0 vagy 2.2.9 [alapszabálykészletén](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) alapul. A WAF automatikusan frissül, hogy tartalmazza az új biztonsági rések elleni védelmet, további konfiguráció nélkül. 

Az alább felsorolt ÖSSZES WAF-szolgáltatás a WAF-házirenden belül található. Több szabályzatot is létrehozhat, és azok társíthatók egy alkalmazásátjáróhoz, az egyes figyelőkhöz vagy az alkalmazásátjáró útvonalalapú útválasztási szabályaihoz. Így szükség esetén az Alkalmazásátjáró mögötti minden webhelyhez külön szabályzatok hozhatnak helyet. A WAF-házirendekkel kapcsolatos további információkért [lásd: WAF-házirend létrehozása.](create-waf-policy-ag.md)

   > [!NOTE]
   > A webhelyenkénti és az URI-WAF-házirendek nyilvános előzetes verzióban vannak. Ez azt jelenti, hogy erre a szolgáltatásra a Microsoft kiegészítő használati feltételei vonatkoznak. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Alkalmazásátjáró WAF-diagramja](../media/ag-overview/waf1.png)

Az Application Gateway alkalmazáskézbesítési vezérlőként (ADC) működik. Kínál Secure Sockets Layer (SSL) megszüntetése, cookie-alapú munkamenet affinitás, ciklikus multiplexelés terheléselosztás, tartalom-alapú útválasztás, képes a fogadó több honlapok, és a biztonsági fejlesztések.

Az Application Gateway biztonsági fejlesztései közé tartozik az SSL-házirendek kezelése és a végpontok között ssl-támogatás. Az alkalmazásbiztonságot a WAF alkalmazásátjáróba való integrációja erősíti. A kombináció megvédi a webes alkalmazásokat a gyakori biztonsági résektől. És ez biztosítja a könnyen konfigurálható központi helyen kezelni.

## <a name="benefits"></a>Előnyök

Ez a szakasz a WAF alkalmazásátjárón nyújtott alapvető előnyeit ismerteti.

### <a name="protection"></a>Védelem

* Védje meg webes alkalmazásait a webes biztonsági résektől és támadásoktól a háttérkód módosítása nélkül.

* Védj meg egyszerre több webes alkalmazást. Az Application Gateway egy példánya legfeljebb 40 olyan webhelyet képes üzemeltetni, amelyeket egy webalkalmazás tűzfala véd.

* Egyéni WAF-házirendek létrehozása ugyanazon WAF mögött imbolmiában lévő különböző webhelyekhez 

* Védje meg webes alkalmazásait a rosszindulatú robotoktól az IP Hírnév szabálykészlettel (előzetes verzió)

### <a name="monitoring"></a>Figyelés

* A webalkalmazások elleni támadások at egy valós idejű WAF-napló használatával figyelheti. A napló integrálva van az [Azure Monitorral](../../azure-monitor/overview.md) a WAF-riasztások nyomon követéséhez és a trendek egyszerű figyeléséhez.

* Az Application Gateway WAF integrálva van az Azure Security Centerrel. A Security Center központi képet nyújt az összes Azure-erőforrás biztonsági állapotáról.

### <a name="customization"></a>Testreszabás

* A WAF-szabályok at és szabálycsoportokat az alkalmazás követelményeinek megfelelően testreszabhatja, és kiküszöbölheti a hamis pozitív értékeket.

* WaF-házirend társítása a WAF mögött álló minden egyes webhelyhez, hogy lehetővé tegye a helyspecifikus konfigurációt

* Egyéni szabályok létrehozása az alkalmazás igényeinek megfelelően

## <a name="features"></a>Szolgáltatások

- SQL-befecskendezés elleni védelem.
- Helyek közötti parancsfájlok védelme.
- Védelem más gyakori webes támadásokkal, például a parancsbefecskendezéssel, a HTTP-kérelem-csempészettel, a HTTP-válaszok megosztásával és a távoli fájlok felvételének lehetőségével szemben.
- Védelem a HTTP protokoll megsértése ellen.
- Http-protokoll anomáliák, például a hiányzó gazdagép user-agent és elfogadja a fejlécek elleni védelem.
- Védelem a feltérképező robotok és lapolvasók ellen.
- Gyakori alkalmazáshelytelen konfigurációk észlelése (például Apache és IIS).
- Konfigurálható kérelemméret-korlátok alsó és felső határokkal.
- A kizárási listák lehetővé teszik bizonyos kérésattribútumok elhagyását a WAF-kiértékelésből. Gyakori példa erre a hitelesítéshez vagy jelszómezőkhöz használt Active Directory által beszúrt tokenek.
- Hozzon létre egyéni szabályokat az alkalmazások egyedi igényeinek megfelelően.
- Geo-szűrő forgalom engedélyezése vagy letiltása bizonyos országok számára az alkalmazások eléréséhez. (előzetes verzió)
- Védje alkalmazásait a botoktól a robotkockázat-csökkentő szabálykészlettel. (előzetes verzió)

## <a name="waf-policy"></a>WAF-házirend

Ha engedélyezni szeretné a webalkalmazás-tűzfalat egy alkalmazásátjárón, létre kell hoznia egy WAF-házirendet. Ez a házirend az a hely, ahol az összes felügyelt szabály, egyéni szabály, kizárás és egyéb testreszabás, például a fájlfeltöltési korlát létezik. 

### <a name="core-rule-sets"></a>Alapvető szabálykészletek

Az Application Gateway három szabálykészletet támogat: CRS 3.1, CRS 3.0 és CRS 2.2.9. Ezek a szabályok védik a webalkalmazásokat a rosszindulatú tevékenységekkel szemben.

További információt a [Webalkalmazás tűzfal CRS szabálycsoportjai és szabályai](application-gateway-crs-rulegroups-rules.md)című témakörben talál.

### <a name="custom-rules"></a>Egyéni szabályok

Az Application Gateway egyéni szabályokat is támogat. Az egyéni szabályok, létrehozhat ja a saját szabályokat, amelyek kiértékelése minden kérelem, amely áthalad a WAF.With custom rules, you can create your own rules, which are dekiértékelés for each request that passes through WAF. Ezek a szabályok magasabb prioritással rendelkeznek, mint a kezelt szabálykészletek többi szabálya. Ha egy feltételkészlet teljesül, a művelet az engedélyezésvagy a letiltás érdekében történik. 

A geomatch operátor mostantól elérhető nyilvános előzetes verzióban az egyéni szabályokhoz. További információt a [geoegyezésegyéni szabályaiban](custom-waf-rules-overview.md#geomatch-custom-rules-preview) talál.

> [!NOTE]
> Az egyéni szabályok geoegyezési operátora jelenleg nyilvános előzetes verzióban érhető el, és előzetes szolgáltatásiszint-szerződéssel rendelkezik. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az egyéni szabályokról az [Egyéni szabályok az alkalmazásátjáróhoz című témakörben talál további információt.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Bot mérséklése (előzetes verzió)

A felügyelt robotvédelmi szabálykészlet engedélyezhető a WAF számára, hogy a felügyelt szabálykészlet mellett blokkolja vagy naplózza az ismert rosszindulatú IP-címekről érkező kérelmeket. Az IP-címek a Microsoft Threat Intelligence hírcsatornából származnak. Az Intelligent Security Graph a Microsoft fenyegetésekkel kapcsolatos intelligenciáját működteti, és több szolgáltatás, köztük az Azure Security Center is használja.

> [!NOTE]
> A robotvédelmi szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és előzetes szolgáltatásiszint-szerződéssel rendelkezik. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha a Bot Protection engedélyezve van, a rosszindulatú robot ügyfélIP-jének megfelelő bejövő kérelmek et a tűzfalnaplóba naplózza a rendszer, lásd az alábbi további információkat. Waf-naplókat érhet el a tárfiókból, az eseményközpontból vagy a naplóelemzésből. 

### <a name="waf-modes"></a>WAF módok

Az Application Gateway WAF beállítható úgy, hogy a következő két módban fusson:

* **Észlelési mód:** Figyeli és naplózza az összes fenyegetésriasztást. Az Alkalmazásátjáró naplózási diagnosztikájának bekapcsolása a **Diagnosztika** szakaszban. Azt is meg kell győződnie arról, hogy a WAF-napló ki van jelölve és be van kapcsolva. A webalkalmazás tűzfala nem blokkolja a bejövő kérelmeket, ha észlelési módban működik.
* **Megelőzési mód**: Blokkolja a szabályok által észlelt behatolásokat és támadásokat. A támadó "403-as jogosulatlan hozzáférés" kivételt kap, és a kapcsolat megszakad. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

> [!NOTE]
> Javasoljuk, hogy futtasson egy újonnan üzembe helyezett WAF észlelési módban egy rövid ideig egy éles környezetben. Ez lehetővé teszi a [tűzfalnaplók](../../application-gateway/application-gateway-diagnostics.md#firewall-log) beszerzését és a kivételek vagy [egyéni szabályok](./custom-waf-rules-overview.md) frissítését a Megelőzés módba való áttérés előtt. Ez segíthet csökkenteni a váratlan blokkolt forgalom előfordulását.

### <a name="anomaly-scoring-mode"></a>Anomália pontozási mód

Az OWASP két móddal rendelkezik annak eldöntésére, hogy blokkolja-e a forgalmat: a hagyományos és az anomáliapontozási mód.

Hagyományos módban a bármely szabálynak megfelelő forgalmat a többi szabályegyezéstől függetlenül kell figyelembe venni. Ez a mód könnyen érthető. De az információ hiánya arról, hogy hány szabály felel meg egy adott kérelem egy korlátozás. Szóval, Anomália pontozási mód került bevezetésre. Ez az OWASP 3 alapértelmezett beállítása. *x*.

Anomáliapontozási módban a bármely szabálynak megfelelő forgalom nem lesz azonnal blokkolva, ha a tűzfal megelőzési módban van. A szabályok nak van egy bizonyos súlyossága: *Kritikus*, *Hiba*, *Figyelmeztetés*vagy *Értesítés*. Ez a súlyosság hatással van a kérelem numerikus értékére, amelyet anomáliapontszámnak neveznek. Például egy *figyelmeztetési* szabály egyezés 3-mal járul hozzá a pontszámhoz. Egy *kritikus* szabályegyezés 5-öt ad hozzá.

|Severity  |Érték  |
|---------|---------|
|Kritikus     |5|
|Hiba        |4|
|Figyelmeztetés      |3|
|Észrevesz       |2|

Van egy 5-ös küszöbérték az anomáliapontszámnak, hogy blokkolja a forgalmat. Így egyetlen *kritikus szabály* egyezés elegendő az Application Gateway WAF letiltani egy kérelmet, még a megelőzés módban is. De egy *figyelmeztetési* szabály egyezés csak növeli az anomália pontszám 3,, ami önmagában nem elég, hogy blokkolja a forgalmat.

> [!NOTE]
> A WAF-szabály forgalomnak megfelelő waf-szabály esetén naplózott üzenet tartalmazza a "Letiltva" műveletértéket. De a forgalom valójában csak blokkolt egy anomália pontszám 5 vagy magasabb.  

### <a name="waf-monitoring"></a>WAF-felügyelet

Fontos az alkalmazásátjáró állapotának folyamatos figyelése. A WAF és az általa védett alkalmazások állapotát az Azure Security Center, az Azure Monitor és az Azure Monitor naplóival való integráció támogatja.

![Az Application Gateway WAF diagnosztikájának diagramja](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Az Alkalmazásátjáró-naplók integrálva vannak az [Azure Monitorszolgáltatással.](../../azure-monitor/overview.md) Ez lehetővé teszi a diagnosztikai információk, köztük a WAF-riasztások és naplók nyomon követését. Ezt a funkciót a **diagnosztika** lapon az Application Gateway erőforrás a portálon, vagy közvetlenül az Azure Monitoron keresztül. A naplók engedélyezéséről az [Application Gateway diagnosztikája című témakörben olvashat bővebben.](../../application-gateway/application-gateway-diagnostics.md)

#### <a name="azure-security-center"></a>Azure Security Center

[A Security Center](../../security-center/security-center-intro.md) segítségével megelőzheti, észlelheti és reagálhat azokra. Az Azure-erőforrások biztonságának jobb láthatóságát és szabályozását biztosítja. Az Application Gateway integrálva van [a Security Center alkalmazással.](../../application-gateway/application-gateway-integration-security-center.md) A Security Center megvizsgálja a környezetet a nem védett webalkalmazások észleléséhez. A veszélyeztetett erőforrások védelméhez javasolhatja az Application Gateway WAF-ot. A tűzfalakat közvetlenül a Biztonsági központból hozza létre. Ezek a WAF-példányok integrálva vannak a Security Centerrel. Riasztásokat és állapotinformációkat küldenek a Biztonsági központnak jelentéskészítésre.

![A Biztonsági központ – áttekintő ablak](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

A Microsoft Azure Sentinel egy méretezhető, felhőalapú natív, biztonsági információk eseménykezelési (SIEM) és biztonsági vezénylési automatizált válasz (SZÁRNYLony) megoldás. Az Azure Sentinel intelligens biztonsági elemzéseket és fenyegetésfelderítést biztosít a vállalaton belül, egyetlen megoldást kínálva a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra és a fenyegetésre adott válaszlépésekre.

A beépített Azure WAF tűzfal események munkafüzet, áttekintést kaphat a WAF biztonsági eseményeit. Ez magában foglalja az eseményeket, az egyező és letiltott szabályokat, valamint minden mást, amely a tűzfal naplókba kerül. További információk a naplózásról alább. 


![Sentinel](../media/ag-overview/sentinel.png)

#### <a name="logging"></a>Naplózás

Az Application Gateway WAF részletes jelentést nyújt az észlelt minden egyes fenyegetésről. A naplózás integrálva van az Azure Diagnostics naplóival. A riasztások .json formátumban kerülnek rögzítésre. Ezek a naplók integrálhatók az [Azure Monitor naplóiba.](../../azure-monitor/insights/azure-networking-analytics.md)

![Az Application Gateway diagnosztikai naplói ablakok](../media/ag-overview/waf2.png)

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

A WAF_v1 és a WAF_v2 suk-ok díjszabási modelljei eltérőek. További információért tekintse meg az [Application Gateway díjszabási](https://azure.microsoft.com/pricing/details/application-gateway/) oldalát. 

## <a name="next-steps"></a>További lépések

- Első lépések [waf-szabályzat létrehozásával](create-waf-policy-ag.md)
- További információ a [WAF által kezelt szabályokról](application-gateway-crs-rulegroups-rules.md)
- További információ [az egyéni szabályokról](custom-waf-rules-overview.md)
- További információ [az Azure Bejárati ajtaján lévő webalkalmazás-tűzfalról](../afds/afds-overview.md)

