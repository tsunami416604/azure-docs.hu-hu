---
title: Az Azure Application Gateway webalkalmazási tűzfal CRS szabály csoportok és szabályok
description: Ezen a lapon információt nyújt webes alkalmazás tűzfal CRS csoportok és a szabályok.
documentationcenter: na
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 4/16/2018
ms.author: victorh
ms.openlocfilehash: 5ba291eaa93a48e3aadc11b1f0f7b48b01683b07
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Kínált webes alkalmazás tűzfal CRS csoportok és a szabályok listája

Alkalmazás átjáró webalkalmazási tűzfal (waf-ot) védje a webalkalmazások a normál biztonsági rés és biztonsági réseket. Ez a OWASP core szabálykészletek program 2.2.9-es vagy 3.0 alapján definiált szabályok segítségével történik. Ezek a szabályok által szabály alapján lehet letiltani. Ez a cikk tartalmazza az aktuális szabályok és a kínált szabálykészletek.

Az alábbi táblázatok a következők: a csoportok és a szabályok, amelyek webalkalmazási tűzfal Application Gateway használata esetén érhetők el.  Minden tábla jelenti. a szabályok egy adott CRS verziójához szabály csoportban található.

## <a name="owasp30"></a> OWASP_3.0


### <a name="crs911"></a> <p x-ms-format-detection="none">KÉRELEM 911-METÓDUS-VÉGREHAJTÁST</p>

|ruleId|Leírás|
|---|---|
|911011|A szabály 911011|
|911012|A szabály 911012|
|911100|Házirend által a metódus nem engedélyezett.|
|911013|A szabály 911013|
|911014|A szabály 911014|
|911015|A szabály 911015|
|911016|A szabály 911016|
|911017|A szabály 911017|
|911018|A szabály 911018|


### <a name="crs913"></a> <p x-ms-format-detection="none">KÉRELEM 913-OLVASÓ-ÉSZLELÉS</p>

|ruleId|Leírás|
|---|---|
|913011|A szabály 913011|
|913012|A szabály 913012|
|913100|Felhasználói ügynök társított biztonsági ellenőrzőeszközt található|
|913110|Biztonsági ellenőrzőeszközt társított fejléc található.|
|913120|Kérelem fájlnév/argumentum társított biztonsági ellenőrzőeszközt található|
|913013|A szabály 913013|
|913014|A szabály 913014|
|913101|Felhasználói ügynök társított scripting/általános HTTP-ügyfél található|
|913102|Felhasználói ügynök webes webbejáró/botot társított található|
|913015|A szabály 913015|
|913016|A szabály 913016|
|913017|A szabály 913017|
|913018|A szabály 913018|

### <a name="crs920"></a> <p x-ms-format-detection="none">KÉRELEM 920-PROTOKOLL-VÉGREHAJTÁST</p>

