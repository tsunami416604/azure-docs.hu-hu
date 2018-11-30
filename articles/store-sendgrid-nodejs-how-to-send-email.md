---
title: A SendGrid e-mail szolgáltatás (Node.js) használata |} A Microsoft Docs
description: Ismerje meg, hogyan e-mail küldése a SendGrid e-mail szolgáltatás az Azure-ban. A Kódminták írt, a Node.js API-val.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52421283"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Hogyan lehet E-mail küldése a SendGrid, a Node.js használatával

Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat, a SendGrid e-mail szolgáltatással az Azure-ban. A minták írták, a Node.js API-val. Az ismertetett forgatókönyvek között megtalálható **hozhat létre, amely e-mailek**, **e-mail-küldési**, **mellékletek hozzáadása**, **ezekkel a szűrőkkel**, és **tulajdonságainak frissítése**. A SendGrid és az e-mailt küld a további információkért lásd: a [lépések](#next-steps) szakaszban.

## <a name="what-is-the-sendgrid-email-service"></a>Mi az a SendGrid E-mail szolgáltatással?

A SendGrid van egy [e-mail felhő alapú szolgáltatás] , amely megbízható biztosít [tranzakciós e-mail kézbesítési], a méretezhetőség és a valós idejű analitikát és rugalmas API-kat, amelyek elérhetőbbé teszik egyéni integrációs egyszerű. A SendGrid gyakori használati forgatókönyvek a következők:

* Automatikusan adatokat küldjenek a visszaigazolások az ügyfelek számára
* Ügyfelek küldéséhez a havi e-közleményekben és ajánlatok küldésére terjesztési felügyelete listája
* Valós idejű metrikák, például a blokkolt e-mail és a vásárlói válaszkészségének gyűjtése
* Azonosíthatja a trendeket,-jelentések létrehozása
* Továbbítás vásárlói kapcsolatos kérdésekben
* E-mail-értesítések az alkalmazásból

Tovább információ: [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>A SendGrid-fiók létrehozása

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>A SendGrid Node.js modult hivatkozhat.

A SendGrid-modul a node.js-ben a node package Managerrel (npm) keresztül telepíthető a következő paranccsal:

```bash
npm install sendgrid
```

A telepítés után is szüksége van a modul az alkalmazás a következő kód használatával:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

A SendGrid modul exportálja a **SendGrid** és **E-mail** funkciók.
**A SendGrid** feladata a webes API-val, e-mail-küldési közben **E-mail** magában foglalja egy e-mail-üzenetet.

## <a name="how-to-create-an-email"></a>How to: hozzon létre egy e-mailt

A SendGrid modult használó e-mailbe létrehozása magában foglalja a először létrehoz egy e-mail-üzenetet az E-mail funkció segítségével, és elküldi azt a SendGrid függvény használatával. Az alábbiakban látható egy példa az e-mailek függvény használatával egy új üzenet létrehozását:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Megadhat egy HTML-üzenet is html-tulajdonság beállításával támogató ügyfelek. Példa:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Ügyfelek, amelyek nem támogatják a HTML-üzenetek a szöveg és a html-tulajdonságok beállítása sikeres-e tartalékként történő szöveges tartalom biztosít.

További információ az E-mail funkció által támogatott összes tulajdonság: [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Útmutató: E-mail küldése

Miután létrehozott egy e-mail-üzenetet az E-mail funkció segítségével, elküldheti azt a SendGrid által biztosított webes API-val. 

### <a name="web-api"></a>Webes API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> A fenti példákban egy e-mailt az objektum és a visszahívási függvény megadásának jelennek meg, amíg közvetlenül is hívhat a küldési függvényt közvetlenül megadja az e-mail-tulajdonságok. Példa:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Útmutató: a melléklet hozzáadása
Mellékletek lehet hozzáadni egy üzenetet a fájl neve és elérési úton található megadásával a **fájlok** tulajdonság. A következő példa bemutatja a melléklet küldése:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> Használatakor a **fájlok** tulajdonság, a fájlnak kell lennie keresztül elérhető [fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Ha csatolni kívánt fájlt, például egy Blob-tároló Azure Storage-ban üzemeltetett kell másolnia a fájlt helyi tárolóba vagy egy Azure-meghajtó előtt, egy melléklet használatával küldhető a **fájlok** tulajdonság.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>How to: Enable láblécek és követési szűrők használata

A SendGrid szűrők használatával e-mail további funkciókat biztosít. Ezek a beállítások, amelyek e-mailbe ahhoz, hogy bizonyos funkciók, például engedélyezheti a kampányban, a Google analytics, előfizetés nyomon követése és így tovább lehet hozzáadni. Szűrők teljes listáját lásd: [szűrőbeállítások][Filter Settings].

Szűrők használatával is alkalmazható egy üzenetet a **szűrők** tulajdonság.
Minden szűrő egy szűrő-specifikus beállításokat tartalmazó kivonat szerint van megadva.
Az alábbi példák bemutatják, az élőláb, majd kattintson a szűrők követés:

### <a name="footer"></a>Lábléc

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Kattintson a nyomon követés

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>How to: e-mailek tulajdonságainak frissítése

Néhány e-mail-tulajdonságok használatával lehet felülírni **setProperty** , illetve használatával **addProperty**. Például hozzáadhat további címzettek használatával

```javascript
email.addTo('jeff@contoso.com');
```

vagy állítsa be a szűrő használatával

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

További információkért lásd: [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>How to: további SendGrid-szolgáltatások használata

A SendGrid kínál a webes API-kat használhatja az Azure-alkalmazásból további SendGrid funkciói kihasználhatók. További részletek: a [SendGrid API-dokumentáció][SendGrid API documentation].

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az alapokat, a SendGrid E-mail szolgáltatás, kövesse az alábbi hivatkozások további.

* A SendGrid Node.js modult tárházat: [sendgrid-nodejs][sendgrid-nodejs]
* A SendGrid API-dokumentáció: <https://sendgrid.com/docs>
* A SendGrid a különleges ajánlat Azure-ügyfelek számára: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[e-mail felhő alapú szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mail kézbesítési]: https://sendgrid.com/transactional-email
