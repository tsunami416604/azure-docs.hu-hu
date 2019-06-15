---
title: Az Azure Application Gateway webalkalmazási tűzfal hibaelhárítása
description: Ez a cikk az Azure Application Gateway webalkalmazási tűzfala (WAF) a hibaelhárítási információkat
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082442"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Webalkalmazási tűzfal (WAF) az Azure Application Gateway hibaelhárítása

Néhány dolgot teheti meg, ha kéréseket, amelyek kell átadnia keresztül a webalkalmazási tűzfala (WAF) le vannak tiltva.

Először is győződjön meg arról, hogy elolvasta a [WAF áttekintése](waf-overview.md) és a [WAF konfigurációs](application-gateway-waf-configuration.md) dokumentumokat. Győződjön meg arról, hogy engedélyezve van a is, [WAF-figyelés](application-gateway-diagnostics.md) a cikkek ismertetik, hogyan a WAF-függvények, hogyan a WAF-szabály beállítása a munkát, és a WAF-naplók elérése.

## <a name="understanding-waf-logs"></a>Understanding WAF-naplók

A WAF-naplókban célja, hogy minden egyes kérés matched vagy a WAF által blokkolt megjelenítése. Az összes kiértékelt kérelem matched vagy letiltott Főkönyv. Ha azt tapasztalja, hogy a WAF letiltja, hogy azt ne (hamis pozitív) kérelem, néhány műveleteket végezheti el. Először szűkítéséhez, és keresse meg az adott kérés. Nézze át a naplókat, keresse meg a kérés adott URI-t, időbélyegző vagy tranzakció azonosítója. Ha megtalálta a hozzárendelt naplóbejegyzéseket, elkezdheti a vakriasztások cselekedhet.

Tegyük fel például, egy megbízható adatforgalom a karakterláncot tartalmazó *1 = 1* haladnak át a WAF kívánt. Ha a kérelem, a WAF blokkolja-e a forgalmat, amely tartalmazza a *1 = 1* karakterlánc minden olyan paramétert vagy a mezőben. Ez egy karakterláncérték gyakran társított SQL-injektálásos támadásokkal szemben. Nézze át a naplókat, és tekintse meg a kérelmet, és a blokkolt/egyező szabályok időbélyegét.

