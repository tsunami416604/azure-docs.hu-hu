---
title: Azure webalkalmazási tűzfal (WAF) v2 egyéni szabályok
description: Ez a cikk áttekintést nyújt a Web Application Firewall (WAF) v2 egyéni szabályairól az Azure Application Gatewayban.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 8ae5c9b6b52ea13e3d0981664e8c920cc5b47a01
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263559"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>Áttekintés: webalkalmazási tűzfal v2 egyéni szabályai

Az Azure Application Gateway webalkalmazási tűzfal (WAF) v2 egy előre konfigurált, platform által felügyelt szabálykészlet, amely számos különböző típusú támadás elleni védelmet nyújt. Ezek a támadások többek között a helyek közötti parancsfájlkezelést, az SQL-injektálást és másokat is tartalmaznak. Ha Ön WAF-rendszergazda, érdemes megírnia a saját szabályait az alapvető szabálykészlet szabályainak kibővítéséhez. A szabályok a megfelelő feltételek alapján letilthatják vagy engedélyezhetik a kért forgalmat.

Egyéni szabályokkal saját szabályokat hozhat létre, amelyeket a rendszer az WAF-on keresztül áthaladó kérelmek esetében értékel ki. Ezek a szabályok magasabb prioritással rendelkeznek, mint a felügyelt szabálykészlet többi szabálya. Az egyéni szabályok tartalmazzák a szabály nevét, a szabály prioritását és a megfelelő feltételek tömbjét. Ha ezek a feltételek teljesülnek, a rendszer műveletet végez az engedélyezéshez vagy a blokkoláshoz.

Létrehozhat például egy szabályt, amely az IP-címről érkező összes kérést letiltja a 192.168.5.4/24 tartományon belül. Ebben a szabályban az operátor *IPMatch*, a *matchValues* az IP-címtartomány (192.168.5.4/24), a *művelet* pedig blokkolja a forgalmat. A szabály nevét és prioritását is megadhatja.

Az egyéni szabályok az összetett logika használatát teszik lehetővé a biztonsági igényeket kielégítő speciális szabályok megadásához. Például "(1. feltétel *és* 2. feltétel), *vagy* a 3. feltétel)" azt jelenti, hogy ha *az 1. feltétel teljesül* , *vagy* ha a 3. feltétel teljesül, a WAF az egyéni szabályban megadott műveletet kell végrehajtania.

Az ugyanazon a szabályon belüli eltérő feltételek mindig a *és*a használatával vannak összekapcsolva. Például egy olyan szabály, amely a *és* a használatával letiltja a forgalmat egy adott IP-címről, és csak akkor, ha egy adott böngésző van használatban.

Ha két különböző feltételt *szeretne használni,* a két feltételnek különböző szabályoknak kell lennie. A *vagy* a által használt szabály például letilthatja a forgalmat egy adott IP-címről, vagy letilthatja a forgalmat, ha egy adott böngésző van használatban.

