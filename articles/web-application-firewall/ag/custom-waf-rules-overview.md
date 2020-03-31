---
title: Az Azure Web Application Firewall (WAF) v2 egyéni szabályai az alkalmazásátjárón
description: Ez a cikk áttekintést nyújt a webalkalmazás-tűzfal (WAF) v2 egyéni szabályok az Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 072c7bd5b5b292ca4f0e53c59fcb7e9771331a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031731"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Egyéni szabályok a webalkalmazás-tűzfal v2-hez az Azure Application Gateway-en

Az Azure Application Gateway webalkalmazás-tűzfal (WAF) v2 egy előre konfigurált, platform által felügyelt szabálykészlettel érkezik, amely számos különböző típusú támadás elleni védelmet nyújt. Ezek a támadások közé tartozik a helyek közötti scripting, SQL injekciós, és mások. Ha WaF-rendszergazda vagy, érdemes lehet saját szabályokat írni az alapvető szabálykészlet (CRS) szabályainak bővítéséhez. A szabályok blokkolhatják vagy engedélyezhetik a kért forgalmat a megfelelő feltételek alapján.

Az egyéni szabályok lehetővé teszik, hogy saját szabályokat hozzon létre, amelyek kiértékelése a WAF-on keresztül átmenő minden egyes kérelemhez. Ezek a szabályok magasabb prioritással rendelkeznek, mint a kezelt szabálykészletek többi szabálya. Az egyéni szabályok szabálynevet, szabályprioritást és egy megfelelő feltételeket tartalmazó tömböt tartalmaznak. Ha ezek a feltételek teljesülnek, a művelet (engedélyezése vagy blokkolása).

Például letilthatja az összes kérelmet egy IP-címről a 192.168.5.4/24 tartományban. Ebben a szabályban az operátor *IPMatch*, a matchValues az IP-címtartomány (192.168.5.4/24), és a művelet a forgalom blokkolása. A szabály nevét és prioritását is beállíthatja.

Az egyéni szabályok támogatják az összetett logika használatát a biztonsági igényeket megoldó speciális szabályok létrehozására. Például (1. **feltétel és** 2. feltétel) **vagy** 3. feltétel). Ez azt jelenti, hogy ha az **or** **1.**

Az ugyanazon szabályon belüli különböző egyeztetési feltételek mindig a **és**a használatával lesznek összeadva. Például blokkolja a forgalmat egy adott IP-címről, és csak akkor, ha egy bizonyos böngészőt használ.

Ha azt szeretnénk, **vagy** két különböző feltételek, a két feltétel kell lennie a különböző szabályokat. Például blokkolja a forgalmat egy adott IP-címről, vagy blokkolja a forgalmat, ha egy adott böngészőt használ.

> [!NOTE]
> A WAF egyéni szabályok maximális száma 100. Az Application Gateway-korlátozásokról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)című témakörben talál további információt.

A reguláris kifejezéseket az egyéni szabályok is támogatják, csakúgy, mint a CRS szabályrendszerében. Példák: Példák 3. [Create and use custom web application firewall rules](create-custom-waf-rules.md)

## <a name="allowing-vs-blocking"></a>Lehetővé teszi vs blokkoló

A forgalom engedélyezése és blokkolása egyszerű az egyéni szabályokkal. Például letilthatja az IP-címek tartományából érkező összes forgalmat. Létrehozhat egy másik szabályt, amely engedélyezi a forgalmat, ha a kérés egy adott böngészőből érkezik.

Ha engedélyezni szeretné valamit, győződjön meg arról, hogy a `-Action` paraméter **Engedélyezése**. Ha le szeretne tiltani `-Action` valamit, győződjön meg arról, hogy a paraméter **blokk**.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Az `$BlockRule` előző leképezi a következő egyéni szabályt az Azure Resource Managerben:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Ez az egyéni szabály egy nevet, prioritást, egy műveletet és a művelet hez teljesítendő egyezési feltételek tömbjét tartalmazza. A mezők további magyarázatát az alábbi mezőleírásokban tetszésként. Például egyéni szabályok, [lásd: Egyéni webalkalmazás tűzfalszabályainak létrehozása és használata.](create-custom-waf-rules.md)

## <a name="fields-for-custom-rules"></a>Egyéni szabályok mezői

### <a name="name-optional"></a>Név [nem kötelező]

A szabály neve.  Megjelenik a naplókban.

### <a name="priority-required"></a>Prioritás [kötelező]

- Meghatározza a szabályértékelési sorrendet. Minél alacsonyabb az érték, annál korábban értékeli ki a szabályt. A megengedett tartomány 1-100. 
- Minden egyéni szabályban egyedinek kell lennie. A 40-es prioritású szabályt a rendszer a 80-as prioritású szabály előtt értékeli ki.

