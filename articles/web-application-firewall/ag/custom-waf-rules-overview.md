---
title: Azure webalkalmazási tűzfal (WAF) v2 egyéni szabályok Application Gateway
description: Ez a cikk áttekintést nyújt a Web Application Firewall (WAF) v2 egyéni szabályairól az Azure Application Gateway-on.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.openlocfilehash: 323f01e08007260d4fb6d651b20937c5d5d5e357
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645089"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Egyéni szabályok a webalkalmazási tűzfal v2-hez az Azure Application Gateway

Az Azure Application Gateway webalkalmazási tűzfal (WAF) v2 olyan előre konfigurált, platform által felügyelt szabályokat tartalmaz, amelyek számos különböző típusú támadás elleni védelmet biztosítanak. Ezek a támadások többek között a site Scripting, az SQL-injektálás és egyebek. Ha Ön WAF-rendszergazda, érdemes lehet saját szabályokat írnia az alapszintű szabálykészlet (CRS) szabályainak kibővítéséhez. A szabályok a megfelelő feltételek alapján letilthatják vagy engedélyezhetik a kért forgalmat.

Az egyéni szabályok segítségével saját szabályokat hozhat létre, amelyeket a rendszer a WAF áthaladó kérelmek esetében kiértékel. Ezek a szabályok magasabb prioritással rendelkeznek, mint a felügyelt szabálykészlet többi szabálya. Az egyéni szabályok tartalmazzák a szabály nevét, a szabály prioritását és a megfelelő feltételek tömbjét. Ha ezek a feltételek teljesülnek, a rendszer egy műveletet végez (Engedélyezés vagy Letiltás).

Például letilthatja az összes kérést egy IP-címről a 192.168.5.4/24 tartományba. Ebben a szabályban az operátor *IPMatch*, a matchValues az IP-címtartomány (192.168.5.4/24), a művelet pedig blokkolja a forgalmat. A szabály nevét és prioritását is megadhatja.

Az egyéni szabályok az összetett logika használatát teszik lehetővé a biztonsági igényeket kielégítő speciális szabályok megadásához. Például: (1. feltétel **és** 2. feltétel) **vagy** feltétel 3).  Ez a példa azt jelenti, hogy ha az 1. **és** a 2. feltétel teljesül, **vagy** ha a 3. feltétel teljesül, a WAF el kell végeznie az egyéni szabályban megadott műveletet.

Ugyanazon szabályon belül a különböző egyeztetési feltételek mindig a **és**a használatával vannak összekapcsolva. Például letilthatja a forgalmat egy adott IP-címről, és csak akkor, ha egy adott böngészőt használnak.

Ha **vagy** két különböző feltételt szeretne használni, a két feltételnek különböző szabályokban kell lennie. Például letilthatja a forgalmat egy adott IP-címről, vagy letilthatja a forgalmat, ha egy adott böngészőt használnak.

> [!NOTE]
> Az egyéni WAF-szabályok maximális száma 100. A Application Gateway korlátozásokkal kapcsolatos további információkért tekintse meg az [Azure-előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)című témakört.

A reguláris kifejezések az egyéni szabályokban is támogatottak, ugyanúgy, mint a CRS-szabályrendszerek. Ezekről a példákat lásd: 3. és 5. példa az [Egyéni webalkalmazási tűzfalszabályok létrehozása és használata](create-custom-waf-rules.md)című témakörben.

## <a name="allowing-vs-blocking"></a>Tiltás és blokkolás engedélyezése

A forgalom engedélyezése és blokkolása egyszerű az egyéni szabályokkal. Letilthatja például az IP-címtartományból érkező összes forgalmat. Ha a kérelem egy adott böngészőből származik, egy másik szabályt is beállíthat, amely engedélyezi a forgalmat.

Ha engedélyezni szeretne valamit, győződjön meg arról, hogy a `-Action` paraméter értéke **Engedélyezés**. Ha blokkolni szeretne valamit, győződjön meg arról, hogy a `-Action` paraméter a **Letiltás**értékre van beállítva.

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

Az előző `$BlockRule` leképezi a következő egyéni szabályt a Azure Resource Managerban:

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

