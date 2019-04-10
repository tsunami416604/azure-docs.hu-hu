---
title: Az Azure Application Gateway webalkalmazási tűzfal CRS-szabály, csoportok és szabályok
description: Ez az oldal nyújt tájékoztatást web application firewall CRS-szabálycsoportjainak és szabályokat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 61ab41eed7703c82c2e5ef2a3b5412a9f56389ba
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279703"
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Webes alkalmazás tűzfal CRS-szabálycsoportjainak és szabályok listája érhető el

Application Gateway webalkalmazási tűzfal (WAF) megvédi a webalkalmazásokat a gyakori biztonsági rések és az azokat kihasználó támadások ellen. Ez a alapvető OWASP-szabálykészletek 2.2.9-es vagy 3.0 alapján meghatározott szabályok segítségével történik. Ezek a szabályok egy szabály által szabály alapján lehet letiltani. Ez a cikk tartalmazza az aktuális szabályok és a szabálykészletek érhető el.

Az alábbi táblázatok a következők: a csoportok és a szabályokat, amelyek webalkalmazási tűzfallal rendelkező Application Gateway használata esetén érhetők el.  Mindegyik táblázatban található a meghatározott CRS verziójához szabálycsoport szabályok jelöli.

## <a name="owasp30"></a> OWASP_3.0

### <a name="General"></a> <p x-ms-format-detection="none">Általános kérdések</p>

|RuleId|Leírás|
|---|---|
|200004|Lehetséges többrészes páratlan határt.|

### <a name="crs911"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Leírás|
|---|---|
|911100|Házirend által nem engedélyezett a metódus|


### <a name="crs913"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Leírás|
|---|---|
|913100|Biztonsági ellenőrzőeszköz társított felhasználói ügynök található.|
|913110|Kérelem fejléce társított biztonsági ellenőrzőeszköz található|
|913120|Kérelem fájlnév/argumentumnak társított biztonsági ellenőrzőeszköz található|
|913101|Parancsfájl-kezelési/általános HTTP-alapú társított felhasználói ügynök található.|
|913102|Webes webbejáró vagy robot társított felhasználói ügynök található.|

### <a name="crs920"></a> <p x-ms-format-detection="none">KÉRELEM-920-PROTOKOLL-VÉGREHAJTÁS</p>

