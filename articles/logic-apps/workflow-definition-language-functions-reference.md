---
title: Referenciaútmutató kifejezések függvényeihez
description: Útmutató az Azure Logic Apps és a Power Automate kifejezésekben lévő függvényeihez
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 87798c93bbc1098daea2f7258a3af3e26bb4bb93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283913"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Útmutató a függvények használatához az Azure Logic Apps és a Power Automate kifejezésekben

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Power [Automate](https://docs.microsoft.com/flow/getting-started)munkafolyamat-definícióihoz egyes [kifejezések](../logic-apps/logic-apps-workflow-definition-language.md#expressions) olyan futásidejű műveletekből kapják meg az értékeiket, amelyek előfordulhat, hogy még nem léteznek a munkafolyamat futásának megkezdésekor. Ha ezekre az értékekre hivatkozik, vagy felszeretné dolgozni az értékeket ezekben a kifejezésekben, használhatja a [Munkafolyamat-definíció nyelve](../logic-apps/logic-apps-workflow-definition-language.md)által biztosított *függvényeket.* 

> [!NOTE]
> Ez a referencialap az Azure Logic Apps és a Power Automate alkalmazásra egyaránt vonatkozik, de megjelenik az Azure Logic Apps dokumentációjában. Bár ez a lap kifejezetten a logikai alkalmazások, ezek a függvények a folyamatok és a logikai alkalmazások is működik. A Power Automate funkcióiról és kifejezéseiről a [Kifejezések használata feltételek között](https://docs.microsoft.com/flow/use-expressions-in-conditions)című témakörben talál további információt.

Az értékeket például matematikai függvények, például az [add() függvény](../logic-apps/workflow-definition-language-functions-reference.md#add)használatával számíthatja ki, ha az összeget egész számokból vagy úszókból szeretné használni. Az alábbiakban további példaként szolgálnak a függvényekkel végrehajtható feladatok:

| Tevékenység | Függvény szintaxisa | Eredmény |
| ---- | --------------- | ------ |
| Karakterlánc visszaadása kisbetűs formátumban. | toLower('<*szöveg*>') <p>Például: toLower('Hello') | "Helló" |
| Globálisan egyedi azonosító (GUID) visszaadása. | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Ha [általános céljuk alapján](#ordered-by-purpose)szeretne függvényeket keresni, tekintse át az alábbi táblázatokat. Az egyes függvényekről az [ábécésorrendben](#alphabetical-list)is részletesen ismertthetjük a függvényeket.

> [!NOTE]
> A paraméterdefiníciók szintaxisában a paraméter után megjelenő kérdőjel (?) azt jelenti, hogy a paraméter nem kötelező.
> Lásd például a [GetFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Függvények kifejezésekben

Ha meg szeretné mutatni, hogyan kell egy függvényt használni egy `customerName` kifejezésben, ez a `accountName` példa bemutatja, hogyan szerezheti be az értéket a paraméterből, és hogyan rendelheti hozzá az értéket a tulajdonsághoz a [parameters()](#parameters) függvény használatával egy kifejezésben:

```json
"accountName": "@parameters('customerName')"
```

Az alábbiakban a függvények kifejezésekben való használatának további általános módjait olvashatja:

| Tevékenység | Függvény szintaxisa kifejezésben |
| ---- | -------------------------------- |
| Az adott elem függvénynek való átadásával végezze el a munkát egy cikkel. | "\@<*functionName*>(<*elem*>)" |
| 1. A *parameterName*'s érték beszerezhető `parameters()` függvény használatával. </br>2. Végezze el a munkát az eredménnyel az érték átadásával *a functionName*. | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1. Az eredmény bekésezése a beágyazott belső *függvénybőlNév*. </br>2. Adja át az eredményt a külső *függvényname2*függvénynek . | "\@<*functionName2*>(<*függvényNév*>(<*elem*>))" |
| 1. Az eredmény a *functionName .* </br>2. Tekintettel arra, hogy az eredmény egy tulajdonságtulajdonsággal rendelkező *objektum,* a tulajdonság értékét kapjuk meg. | "\@<*functionName*>(<*elem*>.<*tulajdonságNév*>" |
|||

A `concat()` függvény például két vagy több karakterlánc-értéket vehet fel paraméterként. Ez a függvény ezeket a karakterláncokat egyetlen karakterláncba egyesíti.
Akkor sem adja át a string konstansok, például a "Sophia" és az "Owen", így kap egy kombinált string, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Vagy lekaphatkarakterlánc-értékeket a paraméterekből. Ez a `parameters()` példa az `concat()` egyes `firstName` paraméterek `lastName` ben a függvényt, valamint a paramétereket és paramétereket használja. Ezután átadja az eredményül `concat()` kapott karakterláncokat a függvénynek, így egy kombinált karakterláncot kap, például "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Akárhogy is, mindkét példa `customerName` hozzárendeli az eredményt a tulajdonsághoz.

Itt vannak a rendelkezésre álló funkciók szerint az általános célra, vagy böngészhet a funkciók alapján [ábécésorrendben](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Sztringfüggvények

A karakterláncokkal való munkához használhatja ezeket a karakterlánc-függvényeket és néhány [gyűjteményfüggvényt](#collection-functions)is.
A karakterlánc-függvények csak karakterláncokon működnek.

| Karakterlánc függvény | Tevékenység |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Két vagy több karakterlánc egyesítése és a kombinált karakterlánc visszaadása. |
| [végződikWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Ellenőrizze, hogy egy karakterlánc a megadott karakterlánccal végződik-e. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Szám visszaadása karakterláncként a megadott formátum alapján |
| [Guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Globálisan egyedi azonosító (GUID) létrehozása karakterláncként. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Adja vissza a részkarakterlánc kezdőpozícióját. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Adja vissza a részkarakterlánc utolsó előfordulásának kezdő pozícióját. |
| [Helyettesít](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Cseréljen le egy részkarakterláncot a megadott karakterláncra, és adja vissza a frissített karakterláncot. |
| [felosztás](../logic-apps/workflow-definition-language-functions-reference.md#split) | Olyan tömböt ad vissza, amely vesszővel elválasztva részkarakterláncokat tartalmaz egy nagyobb karakterlánctól, amely az eredeti karakterlánc megadott határoló karakterén alapul. |
| [kezdődik](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Ellenőrizze, hogy egy karakterlánc egy adott karakterlánccal kezdődik-e. |
| [Substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Karakterlánc karaktereit adja vissza a megadott pozíciótól kezdve. |
| [lassabbra](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Karakterlánc visszaadása kisbetűs formátumban. |
| [toUpper (felső](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Nagybetűs karakterlánc visszaadása. |
| [Berendezés](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Távolítsa el a sortávolság és a záró szóközt egy karakterláncból, és adja vissza a frissített karakterláncot. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Gyűjtési függvények

A gyűjteményekkel, általában tömbökkel, karakterláncokkal és néha szótárakkal való munkához használhatja ezeket a gyűjteményfüggvényeket.

| Begyűjtési funkció | Tevékenység |
| ------------------- | ---- |
| [Tartalmaz](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Ellenőrizze, hogy egy gyűjteményrendelkezik-e egy adott cikkel. |
| [Üres](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Ellenőrizze, hogy a gyűjtemény üres-e. |
| [Első](../logic-apps/workflow-definition-language-functions-reference.md#first) | Az első elem visszaküldése egy gyűjteményből. |
| [Kereszteződés](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Olyan gyűjteményt ad vissza, amely *csak* a megadott gyűjteményekben lévő közös elemeket tartalmaz. |
| [Cikk](../logic-apps/workflow-definition-language-functions-reference.md#item) | Ha egy tömbön keresztül ismétlődő műveleten belül van, a művelet aktuális iterációja során adja vissza az aktuális elemet a tömbben. |
| [csatlakozás](../logic-apps/workflow-definition-language-functions-reference.md#join) | Olyan karakterláncot ad vissza, amely egy tömb *összes* elemét a megadott karakterrel elválasztva adja vissza. |
| [Utolsó](../logic-apps/workflow-definition-language-functions-reference.md#last) | Az utolsó elem visszaküldése egy gyűjteményből. |
| [Hossza](../logic-apps/workflow-definition-language-functions-reference.md#length) | Karakterláncban vagy tömbben lévő elemek számát adja vissza. |
| [Ugrál](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Távolítsa el az elemeket a gyűjtemény elejéről, és küldje vissza *az összes többi* elemet. |
| [venni](../logic-apps/workflow-definition-language-functions-reference.md#take) | Cikkek visszaküldése a gyűjtemény elejéről. |
| [Unió](../logic-apps/workflow-definition-language-functions-reference.md#union) | Olyan gyűjteményt ad vissza, amely a megadott gyűjteményből származó *összes* elemet tartalmaz. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logikai összehasonlító függvények

A feltételek kel való munkához, az értékek és a kifejezéseredmények összehasonlításához vagy a különböző típusú logikai értékek kiértékeléséhez használhatja ezeket a logikai összehasonlító függvényeket.
Az egyes függvényekkel kapcsolatos teljes körű hivatkozást az [alfabetikus listában](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)tartalmazza.

| Logikai összehasonlítási függvény | Tevékenység |
| --------------------------- | ---- |
| [és](../logic-apps/workflow-definition-language-functions-reference.md#and) | Ellenőrizze, hogy minden kifejezés igaz-e. |
| [egyenlő](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Ellenőrizze, hogy mindkét érték egyenértékű-e. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Ellenőrizze, hogy az első érték nagyobb-e, mint a második érték. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Ellenőrizze, hogy az első érték nagyobb vagy egyenlő-e a második értékkel. |
| [Ha](../logic-apps/workflow-definition-language-functions-reference.md#if) | Ellenőrizze, hogy egy kifejezés igaz vagy hamis. Az eredmény alapján adja vissza a megadott értéket. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Ellenőrizze, hogy az első érték kisebb-e, mint a második érték. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Ellenőrizze, hogy az első érték kisebb vagy egyenlő-e a második értékkel. |
| [Nem](../logic-apps/workflow-definition-language-functions-reference.md#not) | Ellenőrizze, hogy egy kifejezés hamis-e. |
| [Vagy](../logic-apps/workflow-definition-language-functions-reference.md#or) | Ellenőrizze, hogy legalább egy kifejezés igaz-e. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Konverziós függvények

Egy érték típusának vagy formátumának módosításához használhatja ezeket a konverziós függvényeket.
Módosíthatja például a logikai értékről egész számra.
Ha többet szeretne tudni arról, hogy a Logic Apps hogyan kezeli a tartalomtípusokat a konvertálás során, [olvassa el a Tartalomtípusok kezelése](../logic-apps/logic-apps-content-type.md)című témakört.
Az egyes függvényekkel kapcsolatos teljes körű hivatkozást az [alfabetikus listában](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)tartalmazza.

| Konverziófüggvény | Tevékenység |
| ------------------- | ---- |
| [tömb](../logic-apps/workflow-definition-language-functions-reference.md#array) | Egyetlen megadott bemenetből ad vissza tömböt. Több bemenetesetén lásd: [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Adja vissza a karakterlánc base64 kódolású verzióját. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Base64 kódolású karakterlánc bináris verzióját adja vissza. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Egy base64 kódolású karakterlánc-verziót adja vissza. |
| [Bináris](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Adja vissza a bemeneti érték bináris verzióját. |
| [Bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Adja vissza a logikai verziót egy bemeneti értékhez. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Tömb visszaadása több bemenetből. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Adja vissza a bemeneti érték adatURI-értékét. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | AdatURI bináris verzióját adja vissza. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | AdatURI karakterlánc-verzióját adja vissza. |
| [dekódbase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Egy base64 kódolású karakterlánc-verziót adja vissza. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | AdatURI bináris verzióját adja vissza. |
| [dekódurikomponens](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Visszaad egy karakterláncot, amely az escape karaktereket dekódolt verziókkal helyettesíti. |
| [kódolásuriösszetevő](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Olyan karakterláncot ad vissza, amely az URL-ben nem biztonságos karaktereket escape-karakterekre cseréli. |
| [lebegőpontos](../logic-apps/workflow-definition-language-functions-reference.md#float) | Lebegőpontos számot ad vissza egy bemeneti értékhez. |
| [Int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Adja vissza egy karakterlánc egész verzióját. |
| [Json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Adja vissza a JavaScript-objektumnotika (JSON) típusú értéket vagy objektumot egy karakterlánchoz vagy XML-hez. |
| [sztring](../logic-apps/workflow-definition-language-functions-reference.md#string) | Adja vissza egy bemeneti érték karakterlánc-verzióját. |
| [uriKomponens](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Adja vissza a bemeneti érték URI-kódolású verzióját úgy, hogy az URL-nem biztonságos karaktereket escape karakterekre cseréli. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Adja vissza egy URI-kódolású karakterlánc bináris verzióját. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Adja vissza egy URI-kódolású karakterlánc karakterlánc-verzióját. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Karakterlánc XML-verzióját adja vissza. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Matematikai függvények

Az egész számokkal és úszókkal való munkához használhatja ezeket a matematikai függvényeket.
Az egyes függvényekkel kapcsolatos teljes körű hivatkozást az [alfabetikus listában](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)tartalmazza.

| Matematikai függvény | Tevékenység |
| ------------- | ---- |
| [Hozzáadása](../logic-apps/workflow-definition-language-functions-reference.md#add) | Két szám hozzáadásának eredményét adja vissza. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Két szám felosztásának eredményét adja vissza. |
| [Max](../logic-apps/workflow-definition-language-functions-reference.md#max) | A legnagyobb értéket adja vissza egy számhalmazból vagy tömbből. |
| [Min](../logic-apps/workflow-definition-language-functions-reference.md#min) | A legkisebb értéket adja vissza egy számhalmazból vagy tömbből. |
| [Mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Adja vissza a maradékot két szám felosztásából. |
| [Mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Tegye vissza a terméket két szám szorzatából. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Egy megadott tartományból származó véletlenszerű egész szám visszaadása. |
| [Tartomány](../logic-apps/workflow-definition-language-functions-reference.md#range) | Adott egész számból induló egész tömbet ad vissza. |
| [Al](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Adja vissza a második szám kivonásának eredményét az első számból. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Dátum és időpont függvényei

A dátumok és időpontok használatához használhatja ezeket a dátum- és időfüggvényeket.
Az egyes függvényekkel kapcsolatos teljes körű hivatkozást az [alfabetikus listában](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)tartalmazza.

| Dátum- vagy időfüggvény | Tevékenység |
| --------------------- | ---- |
| [addDays (nap)](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adjon hozzá napokat az időbélyeghez. |
| [addHours (Órák)](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adjon hozzá néhány órát az időbélyeghez. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adjon hozzá néhány percet az időbélyeghez. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adjon hozzá néhány másodpercet az időbélyeghez. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Időmértékegységek hozzáadása az időbélyeghez. Lásd még: [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Időbélyeg konvertálása világidőkoordinált (UTC) értékből a célidőzónába. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Időbélyeg konvertálása a forrás időzónából a célidőzónába. |
| [konvertálásToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Időbélyeg konvertálása a forrásidőzónából világidő-koordinált (UTC) értékké. |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | A hónap napjának az időbélyegből adja vissza. |
| [napOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | A hét napjának az összetevőt egy időbélyegből adja vissza. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Az év napjának eredményest egy időbélyegből adja vissza. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Adja vissza a dátumot egy időbélyegből. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Adja vissza az aktuális időbélyeget és a megadott időegységeket. Lásd [még: addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Az aktuális időbélyeget adja vissza a megadott időmértékekkel csökkentve. Lásd még: [kivonásFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Adja vissza a nap kezdetét egy időbélyeghez. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Adja vissza az óra kezdetét egy időbélyeghez. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Adja vissza a hónap kezdetét egy időbélyeghez. |
| [kivonásAz időből](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Több időegység kivonása az időbélyegből. Lásd [még: getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [Kullancsok](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Adja `ticks` vissza a megadott időbélyeg tulajdonságértékét. |
| [utcNow között](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Adja vissza az aktuális időbélyeget karakterláncként. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Munkafolyamat-függvények

Ezek a munkafolyamat-függvények a következőkben segíthetnek:

* Részletek a munkafolyamat-példányfutási időben.
* A logikai alkalmazások vagy folyamatok példányosítására használt bemenetekkel dolgozhat.
* Hivatkozzon az eseményindítók és műveletek kimeneteire.

Hivatkozhat például egy művelet kimenetére, és ezeket az adatokat egy későbbi műveletben használhatja.
Az egyes függvényekkel kapcsolatos teljes körű hivatkozást az [alfabetikus listában](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)tartalmazza.

| Munkafolyamat-függvény | Tevékenység |
| ----------------- | ---- |
| [Akció](../logic-apps/workflow-definition-language-functions-reference.md#action) | Adja vissza az aktuális művelet kimenetét futásidőben, vagy más JSON név- és értékpárok értékeit. Lásd még: [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Egy művelet kimenetének visszaadása `body` futásidőben. Lásd [még: test](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs (Műveletkimenetek)](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Egy művelet kimenetének visszaadása futásidőben. Lásd: [kimenetek](../logic-apps/workflow-definition-language-functions-reference.md#outputs) és [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [Műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Egy művelet kimenetét adja vissza futásidőben, vagy más JSON-név- és értékpárok értékeit. Lásd [még: művelet](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [Szervezet](#body) | Egy művelet kimenetének visszaadása `body` futásidőben. Lásd [még: actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Hozzon létre egy tömböt *azokkal* az értékekkel, amelyek megfelelnek az űrlapadatok vagy *az űrlapkódolású* műveletkimenetek kulcsnevének. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Egyetlen olyan értéket ad vissza, amely megegyezik egy kulcsnévvel a művelet *űrlapadataiban* vagy *űrlapkódolású kimenetében.* |
| [Cikk](../logic-apps/workflow-definition-language-functions-reference.md#item) | Ha egy tömbön keresztül ismétlődő műveleten belül van, a művelet aktuális iterációja során adja vissza az aktuális elemet a tömbben. |
| [Elemek](../logic-apps/workflow-definition-language-functions-reference.md#items) | Ha egy Foreach vagy Until cikluson belül van, adja vissza az aktuális elemet a megadott ciklusból.|
| [iterációIndexek](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Ha egy Until cikluson belül van, adja vissza az aktuális iteráció indexértékét. Használhatja ezt a függvényt belül beágyazott Ig hurkok. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Adja vissza az eseményindítót vagy műveletet megindítót vagy műveletet megindítót megindítót vagy műveletet megindítót meghívó "visszahívási URL-címet". |
| [többrészes test](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Adja vissza a törzsegy adott része egy művelet kimenet, amely több részből áll. |
| [Kimenetek](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Egy művelet kimenetének visszaadása futásidőben. |
| [Paraméterek](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Adja vissza a munkafolyamat-definícióban ismertetett paraméter értékét. |
| [Eredmény](../logic-apps/workflow-definition-language-functions-reference.md#result) | A megadott hatókörrel kapcsolatos műveleten belüli összes művelet bemenetét `For_each`és `Until`kimenetét adja vissza, például a , és `Scope`a. |
| [Ravaszt](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Adja vissza az eseményindító kimenetét futásidőben, vagy más JSON név- és értékpárokból. Lásd még: [triggerOutputs](#triggerOutputs) és [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Az eseményindító `body` kimenetének visszaadása futásidőben. Lásd az [eseményindítót.](../logic-apps/workflow-definition-language-functions-reference.md#trigger) |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Egyetlen olyan értéket ad vissza, amely megfelel egy kulcsnévnek *az űrlapadatokban* vagy *az űrlapkódolású* eseményindító kimenetekben. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Adja vissza a testet egy adott alkatrészhez az eseményindító többrészes kimenetében. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Hozzon létre egy tömböt, amelynek értékei megegyeznek egy kulcsnévvel *az űrlapadatokban* vagy *az űrlapkódolású* eseményindító kimenetekben. |
| [triggerKimenetek](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Adja vissza az eseményindító kimenetét futásidőben, vagy más JSON-név- és értékpárok értékeit. Lásd az [eseményindítót.](../logic-apps/workflow-definition-language-functions-reference.md#trigger) |
| [Változók](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Adja vissza egy megadott változó értékét. |
| [Munkafolyamat](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | A munkafolyamat összes részletének visszaadása a futási idő alatt. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI-elemzési függvények

Az egységes erőforrás-azonosítókkal (URI-k) való munkához és az URI-k különböző tulajdonságértékeinek leküzdéséhez használhatja ezeket az URI-elemzési függvényeket.
Az egyes függvényekkel kapcsolatos teljes körű hivatkozást az [alfabetikus listában](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)tartalmazza.

| URI-elemzési függvény | Tevékenység |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Adja `host` vissza az egységes erőforrás-azonosító (URI) értékét. |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Adja `path` vissza az egységes erőforrás-azonosító (URI) értékét. |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Az `path` egységes `query` erőforrás-azonosító (URI) és értékeinek visszaadása. |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Adja `port` vissza az egységes erőforrás-azonosító (URI) értékét. |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Adja `query` vissza az egységes erőforrás-azonosító (URI) értékét. |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Adja `scheme` vissza az egységes erőforrás-azonosító (URI) értékét. |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulációs függvények: JSON & XML

A JSON-objektumok és az XML-csomópontok használatához használhatja ezeket a manipulációs függvényeket.
Az egyes függvényekkel kapcsolatos teljes körű hivatkozást az [alfabetikus listában](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)tartalmazza.

| Manipuláció sfunkció | Tevékenység |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Adjon hozzá egy tulajdonságot és annak értékét vagy név-érték párját egy JSON-objektumhoz, és adja vissza a frissített objektumot. |
| [összeolvad](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Adja vissza az első nem null értéket egy vagy több paraméterből. |
| [removeProperty tulajdonság](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Tulajdonság eltávolítása JSON-objektumból, és adja vissza a frissített objektumot. |
| [setProperty tulajdonság](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Állítsa be egy JSON-objektum tulajdonságának értékét, és adja vissza a frissített objektumot. |
| [Xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Ellenőrizze az XML-ben az XPath (XML-elérési út nyelve) kifejezésnek megfelelő csomópontokat vagy értékeket, és adja vissza a megfelelő csomópontokat vagy értékeket. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Minden funkció - betűrendes lista

Ez a szakasz az összes rendelkezésre álló függvényt betűrendben sorolja fel.

<a name="action"></a>

### <a name="action"></a>action

Adja vissza az *aktuális* művelet kimenetét futásidőben, vagy más JSON név- és értékpárok értékeit, amelyeket egy kifejezéshez rendelhet.
Alapértelmezés szerint ez a függvény a teljes műveletobjektumra hivatkozik, de tetszés szerint megadhat egy olyan tulajdonságot, amelynek értékét szeretné.
Lásd [még: actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

A függvényt `action()` csak ezeken a helyeken használhatja:

* A `unsubscribe` webhook-művelet tulajdonsága, így az eredeti `subscribe` kérelem ből származó eredmény elérhető
* A `trackedProperties` művelet tulajdonsága
* A `do-until` művelet hurokfeltétele

```
action()
action().outputs.body.<property>
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Tulajdonság*> | Nem | Sztring | Annak a műveletobjektumnak a neve, amelynek értékét meg szeretné adni: **név**, **startTime**, **endTime**, **bemenetek**, **kimenetek**, **állapot**, **kód**, **követésazonosító**és **clientTrackingId**. Az Azure Portalon ezeket a tulajdonságokat egy adott futtatási előzmények részleteinek áttekintésével találhatja meg. További információ: [REST API - Workflow Run Actions](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*művelet-kimenet*> | Sztring | Az aktuális művelet vagy tulajdonság kimenete |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Egy művelet kimenetének visszaadása `body` futásidőben.
A rövidítése. `actions('<actionName>').outputs.body`
Lásd: [body()](#body) és [actions()](#actions).

```
actionBody('<actionName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (műveletneve)*> | Igen | Sztring | A művelet kívánt kimenetének `body` neve |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*akció-test-kimenet*> | Sztring | A `body` megadott művelet kimenete |
||||

*Példa*

Ez a `body` példa a Twitter-művelet kimenetét kapja: `Get user`

```
actionBody('Get_user')
```

És ezt az eredményt adja vissza:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs (Műveletkimenetek)

Egy művelet kimenetének visszaadása futásidőben.  és a gyorsírás . `actions('<actionName>').outputs` Lásd: [actions()](#actions). A `actionOutputs()` függvény a `outputs()` Logic App Designer ben oldódik meg, ezért `actionOutputs()`fontolja meg [a outputok()](#outputs)használatát a helyett. Bár mindkét funkció ugyanúgy `outputs()` működik, előnyben kell részesíteni.

```
actionOutputs('<actionName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (műveletneve)*> | Igen | Sztring | A művelet kívánt kimenetének neve |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*Kimeneti*> | Sztring | A megadott művelet kimenete |
||||

*Példa*

Ez a példa a Twitter-művelet kimenetét kapja: `Get user`

```
actionOutputs('Get_user')
```

És ezt az eredményt adja vissza:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>műveletek

Egy művelet kimenetét adja vissza futásidőben, vagy más JSON-név- és értékpárok értékeit, amelyeket egy kifejezéshez rendelhet. Alapértelmezés szerint a függvény a teljes műveletobjektumra hivatkozik, de tetszés szerint megadhatja azt a tulajdonságot, amelynek értékét szeretné.
A gyorsírási változatokat lásd: [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)és [body()](#body).
Az aktuális műveletről [lásd: action()](#action).

> [!NOTE]
> Korábban a `actions()` függvényt vagy `conditions` az elemet is használhatja annak megadásakor, hogy egy művelet egy másik művelet kimenete alapján futott. A műveletek közötti explicit függőségek deklarálnia azonban most `runAfter` a függő művelet tulajdonságát kell használnia.
> Ha többet szeretne `runAfter` megtudni a tulajdonságról, olvassa [el a Futtatás tulajdonsággal kapcsolatos hibák befogása és kezelése című témakört.](../logic-apps/logic-apps-workflow-definition-language.md)

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (műveletneve)*> | Igen | Sztring | Annak a műveletobjektumnak a neve, amelynek kimenetét  |
| <*Tulajdonság*> | Nem | Sztring | Annak a műveletobjektumnak a neve, amelynek értékét meg szeretné adni: **név**, **startTime**, **endTime**, **bemenetek**, **kimenetek**, **állapot**, **kód**, **követésazonosító**és **clientTrackingId**. Az Azure Portalon ezeket a tulajdonságokat egy adott futtatási előzmények részleteinek áttekintésével találhatja meg. További információ: [REST API - Workflow Run Actions](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*művelet-kimenet*> | Sztring | A megadott művelet vagy tulajdonság kimenete |
||||

*Példa*

Ez a `status` példa leveszi a `Get user` tulajdonság értékét a Twitter művelet futásidőben:

```
actions('Get_user').outputs.body.status
```

És ezt az eredményt adja vissza:`"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Két szám hozzáadásának eredményét adja vissza.

```
add(<summand_1>, <summand_2>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*> summand_1* *summand_2 <.*> | Igen | Egész, úszó vagy kevert | A hozzáadandó számok |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*eredmény-összeg*> | Egész vagy úszó | A megadott számok hozzáadásának eredménye |
||||

*Példa*

Ez a példa hozzáadja a megadott számokat:

```
add(1, 1.5)
```

És ezt az eredményt adja vissza:`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays (nap)

Adjon hozzá napokat az időbélyeghez.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*Nap*> | Igen | Egész szám | A hozzáadandó napok pozitív vagy negatív száma |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú nap  |
||||

*1. példa*

Ez a példa 10 napot ad hozzá a megadott időbélyeghez:

```
addDays('2018-03-15T13:00:00Z', 10)
```

És ezt az eredményt adja vissza:`"2018-03-25T00:00:0000000Z"`

*2. példa*

Ez a példa öt napot von ki a megadott időbélyegből:

```
addDays('2018-03-15T00:00:00Z', -5)
```

És ezt az eredményt adja vissza:`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours (Órák)

Adjon hozzá néhány órát az időbélyeghez.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*Óra*> | Igen | Egész szám | A hozzáadandó órák pozitív vagy negatív száma |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott óraszám  |
||||

*1. példa*

Ez a példa 10 órát ad hozzá a megadott időbélyeghez:

```
addHours('2018-03-15T00:00:00Z', 10)
```

És ezt az eredményt adja vissza:`"2018-03-15T10:00:0000000Z"`

*2. példa*

Ez a példa öt órát von ki a megadott időbélyegből:

```
addHours('2018-03-15T15:00:00Z', -5)
```

És ezt az eredményt adja vissza:`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adjon hozzá néhány percet az időbélyeghez.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*Perc*> | Igen | Egész szám | A hozzáadni időszámú pozitív vagy negatív számú perc |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú perc |
||||

*1. példa*

Ez a példa 10 percet ad hozzá a megadott időbélyeghez:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

És ezt az eredményt adja vissza:`"2018-03-15T00:20:00.0000000Z"`

*2. példa*

Ez a példa öt percet von ki a megadott időbélyegből:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

És ezt az eredményt adja vissza:`"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Adjon hozzá egy tulajdonságot és annak értékét vagy név-érték párját egy JSON-objektumhoz, és adja vissza a frissített objektumot. Ha a tulajdonság már létezik futásidőben, a függvény meghibásodik, és hibát jelez.

```
addProperty(<object>, '<property>', <value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | Az a JSON-objektum, amelyhez tulajdonságot szeretne hozzáadni |
| <*Tulajdonság*> | Igen | Sztring | A hozzáadni hozandó tulajdonság neve |
| <*Érték*> | Igen | Bármelyik | Az ingatlan értéke |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített objektum*> | Objektum | A frissített JSON-objektum a megadott tulajdonsággal |
||||

Ha gyermektulajdonságot szeretne hozzáadni egy meglévő tulajdonsághoz, használja ezt a szintaxist:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | Az a JSON-objektum, amelyhez tulajdonságot szeretne hozzáadni |
| <*szülő-tulajdonság*> | Igen | Sztring | Annak a szülőtulajdonságnak a neve, ahová hozzá szeretné adni a gyermektulajdonságot |
| <*gyermek-tulajdonság*> | Igen | Sztring | A hozzáadni hozandó gyermektulajdonság neve |
| <*Érték*> | Igen | Bármelyik | A megadott tulajdonsághoz beállítandó érték |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített objektum*> | Objektum | A frissített JSON-objektum, amelynek tulajdonságát beállította |
||||

*1. példa*

Ez a `middleName` példa hozzáadja a tulajdonságot egy JSON-objektumhoz, amely a [JSON()](#json) függvény használatával karakterláncból JSON-ná alakul át. Az objektum már `firstName` `surName` tartalmazza a és a tulajdonságokat. A függvény hozzárendeli a megadott értéket az új tulajdonsághoz, és visszaadja a frissített objektumot:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Az aktuális JSON-objektum:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Itt a frissített JSON objektum:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*2. példa*

Ez a `middleName` példa hozzáadja `customerName` a gyermektulajdonságot egy JSON-objektum meglévő tulajdonságához, amely a [JSON()](#json) függvény használatával karakterláncból JSON-ná alakul át. A függvény hozzárendeli a megadott értéket az új tulajdonsághoz, és visszaadja a frissített objektumot:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Az aktuális JSON-objektum:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Itt a frissített JSON objektum:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Adjon hozzá néhány másodpercet az időbélyeghez.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*Másodperc*> | Igen | Egész szám | A hozzáadni a hozzáadni a pozitív vagy negatív számú másodperc |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú másodperc  |
||||

*1. példa*

Ez a példa 10 másodpercet ad hozzá a megadott időbélyeghez:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

És ezt az eredményt adja vissza:`"2018-03-15T00:00:10.0000000Z"`

*2. példa*

Ez a példa öt másodpercet von ki a megadott időbélyegből:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

És ezt az eredményt adja vissza:`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Időmértékegységek hozzáadása az időbélyeghez.
Lásd még: [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*Intervallum*> | Igen | Egész szám | A hozzáadni hozandó időegységek száma |
| <*timeUnit (időegység)*> | Igen | Sztring | Az *intervallummal*használandó időegység: "Második", "Perc", "Óra", "Nap", "Hét", "Hónap", "Év" |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott időegységek száma  |
||||

*1. példa*

Ez a példa egy napot ad hozzá a megadott időbélyeghez:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

És ezt az eredményt adja vissza:`"2018-01-02T00:00:00.0000000Z"`

*2. példa*

Ez a példa egy napot ad hozzá a megadott időbélyeghez:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

És az eredményt az opcionális "D" formátumban adja vissza:`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>és

Ellenőrizze, hogy minden kifejezés igaz-e.
Igaz értéket ad vissza, ha minden kifejezés igaz, vagy hamis értéket ad vissza, ha legalább egy kifejezés hamis.

```
and(<expression1>, <expression2>, ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*kifejezés1*>, <*kifejezés2*>, ... | Igen | Logikai | Az ellenőrizandó kifejezések |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| true (igaz) vagy false (hamis) | Logikai | Igaz értéket ad vissza, ha minden kifejezés igaz. Hamis értéket ad vissza, ha legalább egy kifejezés hamis. |
||||

*1. példa*

Ezek a példák azt ellenőrzik, hogy a megadott logikai értékek igazak-e:

```
and(true, true)
and(false, true)
and(false, false)
```

És visszaadja ezeket az eredményeket:

* Első példa: Mindkét kifejezés igaz, `true`így visszatér .
* Második példa: Egy kifejezés hamis, így visszatér `false`.
* Harmadik példa: Mindkét kifejezés hamis, `false`így visszatér .

*2. példa*

Ezek a példák azt ellenőrzik, hogy a megadott kifejezések igazak-e:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

És visszaadja ezeket az eredményeket:

* Első példa: Mindkét kifejezés igaz, `true`így visszatér .
* Második példa: Egy kifejezés hamis, így visszatér `false`.
* Harmadik példa: Mindkét kifejezés hamis, `false`így visszatér .

<a name="array"></a>

### <a name="array"></a>tömb

Egyetlen megadott bemenetből ad vissza tömböt.
Több bemenetesetén lásd: [createArray()](#createArray).

```
array('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Tömb létrehozásának karakterlánca |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*érték*>] | Tömb | Az egyetlen megadott bemenetet tartalmazó tömb |
||||

*Példa*

Ez a példa létrehoz egy tömböt a "hello" karakterláncból:

```
array('hello')
```

És ezt az eredményt adja vissza:`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Adja vissza a karakterlánc base64 kódolású verzióját.

```
base64('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A bemeneti karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*base64-karakterlánc*> | Sztring | A bemeneti karakterlánc base64 kódolású verziója |
||||

*Példa*

Ez a példa a "hello" karakterláncot base64 kódolású karakterlánccá alakítja:

```
base64('hello')
```

És ezt az eredményt adja vissza:`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Base64 kódolású karakterlánc bináris verzióját adja vissza.

```
base64ToBinary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A konvertálandó base64 kódolású karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*bináris-base64-string*> | Sztring | A base64 kódolású karakterlánc bináris verziója |
||||

*Példa*

Ez a példa az "aGVsbG8=" base64 kódolású karakterláncot bináris karakterlánccá alakítja:

```
base64ToBinary('aGVsbG8=')
```

És ezt az eredményt adja vissza:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Adja vissza a base64 kódolású karakterlánc-verziót, hatékonyan dekódolva a base64 karakterláncot.
Ezt a függvényt használja a [dekódoljabase64() helyett.](#decodeBase64)
Bár mindkét funkció ugyanúgy `base64ToString()` működik, előnyben kell részesíteni.

```
base64ToString('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A dekódolandó base64 kódolású karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolt-base64-karakterlánc*> | Sztring | Base64 kódolású karakterlánc-verzió |
||||

*Példa*

Ez a példa az "aGVsbG8=" base64 kódolású karakterláncot csak egy karakterláncká alakítja át:

```
base64ToString('aGVsbG8=')
```

És ezt az eredményt adja vissza:`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Adja vissza egy karakterlánc bináris verzióját.

```
binary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A konvertálandó karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*bináris-bemeneti érték*> | Sztring | A megadott karakterlánc bináris verziója |
||||

*Példa*

Ez a példa a "hello" karakterláncot bináris karakterlánccá alakítja:

```
binary('hello')
```

És ezt az eredményt adja vissza:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body (Törzs)

Egy művelet kimenetének visszaadása `body` futásidőben.
A rövidítése. `actions('<actionName>').outputs.body`
Lásd [actionBody()](#actionBody) és [actions()](#actions).

```
body('<actionName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (műveletneve)*> | Igen | Sztring | A művelet kívánt kimenetének `body` neve |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*akció-test-kimenet*> | Sztring | A `body` megadott művelet kimenete |
||||

*Példa*

Ez a `body` példa a `Get user` Twitter-művelet kimenetét kapja meg:

```
body('Get_user')
```

És ezt az eredményt adja vissza:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>Bool

Egy érték logikai változatának visszaadása.

```
bool(<value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Bármelyik | A konvertálandó érték |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | A megadott érték logikai verziója |
||||

*Példa*

Ezek a példák a megadott értékeket logikai értékekké alakítják:

```
bool(1)
bool(0)
```

És visszaadja ezeket az eredményeket:

* Első példa:`true`
* Második példa:`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>összeolvad

Adja vissza az első nem null értéket egy vagy több paraméterből.
Az üres karakterláncok, üres tömbök és üres objektumok nem null értékűek.

```
coalesce(<object_1>, <object_2>, ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, *<object_2*>, ... | Igen | Bármilyen, lehet keverni típusok | Null érték ellenőrzéséhez szükséges elem vagy több elem |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*első nem null elem*> | Bármelyik | Az első elem vagy érték, amely nem null értékű. Ha az összes paraméter null értékű, ez a függvény null értéket ad vissza. |
||||

*Példa*

Ezek a példák a megadott értékek első nem null értékét adják vissza, vagy null értéket, ha az összes érték null értékű:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

És visszaadja ezeket az eredményeket:

* Első példa:`true`
* Második példa:`"hello"`
* Harmadik példa:`null`

<a name="concat"></a>

### <a name="concat"></a>Concat

Két vagy több karakterlánc egyesítése és a kombinált karakterlánc visszaadása.

```
concat('<text1>', '<text2>', ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Igen | Sztring | Legalább két húr kombinálható |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Sztring | A kombinált bemeneti karakterláncokból létrehozott karakterlánc |
||||

*Példa*

Ez a példa a "Hello" és a "World" karakterláncokat egyesíti:

```
concat('Hello', 'World')
```

És ezt az eredményt adja vissza:`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>tartalmazza a következőt:

Ellenőrizze, hogy egy gyűjteményrendelkezik-e egy adott cikkel.
Igaz értéket ad vissza, ha az elemet megtalálják, vagy hamis értéket ad vissza, ha nem található.
Ez a funkció nem imitot.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Pontosabban, ez a funkció működik az alábbi gyűjtemény típusok:

* *Karakterlánc-részkarakterlánc* keresésére használt *karakterlánc*
* *Érték* keresésére a *tömb*
* Kulcs keresésére *a* *szótár*

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Karakterlánc, tömb vagy szótár | Az ellenőrizandó gyűjtemény |
| <*Érték*> | Igen | String, Array vagy Dictionary, illetve | A megkeresandó elem |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | Igaz értéket ad vissza, ha az elem megtalálható. Hamis értéket ad vissza, ha nem található. |
||||

*1. példa*

Ez a példa ellenőrzi a "hello world" karakterláncot a "világ" karakterlánchoz, és igaz értéket ad vissza:

```
contains('hello world', 'world')
```

*2. példa*

Ez a példa ellenőrzi a "hello world" karakterláncot az "univerzum" karakterláncrészhez, és hamis értéket ad vissza:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Időbélyeg konvertálása világidőkoordinált (UTC) értékből a célidőzónába.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*destinationTimeZone*> | Igen | Sztring | A célidőzóna neve. Az időzóna-nevekről a [Microsoft Időzóna indexértékei](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)című témakörben találja, de előfordulhat, hogy az írásjeleket el kell távolítania az időzóna nevéből. |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*konvertált időbélyeg*> | Sztring | A célidőzónára konvertált időbélyeg |
||||

*1. példa*

Ez a példa egy időbélyeget konvertál a megadott időzónába:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

És ezt az eredményt adja vissza:`"2018-01-01T00:00:00.0000000"`

*2. példa*

Ez a példa átalakítja az időbélyeget a megadott időzónára és formátumra:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

És ezt az eredményt adja vissza:`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Időbélyeg konvertálása a forrás időzónából a célidőzónába.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*sourceTimeZone*> | Igen | Sztring | A forrásidőzóna neve. Az időzóna-nevekről a [Microsoft Időzóna indexértékei](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)című témakörben találja, de előfordulhat, hogy az írásjeleket el kell távolítania az időzóna nevéből. |
| <*destinationTimeZone*> | Igen | Sztring | A célidőzóna neve. Az időzóna-nevekről a [Microsoft Időzóna indexértékei](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)című témakörben találja, de előfordulhat, hogy az írásjeleket el kell távolítania az időzóna nevéből. |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*konvertált időbélyeg*> | Sztring | A célidőzónára konvertált időbélyeg |
||||

*1. példa*

Ez a példa a forrásidőzónát a célidőzónává alakítja:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

És ezt az eredményt adja vissza:`"2018-01-01T00:00:00.0000000"`

*2. példa*

Ez a példa az időzónát a megadott időzónára és formátumra konvertálja:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

És ezt az eredményt adja vissza:`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>konvertálásToUtc

Időbélyeg konvertálása a forrásidőzónából világidő-koordinált (UTC) értékké.

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*sourceTimeZone*> | Igen | Sztring | A forrásidőzóna neve. Az időzóna-nevekről a [Microsoft Időzóna indexértékei](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)című témakörben találja, de előfordulhat, hogy az írásjeleket el kell távolítania az időzóna nevéből. |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*konvertált időbélyeg*> | Sztring | UtC-re konvertált időbélyeg |
||||

*1. példa*

Ez a példa egy időbélyeget UTC-vé konvertál:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

És ezt az eredményt adja vissza:`"2018-01-01T08:00:00.0000000Z"`

*2. példa*

Ez a példa egy időbélyeget UTC-vé konvertál:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

És ezt az eredményt adja vissza:`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Tömb visszaadása több bemenetből.
Egyetlen bemeneti tömbök esetén lásd: [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*objektum2*>, ... | Igen | Bármely, de nem vegyes | Legalább két elem a tömb létrehozásához |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*objektum1*>, <*tárgy2*>, ...] | Tömb | Az összes bemeneti elemből létrehozott tömb |
||||

*Példa*

Ez a példa a következő bemenetekből hoz létre tömböt:

```
createArray('h', 'e', 'l', 'l', 'o')
```

És ezt az eredményt adja vissza:`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Adategyenszámlési erőforrás-azonosító (URI) visszaadása egy karakterlánchoz.

```
dataUri('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A konvertálandó karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*adat-uri*> | Sztring | A bemeneti karakterlánc adatURI-ja |
||||

*Példa*

Ez a példa adatURI-t hoz létre a "hello" karakterlánchoz:

```
dataUri('hello')
```

És ezt az eredményt adja vissza:`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Adategyenvanterőforrás-azonosító (URI) bináris verzióját adja vissza.
Ezt a függvényt használja [a decodeDataUri() helyett.](#decodeDataUri)
Bár mindkét funkció ugyanúgy `dataUriBinary()` működik, előnyben kell részesíteni.

```
dataUriToBinary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A konvertálandó adatURI |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*bináris-adat-uri*> | Sztring | Az adatURI bináris verziója |
||||

*Példa*

Ez a példa bináris verziót hoz létre ehhez az adatURI-hoz:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És ezt az eredményt adja vissza:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Adategyen: erőforrás-azonosító (URI) karakterlánc-verzióját adja vissza.

```
dataUriToString('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A konvertálandó adatURI |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*húr-adat-uri*> | Sztring | Az adatURI karakterlánc-verziója |
||||

*Példa*

Ez a példa egy karakterláncot hoz létre ehhez az adatURI-hoz:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És ezt az eredményt adja vissza:`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

A hónap napjának visszaküldése időbélyegből.

```
dayOfMonth('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*hónap nap*> | Egész szám | A hónap napja a megadott időbélyegtől |
||||

*Példa*

Ez a példa a hónap napjának a következő időbélyegtől a számát adja eredményül:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

És ezt az eredményt adja vissza:`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>napOfWeek

A hét napját időbélyegből adja vissza.

```
dayOfWeek('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*a hét napja*> | Egész szám | A hét napja a megadott időbélyegtől, ahol a vasárnap 0, a hétfő 1, és így tovább |
||||

*Példa*

Ez a példa a hét napjának számát adja vissza ebből az időbélyegből:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

És ezt az eredményt adja vissza:`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Az év napjának visszaküldése időbélyegből.

```
dayOfYear('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*év napja*> | Egész szám | Az év napja a megadott időbélyegtől |
||||

*Példa*

Ez a példa az év napjának számát adja vissza ebből az időbélyegből:

```
dayOfYear('2018-03-15T13:27:36Z')
```

És ezt az eredményt adja vissza:`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>dekódbase64

Adja vissza a base64 kódolású karakterlánc-verziót, hatékonyan dekódolva a base64 karakterláncot.
Fontolja meg [a base64ToString()](#base64ToString) használatát a helyett. `decodeBase64()`
Bár mindkét funkció ugyanúgy `base64ToString()` működik, előnyben kell részesíteni.

```
decodeBase64('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A dekódolandó base64 kódolású karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolt-base64-karakterlánc*> | Sztring | Base64 kódolású karakterlánc-verzió |
||||

*Példa*

Ez a példa egy bázis64 kódolású karakterlánchoz hoz létre karakterláncot:

```
decodeBase64('aGVsbG8=')
```

És ezt az eredményt adja vissza:`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Adategyenvanterőforrás-azonosító (URI) bináris verzióját adja vissza.
Fontolja meg [a dataUriToBinary()](#dataUriToBinary)használata helyett `decodeDataUri()`.
Bár mindkét funkció ugyanúgy `dataUriToBinary()` működik, előnyben kell részesíteni.

```
decodeDataUri('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A dekódolni andó adatURI-karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*bináris-adat-uri*> | Sztring | Adat URI-karakterlánc bináris verziója |
||||

*Példa*

Ez a példa az adatURI bináris verzióját adja vissza:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És ezt az eredményt adja vissza:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>dekódurikomponens

Visszaad egy karakterláncot, amely az escape karaktereket dekódolt verziókkal helyettesíti.

```
decodeUriComponent('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A dekódolandó escape karakterekkel rendelkező karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolt-uri*> | Sztring | A frissített karakterlánc a dekódolt escape karakterekkel |
||||

*Példa*

Ez a példa a karakterlánc ban lévő escape karaktereket dekódolt verziókkal helyettesíti:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

És ezt az eredményt adja vissza:`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Adja vissza az egész eredményt két szám elosztásából.
A fennmaradó eredmény hez lásd: [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Osztalék*> | Igen | Egész vagy úszó | Az *osztó* által elosztandó szám |
| <*Osztó*> | Igen | Egész vagy úszó | Az *osztalékot*elválasztó szám , de nem lehet 0 |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*hányados-eredmény*> | Egész szám | Az első szám és a második szám elosztásából származó egész eredmény |
||||

*Példa*

Mindkét példa elosztja az első számot a második számmal:

```
div(10, 5)
div(11, 5)
```

És adja vissza ezt az eredményt:`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>kódolásuriösszetevő

Egy karakterlánc egységes erőforrás-azonosítóval (URI) kódolt verzióját adja vissza úgy, hogy az URL-nem biztonságos karaktereket escape karakterekre cseréli.
Fontolja meg [az uriComponent()](#uriComponent)használata helyett `encodeUriComponent()`.
Bár mindkét funkció ugyanúgy `uriComponent()` működik, előnyben kell részesíteni.

```
encodeUriComponent('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az URI-kódolású formátumra konvertálandó karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kódolt-uri*> | Sztring | Az URI-kódolású karakterlánc escape karakterekkel |
||||

*Példa*

Ez a példa egy URI-kódolású verziót hoz létre ehhez a karakterlánchoz:

```
encodeUriComponent('https://contoso.com')
```

És ezt az eredményt adja vissza:`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>üres

Ellenőrizze, hogy a gyűjtemény üres-e.
Igaz értéket ad vissza, ha a gyűjtemény üres, vagy hamis értéket ad vissza, ha nem üres.

```
empty('<collection>')
empty([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Karakterlánc, tömb vagy objektum | Az ellenőrizandó gyűjtemény |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | Igaz értéket ad vissza, ha a gyűjtemény üres. Hamis értéket ad vissza, ha nem üres. |
||||

*Példa*

Ezek a példák azt ellenőrzik, hogy a megadott gyűjtemények üresek-e:

```
empty('')
empty('abc')
```

És visszaadja ezeket az eredményeket:

* Első példa: Üres karakterláncot ad `true`át, így a függvény visszaadja a függvényt.
* Második példa: átadja az "abc" karakterláncot, így a függvény visszaadja a függvényt. `false`

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Ellenőrizze, hogy egy karakterlánc egy adott karakterlánccal végződik-e.
Igaz értéket ad vissza, ha a karakterláncrész található, vagy hamis értéket ad vissza, ha nem található.
Ez a funkció nem érzékeny a kis- és nagybetűkre.

```
endsWith('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | Az ellenőrizandó karakterlánc |
| <*searchText*> | Igen | Sztring | A megkeresandó záró karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis)  | Logikai | A záró karakterlánc megtalálásakor igaz értéket ad vissza. Hamis értéket ad vissza, ha nem található. |
||||

*1. példa*

Ez a példa azt ellenőrzi, hogy a "hello world" karakterlánc a "világ" karakterlánccal végződik-e:

```
endsWith('hello world', 'world')
```

És ezt az eredményt adja vissza:`true`

*2. példa*

Ez a példa azt ellenőrzi, hogy a "hello world" karakterlánc az "univerzum" karakterlánccal végződik-e:

```
endsWith('hello world', 'universe')
```

És ezt az eredményt adja vissza:`false`

<a name="equals"></a>

### <a name="equals"></a>egyenlő

Ellenőrizze, hogy mindkét érték, kifejezés vagy objektum egyenértékű-e.
Igaz értéket ad vissza, ha mindkettő egyenértékű, vagy hamis értéket ad vissza, ha nem egyenértékűek.

```
equals('<object1>', '<object2>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*objektum2*> | Igen | Különböző | Az összehasonlítandó értékek, kifejezések vagy objektumok |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | Igaz értéket ad vissza, ha mindkettő egyenértékű. Hamis értéket ad vissza, ha az nem egyenértékű. |
||||

*Példa*

Ezek a példák azt ellenőrzik, hogy a megadott bemenetek egyenértékűek-e.

```
equals(true, 1)
equals('abc', 'abcd')
```

És visszaadja ezeket az eredményeket:

* Első példa: Mindkét érték egyenértékű, `true`így a függvény afüggvényt adja vissza.
* Második példa: Mindkét érték nem egyenértékű, `false`így a függvény ad vissza.

<a name="first"></a>

### <a name="first"></a>Első

Karakterláncból vagy tömbből adja vissza az első elemet.

```
first('<collection>')
first([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény, ahol az első elem található |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*első begyűjtési tétel*> | Bármelyik | A gyűjtemény első eleme |
||||

*Példa*

Ezek a példák a gyűjtemények első elemét találják meg:

```
first('hello')
first(createArray(0, 1, 2))
```

És adja vissza ezeket az eredményeket:

* Első példa:`"h"`
* Második példa:`0`

<a name="float"></a>

### <a name="float"></a>lebegőpontos

Úszópontos szám karakterláncverziójának konvertálása tényleges lebegőpontos számmá.
Ezt a funkciót csak akkor használhatja, ha egyéni paramétereket ad át egy alkalmazásnak, például egy logikai alkalmazásnak vagy folyamatnak.

```
float('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A konvertálandó érvényes lebegőpontos számmal bekövetkező karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*lebegőérték*> | Float | A megadott karakterlánc lebegőpontos száma |
||||

*Példa*

Ez a példa egy karakterlánc-verziót hoz létre ehhez a lebegőpontos számhoz:

```
float('10.333')
```

És ezt az eredményt adja vissza:`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Időbélyeget ad vissza a megadott formátumban.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*formázott időbélyeg*> | Sztring | A frissített időbélyeg a megadott formátumban |
||||

*Példa*

Ez a példa az időbélyeget a megadott formátumra konvertálja:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

És ezt az eredményt adja vissza:`"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Olyan tömböt ad vissza, amelynek értékei megegyeznek egy kulcsnévvel a művelet *űrlapadataiban* vagy *űrlapkódolású* kimenetében.

```
formDataMultiValues('<actionName>', '<key>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (műveletneve)*> | Igen | Sztring | Az a művelet, amelynek kimenete a kívánt kulcsértékkel rendelkezik |
| <*Kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*tömb-billentyű-értékekkel*>] | Tömb | Tömb a megadott kulcsnak megfelelő összes értékkel |
||||

*Példa*

Ez a példa létrehoz egy tömböt a "Tárgy" kulcs értékéből a megadott művelet űrlapadatában vagy űrlapkódolású kimenetében:

```
formDataMultiValues('Send_an_email', 'Subject')
```

És egy tömb tárgyszövegét adja vissza, például:`["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Egyetlen olyan értéket ad vissza, amely megegyezik egy kulcsnévvel egy művelet *űrlapadatában* vagy *űrlapkódolású* kimenetében.
Ha a függvény egynél több egyezést talál, a függvény hibát okoz.

```
formDataValue('<actionName>', '<key>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (műveletneve)*> | Igen | Sztring | Az a művelet, amelynek kimenete a kívánt kulcsértékkel rendelkezik |
| <*Kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kulcs-érték*> | Sztring | A megadott kulcs értéke  |
||||

*Példa*

Ez a példa létrehoz egy karakterláncot a "Tárgy" kulcs értékéből a megadott művelet űrlapadatában vagy űrlapkódolású kimenetében:

```
formDataValue('Send_an_email', 'Subject')
```

És a tárgy szövegét karakterláncként adja vissza, például:`"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

A megadott formátumon alapuló karakterláncként ad vissza egy számot.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szám*> | Igen | Egész vagy dupla | A formázni kívánt érték. |
| <*Formátum*> | Igen | Sztring | Összetett formátumú karakterlánc, amely meghatározza a használni kívánt formátumot. A támogatott numerikus formátumú karakterláncokat lásd: [Standard numerikus formátumú karakterláncok](https://docs.microsoft.com/dotnet/standard/base-types/standard-numeric-format-strings), amelyeket a `number.ToString(<format>, <locale>)`program támogat. |
| <*Locale*> | Nem | Sztring | A használni használandó területi `number.ToString(<format>, <locale>)`beállítás a. Ha nincs megadva, az `en-us`alapértelmezett érték . |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*formázott szám*> | Sztring | A megadott szám karakterláncként a megadott formátumban. Ezt a visszatérési értéket `int` `float`bevetheti egy vagy . |
||||

*1. példa*

Tegyük fel, hogy `1234567890`formázni szeretné a számot. Ez a példa ezt a számot az "1,234,567,890.00" karakterláncként formázza.

```
formatNumber(1234567890, '{0:0,0.00}', 'en-us')
```

*2. példa"

Tegyük fel, hogy `1234567890`formázni szeretné a számot. Ez a példa az "1.234.567.890,00" karakterláncra formázza a számot.

```
formatNumber(1234567890, '{0:0,0.00}', 'is-is')
```

*3. példa*

Tegyük fel, hogy `17.35`formázni szeretné a számot. Ez a példa a számot a "$17.35" karakterláncra formázza.

```
formatNumber(17.36, 'C2')
```

*4. példa*

Tegyük fel, hogy `17.35`formázni szeretné a számot. Ez a példa a számot a "17,35 kr" karakterláncra formázza.

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Adja vissza az aktuális időbélyeget és a megadott időegységeket.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Intervallum*> | Igen | Egész szám | A hozzáadni hozandó időegységek száma |
| <*timeUnit (időegység)*> | Igen | Sztring | Az *intervallummal*használandó időegység: "Második", "Perc", "Óra", "Nap", "Hét", "Hónap", "Év" |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az aktuális időbélyeg és a megadott időegységek száma |
||||

*1. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-03-01T00:00:00.0000000Z".
Ez a példa öt napot ad hozzá az időbélyeghez:

```
getFutureTime(5, 'Day')
```

És ezt az eredményt adja vissza:`"2018-03-06T00:00:00.0000000Z"`

*2. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-03-01T00:00:00.0000000Z".
Ez a példa öt napot ad hozzá, és az eredményt "D" formátumra konvertálja:

```
getFutureTime(5, 'Day', 'D')
```

És ezt az eredményt adja vissza:`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Az aktuális időbélyeget adja vissza a megadott időmértékekkel csökkentve.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Intervallum*> | Igen | Egész szám | A kivonandó megadott időegységek száma |
| <*timeUnit (időegység)*> | Igen | Sztring | Az *intervallummal*használandó időegység: "Második", "Perc", "Óra", "Nap", "Hét", "Hónap", "Év" |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az aktuális időbélyeg mínusz a megadott időegységek száma |
||||

*1. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-02-01T00:00:00.0000000Z".
Ez a példa öt napot von ki az időbélyegből:

```
getPastTime(5, 'Day')
```

És ezt az eredményt adja vissza:`"2018-01-27T00:00:00.0000000Z"`

*2. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-02-01T00:00:00.0000000Z".
Ez a példa öt napot von ki, és az eredményt "D" formátumra konvertálja:

```
getPastTime(5, 'Day', 'D')
```

És ezt az eredményt adja vissza:`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Ellenőrizze, hogy az első érték nagyobb-e, mint a második érték.
Igaz értéket ad vissza, ha az első érték több, vagy hamis értéket ad vissza, ha kevesebb.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Egész, úszó vagy karakterlánc | Az első érték, amely ellenőrzi, hogy nagyobb-e, mint a második érték |
| <*compareTo*> | Igen | Egész, lebegős vagy karakterlánc, illetve | Az összehasonlítási érték |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | Igaz értéket ad vissza, ha az első érték nagyobb, mint a második érték. Hamis értéket ad vissza, ha az első érték egyenlő vagy kisebb, mint a második érték. |
||||

*Példa*

Ezek a példák azt vizsgálják, hogy az első érték nagyobb-e, mint a második érték:

```
greater(10, 5)
greater('apple', 'banana')
```

És adja vissza ezeket az eredményeket:

* Első példa:`true`
* Második példa:`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Ellenőrizze, hogy az első érték nagyobb vagy egyenlő-e a második értékkel.
Igaz értéket ad vissza, ha az első érték nagyobb vagy egyenlő, vagy hamis értéket ad vissza, ha az első érték kisebb.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Egész, úszó vagy karakterlánc | Az első érték, amely azt ellenőrzi, hogy nagyobb vagy egyenlő-e a második értékkel |
| <*compareTo*> | Igen | Egész, lebegős vagy karakterlánc, illetve | Az összehasonlítási érték |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | Igaz értéket ad vissza, ha az első érték nagyobb vagy egyenlő a második értékkel. Hamis értéket ad vissza, ha az első érték kisebb, mint a második érték. |
||||

*Példa*

Ezek a példák azt vizsgálják, hogy az első érték nagyobb vagy egyenlő-e a második értéknél:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

És adja vissza ezeket az eredményeket:

* Első példa:`true`
* Második példa:`false`

<a name="guid"></a>

### <a name="guid"></a>Guid

Globálisan egyedi azonosító (GUID) létrehozása karakterláncként, például "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Az alapértelmezett "D" formátumtól eltérő formátumot is megadhat a GUID azonosítóhoz, amely 32 számjegy, kötőjellel elválasztva.

```
guid('<format>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Formátum*> | Nem | Sztring | A visszaadott GUID egyetlen [formátumú megváltója.](https://msdn.microsoft.com/library/97af8hh4) Alapértelmezés szerint a formátum "D", de használhatja az "N", "D", "B", "P", vagy "X". |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*GUID-érték*> | Sztring | Véletlenszerűen generált GUID |
||||

*Példa*

Ez a példa ugyanazt a GUID azonosítót hozza létre, de 32 számjeggyel, kötőjelekkel elválasztva és zárójelben:

```
guid('P')
```

És ezt az eredményt adja vissza:`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Ellenőrizze, hogy egy kifejezés igaz vagy hamis.
Az eredmény alapján adja vissza a megadott értéket.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Kifejezés*> | Igen | Logikai | Az ellenőrizandó kifejezés |
| <*értékHaIgaz*> | Igen | Bármelyik | A kifejezés igaz kifejezése esetén visszaadandó érték |
| <*értékHaFalse*> | Igen | Bármelyik | A kifejezés hamis kifejezése esetén visszaadandó érték |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*megadott visszatérési érték*> | Bármelyik | A megadott érték, amely a kifejezés igaz vagy hamis kifejezésén alapul |
||||

*Példa*

Ez a `"yes"` példa azért ad vissza, mert a megadott kifejezés igaz értéket ad vissza.
Ellenkező esetben a `"no"`példa a következőket adja vissza:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Adja vissza egy részkarakterlánc kezdő pozícióját vagy indexértékét.
Ez a funkció nem a kis- és nagybetűket, és az indexek a 0 számmal kezdődnek.

```
indexOf('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A megkeresandó karakterlánc |
| <*searchText*> | Igen | Sztring | A megkeresandó karakterlánc részkarakterlánca |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*index-érték*>| Egész szám | A megadott karakterlánc kezdőpozíciója vagy indexértéke. <p>Ha a karakterlánc nem található, adja vissza a -1 számot. |
||||

*Példa*

Ez a példa a "hello world" karakterlánc "világ" részkarakterláncának kezdő indexértékét keresi meg a "hello world" karakterláncban:

```
indexOf('hello world', 'world')
```

És ezt az eredményt adja vissza:`6`

<a name="int"></a>

### <a name="int"></a>int

Adja vissza egy karakterlánc egész verzióját.

```
int('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A konvertálandó karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*egész szám-eredmény*> | Egész szám | A megadott karakterlánc egész verziója |
||||

*Példa*

Ez a példa egy egész verziót hoz létre a "10" karakterlánchoz:

```
int('10')
```

És ezt az eredményt adja vissza:`10`

<a name="item"></a>

### <a name="item"></a>item

Ha ismétlődő műveletben használja egy tömbön keresztül, a művelet aktuális iterációja során adja vissza az aktuális elemet a tömbben.
Az értékeket az elem tulajdonságaiból is lejuthat.

```
item()
```

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*aktuális-tömb-elem*> | Bármelyik | A művelet aktuális iterációjának aktuális eleme a tömbben |
||||

*Példa*

Ez a `body` példa az elemet az aktuális üzenetből kapja le az "Send_an_email" művelethez egy minden ciklus aktuális iterációjának belsejében:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>Elemek

Az aktuális cikket adja vissza az egyes ciklusokból egy-minden ciklusban.
Használja ezt a funkciót az egyes ciklusokon belül.

```
items('<loopName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*ciklusneve*> | Igen | Sztring | Az egyes ciklusok neve |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Cikk*> | Bármelyik | Az aktuális ciklus ból származó elem a megadott ciklusban |
||||

*Példa*

Ez a példa lekéri az aktuális elemet a megadott minden ciklusból:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterációIndexek

Adja vissza az aktuális iteráció indexértékét egy Until cikluson belül. Használhatja ezt a függvényt belül beágyazott Ig hurkok. 

```
iterationIndexes('<loopName>')
```

| Paraméter | Kötelező | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*ciklusneve*> | Igen | Sztring | Az Until ciklus neve | 
||||| 

| Visszatérítési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*Index*> | Egész szám | Az aktuális iteráció indexértéke a megadott Ig cikluson belül | 
|||| 

*Példa* 

Ez a példa létrehoz egy számláló változót, és minden egyes iteráció során egy-egy lépéssel megnövekszik, amíg a számláló értéke el nem éri az ötöt. A példa is létrehoz egy változót, amely nyomon követi az aktuális index minden iteráció. Az Esztor ciklusban minden egyes ismétlés során a példa megnő a számlálón, majd hozzárendeli a számláló értékét az aktuális indexértékhez, majd megnöveli a számlálót. Az aktuális iterációs számot bármikor meghatározhatja az aktuális indexérték beolvasásával.

```
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5),
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Adja vissza a JavaScript-objektumnotika (JSON) típusú értéket vagy objektumot egy karakterlánchoz vagy XML-hez.

```
json('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Karakterlánc vagy XML | A konvertálandó karakterlánc vagy XML |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*JSON-eredmény*> | JSON natív típus vagy objektum | A megadott karakterlánc vagy XML JSON natív típusértéke vagy objektuma. Ha a karakterlánc null értékű, a függvény üres objektumot ad vissza. |
||||

*1. példa*

Ez a példa ezt a karakterláncot JSON-értékké alakítja:

```
json('[1, 2, 3]')
```

És ezt az eredményt adja vissza:`[1, 2, 3]`

*2. példa*

Ez a példa ezt a karakterláncot JSON-ra konvertálja:

```
json('{"fullName": "Sophia Owen"}')
```

És ezt az eredményt adja vissza:

```
{
  "fullName": "Sophia Owen"
}
```

*3. példa*

Ez a példa az XML-t JSON-ra konvertálja:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

És ezt az eredményt adja vissza:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>Kereszteződés

Olyan gyűjteményt ad vissza, amely *csak* a megadott gyűjteményekben lévő közös elemeket tartalmaz.
Az eredményben való megjelenéshez egy elemnek meg kell jelennie a függvénynek átadott összes gyűjteményben.
Ha egy vagy több elem neve megegyezik, az utolsó ilyen nevű elem jelenik meg az eredményben.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Igen | Tömb vagy objektum, de nem mindkettő | Azok a gyűjtemények, amelyeket *csak* a közös elemekre szeretne kitenni |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*közös elemek*> | Tömb vagy objektum, illetve | Olyan gyűjtemény, amely csak a megadott gyűjtemények közös elemeit tartalmaz |
||||

*Példa*

Ez a példa megkeresi a közös elemeket ezekben a tömbökben:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

És csak *ezeket* az elemeket tartalmazó tömböt ad vissza:`[1, 2]`

<a name="join"></a>

### <a name="join"></a>csatlakozás

Olyan karakterláncot ad vissza, amely egy tömb összes elemét bejelöli, és minden karaktert elválaszt egy *határolójel.*

```
join([<collection>], '<delimiter>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Tömb | Az a tömb, amely a követendő elemeket |
| <*Határoló*> | Igen | Sztring | Az eredményül kapott karakterlánc ban az egyes karakterek között megjelenő elválasztó |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*char1*><*határoló*><*karakter2*><*határoló*>... | Sztring | A megadott tömb összes eleméből létrehozott létrejövő karakterlánc |
||||

*Példa*

Ez a példa egy karakterláncot hoz létre a tömb összes eleméből, a megadott karakterrel a határolójelként:

```
join(createArray('a', 'b', 'c'), '.')
```

És ezt az eredményt adja vissza:`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>Utolsó

Az utolsó elem visszaküldése egy gyűjteményből.

```
last('<collection>')
last([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény, ahol az utolsó elem található |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*utolsó begyűjtési elem*> | Karakterlánc vagy tömb | A gyűjtemény utolsó eleme |
||||

*Példa*

Ezek a példák a gyűjtemények utolsó elemét találják meg:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

És visszaadja ezeket az eredményeket:

* Első példa:`"d"`
* Második példa:`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Adja vissza a részkarakterlánc utolsó előfordulásának kezdő pozícióját vagy indexértékét.
Ez a funkció nem a kis- és nagybetűket, és az indexek a 0 számmal kezdődnek.

```
lastIndexOf('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A megkeresandó karakterlánc |
| <*searchText*> | Igen | Sztring | A megkeresandó karakterlánc részkarakterlánca |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*záró-index-érték*> | Egész szám | A megadott részkarakterlánc utolsó előfordulásának kezdő pozíciója vagy indexértéke. <p>Ha a karakterlánc nem található, adja vissza a -1 számot. |
||||

*Példa*

Ez a példa a "hello world" karakterlánc utolsó előfordulásának kezdő indexértékét keresi meg a "hello world" karakterláncban:

```
lastIndexOf('hello world', 'world')
```

És ezt az eredményt adja vissza:`6`

<a name="length"></a>

### <a name="length"></a>hossz

A gyűjteményben lévő elemek számát adja vissza.

```
length('<collection>')
length([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény a megszámlálandó elemekkel |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*vagy-szám*> | Egész szám | A gyűjteményben lévő elemek száma |
||||

*Példa*

Ezek a példák az alábbi gyűjteményekelemeinek számát számolják:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

És adja vissza ezt az eredményt:`4`

<a name="less"></a>

### <a name="less"></a>less

Ellenőrizze, hogy az első érték kisebb-e, mint a második érték.
Igaz értéket ad vissza, ha az első érték kisebb, vagy hamis értéket ad vissza, ha az első érték több.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Egész, úszó vagy karakterlánc | Az első érték, amely azt ellenőrzi, hogy kisebb-e, mint a második érték |
| <*compareTo*> | Igen | Egész, lebegős vagy karakterlánc, illetve | Az összehasonlító elem |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | Igaz értéket ad vissza, ha az első érték kisebb, mint a második érték. Hamis értéket ad vissza, ha az első érték egyenlő vagy nagyobb, mint a második érték. |
||||

*Példa*

Ezek a példák azt vizsgálják, hogy az első érték kisebb-e, mint a második érték.

```
less(5, 10)
less('banana', 'apple')
```

És adja vissza ezeket az eredményeket:

* Első példa:`true`
* Második példa:`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Ellenőrizze, hogy az első érték kisebb vagy egyenlő-e a második értékkel.
Igaz értéket ad vissza, ha az első érték kisebb vagy egyenlő, vagy hamis értéket ad vissza, ha az első érték több.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Egész, úszó vagy karakterlánc | A második értéknél kisebb vagy azzal egyenlő első érték |
| <*compareTo*> | Igen | Egész, lebegős vagy karakterlánc, illetve | Az összehasonlító elem |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis)  | Logikai | Igaz értéket ad vissza, ha az első érték kisebb vagy egyenlő a második értékkel. Hamis értéket ad vissza, ha az első érték nagyobb, mint a második érték. |
||||

*Példa*

Ezek a példák azt vizsgálják, hogy az első érték kisebb vagy egyenlő-e a második értéknél.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

És adja vissza ezeket az eredményeket:

* Első példa:`true`
* Második példa:`false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Adja vissza az eseményindítót vagy műveletet megindítót vagy műveletet megindítót megindítót vagy műveletet megindítót meghívó "visszahívási URL-címet".
Ez a függvény csak a **HttpWebhook** és **ApiConnectionWebhook** összekötőtípusok eseményindítóival és műveleteivel működik, de a **Manual**, **Recurrence**, **HTTP**és **APIConnection** típusokhoz nem.

```
listCallbackUrl()
```

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*visszahívás-URL*> | Sztring | Az eseményindító vagy művelet visszahívási URL-címe |
||||

*Példa*

Ez a példa egy minta visszahívási URL-címet mutat be, amelyet ez a függvény visszaadhat:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>Max

A legmagasabb értéket adja vissza egy olyan listából vagy tömbből, amelynek mindkét végén a számok vannak befoglalva.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*1-es szám*>, <*szám2*>, ... | Igen | Egész, Úszó vagy mindkettő | Azok a számhalmaz, amelyből a legmagasabb értéket szeretné |
| [<*szám1*>, <*szám2*>, ...] | Igen | Tömb - egész, lebegő, vagy mindkettő | Az a számtömb, amelyből a legmagasabb értéket szeretné |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*maximális érték*> | Egész vagy úszó | A megadott tömb vagy számhalmaz legnagyobb értéke |
||||

*Példa*

Ezek a példák a legnagyobb értéket kapják a számok és a tömb készletéből:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

És adja vissza ezt az eredményt:`3`

<a name="min"></a>

### <a name="min"></a>p

A legkisebb értéket adja vissza egy számhalmazból vagy tömbből.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*1-es szám*>, <*szám2*>, ... | Igen | Egész, Úszó vagy mindkettő | Az a számkészlet, amelyből a legalacsonyabb értéket szeretné |
| [<*szám1*>, <*szám2*>, ...] | Igen | Tömb - egész, lebegő, vagy mindkettő | Az a számtömb, amelyből a legalacsonyabb értéket szeretné |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*min-érték*> | Egész vagy úszó | A megadott számhalmaz vagy a megadott tömb legalacsonyabb értéke |
||||

*Példa*

Ezek a példák a számok és a tömb készletének legalacsonyabb értékét kapják:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

És adja vissza ezt az eredményt:`1`

<a name="mod"></a>

### <a name="mod"></a>Mod

Adja vissza a maradékot két szám felosztásából.
Az egész eredmény megtekintéséhez lásd: [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Osztalék*> | Igen | Egész vagy úszó | Az *osztó* által elosztandó szám |
| <*Osztó*> | Igen | Egész vagy úszó | Az *osztalékot*elválasztó szám , de nem lehet 0. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*modulo-eredmény*> | Egész vagy úszó | Az első szám és a második szám elosztásának fennmaradó része |
||||

*Példa*

Ez a példa elosztja az első számot a második számmal:

```
mod(3, 2)
```

És adja vissza ezt az eredményt:`1`

<a name="mul"></a>

### <a name="mul"></a>Mul

Tegye vissza a terméket két szám szorzatából.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Igen | Egész vagy úszó | A szorzattalszorozandó *szám2* |
| <*multiplicand2*> | Igen | Egész vagy úszó | A *többszöröstmegmászószám1* |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*termék-eredmény*> | Egész vagy úszó | Az első szám és a második szám szorzatából származó termék |
||||

*Példa*

Ezek a példák több az első szám a második szám:

```
mul(1, 2)
mul(1.5, 2)
```

És adja vissza ezeket az eredményeket:

* Első példa:`2`
* Második példa`3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>többrészes test

Adja vissza a törzsegy adott része egy művelet kimenet, amely több részből áll.

```
multipartBody('<actionName>', <index>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (műveletneve)*> | Igen | Sztring | A több részből álló kimenetet tartalmazó művelet neve |
| <*Index*> | Igen | Egész szám | A kívánt rész indexértéke |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Szervezet*> | Sztring | A test a megadott rész |
||||

<a name="not"></a>

### <a name="not"></a>not

Ellenőrizze, hogy egy kifejezés hamis-e.
Igaz értéket ad vissza, ha a kifejezés hamis, vagy hamis értéket ad vissza, ha igaz.

```json
not(<expression>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Kifejezés*> | Igen | Logikai | Az ellenőrizandó kifejezés |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | Igaz értéket ad vissza, ha a kifejezés hamis. Hamis értéket ad vissza, ha a kifejezés igaz. |
||||

*1. példa*

Ezek a példák azt ellenőrzik, hogy a megadott kifejezések hamisak-e:

```json
not(false)
not(true)
```

És adja vissza ezeket az eredményeket:

* Első példa: A kifejezés hamis, `true`így a függvény afüggvény t ad vissza.
* Második példa: A kifejezés igaz, `false`így a függvény ad vissza .

*2. példa*

Ezek a példák azt ellenőrzik, hogy a megadott kifejezések hamisak-e:

```json
not(equals(1, 2))
not(equals(1, 1))
```

És adja vissza ezeket az eredményeket:

* Első példa: A kifejezés hamis, `true`így a függvény afüggvény t ad vissza.
* Második példa: A kifejezés igaz, `false`így a függvény ad vissza .

<a name="or"></a>

### <a name="or"></a>vagy

Ellenőrizze, hogy legalább egy kifejezés igaz-e.
Igaz értéket ad vissza, ha legalább egy kifejezés igaz, vagy hamis értéket ad vissza, ha mindegyik hamis.

```
or(<expression1>, <expression2>, ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*kifejezés1*>, <*kifejezés2*>, ... | Igen | Logikai | Az ellenőrizandó kifejezések |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | Igaz értéket ad vissza, ha legalább egy kifejezés igaz. Hamis értéket ad vissza, ha az összes kifejezés hamis. |
||||

*1. példa*

Ezek a példák azt vizsgálják, hogy legalább egy kifejezés igaz-e:

```json
or(true, false)
or(false, false)
```

És adja vissza ezeket az eredményeket:

* Első példa: Legalább egy kifejezés igaz, `true`így a függvény ad vissza.
* Második példa: Mindkét kifejezés hamis, így `false`a függvény visszaadja.

*2. példa*

Ezek a példák azt vizsgálják, hogy legalább egy kifejezés igaz-e:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

És adja vissza ezeket az eredményeket:

* Első példa: Legalább egy kifejezés igaz, `true`így a függvény ad vissza.
* Második példa: Mindkét kifejezés hamis, így `false`a függvény visszaadja.

<a name="outputs"></a>

### <a name="outputs"></a>Kimenetek

Egy művelet kimenetek futásidőben visszaad. Használja ezt a `actionOutputs()`függvényt, nem `outputs()` pedig a , amely a Logic App Designer ben oldódik fel. Bár mindkét funkció ugyanúgy `outputs()` működik, előnyben kell részesíteni.

```
outputs('<actionName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (műveletneve)*> | Igen | Sztring | A művelet kívánt kimenetének neve |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*Kimeneti*> | Sztring | A megadott művelet kimenete |
||||

*Példa*

Ez a példa a Twitter-művelet kimenetét kapja: `Get user`

```
outputs('Get_user')
```

És ezt az eredményt adja vissza:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>paraméterek

Adja vissza a munkafolyamat-definícióban ismertetett paraméter értékét.

```
parameters('<parameterName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*paraméternév*> | Igen | Sztring | Annak a paraméternek a neve, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*paraméter-érték*> | Bármelyik | A megadott paraméter értéke |
||||

*Példa*

Tegyük fel, hogy rendelkezik ezzel a JSON értékkel:

```json
{
  "fullName": "Sophia Owen"
}
```

Ez a példa a megadott paraméter értékét kapja meg:

```
parameters('fullName')
```

És ezt az eredményt adja vissza:`"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>Rand

Egy véletlenszerű egész szám visszaadása egy adott tartományból, amely csak a kezdő végén van befoglalva.

```
rand(<minValue>, <maxValue>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Igen | Egész szám | A tartomány legalacsonyabb egész száma |
| <*maxValue érték*> | Igen | Egész szám | A függvény által visszaadott tartomány legmagasabb egész számát követő egész szám |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*véletlenszerű eredmény*> | Egész szám | A megadott tartományból visszaadott véletlenszerű egész szám |
||||

*Példa*

Ez a példa egy véletlenszerű egész számait kapja meg a megadott tartományból, a maximális érték nélkül:

```
rand(1, 5)
```

És a következő számok egyikét `1` `2`adja `3`eredményül: , , , vagy`4`

<a name="range"></a>

### <a name="range"></a>Tartomány

Adott egész számból induló egész tömbet ad vissza.

```
range(<startIndex>, <count>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Igen | Egész szám | Egész érték, amely a tömböt első elemként indítja el |
| <*Számít*> | Igen | Egész szám | Az egész számok száma a tömbben |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*tartomány-eredmény*>] | Tömb | A megadott indexből származó egész számokkal rendelkező tömb |
||||

*Példa*

Ez a példa egy egész tömböt hoz létre, amely a megadott indexből indul ki, és a megadott számú egész számmal rendelkezik:

```
range(1, 4)
```

És ezt az eredményt adja vissza:`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Helyettesít

Cseréljen le egy részkarakterláncot a megadott karakterláncra, és adja vissza az eredménykarakterláncot. Ez a funkció nem imitot.

```
replace('<text>', '<oldText>', '<newText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A lecserélni kívánt karakterlánc |
| <*régiszöveg*> | Igen | Sztring | A helyettesíthető karakterlánc |
| <*újszöveg*> | Igen | Sztring | A cserekarakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített szöveg*> | Sztring | A frissített karakterlánc a karakterlánc-helyettes cseréje után <p>Ha a karakterláncrész nem található, adja vissza az eredeti karakterláncot. |
||||

*Példa*

Ez a példa megkeresi a "régi" részkarakterláncot a "régi karakterláncban", és a "régi" szót az "új" helyett az "új" kifejezésre cseréli:

```
replace('the old string', 'old', 'new')
```

És ezt az eredményt adja vissza:`"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty tulajdonság

Tulajdonság eltávolítása egy objektumból, és adja vissza a frissített objektumot. Ha az eltávolítani kívánt tulajdonság nem létezik, a függvény az eredeti objektumot adja vissza.

```
removeProperty(<object>, '<property>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | A JSON-objektum, ahonnan el szeretne távolítani egy tulajdonságot |
| <*Tulajdonság*> | Igen | Sztring | Az eltávolítandó tulajdonság neve |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített objektum*> | Objektum | A frissített JSON-objektum a megadott tulajdonság nélkül |
||||

Ha el szeretne távolítani egy gyermektulajdonságot egy meglévő tulajdonságból, használja ezt a szintaxist:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | Az a JSON-objektum, amelynek tulajdonságát el szeretné távolítani |
| <*szülő-tulajdonság*> | Igen | Sztring | Az eltávolítani kívánt gyermektulajdonsággal rendelkező szülőtulajdonság neve |
| <*gyermek-tulajdonság*> | Igen | Sztring | Az eltávolítandó gyermektulajdonság neve |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített objektum*> | Objektum | A frissített JSON-objektum, amelynek gyermektulajdonságát eltávolította |
||||

*1. példa*

Ez a példa `middleName` eltávolítja a tulajdonságot egy JSON-objektumból, amely a [JSON()](#json) függvénnyel karakterláncból JSON-ná alakul át, és a frissített objektumot adja vissza:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Az aktuális JSON-objektum:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Itt a frissített JSON objektum:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*2. példa*

Ez a példa `middleName` eltávolítja `customerName` a gyermek tulajdonságot egy JSON-objektum szülőtulajdonságából, amely a [JSON()](#json) függvénnyel karakterláncból JSON-ná alakul át, és a frissített objektumot adja vissza:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Az aktuális JSON-objektum:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Itt a frissített JSON objektum:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

Visszaadja a megadott hatókörrel kapcsolatos műveleten (például `For_each`a, a , `Until`a) vagy a műveleten belüli összes művelet bemeneteit és `Scope` kimeneteit. Ez a függvény hasznos egy sikertelen művelet eredményeit visszaad, így diagnosztizálhatja és kezelheti a kivételeket. További információ: [Get context and results for failures](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Igen | Sztring | Annak a hatókörrel kapcsolatos műveletnek a neve, amelyből az összes belső művelet bemeneteit és kimeneteit vissza kell adni |
||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*tömb-objektum*> | Tömbobjektum | Olyan tömb, amely a megadott hatókörrel kapcsolatos műveleten belül megjelenő minden művelet bemeneteinek és kimeneteinek tömbjeit tartalmazza |
||||

*Példa*

Ez a példa a `For_each` cikluson belüli HTTP-művelet minden egyes iterációjának bemeneteit és kimeneteit adja vissza a `result()` `Compose` művelet függvényének használatával:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

A példaként visszaadott tömb hogyan nézhet `outputs` ki ott, ahol a külső objektum tartalmazza a `For_each` műveleten belüli műveletek egyes iterációinak bemeneteit és kimeneteit.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty tulajdonság

Állítsa be a JSON-objektum tulajdonságának értékét, és adja vissza a frissített objektumot. Ha a beállítani kívánt tulajdonság nem létezik, a tulajdonság hozzáadódik az objektumhoz. Új tulajdonság hozzáadásához használja az [addProperty()](#addProperty) függvényt.

```
setProperty(<object>, '<property>', <value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | Az a JSON-objektum, amelynek tulajdonságát be szeretné állítani |
| <*Tulajdonság*> | Igen | Sztring | A beállítandó meglévő vagy új tulajdonság neve |
| <*Érték*> | Igen | Bármelyik | A megadott tulajdonsághoz beállítandó érték |
|||||

Ha a gyermektulajdonságot gyermekobjektumban szeretné `setProperty()` beállítani, használjon beágyazott hívást. Ellenkező esetben a függvény csak a gyermekobjektumot adja vissza kimenetként.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | Az a JSON-objektum, amelynek tulajdonságát be szeretné állítani |
| <*szülő-tulajdonság*> | Igen | Sztring | A szülőtulajdonság neve a beállítani kívánt gyermektulajdonsággal |
| <*gyermek-tulajdonság*> | Igen | Sztring | A beállítandó gyermektulajdonság neve |
| <*Érték*> | Igen | Bármelyik | A megadott tulajdonsághoz beállítandó érték |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített objektum*> | Objektum | A frissített JSON-objektum, amelynek tulajdonságát beállította |
||||

*1. példa*

Ez a `surName` példa egy JSON-objektum tulajdonságát állítja be, amely a [JSON()](#json) függvény használatával karakterláncból JSON-ná alakul át. A függvény hozzárendeli a megadott értéket a tulajdonsághoz, és visszaadja a frissített objektumot:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Az aktuális JSON-objektum:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Itt a frissített JSON objektum:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*2. példa*

Ez a `surName` példa egy `customerName` JSON-objektum szülőtulajdonságának gyermektulajdonságát állítja be, amely a [JSON()](#json) függvény használatával karakterláncból JSON-ra konvertálódik. A függvény hozzárendeli a megadott értéket a tulajdonsághoz, és visszaadja a frissített objektumot:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Az aktuális JSON-objektum:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Itt a frissített JSON objektum:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>Ugrál

Távolítsa el az elemeket a gyűjtemény elejéről, és küldje vissza *az összes többi* elemet.

```
skip([<collection>], <count>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Tömb | Az a gyűjtemény, amelynek az eltávolítani kívánt elemei |
| <*Számít*> | Igen | Egész szám | Pozitív egész szám az előlapról eltávolítandó elemek számához |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*frissített gyűjtési*>] | Tömb | A frissített gyűjtemény a megadott elemek eltávolítása után |
||||

*Példa*

Ez a példa eltávolít egy elemet, a 0 számot a megadott tömb elejéről:

```
skip(createArray(0, 1, 2, 3), 1)
```

És visszaadja ezt a tömböt a többi elemből:`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>felosztás

Az eredeti karakterláncban megadott határolókarakter alapján vesszővel elválasztott részkarakterláncokat tartalmazó tömböt ad vissza.

```
split('<text>', '<delimiter>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | Az eredeti karakterláncban megadott határolójel alapján részkarakterláncokra bontandó karakterlánc |
| <*Határoló*> | Igen | Sztring | A határolójelként használandó eredeti karakterlánc karaktere |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*>,<* *>,...]* | Tömb | Az eredeti karakterláncrészeket tartalmazó tömb, vesszővel elválasztva |
||||

*Példa*

Ez a példa egy tömböt hoz létre a megadott karakterláncrészből a megadott karakter alapján, mint határoló:

```
split('a_b_c', '_')
```

És ennek a tömbnek az eredményeként adja vissza:`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Adja vissza a nap kezdetét egy időbélyeghez.

```
startOfDay('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | A megadott időbélyeg, de a nap nulla órás jeltől indul |
||||

*Példa*

Ez a példa megkeresi a nap kezdetét ehhez az időbélyeghez:

```
startOfDay('2018-03-15T13:30:30Z')
```

És ezt az eredményt adja vissza:`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Adja vissza az óra kezdetét egy időbélyeghez.

```
startOfHour('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | A megadott időbélyeg, de az óra nullaperces jeltől indul |
||||

*Példa*

Ez a példa megkeresi az óra kezdetét ehhez az időbélyeghez:

```
startOfHour('2018-03-15T13:30:30Z')
```

És ezt az eredményt adja vissza:`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Adja vissza a hónap kezdetét egy időbélyeghez.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | A megadott időbélyeg, de a hónap első napjától a nulla órás jelnél kezdődik |
||||

*Példa*

Ez a példa a hónap kezdetét adja vissza ehhez az időbélyeghez:

```
startOfMonth('2018-03-15T13:30:30Z')
```

És ezt az eredményt adja vissza:`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Ellenőrizze, hogy egy karakterlánc egy adott karakterlánccal kezdődik-e.
Igaz értéket ad vissza, ha a karakterláncrész található, vagy hamis értéket ad vissza, ha nem található.
Ez a funkció nem érzékeny a kis- és nagybetűkre.

```
startsWith('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | Az ellenőrizandó karakterlánc |
| <*searchText*> | Igen | Sztring | A megkeresandó kezdő karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis)  | Logikai | A kezdő karakterlánc-részkarakterlánc megkeresésekén igaz értéket ad vissza. Hamis értéket ad vissza, ha nem található. |
||||

*1. példa*

Ez a példa azt ellenőrzi, hogy a "hello world" karakterlánc a "hello" részkarakterlánccal kezdődik-e:

```
startsWith('hello world', 'hello')
```

És ezt az eredményt adja vissza:`true`

*2. példa*

Ez a példa azt ellenőrzi, hogy a "hello world" karakterlánc az "üdvözlések" részkarakterlánccal kezdődik-e:

```
startsWith('hello world', 'greetings')
```

És ezt az eredményt adja vissza:`false`

<a name="string"></a>

### <a name="string"></a>sztring

Egy érték karakterlánc-verzióját adja vissza.

```
string(<value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Bármelyik | A konvertálandó érték |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*karakterlánc-érték*> | Sztring | A megadott érték karakterláncverziója |
||||

*1. példa*

Ez a példa létrehozza a karakterlánc verzióját ehhez a számhoz:

```
string(10)
```

És ezt az eredményt adja vissza:`"10"`

*2. példa*

Ez a példa karakterláncot hoz létre a megadott JSON-objektumhoz, és a fordított perjel (\\) karaktert használja a dupla idézőjel () escape karaktereként.

```
string( { "name": "Sophie Owen" } )
```

És ezt az eredményt adja vissza:`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Al

Adja vissza a második szám kivonásának eredményét az első számból.

```
sub(<minuend>, <subtrahend>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Kisebbítendő*> | Igen | Egész vagy úszó | Az a szám, amelyből kikell vonni a *kivonandó* |
| <*Kivonandó*> | Igen | Egész vagy úszó | A *minuendből* kivonandó szám |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Eredmény*> | Egész vagy úszó | A második szám nak az első számból történő kivonásának eredménye |
||||

*Példa*

Ez a példa kivonja a második számot az első számból:

```
sub(10.3, .3)
```

És ezt az eredményt adja vissza:`10`

<a name="substring"></a>

### <a name="substring"></a>Substring

Karakterlánc karaktereit adja vissza a megadott pozíciótól vagy indextől kezdve.
Az indexértékek a 0 számmal kezdődnek.

```
substring('<text>', <startIndex>, <length>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | Az a karakterlánc, amelynek karaktereit |
| <*startIndex*> | Igen | Egész szám | 0-val egyenlő vagy nagyobb pozitív szám, amelyet kezdő pozícióként vagy indexértékként kíván használni |
| <*Hossza*> | Igen | Egész szám | A karakterláncrészben kívánt karakterek pozitív száma |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*karakterlánc-pont eredménye*> | Sztring | Adott számú karakterrel rendelkező részkarakterlánc, amely a forráskarakterlánc megadott indexpozíciójától indul |
||||

*Példa*

Ez a példa ötkarakteres részkarakterláncot hoz létre a megadott karakterláncból, a 6-os indexértéktől kezdve:

```
substring('hello world', 6, 5)
```

És ezt az eredményt adja vissza:`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>kivonásAz időből

Több időegység kivonása az időbélyegből.
Lásd [még: getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó karakterlánc |
| <*Intervallum*> | Igen | Egész szám | A kivonandó megadott időegységek száma |
| <*timeUnit (időegység)*> | Igen | Sztring | Az *intervallummal*használandó időegység: "Második", "Perc", "Óra", "Nap", "Hét", "Hónap", "Év" |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg mínusz a megadott időegységek száma |
||||

*1. példa*

Ez a példa egy napot von ki ebből az időbélyegből:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

És ezt az eredményt adja vissza:`"2018-01-01T00:00:00:0000000Z"`

*2. példa*

Ez a példa egy napot von ki ebből az időbélyegből:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

És ezt az eredményt adja vissza az opcionális "D" formátumban:`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>venni

Cikkek visszaküldése a gyűjtemény elejéről.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Karakterlánc vagy tömb | Az a gyűjtemény, amelynek a kívánt elemeket |
| <*Számít*> | Igen | Egész szám | Pozitív egész szám az elölről elkívánt elemek számához |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*>* vagy [<*> részhalmaz]* | Karakterlánc vagy tömb | Olyan karakterlánc vagy tömb, amely az eredeti gyűjtemény elejéről vett elemek megadott számát tartalmaz |
||||

*Példa*

Ezek a példák a megadott számú elemet kapják meg a gyűjtemények elejéről:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

És adja vissza ezeket az eredményeket:

* Első példa:`"abc"`
* Második példa:`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Kullancsok

Adja `ticks` vissza a megadott időbélyeg tulajdonságértékét.
A *kullancs* egy 100 nanoszekundumos.

```
ticks('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeg karakterlánca |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*ticks-szám*> | Egész szám | A megadott időbélyeg óta eltelt kullancsok száma |
||||

<a name="toLower"></a>

### <a name="tolower"></a>lassabbra

Karakterlánc visszaadása kisbetűs formátumban. Ha a karakterlánc egy karaktere nem rendelkezik kisbetűs verzióval, az a karakter változatlan marad a visszaadott karakterláncban.

```
toLower('<text>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A kisbetűs formátumban visszaadandó karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kisbetűs szöveg*> | Sztring | Az eredeti karakterlánc kisbetűs formátumban |
||||

*Példa*

Ez a példa ezt a karakterláncot kisbetűssé alakítja:

```
toLower('Hello World')
```

És ezt az eredményt adja vissza:`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper (felső

Nagybetűs karakterlánc visszaadása. Ha a karakterlánc egyik karaktere nem rendelkezik nagybetűs verzióval, az a karakter változatlan marad a visszaadott karakterláncban.

```
toUpper('<text>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A nagybetűs formátumban visszaadandó karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*nagybetűs szöveg*> | Sztring | Az eredeti karakterlánc nagybetűs formátumban |
||||

*Példa*

Ez a példa ezt a karakterláncot nagybetűssé alakítja:

```
toUpper('Hello World')
```

És ezt az eredményt adja vissza:`"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Ravaszt

Adja vissza az eseményindító kimenetét futásidőben, vagy más JSON-név- és értékpárok értékeit, amelyeket egy kifejezéshez rendelhet.

* Az eseményindító bemenetein belül ez a függvény az előző végrehajtás kimenetét adja vissza.

* Az eseményindító állapotában ez a függvény az aktuális végrehajtás kimenetét adja vissza.

Alapértelmezés szerint a függvény a teljes eseményindító objektumra hivatkozik, de tetszés szerint megadhatja azt a tulajdonságot, amelynek értékét szeretné.
Ez a függvény is rendelkezik a gyorsírási verziók elérhető, lásd [triggerOutputs()](#triggerOutputs) és [triggerBody()](#triggerBody).

```
trigger()
```

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*trigger-kimenet*> | Sztring | Az eseményindító kimenete futásidőben |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Az eseményindító `body` kimenetének visszaadása futásidőben.
A rövidítése. `trigger().outputs.body`
Lásd [trigger()](#trigger).

```
triggerBody()
```

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*trigger-test-kimenet*> | Sztring | Az `body` eseményindító kimenete |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Olyan tömböt ad vissza, amelynek értékei megegyeznek az eseményindító *űrlapadatában* vagy *űrlapkódolású* kimenetében lévő kulcsnévvel.

```
triggerFormDataMultiValues('<key>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*tömb-billentyű-értékekkel*>] | Tömb | Tömb a megadott kulcsnak megfelelő összes értékkel |
||||

*Példa*

Ez a példa létrehoz egy tömböt a "feedUrl" kulcsértékből egy RSS-eseményindító űrlapadatában vagy űrlapkódolású kimenetén:

```
triggerFormDataMultiValues('feedUrl')
```

És ezt a tömböt példaként adja vissza:`["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Egyetlen értékkel rendelkező karakterláncot ad vissza, amely megegyezik az eseményindító *űrlapadatában* vagy *űrlapkódolású* kimenetében szereplő kulcsnévvel.
Ha a függvény egynél több egyezést talál, a függvény hibát okoz.

```
triggerFormDataValue('<key>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kulcs-érték*> | Sztring | A megadott kulcs értéke |
||||

*Példa*

Ez a példa egy karakterláncot hoz létre a "feedUrl" kulcsértékből egy RSS-eseményindító űrlapadatában vagy űrlapkódolású kimenetén:

```
triggerFormDataValue('feedUrl')
```

És ezt a karakterláncot adja vissza példaként:`"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Adja vissza a testet egy adott részhez az eseményindító több részből álló kimenetében.

```
triggerMultipartBody(<index>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Index*> | Igen | Egész szám | A kívánt rész indexértéke |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Szervezet*> | Sztring | A megadott alkatrész teste az eseményindító többrészes kimenetében |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerKimenetek

Adja vissza az eseményindító kimenetét futásidőben, vagy más JSON-név- és értékpárok értékeit.
A rövidítése. `trigger().outputs`
Lásd [trigger()](#trigger).

```
triggerOutputs()
```

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*trigger-kimenet*> | Sztring | Az eseményindító kimenete futásidőben  |
||||

<a name="trim"></a>

### <a name="trim"></a>Berendezés

Távolítsa el a sortávolság és a záró szóközt egy karakterláncból, és adja vissza a frissített karakterláncot.

```
trim('<text>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | Az a karakterlánc, amelynek elvezető és záró szóközevan az eltávolítandó |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissítveSzöveg*> | Sztring | Frissített verzió az eredeti karakterlánchoz, sortávolság vagy záró szóköz nélkül |
||||

*Példa*

Ez a példa eltávolítja a kezdő és záró szóközt a " Hello World " karakterláncból:

```
trim(' Hello World  ')
```

És ezt az eredményt adja vissza:`"Hello World"`

<a name="union"></a>

### <a name="union"></a>Unió

Olyan gyűjteményt ad vissza, amely a megadott gyűjteményből származó *összes* elemet tartalmaz.
Az eredményben való megjelenéshez egy elem bármely, a függvénynek átadott gyűjteményben megjelenhet. Ha egy vagy több elem neve megegyezik, az utolsó ilyen nevű elem jelenik meg az eredményben.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Igen | Tömb vagy objektum, de nem mindkettő | A gyűjtemények, ahonnan az *összes* elemet szeretné |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissítveGyűjtemény*> | Tömb vagy objektum, illetve | Gyűjtemény a megadott gyűjtemények összes elemét tartalmazva - nincs enek |
||||

*Példa*

Ez a példa az alábbi gyűjtemények *összes* elemét beszerzi:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

És ezt az eredményt adja vissza:`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriKomponens

Egy karakterlánc egységes erőforrás-azonosítóval (URI) kódolt verzióját adja vissza úgy, hogy az URL-nem biztonságos karaktereket escape karakterekre cseréli.
Az [encodeUriComponent()](#encodeUriComponent)helyett ezt a függvényt használja.
Bár mindkét funkció ugyanúgy `uriComponent()` működik, előnyben kell részesíteni.

```
uriComponent('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az URI-kódolású formátumra konvertálandó karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kódolt-uri*> | Sztring | Az URI-kódolású karakterlánc escape karakterekkel |
||||

*Példa*

Ez a példa egy URI-kódolású verziót hoz létre ehhez a karakterlánchoz:

```
uriComponent('https://contoso.com')
```

És ezt az eredményt adja vissza:`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Egy egységes erőforrás-azonosító (URI) összetevő bináris verzióját adja vissza.

```
uriComponentToBinary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A konvertálandó URI-kódolású karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*bináris-kódolt-uri*> | Sztring | Az URI-kódolású karakterlánc bináris verziója. A bináris tartalom base64-kódolású és `$content`a . |
||||

*Példa*

Ez a példa létrehozza az URI-kódolású karakterlánc bináris verzióját:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

És ezt az eredményt adja vissza:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Adja vissza az egységes erőforrás-azonosító (URI) kódolású karakterlánc-verzióját, amely hatékonyan dekódolja az URI-kódolású karakterláncot.

```
uriComponentToString('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A dekódoláshoz megadott URI-kódolású karakterlánc |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolt-uri*> | Sztring | Az URI-kódolású karakterlánc dekódolt verziója |
||||

*Példa*

Ez a példa létrehozza az URI-kódolású karakterlánc dekódolt karakterlánc-verzióját:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

És ezt az eredményt adja vissza:`"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Adja `host` vissza az egységes erőforrás-azonosító (URI) értékét.

```
uriHost('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Az az `host` URI, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*állomás-érték*> | Sztring | A `host` megadott URI értéke |
||||

*Példa*

Ez a `host` példa megkeresi ennek az URI-nak az értékét:

```
uriHost('https://www.localhost.com:8080')
```

És ezt az eredményt adja vissza:`"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Adja `path` vissza az egységes erőforrás-azonosító (URI) értékét.

```
uriPath('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Az az `path` URI, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*elérési út értéke*> | Sztring | A `path` megadott URI értéke. Ha `path` nincs értéke, adja vissza a "/" karaktert. |
||||

*Példa*

Ez a `path` példa megkeresi ennek az URI-nak az értékét:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És ezt az eredményt adja vissza:`"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Az `path` egységes `query` erőforrás-azonosító (URI) és értékeinek visszaadása.

```
uriPathAndQuery('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | A kívánt `path` `query` URI és értékek |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*elérési út lekérdezési értéke*> | Sztring | A `path` `query` megadott URI és értékek. Ha `path` nem ad meg értéket, adja vissza a "/" karaktert. |
||||

*Példa*

Ez a `path` példa `query` megkeresi az URI és az értékeket:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És ezt az eredményt adja vissza:`"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Adja `port` vissza az egységes erőforrás-azonosító (URI) értékét.

```
uriPort('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Az az `port` URI, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*port-érték*> | Egész szám | A `port` megadott URI értéke. Ha `port` nem ad meg értéket, adja vissza a protokoll alapértelmezett portját. |
||||

*Példa*

Ez a `port` példa ennek az URI-nak az értékét adja vissza:

```
uriPort('http://www.localhost:8080')
```

És ezt az eredményt adja vissza:`8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Adja `query` vissza az egységes erőforrás-azonosító (URI) értékét.

```
uriQuery('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Az az `query` URI, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*lekérdezés-érték*> | Sztring | A `query` megadott URI értéke |
||||

*Példa*

Ez a `query` példa ennek az URI-nak az értékét adja vissza:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És ezt az eredményt adja vissza:`"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Adja `scheme` vissza az egységes erőforrás-azonosító (URI) értékét.

```
uriScheme('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Az az `scheme` URI, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*rendszer-érték*> | Sztring | A `scheme` megadott URI értéke |
||||

*Példa*

Ez a `scheme` példa ennek az URI-nak az értékét adja vissza:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És ezt az eredményt adja vissza:`"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow között

Adja vissza az aktuális időbélyeget.

```
utcNow('<format>')
```

Tetszés szerint más formátumot is megadhat a <*formátumú*> paraméterrel.


| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumú megnevező](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumminta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), amely megfelel az [ISO 8601-nek,](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információkat. |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*aktuális időbélyeg*> | Sztring | Az aktuális dátum és idő |
||||

*1. példa*

Tegyük fel, hogy ma van 2018.
Ez a példa az aktuális időbélyeget kapja:

```
utcNow()
```

És ezt az eredményt adja vissza:`"2018-04-15T13:00:00.0000000Z"`

*2. példa*

Tegyük fel, hogy ma van 2018.
Ez a példa az aktuális időbélyeget kapja le a választható "D" formátumban:

```
utcNow('D')
```

És ezt az eredményt adja vissza:`"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Változók

Adja vissza egy megadott változó értékét.

```
variables('<variableName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*változóNév*> | Igen | Sztring | Annak a változónak a neve, amelynek értékét |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*változó-érték*> | Bármelyik | A megadott változó értéke |
||||

*Példa*

Tegyük fel, hogy egy "numItems" változó aktuális értéke 20.
Ez a példa a változó egész értékét kapja meg:

```
variables('numItems')
```

És ezt az eredményt adja vissza:`20`

<a name="workflow"></a>

### <a name="workflow"></a>munkafolyamat

A munkafolyamat összes részletének visszaadása a futási idő alatt.

```
workflow().<property>
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Tulajdonság*> | Nem | Sztring | Annak a munkafolyamat-tulajdonságnak a neve, amelynek értékét szeretné <p>A munkafolyamat-objektumok a következő tulajdonságokkal rendelkeznek: **név**, **típus**, **id**, **hely**és **futtatás.** A **futtatási** tulajdonság értéke egyben olyan objektum is, amely a következő tulajdonságokkal rendelkezik: **név**, **típus**és **id**. |
|||||

*Példa*

Ez a példa egy munkafolyamat aktuális futásának nevét adja vissza:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

JSON-objektumot tartalmazó karakterlánc XML-verzióját adja vissza.

```
xml('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A konvertálandó JSON-objektummal rendelkező karakterlánc <p>A JSON-objektumnak csak egy gyökértulajdonsággal kell rendelkeznie, amely nem lehet tömb. <br>A fordított perjel\\karaktert ( ) használja a dupla idézőjel ("). |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*xml-verzió*> | Objektum | A megadott karakterlánc vagy JSON-objektum kódolt XML-je |
||||

*1. példa*

Ez a példa létrehozza a karakterlánc XML-verzióját, amely JSON-objektumot tartalmaz:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

És visszaadja ezt az eredményt XML:

```xml
<name>Sophia Owen</name>
```

*2. példa*

Tegyük fel, hogy rendelkezik ezzel a JSON-objektummal:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Ez a példa xml-t hoz létre egy Olyan karakterlánchoz, amely ezt a JSON-objektumot tartalmazza:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

És visszaadja ezt az eredményt XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Xpath

Ellenőrizze az XML-ben az XPath (XML-elérési út nyelve) kifejezésnek megfelelő csomópontokat vagy értékeket, és adja vissza a megfelelő csomópontokat vagy értékeket. Az XPath-kifejezések (vagy csak "XPath") segítségével navigálhat az XML-dokumentumszerkezetben, így csomópontokat vagy értékeket választhat ki az XML-tartalomban.

```
xpath('<xml>', '<xpath>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Igen | Bármelyik | Az XPath-kifejezésértéknek megfelelő csomópontok at vagy értékeket kereső XML-karakterlánc |
| <*Xpath*> | Igen | Bármelyik | A megfelelő XML-csomópontok vagy értékek keresésére használt XPath-kifejezés |
|||||

| Visszatérítési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*xml-csomópont*> | XML | XML-csomópont, ha csak egyetlen csomópont felel meg a megadott XPath-kifejezésnek |
| <*Érték*> | Bármelyik | Az XML-csomópont értéke, ha csak egyetlen érték felel meg a megadott XPath-kifejezésnek |
| [<*xml-node1>,* <*xml-node2*>, ...] </br>– vagy – </br>[<*érték1*>, <*érték2*>, ...] | Tömb | A megadott XPath-kifejezésnek megfelelő XML-csomópontokkal vagy értékekkel rendelkező tömb |
||||

*1. példa*

Ez a példa megkeresi `<name></name>` azokat a csomópontokat, amelyek megfelelnek a megadott argumentumokban lévő csomópontnak, és egy tömböt ad vissza az okkal a csomópontértékekkel:

`xpath(xml(parameters('items')), '/produce/item/name')`

Itt vannak az érvek:

* Az XML-t tartalmazó "items" karakterlánc:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  A példa a [parameters()](#parameters) függvény t használja az XML-karakterlánc bekéseléséhez az "elemek" argumentumból, de a karakterláncot XML formátumba is konvertálnia kell az [xml()](#xml) függvénnyel.

* Ez az XPath-kifejezés, amely karakterláncként kerül átadásra:

  `"/produce/item/name"`

Itt van az eredmény tömb a `<name></name`csomópontok, amelyek megfelelnek:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*2. példa*

Az 1. `<count></count>` `sum()`

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

És ezt az eredményt adja vissza:`30`

*3. példa*

Ebben a példában mindkét kifejezés olyan csomópontokat keres, amelyek megfelelnek a `<location></location>` csomópontnak a megadott argumentumokban, amelyek tartalmazzák az XML-t egy névtérrel. 

> [!NOTE]
>
> Ha kódnézetben dolgozik, a fordított perjel (\\) karakterrel kerülje el a dupla idézőjelet ("). 
> Például escape karaktereket kell használnia, amikor egy kifejezést JSON-karakterláncként szerializál. 
> Ha azonban a Logic App Designerben vagy a kifejezésszerkesztőben dolgozik, nem kell kilépnie a dupla idézőjelből, mert a fordított perjel karakter automatikusan hozzáadódik az alapul szolgáló definícióhoz, például:
> 
> * Kód nézet:`xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Kifejezésszerkesztő:`xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
> 
> A következő példák a kifejezésszerkesztőben megadott kifejezésekre vonatkoznak.

* *1. kifejezés*

  `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

* *2. kifejezés*

  `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

Itt vannak az érvek:

* Ez az XML-dokumentumnévteret tartalmazó `xmlns="http://contoso.com"`XML:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Itt van az XPath-kifejezés:

  * `/*[name()="file"]/*[name()="location"]`

  * `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]`

Itt van az eredménycsomópont, `<location></location>` amely megfelel a csomópontnak:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*4. példa*

A 3. `<location></location>`

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

És ezt az eredményt adja vissza:`"Paris"`

## <a name="next-steps"></a>További lépések

További információ a [munkafolyamat-definíció nyelvéről](../logic-apps/logic-apps-workflow-definition-language.md)
