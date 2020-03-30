---
title: Hibaelhárítás – Azure webalkalmazás-tűzfal
description: Ez a cikk hibaelhárítási információkat tartalmaz az Azure Application Gateway webalkalmazás-tűzfalával (WAF) kapcsolatban
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046192"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Webalkalmazás-tűzfal (WAF) – hibaelhárítás az Azure Application Gateway alkalmazásátjáróhoz

Néhány dolgot megtehet, ha a webalkalmazás-tűzfalon (WAF) áthaladó kérelmek blokkolva vannak.

Először győződjön meg arról, hogy elolvasta a [WAF áttekintését](ag-overview.md) és a [WAF konfigurációs](application-gateway-waf-configuration.md) dokumentumokat. Győződjön meg arról is, hogy engedélyezte a [WAF-figyelést:](../../application-gateway/application-gateway-diagnostics.md) Ezek a cikkek ismertetik a WAF működését, a WAF-szabálykészletek működését és a WAF-naplók elérésének módját.

## <a name="understanding-waf-logs"></a>A WAF-naplók ismertetése

A WAF-naplók célja, hogy minden olyan kérést megjelenítsen, amelyet a WAF egyeztetett vagy blokkolt. Ez az összes kiegyenlített vagy blokkolt kiértékelt kérelem főkönyve. Ha azt veszi észre, hogy a WAF blokkolja a kérelmet, hogy nem kellene (hamis pozitív), meg tudod csinálni néhány dolgot. Először szűkítse le, és keresse meg az adott kérést. Tekintse át a naplókat, hogy megtalálja a kérelem adott URI-, időbélyeg- vagy tranzakcióazonosítóját. Amikor megtalálja a kapcsolódó naplóbejegyzéseket, elkezdheti a hamis pozitív műveleteket.

Tegyük fel például, hogy van egy legitim forgalma, amely az *1=1* karakterláncot tartalmazza, amelyet át szeretne haladni a WAF-on. Ha megpróbálja a kérelmet, a WAF blokkolja a forgalmat, amely tartalmazza a *1 =1* karakterlánc ot bármely paraméter vagy mezőben. Ez egy karakterlánc gyakran kapcsolódik egy SQL-injektálási támadáshoz. Megtekintheti a naplókat, és megtekintheti a kérelem időbélyegét és a blokkolt/egyeztetett szabályokat.