Ez az egyéni szabály tartalmaz egy nevet, egy prioritást, egy műveletet, valamint az egyeztetési feltételek azon tömbjét, amelynek teljesülnie kell a művelet elvégzéséhez. A mezők további ismertetését lásd a következő mezők leírásában. Például egyéni szabályok: [Egyéni webalkalmazási tűzfalszabályok létrehozása és használata](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Egyéni szabályok mezői

### <a name="name-optional"></a>Név [nem kötelező]

Ez a szabály neve. Ez a név jelenik meg a naplókban.

### <a name="priority-required"></a>Prioritás [kötelező]

- Meghatározza a szabály értékelési sorrendjét. Minél kisebb a érték, annál korábbi a szabály kiértékelése. A megengedett tartomány 1-100. 
- Egyedinek kell lennie az összes egyéni szabályban. A 40 prioritású szabályt a rendszer a 80 prioritású szabály előtt értékeli ki.

### <a name="rule-type-required"></a>Szabály típusa [kötelező]

Jelenleg **MatchRule**kell lennie.

### <a name="match-variable-required"></a>Egyezés változó [kötelező]

Az egyik változónak kell lennie:

- RemoteAddr – a távoli számítógép-kapcsolatok IP-címe/állomásneve
- RequestMethod – HTTP-kérelem módszere (GET, POST, PUT, DELETE stb.)
- QueryString – változó az URI-ban
- PostArgs – a POST törzsében elküldett argumentumok. Az ezt a megfeleltetési változót használó egyéni szabályok csak akkor lesznek alkalmazva, ha a "Content-Type" fejléc az "application/x-www-Form-urlencoded" és a "multipart/form-adatai" értékre van beállítva.
- RequestUri – a kérelem URI-ja
- RequestHeaders – a kérelem fejlécei
- RequestBody – a teljes kérelem törzsét tartalmazza egészként. Az ezt a megfeleltetési változót használó egyéni szabályok csak akkor lesznek alkalmazva, ha a "Content-Type" fejléc az "application/x-www-Form-urlencoded" értékre van beállítva. 
- RequestCookies – a kérelem cookie-jai

### <a name="selector-optional"></a>Választó [nem kötelező]

A matchVariable-gyűjtemény mezőjét ismerteti. Ha például a matchVariable RequestHeaders, a választó lehet a *User-Agent* fejlécben.

### <a name="operator-required"></a>Operátor [kötelező]

A következő operátorok egyikének kell lennie:

- IPMatch – csak akkor használható, ha a Match változó értéke *RemoteAddr*
- Equals – a bemeneti érték megegyezik a MatchValue
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- regex
- Geomatch (előzetes verzió)

### <a name="negate-condition-optional"></a>Tagadási feltétel [opcionális]

Megtagadja az aktuális feltételt.

### <a name="transform-optional"></a>Átalakítás [nem kötelező]

Azoknak a karakterláncoknak a listája, amelyeket a rendszer a egyezés megkísérlése előtt elvégez. Ezek a következő átalakítások lehetnek:

- Kisbetűs
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Értékek egyeztetése [kötelező]

Azoknak az értékeknek a listája, amelyek megfelelnek a következőnek *: "ED*". Lehetnek például IP-címek vagy más karakterláncok. Az érték formátuma az előző operátortól függ.

### <a name="action-required"></a>Művelet [kötelező]

- Engedélyezés – engedélyezi a tranzakciót, kihagyva az összes további szabályt. Ez azt jelenti, hogy a megadott kérés hozzá lesz adva az engedélyezési listához, és az egyeztetés után a kérés leáll, és a rendszer elküldi a háttér-készletnek. Az engedélyezési listán szereplő szabályok nincsenek kiértékelve a további egyéni szabályok vagy felügyelt szabályok esetében.
- Letiltás – blokkolja a tranzakciót a *SecDefaultAction* (észlelési/megelőzési mód) alapján. Az engedélyezés művelethez hasonlóan, ha a rendszer kiértékeli a kérést, és hozzáadja a blokkolási listához, a rendszer leállítja az értékelést, és letiltja a kérést. A rendszer nem értékeli ki azokat a kéréseket, amelyek után az azonos feltételek teljesülnek, és csak le lesz tiltva. 
- Log – lehetővé teszi a szabály írását a naplóba, de a többi szabály futtatását is lehetővé teszi a kiértékeléshez. A következő egyéni szabályok prioritási sorrendben, majd a felügyelt szabályok alapján lesznek kiértékelve.

## <a name="geomatch-custom-rules-preview"></a>Egyéni Geomatch-szabályok (előzetes verzió)

Az egyéni szabályok lehetővé teszik a testreszabott szabályok létrehozását az alkalmazások pontos igényeinek megfelelően, valamint a biztonsági házirendeket. Most már a nyilvános előzetes verzióban elérhető ország/régió alapján korlátozhatja a webalkalmazásokhoz való hozzáférést. Az összes egyéni szabályhoz hasonlóan ez a logika más szabályokkal is felhasználható az alkalmazás igényeinek megfelelően. 

   > [!NOTE]
   > A Geomatch egyéni szabályai az USA déli középső régiójában és Észak-Európában is elérhetők. Ha a portálon szeretné elérni őket, használja [ezt a hivatkozást](https://aka.ms/AppGWWAFGeoMatch) , amíg mindenki számára elérhetővé nem válik. 

Ha a Geomatch operátort használja, a választókat a következő kétszámjegyű országkódok bármelyike lehet. 

|Országkód | Ország neve |
| ----- | ----- |
| AD | Andorra |
| AE | Egyesült Arab Emírségek|
| AF | Afganisztán|
| AG | Antigua és Barbuda|
| AL | Albánia|
| AM | Örményország|
| AO | Angola|
| AR | Argentína|
| AS | Amerikai Szamoa|
| AT | Ausztria|
| AU | Ausztrália|
| AZ | Azerbajdzsán|
| BA | Bosznia-Hercegovina|
| BB | Barbados|
| BD | Banglades|
| BE | Belgium|
| BF | Burkina Faso|
| BG | Bulgária|
| BH | Bahrein|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthélemy|
| BN | Brunei Szultanátus|
| BO | Bolívia|
| BR | Brazília|
| BS | Bahama-szigetek|
| BT | Bhután|
| BW | Botswana|
| BY | Belarusz|
| BZ | Belize|
| CA | Kanada|
| CD | Kongói Demokratikus Köztársaság|
| CF | Közép-afrikai Köztársaság|
| CH | Svájc|
| CI | Elefántcsontpart|
| CL | Chile|
| CM | Kamerun|
| CN | Kína|
| CO | Kolumbia|
| CR | Costa Rica|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Ciprus|
| CZ | Csehország|
| DE | Németország|
| DK | Dánia|
| DO | Dominikai Köztársaság|
| DZ | Algéria|
| EC | Ecuador|
| EE | Észtország|
| EG | Egyiptom|
| ES | Spanyolország|
| ET | Etiópia|
| FI | Finnország|
| FJ | Fidzsi|
| FM | Mikronéziai Szövetséges Államok|
| JK | Franciaország|
| GB | Egyesült Királyság|
| GE | Grúzia|
| GF | Francia Guyana|
| GH | Ghána|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Görögország|
| GT | Guatemala|
| GY | Guyana|
| HK | Hongkong KKT|
| HN | Honduras|
| HR | Horvátország|
| HT | Haiti|
| HU | Magyarország|
| ID (Azonosító) | Indonézia|
| IE | Írország|
| IL | Izrael|
| IN | India|
| IQ | Irak|
| IR | Iráni Iszlám Köztársaság|
| IS | Izland|
| IT | Olaszország|
| JM | Jamaica|
| JO | Jordánia|
| JP | Japán|
| KE | Kenya|
| KG | Kirgizisztán|
| KH | Kambodzsa|
| KI | Kiribati|
| KN | Saint Kitts és Nevis|
| KP | Koreai Népi Demokratikus Köztársaság|
| KR | Koreai Köztársaság|
| KW | Kuvait|
| KY | Kajmán-szigetek|
| KZ | Kazahsztán|
| LATIN-AMERIKA | Laoszi Népi Demokratikus Köztársaság|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Srí Lanka|
| LR | Libéria|
| LS | Lesotho|
| LT | Litvánia|
| LU | Luxemburg|
| LV | Lettország|
| LY | Líbia |
| MA | Marokkó|
| MD | Moldovai Köztársaság|
| MG | Madagaszkár|
| MK | Észak-Macedónia|
| ML | Mali|
| MM | Mianmar|
| MN | Mongólia|
| MO | Makaó (KKT)|
| MQ | Martinique|
| MR | Mauritánia|
| MT | Málta|
| MV | Maldív-szigetek|
| MW | Malawi|
| MX | Mexikó|
| MY | Malajzia|
| MZ | Mozambik|
| n/a | Namíbia|
| NE | Niger|
| NG | Nigéria|
| NI | Nicaragua|
| NL | Hollandia|
| NO | Norvégia|
| NP | Nepál|
| NR | Nauru|
| NZ | Új-Zéland|
| OM | Omán|
| PA | Panama|
| PE | Peru|
| PH | Fülöp-szigetek|
| PK | Pakisztán|
| PL | Lengyelország|
| PR | Puerto Rico|
| PT | Portugália|
| PW | Palau|
| PY | Paraguay|
| QA | Katar|
| RE | Réunion|
| RO | Románia|
| RS | Szerbia|
| RU | Orosz Föderáció|
| RW | Ruanda|
| SA | Szaúd-Arábia|
| SD | Szudán|
| SE | Svédország|
| SG | Szingapúr|
| SI | Szlovénia|
| SK | Szlovákia|
| SN | Szenegál|
| SO | Szomália|
| SR | Suriname|
| SS | Dél-Szudán|
| SV | Salvador|
| SY | Szíriai Arab Köztársaság|
| SZ | Szváziföld|
| TC | Turks- és Caicos-szigetek|
| TG | Togo|
| TH | Thaiföld|
| TN | Tunézia|
| TR | Törökország|
| TT | Trinidad és Tobago|
| TW | Tajvan|
| TZ | Tanzániai Egyesült Köztársaság|
| UA | Ukrajna|
| UG | Uganda|
| Egyesült Államok | Egyesült Államok|
| UY | Uruguay|
| UZ | Üzbegisztán|
| VC | Saint Vincent és Grenadine-szigetek|
| VE | Venezuela|
| VG | Brit Virgin-szigetek|
| VI | Amerikai Virgin-szigetek|
| VN | Vietnam|
| ZA | Dél-Afrika|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Következő lépések

Az egyéni szabályok megismerése után [hozza létre saját egyéni szabályait](create-custom-waf-rules.md).