|RuleId|Leírás|
|---|---|
|920100|Érvénytelen HTTP-kérelem-sor|
|920130|Nem sikerült elemezni a kérelem törzsében.|
|920140|Többrészes kérelem törzse nem sikerült a szigorú ellenőrzési PE %@{REQBODY_PROCESSOR_ERROR =} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} NF %@{MULTIPART_HEADER_FOLDING} LF a(z) % @ {MULTIPART_LF_LINE}     FÁJL %@{MULTIPART_FILE_LIMIT_EXCEEDED IH %@{MULTIPART_INVALID_HEADER_FOLDING IQ %@{MULTIPART_INVALID_QUOTING SM %@{MULTIPART_SEMICOLON_MISSING}}}}|
|920160|Content-Length HTTP-fejléc nem numerikus.|
|920170|GET vagy HEAD-kérést szövegtörzse.|
|920180|POST-kérés Content-Length fejléc hiányzik.|
|920190|Tartomány = utolsó bájtig eltelt értéke érvénytelen.|
|920210|Több/ütköző kapcsolat fejléc adatok találhatók.|
|920220|URL-cím kódolás visszaélés támadási kísérlet|
|920240|URL-cím kódolás visszaélés támadási kísérlet|
|920250|UTF8 Kódolást visszaélés támadási kísérlet|
|920260|Unicode teljes vagy fél visszaélés támadási kísérlet|
|920270|Érvénytelen karakter a (NULL értékű karakterrel) kérelem|
|920280|Hiányzik az állomásfejléc kérelem|
|920290|Üres állomásnév|
|920310|Kérelem tartalmaz egy üres fogadja el a fejléc|
|920311|Kérelem tartalmaz egy üres fogadja el a fejléc|
|920330|Üres felhasználói ügynök fejléc|
|920340|Tartalom tartalmazó, de hiányzik a Content-Type fejléc kérése|
|920350|Állomásfejléc az egy numerikus IP-cím|
|920380|Kérelemben túl sok argumentum|
|920360|Argument neve túl hosszú.|
|920370|Az argumentum értéke túl hosszú|
|920390|Teljes argumentumok mérete meghaladta az engedélyezett|
|920400|A feltöltött fájl mérete túl nagy|
|920410|Feltöltött fájlok összesített mérete túl nagy|
|920420|Házirend által nem engedélyezett a kérelem tartalom típusa|
|920430|Házirend által nem engedélyezett a HTTP protokoll verziója|
|920440|A házirend korlátozza URL-cím fájlkiterjesztés|
|920450|HTTP-fejléc korlátozza a házirend (%@{MATCHED_VAR})|
|920200|Tartomány = túl sok mező (6 vagy több)|
|920201|Tartomány = túl sok mező pdf-kérelem (35 vagy több)|
|920230|Több URL-Címének kódolása észlelt|
|920300|Kérelem hiányzik egy fejléc elfogadása|
|920271|Érvénytelen karakter a kérelem (nem nyomtatható karakter)|
|920320|Felhasználói ügynök Záhlaví|
|920272|Érvénytelen karakter a kérelem (kívül az alábbi ascii 127 nyomtatható karakter)|
|920202|Tartomány = túl sok mező pdf-kérelem (6 vagy több)|
|920273|Érvénytelen karakter a kérelem (kívül nagyon szigorú set)|
|920274|Érvénytelen karakter a kérelem fejlécében (kívül nagyon szigorú set)|
|920460|Rendellenes escape-karakter|

### <a name="crs921"></a> <p x-ms-format-detection="none">A KÉRELEM-921-PROTOKOLL-TÁMADÁS</p>