> [!NOTE]
> Az egyéni WAF-szabályok maximális száma 100. A Application Gateway korlátozásokkal kapcsolatos további információkért tekintse meg az [Azure-előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../azure-subscription-service-limits.md#application-gateway-limits)című témakört.

A reguláris kifejezések az egyéni szabályokban is támogatottak, ugyanúgy, mint az alapvető szabálykészlet esetében. Ezekre a szabályokra példákat a "3. példa" és az "example 5" című témakörben talál az [Egyéni webalkalmazási tűzfalszabályok létrehozása és használata](create-custom-waf-rules.md)című részben.

## <a name="allowing-or-blocking-traffic"></a>Forgalom engedélyezése vagy letiltása

A forgalom engedélyezése vagy blokkolása egyszerű az egyéni szabályokkal. Letilthatja például az IP-címtartományból származó összes forgalmat. Ha a kérelem egy adott böngészőből származik, egy másik szabályt is beállíthat, amely engedélyezi a forgalmat.

Ha engedélyezni szeretné a kívánt értéket, győződjön meg arról, hogy a `-Action` paraméter **engedélyezve**értékre van állítva. Ha blokkolni szeretne valamit, győződjön meg arról, hogy a `-Action` paraméter a **Letiltás**értékre van beállítva, ahogy az a következő kódban látható:

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

Az előző `$BlockRule` a következő egyéni szabályhoz rendeli a Azure Resource Managerban:

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

Ez az egyéni szabály tartalmaz egy nevet, egy prioritást, egy műveletet, valamint az egyeztetési feltételek egy olyan tömbjét, amelynek teljesülnie kell a művelet végrehajtásához. Az egyéni szabályok mezőinek leírását az alábbi részekben találja. Az egyéni szabályokra vonatkozó példákat az [Egyéni webalkalmazási tűzfalszabályok létrehozása és használata](create-custom-waf-rules.md)című témakörben talál.

## <a name="custom-rule-fields"></a>Egyéni szabályok mezői

### <a name="name-optional"></a>Név (nem kötelező)

Ez a szabály neve. A név megjelenik a naplókban.

### <a name="priority-required"></a>Prioritás (kötelező)

- A prioritás határozza meg, hogy a szabályok milyen sorrendben legyenek kiértékelve. Minél kisebb a érték, annál korábbi a szabály kiértékelése. A megengedett tartomány 1 és 100 között van.
- A prioritásnak egyedinek kell lennie az összes egyéni szabály között. A 40 prioritású szabályt a rendszer a 80 prioritású szabály előtt értékeli ki.

### <a name="rule-type-required"></a>Szabály típusa (kötelező)

A szabály típusának jelenleg **MatchRule**kell lennie.

### <a name="match-variable-required"></a>Egyezési változó (kötelező)

Az egyeztetési változónak a következők egyikének kell lennie:

- RemoteAddr: a távoli számítógép-kapcsolatok IP-címe vagy állomásneve
- RequestMethod: a HTTP-kérelem metódusa (GET, POST, PUT, DELETE stb.).
- QueryString: a változó az URI-ban.
- PostArgs: a POST szövegtörzsben elküldett argumentumok. Az ezt a megfeleltetési változót használó egyéni szabályok csak akkor lesznek alkalmazva, ha a Content-Type fejléc az "application/x-www-Form-urlencoded" és a "multipart/form-adatértékek" értékre van beállítva.
- RequestUri: a kérelem URI-ja.
- RequestHeaders: a kérelem fejlécei.
- RequestBody: a teljes kérelem törzsét tartalmazó változó egésze. Az ezt a megfeleltetési változót használó egyéni szabályok csak akkor lesznek alkalmazva, ha a Content-Type fejléc az "application/x-www-Form-urlencoded" értékre van beállítva. 
- RequestCookies: a kérelem cookie-jai.

### <a name="selector-optional"></a>Választó (nem kötelező)

A választó ismerteti a matchVariable-gyűjtemény mezőjét. Ha például a matchVariable "RequestHeaders", a választó lehet a User-Agent fejlécben.

### <a name="operator-required"></a>Operátor (kötelező)

Az operátornak a következők egyikének kell lennie:

- IPMatch: ez az operátor csak akkor használható, ha a Match változó értéke *RemoteAddr*.
- Egyenlő: a bemenet megegyezik a MatchValue.
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- regex

### <a name="negate-condition-optional"></a>Tagadási feltétel (nem kötelező)

Megtagadja az aktuális feltételt.

### <a name="transform-optional"></a>Átalakítás (nem kötelező)

Azoknak a sztringeknek a listája, amelyeknek a neve megegyezik a egyezés megkísérlése előtt elvégzendő átalakításokkal. Az átalakítások a következők:

- Kisbetűs
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Egyezési értékek (kötelező)

A matchValues mező azoknak az értékeknek a listája, amelyeknek egyezniük kell, ami lehet például *"ED*". Az értékek lehetnek például IP-címek vagy más karakterláncok. Az érték formátuma az előző operátortól függ.

### <a name="action-required"></a>Művelet (kötelező)

A művelet mező a következő beállításokat kínálja: 

- Engedélyezés: engedélyezi a tranzakciót, és kihagyja az összes további szabályt. Ez azt jelenti, hogy a megadott kérés hozzá lesz adva az engedélyezési listához, és a megfeleltetés után a kérés leáll, és a rendszer elküldi a háttér-készletnek. Az engedélyezési listán szereplő szabályok nem lesznek kiértékelve további egyéni szabályok vagy felügyelt szabályok esetén.

- Letiltás: blokkolja a tranzakciót a *SecDefaultAction* (észlelés/megelőzés mód) alapján. Az engedélyezés művelethez hasonlóan, miután a rendszer kiértékelte a kérést, és hozzáadja a tiltási listához, a rendszer leállítja a kiértékelést, és letiltja a kérést. Az azonos feltételeknek megfelelő további kérelmeket a rendszer nem értékeli ki. Csak le vannak tiltva. 

- Napló: lehetővé teszi, hogy a szabály írjon a naplóba, és lehetővé teszi a többi szabály futtatását a kiértékeléshez. A következő egyéni szabályok prioritási sorrendben, majd a felügyelt szabályok alapján lesznek kiértékelve.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az egyéni szabályokat, [létrehozhat saját egyéni szabályokat](create-custom-waf-rules.md)is.
