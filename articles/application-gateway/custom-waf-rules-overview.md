---
title: Webalkalmazási tűzfal (WAF) az Azure egyéni szabályok
description: Ez a cikk az egyéni szabályok (WAF) az Azure Application Gateway webalkalmazási tűzfal áttekintést nyújt.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 62259749e04d66d78206a0bba77ce88f2c08c82f
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66689001"
---
# <a name="custom-rules-for-web-application-firewall"></a>Webalkalmazási tűzfal egyéni szabályok

> [!IMPORTANT]
> Az Azure Application Gateway WAF egyéni szabályok jelenleg nyilvános előzetes verzióként. **Egyéni szabályok csak a WAF_v2 Termékváltozat érhetők**.
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Application Gateway webalkalmazási tűzfala (WAF) tartalmaz egy előre konfigurált, a platform által kezelt szabálykészletben által kínált számos különböző típusú támadások elleni védelem. Ezeket a támadásokat hely scripting, SQL-injektálás és mások közötti közé tartozik. Ha Ön a WAF-rendszergazda, érdemes lehet írni, mivel megvédi a alapvető szabálykészlet saját szabályok (CRS) szabályokat állíthat be. A szabályok vagy blokkolhatják vagy engedélyezhetik a kért forgalmat, a megadott feltételeknek megfelelő alapján.

Egyéni szabályok lehetővé teszik a saját, amely értékeli az egyes kérések, amely áthalad a WAF-szabályok létrehozása. Ezek a szabályok egy magasabb prioritású, mint a szabályokat a többi felügyelt szabálykészletek tart. Az egyéni szabályok tartalmaznak, a szabály nevét, a szabály prioritása és a feltételeknek megfelelő tömbjét. Ha ezek a feltételek teljesülnek, egy műveletet (az engedélyezés vagy letiltás).

Letilthatja például IP-címet a tartomány 192.168.5.4/24 érkező kérelmek. Ez a szabály az operátor nem *IPMatch*, a matchValues az IP-címtartomány (192.168.5.4/24), és a művelet letiltja a forgalmat. Is be a szabály nevét és prioritását.

Egyéni szabályok támogatja, hogy speciális szabályokkal, cím, a biztonság kell tőkésítési logikai kapcsolattal. For example (1. feltétel **és** feltétel 2) **vagy** feltétel 3).  Ebben a példában azt jelenti, hogy ha a feltétel 1 **és** 2 feltétel teljesül, **vagy** 3 feltétel teljesülése esetén, a WAF kell azokat a műveleteket az egyéni szabály.

Különböző folyamatmegfeleltetési feltételek belül ugyanaz a szabály minden esetben vannak bonyolítja használatával **és**. Ha például blokkolja a forgalmat egy adott IP-címről, és csak akkor, ha azok bizonyos böngészőt használ.

Ha azt szeretné, hogy **vagy** két különböző feltételeket, a két feltételt kell lennie különböző szabályokat. Például blokkolja a forgalmat egy adott IP-cím vagy blokk forgalmat, ha azokat a konkrét böngészőt használ.

> [!NOTE]
> A WAF egyéni szabályok maximális száma pedig a 100. Application Gateway korlátainak kapcsolatos további információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md#application-gateway-limits).