|ruleId|Leírás|
|---|---|
|920011|A szabály 920011|
|920012|A szabály 920012|
|920100|Érvénytelen HTTP-kérelem-sor|
|920130|Nem sikerült elemezni a kérés törzsében.|
|920140|Több részből álló kérelem törzse szigorú ellenőrzése sikertelen PE %@{REQBODY_PROCESSOR_ERROR =} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} NF %@{MULTIPART_HEADER_FOLDING} LF % @ {MULTIPART_LF_LINE}     FÁJL %@{MULTIPART_FILE_LIMIT_EXCEEDED IH %@{MULTIPART_INVALID_HEADER_FOLDING IQ %@{MULTIPART_INVALID_QUOTING SM %@{MULTIPART_SEMICOLON_MISSING}}}}|
|920160|Content-Length HTTP-fejléc nincs numerikus.|
|920170|GET vagy HEAD kérelem törzse tartalmú.|
|920180|Hiányzik a Content-Length fejlécet POST-kérelmet.|
|920190|Tartomány = utolsó bájtig eltelt értéke érvénytelen.|
|920210|Több/ütköző kapcsolat fejléc adatok találhatók.|
|920220|URL-cím kódolás visszaélés támadási kísérletek|
|920240|URL-cím kódolás visszaélés támadási kísérletek|
|920250|UTF8 Kódolást visszaélés támadási kísérletek|
|920260|Unicode teljes vagy fél visszaélés támadási kísérletek|
|920270|Érvénytelen karakter szerepel a kérelem (üres karakter)|
|920280|Hiányzik egy állomásfejlécet kérelem|
|920290|Üres állomásfejléc|
|920310|Kérelem rendelkezik egy üres fogadja el a fejléc|
|920311|Kérelem rendelkezik egy üres fogadja el a fejléc|
|920330|Üres felhasználói ügynök fejléc|
|920340|Tartalom tartalmazó, de hiányzik a Content-Type fejléc kérése|
|920350|Állomásfejléc az egy numerikus IP-cím|
|920380|A kérelem túl sok argumentum|
|920360|Argumentum neve túl hosszú.|
|920370|Az argumentum értéke túl hosszú|
|920390|Teljes argumentumok túllépve|
|920400|Feltöltött fájl mérete túl nagy|
|920410|Teljes feltöltött fájlok mérete túl nagy|
|920420|Házirend által nem engedélyezett a kérelem tartalomtípus|
|920430|Házirend által nem engedélyezett a HTTP protokoll verziója|
|920440|URL-cím fájlkiterjesztés korlátozza házirend|
|920450|HTTP-fejléc korlátozza házirend (%@{MATCHED_VAR})|
|920013|A szabály 920013|
|920014|A szabály 920014|
|920200|Tartomány = túl sok mezőből áll (6 vagy több)|
|920201|Tartomány = túl sok mezőből áll, pdf-kérelem (35 vagy több)|
|920230|Több URL-kódolást észlelt|
|920300|Kérelem hiányzik egy fejléc elfogadása|
|920271|Érvénytelen karakter szerepel a kérelem (nem nyomtatható karaktereket)|
|920320|Felhasználói ügynök hiányzik egy fejléc|
|920015|A szabály 920015|
|920016|A szabály 920016|
|920272|Érvénytelen karakter szerepel a kérelem (kívül nyomtatható karakter ascii 127 alatt)|
|920017|A szabály 920017|
|920018|A szabály 920018|
|920202|Tartomány = túl sok mezőből áll, pdf-kérelem (6 vagy több)|
|920273|Érvénytelen karakter szerepel a kérelem (kívül nagyon szigorú)|
|920274|Érvénytelen karaktert tartalmaz (kívül nagyon szigorú) kérelemfejléc|
|920460|A szabály 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">A KÉRELEM-921-PROTOKOLL-TÁMADÁS</p>

|ruleId|Leírás|
|---|---|
|921011|A szabály 921011|
|921012|A szabály 921012|
|921100|HTTP-kérelmek támadás.|
|921110|HTTP-kérelem való támadás|
|921120|HTTP-válasz a felosztás támadás|
|921130|HTTP-válasz a felosztás támadás|
|921140|HTTP fejléc injektálási támadás fejlécek keresztül|
|921150|HTTP fejléc injektálási támadás keresztül forgalma (Kocsivissza/Soremelés észlelt)|
|921160|HTTP fejléc injektálási támadás keresztül forgalma (Kocsivissza/Soremelés és fejlécnév észlelt)|
|921013|A szabály 921013|
|921014|A szabály 921014|
|921151|HTTP fejléc injektálási támadás keresztül forgalma (Kocsivissza/Soremelés észlelt)|
|921015|A szabály 921015|
|921016|A szabály 921016|
|921170|A szabály 921170|
|921180|HTTP-paraméter szennyezés (% @{TX.1})|
|921017|A szabály 921017|
|921018|A szabály 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">KÉRELEM-930-ALKALMAZÁS-TÁMADÁS-LFI</p>

|ruleId|Leírás|
|---|---|
|930011|A szabály 930011|
|930012|A szabály 930012|
|930100|Elérési út könyvtármegkerülési támadás (/... /)|
|930110|Elérési út könyvtármegkerülési támadás (/... /)|
|930120|Az operációs rendszer fájlhoz való hozzáférési kísérlet|
|930130|A korlátozott fájl hozzáférési kísérlet|
|930013|A szabály 930013|
|930014|A szabály 930014|
|930015|A szabály 930015|
|930016|A szabály 930016|
|930017|A szabály 930017|
|930018|A szabály 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">KÉRELEM-931-ALKALMAZÁS-TÁMADÁS-RFI</p>

