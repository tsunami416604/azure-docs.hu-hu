---
title: Munkafolyamat Definition Language funkciók - Azure Logic Apps |} Microsoft Docs
description: További információk a logic apps Munkafolyamatdefiníciós nyelve a való létrehozásának funkciók
services: logic-apps
author: ecfan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/25/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 6f1871e1e135ecb9e7cb37c0bedff3737d3febb7
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301388"
---
# <a name="workflow-definition-language-functions-reference-for-azure-logic-apps"></a>Munkafolyamat definícióját nyelvi funkciók dokumentáció az Azure Logic Apps

Ez a cikk ismerteti a Funkciók, amelyekkel a munkafolyamatok létrehozásakor [Azure Logic Apps](../logic-apps/logic-apps-overview.md). A logikai alkalmazás definícióiról funkciók kapcsolatos további információkért lásd: [az Azure Logic Apps Munkafolyamatdefiníciós nyelve](../logic-apps/logic-apps-workflow-definition-language.md#functions). 

> [!NOTE]
> A paraméter-definíciók szintaxisának a kérdőjel (?), amely akkor jelenik meg, miután egy paraméter azt jelenti, hogy a paraméter nem kötelező megadni. Lásd például: [getFutureTime()](#getFutureTime).

<a name="action"></a>

## <a name="action"></a>művelet

Térjen vissza a *aktuális* művelet kimenetének futásidejű, illetve más JSON név-érték párok, rendelhet egy kifejezés, amely a értékeket. Alapértelmezés szerint ez a funkció a teljes műveletet objektumra hivatkozik, de nem kötelezően megadhatja a használni kívánt tulajdonság. Lásd még: [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Használhatja a `action()` függvény csak a ezeket a helyeket: 

* A `unsubscribe` tulajdonság a webhook művelet, hogy hozzáférhessen az eredmény az eredeti `subscribe` kérelem
* A `trackedProperties` tulajdonság művelet
* A `do-until` hurok egy művelet feltételét

```
action()
action().outputs.body.<property> 
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Tulajdonság*> | Nem | Sztring | A műveleti objektum tulajdonság használni kívánt nevét: **neve**, **startTime**, **endTime**, **bemenetek**,  **kimeneti**, **állapot**, **kód**, **trackingId**, és **clientTrackingId**. Az Azure portálon található ezeket a tulajdonságokat egy adott futtatási előzményei részletek megtekintésével. További információkért lásd: [REST API - munkafolyamat futtatásához műveletek](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | -----| ----------- | 
| <*o-művelet*> | Sztring | Az aktuális művelet vagy a tulajdonság a kimenetét | 
|||| 

<a name="actionBody"></a>

## <a name="actionbody"></a>actionBody

Egy műveletet vissza `body` kimeneti futásidőben. A rövid szintaxist `actions('<actionName>').outputs.body`. Lásd: [body()](#body) és [actions()](#actions).

```
actionBody('<actionName>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Műveletnév*> | Igen | Sztring | A művelet neve `body` kívánt kimeneti | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | -----| ----------- | 
| <*o-művelet-szervezet*> | Sztring | A `body` kimenetét a megadott művelet | 
|||| 

*Példa*

Ez a példa lekérdezi a `body` a Twitteren művelet eredményét `Get user`: 

```
actionBody('Get_user')
```

És az eredményt adja vissza:

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

## <a name="actionoutputs"></a>actionOutputs

Egy műveleti kimenet futásidőben visszaadása. A rövid szintaxist `actions('<actionName>').outputs`. Lásd: [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Műveletnév*> | Igen | Sztring | A művelet a nevét, amelyet kimenetének | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | -----| ----------- | 
| <*Kimeneti*> | Sztring | A megadott művelet kimenetében | 
|||| 

*Példa*

Ebben a példában a kimeneti lekérése a Twitter művelet `Get user`: 

```
actionOutputs('Get_user')
```

És az eredményt adja vissza:

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

## <a name="actions"></a>műveletek

Más JSON név-érték párok, rendelhet egy kifejezés, amely egy műveleti kimenet futásidejű, illetve értékeket visszaadása. Alapértelmezés szerint a függvény a teljes műveletet objektumra hivatkozik, de nem kötelezően megadhat egy tulajdonság amelynek kívánt értéket. Rövid szintaxist verzióiért lásd: [actionBody()](#actionBody), [actionOutputs()](#actionOutputs), és [body()](#body). Az aktuális művelet, tekintse meg a [action()](#action).

> [!NOTE] 
> Korábban, használhatja a `actions()` függvény vagy a `conditions` elem, hogy egy művelet futott alapján a kimenet a másik művelet megadásakor. Azonban deklarálható explicit módon műveletek közötti függőségeket, most segítségével kell a függő művelet `runAfter` tulajdonság. További részletek a `runAfter` tulajdonság, lásd: [Catch és kijavíthassa a hibákat a runAfter tulajdonsággal](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property> 
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Műveletnév*> | Igen | Sztring | A művelet azt szeretné, amelynek kimenete objektum nevét  | 
| <*Tulajdonság*> | Nem | Sztring | A műveleti objektum tulajdonság használni kívánt nevét: **neve**, **startTime**, **endTime**, **bemenetek**,  **kimeneti**, **állapot**, **kód**, **trackingId**, és **clientTrackingId**. Az Azure portálon található ezeket a tulajdonságokat egy adott futtatási előzményei részletek megtekintésével. További információkért lásd: [REST API - munkafolyamat futtatásához műveletek](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | -----| ----------- | 
| <*o-művelet*> | Sztring | A megadott művelet vagy tulajdonság kimenete | 
|||| 

*Példa*

Ez a példa lekérdezi a `status` tulajdonság értékek forrását a Twitter művelet `Get user` futásidőben: 

```
actions('Get_user').outputs.body.status 
```

És az eredményt adja vissza: `"Succeeded"`

<a name="add"></a>

## <a name="add"></a>hozzáadás

Az eredményt adjanak hozzá két számot.

```
add(<summand_1>, <summand_2>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*summand_1*>, <*summand_2*> | Igen | Egész szám, lebegőpontos, vagy vegyes | A számok hozzáadása | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | -----| ----------- | 
| <*eredmény összeg*> | Egész szám vagy lebegőpontos | A megadott számok kapott eredmény | 
|||| 

*Példa*

Ebben a példában a megadott számok hozzáadása:

```
add(1, 1.5)
```

És az eredményt adja vissza: `2.5`

<a name="addDays"></a>

## <a name="adddays"></a>napokHozzaadasa

Adja hozzá a napok számát időbélyeg.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*nap*> | Igen | Egész szám | A napok hozzáadása pozitív vagy negatív szám | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott napok száma alapján  | 
|||| 

*1. példa*

Ebben a példában a 10 napos hozzáadja a megadott időbélyeg:

```
addDays('2018-03-15T13:00:00Z', 10)
```

És az eredményt adja vissza: `"2018-03-25T00:00:0000000Z"`

*2. példa*

Ez a példa kivonja a megadott időbélyeg 5 napos:

```
addDays('2018-03-15T00:00:00Z', -5)
```

És az eredményt adja vissza: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

## <a name="addhours"></a>addHours

Adja hozzá egy Timestamp típusú órák száma.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*Üzemideje (óra)*> | Igen | Egész szám | Az órát adja hozzá a pozitív vagy negatív szám | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú üzemideje (óra)  | 
|||| 

*1. példa*

Ebben a példában a megadott időbélyeg 10 órát ad:

```
addHours('2018-03-15T00:00:00Z', 10)
```

És az eredményt adja vissza: `"2018-03-15T10:00:0000000Z"`

*2. példa*

Ez a példa kivonja a megadott időbélyeg öt órát:

```
addHours('2018-03-15T15:00:00Z', -5)
```

És az eredményt adja vissza: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

## <a name="addminutes"></a>addMinutes

Adja hozzá a percek száma időbélyeg.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*perc*> | Igen | Egész szám | A hozzáadandó perc pozitív vagy negatív szám | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú perc | 
|||| 

*1. példa*

Ez a példa 10 perc hozzáadja a megadott időbélyeg:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

És az eredményt adja vissza: `"2018-03-15T00:20:00.0000000Z"`

*2. példa*

Ez a példa kivonja a megadott időbélyeg öt perc:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

És az eredményt adja vissza: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

## <a name="addproperty"></a>addProperty

Tulajdonság és az érték vagy a név-érték pár hozzáadása egy JSON-objektum, és térjen vissza a frissített objektum. Ha az objektum már létezik futásidőben, a függvény hibát jelez.

```
addProperty(<object>, '<property>', <value>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Objektum*> | Igen | Objektum | A JSON-objektumból, ahová tulajdonság hozzáadása | 
| <*Tulajdonság*> | Igen | Sztring | A tulajdonság nevét | 
| <*Érték*> | Igen | Bármelyik | A tulajdonság értéke |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített objektum*> | Objektum | A frissített JSON-objektum, a megadott tulajdonság | 
|||| 

*Példa*

Ez a példa hozzáadja a `accountNumber` tulajdonságot a `customerProfile` objektum, amely alakítja át a JSON-t a [JSON()](#json) függvény. A függvény által létrehozott értéket rendeli hozzá a [guid()](#guid) működik, és a frissített objektumot:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

## <a name="addseconds"></a>masodpercekHozzaadasa

Adja hozzá a másodpercek számát időbélyeg.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*Másodpercben*> | Igen | Egész szám | A hozzáadandó másodpercben pozitív vagy negatív szám | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú másodpercnél tovább  | 
|||| 

*1. példa*

Ez a példa hozzáadja a megadott időbélyeg 10 másodperc:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

És az eredményt adja vissza: `"2018-03-15T00:00:10.0000000Z"`

*2. példa*

Ez a példa kivonja a megadott Timestamp öt másodpercenként:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

És az eredményt adja vissza: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

## <a name="addtotime"></a>addToTime

Időbélyeg időegységek számát adja hozzá. Lásd még: [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*időköz*> | Igen | Egész szám | Adja hozzá a megadott idő egységek száma | 
| <*timeUnit*> | Igen | Sztring | A használandó egysége *időköz*: "Második", "Minute", "Hour", "Day", "Hetente", "Honap", "EV" | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | Az időbélyeget és az időegységek megadott száma  | 
|||| 

*1. példa*

Ez a példa egy nap hozzáadja a megadott időbélyeg:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day') 
```

És az eredményt adja vissza: `"2018-01-02T00:00:00:0000000Z"`

*2. példa*

Ez a példa egy nap hozzáadja a megadott időbélyeg:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

A választható "D" formátumban adja vissza: `"Tuesday, January 2, 2018"`

<a name="and"></a>

## <a name="and"></a>és

Ellenőrzése, hogy összes kifejezés igaz. Igaz értéket ad vissza összes kifejezés igaz értéke esetén, vagy vissza hamis, ha legalább egy kifejezés értéke false.

```
and(<expression1>, <expression2>, ...)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Kifejezés1*>, <*Kifejezés2*>,... | Igen | Logikai | A kifejezések ellenőrzése | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | -----| ----------- | 
| IGAZ vagy HAMIS eredményt ad | Logikai | Vissza a igaz, ha összes kifejezés igaz. Vissza a hamis értéket, ha legalább egy kifejezés értéke false. | 
|||| 

*1. példa*

Ezek a példák ellenőrzése, hogy a megadott logikai értékek összes igaz:

```
and(true, true)
and(false, true)
and(false, false)
```

És visszaadja az eredmények:

* Első példa: mindkét kifejezés igaz, így függvény `true`. 
* Második példa: egy kifejezés értéke false, ezért adja vissza `false`.
* Harmadik példa: mindkét kifejezés hamis értékű, ezért függvény `false`.

*2. példa*

Ezek a példák ellenőrzése, hogy az adott kifejezések teljesülésekor:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

És visszaadja az eredmények:

* Első példa: mindkét kifejezés igaz, így függvény `true`. 
* Második példa: egy kifejezés értéke false, ezért adja vissza `false`.
* Harmadik példa: mindkét kifejezés hamis értékű, ezért függvény `false`.

<a name="array"></a>

## <a name="array"></a>tömb

Térjen vissza a megadott egyetlen bemeneti tömb. Több bemenetek, lásd: [createArray()](#createArray). 

```
array('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | A karakterlánc egy tömb létrehozásához | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| [<*érték*>] | Tömb | Olyan tömb, amely tartalmazza az egyetlen megadott bemeneti érték | 
|||| 

*Példa*

Ebben a példában a "hello" karakterláncból hoz létre egy tömb:

```
array('hello')
```

És az eredményt adja vissza: `["hello"]`

<a name="base64"></a>

## <a name="base64"></a>a Base64

A base64-kódolású verzió karakterlánc visszaadása.

```
base64('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | A bemeneti karakterlánc | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*a Base64-karakterlánc*> | Sztring | A bemeneti karakterlánc base64-kódolású verzióját | 
|||| 

*Példa*

Ebben a példában a "hello" base64 kódolású karakterlánc alakít át:

```
base64('hello')
```

És az eredményt adja vissza: `"aGVsbG8="`

<a name="base64ToBinary"></a>

## <a name="base64tobinary"></a>base64ToBinary

A bináris verzió base64 kódolású karakterlánc visszaadása.

```
base64ToBinary('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | A base64 kódolású karakterlánc konvertálni | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*bináris a Base64 kódolású karakterlánc*> | Sztring | A base64 kódolású karakterlánc bináris verzió | 
|||| 

*Példa*

Ez a példa alakítja át a "aGVsbG8 =" base64 kódolású karakterlánc bináris karakterláncra:

```
base64ToBinary('aGVsbG8=')
```

És az eredményt adja vissza: 

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

## <a name="base64tostring"></a>base64ToString

Térjen vissza a karakterlánc verzió base64 kódolású karakterlánc, hatékonyan dekódolás a Base64 kódolású karakterlánc. Ezzel a funkcióval helyett [decodeBase64()](#decodeBase64). Bár mindkét funkciók működik, `base64ToString()` részesíti előnyben.

```
base64ToString('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | A base64 kódolású karakterlánc dekódolás | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*dekódolni base64-karakterlánc*> | Sztring | A karakterlánc-verzió a base64 kódolású karakterlánc | 
|||| 

*Példa*

Ez a példa alakítja át a "aGVsbG8 =" base64 kódolású karakterlánc, csak egy karakterlánc:

```
base64ToString('aGVsbG8=')
```

És az eredményt adja vissza: `"hello"`

<a name="binary"></a>

## <a name="binary"></a>Bináris 

A bináris verzió karakterlánc visszaadása.

```
binary('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | Alakítandó karakterláncot | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*bináris a bemeneti érték*> | Sztring | A bináris verzióra a megadott karakterlánc | 
|||| 

*Példa*

Ebben a példában a "hello" karakterlánc bináris karakterlánccá alakítja át:

```
binary('hello')
```

És az eredményt adja vissza: 

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

## <a name="body"></a>törzs

Egy műveletet vissza `body` kimeneti futásidőben. A rövid szintaxist `actions('<actionName>').outputs.body`. Lásd: [actionBody()](#actionBody) és [actions()](#actions).

```
body('<actionName>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Műveletnév*> | Igen | Sztring | A művelet neve `body` kívánt kimeneti | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | -----| ----------- | 
| <*o-művelet-szervezet*> | Sztring | A `body` kimenetét a megadott művelet | 
|||| 

*Példa*

Ez a példa lekérdezi a `body` kimenetét a `Get user` Twitter művelet: 

```
body('Get_user')
```

És az eredményt adja vissza: 

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

## <a name="bool"></a>logikai érték

Egy érték logikai verzióját adja vissza.

```
bool(<value>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Bármelyik | Az érték átalakítása | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad | Logikai | A megadott értéknek Boolean verzióját | 
|||| 

*Példa*

Ezekben a példákban a megadott érték átalakítása logikai értékek: 

```
bool(1)
bool(0)
```

És visszaadja az eredmények: 

* Első példa: `true` 
* Második példáját: `false`

<a name="coalesce"></a>

## <a name="coalesce"></a>Egyesítés

Az első nem üres érték visszaadása egy vagy több paramétert. Üres karakterláncokat, üres tömbök és üres objektumok csak NULL értékű.

```
coalesce(<object_1>, <object_2>, ...)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*object_1*>, <*object_2*>,... | Igen | Bármely, lehet összekeveri az típusok | Egy vagy több elem null kereséséhez | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*első-nem-null-elem*> | Bármelyik | Az első elem vagy nem null értéket. Ha minden paraméter null értékű, ez a függvény null értéket ad vissza. | 
|||| 

*Példa*

Ezek a példák vissza az első nem üres értéket a megadott értékeket, vagy null null értékek esetén:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

És visszaadja az eredmények: 

* Első példa: `true` 
* Második példáját: `"hello"`
* Harmadik példa: `null`

<a name="concat"></a>

## <a name="concat"></a>Concat

Két vagy több karakterlánc kombinálhatja, és térjen vissza a kombinált karakterlánc. 

```
concat('<text1>', '<text2>', ...)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg1*>, <*szöveg2*>,... | Igen | Sztring | Legalább két karakterlánc egyesítése | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*text1text2...*> | Sztring | A kombinált bemeneti karakterláncok alapján létrehozott karakterlánc | 
|||| 

*Példa*

Ebben a példában a "Hello" és "World" karakterlánc egyesíti:

```
concat('Hello', 'World')
```

És az eredményt adja vissza: `"HelloWorld"`

<a name="contains"></a>

## <a name="contains"></a>tartalmazza a következőt:

Ellenőrizze, hogy egy gyűjtemény van-e egy adott cikk. Igaz értéket ad vissza, ha az elem található, vagy vissza hamis mikor nem található. Ez a függvény megkülönbözteti a kis-és nagybetűket.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Pontosabban a funkció ezen gyűjteménytípusokon működik: 

* A *karakterlánc* található egy *substring*
* Egy *tömb* található egy *érték*
* A *szótár* található egy *kulcs*

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Gyűjtemény*> | Igen | Karakterlánc, a tömb vagy a könyvtár | Ellenőrizze a gyűjtemény | 
| <*Érték*> | Igen | Karakterlánc, tömbben vagy szótár, illetve | Az elem keresése | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad | Logikai | Vissza a igaz, ha az elem található. Térjen vissza hamis mikor nem található. |
|||| 

*1. példa*

Ebben a példában a karakterlánc a "world" substring "hello world" ellenőrzi, és IGAZ értéket ad vissza:

```
contains('hello world', 'world')
```

*2. példa*

Ebben a példában a karakterlánc a "universe" substring "hello world" ellenőrzi, és hamis értéket ad vissza:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

## <a name="convertfromutc"></a>convertFromUtc

Időbélyeg az idő egyezményes világidő (UTC) konvertálja a célként megadott időzóna.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*destinationTimeZone*> | Igen | Sztring | A célként megadott időzóna neve. További információkért lásd: [időzóna azonosítók](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*konvertálja időbélyeg*> | Sztring | Az időbélyeg alakítja át a cél időzóna | 
|||| 

*1. példa*

Ebben a példában a megadott időzóna időbélyeg alakít: 

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

És az eredményt adja vissza: `"2018-01-01T00:00:00.0000000"`

*2. példa*

Ebben a példában a időbélyeg alakítja a megadott időzóna és formátuma:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

És az eredményt adja vissza: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

## <a name="converttimezone"></a>convertTimeZone

A célként megadott időzóna a forrás időzóna átalakítása időbélyeg.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*sourceTimeZone*> | Igen | Sztring | A forrás időzóna neve. További információkért lásd: [időzóna azonosítók](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*destinationTimeZone*> | Igen | Sztring | A célként megadott időzóna neve. További információkért lásd: [időzóna azonosítók](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*konvertálja időbélyeg*> | Sztring | Az időbélyeg alakítja át a cél időzóna | 
|||| 

*1. példa*

Ebben a példában a forrás időzóna konvertálja a célként megadott időzóna: 

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

És az eredményt adja vissza: `"2018-01-01T00:00:00.0000000"`

*2. példa*

Ebben a példában a megadott időzóna és formázását alakítja át egy időzóna:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

És az eredményt adja vissza: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

## <a name="converttoutc"></a>convertToUtc

Konvertálása időbélyeg a forrás időzóna az idő egyezményes világidő (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*sourceTimeZone*> | Igen | Sztring | A forrás időzóna neve. További információkért lásd: [időzóna azonosítók](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*konvertálja időbélyeg*> | Sztring | Az UTC szerint konvertálja időbélyeg | 
|||| 

*1. példa*

Ebben a példában időbélyeg UTC szerint konvertálja: 

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

És az eredményt adja vissza: `"2018-01-01T08:00:00.0000000Z"`

*2. példa*

Ebben a példában időbélyeg UTC szerint konvertálja:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

És az eredményt adja vissza: `"Monday, January 1, 2018"`

<a name="createArray"></a>

## <a name="createarray"></a>createArray

A több bemenetei olyan tömböt adjon vissza. Egyetlen bemeneti tömbök, lásd: [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Object1*>, <*object2*>,... | Igen | Bármely, de nem vegyes | A tömb létrehozásához legalább két elemet | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| [<*object1*>, <*object2*>,...] | Tömb | A bemeneti elemeinek alapján létrehozott tömb | 
|||| 

*Példa*

Ebben a példában a bemeneti tömb hoz:

```
createArray('h', 'e', 'l', 'l', 'o')
```

És az eredményt adja vissza: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

## <a name="datauri"></a>dataUri

Térjen vissza a adatok egységes erőforrás-azonosítója (URI) egy karakterláncot kell megadnia. 

```
dataUri('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | Alakítandó karakterláncot | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*adat-URI-azonosítót*> | Sztring | Az adat-URI azonosító a bemeneti karakterlánc | 
|||| 

*Példa*

Ez a példa létrehoz egy adat-URI azonosító a "hello" karakterláncot:

```
dataUri('hello') 
```

És az eredményt adja vissza: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

## <a name="datauritobinary"></a>dataUriToBinary

Térjen vissza a bináris adatok egységes erőforrás-azonosítója (URI) verziója. Ezzel a funkcióval helyett [decodeDataUri()](#decodeDataUri). Bár mindkét funkciók működik, `decodeDataUri()` részesíti előnyben.

```
dataUriToBinary('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | Az adat-URI azonosító konvertálni | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*bináris az adatok uri*> | Sztring | Az adat-URI azonosító bináris verzióját | 
|||| 

*Példa*

Ez a példa egy bináris verziót ezeket az adatokat URI hoz létre:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És az eredményt adja vissza: 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

## <a name="datauritostring"></a>dataUriToString

Térjen vissza a karakterlánc verzió adatok egységes erőforrás-azonosító (URI).

```
dataUriToString('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | Az adat-URI azonosító konvertálni | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*karakterlánc az adatok uri*> | Sztring | Az adat-URI azonosító karakterlánc verzióját | 
|||| 

*Példa*

Ebben a példában a karakterlánc ezeket az adatokat URI hoz létre:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És az eredményt adja vissza: `"hello"`

<a name="dayOfMonth"></a>

## <a name="dayofmonth"></a>DayOfMonth

Vissza a hónap napját időbélyeg. 

```
dayOfMonth('<timestamp>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*hónap napja*> | Egész szám | A megadott időbélyeg a hónap napját | 
|||| 

*Példa*

Ebben a példában a közötti számot ad vissza a hónap napjának az időbélyegző:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

És az eredményt adja vissza: `15`

<a name="dayOfWeek"></a>

## <a name="dayofweek"></a>DayOfWeek

A hét napjának visszaadása időbélyeg.  

```
dayOfWeek('<timestamp>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*a hét napja*> | Egész szám | A megadott időbélyeg vasárnap esetén 0, hétfőtől a hét napja, 1, és így tovább | 
|||| 

*Példa*

Ebben a példában a közötti számot ad vissza a hét napjára az időbélyegző:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

És az eredményt adja vissza: `3`

<a name="dayOfYear"></a>

## <a name="dayofyear"></a>DayOfYear

Az év napjának visszaadása a Timestamp típusú. 

```
dayOfYear('<timestamp>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*év napja*> | Egész szám | A megadott időbélyeg az év napját | 
|||| 

*Példa*

Ebben a példában az időbélyegző ad vissza az év napjának száma:

```
dayOfYear('2018-03-15T13:27:36Z')
```

És az eredményt adja vissza: `74`

<a name="decodeBase64"></a>

## <a name="decodebase64"></a>decodeBase64

Térjen vissza a karakterlánc verzió base64 kódolású karakterlánc, hatékonyan dekódolás a Base64 kódolású karakterlánc. Érdemes lehet [base64ToString()](#base64ToString) helyett `decodeBase64()`. Bár mindkét funkciók működik, `base64ToString()` részesíti előnyben.

```
decodeBase64('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | A base64 kódolású karakterlánc dekódolás | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*dekódolni base64-karakterlánc*> | Sztring | A karakterlánc-verzió a base64 kódolású karakterlánc | 
|||| 

*Példa*

Ebben a példában a karakterlánc a base64 kódolású karakterlánc hoz létre:

```
decodeBase64('aGVsbG8=')
```

És az eredményt adja vissza: `"hello"`

<a name="decodeDataUri"></a>

## <a name="decodedatauri"></a>decodeDataUri

Térjen vissza a bináris adatok egységes erőforrás-azonosítója (URI) verziója. Érdemes lehet [dataUriToBinary()](#dataUriToBinary), hanem `decodeDataUri()`. Bár mindkét funkciók működik, `dataUriToBinary()` részesíti előnyben.

```
decodeDataUri('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | Az adatok dekódolására a URI karakterlánc | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*bináris az adatok uri*> | Sztring | A bináris verzió a URI karakterlánc adatok | 
|||| 

*Példa*

Ebben a példában a bináris verzió URI ezeket az adatokat adja vissza:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És az eredményt adja vissza: 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

## <a name="decodeuricomponent"></a>decodeUriComponent

Visszatérés egy karakterlánc, hogy cserél escape-karakter dekódolt verzióival. 

```
decodeUriComponent('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | A karakterlánc a escape-karakterekkel dekódolás | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*dekódolni uri*> | Sztring | A dekódolt escape-karakterekkel frissített karakterlánc | 
|||| 

*Példa*

Ebben a példában az escape-karaktereket, ez a karakterlánc a felváltja a dekódolt verzióival:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

És az eredményt adja vissza: `"https://contoso.com"`

<a name="div"></a>

## <a name="div"></a>DIV

Két szám osztásával kapott térjen vissza az egész típusú eredményként. A fennmaradó eredményt kapja, lásd: [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*osztandó*> | Igen | Egész szám vagy lebegőpontos | A szám nullával a *osztó* | 
| <*osztó*> | Igen | Egész szám vagy lebegőpontos | A szám, amellyel a *osztandó*, de nem lehet 0 | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*hányadosa-eredménye*> | Egész szám | Az egész típusú eredményként a második szám szerint az első szám osztásával kapott | 
|||| 

*Példa*

Mindkét példák osztani az első szám, a második szám szerint:

```
div(10, 5)
div(11, 5)
```

És az eredményt adja vissza: `2`

<a name="encodeUriComponent"></a>

## <a name="encodeuricomponent"></a>encodeUriComponent

Térjen vissza a karakterlánc egy egységes erőforrás-azonosító (URI) kódolású verziót tartományvezérlőkkel történő lecserélésével URL nem biztonságos karakterek escape-karakter. Érdemes lehet [uriComponent()](#uriComponent), hanem `encodeUriComponent()`. Bár mindkét funkciók működik, `uriComponent()` részesíti előnyben.

```
encodeUriComponent('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | Az URI-ként kódolt formában alakítandó karakterláncot | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*uri-kódolása*> | Sztring | Az escape-karakterekkel URI-kódolású karakterlánc | 
|||| 

*Példa*

Ebben a példában ez a karakterlánc egy URI-kódolt verziót hoz létre:

```
encodeUriComponent('https://contoso.com')
```

És az eredményt adja vissza: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

## <a name="empty"></a>üres

Ellenőrizze, hogy egy gyűjtemény üres. Igaz értéket ad vissza üres gyűjtemény esetén, vagy visszatérhet a hamis értéket, ha nem üres.

```
empty('<collection>')
empty([<collection>])
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Gyűjtemény*> | Igen | Karakterlánc, a tömb vagy objektum | Ellenőrizze a gyűjtemény | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad | Logikai | Vissza a igaz, ha a gyűjtemény üres. Térjen vissza a hamis értéket, ha nem üres. | 
|||| 

*Példa* 

Ezek a példák ellenőrzése, hogy a megadott gyűjteményekkel üres:

```
empty('')
empty('abc')
```

És visszaadja az eredmények: 

* Első példa: üres karakterlánc, továbbítja, a függvény `true`. 
* Második példáját: továbbítja a "abc", a karakterlánc, a függvény `false`. 

<a name="endswith"></a>

## <a name="endswith"></a>megadott módon végződő

Ellenőrizze, hogy egy karakterláncot végződik-e egy adott karakterláncrészletet. Igaz értéket ad vissza a karakterláncrészt programokat, vagy vissza hamis mikor nem található. Ez a funkció nincs kis-és nagybetűket.

```
endsWith('<text>', '<searchText>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg*> | Igen | Sztring | A karakterlánc | 
| <*keresettszöveg amelyben*> | Igen | Sztring | A befejezési karakterláncrész keresése | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad  | Logikai | Vissza a igaz, ha az megtalálható-e a befejezési karakterláncrészletet. Térjen vissza hamis mikor nem található. | 
|||| 

*1. példa* 

Ez a példa ellenőrzi, hogy a "hello world" karakterlánc a "world" karakterlánccal végződik-e:

```
endsWith('hello world', 'world')
```

És az eredményt adja vissza: `true`

*2. példa*

Ez a példa ellenőrzi, hogy a "hello world" karakterlánc "universe" karakterlánccal végződik-e:

```
endsWith('hello world', 'universe')
```

És az eredményt adja vissza: `false`

<a name="equals"></a>

## <a name="equals"></a>egyenlő

Ellenőrzése, hogy mindkét értékek, kifejezés vagy objektumok megfelelő-e. Vissza a igaz, ha az egyenértékű, vagy mindkét hamis vissza, ha azok nem megfelelő.

```
equals('<object1>', '<object2>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Object1*>, <*object2*> | Igen | különböző | A értékek, kifejezések vagy az összehasonlítandó objektumok | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad | Logikai | Vissza a igaz, ha az egyenértékű mindkettő. Térjen vissza a hamis értéket, ha nem megfelelő. | 
|||| 

*Példa*

Ezek a példák ellenőrzése, hogy a megadott bemeneti adatok egyenértékű. 

```
equals(true, 1)
equals('abc', 'abcd')
```

És visszaadja az eredmények: 

* Első példa: két egyenértékűek, a függvény `true`.
* Második exmaple: mindkét értéket nem egyenértékű, a függvény `false`.

<a name="first"></a>

## <a name="first"></a>első

Vissza az első elem egy karakterlánc vagy tömb.

```
first('<collection>')
first([<collection>])
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény első elemének megkeresése |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*első gyűjteményelem*> | Bármelyik | Az első elem a gyűjteményben | 
|||| 

*Példa*

Ezekben a példákban szereplő ezekhez a gyűjteményekhez első elem megkeresése:

```
first('hello')
first([0, 1, 2])
```

És az eredmények visszaadása: 

* Első példa: `"h"`
* Második exmaple: `0`

<a name="float"></a>

## <a name="float"></a>lebegőpontos

A karakterlánc verziójú konvertálni a lebegőpontos szám egy tényleges lebegőpontos szám. E funkció használata csak akkor, ha egyéni paraméterek átadása alkalmazások, például a logikai alkalmazás számára.

```
float('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | A karakterlánc, amely rendelkezik alakítható át érvényes lebegőpontos szám |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*érték lebegőpontos érték*> | Lebegőpontos | A megadott karakterlánc lebegőpontos szám | 
|||| 

*Példa*

Ez a példa létrehoz egy karakterlánc verziót a lebegőpontos szám:

```
float('10.333')
```

És az eredményt adja vissza: `10.333`

<a name="formatDateTime"></a>

## <a name="formatdatetime"></a>formatDateTime

Térjen vissza az időbélyeg formátuma.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*újraformázza időbélyeg*> | Sztring | A frissített időbélyeg formátuma | 
|||| 

*Példa*

Ez a példa időbélyeg a megadott formátumra alakítja át:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

És az eredményt adja vissza: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

## <a name="formdatamultivalues"></a>formDataMultiValues

Ettől a kulcs nevét egy művelet olyan tömböt adjon vissza *űrlapadat* vagy *űrlap-kódolású* kimeneti. 

```
formDataMultiValues('<actionName>', '<key>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Műveletnév*> | Igen | Sztring | A műveletet, amelynek kimenete van a kulcs értéke | 
| <*Kulcs*> | Igen | Sztring | A használni kívánt kulcs nevét | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| [<*tömb a kulcs értékét*>] | Tömb | Az összes, amelyek megfelelnek a megadott kulcs tömb | 
|||| 

*Példa* 

Ez a példa egy tömb be a "Tárgy" kulcs értéke a megadott művelet űrlapadat vagy űrlap-kódolású kimeneti hoz létre:  

```
formDataMultiValues('Send_an_email', 'Subject')
```

És a tulajdonos a szöveget adja vissza egy tömb, például: `["Hello world"]`

<a name="formDataValue"></a>

## <a name="formdatavalue"></a>formDataValue

Egyetlen érték, amely megfelel a kulcs nevét, egy műveletben visszaadnia *űrlapadat* vagy *űrlap-kódolású* kimeneti. Ha a függvény egynél több egyezést talál, a függvény hibát jelez.

```
formDataValue('<actionName>', '<key>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Műveletnév*> | Igen | Sztring | A műveletet, amelynek kimenete van a kulcs értéke | 
| <*Kulcs*> | Igen | Sztring | A használni kívánt kulcs nevét |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*kulcs-érték*> | Sztring | A megadott kulcs értéke  | 
|||| 

*Példa* 

Ebben a példában a "Tárgy" kulcs-érték a megadott művelet űrlapadat vagy kimeneti űrlap-kódolású karakterlánc létrehoz:  

```
formDataValue('Send_an_email', 'Subject')
```

És szöveget adja vissza a tulajdonos karakterláncként, például: `"Hello world"`

<a name="getFutureTime"></a>

## <a name="getfuturetime"></a>getFutureTime

Az aktuális időbélyeg és a megadott mértékegységét adja vissza.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időköz*> | Igen | Egész szám | A kivonandó időnél megadott idő egységek száma | 
| <*timeUnit*> | Igen | Sztring | A használandó egysége *időköz*: "Második", "Minute", "Hour", "Day", "Hetente", "Honap", "EV" | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | Az aktuális időbélyeg és a megadott számú időegység | 
|||| 

*1. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-03-01T00:00:00.0000000Z". Ez a példa hozzáadja az időbélyeg öt nap:

```
getFutureTime(5, 'Day')
```

És az eredményt adja vissza: `"2018-03-06T00:00:00.0000000Z"`

*2. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-03-01T00:00:00.0000000Z". Ebben a példában öt nap hozzáadja, és a "D" formátumra alakítja át az eredmény:

```
getFutureTime(5, 'Day', 'D')
```

És az eredményt adja vissza: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

## <a name="getpasttime"></a>getPastTime

A megadott időegységek mínusz aktuális időbélyeg visszaadása.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időköz*> | Igen | Egész szám | A kivonandó időnél megadott idő egységek száma | 
| <*timeUnit*> | Igen | Sztring | A használandó egysége *időköz*: "Második", "Minute", "Hour", "Day", "Hetente", "Honap", "EV" | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | A megadott számú időegységek mínusz aktuális időbélyeg | 
|||| 

*1. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-02-01T00:00:00.0000000Z". Ebben a példában az időbélyeg 5 napos kivonja:

```
getPastTime(5, 'Day')
```

És az eredményt adja vissza: `"2018-01-27T00:00:00.0000000Z"`

*2. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-02-01T00:00:00.0000000Z". Ez a példa levonja öt nap, és a "D" formátumra alakítja át az eredmény:

```
getPastTime(5, 'Day', 'D')
```

És az eredményt adja vissza: `"Saturday, January 27, 2018"`

<a name="greater"></a>

## <a name="greater"></a>nagyobb

Ellenőrizze, hogy az első érték nagyobb, mint a második érték. Igaz értéket ad vissza az első érték további esetén, vagy vissza hamis mikor kevesebb.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Egész szám, lebegőpontos vagy karakterlánc | Ellenőrizze, hogy nagyobb, mint a második érték első érték | 
| <*compareTo*> | Igen | Egész szám, lebegőpontos vagy karakterlánc, illetve | Az összehasonlítási érték | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad | Logikai | Vissza a igaz, ha az első érték nagyobb, mint a második érték. Vissza a hamis értéket, ha az első érték kisebb, mint a második érték. | 
|||| 

*Példa*

Ezek a példák ellenőrizze, hogy az első érték nagyobb, mint a második érték:

```
greater(10, 5)
greater('apple', 'banana')
```

És az eredmények visszaadása: 

* Első példa: `true`
* Második példáját: `false`

<a name="greaterOrEquals"></a>

## <a name="greaterorequals"></a>greaterOrEquals

Ellenőrizze, hogy az első érték kisebb, mint a második érték.
Igaz értéket ad vissza az első érték kisebb, mint amikor, vagy vissza hamis, ha az első érték kisebb.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Egész szám, lebegőpontos vagy karakterlánc | Ellenőrizze, hogy kisebb, mint a második érték első érték | 
| <*compareTo*> | Igen | Egész szám, lebegőpontos vagy karakterlánc, illetve | Az összehasonlítási érték | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad | Logikai | Vissza a igaz, amikor az első érték kisebb, mint a második érték. Térjen vissza a hamis értéket, ha az első érték kisebb, mint a második érték. | 
|||| 

*Példa*

Ezekben a példákban ellenőrizze, hogy az első érték nagyobb vagy egyenlő, mint a második érték:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

És az eredmények visszaadása: 

* Első példa: `true`
* Második példáját: `false`

<a name="guid"></a>

## <a name="guid"></a>GUID

Karakterlánc, például "c2ecc88d-88c8-4096-912c-d6f2e2b138ce" készítése a globálisan egyedi azonosítóját (GUID): 

```
guid()
```

Emellett az alapértelmezettől eltérő, "D", amely 32 számjegy betű kötőjelekkel tagolva GUID más formátumú is megadhat.

```
guid('<format>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Formátumban*> | Nem | Sztring | Egyetlen [megadása formázása](https://msdn.microsoft.com/library/97af8hh4) visszaadott GUID. Alapértelmezés szerint a formátum: "D", de használhatja a "N", "D", "B", "P" vagy "X". | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*GUID-érték*> | Sztring | Egy véletlenszerűen előállított GUID Azonosítóhoz | 
|||| 

*Példa* 

Ebben a példában állít elő, ugyanaz a GUID azonosítója, de 32 számjegy, kötőjelekkel tagolva, és egyenként zárójelbe: 

```
guid('P')
```

És az eredményt adja vissza: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

## <a name="if"></a>Ha

Ellenőrizze, hogy egy kifejezés igaz vagy hamis. Az eredmény alapján, a megadott érték visszaadása.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*kifejezés*> | Igen | Logikai | A kifejezés kereséséhez | 
| <*valueIfTrue*> | Igen | Bármelyik | A visszatérési érték, ha a kifejezés igaz értékű | 
| <*valueIfFalse*> | Igen | Bármelyik | Az érték vissza, ha a kifejezés értéke "false" | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*a megadott visszatérési-értéke*> | Bármelyik | A megadott értéket visszaadó alapján meg, hogy a kifejezés igaz vagy hamis | 
|||| 

*Példa* 

Ez a példa adja vissza `"yes"` , mert a megadott kifejezés igaz értéket ad vissza. Ellenkező esetben adja vissza a példa `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

## <a name="indexof"></a>indexOf

Térjen vissza a kezdő pozíciót vagy a substring lemezképindex-értékét. Ez a funkció nincs kis-és nagybetűket, és a 0 számmal kezdődik. 

```
indexOf('<text>', '<searchText>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg*> | Igen | Sztring | A karakterlánc, amely rendelkezik a karakterláncrész keresése | 
| <*keresettszöveg amelyben*> | Igen | Sztring | A karakterláncrész keresése | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*index-érték*>| Egész szám | A megadott karakterláncrészlet kezdő pozíció vagy az index értéket. <p>Ha a karakterlánc nem található, a -1 számot adja vissza. </br>Ha a karakterlánc üres, térjen vissza a 0 számot. | 
|||| 

*Példa* 

Ebben a példában a kezdő index értéket talált a "hello world" karakterlánc a "world" karakterláncrészlet:

```
indexOf('hello world', 'world')
```

És az eredményt adja vissza: `6`

<a name="int"></a>

## <a name="int"></a>int

Az egész verzió karakterlánc visszaadása.

```
int('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | Alakítandó karakterláncot | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*egész-eredménye*> | Egész szám | A megadott karakterlánc egész verzióját | 
|||| 

*Példa* 

Ebben a példában a karakterlánc a "10" egész verziójú hoz létre:

```
int('10')
```

És az eredményt adja vissza: `10`

<a name="item"></a>

## <a name="item"></a>Elem

Ha egy ismétlődő művelet belül használt tömb, térjen vissza az aktuális elem a tömbben a művelet aktuális iteráció során. Ez az elem tulajdonságait is be az értékeket. 

```
item()
```

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*Aktuális-tömb-elem*> | Bármelyik | A művelet aktuális közelítés a tömb aktuális elemét. | 
|||| 

*Példa* 

Ez a példa lekérdezi a `body` elem az aktuális üzenet "Send_an_email" művelethez egy a-each ciklus aktuális iterációs belül:

```
item().body
```

<a name="items"></a>

## <a name="items"></a>elem

Az aktuális elem egy a-each ciklus az egyes ciklusok visszaadása. Használja ezt a funkciót az a-each ciklus található.

```
items('<loopName>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Igen | Sztring | A nevet a-each ciklus | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*Elem*> | Bármelyik | Az elem az aktuális ciklus található a megadott tartozó-each ciklus | 
|||| 

*Példa* 

Ebben a példában az aktuális elem lekérése a megadott tartozó-each ciklus:

```
items('myForEachLoopName')
```

<a name="json"></a>

## <a name="json"></a>JSON

A JavaScript Object Notation (JSON) típusú érték, vagy egy karakterlánc- vagy XML-objektumot vissza.

```
json('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Karakterlánc- vagy XML | A karakterlánc- vagy XML konvertálni | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*JSON-eredménye*> | Natív JSON-típus vagy objektum | A natív JSON értéke, és a megadott karakterlánc- vagy XML-objektumot. Ha a karakterlánc null értékű, a függvény egy üres objektumot ad vissza. | 
|||| 

*1. példa* 

Ebben a példában ez a karakterlánc konvertálja a JSON-érték:

```
json('[1, 2, 3]')
```

És az eredményt adja vissza: `[1, 2, 3]`

*2. példa*

Ebben a példában ez a karakterlánc konvertálása JSON: 

```
json('{"fullName": "Sophia Owen"}')
```

És az eredményt adja vissza:

```
{
  "fullName": "Sophia Owen"
}
```

*3. példa*

Ebben a példában az XML konvertálása JSON: 

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

És az eredményt adja vissza:

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

## <a name="intersection"></a>metszetének

Térjen vissza olyan gyűjtemény, amelyikhez *csak* a közös elemek a megadott gyűjtemények között. Az eredmény jelenik meg, hogy egy elem szerepelnie kell a függvénynek átadott összes gyűjteményt. Ha egy vagy több elem neve, az eredmény az utolsó elem ezen a néven jelenik meg.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>,... | Igen | A tömb vagy objektum, de nem mindkettőt | A gyűjtemények, ahonnan szeretné *csak* a közös elemek | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*közös-elemek*> | Tömb vagy objektum, illetve | A gyűjteményt, amely csak a közös elemek a megadott gyűjtemények között | 
|||| 

*Példa* 

Ebben a példában a közös elemek keres, ezek a tömbök közötti:  

```
intersection([1, 2, 3], [101, 2, 1, 10], [6, 8, 1, 2])
```

A tömböt ad vissza, és *csak* ezeket az elemeket: `[1, 2]`

<a name="join"></a>

## <a name="join"></a>csatlakozás

Visszatérési elválasztott karakterlánc, amely a tömb összes elemet és az egyes karakterek rendelkezik egy *elválasztó*.

```
join([<collection>], '<delimiter>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Gyűjtemény*> | Igen | Tömb | A tömb a elemekkel való csatlakozáshoz |  
| <*Elválasztó*> | Igen | Sztring | Az elválasztó, amely akkor jelenik meg a létrehozott karakterláncban szereplő karakterek közé | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*char1*><*elválasztó*><*char2*><*elválasztó*>... | Sztring | A megadott tömb elemeinek alapján létrehozott az eredményül kapott karakterláncot |
|||| 

*Példa* 

Ez a példa létrehoz egy karakterlánc a megadott karakter a tömb elemeinek elválasztóként:

```
join([a, b, c], '.')
```

És az eredményt adja vissza: `"a.b.c"`

<a name="last"></a>

## <a name="last"></a>utolsó

Egy gyűjtemény utolsó elemének visszaadása.

```
last('<collection>')
last([<collection>])
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény utolsó elemének megkeresése | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*utolsó gyűjteményelem*> | Karakterlánc vagy tömb, illetve | Az utolsó elem a gyűjteményben | 
|||| 

*Példa* 

Ezekben a példákban szereplő ezekhez a gyűjteményekhez utolsó elem megkeresése:

```
last('abcd')
last([0, 1, 2, 3])
```

És visszaadja az eredmények: 

* Első példa: `"d"`
* Második példáját: `3`

<a name="lastindexof"></a>

## <a name="lastindexof"></a>lastIndexOf

Egy substring záró pozíciójának vagy az index értéket adnak vissza. Ez a funkció nincs kis-és nagybetűket, és a 0 számmal kezdődik.

```
lastIndexOf('<text>', '<searchText>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg*> | Igen | Sztring | A karakterlánc, amely rendelkezik a karakterláncrész keresése | 
| <*keresettszöveg amelyben*> | Igen | Sztring | A karakterláncrész keresése | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*befejezési indexértéket*> | Egész szám | A megadott karakterláncrészlet záró pozíciójának vagy az index értéke. <p>Ha a karakterlánc nem található, a -1 számot adja vissza. </br>Ha a karakterlánc üres, térjen vissza a 0 számot. | 
|||| 

*Példa* 

Ebben a példában az befejezési értéket talált a "hello world" karakterlánc a "world" karakterláncrészlet:

```
lastIndexOf('hello world', 'world')
```

És az eredményt adja vissza: `10`

<a name="length"></a>

## <a name="length"></a>Hossza

Egy gyűjtemény elemek számának visszaadása.

```
length('<collection>')
length([<collection>])
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Gyűjtemény*> | Igen | Karakterlánc vagy tömb | Az elemek száma a gyűjteményben | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*hossza vagy száma*> | Egész szám | A gyűjtemény elemeinek száma | 
|||| 

*Példa*

Ezekben a példákban a gyűjteményekbe elemek száma: 

```
length('abcd')
length([0, 1, 2, 3])
```

És az eredményt adja vissza: `4`

<a name="less"></a>

## <a name="less"></a>kevesebb

Ellenőrizze, hogy van-e az első érték kisebb, mint a második érték.
Igaz értéket ad vissza, ha az első érték kisebb, vagy adja vissza, ha az első érték további hamis.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Egész szám, lebegőpontos vagy karakterlánc | Ellenőrizze, hogy kevesebb, mint az első érték a második érték | 
| <*compareTo*> | Igen | Egész szám, lebegőpontos vagy karakterlánc, illetve | Az összehasonlítás elem | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad | Logikai | Igaz értéket adnak vissza, ha az első érték kisebb, mint a második érték. Vissza a hamis értéket, ha az első érték egyenlő vagy nagyobb, mint a második érték. | 
|||| 

*Példa*

Ezek a példák ellenőrizze, hogy van-e az első érték kisebb, mint a második érték.

```
less(5, 10)
less('banana', 'apple')
```

És az eredmények visszaadása: 

* Első példa: `true`
* Második példáját: `false`

<a name="lessOrEquals"></a>

## <a name="lessorequals"></a>lessOrEquals

Ellenőrizze, hogy az első érték nagyobb, mint a második érték.
Igaz értéket ad vissza az első érték kisebb vagy egyenlő, mint amikor, vagy adja vissza, ha az első érték további hamis.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Egész szám, lebegőpontos vagy karakterlánc | Ellenőrizze, hogy kevesebb, mint az első érték vagy a második érték egyenlő | 
| <*compareTo*> | Igen | Egész szám, lebegőpontos vagy karakterlánc, illetve | Az összehasonlítás elem | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad  | Logikai | Vissza a igaz, ha az első érték nagyobb, mint a második érték. Vissza a hamis értéket, ha az első érték nagyobb, mint a második érték. |  
|||| 

*Példa*

Ezek a példák ellenőrizze, hogy az első értéke kisebb vagy egyenlő, mint a második érték.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

És az eredmények visszaadása: 

* Első példa: `true`
* Második példáját: `false`

<a name="listCallbackUrl"></a>

## <a name="listcallbackurl"></a>listCallbackUrl

A "visszahívási URL-címe", amely behívja egy eseményindító vagy a művelet vissza. A funkció csak az eseményindítók és műveletek esetében működik a **HttpWebhook** és **ApiConnectionWebhook** összekötő meg kell adnia, de nem a **manuális**,  **Ismétlődés**, **HTTP**, és **APIConnection** típusok. 

```
listCallbackUrl()
```

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*a visszahívás-URL-címe*> | Sztring | A visszahívási URL-cím egy eseményindító vagy művelet |  
|||| 

*Példa*

Ez a példa bemutatja, hogy ez a függvény visszatérési előfordulhat, hogy egy minta visszahívási URL-címe:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

## <a name="max"></a>max.

A legmagasabb érték visszaadása egy listából, vagy számokat, amelyek a határokat is beleértve, mindkét végén tömb. 

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szám1*>, <*szám2*>,... | Igen | Egész szám, lebegőpontos vagy mindkettő | A szám, amelyből el kívánja a legmagasabb érték beállítása | 
| [<*szám1*>, <*szám2*>,...] | Igen | A tömb - egész, lebegőpontos vagy mindkettő | A tömb, amelyből el kívánja a legmagasabb érték számok | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*a maximális-érték*> | Egész szám vagy lebegőpontos | A megadott tömb, vagy számokat a legmagasabb érték | 
|||| 

*Példa* 

Ezekben a példákban a legmagasabb érték lekérése a készletből a számok és a tömb:

```
max(1, 2, 3)
max([1, 2, 3])
```

És az eredményt adja vissza: `3`

<a name="min"></a>

## <a name="min"></a>perc

A számok vagy tömb a legalacsonyabb értéket adnak vissza.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szám1*>, <*szám2*>,... | Igen | Egész szám, lebegőpontos vagy mindkettő | A szám, amelyből el kívánja legkisebb készlete | 
| [<*szám1*>, <*szám2*>,...] | Igen | A tömb - egész, lebegőpontos vagy mindkettő | A tömb, amelyből el kívánja legkisebb számok | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*minimális-értéke*> | Egész szám vagy lebegőpontos | A legalacsonyabb a megadott értékét számokat, vagy a megadott tömb | 
|||| 

*Példa* 

Ezekben a példákban szereplő számok és a tömb legalacsonyabb értékének beolvasása:

```
min(1, 2, 3)
min([1, 2, 3])
```

És az eredményt adja vissza: `1`

<a name="mod"></a>

## <a name="mod"></a>MOD

Térjen vissza a többi két szám osztásával kapott. Ahhoz, hogy az egész típusú eredményként, lásd: [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*osztandó*> | Igen | Egész szám vagy lebegőpontos | A szám nullával a *osztó* | 
| <*osztó*> | Igen | Egész szám vagy lebegőpontos | A szám, amellyel a *osztandó*, de nem lehet 0. | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*nullával való Maradékos eredménye*> | Egész szám vagy lebegőpontos | A második szám szerint az első szám osztásával kapott maradékot | 
|||| 

*Példa* 

Ebben a példában az első szám, a második szám hányadosát:

```
mod(3, 2)
```

És az eredményt adja vissza: `1`

<a name="mul"></a>

## <a name="mul"></a>MUL számú

A termék visszaadásának két szám szorzásával.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*multiplicand1*> | Igen | Egész szám vagy lebegőpontos | A szám szorozza meg *multiplicand2* | 
| <*multiplicand2*> | Igen | Egész szám vagy lebegőpontos | A szám, amely több *multiplicand1* | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*a termék-eredménye*> | Egész szám vagy lebegőpontos | A terméket az első szám és a második szám szerint | 
|||| 

*Példa* 

Ezekben a példákban a második szám több első számot:

```
mul(1, 2)
mul(1.5, 2)
```

És az eredmények visszaadása:

* Első példa: `2`
* Második – példa `3`

<a name="multipartBody"></a>

## <a name="multipartbody"></a>multipartBody

A szervezet egy adott részére, amely több részből áll egy műveleti kimenet visszaadása.

```
multipartBody('<actionName>', <index>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Műveletnév*> | Igen | Sztring | A művelet, amely több alkotórészek rendelkezik kimeneti a nevét | 
| <*Index*> | Igen | Egész szám | Az értéket, amelyet a részére | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*Törzs*> | Sztring | A szervezet a megadott részére | 
|||| 

<a name="not"></a>

## <a name="not"></a>nem

Ellenőrizze, hogy egy kifejezés értéke false. Igaz értéket ad vissza, ha a kifejezés értéke false, vagy visszatérhet a hamis értéket, ha igaz.

```
not(<expression>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*kifejezés*> | Igen | Logikai | A kifejezés kereséséhez | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad | Logikai | Vissza a igaz, ha a kifejezés értéke false. Vissza a hamis értéket, ha a kifejezés értéke true. |  
|||| 

*1. példa*

Ezek a példák ellenőrzése, hogy az adott kifejezések hamis: 

```
not(false)
not(true)
```

És az eredmények visszaadása:

* Első példa: A kifejezés értéke false, így a függvény `true`.
* Második példáját: A kifejezés értéke igaz, így a függvény `false`.

*2. példa*

Ezek a példák ellenőrzése, hogy az adott kifejezések hamis: 

```
not(equals(1, 2))
not(equals(1, 1))
```

És az eredmények visszaadása:

* Első példa: A kifejezés értéke false, így a függvény `true`.
* Második példáját: A kifejezés értéke igaz, így a függvény `false`.

<a name="or"></a>

## <a name="or"></a>vagy

Ellenőrizze, hogy legalább egy kifejezés igaz-e. Igaz értéket ad vissza IGAZ esetén legalább egy kifejezést, vagy vissza hamis, ha az összes kötelezően hamis.

```
or(<expression1>, <expression2>, ...)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Kifejezés1*>, <*Kifejezés2*>,... | Igen | Logikai | A kifejezések ellenőrzése | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad | Logikai | Vissza a igaz, ha legalább egy kifejezés értéke igaz. Vissza a hamis értéket, ha az összes kifejezések szerepelnek hamis. |  
|||| 

*1. példa*

Ezekben a példákban arról, hogy legalább egy kifejezés igaz:

```
or(true, false)
or(false, false)
```

És az eredmények visszaadása:

* Első példa: legalább egy kifejezés értéke igaz, így a függvény `true`.
* Második példáját: mindkét kifejezés értéke HAMIS,, a függvény `false`.

*2. példa*

Ezekben a példákban arról, hogy legalább egy kifejezés igaz:

```
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

És az eredmények visszaadása:

* Első példa: legalább egy kifejezés értéke igaz, így a függvény `true`.
* Második példáját: mindkét kifejezés értéke HAMIS,, a függvény `false`.

<a name="parameters"></a>

## <a name="parameters"></a>paraméterek

A logic app-definíciót egy paraméter ismertetett visszaadása. 

```
parameters('<parameterName>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*parameterName*> | Igen | Sztring | A használni kívánt paraméterének neve | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*paraméter-értéke*> | Bármelyik | A megadott paraméter értéke | 
|||| 

*Példa* 

Tegyük fel, hogy rendelkezik-e a JSON-érték:

```json
{
  "fullName": "Sophia Owen"
}
```

Ez a példa lekérdezi a megadott paraméter értékét:

```
parameters('fullName')
```

És az eredményt adja vissza: `"Sophia Owen"`

<a name="rand"></a>

## <a name="rand"></a>VÉL

Egy véletlenszerű egész számot adjon vissza egy megadott címtartományból, amely csak a kezdő végén között lehet.

```
rand(<minValue>, <maxValue>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*A MinValue*> | Igen | Egész szám | A legkisebb egész szám | 
| <*MaxValue*> | Igen | Egész szám | Az egész szám, amely a legnagyobb egész szám, amely a függvénynek a következő | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*véletlenszerű eredménye*> | Egész szám | A megadott tartomány által visszaadott véletlenszerű egész szám |  
|||| 

*Példa*

Ebben a példában egy véletlenszerű egész lekérdezi a megadott tartományból, kivéve a maximális érték: 

```
rand(1, 5)
```

És az eredmény számot adja vissza: `1`, `2`, `3`, vagy `4` 

<a name="range"></a>

## <a name="range"></a>tartomány

Térjen vissza olyan egész tömb, amely elindítja a megadott egész szám.

```
range(<startIndex>, <count>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*startIndex*> | Igen | Egész szám | Az egész szám, amely indítja el a tömb első eleme | 
| <*Száma*> | Igen | Egész szám | A tömb egész számok száma | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| [<*tartomány-eredmény*>] | Tömb | Egész számok, a megadott index a tömb |  
|||| 

*Példa*

Ebben a példában hoz létre, amely a megadott index indul, és rendelkezik a megadott szám számokból álló egész tömb:

```
range(1, 4)
```

És az eredményt adja vissza: `[1, 2, 3, 4]`

<a name="replace"></a>

## <a name="replace"></a>cserélje le

Cserélje le a megadott karakterláncot egy substring, és térjen vissza az eredmény-karakterlánc. Ez a függvény megkülönbözteti a kis-és nagybetűket.

```
replace('<text>', '<oldText>', '<newText>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg*> | Igen | Sztring | A karakterlánc, amely rendelkezik a lecserélendő karakterláncrészletet | 
| <*oldText*> | Igen | Sztring | A lecserélendő karakterláncrészletet | 
| <*newText*> | Igen | Sztring | A behelyettesítendő karakterláncot | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített szöveg*> | Sztring | A substring cseréje után a frissített karakterlánc <p>Ha a substring nem található, az eredeti karakterláncot adja vissza. | 
|||| 

*Példa* 

Ebben a példában a "régi" substring "a régi karakterlánc" talál, és "régi", "új" a felváltja: 

```
replace('the old string', 'old', 'new')
```

És az eredményt adja vissza: `"the new string"`

<a name="removeProperty"></a>

## <a name="removeproperty"></a>removeProperty

Tulajdonság eltávolítása egy objektumot, és térjen vissza a frissített objektum.

```
removeProperty(<object>, '<property>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Objektum*> | Igen | Objektum | A JSON-objektumból, ahol el szeretné távolítani egy tulajdonságot | 
| <*Tulajdonság*> | Igen | Sztring | Az eltávolítandó tulajdonság neve | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített objektum*> | Objektum | A frissített JSON-objektum nélkül a megadott tulajdonság | 
|||| 

*Példa*

Ebben a példában eltávolítjuk a `"accountLocation"` tulajdonságot egy `"customerProfile"` objektum, amely alakítja át a JSON-t a [JSON()](#json) működik, és a frissített objektumot:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

## <a name="setproperty"></a>setProperty

A egy objektum tulajdonság értékét, és térjen vissza a frissített objektum. Új tulajdonság hozzáadásához használhatja ezt a funkciót, vagy a [addProperty()](#addProperty) függvény.

```
setProperty(<object>, '<property>', <value>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Objektum*> | Igen | Objektum | A JSON-objektum, amelynek tulajdonsága be szeretné állítani | 
| <*Tulajdonság*> | Igen | Sztring | A meglévő vagy új tulajdonság beállításához a nevét | 
| <*Érték*> | Igen | Bármelyik | Az érték a megadott tulajdonság beállítása |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített objektum*> | Objektum | A frissített JSON-objektum amelynek tulajdonsága | 
|||| 

*Példa*

Ez a példa beállítja a `"accountNumber"` tulajdonságának egy `"customerProfile"` objektum, amely alakítja át a JSON-t a [JSON()](#json) függvény. A függvény által létrehozott egyik érték hozzárendel [guid()](#guid) működik, és a frissített JSON-objektumot:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

## <a name="skip"></a>Kihagyása

Elem eltávolítása a gyűjtemény elejére, és térjen vissza *összes többi* elemeket.

```
skip([<collection>], <count>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Gyűjtemény*> | Igen | Tömb | A gyűjtemény el szeretné távolítani elemei | 
| <*Száma*> | Igen | Egész szám | Elöl eltávolítandó elemek számának pozitív egész szám | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| [<*frissítése gyűjtemény*>] | Tömb | A megadott elemek eltávolítása után a frissített gyűjtemény | 
|||| 

*Példa*

Ebben a példában egy elem, a szám 0, a megadott tömb első eltávolítása: 

```
skip([0, 1, 2, 3], 1)
```

Ez a fennmaradó elemekhez tömböt ad vissza, és: `[1,2,3]`

<a name="split"></a>

## <a name="split"></a>felosztás

Térjen vissza olyan tömb, amely egy karakterlánc összes karaktert tartalmaz és az egyes karakterek elválasztva egy *elválasztó*.

```
split('<text>', '<separator>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg*> | Igen | Sztring | A karakterláncot, amely a leíró karaktereket tartalmaz |  
| <*Elválasztó*> | Igen | Sztring | Az elválasztó, amely akkor jelenik meg, a karakterek eredményül kapott tömbben között | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| [<*char1*><*elválasztó*><*char2*><*elválasztó*>...] | Tömb | Az eredményül kapott tömb elemeinek a megadott karakterlánc alapján létrehozott |
|||| 

*Példa* 

Ez a példa egy tömb a megadott karakterlánc, az egyes karakterek elválasztóként vesszővel elválasztva hoz létre:

```
split('abc', ',')
```

És az eredményt adja vissza: `[a, b, c]`

<a name="startOfDay"></a>

## <a name="startofday"></a>startOfDay

Időbélyeg nap kezdetének visszaadása. 

```
startOfDay('<timestamp>', '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | A megadott időbélyeg, de a nulla órás kezdődő jelölje napjára | 
|||| 

*Példa* 

Ez a példa megkeresi az időbélyegző nap kezdete:

```
startOfDay('2018-03-15T13:30:30Z')
```

És az eredményt adja vissza: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

## <a name="startofhour"></a>startOfHour

Az időbélyeg óra kezdetének visszaadása. 

```
startOfHour('<timestamp>', '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | A megadott időbélyeg, de a nulla perces kezdődő jelölje meg az egy óra | 
|||| 

*Példa* 

Ebben a példában az órában az időbélyegző kezdetét talál:

```
startOfHour('2018-03-15T13:30:30Z')
```

És az eredményt adja vissza: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

## <a name="startofmonth"></a>startOfMonth

Az adott hónapban az időbélyeg kezdetének visszaadása. 

```
startOfMonth('<timestamp>', '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | A megadott időbélyeg, de a nulla órás be van jelölve, a hónap első napján indítása | 
|||| 

*Példa* 

Ebben a példában az időbélyegző a hónap kezdetét adja vissza:

```
startOfMonth('2018-03-15T13:30:30Z')
```

És az eredményt adja vissza: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

## <a name="startswith"></a>startswith elemnek

Ellenőrizze, hogy a karakterlánc egy adott substring indításakor. Igaz értéket ad vissza a karakterláncrészt programokat, vagy vissza hamis mikor nem található. Ez a funkció nincs kis-és nagybetűket.

```
startsWith('<text>', '<searchText>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg*> | Igen | Sztring | A karakterlánc | 
| <*keresettszöveg amelyben*> | Igen | Sztring | A kezdő karakterlánc kereséséhez | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| IGAZ vagy HAMIS eredményt ad  | Logikai | Vissza a igaz, ha az megtalálható-e a kezdődő karakterláncrészletet. Térjen vissza hamis mikor nem található. | 
|||| 

*1. példa* 

Ez a példa ellenőrzi, hogy a "hello world" karakterlánc a "hello" substring indításakor:

```
startsWith('hello world', 'hello')
```

És az eredményt adja vissza: `true`

*2. példa*

Ez a példa ellenőrzi, hogy a "hello world" karakterlánc a "hónap" substring indításakor:

```
startsWith('hello world', 'greetings')
```

És az eredményt adja vissza: `false`

<a name="string"></a>

## <a name="string"></a>sztring

Visszatérési érték karakterlánc verzióját.

```
string(<value>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Bármelyik | Az érték átalakítása | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*karakterlánc-érték*> | Sztring | A megadott érték karakterlánc verzióját | 
|||| 

*1. példa* 

Ebben a példában ez a szám karakterlánc verzióját hozza létre:

```
string(10)
```

És az eredményt adja vissza: `"10"`

*2. példa*

Ez a példa hoz létre a megadott JSON-objektum egy karakterláncot, és használja a fordított perjel karakterrel (\\) a dupla idézőjel (") helyettesítő karakterek.

```
string( { "name": "Sophie Owen" } )
```

És az eredményt adja vissza: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

## <a name="sub"></a>Sub

Vissza az eredmény az első szám a második szám különbsége.

```
sub(<minuend>, <subtrahend>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*minuend*> | Igen | Egész szám vagy lebegőpontos | A szám, amelynek be kell vonni a *subtrahend* | 
| <*subtrahend*> | Igen | Egész szám vagy lebegőpontos | A szám, a a *minuend* | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*eredménye*> | Egész szám vagy lebegőpontos | A második szám az első szám különbsége kapott eredmény | 
|||| 

*Példa* 

Ez a példa kivonja a második szám az első szám:

```
sub(10.3, .3)
```

És az eredményt adja vissza: `10`

<a name="substring"></a>

## <a name="substring"></a>Substring

Egy karakterlánc, a megadott pozíciónál, vagy az index-től kezdődő karaktert adja vissza. A szám 0 index értékek kezdődik. 

```
substring('<text>', <startIndex>, <length>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg*> | Igen | Sztring | A karakterláncot, amelynek kívánt karakterek | 
| <*startIndex*> | Igen | Egész szám | A kezdő helyzete, vagy súgóindex-értéket egy pozitív számot | 
| <*Hossza*> | Igen | Egész szám | A pozitív számú karaktert, a substring kívánt | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*Substring-eredménye*> | Sztring | A megadott számú karaktert, a forrás-karakterláncban megadott pozíciótól kezdődően a karakterláncrész | 
|||| 

*Példa* 

Ez a példa öt karakterből álló karakterláncrész a megadott karakterlánc, kezdve az 6 értéket hoz létre:

```
substring('hello world', 6, 5)
```

És az eredményt adja vissza: `"world"`

<a name="subtractFromTime"></a>

## <a name="subtractfromtime"></a>subtractFromTime

Az időbélyeg mértékegységét számos kivonandó időnél. Lásd még: [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | A karakterlánc, amely tartalmazza az időbélyeget | 
| <*időköz*> | Igen | Egész szám | A kivonandó időnél megadott idő egységek száma | 
| <*timeUnit*> | Igen | Sztring | A használandó egysége *időköz*: "Második", "Minute", "Hour", "Day", "Hetente", "Honap", "EV" | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*frissített időbélyeg*> | Sztring | A megadott számú időegységek mínusz időbélyeg | 
|||| 

*1. példa*

Ez a példa kivonja a Timestamp típusú egy nap:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day') 
```

És az eredményt adja vissza: `"2018-01-01T00:00:00:0000000Z"`

*2. példa*

Ez a példa kivonja a Timestamp típusú egy nap:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D') 
```

Ez a választható "D" formátumban eredményt adja vissza, és: `"Monday, January, 1, 2018"`

<a name="take"></a>

## <a name="take"></a>hajtsa végre a megfelelő

Az első gyűjtemény elemek visszaadása. 

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény, amelynek kívánt elemeket | 
| <*Száma*> | Igen | Egész szám | Az első kívánt elemek számának pozitív egész szám | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*részhalmaza*> vagy [<*részhalmaza*>] | Karakterlánc vagy tömb, illetve | Egy karakterlánc vagy tömb, amely rendelkezik a megadott számú elemet az eredeti gyűjtemény első forrása | 
|||| 

*Példa*

Ezekben a példákban a megadott számú elemet beszerezni ezeket a gyűjteményeket elejéhez:

```
take('abcde`, 3)
take([0, 1, 2, 3, 4], 3)
```

És az eredmények visszaadása:

* Első példa: `"abc"`
* Második példáját: `[0, 1, 2]`

<a name="ticks"></a>

## <a name="ticks"></a>órajel

Térjen vissza a `ticks` megadott időbélyeg-tulajdonság értéke. A *osztásjelek* egy 100 nanoszekundumos időszakban van.

```
ticks('<timestamp>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*időbélyeg*> | Igen | Sztring | Az időbélyeg karakterlánc | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*Ticks-száma*> | Egész szám | A megadott időbélyeg óta jelölések száma | 
|||| 

<a name="toLower"></a>

## <a name="tolower"></a>toLower

Csupa kisbetűssé formátumban adja vissza. Ha egy karaktert a karakterlánc nem rendelkezik egy kis verziójával, ez a karakter a visszaadott karakterlánc változatlan marad.

```
toLower('<text>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg*> | Igen | Sztring | A karakterlánc kis formátumban vissza | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*kisbetűk-szöveg*> | Sztring | Az eredeti karakterláncot kisbetűssé formátumban | 
|||| 

*Példa* 

Ebben a példában ez a karakterlánc csupa kisbetűssé alakítja: 

```
toLower('Hello World')
```

És az eredményt adja vissza: `"hello world"`

<a name="toUpper"></a>

## <a name="toupper"></a>toUpper

Nagybetűk formátumban adja vissza. Ha a karakterlánc egy karakter nagybetűssé verziójú nem rendelkezik, ez a karakter a visszaadott karakterlánc változatlan marad.

```
toUpper('<text>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg*> | Igen | Sztring | A karakterlánc nagybetűssé formátumban vissza | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*nagybetűk szöveg*> | Sztring | Az eredeti karakterláncot nagybetűssé formátumban | 
|||| 

*Példa* 

Ebben a példában ez a karakterlánc nagybetűssé alakítja:

```
toUpper('Hello World')
```

És az eredményt adja vissza: `"HELLO WORLD"`

<a name="trigger"></a>

## <a name="trigger"></a>Eseményindító

Más JSON név-érték párok, rendelhet egy kifejezés, amely egy eseményindító kimeneti futásidejű, illetve értékeket visszaadása. 

* A trigger ráfordítások belül ezt a funkciót a kimenetet visszaadja az előző végrehajtási. 

* Belül egy eseményindító-feltétel a függvény az aktuális végrehajtási a kimenetet visszaadja. 

Alapértelmezés szerint a függvény a teljes eseményindító objektumra hivatkozik, de nem kötelezően megadhat egy tulajdonság amelynek kívánt értéket. Emellett ez a funkció rövid szintaxist verziók rendelkezik, lásd: [triggerOutputs()](#triggerOutputs) és [triggerBody()](#triggerBody). 

```
trigger()
```

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*eseményindító-kimenet*> | Sztring | Egy eseményindító futásidőben kimenete | 
|||| 

<a name="triggerBody"></a>

## <a name="triggerbody"></a>triggerBody

Térjen vissza a trigger elem `body` kimeneti futásidőben. A rövid szintaxist `trigger().outputs.body`. Lásd: [trigger()](#trigger). 

```
triggerBody()
```

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*o-eseményindító-szervezet*> | Sztring | A `body` az eseményindító kimenetét | 
|||| 

<a name="triggerFormDataMultiValues"></a>

## <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Ettől a kulcs egy eseményindító nevét olyan tömböt adjon vissza *űrlapadat* vagy *űrlap-kódolású* kimeneti. 

```
triggerFormDataMultiValues('<key>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Kulcs*> | Igen | Sztring | A használni kívánt kulcs nevét | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| [<*tömb a kulcs értékét*>] | Tömb | Az összes, amelyek megfelelnek a megadott kulcs tömb | 
|||| 

*Példa* 

Ebben a példában a "feedUrl" kulcs értéke egy RSS eseményindító-űrlapadat vagy űrlap-kódolású kimeneti létrehoz egy tömb: 

```
triggerFormDataMultiValues('feedUrl')
```

És a tömb nem példa eredményt adja vissza: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

## <a name="triggerformdatavalue"></a>triggerFormDataValue

Egyetlen értékkel, amely megfelel a kulcs egy eseményindító nevét adja vissza *űrlapadat* vagy *űrlap-kódolású* kimeneti. Ha a függvény egynél több egyezést talál, a függvény hibát jelez.

```
triggerFormDataValue('<key>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Kulcs*> | Igen | Sztring | A használni kívánt kulcs nevét |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*kulcs-érték*> | Sztring | A megadott kulcs értéke | 
|||| 

*Példa* 

Ebben a példában a karakterlánc a "feedUrl" kulcs értéke egy RSS eseményindító-űrlapadat vagy űrlap-kódolású kimeneti hoz létre:

```
triggerFormDataValue('feedUrl')
```

És ez a karakterlánc egy példa eredményt adja vissza: `"http://feeds.reuters.com/reuters/topNews"` 

<a name="triggerMultipartBody"></a>

A szervezet egy adott részére, amely több részből áll egy eseményindító kimeneti adja vissza. 

```
triggerMultipartBody(<index>)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Index*> | Igen | Egész szám | Az értéket, amelyet a részére |
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*Törzs*> | Sztring | A szervezet a trigger elem több részből álló kimenet megadott rész | 
|||| 

<a name="triggerOutputs"></a>

## <a name="triggeroutputs"></a>triggerOutputs

Egy eseményindító kimeneti futásidejű, illetve értékeket visszaadásának más JSON-név és érték párokat. A rövid szintaxist `trigger().outputs`. Lásd: [trigger()](#trigger). 

```
triggerOutputs()
```

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*eseményindító-kimenet*> | Sztring | Egy eseményindító futásidőben kimenete  | 
|||| 

<a name="trim"></a>

## <a name="trim"></a>Trim

Kezdő és záró szóközök eltávolítása a karakterláncot, és térjen vissza a frissített karakterlánc.

```
trim('<text>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Szöveg*> | Igen | Sztring | A karakterlánc, amely rendelkezik a kezdő és záró szóközök eltávolítása | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*updatedText*> | Sztring | Frissítse az eredeti karakterlánc szerepel bevezető vagy záró szóközök nélkül | 
|||| 

*Példa* 

Ebben a példában a kezdő és záró szóközök eltávolítása a "Hello, World" karakterlánc:  

```
trim(' Hello World  ')
```

És az eredményt adja vissza: `"Hello World"`

<a name="union"></a>

## <a name="union"></a>a UNION

Térjen vissza olyan gyűjtemény, amelyikhez *összes* az elemek a megadott ügyfélgyűjteményekből. Az eredmény jelenik meg, hogy egy elemet egy gyűjteményt a függvénynek átadott is megjelennek. Ha egy vagy több elem neve, az eredmény az utolsó elem ezen a néven jelenik meg. 

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>,...  | Igen | A tömb vagy objektum, de nem mindkettőt | A gyűjtemények, ahonnan szeretné *összes* elemek | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*updatedCollection*> | Tömb vagy objektum, illetve | A gyűjtemény elemeinek megadott gyűjtemények - ismétlődő elemek | 
|||| 

*Példa* 

Ez a példa lekérdezi *összes* ezeket a gyűjteményeket a cikkeket: 

```
union([1, 2, 3], [1, 2, 10, 101])
```

És az eredményt adja vissza: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

## <a name="uricomponent"></a>uriComponent

Térjen vissza a karakterlánc egy egységes erőforrás-azonosító (URI) kódolású verziót tartományvezérlőkkel történő lecserélésével URL nem biztonságos karakterek escape-karakter. Ezzel a funkcióval helyett [encodeUriComponent()](#encodeUriComponent). Bár mindkét funkciók működik, `uriComponent()` részesíti előnyben.

```
uriComponent('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | Az URI-ként kódolt formában alakítandó karakterláncot | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*uri-kódolása*> | Sztring | Az escape-karakterekkel URI-kódolású karakterlánc | 
|||| 

*Példa*

Ebben a példában ez a karakterlánc egy URI-kódolt verziót hoz létre:

```
uriComponent('https://contoso.com')
```

És az eredményt adja vissza: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

## <a name="uricomponenttobinary"></a>uriComponentToBinary

A bináris verzió egy egységes erőforrás-azonosító (URI) összetevő visszaadása.

```
uriComponentToBinary('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | Az átalakítandó URI-kódolású karakterlánc | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*bináris-az-kódolású-uri*> | Sztring | Az URI-kódolású karakterlánc bináris verzióját. A bináris tartalma base64-kódolású és által képviselt `$content`. | 
|||| 

*Példa*

Ebben a példában az URI-kódolású karakterlánc bináris verzióját hozza létre: 

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

És az eredményt adja vissza: 

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

## <a name="uricomponenttostring"></a>uriComponentToString

Térjen vissza a karakterlánc-verziót egy egységes erőforrás-azonosító (URI) kódolású karakterlánc, hatékonyan dekódolás az URI-kódolású karakterlánc.

```
uriComponentToString('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | Az URI-kódolású karakterlánc dekódolás | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*dekódolni uri*> | Sztring | Az URI-kódolású karakterlánc dekódolt verzióját | 
|||| 

*Példa*

Ebben a példában az URI-kódolású karakterlánc dekódolt karakterlánc verzióját hozza létre: 

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

És az eredményt adja vissza: `"https://contoso.com"` 

<a name="uriHost"></a>

## <a name="urihost"></a>uriHost

Térjen vissza a `host` egységes erőforrás-azonosítója (URI) értékét.

```
uriHost('<uri>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*URI*> | Igen | Sztring | Az URI amelynek `host` kívánt érték | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*gazdagép-értékét*> | Sztring | A `host` értéket a megadott URI-hoz | 
|||| 

*Példa*

Ez a példa megkeresi a `host` ezt az URI a következő: 

```
uriHost('https://www.localhost.com:8080')
```

És az eredményt adja vissza: `"www.localhost.com"`

<a name="uriPath"></a>

## <a name="uripath"></a>uriPath

Térjen vissza a `path` egységes erőforrás-azonosítója (URI) értékét. 

```
uriPath('<uri>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*URI*> | Igen | Sztring | Az URI amelynek `path` kívánt érték | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*elérési út-értéke*> | Sztring | A `path` értéket a megadott URI-hoz. Ha `path` nem rendelkezik értékkel, a "/" karaktert adja vissza. | 
|||| 

*Példa*

Ez a példa megkeresi a `path` ezt az URI a következő: 

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És az eredményt adja vissza: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

## <a name="uripathandquery"></a>uriPathAndQuery

Térjen vissza a `path` és `query` egységes erőforrás-azonosító (URI) értékeket.

```
uriPathAndQuery('<uri>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*URI*> | Igen | Sztring | Az URI amelynek `path` és `query` kívánt értékek | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*elérési út – lekérdezés-értéke*> | Sztring | A `path` és `query` értékeket a megadott URI-hoz. Ha `path` nem adja meg egy értéket, a "/" karaktert adja vissza. | 
|||| 

*Példa*

Ez a példa megkeresi a `path` és `query` ezt az URI értékeit:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És az eredményt adja vissza: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

## <a name="uriport"></a>uriPort

Térjen vissza a `port` egységes erőforrás-azonosítója (URI) értékét.

```
uriPort('<uri>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*URI*> | Igen | Sztring | Az URI amelynek `port` kívánt érték | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*port-értéke*> | Egész szám | A `port` értéket a megadott URI-hoz. Ha `port` nem adja meg egy értéket, térjen vissza az alapértelmezett port a protokoll. | 
|||| 

*Példa*

Ez a példa adja vissza a `port` ezt az URI a következő:

```
uriPort('http://www.localhost:8080')
```

És az eredményt adja vissza: `8080`

<a name="uriQuery"></a>

## <a name="uriquery"></a>uriQuery

Térjen vissza a `query` egységes erőforrás-azonosítója (URI) értékét.

```
uriQuery('<uri>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*URI*> | Igen | Sztring | Az URI amelynek `query` kívánt érték | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*lekérdezés-érték*> | Sztring | A `query` értéket a megadott URI-hoz | 
|||| 

*Példa*

Ez a példa adja vissza a `query` ezt az URI a következő: 

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És az eredményt adja vissza: `"?date=today"`

<a name="uriScheme"></a>

## <a name="urischeme"></a>UriScheme

Térjen vissza a `scheme` egységes erőforrás-azonosítója (URI) értékét.

```
uriScheme('<uri>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*URI*> | Igen | Sztring | Az URI amelynek `scheme` kívánt érték | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*rendszer-érték*> | Sztring | A `scheme` értéket a megadott URI-hoz | 
|||| 

*Példa*

Ez a példa adja vissza a `scheme` ezt az URI a következő:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

És az eredményt adja vissza: `"http"`

<a name="utcNow"></a>

## <a name="utcnow"></a>utcNow

Az aktuális időbélyeg visszaadása. 

```
utcNow('<format>')
```

Másik lehetőségként megadhat egy másik formátummal a <*formátum*> paraméter. 


| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Formátumban*> | Nem | Sztring | Vagy egy [egyetlen formátummegadó](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy egy [egyéni formátum mintát](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az alapértelmezett az időbélyeg formátuma ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-ddT:mm:ss:fffffffK), amely megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és megőrzi az időzóna-információk. | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*aktuális-időbélyeg*> | Sztring | Az aktuális dátum és idő | 
|||| 

*1. példa*

Tegyük fel, hogy a ma jelenleg 1:00:00 PM 2018. április 15. Ez a példa lekérdezi az aktuális időbélyeg: 

```
utcNow()
```

És az eredményt adja vissza: `"2018-04-15T13:00:00.0000000Z"`

*2. példa*

Tegyük fel, hogy a ma jelenleg 1:00:00 PM 2018. április 15. Ez a példa lekérdezi az aktuális időbélyeg a választható "D" formátumot használja:

```
utcNow('D')
```

És az eredményt adja vissza: `"Sunday, April 15, 2018"`

<a name="variables"></a>

## <a name="variables"></a>változók

A megadott változó értékének visszaadása. 

```
variables('<variableName>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*variableName*> | Igen | Sztring | A használni kívánt változó nevét | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*változó értékű*> | Bármelyik | A megadott változó értékét | 
|||| 

*Példa*

Tegyük fel, hogy az aktuális "numItems" változó értéke 20. Ez a példa lekérdezi az egész értéket a változóhoz:

```
variables('numItems')
```

És az eredményt adja vissza: `20`

<a name="workflow"></a>

## <a name="workflow"></a>munkafolyamat

Térjen vissza a munkafolyamat magát a részleteit futásidőben. 

```
workflow().<property>
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Tulajdonság*> | Nem | Sztring | A munkafolyamat tulajdonság használni kívánt nevét <p>Egy munkafolyamat-objektumnak van, ezek a tulajdonságok: **neve**, **típus**, **azonosító**, **hely**, és **futtatása**. A **futtatása** tulajdonság értéke is olyan objektum, amely rendelkezik ezekkel a tulajdonságokkal: **neve**, **típus**, és **azonosító**. | 
||||| 

*Példa*

Ebben a példában a munkafolyamat aktuális futtató nevét adja vissza:

```
workflow().run.name
```

<a name="xml"></a>

## <a name="xml"></a>xml

Az XML-verziója, egy JSON-objektum tartalmazó karakterlánc visszaadása. 

```
xml('<value>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*Érték*> | Igen | Sztring | A karakterlánc alakítható át JSON-objektum <p>A JSON-objektum csak egyetlen legfelső szintű tulajdonsággal kell rendelkeznie. <br>Használja a fordított perjel karaktert (\\) a dupla idézőjel (") helyettesítő karakterek. | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*XML-verzió*> | Objektum | A megadott karakterlánc-vagy JSON-objektum kódolt XML | 
|||| 

*1. példa*

Ebben a példában ez a karakterlánc, amely tartalmaz egy JSON-objektum XML verzióját hozza létre: 

`xml( '{ \"name\": \"Sophia Owen\" }' )`

És visszaadja az eredményt XML: 

```xml
<name>Sophia Owen</name>
```

*2. példa*

Tegyük fel, a JSON-objektum:

```json
{ 
  "person": { 
    "name": "Sophia Owen", 
    "city": "Seattle" 
  } 
}
```

Ebben a példában a karakterlánc, amely tartalmazza a JSON-objektum hoz létre XML:

`xml( '{ \"person\": { \"name\": \"Sophia Owen\", \"city\": \"Seattle\" } }' )`

És visszaadja az eredményt XML: 

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

## <a name="xpath"></a>XPath

Ellenőrizze az XML-kódot a csomópontok vagy egyező (XML Path Language) XPath kifejezés, és térjen vissza a megfelelő csomópontok vagy értékeket. Az XPath-kifejezés, vagy csak "XPath," segít, hogy a csomópontok vagy számítási értékek választhatja az XML-tartalom, keresse meg az XML-dokumentum struktúra.

```
xpath('<xml>', '<xpath>')
```

| Paraméter | Szükséges | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*XML*> | Igen | Bármelyik | Az XML-karakterlánc csomópontok vagy az XPath kifejezés értékével egyező keresése | 
| <*XPath*> | Igen | Bármelyik | Az XPath-kifejezés található a megfelelő XML-csomópontnak vagy értékek használt | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*XML-csomópont*> | XML | Az XML-csomópontot csak egyetlen csomópont megegyezik a megadott XPath-kifejezés | 
| <*Érték*> | Bármelyik | Az érték egy XML-csomópont csak egyetlen érték megegyezik a megadott XPath-kifejezés | 
| [<*xml-1. csomópont*>, <*xml-csomópont2*>,...] </br>– vagy – </br>[<*érték1*>, <*érték2*>,...] | Tömb | Az XML-csomópontnak vagy értékek, amelyek megfelelnek a megadott XPath kifejezésnek tömb | 
|||| 

*1. példa*

Ebben a példában a megfelelő csomópontok megkeresi a `<name></name>` csomópont található a megadott argumentumok, és ezen csomópont értékekkel tömböt ad vissza: 

`xpath(xml(parameters('items')), '/produce/item/name')`

Az argumentumok a következők:

* A "elem" karakterlánc, amely tartalmazza az XML-kód:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  A példában a [parameters()](#parameters) az XML-karakterlánc beolvasása "elem" argumentum a funkciót, de kell is konvertálása a karakterlánc XML formátum használatával az [xml()](#xml) függvény. 

* Az XPath kifejezés, amelyet karakterláncként:

  `"/produce/item/name"`

Ez a csomópontokat, amelyek megfelelnek az eredmény tömb `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*2. példa*

1. példa az alábbi, ebben a példában megkeresi a csomópontokat, amelyek megfelelnek a `<count></count>` csomópont, és hozzáadja azokat csomópont értékeket a `sum()` függvény:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

És az eredményt adja vissza: `30`

*3. példa*

Ehhez a példához mindkét kifejezés található a megfelelő csomópontok a `<location></location>` csomópont, a megadott argumentumok, többek között XML névtérrel. A kifejezések használata perjel (\\) a dupla idézőjel (") helyettesítő karakterek.

* *1 kifejezés*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Kifejezés 2* 

  `xpath(xml(body('Http')), '/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]')`

Az argumentumok a következők:

* Az XML, amely tartalmazza az XML-dokumentum névtér, `xmlns="http://contoso.com"`: 

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Vagy XPath kifejezés itt:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]`

Ez a megfelelő eredmény csomópont a `<location></location` csomópont:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*4. példa*

Ebben a példában a következő példa 3, megkeresi az értéket a `<location></location>` csomópont: 

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

És az eredményt adja vissza: `"Paris"`

## <a name="next-steps"></a>További lépések

További tudnivalók a [Munkafolyamatdefiníciós nyelve](../logic-apps/logic-apps-workflow-definition-language.md)