A következő példában láthatja, hogy négyet aktivált során a kérésben (tranzakcióazonosító mező használatával). Az elsőt arról tájékoztat, mert a felhasználó egy numerikus/IP-címet használva egyezést mutat, amely növeli a anomáliadetektálás pontszámot három figyelmeztető üzenetet, mert a kérelem URL-CÍMÉT. A következő szabályt, amely megfelel érték 942130, amely a keresett egy. Megtekintheti a *1 = 1* a a `details.data` mező. Ez további nő a anomáliadetektálás pontszámot három újra, mert egyben egy figyelmeztetés. Általában minden a szabály, amely rendelkezik a művelet **megfeleltetett** növeli az anomáliadetektálási pontozása, és ezen a ponton a anomáliadetektálás pontszámot lenne a hat. További információkért lásd: [Anomáliadetektálási pontozási mód](waf-overview.md#anomaly-scoring-mode).

Az utolsó két naplóbejegyzések mutatják a kérelem le lett tiltva, mert az anomáliadetektálás pontszámot nem volt elég magas. Ezek a bejegyzések rendelkeznek, mint a másik kettőt egy másik művelet. Látszanak azok ténylegesen *blokkolt* a kérelmet. Ezek a szabályok megadása kötelező, és nem tiltható le. Nem kell azt hitték szabályok, hanem több WAF belső az alapvető infrastruktúra.

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

## <a name="fixing-false-positives"></a>Vakriasztások kijavítása

Ezt az információt, és az ismeretek 942130, amely egyezik a szabály által a *1 = 1* karakterlánc, lehetőség van néhány ahhoz, hogy ez ne blokkolja a forgalmat:

- Kizárási lista használata

   Lásd: [WAF konfigurációs](application-gateway-waf-configuration.md#waf-exclusion-lists) bővebben kizárási sorolja fel.
- A szabály letiltása.

### <a name="using-an-exclusion-list"></a>Kizárási lista használatával

Ahhoz, hogy tájékozott döntést kezelése a hamis pozitív, fontos, hogy megismerje a technológiákat, az alkalmazás használja a. Tegyük fel például, egy SQL-kiszolgáló nem a technológia, és ezeket a szabályokat kapcsolatos téves azért kapta. Ezek a szabályok letiltása nem feltétlenül a biztonság gyengítheti az.

Kizárási lista használatának egyik előnye az, hogy csak egy adott részét kérelem le van tiltva. Azonban ez azt jelenti, hogy egy adott kizárás összes forgalmat egy globális beállítás, mert a WAF áthaladó alkalmazandó. Például ez vezethet probléma Ha *1 = 1* kérés törzsében egy bizonyos alkalmazás, de nem mások számára. Egy másik előnye, hogy választhat törzse, fejlécek és cookie-k kizárandó, ha egy bizonyos feltétel teljesül, ellentétben a teljes kérést kivételével.

Néha előfordul vannak esetek, ahol a WAF-t, hogy nem lehet intuitív meghatározott paraméterek lekérése átadott. Van például egy jogkivonatot, amely az Azure Active Directoryval hitelesítésekor át. Ez a token *__RequestVerificationToken*, a kérelem cookie-k, az általában átadni. Azonban bizonyos esetekben, ahol a cookie-k le vannak tiltva, ezt a tokent is átadott kérelemattribútum vagy "arg". Ha ez történik, győződjön meg arról, hogy kell *__RequestVerificationToken* , a kizárási listához való hozzáadásakor egy **attribútum neve** is.

![Korlátozások](media/waf-tshoot/exclusion-list.png)

Ebben a példában a kizárni kívánt a **kérelem attribútumnév** , amely egyenlő *text1*. Ez az kétségtelenül fennáll-e, mert az attribútum nevét, a tűzfal-naplókban tekintheti: **adatokat: Megfeleltetett adatokat: 1 = 1 ARGS:text1 található: 1=1**. Az attribútum **text1**. Is megkeresheti az attribútumnév néhány más módokon, lásd: [keresése kérelem attribútumnevek](#finding-request-attribute-names).

![WAF kizárási listák](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>Szabályok letiltása

Egy másik módja egy hamis pozitív megkerülésében, amelyek megfelelnek a bemenetet a WAF gondolat a szabály letiltása rosszindulatú volt. Mivel már elemezni a WAF-naplókban és keskenyebb lett a szabály le 942130, tiltsa le azt az Azure Portalon. Lásd: [webalkalmazási tűzfalszabályok az Azure Portalon keresztül testreszabása](application-gateway-customize-waf-rules-portal.md).

Egy szabály letiltása előnye, hogy ha tudja, hogy minden forgalmat, amely tartalmaz egy bizonyos feltételnek, amelyek normál esetben le lesznek tiltva érvényes forgalmat, letilthatja, hogy a szabály a teljes WAF számára. Azonban ha csak egy meghatározott alkalmazási helyzet érvényes forgalom, megnyitásához egy biztonsági másolatot egy globális beállítás, mert ez a szabály letiltása a teljes WAF.

Ha szeretné használni az Azure Powershellt, tekintse meg [webalkalmazási tűzfalszabályok Powershellen keresztül testreszabása](application-gateway-customize-waf-rules-powershell.md). Ha szeretné használni az Azure CLI-vel, tekintse meg [webalkalmazási tűzfalszabályok az Azure CLI-n keresztül testreszabása](application-gateway-customize-waf-rules-cli.md).

![WAF-szabályok](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Kérelem attribútumnevek keresése

Segítségével [Fiddler](https://www.telerik.com/fiddler), vizsgálja meg az egyes kéréseket, és határozza meg, milyen konkrét mezők weblap neve. Ez segít bizonyos mezők kizárása kizárási listákat használó ellenőrzés.

Ebben a példában láthatja, hogy a mező, a *1 = 1* karakterlánc lett megadva a neve **text1**.

![Fiddler](media/waf-tshoot/fiddler-1.png)

Ez az egy mezőt, kizárhat. Kizárási listák kapcsolatos további információkért lásd: [webes méretű alkalmazások tűzfal kérelmekre vonatkozó korlátok és kizárási listák](application-gateway-waf-configuration.md#waf-exclusion-lists). Az értékelés ebben az esetben is kizárhat úgy konfigurálja a következő kizárásával:

![WAF kizárása](media/waf-tshoot/waf-exclusion-02.png)

A tűzfalnaplók, mire lenne szüksége a kizárási listához hozzáadni az információkat is ellenőrizheti. Engedélyezheti a naplózást [háttérrendszer állapota, diagnosztikai naplók és mérőszámok az Application Gateway](application-gateway-diagnostics.md).

A tűzfal naplóban, és megtekintheti a PT1H.json fájlhoz fűzve az órát, hogy a kérés azt szeretné, hogy mi történt.

Ebben a példában láthatja, hogy rendelkezik-e az azonos tranzakcióazonosító négy szabályokat, és, hogy minden pontosan egy időben történt:

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

Hogyan ismereteit a CRS szabály beállítja a munkahelyi, és hogy a rendszer pontozási anomáliát együttműködik a CRS 3.0-s szabálykészletben (lásd: [az Azure Application Gateway webalkalmazási tűzfal](waf-overview.md)) tudja, hogy az utolsó két a szabályok a  **művelet: Blokkolt** tulajdonság forgalomszűrők blokkolják a teljes anomáliadetektálási pontszám alapján. A szabályokat, hogy arra koncentrálhasson, az első két.

Az első bejegyzés kerül, mert a felhasználó egy numerikus IP-cím segítségével keresse meg az Application Gateway, amely figyelmen kívül hagyható ebben az esetben.

A második (szabály 942130) egyike érdekes azt. Láthatja az adatokat, hogy (1 = 1) mintát egyezést mutat, és a mező neve **text1**. Ugyanezekkel a lépésekkel előző kizárása a **kérés attribútum neve** , amely **egyenlő** **1 = 1**.

## <a name="finding-request-header-names"></a>Kérelem fejlécnevek keresése

Fiddler az eszköz ismét a kérést a fejlécnevek kereséséhez. Az alábbi képernyőképen látható a GET kérelem a fejlécek, többek között *Content-Type*, *felhasználói ügynök*, és így tovább.

![Fiddler](media/waf-tshoot/fiddler-2.png)

Kérelmek és válaszfejlécek megtekintése egy másik úgy, hogy nyissa meg a fejlesztői eszközök, a Chrome-ban. F12 billentyű lenyomásával, vagy kattintson a jobb gombbal -> **vizsgálat** -> **fejlesztői eszközök**, és válassza ki a **hálózati** fülre. Betölthet egy weboldalt, és kattintson a kívánt vizsgálhatja meg a kérelmet.

![Chrome F12 billentyű](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Keresés kérelem cookie neve

Ha a kérelem tartalmazza a cookie-kat, a **cookie-k** lapon kiválaszthatja, megtekintheti őket a fiddler esetében.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Globális paraméterek a hamis pozitív jelzések elkerülése korlátozása

- Kérelem törzse ellenőrzés letiltása

   Beállításával **vizsgálat kéréstörzs** kapcsolja ki, az összes forgalom kéréstörzsekkel nem értékelhető a WAF által. Ez akkor lehet hasznos, ha tudja, hogy a kéréstörzsekkel nem rosszindulatú az alkalmazáshoz.

   Ha letiltja ezt a beállítást, csak a kérés törzse nem megvizsgálni. A fejlécek és a cookie-k továbbra is ellenőrzött, kivéve, ha egyes is ki vannak zárva, a kizárási lista funkció használata.

- Fájlméret-korlátozások

   Ha a fájl mérete korlátozza a waf, akkor még korlátozza a történik a webkiszolgálókhoz irányuló támadások. Azáltal, hogy a nagy méretű fájlokat fel kell tölteni, a háttérbeli példányairól kockázatát is nő. Az alkalmazás normál használati esetek fájlok méretét korlátozó a támadások megelőzése érdekében egy másik módszerrel.

   > [!NOTE]
   > Ha tudja, hogy az alkalmazás soha nem kell minden olyan fájl feltöltése egy adott méretet, fent korlátozhatja, hogy egy korlát beállításával.

## <a name="next-steps"></a>További lépések

Lásd: [webalkalmazási tűzfal konfigurálása Application gatewayen](tutorial-restrict-web-traffic-powershell.md).