|RuleId|Leírás|
|---|---|
|921100|HTTP-Kéréscsempészet támadás.|
|921110|HTTP-kérelem való támadás|
|921120|HTTP-válasz a felosztás támadás|
|921130|HTTP-válasz a felosztás támadás|
|921140|Fejlécek via HTTP fejléc injektálásos támadásokkal szemben|
|921150|Hasznos adat (CR vagy LF észlelt) keresztül HTTP fejléc injektálásos támadásokkal szemben|
|921160|Hasznos adat (CR vagy LF Karakterrel és fejléc-nevet észlelt) keresztül HTTP fejléc injektálásos támadásokkal szemben|
|921151|Hasznos adat (CR vagy LF észlelt) keresztül HTTP fejléc injektálásos támadásokkal szemben|
|921170|HTTP-paraméter szennyezés|
|921180|HTTP-paraméter szennyezés (% @{TX.1})|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Leírás|
|---|---|
|930100|A bejárási támadási elérési útja (/... /)|
|930110|A bejárási támadási elérési útja (/... /)|
|930120|Az operációs rendszer fájl hozzáférési kísérlet|
|930130|A korlátozott fájl hozzáférési kísérlet|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Leírás|
|---|---|
|931100|Lehetséges fájl beszúrásos (RFI) támadások = IP-címet használó URL-paraméter|
|931110|Lehetséges fájl beszúrásos (RFI) támadások = RFI sebezhető paraméter használt név w/URL adattartalom|
|931120|Lehetséges távoli fájl beszúrásos (RFI) támadások = URL-cím használt hasznos w/záró kérdés Mark karakter (?)|
|931130|Lehetséges távoli fájl beszúrásos (RFI) támadások =-Domain hivatkozás|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Leírás|
|---|---|
|932120|Távoli parancs végrehajtása található Windows PowerShell-paranccsal =|
|932130|Távoli parancs végrehajtása Unix rendszerhéj-kifejezés található =|
|932140|Távoli parancs végrehajtása Windows = / Ha parancs található|
|932160|Távoli parancs végrehajtása található Unix rendszerhéj kód =|
|932170|Távoli parancs végrehajtása Shellshock (CVE – 2014-6271) =|
|932171|Távoli parancs végrehajtása Shellshock (CVE – 2014-6271) =|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Leírás|
|---|---|
|933100|PHP-injektálási támadások Znak Levé nebo Pravé címke található =|
|933110|PHP-injektálási támadások = található PHP parancsfájl-fájl feltöltése|
|933120|PHP Injection Attack = Configuration Directive Found|
|933130|PHP-injektálási támadások = található változók|
|933150|PHP Injection Attack = High-Risk PHP Function Name Found|
|933160|PHP-injektálási támadások magas kockázatú PHP függvény hívásához szükséges található =|
|933180|PHP Injection Attack = Variable Function Call Found|
|933151|PHP Injection Attack = Medium-Risk PHP Function Name Found|
|933131|PHP-injektálási támadások = található változók|
|933161|PHP-injektálási támadások kevés PHP függvény hívásához szükséges található =|
|933111|PHP-injektálási támadások = található PHP parancsfájl-fájl feltöltése|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Leírás|
|---|---|
|941100|XSS támadás észlelve libinjection|
|941110|XSS szűrő - kategória 1 = parancsfájl címke vektor|
|941130|XSS szűrő - kategória 3 = attribútuma vektor|
|941140|XSS szűrő - kategória 4 = Javascript URI vektor|
|941150|XSS szűrő - kategória 5 = nem engedélyezett HTML-attribútumok|
|941180|Csomópont-érvényesítő Blacklist kulcsszavak|
|941190|XSS stíluslapok használatával|
|941200|XSS VML keretek használata|
|941210|XSS rejtjelezett Javascript használatával|
|941220|XSS rejtjelezett Visual Basic-parancsfájl használatával|
|941230|XSS használatával "" címke beágyazása|
|941240|XSS "import" vagy "végrehajtása" attribútum használatával|
|941260|XSS "meta" címke használatával|
|941270|XSS "hivatkozás" href használatával|
|941280|XSS "base" címke használata|
|941290|XSS "alkalmazás" címke használata|
|941300|XSS "objektum" címke használata|
|941310|US-ASCII hibás kódolás XSS szűrő - támadás észlelve.|
|941330|Internet Explorer XSS szűrők - támadás észlelve.|
|941340|Internet Explorer XSS szűrők - támadás észlelve.|
|941350|UTF-7 kódolási IE XSS - támadás észlelve.|
|941320|Lehetséges XSS-támadások észlelt – HTML-címke-kezelő|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Leírás|
|---|---|
|942100|SQL injektálási támadások észlelt libinjection keresztül|
|942110|SQL-injektálásos támadásokkal szemben: Közös olyan injektálás, a tesztelés észlelt|
|942130|SQL-injektálásos támadásokkal szemben: SQL Tautology észlelt.|
|942140|SQL-injektálásos támadásokkal szemben = közös DB nevet észlelt|
|942160|Sleep() vagy benchmark() vak sqli teszteket észlel.|
|942170|Észleli az SQL-injektálás referenciaalap és alvó állapottal kapcsolatos megkísérli többek között a feltételes lekérdezések|
|942190|Észleli az MSSQL-végrehajtás és a információgyűjtés kísérletek|
|942200|Észleli a MySQL Megjegyzés- / terület rejtjelezett injektálások és a használni kívánt szintaxiskiemelést megszüntetése|
|942230|SQL-injektálási kísérleteket feltételes észleli|
|942260|Észleli az SQL-hitelesítés alapszintű Mellőzés megkísérli 2/3|
|942270|Alapszintű sql injektálás keres. Gyakori támadási karakterlánc, mysql oracle és mások.|
|942290|Megkeresi a mongodb-hez alapszintű SQL injektálási kísérletek|
|942300|Észleli a megjegyzéseket, a feltételek és a ch (a) r-injektálások MySQL|
|942320|Észleli a MySQL és a PostgreSQL tárolt eljárás vagy függvény injektálások|
|942330|Észleli a klasszikus SQL injektálási probings 1/2|
|942340|Észleli az SQL-hitelesítés alapszintű Mellőzés megkísérli 3/3|
|942350|Észleli a MySQL UDF injektálással vagy a más adatstruktúra /-manipuláció kísérletek|
|942360|Észleli összefűzött alapszintű SQL injektálás és SQLLFI kísérletek|
|942370|Észleli a klasszikus SQL injektálási probings 2/2|
|942150|SQL-injektálásos támadásokkal szemben|
|942410|SQL-injektálásos támadásokkal szemben|
|942430|Korlátozott SQL karakter rendellenességek észlelése (argumentumok): speciális karakterek száma túllépte a (12)|
|942440|SQL-megjegyzés feladatütemezési észlelt.|
|942450|SQL hexadecimális kódolás azonosított|
|942251|Észleli a HAVING injektálások|
|942460|Meta karakter Anomáliadetektálási észleléséről szóló figyelmeztetés - ismétlődő a Word karakter|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Leírás|
|---|---|
|943100|Munkamenet-rögzítési támadási = HTML formátumban a cookie-k értékeinek beállítása|
|943110|Munkamenet-rögzítési támadási = tartományon kívüli hivatkozó a munkamenet-azonosító paraméter neve|
|943120|Munkamenet-rögzítési támadási = nincs hivatkozó a munkamenet-azonosító paraméter neve|

