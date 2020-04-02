---
title: CRS-szabálycsoportok és -szabályok
titleSuffix: Azure Web Application Firewall
description: Ez a lap a webalkalmazás tűzfalcrs szabálycsoportjairól és szabályairól tartalmaz tájékoztatást.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 563aa701c1403a1ef26c6073496e7e59c7c5096c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521848"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Webalkalmazás-tűzfal CRS-szabálycsoportjai és szabályai

Az Application Gateway webalkalmazás-tűzfal (WAF) megvédi a webalkalmazásokat a gyakori biztonsági résektől és biztonsági résektől. Ez az OWASP 3.1, 3.0 vagy 2.2.9-es alapszabály-készlete alapján meghatározott szabályokon keresztül történik. Ezek a szabályok szabályonként letilthatók. Ez a cikk a felajánlott aktuális szabályokat és szabálykészleteket tartalmazza.

## <a name="core-rule-sets"></a>Alapvető szabálykészletek

Az Application Gateway WAF alapértelmezés szerint előre konfigurálva van crs 3.0-s állapotban. De választhatja a CRS 3.1 vagy CRS 2.2.9 helyett. A CRS 3.1 új szabálykészleteket kínál a Java-fertőzések ellen, a fájlfeltöltési ellenőrzések kezdeti készletét, a rögzített hamis pozitív okat stb. A CRS 3.0 a CRS 2.2.9-hez képest csökkentett hamis pozitív eredményt ad. A [szabályokat igény szerint](application-gateway-customize-waf-rules-portal.md)is testreszabhatja.