### <a name="rule-type-required"></a>Szabálytípus [kötelező]

Jelenleg **matchrule**-nek kell lennie.

### <a name="match-variable-required"></a>A [kötelező] változó egyeztetése

Az egyik változónak kell lennie:

- RemoteAddr – A távoli számítógép-kapcsolat IP-címe/állomásneve
- RequestMethod – HTTP-kérelem módszer (GET, POST, PUT, DELETE és így tovább.)
- QueryString – Változó az URI-ban
- PostArgs – A POST-törzsbe küldött argumentumok. Az egyezési változót használó egyéni szabályok csak akkor kerülnek alkalmazásra, ha a "Content-Type" fejléc "application/x-www-form-urlencoded" és "multipart/form-data" ("alkalmazás/x-www-form-urlencoded" és "multipart/form-data" lesz.
- RequestUri – A kérelem URI-ja
- RequestHeaders – a kérelem fejlécei
- RequestBody – Ez tartalmazza a teljes kérelem törzs egészére. Az egyezési változót használó egyéni szabályok csak akkor lesznek alkalmazva, ha a "Content-Type" fejléc "application/x-www-form-urlencoded" (alkalmazás/x-www-form-urlencoded' lesz. 
- RequestCookies - Cookie-k a kérelem

### <a name="selector-optional"></a>Választó [nem kötelező]

A matchVariable gyűjtemény mezőjének ismertetése. Ha például a matchVariable requestheaders, a választó lehet a *User-Agent* fejlécen.

### <a name="operator-required"></a>Operátor [kötelező]

A következő operátorok egyikének kell lennie:

- IPMatch - csak akkor használatos, ha a Változó egyeztetése *RemoteAddr*
- Egyenlő – a bevitel megegyezik a MatchValue értékkel
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith (Kezdete)
- EndsWith
- Regex
- Földrajzi egyezés (előzetes verzió)

### <a name="negate-condition-optional"></a>A feltétel megcáfolása [nem kötelező]

Tagadja a jelenlegi állapotot.

### <a name="transform-optional"></a>Átalakítás [nem kötelező]

Az egyeztetés idote előtt elvégezandó átalakítások neveit tartalmazó karakterláncok listája. Ezek a következő átalakítások lehetnek:

- Kisbetűs
- Trim
- UrlDekód
- UrlEncode 
- EltávolításNull
- HtmlEntityDecode

### <a name="match-values-required"></a>Értékek egyeztetése [kötelező]

Az egyező értékek listája, amely ről úgy lehet gondolni, hogy *OR*'ed. Lehet például IP-cím vagy más karakterlánc. Az értékformátum az előző operátortól függ.

### <a name="action-required"></a>Művelet [szükséges]

- Engedélyezés – Engedélyezi a tranzakciót, kihagyva az összes többi szabályt. A megadott kérelem hozzáadódik az engedélyezési listához, és miután egyeztetett, a kérelem leállítja a további kiértékelést, és elküldi a háttérkészletnek. Az engedélyezési listán szereplő szabályokat a rendszer nem értékeli ki további egyéni vagy felügyelt szabályokra.
- Blokk – Letiltja a tranzakcióalapján *SecDefaultAction* (észlelési/megelőzési mód). Csakúgy, mint a művelet engedélyezése, miután a kérelem kiértékelése és hozzáadása a tiltólista, kiértékelés leáll, és a kérelem le van tiltva. Minden kérelem után, amely megfelel az azonos feltételeknek nem lesz kiértékelve, és csak le kell tiltani. 
- Napló – Lehetővé teszi, hogy a szabály írjon a naplóba, de lehetővé teszi, hogy a többi szabály kiértékelésre fusson. A többi egyéni szabályt a rendszer prioritási sorrendben értékeli ki, amelyet a felügyelt szabályok követnek.

## <a name="geomatch-custom-rules-preview"></a>Földrajzi egyezés egyéni szabályai (előzetes verzió)

Az egyéni szabályok lehetővé teszik, hogy az alkalmazások és a biztonsági házirendek pontos igényeinek megfelelő, testre szabott szabályokat hozzon létre. A webalkalmazásokhoz való hozzáférést ország/régió szerint korlátozhatja. További információ: [Geomatch custom rules (preview)](geomatch-custom-rules.md).

## <a name="next-steps"></a>További lépések

Miután tudomást szerzett az egyéni szabályokról, [hozzon létre saját egyéni szabályokat.](create-custom-waf-rules.md)