## <a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|RuleId|Leírás|
|---|---|
|960911|Érvénytelen HTTP-kérelem-sor|
|981227|Az Apache hiba = a kérelem URI érvénytelen.|
|960912|Nem sikerült elemezni a kérelem törzsében.|
|960914|Többrészes kérelem törzse nem sikerült a szigorú ellenőrzési PE %@{REQBODY_PROCESSOR_ERROR =} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} NF %@{MULTIPART_HEADER_FOLDING} LF a(z) % @ {MULTIPART_LF_LINE}     FÁJL %@{MULTIPART_FILE_LIMIT_EXCEEDED IH %@{MULTIPART_INVALID_HEADER_FOLDING IQ %@{MULTIPART_INVALID_QUOTING SM %@{MULTIPART_SEMICOLON_MISSING}}}}|
|960915|Többrészes elemző észlelt egy lehetséges páratlan határt.|
|960016|Content-Length HTTP-fejléc nem numerikus.|
|960011|GET vagy HEAD-kérést szövegtörzse.|
|960012|POST-kérés Content-Length fejléc hiányzik.|
|960902|Identitás-kódolást használva.|
|960022|Nem engedélyezett a HTTP 1.0 fejléc várt.|
|960020|Direktiva pragma fejléc Cache-Control fejléc a HTTP/1.1-es kérelmek kell rendelkeznie.|
|958291|Tartomány = mező létezik, és 0-val kezdődik.|
|958230|Tartomány = utolsó bájtig eltelt értéke érvénytelen.|
|958295|Több/ütköző kapcsolat fejléc adatok találhatók.|
|950107|URL-cím kódolás visszaélés támadási kísérlet|
|950109|Több URL-Címének kódolása észlelt|
|950108|URL-cím kódolás visszaélés támadási kísérlet|
|950801|UTF8 Kódolást visszaélés támadási kísérlet|
|950116|Unicode teljes vagy fél visszaélés támadási kísérlet|
|960901|A kérelem érvénytelen karakter|
|960018|A kérelem érvénytelen karakter|