|ruleId|Leírás|
|---|---|
|931011|A szabály 931011|
|931012|A szabály 931012|
|931100|Távoli fájl befoglalási (RFI) támadási = URL paraméter IP-cím használatával|
|931110|Távoli fájl befoglalási (RFI) támadási = közös RFI sebezhető paraméternév használt w/URL forgalma|
|931120|Lehetséges távoli fájl befoglalási (RFI) támadás = URL-címet használt hasznos w/záró kérdés megjelölés karakter (?)|
|931013|A szabály 931013|
|931014|A szabály 931014|
|931130|Lehetséges távoli fájl befoglalási (RFI) támadás = tartomány hivatkozás|
|931015|A szabály 931015|
|931016|A szabály 931016|
|931017|A szabály 931017|
|931018|A szabály 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">KÉRELEM-932-ALKALMAZÁS-TÁMADÁS-RCE</p>

|ruleId|Leírás|
|---|---|
|932011|A szabály 932011|
|932012|A szabály 932012|
|932120|Távoli parancsvégrehajtás található Windows PowerShell-paranccsal =|
|932130|Távoli parancsvégrehajtás Unix rendszerhéj-kifejezés található =|
|932140|Távoli parancsvégrehajtás Windows = / Ha parancs|
|932160|Távoli parancsvégrehajtás található Unix rendszerhéj kód =|
|932170|Távoli parancsvégrehajtás = Shellshock (CVE-2014-6271)|
|932171|Távoli parancsvégrehajtás = Shellshock (CVE-2014-6271)|
|932013|A szabály 932013|
|932014|A szabály 932014|
|932015|A szabály 932015|
|932016|A szabály 932016|
|932017|A szabály 932017|
|932018|A szabály 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">A KÉRELEM-933-ALKALMAZÁS-TÁMADÁS-PHP</p>

|ruleId|Leírás|
|---|---|
|933011|A szabály 933011|
|933012|A szabály 933012|
|933100|A PHP injektálási támadás nyitó/záró címke található =|
|933110|A PHP injektálási támadás = található PHP parancsfájl-fájl feltöltése|
|933120|A PHP injektálási támadás található konfigurációs irányelv =|
|933130|A PHP injektálási támadás = található változók|
|933150|A PHP injektálási támadás található nagy kockázatú PHP függvény neve =|
|933160|A PHP injektálási támadás megtalálható Függvényhíváshoz magas kockázatú PHP =|
|933180|A PHP injektálási támadás változó megtalálható Függvényhíváshoz hasonló =|
|933013|A szabály 933013|
|933014|A szabály 933014|
|933151|A PHP injektálási támadás található közepes kockázatú PHP függvény neve =|
|933015|A szabály 933015|
|933016|A szabály 933016|
|933131|A PHP injektálási támadás = található változók|
|933161|A PHP injektálási támadás megtalálható Függvényhíváshoz alacsony értékű PHP =|
|933111|A PHP injektálási támadás = található PHP parancsfájl-fájl feltöltése|
|933017|A szabály 933017|
|933018|A szabály 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">KÉRELEM-941-ALKALMAZÁS-TÁMADÁS-LEHETŐVÉ</p>

|ruleId|Leírás|
|---|---|
|941011|A szabály 941011|
|941012|A szabály 941012|
|941100|Lehetővé a támadás libinjection észlelve|
|941110|Lehetővé szűrő - kategória 1 parancsfájl címke vektoros =|
|941130|Lehetővé szűrő - kategória 3 attribútum vektoros =|
|941140|Lehetővé szűrő - kategória 4 Javascript URI vektoros =|
|941150|Lehetővé szűrő - kategória 5 = nem engedélyezett HTML-attribútumok|
|941180|Csomópont-érvényesítő Blacklist kulcsszavak|
|941190|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941200|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941210|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941220|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941230|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941240|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941260|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941270|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941280|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941290|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941300|Internet Explorer lehetővé szűrők - támadás észlelése.|
|941310|US-ASCII hibás kódolási lehetővé szűrő - támadás észlelése.|
|941350|Az UTF-7 kódolási IE lehetővé - támadás észlelése.|
|941013|A szabály 941013|
|941014|A szabály 941014|
|941320|Lehetséges lehetővé támadás észlelése - HTML-címke kezelő|
|941015|A szabály 941015|
|941016|A szabály 941016|
|941017|A szabály 941017|
|941018|A szabály 941018|