> [!div class="mx-imgBorder"]
> ![Szabályok kezelése](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

A WAF a következő webes biztonsági rések ellen nyújt védelmet:

- SQL-injektálási támadások
- Helyek közötti parancsfájlok futtatásával kapcsolatos támadások
- Egyéb gyakori támadások, például parancsinjektálás, HTTP-kérelem-csempészet, HTTP-válaszok felosztása és távoli fájlok felvétele
- A HTTP protokoll megsértése
- HTTP-protokollanomáliák, például hiányzó állomásuser-ügynök és fejlécek elfogadása
- Robotprogramok, webbejárók és képolvasók
- Gyakori alkalmazáshelytelen konfigurációk (például Apache és IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3.1

A CRS 3.1 13 szabálycsoportot tartalmaz, az alábbi táblázat ban látható módon. Minden csoport több szabályt tartalmaz, amelyek letilthatók.

|Szabálycsoport|Leírás|
|---|---|
|**[Általános kérdések](#general-31)**|Általános csoport|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Lezárási módszerek (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Védelem a port- és környezetszkennerek ellen|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Védelem a protokoll- és kódolási problémák ellen|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Védelem a fejlécek befecskendezése, a kérelmek csempészete és a válaszok felosztása ellen|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Fájl- és elérésiút-támadások elleni védelem|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Védelem a távoli fájlfelvétel (RFI) támadásai ellen|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Ismét védheti a távoli kódfuttatást lehetővé tévő támadásokat|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Védelem a PHP-befecskendezéses támadások ellen|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Védelem a webhelyek közötti parancsfájlok támadásai ellen|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Védelem az SQL-injektálási támadások ellen|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Védelem a munkamenet-rögzítési támadások ellen|
|**[KÉRÉS-944-ALKALMAZÁS-TÁMADÁS-MUNKAMENET-JAVA](#crs944-31)**|Védett java támadások ellen|

### <a name="owasp-crs-30"></a>OWASP CRS 3.0

A CRS 3.0 12 szabálycsoportot tartalmaz, ahogy az az alábbi táblázatban látható. Minden csoport több szabályt tartalmaz, amelyek letilthatók.

|Szabálycsoport|Leírás|
|---|---|
|**[Általános kérdések](#general-30)**|Általános csoport|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Lezárási módszerek (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Védelem a port- és környezetszkennerek ellen|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Védelem a protokoll- és kódolási problémák ellen|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Védelem a fejlécek befecskendezése, a kérelmek csempészete és a válaszok felosztása ellen|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Fájl- és elérésiút-támadások elleni védelem|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Védelem a távoli fájlfelvétel (RFI) támadásai ellen|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Ismét védheti a távoli kódfuttatást lehetővé tévő támadásokat|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Védelem a PHP-befecskendezéses támadások ellen|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Védelem a webhelyek közötti parancsfájlok támadásai ellen|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Védelem az SQL-injektálási támadások ellen|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Védelem a munkamenet-rögzítési támadások ellen|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

A CRS 2.2.9 10 szabálycsoportot tartalmaz, az alábbi táblázat szerint. Minden csoport több szabályt tartalmaz, amelyek letilthatók.

|Szabálycsoport|Leírás|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Védelem a protokollmegsértések ellen (például érvénytelen karakterek vagy get kéréstörzsesetén)|
|**[crs_21_protocol_anomalies](#crs21)**|Védelem a helytelen fejlécadatok ellen|
|**[crs_23_request_limits](#crs23)**|Védelem a korlátozásokat meghaladó argumentumokkal vagy fájlokkal szemben|
|**[crs_30_http_policy](#crs30)**|Védelem a korlátozott metódusokkal, fejlécekkel és fájltípusokkal szemben|
|**[crs_35_bad_robots](#crs35)**|Védelem a webbejárók és lapolvasók ellen|
|**[crs_40_generic_attacks](#crs40)**|Általános támadások elleni védelem (például munkamenet-rögzítés, távoli fájlfelvétel és PHP-injektálás)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Védelem az SQL-injektálási támadások ellen|
|**[crs_41_xss_attacks](#crs41xss)**|Védelem a webhelyek közötti parancsfájlok támadásai ellen|
|**[crs_42_tight_security](#crs42)**|Védelem az útvonal-átjárási támadások ellen|
|**[crs_45_trojans](#crs45)**|Véd a hátsó ajtó trójaiak|

A következő szabálycsoportok és szabályok érhetők el, ha webalkalmazás-tűzfalat használ az Application Gateway-en.

# <a name="owasp-31"></a>[OWASP 3.1.](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a>Szabálykészletek

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Általános kérdések</p>

|Szabályazonosító|Leírás|
|---|---|
|200004|Lehetséges többrészes páratlan határvonal.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|Szabályazonosító|Leírás|
|---|---|
|911100|A metódust a házirend nem engedélyezi|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|Szabályazonosító|Leírás|
|---|---|
|913100|A biztonsági képolvasóhoz társított felhasználó-ügynök|
|913101|Parancsfájl-ügynökhöz társított felhasználó-ügynök|
|913102|Webbejáróhoz/robothoz társított felhasználó-ügynök|
|913110|A biztonsági képolvasóhoz társított kérelemfejléc|
|913120|A biztonsági képolvasóhoz társított kérelemfájlnév/argumentum|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|Szabályazonosító|Leírás|
|---|---|
|920100|Érvénytelen HTTP-kérelemsor|
|920120|Többrészes/űrlapadat-megkerülési kísérlet|
|920121|Többrészes/űrlapadat-megkerülési kísérlet|
|920130|Nem sikerült elemezni a kérelem törzsét.|
|920140|A többrészes kérelem törzse nem felelt meg a szigorú érvényesítésen|
|920160|A tartalomhosszúságú HTTP-fejléc nem numerikus.|
|920170|GET vagy head kérés body tartalommal.|
|920171|GET vagy HEAD request átvitel-kódolással.|
|920180|Post kérelem hiányzik tartalom-hossz fejléc.|
|920190|Tartomány = Érvénytelen utolsó bájtérték.|
|920200|Tartomány = Túl sok mező (6 vagy több)|
|920201|Tartomány = Túl sok mező pdf kéréshez (35 vagy több)|
|920202|Tartomány = Túl sok mező pdf kéréshez (6 vagy több)|
|920210|Több/ütköző kapcsolatfejléc-adat található.|
|920220|URL-kódolással való visszaélés támadási kísérlete|
|920230|Több URL-kódolás észlelve|
|920240|URL-kódolással való visszaélés támadási kísérlete|
|920250|UTF8 kódolási visszaélés támadási kísérlet|
|920260|Unicode teljes/félszélességű visszaélés támadási kísérlet|
|920270|Érvénytelen karakter a kérelemben (null karakter)|
|920271|Érvénytelen karakter a kérelemben (nem nyomtatható karakterek)|
|920272|Érvénytelen karakter a kérelemben (az ascii 127 alatti nyomtatható karakteren kívül)|
|920273|Érvénytelen karakter kérésre (nagyon szigorú készleten kívül)|
|920274|Érvénytelen karakter a kérelemfejlécekben (a nagyon szigorú készleten kívül)|
|920280|Állomásfejlécből hiányzik kérelem|
|920290|Üres állomásfejléc|
|920300|Hiányzó elfogadási fejlécet hiányzó kérelem|
|920310|A kérelem üres elfogadási fejléccel rendelkezik|
|920311|A kérelem üres elfogadási fejléccel rendelkezik|
|920320|Hiányzó felhasználói ügynök fejléce|
|920330|Üres felhasználói ügynök fejléce|
|920340|Tartalmat tartalmazó, de hiányzó tartalomtípus-fejlécet tartalmazó kérelem|
|920341|A tartalmat tartalmazó kérelemhez tartalomtípus-fejléc szükséges|
|920350|Az állomásfejléc egy numerikus IP-cím|
|920360|Az argumentum neve túl hosszú|
|920370|Az argumentum értéke túl hosszú|
|920380|Túl sok argumentum van a kérelemben|
|920390|Az összes argumentum mérete túllépve|
|920400|A feltöltött fájl mérete túl nagy|
|920410|A feltöltött fájlok teljes mérete túl nagy|
|920420|A kérelem tartalomtípusát a házirend nem engedélyezi|
|920430|A HTTP protokoll verziót a házirend nem engedélyezi|
|920440|Az URL-fájlkiterjesztését házirend korlátozza|
|920450|A HTTP-fejlécet házirend korlátozza (%@{MATCHED_VAR})|
|920460|Rendellenes escape karakterek|
|920470|Érvénytelen tartalomtípus fejléc|
|920480|Karakterkészlet-paraméter korlátozása a tartalomtípus fejlécében|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|Szabályazonosító|Leírás|
|---|---|
|921110|HTTP-kérelem csempészet támadás|
|921120|HTTP-válaszfelosztási támadás|
|921130|HTTP-válaszfelosztási támadás|
|921140|HTTP-fejléces injektálási támadás fejléceken keresztül|
|921150|HTTP-fejlécbefecskendezéses támadás a hasznos adaton keresztül (CR/LF észlelve)|
|921151|HTTP-fejlécbefecskendezéses támadás a hasznos adaton keresztül (CR/LF észlelve)|
|921160|HTTP-fejlécbefecskendezéses támadás a hasznos adaton keresztül (CR/LF és fejlécnév észlelve)|
|921170|HTTP paraméter szennyezés|
|921180|HTTP-paraméterszennyezés (%{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|Szabályazonosító|Leírás|
|---|---|
|930100|Elérési út átjárási támadása (/.. /)|
|930110|Elérési út átjárási támadása (/.. /)|
|930120|Operációs rendszer fájlhozzáférési kísérlet|
|930130|Korlátozott fájlhozzáférési kísérlet|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|Szabályazonosító|Leírás|
|---|---|
|931100|Lehetséges távoli fájlfelvétel (RFI) támadás = URL-paraméter IP-cím használatával|
|931110|Lehetséges távoli fájlfelvétel (RFI) támadás = Gyakori RFI sebezhető paraméternév használt w/URL hasznos adat|
|931120|Lehetséges távoli fájlfelvétel (RFI) Attack = URL hasznos adat Használt w / záró kérdőjel karakter (?)|
|931130|Lehetséges távoli fájlfelvétel (RFI) támadás = tartományon kívüli hivatkozás/hivatkozás|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|Szabályazonosító|Leírás|
|---|---|
|932100|Távoli parancs végrehajtása: Unix parancs befecskendezése|
|932105|Távoli parancs végrehajtása: Unix parancs befecskendezése|
|932106|Távoli parancs végrehajtása: Unix parancs befecskendezése|
|932110|Távoli parancs végrehajtása: Windows parancsinjektálás|
|932115|Távoli parancs végrehajtása: Windows parancsinjektálás|
|932120|Távoli parancsvégrehajtás = A Windows PowerShell parancs található|
|932130|Távoli parancsvégrehajtás = Unix rendszerhéj-kifejezés található|
|932140|Távoli parancsvégrehajtás = Windows FOR/IF parancs található|
|932160|Távoli parancsvégrehajtás = Unix rendszerhéj-kód található|
|932170|Távoli parancsvégrehajtás = Shellshock (CVE-2014-6271)|
|932171|Távoli parancsvégrehajtás = Shellshock (CVE-2014-6271)|
|932180|Korlátozott fájlfeltöltési kísérlet|
|932190|Távoli parancsvégrehajtása: Helyettesítő karakter megkerülési technikával|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|Szabályazonosító|Leírás|
|---|---|
|933100|PHP injection attack = Nyitó/záró címke található|
|933110|PHP Injection Attack = PHP Script File Upload Talált|
|933111|PHP injection támadás: PHP Script File Upload Talált|
|933120|PHP injection attack = konfigurációs irányelv található|
|933130|PHP injection attack = talált változók|
|933131|PHP injection attack: Talált változók|
|933140|PHP injection támadás: I / O Stream talált|
|933150|PHP Injection Attack = Magas kockázatú PHP függvény neve található|
|933151|PHP injection attack: Közepes kockázatú PHP függvény neve található|
|933160|PHP injection attack = magas kockázatú PHP függvényhívás található|
|933161|PHP injection attack: Alacsony értékű PHP függvény hívás található|
|933170|PHP injection támadás: szerializált objektum injekció|
|933180|PHP injection attack = változó függvényhívás található|
|933190|PHP injection támadás: PHP záró tag található|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|Szabályazonosító|Leírás|
|---|---|
|941100|XSS támadás észlelt libinjection|
|941101|XSS támadás észlelt libinjection|
|941110|XSS-szűrő - 1.|
|941130|XSS-szűrő - 3.|
|941140|XSS-szűrő - 4.|
|941150|XSS-szűrő - 5.|
|941160|NoScript XSS InjectionChecker: HTML injekció|
|941170|NoScript XSS InjectionChecker: Attribútum injekció|
|941180|Csomópont-validator feketelista kulcsszavak|
|941190|XSS stíluslapok használatával|
|941200|XSS VML-keretekkel|
|941210|XSS a homályos Javascript|
|941220|XSS a homályos VB-parancsfájl használatával|
|941230|XSS a "beágyazás" címkével|
|941240|XSS az "import" vagy a "implementation" attribútum használatával|
|941250|IE XSS-szűrők - Támadás észlelve|
|941260|XSS a "meta" címkével|
|941270|XSS a "link" href használatával|
|941280|XSS az "alap" címkével|
|941290|XSS az "applet" címkével|
|941300|XSS az "objektum" címkével|
|941310|US-ASCII hibás kódolású XSS-szűrő - Támadás észlelve.|
|941320|Lehetséges XSS-támadás észlelve - HTML címkekezelő|
|941330|Az azI XSS-szűrők – támadás észlelve.|
|941340|Az azI XSS-szűrők – támadás észlelve.|
|941350|UTF-7 Kódolás IE XSS - Támadás észlelve.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|Szabályazonosító|Leírás|
|---|---|
|942100|SQL injection attack észlelt keresztül libinjection|
|942110|SQL injection attack: Gyakori injektálási vizsgálat észlelve|
|942120|SQL injection attack: SQL operátor észlelve|
|942130|SQL injection attack: SQL Tautology észlelt.|
|942140|SQL injection attack = Gyakori DB-nevek észlelve|
|942150|SQL injektálási támadás|
|942160|Észleli a vak sqli teszteket sleep() vagy benchmark() használatával.|
|942170|Észleli az SQL benchmark és az alvó állapot injekciózási kísérleteit, beleértve a feltételes lekérdezéseket is|
|942180|Alapszintű SQL-hitelesítési megkerülési kísérletek észlelése 1/3|
|942190|Érzékeli az MSSQL kódfuttatást és információgyűjtési kísérleteket|
|942200|Felismeri a MySQL comment-/space-obfuscated injekciók és backtick megszűnése|
|942210|Észleli a láncolt SQL-befecskendezési kísérleteket 1/2|
|942220|Keresi egész túlcsordulás támadások, ezek venni skipfish, kivéve 3.0.00738585072|
|942230|Feltételes SQL-injektálási kísérletek észlelése|
|942240|Felismeri a MySQL charset kapcsolót és az MSSQL DoS kísérleteket|
|942250|Érzékeli match ellen, egyesítése és végrehajtása azonnali injekciók|
|942251|Érzékeli a HAVING injekciókat|
|942260|Alapszintű SQL-hitelesítési megkerülési kísérletek észlelése 2/3|
|942270|Keresi az alapvető sql injekció. Közös támadási karakterlánc a mysql oracle és mások számára|
|942280|Észleli a Postgres pg_sleep injekciót, megvárja a késleltetett támadásokat és az adatbázis leállítására irányuló kísérleteket|
|942290|Megkeresi az alapvető MongoDB SQL-injektálási kísérleteket|
|942300|Felismeri a MySQL megjegyzéseket, feltételeket és ch (a)r injekciókat|
|942310|Észleli a láncolt SQL-befecskendezési kísérleteket 2/2|
|942320|Érzékeli a MySQL és a PostgreSQL tárolt eljárás / funkció injekciók|
|942330|Észleli a klasszikus SQL-befecskendezéses szondákat 1/2|
|942340|Alapszintű SQL-hitelesítési megkerülési kísérletek észlelése 3/3|
|942350|Felismeri a MySQL UDF injekciózást és más adat/szerkezet manipulációs kísérleteket|
|942360|Észleli az összefont alapvető SQL-injektálást és az SQLLFI kísérleteket|
|942361|Észleli az alapvető SQL-injektálást a kulcsszómódosítása vagy az egyesítés alapján|
|942370|Felismeri a klasszikus SQL-befecskendezéses szondákat 2/2|
|942380|SQL injektálási támadás|
|942390|SQL injektálási támadás|
|942400|SQL injektálási támadás|
|942410|SQL injektálási támadás|
|942420|Korlátozott SQL-karakteranomália-detektálás (cookie-k): # a speciális karakterek túllépve (8)|
|942421|Korlátozott SQL-karakteranomália-detektálás (cookie-k): # a speciális karakterek túllépve (3)|
|942430|Korlátozott SQL-karakteranomáliadetektálás (args): # a speciális karakterek túllépve (12)|
|942431|Korlátozott SQL-karakteranomáliadetektálás (args): # a speciális karakterek túllépve (6)|
|942432|Korlátozott SQL-karakteranomáliadetektálás (args): # a speciális karakterek túllépve (2)|
|942440|SQL megjegyzéssorozat észlelve.|
|942450|SQL hex kódolás azonosítva|
|942460|Meta-karakter anomáliadetektálási riasztás – ismétlődő, nem szókarakterek|
|942470|SQL injektálási támadás|
|942480|SQL injektálási támadás|
|942490|Észleli a klasszikus SQL-befecskendezéses szondákat 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|Szabályazonosító|Leírás|
|---|---|
|943100|Lehetséges munkamenet-rögzítési támadás = Cookie-értékek beállítása HTML-ben|
|943110|Lehetséges munkamenet-rögzítési támadás = Munkamenet-azonosító paraméterneve tartományon kívüli hivatkozóval|
|943120|Lehetséges munkamenet-rögzítési támadás = Munkamenet-azonosító paraméterneve ajánló nélkül|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">KÉRÉS-944-ALKALMAZÁS-TÁMADÁS-MUNKAMENET-JAVA</p>

|Szabályazonosító|Leírás|
|---|---|
|944120|Lehetséges hasznos adat végrehajtás és távoli parancsvégrehajtás|
|944130|Gyanús Java osztályok|
|944200|A Java deszerializálása Apache Commons kihasználása|

# <a name="owasp-30"></a>[OWASP 3,0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a>Szabálykészletek

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Általános kérdések</p>

|Szabályazonosító|Leírás|
|---|---|
|200004|Lehetséges többrészes páratlan határvonal.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|Szabályazonosító|Leírás|
|---|---|
|911100|A metódust a házirend nem engedélyezi|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|Szabályazonosító|Leírás|
|---|---|
|913100|A biztonsági képolvasóhoz társított felhasználó-ügynök|
|913110|A biztonsági képolvasóhoz társított kérelemfejléc|
|913120|A biztonsági képolvasóhoz társított kérelemfájlnév/argumentum|
|913101|Parancsfájl-ügynökhöz társított felhasználó-ügynök|
|913102|Webbejáróhoz/robothoz társított felhasználó-ügynök|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|Szabályazonosító|Leírás|
|---|---|
|920100|Érvénytelen HTTP-kérelemsor|
|920130|Nem sikerült elemezni a kérelem törzsét.|
|920140|A többrészes kérelem törzse nem felelt meg a szigorú érvényesítésen|
|920160|A tartalomhosszúságú HTTP-fejléc nem numerikus.|
|920170|GET vagy head kérés body tartalommal.|
|920180|Post kérelem hiányzik tartalom-hossz fejléc.|
|920190|Tartomány = Érvénytelen utolsó bájtérték.|
|920210|Több/ütköző kapcsolatfejléc-adat található.|
|920220|URL-kódolással való visszaélés támadási kísérlete|
|920240|URL-kódolással való visszaélés támadási kísérlete|
|920250|UTF8 kódolási visszaélés támadási kísérlet|
|920260|Unicode teljes/félszélességű visszaélés támadási kísérlet|
|920270|Érvénytelen karakter a kérelemben (null karakter)|
|920280|Állomásfejlécből hiányzik kérelem|
|920290|Üres állomásfejléc|
|920310|A kérelem üres elfogadási fejléccel rendelkezik|
|920311|A kérelem üres elfogadási fejléccel rendelkezik|
|920330|Üres felhasználói ügynök fejléce|
|920340|Tartalmat tartalmazó, de hiányzó tartalomtípus-fejlécet tartalmazó kérelem|
|920350|Az állomásfejléc egy numerikus IP-cím|
|920380|Túl sok argumentum van a kérelemben|
|920360|Az argumentum neve túl hosszú|
|920370|Az argumentum értéke túl hosszú|
|920390|Az összes argumentum mérete túllépve|
|920400|A feltöltött fájl mérete túl nagy|
|920410|A feltöltött fájlok teljes mérete túl nagy|
|920420|A kérelem tartalomtípusát a házirend nem engedélyezi|
|920430|A HTTP protokoll verziót a házirend nem engedélyezi|
|920440|Az URL-fájlkiterjesztését házirend korlátozza|
|920450|A HTTP-fejlécet házirend korlátozza (%@{MATCHED_VAR})|
|920200|Tartomány = Túl sok mező (6 vagy több)|
|920201|Tartomány = Túl sok mező pdf kéréshez (35 vagy több)|
|920230|Több URL-kódolás észlelve|
|920300|Hiányzó elfogadási fejlécet hiányzó kérelem|
|920271|Érvénytelen karakter a kérelemben (nem nyomtatható karakterek)|
|920320|Hiányzó felhasználói ügynök fejléce|
|920272|Érvénytelen karakter a kérelemben (az ascii 127 alatti nyomtatható karakteren kívül)|
|920202|Tartomány = Túl sok mező pdf kéréshez (6 vagy több)|
|920273|Érvénytelen karakter kérésre (nagyon szigorú készleten kívül)|
|920274|Érvénytelen karakter a kérelemfejlécekben (a nagyon szigorú készleten kívül)|
|920460|Rendellenes escape karakterek|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|Szabályazonosító|Leírás|
|---|---|
|921100|HTTP-kérelem csempészet támadás.|
|921110|HTTP-kérelem csempészet támadás|
|921120|HTTP-válaszfelosztási támadás|
|921130|HTTP-válaszfelosztási támadás|
|921140|HTTP-fejléces injektálási támadás fejléceken keresztül|
|921150|HTTP-fejlécbefecskendezéses támadás a hasznos adaton keresztül (CR/LF észlelve)|
|921160|HTTP-fejlécbefecskendezéses támadás a hasznos adaton keresztül (CR/LF és fejlécnév észlelve)|
|921151|HTTP-fejlécbefecskendezéses támadás a hasznos adaton keresztül (CR/LF észlelve)|
|921170|HTTP paraméter szennyezés|
|921180|HTTP-szennyezési paraméter (%@{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|Szabályazonosító|Leírás|
|---|---|
|930100|Elérési út átjárási támadása (/.. /)|
|930110|Elérési út átjárási támadása (/.. /)|
|930120|Operációs rendszer fájlhozzáférési kísérlet|
|930130|Korlátozott fájlhozzáférési kísérlet|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|Szabályazonosító|Leírás|
|---|---|
|931100|Lehetséges távoli fájlfelvétel (RFI) támadás = URL-paraméter IP-cím használatával|
|931110|Lehetséges távoli fájlfelvétel (RFI) támadás = Gyakori RFI sebezhető paraméternév használt w/URL hasznos adat|
|931120|Lehetséges távoli fájlfelvétel (RFI) Attack = URL hasznos adat Használt w / záró kérdőjel karakter (?)|
|931130|Lehetséges távoli fájlfelvétel (RFI) támadás = tartományon kívüli hivatkozás/hivatkozás|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|Szabályazonosító|Leírás|
|---|---|
|932120|Távoli parancsvégrehajtás = A Windows PowerShell parancs található|
|932130|Távoli parancsvégrehajtás = Unix rendszerhéj-kifejezés található|
|932140|Távoli parancsvégrehajtás = Windows FOR/IF parancs található|
|932160|Távoli parancsvégrehajtás = Unix rendszerhéj-kód található|
|932170|Távoli parancsvégrehajtás = Shellshock (CVE-2014-6271)|
|932171|Távoli parancsvégrehajtás = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|Szabályazonosító|Leírás|
|---|---|
|933100|PHP injection attack = Nyitó/záró címke található|
|933110|PHP Injection Attack = PHP Script File Upload Talált|
|933120|PHP injection attack = konfigurációs irányelv található|
|933130|PHP injection attack = talált változók|
|933150|PHP Injection Attack = Magas kockázatú PHP függvény neve található|
|933160|PHP injection attack = magas kockázatú PHP függvényhívás található|
|933180|PHP injection attack = változó függvényhívás található|
|933151|PHP Injection Attack = Közepes kockázatú PHP függvény neve található|
|933131|PHP injection attack = talált változók|
|933161|PHP Injection Attack = Alacsony értékű PHP függvényhívás található|
|933111|PHP Injection Attack = PHP Script File Upload Talált|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|Szabályazonosító|Leírás|
|---|---|
|941100|XSS támadás észlelt libinjection|
|941110|XSS-szűrő - 1.|
|941130|XSS-szűrő - 3.|
|941140|XSS-szűrő - 4.|
|941150|XSS-szűrő - 5.|
|941180|Csomópont-validator feketelista kulcsszavak|
|941190|XSS stíluslapok használatával|
|941200|XSS VML-keretekkel|
|941210|XSS a homályos Javascript|
|941220|XSS a homályos VB-parancsfájl használatával|
|941230|XSS a "beágyazás" címkével|
|941240|XSS az "import" vagy a "implementation" attribútum használatával|
|941260|XSS a "meta" címkével|
|941270|XSS a "link" href használatával|
|941280|XSS az "alap" címkével|
|941290|XSS az "applet" címkével|
|941300|XSS az "objektum" címkével|
|941310|US-ASCII hibás kódolású XSS-szűrő - Támadás észlelve.|
|941330|Az azI XSS-szűrők – támadás észlelve.|
|941340|Az azI XSS-szűrők – támadás észlelve.|
|941350|UTF-7 Kódolás IE XSS - Támadás észlelve.|
|941320|Lehetséges XSS-támadás észlelve - HTML címkekezelő|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|Szabályazonosító|Leírás|
|---|---|
|942100|SQL injection attack észlelt keresztül libinjection|
|942110|SQL injection attack: Gyakori injektálási vizsgálat észlelve|
|942130|SQL injection attack: SQL Tautology észlelt.|
|942140|SQL injection attack = Gyakori DB-nevek észlelve|
|942160|Észleli a vak sqli teszteket sleep() vagy benchmark() használatával.|
|942170|Észleli az SQL benchmark és az alvó állapot injekciózási kísérleteit, beleértve a feltételes lekérdezéseket is|
|942190|Érzékeli az MSSQL kódfuttatást és információgyűjtési kísérleteket|
|942200|Felismeri a MySQL comment-/space-obfuscated injekciók és backtick megszűnése|
|942230|Feltételes SQL-injektálási kísérletek észlelése|
|942260|Alapszintű SQL-hitelesítési megkerülési kísérletek észlelése 2/3|
|942270|Keresi az alapvető sql injekció. Közös támadás string mysql oracle és mások.|
|942290|Megkeresi az alapvető MongoDB SQL-injektálási kísérleteket|
|942300|Felismeri a MySQL megjegyzéseket, feltételeket és ch (a)r injekciókat|
|942310|Észleli a láncolt SQL-befecskendezési kísérleteket 2/2|
|942320|Érzékeli a MySQL és a PostgreSQL tárolt eljárás / funkció injekciók|
|942330|Észleli a klasszikus SQL-befecskendezéses szondákat 1/2|
|942340|Alapszintű SQL-hitelesítési megkerülési kísérletek észlelése 3/3|
|942350|Felismeri a MySQL UDF injekciózást és más adat/szerkezet manipulációs kísérleteket|
|942360|Észleli az összefont alapvető SQL-injektálást és az SQLLFI kísérleteket|
|942370|Felismeri a klasszikus SQL-befecskendezéses szondákat 2/2|
|942150|SQL injektálási támadás|
|942410|SQL injektálási támadás|
|942430|Korlátozott SQL-karakteranomáliadetektálás (args): # a speciális karakterek túllépve (12)|
|942440|SQL megjegyzéssorozat észlelve.|
|942450|SQL hex kódolás azonosítva|
|942251|Érzékeli a HAVING injekciókat|
|942460|Meta-karakter anomáliadetektálási riasztás – ismétlődő, nem szókarakterek|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|Szabályazonosító|Leírás|
|---|---|
|943100|Lehetséges munkamenet-rögzítési támadás = Cookie-értékek beállítása HTML-ben|
|943110|Lehetséges munkamenet-rögzítési támadás = Munkamenet-azonosító paraméterneve tartományon kívüli hivatkozóval|
|943120|Lehetséges munkamenet-rögzítési támadás = Munkamenet-azonosító paraméterneve ajánló nélkül|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a>Szabálykészletek

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a>crs_20_protocol_violations

|Szabályazonosító|Leírás|
|---|---|
|960911|Érvénytelen HTTP-kérelemsor|
|981227|Apache error = Érvénytelen URI a kérelemben.|
|960912|Nem sikerült elemezni a kérelem törzsét.|
|960914|A többrészes kérelem törzse nem felelt meg a szigorú érvényesítésen|
|960915|A többrészes elemző egy lehetséges nem egyező határvonalat észlelt.|
|960016|A tartalomhosszúságú HTTP-fejléc nem numerikus.|
|960011|GET vagy head kérés body tartalommal.|
|960012|Post kérelem hiányzik tartalom-hossz fejléc.|
|960902|Az identitáskódolás érvénytelen használata.|
|960022|A HTTP 1.0-s verziójához nem engedélyezett a fejléc.|
|960020|A Pragma-fejléc hez cache-control fejléc szükséges a HTTP/1.1-es kérelmekhez.|
|958291|Tartomány = mező létezik, és 0-val kezdődik.|
|958230|Tartomány = Érvénytelen utolsó bájtérték.|
|958295|Több/ütköző kapcsolatfejléc-adat található.|
|950107|URL-kódolással való visszaélés támadási kísérlete|
|950109|Több URL-kódolás észlelve|
|950108|URL-kódolással való visszaélés támadási kísérlete|
|950801|UTF8 kódolási visszaélés támadási kísérlet|
|950116|Unicode teljes/félszélességű visszaélés támadási kísérlet|
|960901|Érvénytelen karakter a kérelemben|
|960018|Érvénytelen karakter a kérelemben|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a>crs_21_protocol_anomalies

|Szabályazonosító|Leírás|
|---|---|
|960008|Állomásfejlécből hiányzik kérelem|
|960007|Üres állomásfejléc|
|960015|Hiányzó elfogadási fejlécet hiányzó kérelem|
|960021|A kérelem üres elfogadási fejléccel rendelkezik|
|960009|Felhasználóügynök fejlécének hiányzik kérése|
|960006|Üres felhasználói ügynök fejléce|
|960904|Tartalmat tartalmazó, de hiányzó tartalomtípus-fejlécet tartalmazó kérelem|
|960017|Az állomásfejléc egy numerikus IP-cím|

### <a name="crs_23_request_limits"></a><a name="crs23"></a>crs_23_request_limits

|Szabályazonosító|Leírás|
|---|---|
|960209|Az argumentum neve túl hosszú|
|960208|Az argumentum értéke túl hosszú|
|960335|Túl sok argumentum van a kérelemben|
|960341|Az összes argumentum mérete túllépve|
|960342|A feltöltött fájl mérete túl nagy|
|960343|A feltöltött fájlok teljes mérete túl nagy|

### <a name="crs_30_http_policy"></a><a name="crs30"></a>crs_30_http_policy

|Szabályazonosító|Leírás|
|---|---|
|960032|A metódust a házirend nem engedélyezi|
|960010|A kérelem tartalomtípusát a házirend nem engedélyezi|
|960034|A HTTP protokoll verziót a házirend nem engedélyezi|
|960035|Az URL-fájlkiterjesztését házirend korlátozza|
|960038|A HTTP-fejlécet házirend korlátozza|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a>crs_35_bad_robots

|Szabályazonosító|Leírás|
|---|---|
|990002|Kérelem: A webhelyet beszkennelt biztonsági szkenner|
|990901|Kérelem: A webhelyet beszkennelt biztonsági szkenner|
|990902|Kérelem: A webhelyet beszkennelt biztonsági szkenner|
|990012|Rogue weboldal lánctalpas|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a>crs_40_generic_attacks

|Szabályazonosító|Leírás|
|---|---|
|960024|Meta-karakter anomáliadetektálási riasztás – ismétlődő, nem szókarakterek|
|950008|Nem dokumentált coldfusion címkék befecskendezése|
|950010|LDAP injekciós támadás|
|950011|SSI injekció attack|
|950018|Univerzális PDF XSS URL-cím észlelve.|
|950019|E-mail injekció támadás|
|950012|HTTP-kérelem csempészet támadás.|
|950910|HTTP-válaszfelosztási támadás|
|950911|HTTP-válaszfelosztási támadás|
|950117|Távoli fájlfelvétel-támadás|
|950118|Távoli fájlfelvétel-támadás|
|950119|Távoli fájlfelvétel-támadás|
|950120|Lehetséges távoli fájlfelvétel (RFI) támadás = tartományon kívüli hivatkozás/hivatkozás|
|981133|szabály|
|981134|szabály|
|950009|Munkamenet-rögzítési támadás|
|950003|Munkamenet-rögzítés|
|950000|Munkamenet-rögzítés|
|950005|Távoli fájlelérési kísérlet|
|950002|Rendszerparancs-hozzáférés|
|950006|System Command injekció|
|959151|PHP injekció sinttámadás|
|958976|PHP injekció sinttámadás|
|958977|PHP injekció sinttámadás|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a>crs_41_sql_injection_attacks

|Szabályazonosító|Leírás|
|---|---|
|981231|SQL megjegyzéssorozat észlelve.|
|981260|SQL hex kódolás azonosítva|
|981320|SQL injection attack = Gyakori DB-nevek észlelve|
|981300|szabály|
|981301|szabály|
|981302|szabály|
|981303|szabály|
|981304|szabály|
|981305|szabály|
|981306|szabály|
|981307|szabály|
|981308|szabály|
|981309|szabály|
|981310|szabály|
|981311|szabály|
|981312|szabály|
|981313|szabály|
|981314|szabály|
|981315|szabály|
|981316|szabály|
|981317|SQL SELECT utasítás anomáliaészlelési riasztás|
|950007|Vak SQL injekciós támadás|
|950001|SQL injektálási támadás|
|950908|SQL injection támadás.|
|959073|SQL injektálási támadás|
|981272|Észleli a vak sqli teszteket sleep() vagy benchmark() használatával.|
|981250|Észleli az SQL benchmark és az alvó állapot injekciózási kísérleteit, beleértve a feltételes lekérdezéseket is|
|981241|Feltételes SQL-injektálási kísérletek észlelése|
|981276|Keresi az alapvető sql injekció. Közös támadás string mysql oracle és mások.|
|981270|Megkeresi az alapvető MongoDB SQL-injektálási kísérleteket|
|981253|Érzékeli a MySQL és a PostgreSQL tárolt eljárás / funkció injekciók|
|981251|Felismeri a MySQL UDF injekciózást és más adat/szerkezet manipulációs kísérleteket|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a>crs_41_xss_attacks

|Szabályazonosító|Leírás|
|---|---|
|973336|XSS-szűrő - 1.|
|973338|XSS-szűrő - 3.|
|981136|szabály|
|981018|szabály|
|958016|Helyek közötti parancsfájlok (XSS) támadása|
|958414|Helyek közötti parancsfájlok (XSS) támadása|
|958032|Helyek közötti parancsfájlok (XSS) támadása|
|958026|Helyek közötti parancsfájlok (XSS) támadása|
|958027|Helyek közötti parancsfájlok (XSS) támadása|
|958054|Helyek közötti parancsfájlok (XSS) támadása|
|958418|Helyek közötti parancsfájlok (XSS) támadása|
|958034|Helyek közötti parancsfájlok (XSS) támadása|
|958019|Helyek közötti parancsfájlok (XSS) támadása|
|958013|Helyek közötti parancsfájlok (XSS) támadása|
|958408|Helyek közötti parancsfájlok (XSS) támadása|
|958012|Helyek közötti parancsfájlok (XSS) támadása|
|958423|Helyek közötti parancsfájlok (XSS) támadása|
|958002|Helyek közötti parancsfájlok (XSS) támadása|
|958017|Helyek közötti parancsfájlok (XSS) támadása|
|958007|Helyek közötti parancsfájlok (XSS) támadása|
|958047|Helyek közötti parancsfájlok (XSS) támadása|
|958410|Helyek közötti parancsfájlok (XSS) támadása|
|958415|Helyek közötti parancsfájlok (XSS) támadása|
|958022|Helyek közötti parancsfájlok (XSS) támadása|
|958405|Helyek közötti parancsfájlok (XSS) támadása|
|958419|Helyek közötti parancsfájlok (XSS) támadása|
|958028|Helyek közötti parancsfájlok (XSS) támadása|
|958057|Helyek közötti parancsfájlok (XSS) támadása|
|958031|Helyek közötti parancsfájlok (XSS) támadása|
|958006|Helyek közötti parancsfájlok (XSS) támadása|
|958033|Helyek közötti parancsfájlok (XSS) támadása|
|958038|Helyek közötti parancsfájlok (XSS) támadása|
|958409|Helyek közötti parancsfájlok (XSS) támadása|
|958001|Helyek közötti parancsfájlok (XSS) támadása|
|958005|Helyek közötti parancsfájlok (XSS) támadása|
|958404|Helyek közötti parancsfájlok (XSS) támadása|
|958023|Helyek közötti parancsfájlok (XSS) támadása|
|958010|Helyek közötti parancsfájlok (XSS) támadása|
|958411|Helyek közötti parancsfájlok (XSS) támadása|
|958422|Helyek közötti parancsfájlok (XSS) támadása|
|958036|Helyek közötti parancsfájlok (XSS) támadása|
|958000|Helyek közötti parancsfájlok (XSS) támadása|
|958018|Helyek közötti parancsfájlok (XSS) támadása|
|958406|Helyek közötti parancsfájlok (XSS) támadása|
|958040|Helyek közötti parancsfájlok (XSS) támadása|
|958052|Helyek közötti parancsfájlok (XSS) támadása|
|958037|Helyek közötti parancsfájlok (XSS) támadása|
|958049|Helyek közötti parancsfájlok (XSS) támadása|
|958030|Helyek közötti parancsfájlok (XSS) támadása|
|958041|Helyek közötti parancsfájlok (XSS) támadása|
|958416|Helyek közötti parancsfájlok (XSS) támadása|
|958024|Helyek közötti parancsfájlok (XSS) támadása|
|958059|Helyek közötti parancsfájlok (XSS) támadása|
|958417|Helyek közötti parancsfájlok (XSS) támadása|
|958020|Helyek közötti parancsfájlok (XSS) támadása|
|958045|Helyek közötti parancsfájlok (XSS) támadása|
|958004|Helyek közötti parancsfájlok (XSS) támadása|
|958421|Helyek közötti parancsfájlok (XSS) támadása|
|958009|Helyek közötti parancsfájlok (XSS) támadása|
|958025|Helyek közötti parancsfájlok (XSS) támadása|
|958413|Helyek közötti parancsfájlok (XSS) támadása|
|958051|Helyek közötti parancsfájlok (XSS) támadása|
|958420|Helyek közötti parancsfájlok (XSS) támadása|
|958407|Helyek közötti parancsfájlok (XSS) támadása|
|958056|Helyek közötti parancsfájlok (XSS) támadása|
|958011|Helyek közötti parancsfájlok (XSS) támadása|
|958412|Helyek közötti parancsfájlok (XSS) támadása|
|958008|Helyek közötti parancsfájlok (XSS) támadása|
|958046|Helyek közötti parancsfájlok (XSS) támadása|
|958039|Helyek közötti parancsfájlok (XSS) támadása|
|958003|Helyek közötti parancsfájlok (XSS) támadása|
|973300|Lehetséges XSS-támadás észlelve - HTML címkekezelő|
|973301|XSS-támadás észlelve|
|973302|XSS-támadás észlelve|
|973303|XSS-támadás észlelve|
|973304|XSS-támadás észlelve|
|973305|XSS-támadás észlelve|
|973306|XSS-támadás észlelve|
|973307|XSS-támadás észlelve|
|973308|XSS-támadás észlelve|
|973309|XSS-támadás észlelve|
|973311|XSS-támadás észlelve|
|973313|XSS-támadás észlelve|
|973314|XSS-támadás észlelve|
|973331|Az azI XSS-szűrők – támadás észlelve.|
|973315|Az azI XSS-szűrők – támadás észlelve.|
|973330|Az azI XSS-szűrők – támadás észlelve.|
|973327|Az azI XSS-szűrők – támadás észlelve.|
|973326|Az azI XSS-szűrők – támadás észlelve.|
|973346|Az azI XSS-szűrők – támadás észlelve.|
|973345|Az azI XSS-szűrők – támadás észlelve.|
|973324|Az azI XSS-szűrők – támadás észlelve.|
|973323|Az azI XSS-szűrők – támadás észlelve.|
|973348|Az azI XSS-szűrők – támadás észlelve.|
|973321|Az azI XSS-szűrők – támadás észlelve.|
|973320|Az azI XSS-szűrők – támadás észlelve.|
|973318|Az azI XSS-szűrők – támadás észlelve.|
|973317|Az azI XSS-szűrők – támadás észlelve.|
|973329|Az azI XSS-szűrők – támadás észlelve.|
|973328|Az azI XSS-szűrők – támadás észlelve.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a>crs_42_tight_security

|Szabályazonosító|Leírás|
|---|---|
|950103|Elérési út átjárási támadás|

### <a name="crs_45_trojans"></a><a name="crs45"></a>crs_45_trojans

|Szabályazonosító|Leírás|
|---|---|
|950110|Hátsó ajtó hozvaló|
|950921|Hátsó ajtó hozvaló|
|950922|Hátsó ajtó hozvaló|

---

## <a name="next-steps"></a>További lépések

- [Webalkalmazás-tűzfal szabályainak testreszabása az Azure Portal használatával](application-gateway-customize-waf-rules-portal.md)