### <a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Leírás|
|---|---|
|960008|Hiányzik az állomásfejléc kérelem|
|960007|Üres állomásnév|
|960015|Kérelem hiányzik egy fejléc elfogadása|
|960021|Kérelem tartalmaz egy üres fogadja el a fejléc|
|960009|Felhasználói ügynök fejléc hiányzik a kérés|
|960006|Üres felhasználói ügynök fejléc|
|960904|Tartalom tartalmazó, de hiányzik a Content-Type fejléc kérése|
|960017|Állomásfejléc az egy numerikus IP-cím|

### <a name="crs23"></a> crs_23_request_limits

|RuleId|Leírás|
|---|---|
|960209|Argument neve túl hosszú.|
|960208|Az argumentum értéke túl hosszú|
|960335|Kérelemben túl sok argumentum|
|960341|Teljes argumentumok mérete meghaladta az engedélyezett|
|960342|A feltöltött fájl mérete túl nagy|
|960343|Feltöltött fájlok összesített mérete túl nagy|

### <a name="crs30"></a> crs_30_http_policy

|RuleId|Leírás|
|---|---|
|960032|Házirend által nem engedélyezett a metódus|
|960010|Házirend által nem engedélyezett a kérelem tartalom típusa|
|960034|Házirend által nem engedélyezett a HTTP protokoll verziója|
|960035|A házirend korlátozza URL-cím fájlkiterjesztés|
|960038|A házirend korlátozza a HTTP-fejléc|

### <a name="crs35"></a> crs_35_bad_robots

|RuleId|Leírás|
|---|---|
|990002|Kérelem azt jelzi, hogy egy biztonsági ellenőrzőeszköz ellenőrzi a hely|
|990901|Kérelem azt jelzi, hogy egy biztonsági ellenőrzőeszköz ellenőrzi a hely|
|990902|Kérelem azt jelzi, hogy egy biztonsági ellenőrzőeszköz ellenőrzi a hely|
|990012|Rosszindulatú webhely webbejáró|

### <a name="crs40"></a> crs_40_generic_attacks

|RuleId|Leírás|
|---|---|
|960024|Meta karakter Anomáliadetektálási észleléséről szóló figyelmeztetés - ismétlődő a Word karakter|
|950008|Nem dokumentált ColdFusion címkék injektálás|
|950010|LDAP-injektálásos támadásokkal szemben|
|950011|SSI injektálási támadások|
|950018|Univerzális PDF XSS URL-cím észlelhető.|
|950019|E-mail-injektálásos támadásokkal szemben|
|950012|HTTP-Kéréscsempészet támadás.|
|950910|HTTP-válasz a felosztás támadás|
|950911|HTTP-válasz a felosztás támadás|
|950117|Távolifájl-beszúrásos támadás|
|950118|Távolifájl-beszúrásos támadás|
|950119|Távolifájl-beszúrásos támadás|
|950120|Lehetséges távoli fájl beszúrásos (RFI) támadások =-Domain hivatkozás|
|981133|A szabály 981133|
|981134|A szabály 981134|
|950009|Munkamenet-rögzítési támadások|
|950003|Munkamenet-rögzítés|
|950000|Munkamenet-rögzítés|
|950005|Távoli hozzáférési kísérlet|
|950002|Rendszer-parancs hozzáférést|
|950006|Rendszer Parancsinjektálás|
|959151|PHP-injektálási támadások|
|958976|PHP-injektálási támadások|
|958977|PHP-injektálási támadások|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Leírás|
|---|---|
|981231|SQL-megjegyzés feladatütemezési észlelt.|
|981260|SQL hexadecimális kódolás azonosított|
|981320|SQL-injektálásos támadásokkal szemben = közös DB nevet észlelt|
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
|950007|Titkos SQL-injektálásos támadásokkal szemben|
|950001|SQL-injektálásos támadásokkal szemben|
|950908|SQL-injektálásos támadásokkal szemben.|
|959073|SQL-injektálásos támadásokkal szemben|
|981272|Sleep() vagy benchmark() vak sqli teszteket észlel.|
|981250|Észleli az SQL-injektálás referenciaalap és alvó állapottal kapcsolatos megkísérli többek között a feltételes lekérdezések|
|981241|SQL-injektálási kísérleteket feltételes észleli|
|981276|Alapszintű sql injektálás keres. Gyakori támadási karakterlánc, mysql oracle és mások.|
|981270|Megkeresi a mongodb-hez alapszintű SQL injektálási kísérletek|
|981253|Észleli a MySQL és a PostgreSQL tárolt eljárás vagy függvény injektálások|
|981251|Észleli a MySQL UDF injektálással vagy a más adatstruktúra /-manipuláció kísérletek|

