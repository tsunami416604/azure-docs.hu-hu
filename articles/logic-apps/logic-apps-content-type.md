---
title: "Tartalomtípus - Azure Logic Apps alkalmazásokat kezeléséhez |} Microsoft Docs"
description: "Hogy az Azure Logic Apps hogyan kezelje a tervezési és futásidejű tartalomtípusok"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: ac67838344bbd10384299c086ff096fbe5dec6a9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="handle-content-types-in-logic-apps"></a>A logic apps leíró tartalomtípusok

Számos különböző típusú tartalmat is áramlása logikai alkalmazás, beleértve a JSON-NÁ, XML, egyszerű fájlok és a bináris adatok. Míg a Logic Apps-motor minden tartalomtípusok támogatja, néhány vannak natív módon képes volt értelmezni a Logic Apps motor. Előfordulhat, hogy másokat, konvertálási vagy átalakítás esetén szükség szerint. Ez a cikk ismerteti, hogyan kezeli a motor a különböző típusú tartalmakat és annak megfelelően kezeli az ilyen jellegű, amikor erre szükség van.

## <a name="content-type-header"></a>Content-Type fejléc

Alapvetően elindításához vizsgáljuk meg a két `Content-Types` , amelyek nem igényelnek konverzió vagy a logikai alkalmazás használható adattípusokról: `application/json` és `text/plain`.

## <a name="applicationjson"></a>Az Application/JSON

A munkafolyamat-motor támaszkodik a `Content-Type` HTTP fejléc meghívja a megfelelő kezelési meghatározásához. A kérelem tartalomtípus `application/json` tárolja, és JSON-objektumként kezeli. Alapértelmezés szerint akkor értelmezhető JSON-tartalmak anélkül, hogy bármely adattípusokról. 

Például tudta elemezni a kérelmeket, amelyek a content-type fejléc `application/json ` egy munkafolyamatban például a kifejezés használatával `@body('myAction')['foo'][0]` a érték `bar` ebben az esetben:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

Nincsenek további adattípusokról van szükség. Ha JSON, de nem volt a megadott fejléc adatokkal dolgozik, akkor is manuális típussá JSON használata a `@json()` működnek, például: `@json(triggerBody())['foo']`.

### <a name="schema-and-schema-generator"></a>Séma és a séma-készítő

A kérelem eseményindító lehetővé teszi, hogy meg kell adnia egy JSON-séma az fogadni várt tartalom. A séma lehetővé teszi, hogy a Tervező generate jogkivonatokat, a kérelem tartalmának felhasználhat. Ha készen áll a séma nem rendelkezik, válassza ki a **séma létrehozásához használja a minta hasznos**, így a JSON-séma generálása a minta hasznos adatok között.

![Séma](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>A művelet "Elemzése JSON"

A `Parse JSON` művelet lehetővé teszi a JSON-tartalom elemzése a logic app felhasználásához rövid jogkivonatokba. A kérelem eseményindító hasonló, ez a művelet lehetővé teszi adja meg vagy hozható létre a JSON-séma az elemezni kívánt tartalom. Ez az eszköz fogyasztó adatokat a Service Bus-, Azure Cosmos DB, és így tovább, sokkal egyszerűbbé teszi.

![JSON elemzése](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>egyszerű szöveg

Hasonló `application/json`, érkezett a HTTP-üzenetek a `Content-Type` fejlécének `text/plain` nyers formátumban tárolódnak. Is, ha azokat az üzeneteket a későbbi műveletek nélkül adattípusokról szerepelnek, ezeket a kérelmeket, lépjen a `Content-Type`: `text/plain` fejléc. Például az egyszerű használatakor kaphat a HTTP tartalom másként `text/plain`:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

Ha a következő művelet küldi el a kérelmet egy másik kérelem törzsében (`@body('flatfile')`), a kérelmet egy `text/plain` Content-Type fejléc. Egyszerű szöveges, de nem volt a megadott fejléc adatokkal dolgozik, ha manuálisan is konvertálni szöveg használatával az adatok a `@string()` működnek, például: `@string(triggerBody())`.

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml és Application/octet-stream és konverter funkciók

A Logic Apps motor mindig megőrzi a `Content-Type` , amelyek a HTTP-kérés vagy válasz érkezett. Igen, ha a motor megkapja a tartalmat a `Content-Type` a `application/octet-stream`, és megadja, hogy tartalom nélkül adattípusokról későbbi művelettel, a kimenő kérelem rendelkezik `Content-Type`: `application/octet-stream`. Ezzel a módszerrel a motor is garantálja az adatok nem vesznek el a munkafolyamaton keresztül áthelyezés közben. Azonban a műveletállapot (bemenetekhez és kimenetekhez) van tárolva egy JSON-objektum állapotát a munkafolyamaton keresztül helyezi át. Így egyes adattípusok megőrzéséhez a motor konvertálja a tartalmat egy bináris a base64 kódolású karakterlánc, amely megőrzi az mindkét megfelelő metaadatok `$content` és `$content-type`, amelyeket automatikusan alakítható. 

* `@json()`-adatok kerül`application/json`
* `@xml()`-adatok kerül`application/xml`
* `@binary()`-adatok kerül`application/octet-stream`
* `@string()`-adatok kerül`text/plain`
* `@base64()`-tartalom konvertálja a Base64 kódolású karakterlánc
* `@base64toString()`-a base64 kódolású karakterlánc konvertálása`text/plain`
* `@base64toBinary()`-a base64 kódolású karakterlánc konvertálása`application/octet-stream`
* `@encodeDataUri()`-dataUri bájt tömbként karakterlánc kódolja
* `@decodeDataUri()`-a dataUri visszafejti azokat egy bájttömbben.

Például, ha HTTP kérést fogadott `Content-Type`: `application/xml`:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Nem sikerült konvertálni és későbbi használatra a következőhöz hasonlóan `@xml(triggerBody())`, vagy hasonló függvények `@xpath(xml(triggerBody()), '/CustomerName')`.

## <a name="other-content-types"></a>Egyéb tartalomtípusok

Egyéb tartalomtípusok támogatott és a logic apps működni, de előfordulhat, hogy manuálisan beolvasása az üzenettörzs dekódolás által a `$content`. Tegyük fel például, hogy indít el egy `application/x-www-url-formencoded` where kérelem `$content` kódolt Base64 kódolású karakterlánc összes adatok megőrzése érdekében a tartalom:

```
CustomerName=Frank&Address=123+Avenue
```

A kérelem nem egyszerű szöveges vagy JSON-NÁ, mert a kérelem tárolódik a műveletet az alábbiak szerint:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Jelenleg nem áll rendelkezésre az űrlap adatait egy natív függvény, így továbbra is használhatja ezeket az adatokat a munkafolyamat manuális elérésével, például az adatokat egy olyan függvényt a `@string(body('formdataAction'))`. Ha a kimenő kérelem rendelkeznie kell a `application/x-www-url-formencoded` content-type fejléc, hozzáadhatja a kérelem a műveleti szövegtörzs nélkül bármely adattípusokról például `@body('formdataAction')`. Azonban ezt a módszert csak akkor működik, ha a szervezet az az egyetlen paraméter a `body` bemeneti. Ha próbálja használni a `@body('formdataAction')` a egy `application/json` kérte, futásidejű hiba beolvasni, mert a kódolt body zajlik.