Reguláris kifejezésekkel is támogatottak az egyéni szabályok, csakúgy, mint a CRS szabálykészletek. Ezek a példák: példák 3. és 5 [létrehozása és használata egyéni webalkalmazási tűzfalszabályok](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Engedélyezése és letiltása

Engedélyezéséről és tiltásáról forgalmat az egyéni szabályok egyszerű. Letilthatja például az IP-címek egy származó minden forgalmat. Egy másik szabállyal a forgalom engedélyezéséhez, ha a kérelem érkezik egy meghatározott böngészőből is felvehető.

Ahhoz, hogy valami, ellenőrizze, hogy a `-Action` paraméter értéke **engedélyezése**. Letiltja a hiba, ellenőrizze, hogy a `-Action` paraméter értéke **blokk**.

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

Az előző `$BlockRule` képez le az Azure Resource Manager az alábbi egyéni szabály:

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

Az egyéni szabály nevét, prioritás, műveletet és a megfelelő feltételek, amelyeknek teljesülniük kell a művelet, hogy végre lehessen hajtani a tömb tartalmazza. További mezők ismertetését a következő mezők leírása. Például az egyéni szabályok, lásd: [létrehozása és használata egyéni webalkalmazási tűzfalszabályok](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Egyéni szabályok mezők

### <a name="name-optional"></a>[Opcionális] neve

Ez a szabály nevét. Ez a név jelenik meg a naplókat.

### <a name="priority-required"></a>[Kötelező] prioritás

- Határozza meg a kiértékelt szabályok. A kisebb az érték, a szabály a korábbi értékelését.
-Minden egyéni szabályok között egyedinek kell lennie. 100 prioritású szabály 200-as prioritású szabály előtt értékelik.

### <a name="rule-type-required"></a>[Kötelező] szabálytípus

Jelenleg kell **MatchRule**.

### <a name="match-variable-required"></a>[Kötelező] egyezés változó

A változók egyikének kell lennie:

- RemoteAddr – IP cím vagy állomásnév a távoli számítógép-kapcsolat
- RequestMethod – HTTP-kérési metódus (GET, POST, PUT, DELETE stb.)
- Lekérdezési karakterlánc – az URI azonosító változó
- PostArgs – argumentumok küldött POST törzsében.
- RequestUri – a kérelem URI azonosítója
- RequestHeaders – a kérelem fejlécei
- RequestBody – a kérelem törzse
- RequestCookies – a kérelem cookie-k

### <a name="selector-optional"></a>[Opcionális] a választó

A mező matchVariable gyűjtemény ismerteti. Például, ha a matchVariable RequestHeaders, a választó oka az lehet az a *felhasználói ügynök* fejléc.

### <a name="operator-required"></a>[Kötelező] operátor

Az alábbi operátorok egyikének kell lennie:

- IPMatch – csak használja, ha egyezés változó *RemoteAddr*
- Egyenlő – bemeneti megegyezik a MatchValue
- tartalmaz
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- endsWith
- Regex

### <a name="negate-condition-optional"></a>[Opcionális] a feltétel negálandó

Az aktuális időjárási helyzetet ellentettjét adja.

### <a name="transform-optional"></a>[Opcionális]. átalakító

Kísérlet történik a átalakításokat is az egyezés előtt nevére karakterláncok listáját. Ezek lehetnek a következő átalakítások:

- Kisbetűs
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>[Kötelező] értékekre

Az egyeztetéshez, értékek listája, amelyből is értelmezhetők, hogy *vagy*"ed. Például annak oka az lehet IP-címeket vagy más karakterláncban. Az érték formátuma az előző operátor függ.

### <a name="action-required"></a>[Kötelező] művelet

- Engedélyezése – engedélyezi a tranzakciót, a rendszer kihagyja az összes további szabályra. Ez azt jelenti, hogy a megadott kérelem az engedélyezési listához, és megfelel a feltételeknek a kérelem leáll további értékelés, és a háttérkészlet küldendő kerül-e. Az engedélyezési listán lévő szabályok nem kerülnek kiértékelésre bármilyen további egyéni szabályok és a felügyelt szabályokat.
- Blokk – letiltja a tranzakció alapján *SecDefaultAction* (észlelési és megelőzés üzemmód). Az engedélyezési művelet, mint a kérelem értékeli ki, és a tiltólistához hozzáadva, értékelési leáll, és a kérelem le van tiltva. Minden kérelmet, amely megfelel-e az azonos után nem lesz kiértékelve, és csak le lesz tiltva. 
- Naplófájl – lehetővé teszi, hogy a szabály a naplóba, de lehetővé teszi, hogy a többi szabály értékelés futtatása. További egyéni szabályok a felügyelt szabályokat követ, fontossági sorrendben értékeli ki.

## <a name="next-steps"></a>További lépések

Egyéni szabályok megismerkedhet után [hozzon létre saját egyéni szabályok](create-custom-waf-rules.md).