### <a name="crs942"></a> <p x-ms-format-detection="none">KÉRELEM-942-ALKALMAZÁS-TÁMADÁS-SQLI</p>

|ruleId|Leírás|
|---|---|
|942011|A szabály 942011|
|942012|A szabály 942012|
|942100|SQL injektálási támadás észlelve libinjection|
|942140|SQL injektálási támadás észlelhető közös DB neve =|
|942160|Észleli a sleep() vagy benchmark() vak sqli teszteket.|
|942170|Észleli a SQL teljesítményteszt és alvó injektálási kísérletek többek között a feltételes lekérdezések|
|942230|Feltételes SQL injektálási kísérletek begyűjtésére észleli|
|942270|Alapvető sql-injektálás keres. Gyakori támadási karakterlánc mysql oracle és mások számára.|
|942290|Alapszintű MongoDB SQL injektálási kísérletek begyűjtésére megkeresése|
|942320|Észleli a MySQL és PostgreSQL tárolt eljárás vagy függvény alkalommal|
|942350|Észleli a MySQL UDF injektálási és egyéb adatszerkezet/adatkezelési kísérletek|
|942013|A szabály 942013|
|942014|A szabály 942014|
|942150|SQL injektálási támadás|
|942410|SQL injektálási támadás|
|942440|SQL megjegyzés feladatütemezési észlelte.|
|942450|SQL hexadecimális kódolás azonosított|
|942015|A szabály 942015|
|942016|A szabály 942016|
|942251|Észleli a HAVING alkalommal|
|942460|Meta karakter Anomáliadetektálási észleléséről szóló figyelmeztetés - karaktereket. a Word ismétlődő.|
|942017|A szabály 942017|
|942018|A szabály 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|ruleId|Leírás|
|---|---|
|943011|A szabály 943011|
|943012|A szabály 943012|
|943100|Munkamenet rögzítés támadás = HTML cookie-k értékeinek beállítása|
|943110|Munkamenet rögzítés támadás a tartományhoz csatlakozó hivatkozó SessionID paraméter neve =|
|943120|Munkamenet rögzítés támadás SessionID paraméternév nem hivatkozó =|
|943013|A szabály 943013|
|943014|A szabály 943014|
|943015|A szabály 943015|
|943016|A szabály 943016|
|943017|A szabály 943017|
|943018|A szabály 943018|

##<a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|ruleId|Leírás|
|---|---|
|960911|Érvénytelen HTTP-kérelem-sor|
|981227|Apache hiba = kérelemben érvénytelen URI.|
|960912|Nem sikerült elemezni a kérés törzsében.|
|960914|Több részből álló kérelem törzse szigorú ellenőrzése sikertelen PE %@{REQBODY_PROCESSOR_ERROR =} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} NF %@{MULTIPART_HEADER_FOLDING} LF % @ {MULTIPART_LF_LINE}     FÁJL %@{MULTIPART_FILE_LIMIT_EXCEEDED IH %@{MULTIPART_INVALID_HEADER_FOLDING IQ %@{MULTIPART_INVALID_QUOTING SM %@{MULTIPART_SEMICOLON_MISSING}}}}|
|960915|Több részből álló elemző észlelte a lehetséges páratlan határ.|
|960016|Content-Length HTTP-fejléc nincs numerikus.|
|960011|GET vagy HEAD kérelem törzse tartalmú.|
|960012|Hiányzik a Content-Length fejlécet POST-kérelmet.|
|960902|Érvénytelen identitás kódolás használatát.|
|960022|Nem engedélyezett a HTTP 1.0 fejléc várt.|
|960020|Pragma fejlécet a HTTP/1.1 kéréseket a Cache-Control fejlécet igényel.|
|958291|Tartomány = mező létezik, és 0-val kezdődik.|
|958230|Tartomány = utolsó bájtig eltelt értéke érvénytelen.|
|958295|Több/ütköző kapcsolat fejléc adatok találhatók.|
|950107|URL-cím kódolás visszaélés támadási kísérletek|
|950109|Több URL-kódolást észlelt|
|950108|URL-cím kódolás visszaélés támadási kísérletek|
|950801|UTF8 Kódolást visszaélés támadási kísérletek|
|950116|Unicode teljes vagy fél visszaélés támadási kísérletek|
|960901|Kérelemben érvénytelen karakter|
|960018|Kérelemben érvénytelen karakter|