### <a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Leírás|
|---|---|
|973336|XSS szűrő - kategória 1 = parancsfájl címke vektor|
|973338|XSS szűrő - kategória 3 = Javascript URI vektor|
|981136|A szabály 981136|
|981018|A szabály 981018|
|958016|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958414|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958032|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958026|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958027|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958054|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958418|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958034|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958019|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958013|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958408|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958012|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958423|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958002|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958017|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958007|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958047|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958410|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958415|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958022|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958405|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958419|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958028|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958057|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958031|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958006|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958033|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958038|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958409|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958001|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958005|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958404|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958023|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958010|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958411|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958422|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958036|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958000|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958018|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958406|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958040|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958052|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958037|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958049|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958030|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958041|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958416|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958024|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958059|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958417|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958020|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958045|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958004|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958421|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958009|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958025|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958413|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958051|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958420|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958407|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958056|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958011|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958412|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958008|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958046|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958039|Többhelyes parancsfájl-kezelési (XSS) támadás|
|958003|Többhelyes parancsfájl-kezelési (XSS) támadás|
|973300|Lehetséges XSS-támadások észlelt – HTML-címke-kezelő|
|973301|XSS támadás észlelve|
|973302|XSS támadás észlelve|
|973303|XSS támadás észlelve|
|973304|XSS támadás észlelve|
|973305|XSS támadás észlelve|
|973306|XSS támadás észlelve|
|973307|XSS támadás észlelve|
|973308|XSS támadás észlelve|
|973309|XSS támadás észlelve|
|973311|XSS támadás észlelve|
|973313|XSS támadás észlelve|
|973314|XSS támadás észlelve|
|973331|Internet Explorer XSS szűrők - támadás észlelve.|
|973315|Internet Explorer XSS szűrők - támadás észlelve.|
|973330|Internet Explorer XSS szűrők - támadás észlelve.|
|973327|Internet Explorer XSS szűrők - támadás észlelve.|
|973326|Internet Explorer XSS szűrők - támadás észlelve.|
|973346|Internet Explorer XSS szűrők - támadás észlelve.|
|973345|Internet Explorer XSS szűrők - támadás észlelve.|
|973324|Internet Explorer XSS szűrők - támadás észlelve.|
|973323|Internet Explorer XSS szűrők - támadás észlelve.|
|973348|Internet Explorer XSS szűrők - támadás észlelve.|
|973321|Internet Explorer XSS szűrők - támadás észlelve.|
|973320|Internet Explorer XSS szűrők - támadás észlelve.|
|973318|Internet Explorer XSS szűrők - támadás észlelve.|
|973317|Internet Explorer XSS szűrők - támadás észlelve.|
|973329|Internet Explorer XSS szűrők - támadás észlelve.|
|973328|Internet Explorer XSS szűrők - támadás észlelve.|

### <a name="crs42"></a> crs_42_tight_security

|RuleId|Leírás|
|---|---|
|950103|A bejárási támadási elérési útja|

### <a name="crs45"></a> crs_45_trojans

|RuleId|Leírás|
|---|---|
|950110|Hátsó kapus hozzáférést|
|950921|Hátsó kapus hozzáférést|
|950922|Hátsó kapus hozzáférést|

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan tilthatja le a WAF-szabályok funkcionáló: [WAF-szabályok testreszabása](application-gateway-customize-waf-rules-portal.md)