---
title: A függvények hivatkozási útmutatója kifejezésekben
description: Útmutató a függvények Azure Logic Apps és az automatizáláshoz kifejezésekben való működéséhez
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 87798c93bbc1098daea2f7258a3af3e26bb4bb93
ms.sourcegitcommit: 85d52b799621d35d7df32c2cb1e339071c619bb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2020
ms.locfileid: "76966095"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>A függvények kifejezésekben való használatát ismertető útmutató a Azure Logic Apps és az energiagazdálkodás automatizálásához

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a [Power automatizálható](https://docs.microsoft.com/flow/getting-started)munkafolyamat-definíciók esetében egyes [kifejezések](../logic-apps/logic-apps-workflow-definition-language.md#expressions) olyan futásidejű műveletek értékeit kapják meg, amelyek esetleg még nem léteznek a munkafolyamat indításakor. Ezeknek az értékeknek a megadásához vagy a kifejezések értékeinek feldolgozásához használhatja a munkafolyamat- [definíciós nyelv](../logic-apps/logic-apps-workflow-definition-language.md)által biztosított *függvényeket* . 

> [!NOTE]
> Ez a hivatkozási oldal a Azure Logic Apps és az energiagazdálkodási automatizálásra is vonatkozik, de a Azure Logic Apps dokumentációjában jelenik meg. Bár ez az oldal kifejezetten a Logic apps szolgáltatásra vonatkozik, ezek a függvények a folyamatok és a Logic apps esetében is működnek. További információ a functions és a kifejezésekről a Power automatizálásban: [kifejezések használata feltételekben](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Például kiszámíthatja az értékeket matematikai függvények, például az [Add () függvény](../logic-apps/workflow-definition-language-functions-reference.md#add)használatával, ha az egész számot vagy az úszót szeretné megadni. Az alábbi példa a functions által elvégezhető műveleteket is végrehajtja:

| Tevékenység | Függvény szintaxisa | Eredmény |
| ---- | --------------- | ------ |
| Karakterláncot ad vissza kisbetűs formátumban. | toLower (' <*text*> ') <p>Például: toLower (' Hello ') | Hello |
| Globálisan egyedi azonosító (GUID) visszaadása. | GUID () |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

A függvények [általános célú](#ordered-by-purpose)kereséséhez tekintse át az alábbi táblázatokat. Az egyes függvények részletes ismertetését lásd a [betűrendes listán](#alphabetical-list).

> [!NOTE]
> A paraméter-definíciók szintaxisa egy olyan kérdőjel (?), amely egy paraméter után jelenik meg, az azt jelenti, hogy a paraméter nem kötelező.
> Lásd például: [getFutureTime ()](#getFutureTime).

## <a name="functions-in-expressions"></a>Függvények a kifejezésekben

Ha szeretné megmutatni, hogyan használható egy függvény egy kifejezésben, ez a példa azt mutatja be, hogyan kérhető le az érték a `customerName` paraméterből, és hogyan rendelhető hozzá az érték a `accountName` tulajdonsághoz egy kifejezés [Parameters ()](#parameters) függvényének használatával:

```json
"accountName": "@parameters('customerName')"
```

Íme néhány további általános módszer, amelyekkel a függvények a kifejezésekben használhatók:

| Tevékenység | Függvény szintaxisa egy kifejezésben |
| ---- | -------------------------------- |
| Hajtson végre munkát egy elemmel az elem függvénybe való átadásával. | "\@<*függvénynév*> (<*elem*>)" |
| 1. szerezze be a *parameterName*értékét a beágyazott `parameters()` függvény használatával. </br>2. végezze el a munkát az eredménnyel, ha ezt az értéket a *függvénynév*értékre adja át. | "\@<*függvénynév*> (parameters (' <*parameterName*> '))" |
| 1. a beágyazott belső függvény *függvénynév*eredményének beolvasása. </br>2. adja át az eredményt a külső függvény *functionName2*. | "\@<*functionName2*> (<*függvénynév*> (<*elem*>))" |
| 1. a *függvénynév*eredményének beolvasása. </br>2. mivel az eredmény egy olyan objektum, amely *propertyName*tulajdonsággal rendelkezik, szerezze be a tulajdonság értékét. | "\@<*függvénynév*> (<*elem*>). <*propertyName*>" |
|||

Az `concat()` függvény például paraméterekként két vagy több karakterlánc-értéket is igénybe vehet. Ez a függvény egyetlen sztringbe egyesíti ezeket a karakterláncokat.
Átadhat karakterlánc-literálokat (például "Sophia" és "Owen"), hogy egy kombinált karakterláncot ("SophiaOwen") kapjon:

```json
"customerName": "@concat('Sophia', 'Owen')"
```

A paraméterekből karakterlánc-értékeket is beszerezhet. Ez a példa a `parameters()` függvényt használja az egyes `concat()` paraméterekben, valamint a `firstName` és `lastName` paramétereket. Ezután adja át az eredményül kapott karakterláncokat a `concat()` függvénynek, hogy egy kombinált karakterláncot kapjon, például "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Mindkét példa mindkét példát hozzárendeli az eredményt a `customerName` tulajdonsághoz.

Az alábbi, általános célú függvények használhatók, vagy [betűrendbe](#alphabetical-list)rendezheti a függvényeket.

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Sztringfüggvények

A sztringek használatához használhatja ezeket a karakterlánc-függvényeket és néhány [gyűjteményi funkciót](#collection-functions)is.
A karakterlánc-függvények csak karakterláncokon működnek.

| Karakterlánc-függvény | Tevékenység |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Egyesítse kettő vagy több karakterláncot, és állítsa vissza az egyesített karakterláncot. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Győződjön meg arról, hogy a karakterlánc a megadott alkarakterlánccal végződik-e. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Szám visszaadása karakterláncként a megadott formátum alapján |
| [GUID](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Globálisan egyedi azonosító (GUID) létrehozása karakterláncként. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Egy alsztring kezdő pozíciójának visszaadása. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Egy alsztring utolsó előfordulásának kezdő pozíciójának visszaadása. |
| [csere](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Cserélje le az alsztringet a megadott sztringre, és adja vissza a frissített karakterláncot. |
| [felosztás](../logic-apps/workflow-definition-language-functions-reference.md#split) | Egy olyan tömböt ad vissza, amely vesszővel elválasztott alsztringeket tartalmaz, egy nagyobb karakterláncból, amely az eredeti karakterlánc megadott elválasztói karaktere alapján van megadva. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Győződjön meg róla, hogy egy sztring egy adott alkarakterlánccal kezdődik-e. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Karakterek visszaadása egy karakterláncból a megadott pozíciótól kezdődően. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Karakterláncot ad vissza kisbetűs formátumban. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Karakterláncot ad vissza nagybetűs formátumban. |
| [Trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Távolítsa el a kezdő és záró szóközt egy karakterláncból, és küldje vissza a frissített karakterláncot. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Gyűjtési függvények

A gyűjtemények, általában tömbök, karakterláncok és esetenként a szótárak használatával a következő gyűjtemény-függvények használhatók.

| Gyűjtési függvény | Tevékenység |
| ------------------- | ---- |
| [tartalmaz](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Győződjön meg arról, hogy egy gyűjteménynek van-e konkrét eleme. |
| [üres](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Győződjön meg arról, hogy a gyűjtemény üres. |
| [első](../logic-apps/workflow-definition-language-functions-reference.md#first) | Egy gyűjtemény első elemének visszaadása. |
| [kereszteződés](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Olyan gyűjteményt ad vissza, amely *csak* a megadott gyűjtemények közös elemeit tartalmazta. |
| [elem](../logic-apps/workflow-definition-language-functions-reference.md#item) | Egy tömbön belüli ismétlődő művelet során a művelet aktuális iterációjában a tömb aktuális elemét adja vissza. |
| [csatlakozás](../logic-apps/workflow-definition-language-functions-reference.md#join) | Egy olyan sztringet ad vissza, amely egy tömb *összes* elemét a megadott karakterrel elválasztva. |
| [utolsó](../logic-apps/workflow-definition-language-functions-reference.md#last) | Egy gyűjtemény utolsó elemének visszaadása. |
| [hossza](../logic-apps/workflow-definition-language-functions-reference.md#length) | Egy sztringben vagy tömbben lévő elemek számának visszaadása. |
| [kihagyása](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Elemek eltávolítása egy gyűjtemény elejéről, és *az összes többi* elem visszaadása. |
| [eltarthat](../logic-apps/workflow-definition-language-functions-reference.md#take) | Elemek visszaküldése egy gyűjtemény elejéről. |
| [Union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Olyan gyűjteményt ad vissza, amely a megadott gyűjtemények *összes* elemét tartalmazta. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logikai összehasonlító függvények

A feltételekkel való együttműködéshez hasonlítsa össze az értékeket és a kifejezés eredményét, vagy értékelje ki a különböző típusú Logikákat, ezeket a logikai összehasonlító függvényeket használhatja.
Az egyes függvényekkel kapcsolatos teljes referenciáért tekintse meg a [betűrendes listát](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Logikai összehasonlító függvény | Tevékenység |
| --------------------------- | ---- |
| [és](../logic-apps/workflow-definition-language-functions-reference.md#and) | Győződjön meg arról, hogy az összes kifejezés igaz-e. |
| [egyenlő](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Győződjön meg arról, hogy mindkét érték egyenértékű-e. |
| [nagyobb](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Győződjön meg arról, hogy az első érték nagyobb, mint a második érték. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Győződjön meg arról, hogy az első érték nagyobb vagy egyenlő, mint a második érték. |
| [Ha](../logic-apps/workflow-definition-language-functions-reference.md#if) | Győződjön meg arról, hogy a kifejezés igaz vagy hamis. Az eredmény alapján adja vissza a megadott értéket. |
| [kisebb](../logic-apps/workflow-definition-language-functions-reference.md#less) | Győződjön meg arról, hogy az első érték kisebb a második értéknél. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Győződjön meg arról, hogy az első érték kisebb vagy egyenlő, mint a második érték. |
| [nem](../logic-apps/workflow-definition-language-functions-reference.md#not) | Győződjön meg arról, hogy egy kifejezés hamis-e. |
| [vagy](../logic-apps/workflow-definition-language-functions-reference.md#or) | Győződjön meg arról, hogy legalább egy kifejezés igaz értékű-e. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Átalakítási függvények

Az érték típusának vagy formátumának módosításához használhatja ezeket az átalakítási függvényeket.
Egy logikai értéket például egész számra válthat.
További információ arról, hogyan kezeli a Logic Apps a tartalom típusát az átalakítás során: [tartalomtípusok kezelése](../logic-apps/logic-apps-content-type.md).
Az egyes függvényekkel kapcsolatos teljes referenciáért tekintse meg a [betűrendes listát](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Átalakítási függvény | Tevékenység |
| ------------------- | ---- |
| [tömb](../logic-apps/workflow-definition-language-functions-reference.md#array) | Tömb visszaadása egyetlen megadott bemenetből. Több bemenet esetén lásd: [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [Base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Egy sztring Base64 kódolású verziójának visszaadása. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Egy Base64 kódolású karakterlánc bináris verziójának visszaadása. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Egy Base64 kódolású karakterlánc karakterlánc-verziójának visszaadása. |
| [bináris](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Egy bemeneti érték bináris verziójának visszaadása. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Egy bemeneti érték logikai verziójának visszaadása. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Tömb visszaadása több bemenetből. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Egy bemeneti értékhez tartozó adat URI-azonosítójának visszaadása. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Egy adaturi-fájl bináris verziójának visszaadása. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Egy adaturi-azonosító karakterlánc-verziójának visszaadása. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Egy Base64 kódolású karakterlánc karakterlánc-verziójának visszaadása. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Egy adaturi-fájl bináris verziójának visszaadása. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Olyan karakterláncot ad vissza, amely lecseréli az Escape-karaktereket a dekódolású verziókra. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Egy olyan karakterláncot ad vissza, amely lecseréli az URL-nem biztonságos karaktereket Escape-karakterekkel. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Lebegőpontos szám visszaadása egy bemeneti értékhez. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Egy sztring egész változatának visszaadása. |
| [JSON](../logic-apps/workflow-definition-language-functions-reference.md#json) | A JavaScript Object Notation (JSON) típusú értéket vagy objektumot adja vissza egy karakterlánc vagy XML számára. |
| [karakterlánc](../logic-apps/workflow-definition-language-functions-reference.md#string) | Egy bemeneti érték karakterlánc-verziójának visszaadása. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Az URI-kódolású verziót adja vissza egy bemeneti értékhez az URL-nem biztonságos karakterek a Escape-karakterekkel való lecserélésével. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Egy URI-kódolású karakterlánc bináris verziójának visszaadása. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | URI-kódolású karakterlánc karakterlánc-verziójának visszaadása. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Egy sztring XML-verziójának visszaadása. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Matematikai függvények

Az egész számokkal és az úszókkal való munkavégzéshez használhatja ezeket a matematikai függvényeket.
Az egyes függvényekkel kapcsolatos teljes referenciáért tekintse meg a [betűrendes listát](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Matematikai függvény | Tevékenység |
| ------------- | ---- |
| [hozzáadása](../logic-apps/workflow-definition-language-functions-reference.md#add) | Az eredmény visszaadása két szám hozzáadásával. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Az eredmény visszaadása két szám elosztásával. |
| [Max](../logic-apps/workflow-definition-language-functions-reference.md#max) | A legmagasabb értéket adja vissza számokból vagy tömbből. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | A legkisebb értéket adja vissza számokból vagy tömbből. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | A maradékot a két szám felosztásával állítsa vissza. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | A termék visszaküldése két szám szorzatával. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Egy véletlenszerű egész számot ad vissza egy megadott tartományból. |
| [tartomány](../logic-apps/workflow-definition-language-functions-reference.md#range) | Egy egész tömböt ad vissza, amely egy megadott egész számból indul. |
| [Sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Az eredmény visszaadása a második számnak az első számból való kivonásával. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Dátum és időpont függvényei

A dátumok és időpontok használatához használhatja ezeket a dátum-és időfüggvényeket.
Az egyes függvényekkel kapcsolatos teljes referenciáért tekintse meg a [betűrendes listát](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Dátum vagy idő függvény | Tevékenység |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adja hozzá a napok számát egy időbélyeghez. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adja meg az időbélyegzőhöz tartozó órák számát. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adjon hozzá néhány percet egy időbélyeghez. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adjon meg néhány másodpercet egy időbélyeghez. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Több időegység hozzáadása egy időbélyeghez. Lásd még: [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Az időbélyeget az egyezményes világidő (UTC) alapján alakítsa át a cél időzónára. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | A forrás időzóna időbélyegének konvertálása a cél időzónára. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | A forrás időzóna időbélyegének konvertálása az egyezményes világidő (UTC) szerinti időpontra |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | A hónap napjának visszaadása időbélyeg alapján. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Egy időbélyegből a hét napjának visszaadása. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Az év napjának visszaadása egy időbélyegből. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Az időbélyegből származó dátum visszaadása. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Az aktuális időbélyeg és a megadott időegységek visszaadása. Lásd még: [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Az aktuális időbélyeg visszaadása a megadott időegységek mínusz. Lásd még: [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Egy időbélyeghez tartozó nap kezdetének visszaadása. |
| [Óra kezdete](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Az óra kezdetének visszaadása egy időbélyeghez. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | A hónap kezdetének visszaadása egy időbélyeghez. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Több időegység kivonása egy időbélyegből. Lásd még: [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Egy megadott időbélyeg `ticks` tulajdonságának értékét adja vissza. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Az aktuális időbélyeg visszaadása karakterláncként. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Munkafolyamat-függvények

Ezek a munkafolyamat-függvények a következőket teszik lehetővé:

* A munkafolyamat-példány részleteinek lekérése futásidőben.
* A Logic apps vagy folyamatok létrehozásához használt bemenetekkel dolgozhat.
* Az eseményindítók és műveletek kimenetének hivatkozása.

Hivatkozhat például egy művelet kimenetére, és az adatokat egy későbbi műveletben is használhatja.
Az egyes függvényekkel kapcsolatos teljes referenciáért tekintse meg a [betűrendes listát](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Munkafolyamat-függvény | Tevékenység |
| ----------------- | ---- |
| [művelet](../logic-apps/workflow-definition-language-functions-reference.md#action) | Az aktuális művelet kimenetének visszaadása futásidőben vagy más JSON-név és érték párokból származó értékek alapján. Lásd még: [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Egy művelet `body` kimenetének visszaadása futásidőben. Lásd még: [törzs](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Egy művelet kimenetének visszaadása futásidőben. Lásd: [kimenetek](../logic-apps/workflow-definition-language-functions-reference.md#outputs) és [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Egy művelet kimenetének visszaadása futásidőben vagy más JSON-név és érték párokból származó értékként. Lásd még: [művelet](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [törzse](#body) | Egy művelet `body` kimenetének visszaadása futásidőben. Lásd még: [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Hozzon létre egy tömböt azokkal az értékekkel, amelyek megfelelnek egy kulcsnévnek az *űrlap-* vagy az *űrlap kódolású* művelet kimenetében. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Egyetlen olyan értéket adjon vissza, amely megfelel a művelet *űrlap-* vagy *űrlap-kódolású kimenetében*szereplő kulcs nevének. |
| [elem](../logic-apps/workflow-definition-language-functions-reference.md#item) | Egy tömbön belüli ismétlődő művelet során a művelet aktuális iterációjában a tömb aktuális elemét adja vissza. |
| [elemek](../logic-apps/workflow-definition-language-functions-reference.md#items) | Ha egy foreach belül vagy a hurokba kerül, az aktuális elemet adja vissza a megadott hurokból.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Amíg a cikluson belül be nem fejeződik, az aktuális iteráció indexének értékét kell visszaadnia. Ezt a függvényt a hurkok keretén belül ágyazva is használhatja. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | A visszahívási URL-cím visszaadása, amely egy triggert vagy műveletet hív meg. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Egy művelet adott részének törzsét adja vissza egy olyan kimenetben, amely több részből áll. |
| [kimenetek](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Egy művelet kimenetének visszaadása futásidőben. |
| [paraméterek](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | A munkafolyamat-definícióban leírt paraméterek értékének visszaadása. |
| [találat](../logic-apps/workflow-definition-language-functions-reference.md#result) | A megadott hatókörű műveletben lévő összes művelet bemeneteit és kimeneteit adja vissza, például `For_each`, `Until`és `Scope`. |
| [eseményindító](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Egy trigger kimenetének visszaadása futásidőben vagy más JSON-név és érték párokból. Lásd még: [triggerOutputs](#triggerOutputs) és [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Egy trigger `body` kimenetének visszaadása futásidőben. Lásd: [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Egy olyan értéket adjon vissza, amely megfelel egy kulcsnévnek a *Form-* vagy az *űrlap-kódolású* trigger kimenetében. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Egy trigger többrészes kimenetének adott részének törzsét adja vissza. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Hozzon létre egy tömböt, amelynek értékei megegyeznek a kulcs nevével az *űrlap – adatok* vagy az *űrlap kódolású* trigger kimenetei között. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Egy trigger kimenetének visszaadása futásidőben vagy más JSON-név és érték párokból származó értékek alapján. Lásd: [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [változók](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Egy megadott változó értékét adja vissza. |
| [munkafolyamat](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | A munkafolyamat összes adatának visszaküldése a Futtatás ideje alatt. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI-elemzési függvények

Az egységes erőforrás-azonosítók (URI-k) használata és a különböző tulajdonságértékek beolvasása az URI-k esetében ezeket az URI-elemzési funkciókat használhatja.
Az egyes függvényekkel kapcsolatos teljes referenciáért tekintse meg a [betűrendes listát](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| URI-elemzési függvény | Tevékenység |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Egy egységes erőforrás-azonosító (URI) `host` értékének visszaadása. |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Egy egységes erőforrás-azonosító (URI) `path` értékének visszaadása. |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Egy egységes erőforrás-azonosító (URI) `path` és `query` értékének visszaadása. |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Egy egységes erőforrás-azonosító (URI) `port` értékének visszaadása. |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Egy egységes erőforrás-azonosító (URI) `query` értékének visszaadása. |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Egy egységes erőforrás-azonosító (URI) `scheme` értékének visszaadása. |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulációs függvények: JSON & XML

A JSON-objektumok és az XML-csomópontok használatához ezeket a manipulációs funkciókat használhatja.
Az egyes függvényekkel kapcsolatos teljes referenciáért tekintse meg a [betűrendes listát](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Manipulációs függvény | Tevékenység |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Adjon hozzá egy tulajdonságot és annak értékét, vagy név-érték párokat egy JSON-objektumhoz, és adja vissza a frissített objektumot. |
| [összefonódik](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Az első nem null értéket ad vissza egy vagy több paraméterből. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Eltávolít egy tulajdonságot egy JSON-objektumból, és visszaküldi a frissített objektumot. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Adja meg egy JSON-objektum tulajdonságának értékét, és adja vissza a frissített objektumot. |
| [XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Az XML-t az XPath (XML-elérésiút) kifejezésnek megfelelő csomópontok vagy értékek esetében adja meg, és adja vissza a megfelelő csomópontokat vagy értékeket. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Minden függvény – betűrendes lista

Ez a szakasz felsorolja az összes rendelkezésre álló függvényt betűrendbe rendezve.

<a name="action"></a>

### <a name="action"></a>action

Visszaadja az *aktuális* művelet kimenetét futásidőben vagy más JSON-név-érték párokból, amelyeket hozzárendelhet egy kifejezéshez.
Alapértelmezés szerint ez a függvény a teljes műveleti objektumra hivatkozik, de igény szerint megadhat egy olyan tulajdonságot, amelynek a kívánt értéke.
Lásd még: [műveletek ()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

A `action()` függvényt csak a következő helyeken használhatja:

* Webhook-művelet `unsubscribe` tulajdonsága, amely az eredeti `subscribe` kérelem eredményének elérésére szolgál.
* Egy művelet `trackedProperties` tulajdonsága
* Egy művelet `do-until` hurok feltétele

```
action()
action().outputs.body.<property>
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*tulajdonság*> | Nem | Sztring | A műveleti objektum azon tulajdonságának neve, amelynek a kívánt értéke: **név**,kezdési **időpont, Befejezés**, **bemenet**, **kimenet**, **állapot**, **kód**, **trackingId**és **clientTrackingId**. A Azure Portal a tulajdonságok megkereséséhez tekintse át az adott futtatási előzmények részleteit. További információ: [REST API – munkafolyamat-futtatási műveletek](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | -----| ----------- |
| <*művelet – kimeneti*> | Sztring | Az aktuális művelet vagy tulajdonság kimenete |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Egy művelet `body` kimenetének visszaadása futásidőben.
`actions('<actionName>').outputs.body`rövidítése.
Lásd: [Body ()](#body) és [Actions ()](#actions).

```
actionBody('<actionName>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*műveletnév*> | Igen | Sztring | A művelet `body` kívánt kimenetének neve |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | -----| ----------- |
| <*művelet – törzs – kimeneti*> | Sztring | A megadott művelet `body` kimenete |
||||

*Példa*

Ez a példa lekéri a Twitter-művelet `body` kimenetét `Get user`:

```
actionBody('Get_user')
```

És visszaadja ezt az eredményt:

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

### <a name="actionoutputs"></a>actionOutputs

Egy művelet kimenetének visszaadása futásidőben.  a és a `actions('<actionName>').outputs`rövidítése. Lásd: [műveletek ()](#actions). A `actionOutputs()` függvény feloldja a Logic app Designerben `outputs()`, ezért a [kimenetek ()](#outputs)helyett érdemes a `actionOutputs()`. Habár mindkét függvény ugyanúgy működik, `outputs()` előnyben részesített.

```
actionOutputs('<actionName>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*műveletnév*> | Igen | Sztring | A művelet kívánt kimenetének neve |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | -----| ----------- |
| <*kimeneti*> | Sztring | A megadott művelet kimenete |
||||

*Példa*

Ez a példa a Twitter-művelet `Get user`kimenetét kéri le:

```
actionOutputs('Get_user')
```

És visszaadja ezt az eredményt:

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

### <a name="actions"></a>Műveletek

Egy művelet kimenetének visszaadása futásidőben vagy más JSON-név és érték párokból származó értékekkel, amelyeket hozzárendelhet egy kifejezéshez. Alapértelmezés szerint a függvény a teljes művelet objektumra hivatkozik, de igény szerint megadhat egy olyan tulajdonságot, amelynek a kívánt értéke.
A Gyorsírási verziókhoz lásd: [actionBody ()](#actionBody), [actionOutputs ()](#actionOutputs)és [Body ()](#body).
Az aktuális művelethez lásd: [Action ()](#action).

> [!NOTE]
> Korábban a `actions()` függvényt vagy a `conditions` elemet is használhatja, ha megadja, hogy egy művelet egy másik művelet kimenete alapján futott-e. A műveletek közötti explicit függőségek kinyilvánítása érdekében azonban a függő művelet `runAfter` tulajdonságát kell használnia.
> Ha többet szeretne megtudni a `runAfter` tulajdonságról, tekintse meg [a hibák befogása és kezelése a runAfter tulajdonsággal](../logic-apps/logic-apps-workflow-definition-language.md)című témakört.

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*műveletnév*> | Igen | Sztring | Annak a műveleti objektumnak a neve, amelynek a kimenetét szeretné  |
| <*tulajdonság*> | Nem | Sztring | A műveleti objektum azon tulajdonságának neve, amelynek a kívánt értéke: **név**,kezdési **időpont, Befejezés**, **bemenet**, **kimenet**, **állapot**, **kód**, **trackingId**és **clientTrackingId**. A Azure Portal a tulajdonságok megkereséséhez tekintse át az adott futtatási előzmények részleteit. További információ: [REST API – munkafolyamat-futtatási műveletek](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | -----| ----------- |
| <*művelet – kimeneti*> | Sztring | A megadott művelet vagy tulajdonság kimenete |
||||

*Példa*

Ez a példa lekéri a `status` tulajdonság értékét a Twitter-művelet `Get user` futásidőben:

```
actions('Get_user').outputs.body.status
```

És visszaadja ezt az eredményt: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Az eredmény visszaadása két szám hozzáadásával.

```
add(<summand_1>, <summand_2>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*summand_1*> <*summand_2*> | Igen | Egész szám, lebegőpontos vagy vegyes | A hozzáadandó számok |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | -----| ----------- |
| <*eredmény-sum*> | Egész vagy lebegőpontos | A megadott számok hozzáadásának eredménye |
||||

*Példa*

Ez a példa hozzáadja a megadott számokat:

```
add(1, 1.5)
```

És visszaadja ezt az eredményt: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adja hozzá a napok számát egy időbélyeghez.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*nap*> | Igen | Egész szám | A hozzáadni kívánt napok pozitív vagy negatív száma |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | Az időbélyeg és a megadott számú nap  |
||||

*1. példa*

Ez a példa 10 napot ad hozzá a megadott időbélyeghez:

```
addDays('2018-03-15T13:00:00Z', 10)
```

És visszaadja ezt az eredményt: `"2018-03-25T00:00:0000000Z"`

*2. példa*

Ez a példa öt napot kivon a megadott időbélyegből:

```
addDays('2018-03-15T00:00:00Z', -5)
```

És visszaadja ezt az eredményt: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adja meg az időbélyegzőhöz tartozó órák számát.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*óra*> | Igen | Egész szám | A hozzáadni kívánt órák pozitív vagy negatív száma |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | Az időbélyeg és a megadott számú óra  |
||||

*1. példa*

Ez a példa 10 órát ad hozzá a megadott időbélyeghez:

```
addHours('2018-03-15T00:00:00Z', 10)
```

És visszaadja ezt az eredményt: `"2018-03-15T10:00:0000000Z"`

*2. példa*

Ez a példa öt órát kivon a megadott időbélyegből:

```
addHours('2018-03-15T15:00:00Z', -5)
```

És visszaadja ezt az eredményt: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adjon hozzá néhány percet egy időbélyeghez.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*perc*> | Igen | Egész szám | A hozzáadni kívánt percek pozitív vagy negatív száma |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | Az időbélyeg és a megadott számú perc |
||||

*1. példa*

Ez a példa 10 percet ad hozzá a megadott időbélyeghez:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

És visszaadja ezt az eredményt: `"2018-03-15T00:20:00.0000000Z"`

*2. példa*

Ez a példa öt percet kivon a megadott időbélyegből:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

És visszaadja ezt az eredményt: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Adjon hozzá egy tulajdonságot és annak értékét, vagy név-érték párokat egy JSON-objektumhoz, és adja vissza a frissített objektumot. Ha a tulajdonság már létezik futásidőben, a függvény meghiúsul, és hibát jelez.

```
addProperty(<object>, '<property>', <value>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*objektum*> | Igen | Objektum | A JSON-objektum, amelyben hozzá kíván adni egy tulajdonságot |
| <*tulajdonság*> | Igen | Sztring | A hozzáadni kívánt tulajdonság neve |
| <*érték*> | Igen | Bármelyik | A tulajdonság értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – objektum*> | Objektum | A frissített JSON-objektum a megadott tulajdonsággal |
||||

Ha egy gyermek tulajdonságot meglévő tulajdonsághoz szeretne adni, használja a következő szintaxist:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*objektum*> | Igen | Objektum | A JSON-objektum, amelyben hozzá kíván adni egy tulajdonságot |
| <*szülő-tulajdonság*> | Igen | Sztring | Annak a szülő tulajdonságnak a neve, amelyhez hozzá kívánja adni a gyermek tulajdonságot |
| <*gyermek-tulajdonság*> | Igen | Sztring | A hozzáadandó gyermek tulajdonság neve |
| <*érték*> | Igen | Bármelyik | A megadott tulajdonsághoz beállított érték |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – objektum*> | Objektum | A frissített JSON-objektum, amelynek a tulajdonságát beállította |
||||

*1. példa*

Ez a példa hozzáadja a `middleName` tulajdonságot egy JSON-objektumhoz, amelyet a rendszer a JSON [()](#json) függvény használatával konvertál a karakterláncból a JSON-ra. Az objektum már tartalmazza a `firstName` és a `surName` tulajdonságokat. A függvény hozzárendeli a megadott értéket az új tulajdonsághoz, és visszaadja a frissített objektumot:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Itt látható az aktuális JSON-objektum:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Itt látható a frissített JSON-objektum:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*2. példa*

Ez a példa hozzáadja a `middleName` Child tulajdonságot egy JSON-objektum meglévő `customerName` tulajdonságához, amelyet a rendszer a JSON [()](#json) függvénnyel konvertál karakterláncból JSON formátumba. A függvény hozzárendeli a megadott értéket az új tulajdonsághoz, és visszaadja a frissített objektumot:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Itt látható az aktuális JSON-objektum:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Itt látható a frissített JSON-objektum:

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

Adjon meg néhány másodpercet egy időbélyeghez.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*másodperc*> | Igen | Egész szám | A hozzáadni kívánt másodpercek pozitív vagy negatív száma |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | Az időbélyeg és a megadott számú másodperc  |
||||

*1. példa*

Ez a példa 10 másodpercet ad hozzá a megadott időbélyeghez:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

És visszaadja ezt az eredményt: `"2018-03-15T00:00:10.0000000Z"`

*2. példa*

Ez a példa öt másodpercet kivon a megadott időbélyegre:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

És visszaadja ezt az eredményt: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Több időegység hozzáadása egy időbélyeghez.
Lásd még: [getFutureTime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*intervallum*> | Igen | Egész szám | A hozzáadandó megadott időegységek száma |
| <*timeUnit*> | Igen | Sztring | Az időegység, amelyet a következő *intervallummal*kell használni: "Second", "minute", "Hour", "Day", "Week", "hónap", "Year" |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | Az időbélyeg és a megadott számú időegység  |
||||

*1. példa*

Ez a példa egy napot ad hozzá a megadott időbélyeghez:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

És visszaadja ezt az eredményt: `"2018-01-02T00:00:00.0000000Z"`

*2. példa*

Ez a példa egy napot ad hozzá a megadott időbélyeghez:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

És visszaadja az eredményt a "D" választható formátum használatával: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>és

Győződjön meg arról, hogy az összes kifejezés igaz-e.
Igaz értéket ad vissza, ha az összes kifejezés igaz, vagy hamis értéket ad vissza, ha legalább egy kifejezés hamis.

```
and(<expression1>, <expression2>, ...)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*Kifejezés2*>,... | Igen | Logikai | Az ellenőrzési kifejezések |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | -----| ----------- |
| Igaz vagy hamis | Logikai | Igaz értéket ad vissza, ha az összes kifejezés igaz. Hamis értéket ad vissza, ha legalább egy kifejezés hamis. |
||||

*1. példa*

Ezek a példák azt jelzik, hogy a megadott logikai értékek mind igazak-e:

```
and(true, true)
and(false, true)
and(false, false)
```

És a következő eredményeket adja vissza:

* Első példa: mindkét kifejezés igaz, így a `true`értéket adja vissza.
* Második példa: az egyik kifejezés hamis, ezért a `false`értéket adja vissza.
* Harmadik példa: mindkét kifejezés hamis, ezért a `false`értéket adja vissza.

*2. példa*

Ezek a példák azt jelzik, hogy a megadott kifejezések mind igazak-e:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

És a következő eredményeket adja vissza:

* Első példa: mindkét kifejezés igaz, így a `true`értéket adja vissza.
* Második példa: az egyik kifejezés hamis, ezért a `false`értéket adja vissza.
* Harmadik példa: mindkét kifejezés hamis, ezért a `false`értéket adja vissza.

<a name="array"></a>

### <a name="array"></a>tömb

Tömb visszaadása egyetlen megadott bemenetből.
Több bemenet esetén lásd: [createArray ()](#createArray).

```
array('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Tömb létrehozásához használt karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| [<*érték*>] | Tömb | Egyetlen megadott bemenetet tartalmazó tömb |
||||

*Példa*

Ez a példa egy tömböt hoz létre a "Hello" sztringből:

```
array('hello')
```

És visszaadja ezt az eredményt: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>Base64

Egy sztring Base64 kódolású verziójának visszaadása.

```
base64('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A bemeneti sztring |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*Base64-karakterlánc*> | Sztring | A bemeneti karakterlánc Base64 kódolású verziója |
||||

*Példa*

Ez a példa a "Hello" karakterláncot Base64 kódolású karakterlánccá alakítja:

```
base64('hello')
```

És visszaadja ezt az eredményt: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Egy Base64 kódolású karakterlánc bináris verziójának visszaadása.

```
base64ToBinary('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó Base64 kódolású karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| *bináris – Base64-karakterlánc* <> | Sztring | A Base64 kódolású karakterlánc bináris verziója |
||||

*Példa*

Ez a példa a "aGVsbG8 =" Base64 kódolású karakterláncot bináris karakterlánccá alakítja:

```
base64ToBinary('aGVsbG8=')
```

És visszaadja ezt az eredményt:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Egy Base64 kódolású karakterlánc karakterlánc-verziójának visszaadása, amely hatékonyan dekódolja a Base64-karakterláncot.
Használja ezt a függvényt a [decodeBase64 ()](#decodeBase64)helyett.
Habár mindkét függvény ugyanúgy működik, `base64ToString()` előnyben részesített.

```
base64ToString('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A dekódolni kívánt Base64 kódolású karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolva – Base64-string*> | Sztring | Base64 kódolású karakterlánc karakterlánc-verziója |
||||

*Példa*

Ez a példa a "aGVsbG8 =" Base64 kódolású karakterláncot csak karakterlánccá alakítja át:

```
base64ToString('aGVsbG8=')
```

És visszaadja ezt az eredményt: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>Bináris

Egy karakterlánc bináris verziójának visszaadása.

```
binary('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*bináris a-input-value*> | Sztring | A megadott karakterlánc bináris verziója |
||||

*Példa*

Ez a példa a "Hello" karakterláncot bináris karakterlánccá alakítja át:

```
binary('hello')
```

És visszaadja ezt az eredményt:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>törzse

Egy művelet `body` kimenetének visszaadása futásidőben.
`actions('<actionName>').outputs.body`rövidítése.
Lásd: [actionBody ()](#actionBody) és [műveletek ()](#actions).

```
body('<actionName>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*műveletnév*> | Igen | Sztring | A művelet `body` kívánt kimenetének neve |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | -----| ----------- |
| <*művelet – törzs – kimeneti*> | Sztring | A megadott művelet `body` kimenete |
||||

*Példa*

Ez a példa a `Get user` Twitter művelet `body` kimenetét kéri le:

```
body('Get_user')
```

És visszaadja ezt az eredményt:

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

### <a name="bool"></a>logikai

Egy érték logikai verziójának visszaadása.

```
bool(<value>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Bármelyik | Az átalakítandó érték |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis | Logikai | A megadott érték logikai verziója |
||||

*Példa*

Ezek a példák logikai értékekre alakítják át a megadott értékeket:

```
bool(1)
bool(0)
```

És a következő eredményeket adja vissza:

* Első példa: `true`
* Második példa: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>összefonódik

Az első nem null értéket ad vissza egy vagy több paraméterből.
Az üres karakterláncok, üres tömbök és üres objektumok nem null értékűek.

```
coalesce(<object_1>, <object_2>, ...)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Igen | Bármely, többféle típust is tartalmazhat | Egy vagy több, a NULL értékre való keresésre szolgáló elem |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <az *első nem null értékű*> | Bármelyik | Az első olyan érték, amely nem null. Ha az összes paraméter null értékű, a függvény Null értéket ad vissza. |
||||

*Példa*

Ezek a példák az első nem null értéket adják vissza a megadott értékekről, vagy Null értéket, ha az összes érték null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

És a következő eredményeket adja vissza:

* Első példa: `true`
* Második példa: `"hello"`
* Harmadik példa: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Egyesítse kettő vagy több karakterláncot, és állítsa vissza az egyesített karakterláncot.

```
concat('<text1>', '<text2>', ...)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*Szöveg2*>,... | Igen | Sztring | Legalább két karakterláncot kell egyesíteni |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*text1text2...* > | Sztring | A kombinált bemeneti sztringből létrehozott sztring |
||||

*Példa*

Ez a példa a "Hello" és a "World" karakterláncokat ötvözi:

```
concat('Hello', 'World')
```

És visszaadja ezt az eredményt: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>tartalmaz

Győződjön meg arról, hogy egy gyűjteménynek van-e konkrét eleme.
Igaz értéket ad vissza, ha az elem található, vagy ha nem található, hamis értéket ad vissza.
Ez a függvény megkülönbözteti a kis-és nagybetűket.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Pontosabban, ez a függvény a következő típusú gyűjteményeken működik:

* Egy *karakterlánc* , amely egy *alsztringet* keres
* Egy *tömb* , amely egy *értéket* keres
* A *kulcs* megtalálásához használandó *szótár*

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc, tömb vagy szótár | Az ellenőrzési gyűjtemény |
| <*érték*> | Igen | Karakterlánc, tömb vagy szótár | A keresendő tétel |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis | Logikai | Igaz értéket ad vissza, ha az objektum megtalálható. Hamis értéket ad vissza, ha nem található. |
||||

*1. példa*

Ez a példa a "Helló világ" karakterláncot ellenőrzi a "World" karakterláncban, és igaz értéket ad vissza:

```
contains('hello world', 'world')
```

*2. példa*

Ez a példa ellenőrzi a "Hello World" karakterláncot a "Universe" alsztringnél, és hamis értéket ad vissza:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Az időbélyeget az egyezményes világidő (UTC) alapján alakítsa át a cél időzónára.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*destinationTimeZone*> | Igen | Sztring | A cél időzóna neve. Az időzóna neveivel kapcsolatban lásd: a [Microsoft időzóna-index értékei](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), de előfordulhat, hogy el kell távolítania az összes írásjelet az időzóna nevéből. |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*átalakítva – timestamp*> | Sztring | A cél időzónára konvertált időbélyeg |
||||

*1. példa*

Ez a példa egy időbélyeget konvertál a megadott időzónába:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

És visszaadja ezt az eredményt: `"2018-01-01T00:00:00.0000000"`

*2. példa*

Ez a példa egy időbélyeget konvertál a megadott időzónába és formátumba:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

És visszaadja ezt az eredményt: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

A forrás időzóna időbélyegének konvertálása a cél időzónára.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*sourceTimeZone*> | Igen | Sztring | A forrás időzóna neve. Az időzóna neveivel kapcsolatban lásd: a [Microsoft időzóna-index értékei](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), de előfordulhat, hogy el kell távolítania az összes írásjelet az időzóna nevéből. |
| <*destinationTimeZone*> | Igen | Sztring | A cél időzóna neve. Az időzóna neveivel kapcsolatban lásd: a [Microsoft időzóna-index értékei](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), de előfordulhat, hogy el kell távolítania az összes írásjelet az időzóna nevéből. |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*átalakítva – timestamp*> | Sztring | A cél időzónára konvertált időbélyeg |
||||

*1. példa*

Ez a példa a forrás időzónát a cél időzónára konvertálja:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

És visszaadja ezt az eredményt: `"2018-01-01T00:00:00.0000000"`

*2. példa*

Ez a példa egy időzónát konvertál a megadott időzónába és formátumba:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

És visszaadja ezt az eredményt: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

A forrás időzóna időbélyegének konvertálása az egyezményes világidő (UTC) szerinti időpontra

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*sourceTimeZone*> | Igen | Sztring | A forrás időzóna neve. Az időzóna neveivel kapcsolatban lásd: a [Microsoft időzóna-index értékei](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), de előfordulhat, hogy el kell távolítania az összes írásjelet az időzóna nevéből. |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*átalakítva – timestamp*> | Sztring | Az UTC-re konvertált időbélyeg |
||||

*1. példa*

Ez a példa a következő időbélyeget konvertálja UTC-re:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

És visszaadja ezt az eredményt: `"2018-01-01T08:00:00.0000000Z"`

*2. példa*

Ez a példa a következő időbélyeget konvertálja UTC-re:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

És visszaadja ezt az eredményt: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Tömb visszaadása több bemenetből.
Egyetlen bemeneti tömbhöz lásd: [array ()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>,... | Igen | Bármilyen, de nem vegyes | Legalább két elem a tömb létrehozásához |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>,...] | Tömb | Az összes bemeneti elemből létrehozott tömb |
||||

*Példa*

Ez a példa egy tömböt hoz létre ezekből a bemenetekről:

```
createArray('h', 'e', 'l', 'l', 'o')
```

És visszaadja ezt az eredményt: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Egy karakterlánchoz tartozó adat egységes erőforrás-azonosítójának (URI) visszaadása.

```
dataUri('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*adaturi-* > | Sztring | A bemeneti karakterlánchoz tartozó adat URI-ja |
||||

*Példa*

Ez a példa létrehoz egy adaturi-t a "Hello" karakterlánchoz:

```
dataUri('hello')
```

És visszaadja ezt az eredményt: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Az adategységes erőforrás-azonosító (URI) bináris verziójának visszaadása.
Használja ezt a függvényt a [decodeDataUri ()](#decodeDataUri)helyett.
Habár mindkét függvény ugyanúgy működik, `dataUriBinary()` előnyben részesített.

```
dataUriToBinary('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó adaturi |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| bináris adat<- *uri*> | Sztring | Az adaturi bináris verziója |
||||

*Példa*

Ez a példa bináris verziót hoz létre ehhez az adaturi-hoz:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És visszaadja ezt az eredményt:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Az adategységes erőforrás-azonosító (URI) karakterlánc-verziójának visszaadása.

```
dataUriToString('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó adaturi |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*karakterlánc-for-adaturi*> | Sztring | Az adaturi-azonosító karakterlánc-verziója |
||||

*Példa*

Ez a példa karakterláncot hoz létre ehhez az adaturi-hoz:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És visszaadja ezt az eredményt: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

A hónap napjának visszaadása egy időbélyegből.

```
dayOfMonth('<timestamp>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*a hónap napja*> | Egész szám | A hónap napja a megadott időbélyegből |
||||

*Példa*

Ez a példa a hónap napjának számát adja vissza ebből az időbélyegből:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

És visszaadja ezt az eredményt: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

A hét napjának visszaadása egy időbélyegből.

```
dayOfWeek('<timestamp>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*hét napja*> | Egész szám | A hét napja a megadott időbélyegtől, ahol a vasárnap 0, hétfő 1, és így tovább |
||||

*Példa*

Ez a példa a hét napjának számát adja vissza ebből az időbélyegből:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

És visszaadja ezt az eredményt: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Az év napjának visszaadása egy időbélyegből.

```
dayOfYear('<timestamp>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*nap*> | Egész szám | Az év napja a megadott időbélyegből |
||||

*Példa*

Ez a példa az év napjának számát adja vissza ebből az időbélyegből:

```
dayOfYear('2018-03-15T13:27:36Z')
```

És visszaadja ezt az eredményt: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Egy Base64 kódolású karakterlánc karakterlánc-verziójának visszaadása, amely hatékonyan dekódolja a Base64-karakterláncot.
`decodeBase64()`helyett vegye fontolóra a [base64ToString ()](#base64ToString) használatát.
Habár mindkét függvény ugyanúgy működik, `base64ToString()` előnyben részesített.

```
decodeBase64('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A dekódolni kívánt Base64 kódolású karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolva – Base64-string*> | Sztring | Base64 kódolású karakterlánc karakterlánc-verziója |
||||

*Példa*

Ez a példa karakterláncot hoz létre egy Base64 kódolású karakterlánchoz:

```
decodeBase64('aGVsbG8=')
```

És visszaadja ezt az eredményt: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Az adategységes erőforrás-azonosító (URI) bináris verziójának visszaadása.
`decodeDataUri()`helyett vegye fontolóra a [dataUriToBinary ()](#dataUriToBinary)használatát.
Habár mindkét függvény ugyanúgy működik, `dataUriToBinary()` előnyben részesített.

```
decodeDataUri('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A dekódolásra szolgáló adaturi-karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| bináris adat<- *uri*> | Sztring | Az adaturi-karakterlánc bináris verziója |
||||

*Példa*

Ez a példa a bináris verziót adja vissza ehhez az adaturi-hoz:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És visszaadja ezt az eredményt:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Olyan karakterláncot ad vissza, amely lecseréli az Escape-karaktereket a dekódolású verziókra.

```
decodeUriComponent('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A dekódolni kívánt Escape-karaktereket tartalmazó sztring |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolva – uri*> | Sztring | A dekódolású Escape-karaktereket tartalmazó frissített sztring |
||||

*Példa*

Ez a példa dekódolású verziókkal helyettesíti a karakterlánc Escape-karaktereit:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

És visszaadja ezt az eredményt: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

A két szám közötti egész szám eredményének visszaadása.
A hátralévő eredmény eléréséhez lásd: [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*osztalék*> | Igen | Egész vagy lebegőpontos | Az *osztó* által elosztani kívánt szám |
| <*osztó*> | Igen | Egész vagy lebegőpontos | Az *osztalékot*osztó szám, de nem lehet 0 |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*hányados – eredmény*> | Egész szám | Az egész szám, amely a második szám alapján osztja el az első számot |
||||

*Példa*

Mindkét példa a második szám alapján osztja el az első számot:

```
div(10, 5)
div(11, 5)
```

És az eredmény visszaadása: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Egy egységes erőforrás-azonosító (URI) kódolású verziót ad vissza egy karakterlánchoz az URL-nem biztonságos karakterek lecserélése Escape-karakterekkel.
`encodeUriComponent()`helyett vegye fontolóra a [uriComponent ()](#uriComponent)használatát.
Habár mindkét függvény ugyanúgy működik, `uriComponent()` előnyben részesített.

```
encodeUriComponent('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az URI-kódolású formátumba konvertálandó karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*kódolt-uri*> | Sztring | Az URI-kódolású karakterlánc Escape-karakterekkel |
||||

*Példa*

Ez a példa egy URI-kódolású verziót hoz létre ehhez a karakterlánchoz:

```
encodeUriComponent('https://contoso.com')
```

És visszaadja ezt az eredményt: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>üres

Győződjön meg arról, hogy a gyűjtemény üres.
Igaz értéket ad vissza, ha a gyűjtemény üres, vagy ha nem üres, hamis értéket ad vissza.

```
empty('<collection>')
empty([<collection>])
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc, tömb vagy objektum | Az ellenőrzési gyűjtemény |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis | Logikai | Igaz értéket ad vissza, ha a gyűjtemény üres. Hamis értéket ad vissza, ha nem üres. |
||||

*Példa*

Ezek a példák azt mutatják be, hogy a megadott gyűjtemények üresek-e:

```
empty('')
empty('abc')
```

És a következő eredményeket adja vissza:

* Első példa: üres karakterláncot ad vissza, így a függvény visszaadja a `true`.
* Második példa: az "ABC" karakterláncot adja vissza, így a függvény visszaadja a `false`.

<a name="endswith"></a>

### <a name="endswith"></a>EndsWith

Győződjön meg arról, hogy egy karakterlánc egy adott alkarakterlánccal végződik-e.
Igaz értéket ad vissza, ha az alsztring található, vagy hamis értéket ad vissza, ha nem található.
Ez a függvény nem megkülönbözteti a kis-és nagybetűket.

```
endsWith('<text>', '<searchText>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | Az ellenőrzési sztring |
| <*keresettszöveg*> | Igen | Sztring | A keresett karakterlánc vége |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis  | Logikai | Igaz értéket ad vissza, ha a záró alsztring található. Hamis értéket ad vissza, ha nem található. |
||||

*1. példa*

Ez a példa ellenőrzi, hogy a "Hello World" karakterlánc a "World" karakterlánccal végződik-e:

```
endsWith('hello world', 'world')
```

És visszaadja ezt az eredményt: `true`

*2. példa*

Ez a példa ellenőrzi, hogy a "Hello World" karakterlánc a "Universe" karakterlánccal végződik-e:

```
endsWith('hello world', 'universe')
```

És visszaadja ezt az eredményt: `false`

<a name="equals"></a>

### <a name="equals"></a>egyenlő

Győződjön meg arról, hogy az értékek, kifejezések vagy objektumok egyenértékűek-e.
Igaz értéket ad vissza, ha mindkettő egyenértékű, vagy ha nem egyenértékű, hamis értéket ad vissza.

```
equals('<object1>', '<object2>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Igen | Különböző | Az összehasonlítandó értékek, kifejezések vagy objektumok |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis | Logikai | Igaz értéket ad vissza, ha mindkettő egyenértékű. Hamis értéket ad vissza, ha nem egyenértékű. |
||||

*Példa*

Ezek a példák azt mutatják be, hogy a megadott bemenetek egyenértékűek-e.

```
equals(true, 1)
equals('abc', 'abcd')
```

És a következő eredményeket adja vissza:

* Első példa: mindkét érték egyenértékű, így a függvény `true`ad vissza.
* Második példa: mindkét érték nem egyenértékű, így a függvény visszaadja a `false`.

<a name="first"></a>

### <a name="first"></a>első

Egy sztringből vagy tömbből származó első elem visszaadása.

```
first('<collection>')
first([<collection>])
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény, ahol az első elemek találhatók |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <– *első gyűjtemény – tétel*> | Bármelyik | A gyűjtemény első eleme |
||||

*Példa*

Ezek a példák a gyűjtemények első tételét keresik:

```
first('hello')
first(createArray(0, 1, 2))
```

És az eredmények visszaadása:

* Első példa: `"h"`
* Második példa: `0`

<a name="float"></a>

### <a name="float"></a>lebegőpontos

Karakterlánc-verzió konvertálása lebegőpontos számra tényleges lebegőpontos számra.
Ezt a függvényt csak akkor használhatja, ha egyéni paramétereket továbbít egy alkalmazásra, például egy logikai alkalmazásra vagy folyamatra.

```
float('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó érvényes lebegőpontos számmal rendelkező sztring |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | A megadott karakterlánc lebegőpontos száma |
||||

*Példa*

Ez a példa egy karakterlánc-verziót hoz létre ehhez a lebegőpontos számhoz:

```
float('10.333')
```

És visszaadja ezt az eredményt: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>FormatDateTime

Egy időbélyeget ad vissza a megadott formátumban.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*átformázva – timestamp*> | Sztring | A frissített időbélyeg a megadott formátumban |
||||

*Példa*

Ez a példa egy időbélyeget konvertál a megadott formátumba:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

És visszaadja ezt az eredményt: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Tömb visszaadása olyan értékekkel, amelyek megfelelnek egy művelet *űrlap-* vagy *űrlap-kódolású* kimenetének.

```
formDataMultiValues('<actionName>', '<key>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*műveletnév*> | Igen | Sztring | Az a művelet, amelynek a kimenete a kívánt kulcs értéke |
| <*kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek a kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| [<*Array-with-Key-values*>] | Tömb | A megadott kulccsal egyező értékeket tartalmazó tömb |
||||

*Példa*

Ez a példa egy tömböt hoz létre a "tulajdonos" kulcs értékétől a megadott művelet űrlap-vagy űrlap-kódolású kimenetében:

```
formDataMultiValues('Send_an_email', 'Subject')
```

És visszaadja a tárgy szövegét egy tömbben, például: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Egyetlen olyan értéket adjon vissza, amely megfelel a művelet *űrlap-* vagy *űrlap-kódolású* kimenetében szereplő kulcs nevének.
Ha a függvény egynél több egyezést talál, a függvény hibát jelez.

```
formDataValue('<actionName>', '<key>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*műveletnév*> | Igen | Sztring | Az a művelet, amelynek a kimenete a kívánt kulcs értéke |
| <*kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek a kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*kulcs-érték*> | Sztring | A megadott kulcs értéke  |
||||

*Példa*

Ez a példa egy karakterláncot hoz létre a "tulajdonos" kulcs értékével a megadott művelet űrlap-vagy űrlap-kódolású kimenetében:

```
formDataValue('Send_an_email', 'Subject')
```

És karakterláncként adja vissza a tárgy szövegét, például: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

Egy szám visszaadása karakterláncként a megadott formátum alapján.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szám*> | Igen | Egész vagy dupla | A formázni kívánt érték. |
| <*formátum*> | Igen | Sztring | Egy összetett formázó karakterlánc, amely meghatározza a használni kívánt formátumot. A támogatott numerikus formázási karakterláncokat a `number.ToString(<format>, <locale>)`által támogatott [szabványos numerikus formázási karakterláncok](https://docs.microsoft.com/dotnet/standard/base-types/standard-numeric-format-strings)című szakaszban tekintheti meg. |
| <*területi beállítás*> | Nem | Sztring | A `number.ToString(<format>, <locale>)`által támogatott területi beállítás. Ha nincs megadva, az alapértelmezett érték `en-us`. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*formázott szám*> | Sztring | A megadott számú karakterlánc a megadott formátumban. Ezt a visszaadott értéket `int` vagy `float`ra is elküldheti. |
||||

*1. példa*

Tegyük fel, hogy formázni szeretné a számot `1234567890`. Ez a példa a "1 234 567 890,00" karakterláncként formázza ezt a számot.

```
formatNumber(1234567890, '{0:0,0.00}', 'en-us')
```

\* 2. példa "

Tegyük fel, hogy formázni szeretné a számot `1234567890`. Ez a példa a számot a "1.234.567.890, 00" karakterláncra formázza.

```
formatNumber(1234567890, '{0:0,0.00}', 'is-is')
```

*3. példa*

Tegyük fel, hogy formázni szeretné a számot `17.35`. Ez a példa a számot a "$17,35" karakterláncra formázza.

```
formatNumber(17.36, 'C2')
```

*4. példa*

Tegyük fel, hogy formázni szeretné a számot `17.35`. Ez a példa a számot a következő sztringre formázza: "17, 35 Kr".

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Az aktuális időbélyeg és a megadott időegységek visszaadása.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*intervallum*> | Igen | Egész szám | A hozzáadandó megadott időegységek száma |
| <*timeUnit*> | Igen | Sztring | Az időegység, amelyet a következő *intervallummal*kell használni: "Second", "minute", "Hour", "Day", "Week", "hónap", "Year" |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | Az aktuális időbélyeg és a megadott számú időegység |
||||

*1. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-03-01T00:00:00.0000000 Z".
Ez a példa öt napot tesz a következő időbélyeghez:

```
getFutureTime(5, 'Day')
```

És visszaadja ezt az eredményt: `"2018-03-06T00:00:00.0000000Z"`

*2. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-03-01T00:00:00.0000000 Z".
Ez a példa öt napot ad hozzá, és az eredményt "D" formátumra alakítja át:

```
getFutureTime(5, 'Day', 'D')
```

És visszaadja ezt az eredményt: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Az aktuális időbélyeg visszaadása a megadott időegységek mínusz.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*intervallum*> | Igen | Egész szám | A kivonandó megadott időegységek száma |
| <*timeUnit*> | Igen | Sztring | Az időegység, amelyet a következő *intervallummal*kell használni: "Second", "minute", "Hour", "Day", "Week", "hónap", "Year" |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | Az aktuális időbélyeg mínusz a megadott számú időegység |
||||

*1. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-02-01T00:00:00.0000000 Z".
Ez a példa öt napot kivon az időbélyegből:

```
getPastTime(5, 'Day')
```

És visszaadja ezt az eredményt: `"2018-01-27T00:00:00.0000000Z"`

*2. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-02-01T00:00:00.0000000 Z".
Ebben a példában öt napot kell kivonnia, és az eredményt "D" formátumra konvertáljuk:

```
getPastTime(5, 'Day', 'D')
```

És visszaadja ezt az eredményt: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>nagyobb

Győződjön meg arról, hogy az első érték nagyobb, mint a második érték.
Igaz értéket ad vissza, ha az első érték nagyobb, vagy ha kevesebbet ad vissza.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Egész szám, lebegőpontos vagy sztring | Az első érték, amellyel ellenőrizhető, hogy a második érték nagyobb-e |
| <*compareto metódus végrehajtása*> | Igen | Egész szám, lebegőpontos vagy sztring | Az összehasonlító érték |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis | Logikai | Igaz értéket ad vissza, ha az első érték nagyobb, mint a második érték. Hamis értéket ad vissza, ha az első érték egyenlő vagy kisebb a második értéknél. |
||||

*Példa*

Ezek a példák azt mutatják be, hogy az első érték nagyobb-e, mint a második érték:

```
greater(10, 5)
greater('apple', 'banana')
```

És az eredmények visszaadása:

* Első példa: `true`
* Második példa: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Győződjön meg arról, hogy az első érték nagyobb vagy egyenlő, mint a második érték.
Igaz értéket ad vissza, ha az első érték nagyobb vagy egyenlő, vagy hamis értéket ad vissza, ha az első érték kisebb.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Egész szám, lebegőpontos vagy sztring | Az első érték, amellyel ellenőrizhető, hogy a második érték nagyobb vagy egyenlő-e |
| <*compareto metódus végrehajtása*> | Igen | Egész szám, lebegőpontos vagy sztring | Az összehasonlító érték |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis | Logikai | Igaz értéket ad vissza, ha az első érték nagyobb vagy egyenlő, mint a második érték. Hamis értéket ad vissza, ha az első érték kisebb a második értéknél. |
||||

*Példa*

Ezek a példák azt mutatják be, hogy az első érték nagyobb vagy egyenlő, mint a második érték:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

És az eredmények visszaadása:

* Első példa: `true`
* Második példa: `false`

<a name="guid"></a>

### <a name="guid"></a>GUID

Globálisan egyedi azonosító (GUID) létrehozása karakterláncként (például "c2ecc88d-88c8-4096-912c-d6f2e2b138ce"):

```
guid()
```

Emellett eltérő formátumot is megadhat az alapértelmezett "D" formátumtól eltérő GUID azonosítóhoz, amely 32 számjegyből áll, kötőjelekkel elválasztva.

```
guid('<format>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*formátum*> | Nem | Sztring | Egyetlen [Formátum](https://msdn.microsoft.com/library/97af8hh4) megadása a VISSZAADOTT GUID azonosítóhoz. Alapértelmezés szerint a formátum a "D", de az "N", "D", "B", "P" vagy "X" is használható. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*GUID-value*> | Sztring | Véletlenszerűen generált GUID |
||||

*Példa*

Ez a példa ugyanazt a GUID azonosítót hozza létre, de 32 számjegyből áll, kötőjelekkel elválasztva, zárójelek közé zárva:

```
guid('P')
```

És visszaadja ezt az eredményt: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>Ha

Győződjön meg arról, hogy a kifejezés igaz vagy hamis.
Az eredmény alapján adja vissza a megadott értéket.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*kifejezés*> | Igen | Logikai | Az ellenőrzési kifejezés |
| <*valueIfTrue*> | Igen | Bármelyik | A kifejezés igaz értéke esetén visszaadott érték |
| <*valueIfFalse*> | Igen | Bármelyik | A kifejezés hamis értéke esetén visszaadott érték |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*megadott – visszatérési érték*> | Bármelyik | A megadott érték, amely attól függ, hogy a kifejezés igaz vagy hamis. |
||||

*Példa*

Ez a példa `"yes"` ad vissza, mert a megadott kifejezés igaz értéket ad vissza.
Ellenkező esetben a példa a `"no"`t adja vissza:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

A kezdő pozíció vagy az index értékének visszaadása egy alsztringnél.
Ez a függvény nem megkülönbözteti a kis-és nagybetűket, és az indexek a 0 számmal kezdődnek.

```
indexOf('<text>', '<searchText>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A keresendő alsztringet tartalmazó karakterlánc |
| <*keresettszöveg*> | Igen | Sztring | A keresendő alsztring |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*index-érték*>| Egész szám | A megadott alsztring kezdő pozíciójának vagy indexének értéke. <p>Ha a sztring nem található, az-1 számot kell visszaadnia. |
||||

*Példa*

Ez a példa megkeresi a "világ" alsztring kezdő indexének értékét a "Hello World" karakterláncban:

```
indexOf('hello world', 'world')
```

És visszaadja ezt az eredményt: `6`

<a name="int"></a>

### <a name="int"></a>int

Egy sztring egész változatának visszaadása.

```
int('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*egész szám – eredmény*> | Egész szám | A megadott karakterlánc egész számú verziója |
||||

*Példa*

Ez a példa egy egész verziót hoz létre a (z) "10" karakterlánchoz:

```
int('10')
```

És visszaadja ezt az eredményt: `10`

<a name="item"></a>

### <a name="item"></a>elem

Ha egy tömbön belül ismétlődő műveletben használja, a művelet aktuális iterációjában a tömb aktuális elemét adja vissza.
Az elem tulajdonságaiból is lekérheti az értékeket.

```
item()
```

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*jelenlegi-Array-item*> | Bármelyik | A művelet aktuális iterációjának aktuális eleme a tömbben |
||||

*Példa*

Ez a példa beolvassa a "Send_an_email" művelethez tartozó `body` elemet a for-each ciklus jelenlegi iterációjában:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>elemek

Az aktuális elem visszaküldése az egyes ciklusokból a-minden hurokhoz.
Használja ezt a függvényt a for-each cikluson belül.

```
items('<loopName>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Igen | Sztring | A for-each hurok neve |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <az *elemek*> | Bármelyik | A megadott a-minden hurokhoz tartozó aktuális ciklusból származó elem |
||||

*Példa*

Ez a példa a megadott for-each ciklus aktuális elemét kéri le:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Az aktuális iterációhoz tartozó index értékének visszaadása a hurokon belül. Ezt a függvényt a hurkok keretén belül ágyazva is használhatja. 

```
iterationIndexes('<loopName>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Igen | Sztring | A csak a ciklushoz tartozó név | 
||||| 

| Visszatérési érték | Type (Típus) | Leírás | 
| ------------ | ---- | ----------- | 
| <*index*> | Egész szám | Az aktuális iteráció indexének értéke a megadott ciklusig | 
|||| 

*Példa* 

Ez a példa egy számláló változót hoz létre, amely a változót az egyes iterációk során eggyel növeli, amíg a számláló értéke nem éri el az öt értéket. A példa egy olyan változót is létrehoz, amely nyomon követi az aktuális indexet az egyes iterációk esetében. A-ig ciklusban az egyes iterációk során a példa növeli a számlálót, majd hozzárendeli a számláló értékét az aktuális index értékhez, majd növeli a számlálót. Az aktuális iterációs számot bármikor megadhatja az aktuális index értékének beolvasásával.

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

### <a name="json"></a>JSON

A JavaScript Object Notation (JSON) típusú értéket vagy objektumot adja vissza egy karakterlánc vagy XML számára.

```
json('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Karakterlánc vagy XML | Az átalakítandó karakterlánc vagy XML |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*JSON – eredmény*> | JSON natív típusa vagy objektum | A JSON natív típusú érték vagy objektum a megadott karakterlánchoz vagy XML-hez. Ha a karakterlánc null értékű, a függvény üres objektumot ad vissza. |
||||

*1. példa*

Ez a példa átalakítja ezt a karakterláncot a JSON-értékre:

```
json('[1, 2, 3]')
```

És visszaadja ezt az eredményt: `[1, 2, 3]`

*2. példa*

Ez a példa a következő sztringet konvertálja JSON-ra:

```
json('{"fullName": "Sophia Owen"}')
```

És visszaadja ezt az eredményt:

```
{
  "fullName": "Sophia Owen"
}
```

*3. példa*

Ez a példa átalakítja ezt az XML-t a JSON-ra:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

És visszaadja ezt az eredményt:

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

### <a name="intersection"></a>kereszteződés

Olyan gyűjteményt ad vissza, amely *csak* a megadott gyűjtemények közös elemeit tartalmazta.
Az eredmény megjelenítéséhez egy elemnek szerepelnie kell a függvénynek átadott összes gyűjteményben.
Ha egy vagy több elem neve azonos, az eredményben megjelenik az utolsó elem az adott névvel.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>,... | Igen | Tömb vagy objektum, de nem mindkettő | Azok a gyűjtemények, amelyekről *csak* az általános elemeket szeretné használni |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*általános elemek*> | Tömb vagy objektum | Gyűjtemény, amely csak a megadott gyűjtemények közös elemeit tartalmazta |
||||

*Példa*

Ez a példa a következő tömbökben lévő gyakori elemeket keresi:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

És egy olyan tömböt ad vissza, amely *csak* az alábbi elemekből áll: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>csatlakozás

Olyan karakterláncot ad vissza, *amely egy tömb*összes elemét tartalmazhatja, és mindegyik karakter elválasztó karaktert tartalmaz.

```
join([<collection>], '<delimiter>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Tömb | Az a tömb, amelybe az elemek csatlakoznak |
| < elválasztó> | Igen | Sztring | Az eredményül kapott karakterlánc egyes karakterei között megjelenő elválasztó |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*char1*>< elválasztó><*char2*><elválasztó *karakter*>... | Sztring | Az eredményül kapott karakterlánc a megadott tömb összes eleme alapján létrehozva |
||||

*Példa*

Ez a példa egy karakterláncot hoz létre a tömb összes eleméről a megadott karakterrel elválasztó karakterrel:

```
join(createArray('a', 'b', 'c'), '.')
```

És visszaadja ezt az eredményt: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>utolsó

Egy gyűjtemény utolsó elemének visszaadása.

```
last('<collection>')
last([<collection>])
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény, ahol az utolsó tételt keresi |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*utolsó gyűjtemény – tétel*> | Karakterlánc vagy tömb, illetve | A gyűjtemény utolsó eleme |
||||

*Példa*

Ezek a példák a gyűjtemények utolsó tételét keresik:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

És a következő eredményeket adja vissza:

* Első példa: `"d"`
* Második példa: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

A kezdő pozíció vagy az index értékének visszaadása egy alsztring utolsó előfordulásakor.
Ez a függvény nem megkülönbözteti a kis-és nagybetűket, és az indexek a 0 számmal kezdődnek.

```
lastIndexOf('<text>', '<searchText>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A keresendő alsztringet tartalmazó karakterlánc |
| <*keresettszöveg*> | Igen | Sztring | A keresendő alsztring |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*End-index-value*> | Egész szám | A megadott alsztring utolsó előfordulásának kezdő pozíciója vagy index értéke. <p>Ha a sztring nem található, az-1 számot kell visszaadnia. |
||||

*Példa*

Ez a példa megkeresi a "világ" alsztring utolsó előfordulásának kezdő index értékét a "Hello World" karakterláncban:

```
lastIndexOf('hello world', 'world')
```

És visszaadja ezt az eredményt: `6`

<a name="length"></a>

### <a name="length"></a>hossza

Egy gyűjteményben lévő elemek számának visszaadása.

```
length('<collection>')
length([<collection>])
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény és a megszámlálni kívánt elemek |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*Hossz vagy darabszám*> | Egész szám | A gyűjtemény elemeinek száma |
||||

*Példa*

Ezek a példák a gyűjtemények elemeinek számát számolják el:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

És az eredmény visszaadása: `4`

<a name="less"></a>

### <a name="less"></a>kisebb

Győződjön meg arról, hogy az első érték kisebb a második értéknél.
Igaz értéket ad vissza, ha az első érték kisebb, vagy hamis értéket ad vissza, ha az első érték nagyobb.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Egész szám, lebegőpontos vagy sztring | Az első érték, amely azt vizsgálja, hogy a második érték kisebb-e |
| <*compareto metódus végrehajtása*> | Igen | Egész szám, lebegőpontos vagy sztring | Az összehasonlító tétel |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis | Logikai | Igaz értéket ad vissza, ha az első érték kisebb a második értéknél. Hamis értéket ad vissza, ha az első érték egyenlő vagy nagyobb, mint a második érték. |
||||

*Példa*

Ezek a példák azt mutatják be, hogy az első érték kisebb-e a második értéknél.

```
less(5, 10)
less('banana', 'apple')
```

És az eredmények visszaadása:

* Első példa: `true`
* Második példa: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Győződjön meg arról, hogy az első érték kisebb vagy egyenlő, mint a második érték.
Igaz értéket ad vissza, ha az első érték kisebb vagy egyenlő, vagy hamis értéket ad vissza, ha az első érték nagyobb.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Egész szám, lebegőpontos vagy sztring | Az első érték, amely azt vizsgálja, hogy a második érték kisebb vagy egyenlő-e |
| <*compareto metódus végrehajtása*> | Igen | Egész szám, lebegőpontos vagy sztring | Az összehasonlító tétel |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis  | Logikai | Igaz értéket ad vissza, ha az első érték kisebb vagy egyenlő, mint a második érték. Hamis értéket ad vissza, ha az első érték nagyobb, mint a második érték. |
||||

*Példa*

Ezek a példák azt mutatják be, hogy az első érték kisebb vagy egyenlő, mint a második érték.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

És az eredmények visszaadása:

* Első példa: `true`
* Második példa: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

A visszahívási URL-cím visszaadása, amely egy triggert vagy műveletet hív meg.
Ez a függvény csak a **HttpWebhook** és a **ApiConnectionWebhook** összekötőhöz használható eseményindítókkal és műveletekkel működik, a **manuális**, az **ismétlődési**, a **http**-és a **APIConnection** -típusok azonban nem.

```
listCallbackUrl()
```

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*visszahívási URL-cím*> | Sztring | Egy trigger vagy művelet visszahívási URL-címe |
||||

*Példa*

Ez a példa egy minta visszahívási URL-címet mutat be, amelyet a függvény visszaadhat:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>Max

Egy listából vagy tömbből származó legmagasabb értéket ad vissza, amelynek a száma mindkét végén szerepel.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szám1*>, <*szám2*>,... | Igen | Egész szám, lebegőpontos vagy mindkettő | Azon számok halmaza, amelyekről a legmagasabb értéket kívánja megadni |
| [<*szám1*>, <*szám2*>,...] | Igen | Tömb – egész szám, lebegőpontos vagy mindkettő | Azon számok tömbje, amelyekről a legmagasabb értéket kívánja használni |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*maximális érték*> | Egész vagy lebegőpontos | A megadott tömb vagy számok legmagasabb értéke |
||||

*Példa*

Ezek a példák a legmagasabb értéket kapják meg a számok és a tömb közül:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

És az eredmény visszaadása: `3`

<a name="min"></a>

### <a name="min"></a>perc

A legkisebb értéket adja vissza számokból vagy tömbből.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szám1*>, <*szám2*>,... | Igen | Egész szám, lebegőpontos vagy mindkettő | Azon számok halmaza, amelyekről a legalacsonyabb értéket szeretné használni |
| [<*szám1*>, <*szám2*>,...] | Igen | Tömb – egész szám, lebegőpontos vagy mindkettő | Azon számok tömbje, amelyekről a legalacsonyabb értéket szeretné használni |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*minimális érték*> | Egész vagy lebegőpontos | A megadott számú vagy megadott tömb legalacsonyabb értéke |
||||

*Példa*

Ezek a példák a legalacsonyabb értéket kapják meg a számok és a tömbben:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

És az eredmény visszaadása: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

A maradékot a két szám felosztásával állítsa vissza.
Az egész szám eredményének lekéréséhez lásd: [div ()](#div).

```
mod(<dividend>, <divisor>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*osztalék*> | Igen | Egész vagy lebegőpontos | Az *osztó* által elosztani kívánt szám |
| <*osztó*> | Igen | Egész vagy lebegőpontos | Az *osztalékot*osztó szám, de nem lehet 0. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| adatsorozatok < *– eredmény*> | Egész vagy lebegőpontos | A maradék az első számnak a második számmal való felosztásával |
||||

*Példa*

Ez a példa a második szám szerint osztja el az első számot:

```
mod(3, 2)
```

És az eredmény visszaadása: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

A termék visszaküldése két szám szorzatával.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Igen | Egész vagy lebegőpontos | A *multiplicand2* által szorozható szám |
| <*multiplicand2*> | Igen | Egész vagy lebegőpontos | A többszörös *multiplicand1* száma |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*termék – eredmény*> | Egész vagy lebegőpontos | A termék, amely az első számot a második számmal szorozza meg |
||||

*Példa*

Ezek a példák többek között az első szám a második szám szerint:

```
mul(1, 2)
mul(1.5, 2)
```

És az eredmények visszaadása:

* Első példa: `2`
* Második példa `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Egy művelet adott részének törzsét adja vissza egy olyan kimenetben, amely több részből áll.

```
multipartBody('<actionName>', <index>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*műveletnév*> | Igen | Sztring | A több részből álló kimenettel rendelkező művelet neve |
| <*index*> | Igen | Egész szám | A kívánt rész indexének értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*törzs*> | Sztring | A megadott rész törzse |
||||

<a name="not"></a>

### <a name="not"></a>nem

Győződjön meg arról, hogy egy kifejezés hamis-e.
Igaz értéket ad vissza, ha a kifejezés hamis, vagy igaz értéket ad vissza.

```json
not(<expression>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*kifejezés*> | Igen | Logikai | Az ellenőrzési kifejezés |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis | Logikai | Igaz értéket ad vissza, ha a kifejezés hamis. Hamis értéket ad vissza, ha a kifejezés igaz. |
||||

*1. példa*

Ezek a példák azt mutatják be, hogy a megadott kifejezések hamisak-e:

```json
not(false)
not(true)
```

És az eredmények visszaadása:

* Első példa: a kifejezés hamis, így a függvény `true`adja vissza.
* Második példa: a kifejezés igaz, így a függvény `false`adja vissza.

*2. példa*

Ezek a példák azt mutatják be, hogy a megadott kifejezések hamisak-e:

```json
not(equals(1, 2))
not(equals(1, 1))
```

És az eredmények visszaadása:

* Első példa: a kifejezés hamis, így a függvény `true`adja vissza.
* Második példa: a kifejezés igaz, így a függvény `false`adja vissza.

<a name="or"></a>

### <a name="or"></a>vagy

Győződjön meg arról, hogy legalább egy kifejezés igaz értékű-e.
Igaz értéket ad vissza, ha legalább egy kifejezés igaz értékű, vagy hamis értéket ad vissza, ha az összes hamis.

```
or(<expression1>, <expression2>, ...)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*Kifejezés2*>,... | Igen | Logikai | Az ellenőrzési kifejezések |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis | Logikai | Igaz értéket ad vissza, ha legalább egy kifejezés igaz. Hamis értéket ad vissza, ha az összes kifejezés hamis. |
||||

*1. példa*

Ezek a példák azt mutatják be, hogy legalább egy kifejezés igaz-e:

```json
or(true, false)
or(false, false)
```

És az eredmények visszaadása:

* Első példa: legalább egy kifejezés igaz, így a függvény visszaadja a `true`.
* Második példa: mindkét kifejezés hamis, így a függvény `false`ad vissza.

*2. példa*

Ezek a példák azt mutatják be, hogy legalább egy kifejezés igaz-e:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

És az eredmények visszaadása:

* Első példa: legalább egy kifejezés igaz, így a függvény visszaadja a `true`.
* Második példa: mindkét kifejezés hamis, így a függvény `false`ad vissza.

<a name="outputs"></a>

### <a name="outputs"></a>kimenetek

Egy művelet kimenetének visszaadása futásidőben. `actionOutputs()`helyett használja ezt a függvényt, amely a Logic app Designerben való `outputs()` feloldására szolgál. Habár mindkét függvény ugyanúgy működik, `outputs()` előnyben részesített.

```
outputs('<actionName>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*műveletnév*> | Igen | Sztring | A művelet kívánt kimenetének neve |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | -----| ----------- |
| <*kimeneti*> | Sztring | A megadott művelet kimenete |
||||

*Példa*

Ez a példa a Twitter-művelet `Get user`kimenetét kéri le:

```
outputs('Get_user')
```

És visszaadja ezt az eredményt:

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

A munkafolyamat-definícióban leírt paraméterek értékének visszaadása.

```
parameters('<parameterName>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Igen | Sztring | Annak a paraméternek a neve, amelynek a kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*paraméter – érték*> | Bármelyik | A megadott paraméter értéke |
||||

*Példa*

Tegyük fel, hogy ezt a JSON-értéket adta meg:

```json
{
  "fullName": "Sophia Owen"
}
```

Ez a példa a megadott paraméter értékét kéri le:

```
parameters('fullName')
```

És visszaadja ezt az eredményt: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>Rand

Egy, a megadott tartományból származó véletlenszerű egész számot ad vissza, amely csak a kezdési végponton van bezárólag.

```
rand(<minValue>, <maxValue>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Igen | Egész szám | A tartomány legalacsonyabb egésze |
| <*maxValue*> | Igen | Egész szám | Az egész szám, amely a függvény által visszaadott tartomány legmagasabb egészét követi |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*véletlenszerű eredmény*> | Egész szám | A megadott tartományból visszaadott véletlenszerű egész szám |
||||

*Példa*

Ez a példa egy véletlenszerű egész számot kap a megadott tartományból, kivéve a maximális értéket:

```
rand(1, 5)
```

És visszaadja a következő számok egyikét az eredményként: `1`, `2`, `3`vagy `4`

<a name="range"></a>

### <a name="range"></a>tartomány

Egy egész tömböt ad vissza, amely egy megadott egész számból indul.

```
range(<startIndex>, <count>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Igen | Egész szám | Egész érték, amely az első elemként elindítja a tömböt. |
| <*száma*> | Igen | Egész szám | A tömbben lévő egész számok száma |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| [<*tartomány – eredmény*>] | Tömb | A tömb a megadott indextől kezdődő egész számokkal |
||||

*Példa*

Ez a példa egy egész tömböt hoz létre, amely a megadott indextől kezdődik, és a megadott számú egész számot tartalmaz:

```
range(1, 4)
```

És visszaadja ezt az eredményt: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>csere

Cserélje le az alsztringet a megadott sztringre, és adja vissza az eredmény-karakterláncot. Ez a függvény megkülönbözteti a kis-és nagybetűket.

```
replace('<text>', '<oldText>', '<newText>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A lecserélni kívánt alsztringet tartalmazó karakterlánc |
| <*oldText*> | Igen | Sztring | A lecserélni kívánt alsztring |
| <*newText*> | Igen | Sztring | A helyettesítő sztring |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – szöveges*> | Sztring | Az alsztring cseréje után a frissített sztring <p>Ha az alkarakterlánc nem található, az eredeti karakterláncot küldje vissza. |
||||

*Példa*

Ez a példa megkeresi a "régi" karakterláncot a "régi" karakterláncban, és a "régi" kifejezést az "új" értékre cseréli:

```
replace('the old string', 'old', 'new')
```

És visszaadja ezt az eredményt: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Eltávolít egy tulajdonságot egy objektumból, és visszaküldi a frissített objektumot. Ha az eltávolítandó tulajdonság nem létezik, a függvény az eredeti objektumot adja vissza.

```
removeProperty(<object>, '<property>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*objektum*> | Igen | Objektum | A JSON-objektum, amelyből el kívánja távolítani a tulajdonságot |
| <*tulajdonság*> | Igen | Sztring | Az eltávolítandó tulajdonság neve |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – objektum*> | Objektum | A frissített JSON-objektum a megadott tulajdonság nélkül |
||||

Ha el szeretne távolítani egy gyermek tulajdonságot egy meglévő tulajdonságból, használja a következő szintaxist:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*objektum*> | Igen | Objektum | A JSON-objektum, amelynek a tulajdonságát el szeretné távolítani |
| <*szülő-tulajdonság*> | Igen | Sztring | Az eltávolítani kívánt gyermek tulajdonsággal rendelkező Parent tulajdonság neve |
| <*gyermek-tulajdonság*> | Igen | Sztring | Az eltávolítandó gyermek tulajdonság neve |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – objektum*> | Objektum | A frissített JSON-objektum, amelynek gyermek tulajdonsága el lett távolítva |
||||

*1. példa*

Ez a példa eltávolítja a `middleName` tulajdonságot egy JSON-objektumból, amelyet a rendszer a JSON [()](#json) függvény használatával konvertál a JSON-ból, és visszaadja a frissített objektumot:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Itt látható az aktuális JSON-objektum:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Itt látható a frissített JSON-objektum:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*2. példa*

Ez a példa eltávolítja a `middleName` Child tulajdonságot egy JSON-objektumban található `customerName` Parent tulajdonságból, amelyet a rendszer a JSON [()](#json) függvény használatával konvertál karakterláncból JSON formátumba, és visszaadja a frissített objektumot:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Itt látható az aktuális JSON-objektum:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Itt látható a frissített JSON-objektum:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>találat

A megadott hatókörű műveletben lévő összes művelet bemeneteit és kimeneteit adja vissza, például `For_each`, `Until`vagy `Scope` művelet. Ez a függvény akkor hasznos, ha egy sikertelen művelet eredményét adja vissza, így diagnosztizálhatja és kezelheti a kivételeket. További információ: [a környezet és a hibák eredményeinek beolvasása](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Igen | Sztring | Annak a hatókörön belüli műveletnek a neve, amelyből vissza kell adni a bemeneteket és kimeneteket az összes belső műveletből |
||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*Array-object*> | Tömb objektum | Egy tömb, amely a megadott hatókörű műveletben megjelenő összes műveletből származó bemeneteket és kimeneteket tartalmaz. |
||||

*Példa*

Ez a példa egy `For_each` hurkon belüli HTTP-művelet minden egyes iterációjának bemeneteit és kimeneteit adja vissza az `Compose` művelet `result()` függvényével:

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

A példában szereplő példa azt mutatja, hogy a külső `outputs` objektum a `For_each` műveletben szereplő műveletek minden egyes iterációjának bemeneteit és kimeneteit tartalmazza.

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

### <a name="setproperty"></a>setProperty

Állítsa be a JSON-objektum tulajdonságának értékét, és adja vissza a frissített objektumot. Ha a beállítani kívánt tulajdonság nem létezik, a tulajdonság bekerül az objektumba. Új tulajdonság hozzáadásához használja a [addProperty ()](#addProperty) függvényt.

```
setProperty(<object>, '<property>', <value>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*objektum*> | Igen | Objektum | Az a JSON-objektum, amelynek a tulajdonságát be szeretné állítani |
| <*tulajdonság*> | Igen | Sztring | A beállítani kívánt meglévő vagy új tulajdonság neve |
| <*érték*> | Igen | Bármelyik | A megadott tulajdonsághoz beállított érték |
|||||

Ha a gyermek tulajdonságot egy alárendelt objektumban szeretné beállítani, használjon helyette egy beágyazott `setProperty()` hívást. Ellenkező esetben a függvény csak a gyermek objektumot adja vissza kimenetként.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*objektum*> | Igen | Objektum | Az a JSON-objektum, amelynek a tulajdonságát be szeretné állítani |
| <*szülő-tulajdonság*> | Igen | Sztring | A beállítani kívánt gyermek tulajdonsággal rendelkező Parent tulajdonság neve |
| <*gyermek-tulajdonság*> | Igen | Sztring | A beállítani kívánt gyermek tulajdonság neve |
| <*érték*> | Igen | Bármelyik | A megadott tulajdonsághoz beállított érték |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – objektum*> | Objektum | A frissített JSON-objektum, amelynek a tulajdonságát beállította |
||||

*1. példa*

Ez a példa egy JSON-objektumban állítja be a `surName` tulajdonságot, amelyet a rendszer a JSON [()](#json) függvény használatával konvertál egy karakterláncból a JSON-ra. A függvény a megadott értéket rendeli hozzá a tulajdonsághoz, és visszaadja a frissített objektumot:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Itt látható az aktuális JSON-objektum:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Itt látható a frissített JSON-objektum:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*2. példa*

Ez a példa egy JSON-objektumban lévő `customerName` Parent tulajdonsághoz tartozó `surName` Child tulajdonságot állítja be, amelyet a rendszer a JSON [()](#json) függvénnyel konvertál karakterláncból JSON formátumba. A függvény a megadott értéket rendeli hozzá a tulajdonsághoz, és visszaadja a frissített objektumot:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Itt látható az aktuális JSON-objektum:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Itt látható a frissített JSON-objektum:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>kihagyása

Elemek eltávolítása egy gyűjtemény elejéről, és *az összes többi* elem visszaadása.

```
skip([<collection>], <count>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Tömb | A gyűjtemény, amelynek elemeit el szeretné távolítani |
| <*száma*> | Igen | Egész szám | Az előtérben eltávolítandó elemek számának pozitív egésze |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| [<*frissítve – gyűjtemény*>] | Tömb | A frissített gyűjtemény a megadott elemek eltávolítása után |
||||

*Példa*

Ez a példa egy elemet távolít el, a 0 számot a megadott tömb elejéről:

```
skip(createArray(0, 1, 2, 3), 1)
```

És visszaadja ezt a tömböt a fennmaradó elemekkel: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>felosztás

Egy olyan tömböt ad vissza, amely vesszővel elválasztott alsztringeket tartalmaz, az eredeti karakterláncban megadott elválasztói karakter alapján.

```
split('<text>', '<delimiter>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A karakterlánc, amely az eredeti sztringben megadott határolójel alapján választja el az alsztringeket |
| < elválasztó> | Igen | Sztring | A határolójelként használandó eredeti karakterláncban szereplő karakter |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| [<*substring1*>, <*substring2*>,...] | Tömb | Egy tömb, amely az eredeti sztringből származó alsztringeket tartalmaz, vesszővel elválasztva |
||||

*Példa*

Ez a példa egy olyan tömböt hoz létre, amely alsztringekkel rendelkezik a megadott karakterlánc alapján, az elválasztó karaktertől függően:

```
split('a_b_c', '_')
```

És ezt a tömböt adja vissza eredményként: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Egy időbélyeghez tartozó nap kezdetének visszaadása.

```
startOfDay('<timestamp>', '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | A megadott időbélyeg, de a nap nulla órás megjelölése után |
||||

*Példa*

Ez a példa megkeresi a nap kezdetét ehhez az időbélyeghez:

```
startOfDay('2018-03-15T13:30:30Z')
```

És visszaadja ezt az eredményt: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>Óra kezdete

Az óra kezdetének visszaadása egy időbélyeghez.

```
startOfHour('<timestamp>', '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | A megadott időbélyeg, de az óra nulla perces jelölése után |
||||

*Példa*

Ez a példa megkeresi az óra kezdetét ehhez az időbélyeghez:

```
startOfHour('2018-03-15T13:30:30Z')
```

És visszaadja ezt az eredményt: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

A hónap kezdetének visszaadása egy időbélyeghez.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | A megadott időbélyeg, de a hónap első napján kezdődik a nulla órás jelölés |
||||

*Példa*

Ez a példa az időbélyeg hónapjának kezdetét adja vissza:

```
startOfMonth('2018-03-15T13:30:30Z')
```

És visszaadja ezt az eredményt: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Győződjön meg róla, hogy egy sztring egy adott alkarakterlánccal kezdődik-e.
Igaz értéket ad vissza, ha az alsztring található, vagy hamis értéket ad vissza, ha nem található.
Ez a függvény nem megkülönbözteti a kis-és nagybetűket.

```
startsWith('<text>', '<searchText>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | Az ellenőrzési sztring |
| <*keresettszöveg*> | Igen | Sztring | A keresett kezdő sztring |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| Igaz vagy hamis  | Logikai | Igaz értéket ad vissza, ha a kezdő alsztring található. Hamis értéket ad vissza, ha nem található. |
||||

*1. példa*

Ez a példa ellenőrzi, hogy a "Hello World" karakterlánc a "Hello" alkarakterlánccal kezdődik-e:

```
startsWith('hello world', 'hello')
```

És visszaadja ezt az eredményt: `true`

*2. példa*

Ez a példa ellenőrzi, hogy a "Hello World" karakterlánc a "köszöntések" alkarakterlánccal kezdődik-e:

```
startsWith('hello world', 'greetings')
```

És visszaadja ezt az eredményt: `false`

<a name="string"></a>

### <a name="string"></a>sztring

Egy érték karakterlánc-verziójának visszaadása.

```
string(<value>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Bármelyik | Az átalakítandó érték |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*karakterlánc-érték*> | Sztring | A megadott érték karakterlánc-verziója |
||||

*1. példa*

Ez a példa a következő karakterlánc-verziót hozza létre ehhez a számhoz:

```
string(10)
```

És visszaadja ezt az eredményt: `"10"`

*2. példa*

Ez a példa egy karakterláncot hoz létre a megadott JSON-objektumhoz, és a fordított perjel karaktert (\\) használja Escape-karakterként a dupla idézőjel (") számára.

```
string( { "name": "Sophie Owen" } )
```

És visszaadja ezt az eredményt: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Sub

Az eredmény visszaadása a második számnak az első számból való kivonásával.

```
sub(<minuend>, <subtrahend>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*kisebbítendőt*> | Igen | Egész vagy lebegőpontos | Az a szám, amelyből ki kell vonni a *kivonandó* |
| <*kivonandó*> | Igen | Egész vagy lebegőpontos | A *kisebbítendőt* kivonni kívánt szám |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*eredmény*> | Egész vagy lebegőpontos | A második számnak az első számból való kivonásának eredménye |
||||

*Példa*

Ez a példa kivonja a második számot az első számból:

```
sub(10.3, .3)
```

És visszaadja ezt az eredményt: `10`

<a name="substring"></a>

### <a name="substring"></a>substring

Karaktereket ad vissza egy karakterláncból, a megadott pozíciótól vagy indextől kezdődően.
Az index értékei a 0 számmal kezdődnek.

```
substring('<text>', <startIndex>, <length>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | Az a karakterlánc, amelynek a karaktereit szeretné |
| <*startIndex*> | Igen | Egész szám | A kiindulási pozícióként vagy indexként használni kívánt nullánál kisebb vagy nagyobb pozitív érték |
| <*hossza*> | Igen | Egész szám | Pozitív számú karakter, amelyet az alkarakterláncban szeretne használni. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*alkarakterlánc – eredmény*> | Sztring | A megadott számú karakterből álló alsztring, amely a forrás sztringben megadott index pozíciótól kezdődik. |
||||

*Példa*

Ez a példa egy öt karakterből álló alkarakterláncot hoz létre a megadott karakterláncból, a 6. index értéktől kezdődően:

```
substring('hello world', 6, 5)
```

És visszaadja ezt az eredményt: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Több időegység kivonása egy időbélyegből.
Lásd még: [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*intervallum*> | Igen | Egész szám | A kivonandó megadott időegységek száma |
| <*timeUnit*> | Igen | Sztring | Az időegység, amelyet a következő *intervallummal*kell használni: "Second", "minute", "Hour", "Day", "Week", "hónap", "Year" |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*frissítve – timestamp*> | Sztring | Az időbélyeg mínusz a megadott számú időegység |
||||

*1. példa*

Ez a példa egy napot kivonja ebből az időbélyegből:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

És visszaadja ezt az eredményt: `"2018-01-01T00:00:00:0000000Z"`

*2. példa*

Ez a példa egy napot kivonja ebből az időbélyegből:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

És visszaadja ezt az eredményt a nem kötelező "D" formátum használatával: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>eltarthat

Elemek visszaküldése egy gyűjtemény elejéről.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény, amelynek elemeit szeretné |
| <*száma*> | Igen | Egész szám | Az elejétől kapott elemek számának pozitív egésze |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*részhalmaz*> vagy [<*részhalmaz*>] | Karakterlánc vagy tömb, illetve | Olyan karakterlánc vagy tömb, amely az eredeti gyűjtemény elejétől kapott megadott számú elemet tartalmaz. |
||||

*Példa*

Ezek a példák a gyűjtemények elejéről kapják meg a megadott számú elemet:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

És az eredmények visszaadása:

* Első példa: `"abc"`
* Második példa: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

Egy megadott időbélyeg `ticks` tulajdonságának értékét adja vissza.
A *Tick* egy 100-ns intervallum.

```
ticks('<timestamp>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Igen | Sztring | Az időbélyeg karakterlánca |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*ticks – szám*> | Egész szám | A megadott időbélyeg óta a kullancsok száma |
||||

<a name="toLower"></a>

### <a name="tolower"></a>ToLower

Karakterláncot ad vissza kisbetűs formátumban. Ha a karakterlánc egyik karaktere nem rendelkezik kisbetűs változattal, a karakter változatlan marad a visszaadott karakterláncban.

```
toLower('<text>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A kisbetűs formátumban visszaadni kívánt karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*kisbetűs szöveg*> | Sztring | Az eredeti sztring kisbetűs formátumban |
||||

*Példa*

Ez a példa kisbetűsre alakítja át ezt a karakterláncot:

```
toLower('Hello World')
```

És visszaadja ezt az eredményt: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>ToUpper

Karakterláncot ad vissza nagybetűs formátumban. Ha a karakterlánc egyik karaktere nem rendelkezik nagybetűs változattal, akkor ez a karakter változatlan marad a visszaadott karakterláncban.

```
toUpper('<text>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A nagybetűs formátumban visszaadni kívánt karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*nagybetűs szöveg*> | Sztring | Az eredeti sztring nagybetűs formátumban |
||||

*Példa*

Ez a példa nagybetűvé alakítja át ezt a karakterláncot:

```
toUpper('Hello World')
```

És visszaadja ezt az eredményt: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>eseményindító

Egy trigger kimenetének visszaadása futásidőben vagy más JSON-név és érték párokból származó értékekkel, amelyeket hozzárendelhet egy kifejezéshez.

* Az trigger bemenetei között ez a függvény az előző végrehajtás eredményét adja vissza.

* Egy trigger feltételében ez a függvény az aktuális végrehajtás eredményét adja vissza.

Alapértelmezés szerint a függvény a teljes trigger objektumra hivatkozik, de igény szerint megadhat egy olyan tulajdonságot, amelynek értékét szeretné.
Emellett a függvénynek van elérhető Gyorsírási verziója, lásd: [triggerOutputs ()](#triggerOutputs) és [triggerBody ()](#triggerBody).

```
trigger()
```

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*trigger – kimeneti*> | Sztring | Egy trigger kimenete futásidőben |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Egy trigger `body` kimenetének visszaadása futásidőben.
`trigger().outputs.body`rövidítése.
Lásd: [trigger ()](#trigger).

```
triggerBody()
```

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*trigger-test-output*> | Sztring | Az trigger `body` kimenete |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Egy olyan tömböt ad vissza, amely egy trigger *űrlap-* vagy *űrlap-kódolású* kimenetében szereplő kulcs nevének felel meg.

```
triggerFormDataMultiValues('<key>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek a kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| [<*Array-with-Key-values*>] | Tömb | A megadott kulccsal egyező értékeket tartalmazó tömb |
||||

*Példa*

Ez a példa egy olyan tömböt hoz létre a "feedUrl" kulcs értékében egy RSS-trigger űrlap-vagy űrlap-kódolt kimenetében:

```
triggerFormDataMultiValues('feedUrl')
```

És ezt a tömböt adja vissza példaként eredményként: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Egy olyan sztringet ad vissza, amely egy trigger *űrlap-* vagy *űrlap-kódolású* kimenetében szereplő kulcsnévnek felel meg.
Ha a függvény egynél több egyezést talál, a függvény hibát jelez.

```
triggerFormDataValue('<key>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek a kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*kulcs-érték*> | Sztring | A megadott kulcs értéke |
||||

*Példa*

Ez a példa egy RSS-trigger űrlap-vagy űrlap-kódolású kimenetében lévő "feedUrl" kulcs értékének karakterláncát hozza létre:

```
triggerFormDataValue('feedUrl')
```

És visszaadja ezt a karakterláncot példaként eredményként: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Egy trigger kimenetének adott részének törzsét adja vissza, amely több részből áll.

```
triggerMultipartBody(<index>)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*index*> | Igen | Egész szám | A kívánt rész indexének értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*törzs*> | Sztring | Egy trigger többrészes kimenetében lévő megadott rész törzse |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Egy trigger kimenetének visszaadása futásidőben vagy más JSON-név és érték párokból származó értékek alapján.
`trigger().outputs`rövidítése.
Lásd: [trigger ()](#trigger).

```
triggerOutputs()
```

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*trigger – kimeneti*> | Sztring | Egy trigger kimenete futásidőben  |
||||

<a name="trim"></a>

### <a name="trim"></a>Trim

Távolítsa el a kezdő és záró szóközt egy karakterláncból, és küldje vissza a frissített karakterláncot.

```
trim('<text>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | Az eltávolítandó kezdő és záró szóközt tartalmazó karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*updatedText*> | Sztring | Frissített verzió az eredeti karakterlánchoz kezdő vagy záró szóközök nélkül |
||||

*Példa*

Ez a példa eltávolítja a kezdő és záró szóközt a (z) ""Helló világ!"alkalmazás" karakterláncból:

```
trim(' Hello World  ')
```

És visszaadja ezt az eredményt: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>Union

Olyan gyűjteményt ad vissza, amely a megadott gyűjtemények *összes* elemét tartalmazta.
Az eredmény megjelenítéséhez egy elem megjelenhet a függvénynek átadott gyűjteményben. Ha egy vagy több elem neve azonos, az eredményben megjelenik az utolsó elem az adott névvel.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>,...  | Igen | Tömb vagy objektum, de nem mindkettő | Azok a gyűjtemények, amelyekről az *összes* elemet meg szeretné jeleníteni |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Tömb vagy objektum | Gyűjtemény a megadott gyűjtemények összes elemével – nincsenek duplikálva |
||||

*Példa*

Ez a példa a gyűjtemények *összes* elemét lekéri:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

És visszaadja ezt az eredményt: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Egy egységes erőforrás-azonosító (URI) kódolású verziót ad vissza egy karakterlánchoz az URL-nem biztonságos karakterek lecserélése Escape-karakterekkel.
Használja ezt a függvényt a [encodeUriComponent ()](#encodeUriComponent)helyett.
Habár mindkét függvény ugyanúgy működik, `uriComponent()` előnyben részesített.

```
uriComponent('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az URI-kódolású formátumba konvertálandó karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*kódolt-uri*> | Sztring | Az URI-kódolású karakterlánc Escape-karakterekkel |
||||

*Példa*

Ez a példa egy URI-kódolású verziót hoz létre ehhez a karakterlánchoz:

```
uriComponent('https://contoso.com')
```

És visszaadja ezt az eredményt: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Egy egységes erőforrás-azonosító (URI) összetevő bináris verziójának visszaadása.

```
uriComponentToBinary('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó URI-kódolású karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| *bináris kódolású URI-* > < | Sztring | Az URI-kódolású karakterlánc bináris verziója. A bináris tartalom Base64 kódolású, és `$content`jelképezi. |
||||

*Példa*

Ez a példa az URI-kódolású karakterlánc bináris verzióját hozza létre:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

És visszaadja ezt az eredményt:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Egy egységes erőforrás-azonosító (URI) kódolású sztring karakterlánc-verzióját ad vissza, amely hatékonyan dekódolja az URI-kódolású karakterláncot.

```
uriComponentToString('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A dekódolni kívánt URI-kódolású karakterlánc |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolva – uri*> | Sztring | Az URI-kódolású karakterlánc dekódolt verziója |
||||

*Példa*

Ez a példa az URI-kódolású karakterlánc dekódolt karakterlánc-verzióját hozza létre:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

És visszaadja ezt az eredményt: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Egy egységes erőforrás-azonosító (URI) `host` értékének visszaadása.

```
uriHost('<uri>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| *URI* -> < | Igen | Sztring | Az URI, amelynek `host` kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*gazdagép-érték*> | Sztring | A megadott URI `host` értéke |
||||

*Példa*

Ez a példa megkeresi az URI `host` értékét:

```
uriHost('https://www.localhost.com:8080')
```

És visszaadja ezt az eredményt: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Egy egységes erőforrás-azonosító (URI) `path` értékének visszaadása.

```
uriPath('<uri>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| *URI* -> < | Igen | Sztring | Az URI, amelynek `path` kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*path-value*> | Sztring | A megadott URI `path` értéke. Ha `path` nem rendelkezik értékkel, akkor a "/" karaktert kell visszaadnia. |
||||

*Példa*

Ez a példa megkeresi az URI `path` értékét:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És visszaadja ezt az eredményt: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Egy egységes erőforrás-azonosító (URI) `path` és `query` értékének visszaadása.

```
uriPathAndQuery('<uri>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| *URI* -> < | Igen | Sztring | Az az URI, amelynek `path` és `query` értékeit szeretné |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*elérési út – lekérdezés-érték*> | Sztring | A megadott URI `path` és `query` értékei. Ha `path` nem ad meg értéket, a "/" karaktert kell visszaadnia. |
||||

*Példa*

Ez a példa megkeresi az URI `path` és `query` értékeit:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És visszaadja ezt az eredményt: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Egy egységes erőforrás-azonosító (URI) `port` értékének visszaadása.

```
uriPort('<uri>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| *URI* -> < | Igen | Sztring | Az URI, amelynek `port` kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*port-érték*> | Egész szám | A megadott URI `port` értéke. Ha `port` nem ad meg értéket, a protokoll alapértelmezett portját küldje vissza. |
||||

*Példa*

Ez a példa a `port` értéket adja vissza ehhez az URI-hoz:

```
uriPort('http://www.localhost:8080')
```

És visszaadja ezt az eredményt: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Egy egységes erőforrás-azonosító (URI) `query` értékének visszaadása.

```
uriQuery('<uri>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| *URI* -> < | Igen | Sztring | Az URI, amelynek `query` kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*lekérdezés-érték*> | Sztring | A megadott URI `query` értéke |
||||

*Példa*

Ez a példa a `query` értéket adja vissza ehhez az URI-hoz:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És visszaadja ezt az eredményt: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>UriScheme

Egy egységes erőforrás-azonosító (URI) `scheme` értékének visszaadása.

```
uriScheme('<uri>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| *URI* -> < | Igen | Sztring | Az URI, amelynek `scheme` kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*séma – érték*> | Sztring | A megadott URI `scheme` értéke |
||||

*Példa*

Ez a példa a `scheme` értéket adja vissza ehhez az URI-hoz:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És visszaadja ezt az eredményt: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Az aktuális időbélyeg visszaadása.

```
utcNow('<format>')
```

A <*format*> paraméterrel eltérő formátumot is megadhat.


| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*formátum*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*aktuális időbélyeg*> | Sztring | Az aktuális dátum és idő |
||||

*1. példa*

Tegyük fel, hogy ma április 15., 2018-kor, 1:00:00 órakor.
Ez a példa az aktuális időbélyeget kapja:

```
utcNow()
```

És visszaadja ezt az eredményt: `"2018-04-15T13:00:00.0000000Z"`

*2. példa*

Tegyük fel, hogy ma április 15., 2018-kor, 1:00:00 órakor.
Ez a példa az aktuális időbélyeget az opcionális "D" formátum használatával olvassa be:

```
utcNow('D')
```

És visszaadja ezt az eredményt: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>változók

Egy megadott változó értékét adja vissza.

```
variables('<variableName>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Igen | Sztring | Annak a változónak a neve, amelynek a kívánt értéke |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*változó – érték*> | Bármelyik | A megadott változó értéke |
||||

*Példa*

Tegyük fel, hogy a "numItems" változó aktuális értéke 20.
Ez a példa beolvassa a változó egész értékét:

```
variables('numItems')
```

És visszaadja ezt az eredményt: `20`

<a name="workflow"></a>

### <a name="workflow"></a>munkafolyamat

A munkafolyamat összes adatának visszaküldése a Futtatás ideje alatt.

```
workflow().<property>
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*tulajdonság*> | Nem | Sztring | Annak a munkafolyamat-tulajdonságnak a neve, amelynek a kívánt értéke <p>A munkafolyamat-objektumok a következő tulajdonságokkal rendelkeznek: **név**, **típus**, **azonosító**, **hely**és **Futtatás**. A **Run** tulajdonság értéke szintén egy olyan objektum, amely a következő tulajdonságokkal rendelkezik: **név**, **típus**és **azonosító**. |
|||||

*Példa*

Ez a példa egy munkafolyamat aktuális futtatásának nevét adja vissza:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Egy JSON-objektumot tartalmazó sztring XML-verziójának visszaadása.

```
xml('<value>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó JSON-objektumot tartalmazó karakterlánc <p>A JSON-objektumnak csak egy gyökérszintű tulajdonsággal kell rendelkeznie, amely nem lehet tömb. <br>Használja a fordított perjel karaktert (\\) Escape-karakterként az idézőjelek (") számára. |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*XML-version*> | Objektum | A megadott karakterlánc-vagy JSON-objektumhoz tartozó kódolt XML |
||||

*1. példa*

Ez a példa a karakterlánchoz tartozó XML-verziót hozza létre, amely egy JSON-objektumot tartalmaz:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

És az eredmény XML-fájlját adja vissza:

```xml
<name>Sophia Owen</name>
```

*2. példa*

Tegyük fel, hogy rendelkezik a következő JSON-objektummal:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Ez a példa egy XML-fájlt hoz létre egy olyan karakterlánchoz, amely tartalmazza ezt a JSON-objektumot:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

És az eredmény XML-fájlját adja vissza:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>XPath

Az XML-t az XPath (XML-elérésiút) kifejezésnek megfelelő csomópontok vagy értékek esetében adja meg, és adja vissza a megfelelő csomópontokat vagy értékeket. Egy XPath-kifejezés, vagy csak "XPath", segít navigálni egy XML-dokumentum struktúrájában, hogy kiválassza a csomópontokat vagy számítási értékeket az XML-tartalomban.

```
xpath('<xml>', '<xpath>')
```

| Paraméter | Szükséges | Type (Típus) | Leírás |
| --------- | -------- | ---- | ----------- |
| <*XML-* > | Igen | Bármelyik | Az XPath-kifejezés értékének megfelelő csomópontok vagy értékek keresésére szolgáló XML-karakterlánc |
| <*xpath*> | Igen | Bármelyik | Az egyező XML-csomópontok vagy-értékek kereséséhez használt XPath-kifejezés |
|||||

| Visszatérési érték | Type (Típus) | Leírás |
| ------------ | ---- | ----------- |
| <*XML-csomópont*> | XML | XML-csomópont, ha csak egyetlen csomópont felel meg a megadott XPath-kifejezésnek |
| <*érték*> | Bármelyik | Egy XML-csomópont értéke, ha csak egyetlen érték egyezik a megadott XPath-kifejezéssel |
| [<*XML-csomópont1*>, <*xml-Csomópont2*>,...] </br>– vagy – </br>[<*érték1*>, <*érték2*>,...] | Tömb | A megadott XPath-kifejezésnek megfelelő XML-csomópontokkal vagy-értékekkel rendelkező tömb |
||||

*1. példa*

Ez a példa megkeresi a megadott argumentumok `<name></name>` csomópontjának megfelelő csomópontokat, és egy tömböt ad vissza ezekkel a csomópont-értékekkel:

`xpath(xml(parameters('items')), '/produce/item/name')`

Az argumentumok a következők:

* Az "Items" karakterlánc, amely tartalmazza ezt az XML-t:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  A példa a [Parameters ()](#parameters) függvény használatával kéri le az XML-karakterláncot az "Items" argumentumból, de a karakterláncot XML-formátumra is konvertálhatja az [XML ()](#xml) függvény használatával.

* Ez az XPath-kifejezés karakterláncként lesz átadva:

  `"/produce/item/name"`

Itt látható az eredményül kapott tömb a `<name></name`egyező csomópontokkal:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*2. példa*

Az 1. példában szereplő példa a `<count></count>` csomópontnak megfelelő csomópontokat keres, és hozzáadja ezeket a csomópont-értékeket a `sum()` függvénnyel:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

És visszaadja ezt az eredményt: `30`

*3. példa*

Ebben a példában mindkét kifejezés olyan csomópontokat talál, amelyek megfelelnek a `<location></location>` csomópontnak, a megadott argumentumokban, amelyek tartalmazzák az XML-névteret. 

> [!NOTE]
>
> Ha kód nézetben dolgozik, a perjel (") karakterrel (\\) megnyithatja a dupla idézőjelet ("). 
> Például ha egy kifejezést JSON-karakterláncként szerializál, a Escape-karaktereket kell használnia. 
> Ha azonban a Logic app Designerben vagy a kifejezés-szerkesztőben dolgozik, nem kell elmenekülnie az idézőjelek közé, mert a fordított perjel karaktert a rendszer automatikusan hozzáadja a mögöttes definícióhoz, például:
> 
> * Kód nézet: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Expression Editor: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
> 
> A következő példák a kifejezés-szerkesztőben megadott kifejezésekre vonatkoznak.

* *1. kifejezés*

  `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

* *2. kifejezés*

  `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

Az argumentumok a következők:

* Ez az XML, amely tartalmazza az XML-dokumentum névterét, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* A következő XPath-kifejezés:

  * `/*[name()="file"]/*[name()="location"]`

  * `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]`

Itt látható az `<location></location>` csomópontnak megfelelő eredmény-csomópont:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*4. példa*

A 3. példán látható példa a `<location></location>` csomópontban található értéket keresi:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

És visszaadja ezt az eredményt: `"Paris"`

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [munkafolyamat-definíciós nyelvről](../logic-apps/logic-apps-workflow-definition-language.md)