### <a name="crs21"></a> crs_21_protocol_anomalies

|ruleId|Leírás|
|---|---|
|960008|Hiányzik egy állomásfejlécet kérelem|
|960007|Üres állomásfejléc|
|960015|Kérelem hiányzik egy fejléc elfogadása|
|960021|Kérelem rendelkezik egy üres fogadja el a fejléc|
|960009|Felhasználói ügynök fejléc hiányzik a kérelem|
|960006|Üres felhasználói ügynök fejléc|
|960904|Tartalom tartalmazó, de hiányzik a Content-Type fejléc kérése|
|960017|Állomásfejléc az egy numerikus IP-cím|

### <a name="crs23"></a> crs_23_request_limits

|ruleId|Leírás|
|---|---|
|960209|Argumentum neve túl hosszú.|
|960208|Az argumentum értéke túl hosszú|
|960335|A kérelem túl sok argumentum|
|960341|Teljes argumentumok túllépve|
|960342|Feltöltött fájl mérete túl nagy|
|960343|Teljes feltöltött fájlok mérete túl nagy|

### <a name="crs30"></a> crs_30_http_policy

|ruleId|Leírás|
|---|---|
|960032|Házirend által a metódus nem engedélyezett.|
|960010|Házirend által nem engedélyezett a kérelem tartalomtípus|
|960034|Házirend által nem engedélyezett a HTTP protokoll verziója|
|960035|URL-cím fájlkiterjesztés korlátozza házirend|
|960038|HTTP-fejléc korlátozza házirend|

### <a name="crs35"></a> crs_35_bad_robots

|ruleId|Leírás|
|---|---|
|990002|Kérelem azt jelzi, hogy egy biztonsági beolvasott a hely|
|990901|Kérelem azt jelzi, hogy egy biztonsági beolvasott a hely|
|990902|Kérelem azt jelzi, hogy egy biztonsági beolvasott a hely|
|990012|Az engedélyezetlen webhely webbejáró|

### <a name="crs40"></a> crs_40_generic_attacks

