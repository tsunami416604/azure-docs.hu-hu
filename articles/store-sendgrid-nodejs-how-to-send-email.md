---
title: A SendGrid e-mail szolgáltatás (Node.js) használata | Microsoft dokumentumok
description: Ismerje meg, hogyan küldhet e-mailt az Azure SendGrid e-mail szolgáltatásával. A Node.js API használatával írt kódminták.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60931704"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Hogyan küldjön e-mailt a SendGrid használatával a Node.js fájlból

Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat az Azure-beli SendGrid e-mail szolgáltatással. A minták a Node.js API használatával íródott. A tárgyalt forgatókönyvek közé tartozik **az e-mailek összeállítása**, **az e-mailek küldése**, a **mellékletek hozzáadása**, szűrők **használata**és a **tulajdonságok frissítése**. A SendGridről és az e-mailek küldéséről a [Következő lépések](#next-steps) szakaszban talál további információt.

## <a name="what-is-the-sendgrid-email-service"></a>Mi a SendGrid e-mail szolgáltatás?

A SendGrid egy [felhőalapú e-mail szolgáltatás,] amely megbízható [tranzakciós e-mail kézbesítést,]méretezhetőséget és valós idejű elemzést, valamint rugalmas API-kat biztosít, amelyek megkönnyítik az egyéni integrációt. A SendGrid gyakori használati forgatókönyvei a következők:

* Nyugták automatikus küldése a vevőknek
* Terjesztési listák felügyelete az ügyfelek havi e-szórólapok és különleges ajánlatok küldéséhez
* Valós idejű mérőszámok gyűjtése például a blokkolt e-mailekhez és az ügyfelek válaszkészségéhez
* Jelentések létrehozása a trendek azonosításához
* Vevői lekérdezések továbbítása
* Értesítések küldése az alkalmazásból

További információ: [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>SendGrid-fiók létrehozása

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Hivatkozás a SendGrid-csomópont.js modulra

A Node.js SendGrid modulja a csomópontcsomag-kezelőn (npm) keresztül telepíthető a következő paranccsal:

```bash
npm install sendgrid
```

A telepítés után a következő kód dal követelheti meg a modult az alkalmazásban:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

A SendGrid modul exportálja a **SendGrid** és **az E-mail** függvényeket.
**A SendGrid** felelős az e-mailek webes API-n keresztültörténő küldéséért, míg **az E-mail** egy e-mail üzenetet foglal magában.

## <a name="how-to-create-an-email"></a>Hogyan: E-mail létrehozása

Ha e-mailt hoz létre a SendGrid modullal, akkor először létre kell hoznunk egy e-mailt az E-mail funkcióval, majd el kell küldeni a SendGrid függvénnyel. Az alábbi példa egy új üzenet létrehozását az E-mail funkcióval:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

A html tulajdonság beállításával HTML-üzenetet is megadhat az azt támogató ügyfelek számára. Példa:

```javascript
html: This is a sample <b>HTML<b> email message.
```

A szöveg- és html-tulajdonságok beállítása kecses tartalékot biztosít a szöveges tartalomhoz azon ügyfelek számára, amelyek nem támogatják a HTML-üzeneteket.

Az E-mail funkció által támogatott összes tulajdonságról további információt a [sendgrid-nodejs című témakörben talál.][sendgrid-nodejs]

## <a name="how-to-send-an-email"></a>Hogyan: E-mail küldése

Miután e-mailt hozott létre az E-mail funkcióval, elküldheti azt a SendGrid által biztosított webes API-val. 

### <a name="web-api"></a>Webes API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Míg a fenti példák azt mutatják, halad egy e-mail objektum és visszahívási funkció, akkor is közvetlenül meghívja a küldési funkciót közvetlenül megadva e-mail tulajdonságait. Példa:  
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

## <a name="how-to-add-an-attachment"></a>Útmutató: Melléklet hozzáadása
Az üzenetekhez mellékleteket adhat hozzá a fájlnév(ek) és elérési út(ok) megadásával a **fájlok** tulajdonságában. A következő példa a melléklet elküldését mutatja be:

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
> A **fájlok** tulajdonság használatakor a fájlnak elérhetőnek kell lennie az [fs.readFile fájlon](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile)keresztül. Ha a csatolni kívánt fájl az Azure Storage-ban van, például egy Blob-tárolóban, először át kell másolnia a fájlt a helyi tárolóba vagy egy Azure-meghajtóra, mielőtt mellékletként elküldhető a **fájlok** tulajdonság használatával.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Útmutató: Szűrők használata az élőlábak és a követés engedélyezéséhez

A SendGrid további e-mail funkciókat biztosít szűrők használatával. Ezek olyan beállítások, amelyek hozzáadhatók egy e-mail üzenethez, hogy lehetővé tegyék bizonyos funkciókat, például a kattintáskövetés engedélyezését, a Google Analytics szolgáltatást, az előfizetések nyomon követését és így tovább. A szűrők teljes listáját a [Szűrőbeállítások (Szűrő beállításai) menülapban találja.][Filter Settings]

A szűrők a **szűrők** tulajdonsághasználatával alkalmazhatók egy üzenetre.
Minden szűrőt egy szűrőspecifikus beállításokat tartalmazó kivonat határoz meg.
A következő példák bemutatják az élőláb- és kattintáskövetési szűrőket:

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

### <a name="click-tracking"></a>Kattintson a Követés gombra

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

## <a name="how-to-update-email-properties"></a>Útmutató: E-mail tulajdonságainak frissítése

Egyes e-mail tulajdonságok felülírhatók **a setProperty** használatával, vagy hozzáfűzhetők **az addProperty**használatával. Hozzáadhat például további címzetteket a

```javascript
email.addTo('jeff@contoso.com');
```

vagy állítson be egy szűrőt a

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

További információ: [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Útmutató: További SendGrid-szolgáltatások használata

A SendGrid webalapú API-kat kínál, amelyeket az Azure-alkalmazás további SendGrid-funkcióinak kihasználására használhat. További részleteket a [SendGrid API dokumentációjában][SendGrid API documentation]talál.

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta a SendGrid E-mail szolgáltatás alapjait, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg.

* SendGrid Node.js modultár: [sendgrid-nodejs][sendgrid-nodejs]
* SendGrid API dokumentáció:<https://sendgrid.com/docs>
* SendGrid különleges ajánlat az Azure-ügyfelek számára:[http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[felhőalapú e-mail szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mail kézbesítés]: https://sendgrid.com/transactional-email