A következő példában láthatja, hogy négy szabály aktiválódik ugyanebben a kérésben (a TransactionId mező használatával). Az első azt mondja, hogy kiegyenlített, mert a felhasználó egy numerikus/IP URL-t használt a kérelemhez, ami hárommal növeli az anomáliapontszámát, mivel ez egy figyelmeztetés. A következő szabály, ami egyezik, a 942130, ami az, amit keres. Láthatjuk a *1 =* 1 `details.data` a területen. Ez tovább növeli az anomália pontszám három újra, mivel ez is egy figyelmeztetés. Általában minden szabály, amely a művelet **kiegyenlített** növeli az anomália pontszám, és ezen a ponton az anomália pontszám lenne hat. További információ: [Anomália pontozási mód.](ag-overview.md#anomaly-scoring-mode)

Az utolsó két naplóbejegyzés azt mutatja, hogy a kérelem blokkolva volt, mert az anomáliapontszám elég magas volt. Ezek a bejegyzések más művelettel rendelkeznek, mint a másik kettő. Azt mutatják, hogy valóban *blokkolta* a kérelmet. Ezek a szabályok kötelezőek, és nem tilthatók le. Nem úgy kell őket tekinteni, mint szabályokat, hanem inkább a WAF belső infrastruktúrájának.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Hamis pozitív állítások rögzítése

Ezzel az információval, és azzal a tudattal, hogy a 942130-as szabály az *1=1* karakterláncnak megfelelő, néhány dolgot megtehet, hogy megakadályozza a forgalom blokkolását:

- Kizárási lista használata

   A kizárási listákkal kapcsolatos további információkért lásd a [WAF-konfigurációt.](application-gateway-waf-configuration.md#waf-exclusion-lists)
- Tiltsa le a szabályt.

### <a name="using-an-exclusion-list"></a>Kizárási lista használata

Ahhoz, hogy tájékozott döntést hozzon a hamis pozitív kezelésről, fontos, hogy megismerkedjen az alkalmazás által használt technológiákkal. Tegyük fel például, hogy nincs SQL-kiszolgáló a technológiai veremben, és hamis pozitív a szabályokhoz kapcsolódóan. A szabályok letiltása nem feltétlenül gyengíti a biztonságát.

A kizárási lista használatának egyik előnye, hogy a kérelemnek csak egy adott része van letiltva. Ez azonban azt jelenti, hogy egy adott kizárás alkalmazható a WAF-on áthaladó összes forgalomra, mert globális beállítás. Ez például akkor okozhat problémát, ha az *1=1* egy adott alkalmazás testében érvényes kérés, mások esetében azonban nem. Egy másik előny, hogy választhat a törzs, a fejlécek és a cookie-k között, amelyeket ki kell zárni, ha egy bizonyos feltétel teljesül, szemben a teljes kérés kizárásával.

Esetenként vannak olyan esetek, amikor bizonyos paraméterek et olyan módon adnak át a WAF-nak, amely nem lehet intuitív. Például van egy jogkivonat, amely átkerül az Azure Active Directory használatával történő hitelesítéskor. Ez a jogkivonat, *__RequestVerificationToken,* általában kap át, mint egy cookie-kérelem. Bizonyos esetekben azonban, amikor a cookie-k le vannak tiltva, ez a jogkivonat kérésattribútumként vagy "arg" néven is átkerül. Ha ez történik, meg kell győződnie arról, hogy *__RequestVerificationToken* is hozzá kerül a kizárási listához **kérelem attribútumnévként.**

![Kizárások](../media/web-application-firewall-troubleshoot/exclusion-list.png)

Ebben a példában ki szeretné zárni a Szövegnek megfelelő **Request attribútumnevet1.** *text1* Ez azért tűnik, mert az attribútum neve látható a tűzfalnaplóiban: **adatok: Egyező adatok: 1=1 található args:text1: 1=1**. Az attribútum **szöveg1**. Ezt az attribútumnevet néhány más módon is megtalálhatja, [lásd: A kérelem attribútumnevek keresése](#finding-request-attribute-names).

![WAF kizárási listák](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Szabályok letiltása

Egy másik módja annak, hogy kap körül egy hamis pozitív, hogy tiltsa le a szabályt, amely megfelelt a bemeneti waf gondoltam rosszindulatú volt. Mivel a WAF-naplókat elemezte, és leszűkítette a szabályt 942130-ra, letilthatja azt az Azure Portalon. Lásd: [Webes alkalmazások tűzfalszabályainak testreszabása az Azure Portalon keresztül.](application-gateway-customize-waf-rules-portal.md)

A szabály letiltásának egyik előnye, hogy ha ismeri az összes olyan forgalmat, amely egy bizonyos feltételt tartalmaz, amely általában blokkolva van, érvényes forgalom, letilthatja ezt a szabályt a teljes WAF-ra. Ha azonban csak egy adott használati esetben érvényes forgalomról van szó, a biztonsági rést úgy nyitja meg, hogy letiltja ezt a szabályt a teljes WAF-ra, mivel globális beállításról van szó.

Ha az Azure PowerShellt szeretné használni, olvassa [el a Webalkalmazás tűzfalszabályainak testreszabása a PowerShellen keresztül című témakört.](application-gateway-customize-waf-rules-powershell.md) Ha az Azure CLI-t szeretné használni, olvassa el [a Webalkalmazás tűzfalszabályainak testreszabása az Azure CLI-n keresztül című témakört.](application-gateway-customize-waf-rules-cli.md)

![WAF-szabályok](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Kérelem attribútumnevének keresése

A [Fiddler](https://www.telerik.com/fiddler)segítségével megvizsgálja az egyes kéréseket, és meghatározza, hogy a weboldal mely mezőit hívják. Ez segíthet kizárni bizonyos mezőket az ellenőrzésből a Kizárási listák használatával.

Ebben a példában láthatja, hogy az *1=1* karakterláncot beírt **mezőt szövegneknevezik1.**

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Ezt a mezőt kizárhatja. A kizárási listákról a [Webalkalmazás tűzfala kérelemméret-korlátja és kizárási listái](application-gateway-waf-configuration.md#waf-exclusion-lists)nak további információiból olvashat. Ebben az esetben a kiértékelést a következő kizárás beállításával zárhatja ki:

![WAF-kizárás](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

A tűzfalnaplókat is megvizsgálhatja, hogy az adatokat megkaphassa, hogy lássa, mit kell hozzáadnia a kizárási listához. A naplózás engedélyezéséhez olvassa el az [Alkalmazásátjáró háttérállapotának, diagnosztikai naplóinak és metrikáinak című témakört.](../../application-gateway/application-gateway-diagnostics.md)

Vizsgálja meg a tűzfal naplóját, és tekintse meg a PT1H.json fájlt arra az órára vonatkozóan, amikor a vizsgálatozni kívánt kérelem megtörtént.

Ebben a példában láthatja, hogy négy szabály a transactionID, és hogy minden történt ugyanabban az időben:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

A CRS-szabálykészletek működésének ismeretében, és hogy a CRS ruleset 3.0 egy anomáliapontozási rendszerrel működik (lásd: [Web Application Firewall for Azure Application Gateway),](ag-overview.md)akkor tudja, hogy a művelettel rendelkező két alsó **szabály: A letiltott** tulajdonság a teljes anomáliapontszám alapján blokkol. A szabályok, hogy összpontosítson az első kettő.

Az első bejegyzés naplózva van, mert a felhasználó egy numerikus IP-címet használt az Alkalmazásátjáróhoz való navigáláshoz, amely ebben az esetben figyelmen kívül hagyható.

A második (942130 szabály) az érdekes. A részletekben látható, hogy egy mintának megfelelő (1=1) és a mező **neve szöveg1**. Az előző lépésekkel zárhatja ki az **equals** **1=1**értékű **Kérelem attribútumnevét.**

## <a name="finding-request-header-names"></a>Kérelemfejléc-nevek keresése

Fiddler egy hasznos eszköz ismét keresni kérés fejléc nevét. A következő képernyőképen láthatja a GET-kérelem fejléceit, amelyek tartalmazzák *a Content-Type*, *User-Agent*és így tovább.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

A kérelem- és válaszfejlécek megtekintésének másik módja a Chrome fejlesztői eszközeinek megtekintése. Lenyomhatja az F12 billentyűt, vagy a jobb gombbal a ->**A fejlesztői eszközök** **vizsgálata** -> parancsra kattinthat, és válassza a **Hálózat** lapot.

![Króm F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Kérelemcookie-nevek keresése

Ha a kérés cookie-kat tartalmaz, a **Cookie-k** lap bejelölhető a Fiddler nézetére.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>A globális paraméterek korlátozása a hamis pozitív értékek kiküszöbölése érdekében

- A kérelemkérő test ellenőrzésének letiltása

   Az **Ellenőrzés kérelem törzsének** kikapcsolásával a WAF nem értékeli ki az összes forgalom kérésszerveit. Ez akkor lehet hasznos, ha tudja, hogy a kérelem-szervek nem rosszindulatú az alkalmazás.

   A beállítás letiltásával csak a kérelem törzse nem lesz megvizsgálva. A fejlécek és a cookie-k továbbra is ellenőrzöttek maradnak, kivéve, ha az egyes eket a kizárási lista funkcióval kizárják.

- Fájlméret-korlátok

   A WAF fájlméretének korlátozásával korlátozza a webkiszolgálókkal való támadás lehetőségét. Azáltal, hogy lehetővé teszi a nagy fájlok feltöltését, a háttérrendszer túlterheltsének kockázata nő. A fájlméret korlátozása az alkalmazás normál használati esetére csak egy másik módja a támadások megelőzésének.

   > [!NOTE]
   > Ha tudja, hogy az alkalmazásnak soha nem lesz szüksége egy adott méret feletti fájlfeltöltésre, korlátozhatja ezt egy korlát beállításával.

## <a name="firewall-metrics-waf_v1-only"></a>Tűzfal-mérőszámok (csak WAF_v1)

A v1 webalkalmazás-tűzfalak esetében a következő mérőszámok érhetők el a portálon: 

1. Webalkalmazás-tűzfal blokkolt kérelmek száma A letiltott kérelmek száma
2. Webalkalmazás tűzfal a tiltott szabályok száma Minden szabály, amely megfelelt, **és** a kérelem le van tiltva
3. Webalkalmazás-tűzfal összes szabályeloszlása Az értékelés során egyeztetett összes szabály
     
A mérőszámok engedélyezéséhez válassza a **Metrikák** lapot a portálon, és válassza ki a három mutató egyikét.

## <a name="next-steps"></a>További lépések

Lásd: [Webalkalmazás-tűzfal konfigurálása az Application Gateway alkalmazásban.](tutorial-restrict-web-traffic-powershell.md)