|ruleId|Leírás|
|---|---|
|960024|Meta karakter Anomáliadetektálási észleléséről szóló figyelmeztetés - karaktereket. a Word ismétlődő.|
|950008|Fájlbeszúrás nem dokumentált ColdFusion címkék|
|950010|LDAP injektálási támadás|
|950011|Ez az SSI injektálási támadás|
|950018|Univerzális PDF lehetővé URL-cím észlelhető.|
|950019|E-mailek injektálási támadás|
|950012|HTTP-kérelmek támadás.|
|950910|HTTP-válasz a felosztás támadás|
|950911|HTTP-válasz a felosztás támadás|
|950117|Távoli fájl befoglalási támadás|
|950118|Távoli fájl befoglalási támadás|
|950119|Távoli fájl befoglalási támadás|
|950120|Lehetséges távoli fájl befoglalási (RFI) támadás = tartomány hivatkozás|
|981133|A szabály 981133|
|981134|A szabály 981134|
|950009|Munkamenet rögzítés támadás|
|950003|Munkamenet-rögzítést|
|950000|Munkamenet-rögzítést|
|950005|Távoli fájl hozzáférési kísérlet|
|950002|Rendszer parancs hozzáférés|
|950006|Rendszer parancs injektálási|
|959151|A PHP injektálási támadás|
|958976|A PHP injektálási támadás|
|958977|A PHP injektálási támadás|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|ruleId|Leírás|
|---|---|
|981231|SQL megjegyzés feladatütemezési észlelte.|
|981260|SQL hexadecimális kódolás azonosított|
|981320|SQL injektálási támadás észlelhető közös DB neve =|
|981300|A szabály 981300|
|981301|A szabály 981301|
|981302|A szabály 981302|
|981303|A szabály 981303|
|981304|A szabály 981304|
|981305|A szabály 981305|
|981306|A szabály 981306|
|981307|A szabály 981307|
|981308|A szabály 981308|
|981309|A szabály 981309|
|981310|A szabály 981310|
|981311|A szabály 981311|
|981312|A szabály 981312|
|981313|A szabály 981313|
|981314|A szabály 981314|
|981315|A szabály 981315|
|981316|A szabály 981316|
|981317|SQL SELECT utasítás Anomáliadetektálási észleléséről szóló figyelmeztetés|
|950007|Vak SQL injektálási támadás|
|950001|SQL injektálási támadás|
|950908|SQL injektálási támadás.|
|959073|SQL injektálási támadás|
|981272|Észleli a sleep() vagy benchmark() vak sqli teszteket.|
|981250|Észleli a SQL teljesítményteszt és alvó injektálási kísérletek többek között a feltételes lekérdezések|
|981241|Feltételes SQL injektálási kísérletek begyűjtésére észleli|
|981276|Alapvető sql-injektálás keres. Gyakori támadási karakterlánc mysql oracle és mások számára.|
|981270|Alapszintű MongoDB SQL injektálási kísérletek begyűjtésére megkeresése|
|981253|Észleli a MySQL és PostgreSQL tárolt eljárás vagy függvény alkalommal|
|981251|Észleli a MySQL UDF injektálási és egyéb adatszerkezet/adatkezelési kísérletek|

### <a name="crs41xss"></a> crs_41_xss_attacks

|ruleId|Leírás|
|---|---|
|973336|Lehetővé szűrő - kategória 1 parancsfájl címke vektoros =|
|973338|Lehetővé szűrő - kategória 3 Javascript URI vektoros =|
|981136|A szabály 981136|
|981018|A szabály 981018|
|958016|Többhelyes Scripting (lehetővé) támadás|
|958414|Többhelyes Scripting (lehetővé) támadás|
|958032|Többhelyes Scripting (lehetővé) támadás|
|958026|Többhelyes Scripting (lehetővé) támadás|
|958027|Többhelyes Scripting (lehetővé) támadás|
|958054|Többhelyes Scripting (lehetővé) támadás|
|958418|Többhelyes Scripting (lehetővé) támadás|
|958034|Többhelyes Scripting (lehetővé) támadás|
|958019|Többhelyes Scripting (lehetővé) támadás|
|958013|Többhelyes Scripting (lehetővé) támadás|
|958408|Többhelyes Scripting (lehetővé) támadás|
|958012|Többhelyes Scripting (lehetővé) támadás|
|958423|Többhelyes Scripting (lehetővé) támadás|
|958002|Többhelyes Scripting (lehetővé) támadás|
|958017|Többhelyes Scripting (lehetővé) támadás|
|958007|Többhelyes Scripting (lehetővé) támadás|
|958047|Többhelyes Scripting (lehetővé) támadás|
|958410|Többhelyes Scripting (lehetővé) támadás|
|958415|Többhelyes Scripting (lehetővé) támadás|
|958022|Többhelyes Scripting (lehetővé) támadás|
|958405|Többhelyes Scripting (lehetővé) támadás|
|958419|Többhelyes Scripting (lehetővé) támadás|
|958028|Többhelyes Scripting (lehetővé) támadás|
|958057|Többhelyes Scripting (lehetővé) támadás|
|958031|Többhelyes Scripting (lehetővé) támadás|
|958006|Többhelyes Scripting (lehetővé) támadás|
|958033|Többhelyes Scripting (lehetővé) támadás|
|958038|Többhelyes Scripting (lehetővé) támadás|
|958409|Többhelyes Scripting (lehetővé) támadás|
|958001|Többhelyes Scripting (lehetővé) támadás|
|958005|Többhelyes Scripting (lehetővé) támadás|
|958404|Többhelyes Scripting (lehetővé) támadás|
|958023|Többhelyes Scripting (lehetővé) támadás|
|958010|Többhelyes Scripting (lehetővé) támadás|
|958411|Többhelyes Scripting (lehetővé) támadás|
|958422|Többhelyes Scripting (lehetővé) támadás|
|958036|Többhelyes Scripting (lehetővé) támadás|
|958000|Többhelyes Scripting (lehetővé) támadás|
|958018|Többhelyes Scripting (lehetővé) támadás|
|958406|Többhelyes Scripting (lehetővé) támadás|
|958040|Többhelyes Scripting (lehetővé) támadás|
|958052|Többhelyes Scripting (lehetővé) támadás|
|958037|Többhelyes Scripting (lehetővé) támadás|
|958049|Többhelyes Scripting (lehetővé) támadás|
|958030|Többhelyes Scripting (lehetővé) támadás|
|958041|Többhelyes Scripting (lehetővé) támadás|
|958416|Többhelyes Scripting (lehetővé) támadás|
|958024|Többhelyes Scripting (lehetővé) támadás|
|958059|Többhelyes Scripting (lehetővé) támadás|
|958417|Többhelyes Scripting (lehetővé) támadás|
|958020|Többhelyes Scripting (lehetővé) támadás|
|958045|Többhelyes Scripting (lehetővé) támadás|
|958004|Többhelyes Scripting (lehetővé) támadás|
|958421|Többhelyes Scripting (lehetővé) támadás|
|958009|Többhelyes Scripting (lehetővé) támadás|
|958025|Többhelyes Scripting (lehetővé) támadás|
|958413|Többhelyes Scripting (lehetővé) támadás|
|958051|Többhelyes Scripting (lehetővé) támadás|
|958420|Többhelyes Scripting (lehetővé) támadás|
|958407|Többhelyes Scripting (lehetővé) támadás|
|958056|Többhelyes Scripting (lehetővé) támadás|
|958011|Többhelyes Scripting (lehetővé) támadás|
|958412|Többhelyes Scripting (lehetővé) támadás|
|958008|Többhelyes Scripting (lehetővé) támadás|
|958046|Többhelyes Scripting (lehetővé) támadás|
|958039|Többhelyes Scripting (lehetővé) támadás|
|958003|Többhelyes Scripting (lehetővé) támadás|
|973300|Lehetséges lehetővé támadás észlelése - HTML-címke kezelő|
|973301|Lehetővé támadás észlelése|
|973302|Lehetővé támadás észlelése|
|973303|Lehetővé támadás észlelése|
|973304|Lehetővé támadás észlelése|
|973305|Lehetővé támadás észlelése|
|973306|Lehetővé támadás észlelése|
|973307|Lehetővé támadás észlelése|
|973308|Lehetővé támadás észlelése|
|973309|Lehetővé támadás észlelése|
|973311|Lehetővé támadás észlelése|
|973313|Lehetővé támadás észlelése|
|973314|Lehetővé támadás észlelése|
|973331|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973315|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973330|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973327|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973326|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973346|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973345|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973324|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973323|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973348|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973321|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973320|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973318|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973317|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973329|Internet Explorer lehetővé szűrők - támadás észlelése.|
|973328|Internet Explorer lehetővé szűrők - támadás észlelése.|

### <a name="crs42"></a> crs_42_tight_security

|ruleId|Leírás|
|---|---|
|950103|Elérési út könyvtármegkerülési támadás|

### <a name="crs45"></a> crs_45_trojans

|ruleId|Leírás|
|---|---|
|950110|Hátsó kapun keresztüli hozzáférést|
|950921|Hátsó kapun keresztüli hozzáférést|
|950922|Hátsó kapun keresztüli hozzáférést|

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan tilthatja ellátogatva WAF: [testreszabása WAF szabályok](application-gateway-customize-waf-rules-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png