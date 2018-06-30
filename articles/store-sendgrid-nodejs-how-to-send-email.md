---
title: A SendGrid e-mail szolgáltatás (Node.js) használatával |} Microsoft Docs
description: Megtudhatja, hogyan küldjön e-maileket a SendGrid e-mail szolgáltatás az Azure-on. A Kódminták Node.js API használatával.
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
ms.openlocfilehash: 327cea3a24cc47a9cc463b37cc2346ebc475ef7f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "23873988"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Hogyan küldhet e-mailek SendGrid Node.js-ből
Ez az útmutató bemutatja, hogyan Azure a SendGrid e-mail szolgáltatás közös programozási feladatok elvégzéséhez. A minták íródtak, a Node.js API használatával. Az ismertetett forgatókönyvek **hozhat létre, e-mailek**, **e-mailek küldéséhez**, **mellékletek hozzáadása**, **szűrőkkel**, és **tulajdonságainak frissítése**. A SendGrid és e-mailek küldéséhez további információkért lásd: a [lépések](#next-steps) szakasz.

## <a name="what-is-the-sendgrid-email-service"></a>Mi az a SendGrid E-mail szolgáltatás?
SendGrid van egy [felhőalapú szolgáltatás] biztosít megbízható [tranzakciós e-mailben kézbesítésre], a méretezhetőség és a valós idejű elemzési rugalmas API-k, amelyek egyéni integrációs könnyedén együtt. Gyakori SendGrid használati forgatókönyvek a következők:

* Automatikusan az ügyfél számára a visszaigazolások küldésére
* Az ügyfelek havi e-szórólapok és a különleges ajánlatokkal terjesztési felügyelete listája
* Többek között a blokkolt e-mail, és az ügyfél reakcióidőt valós idejű metrikáját gyűjtése
* Jelentések segítségével azonosíthatja a trendeket létrehozása
* Ügyfél-lekérdezések továbbítása
* E-mail értesítések az alkalmazásról

Tovább információ: [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>A SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>A SendGrid Node.js modul hivatkozik
A SendGrid modul, a Node.js a csomópont package manager (npm) keresztül telepíthető a következő paranccsal:

    npm install sendgrid

A telepítés után is megkövetelhető a modul az alkalmazásban az alábbi kód használatával:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

A SendGrid modul exportálja a **SendGrid** és **E-mail** funkciók.
**SendGrid** felelős a webes API-n keresztül e-mail elküldése közben **E-mail** foglalja az e-mailt.

## <a name="how-to-create-an-email"></a>Hogyan: hozzon létre egy e-mailt
A SendGrid-modullal e-mailt létrehozása magában foglalja először e-mailt az e-mailek funkcióval, és majd küldje el azt a SendGrid függvény használatával. A következő egy példa az e-mailek funkcióval egy új üzenet létrehozása:

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Egy HTML-üzenetet is megadhat a html-tulajdonság beállításával támogató ügyfelek. Példa:

    html: This is a sample <b>HTML<b> email message.

A szöveg és a html-tulajdonságok beállítása sikeres-e tartalékként történő szöveges tartalom biztosít az ügyfelek, amelyek nem támogatják a HTML-üzenetek.

Az e-mailek függvény által támogatott összes tulajdonság további információkért lásd: [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Útmutató: az e-mailek küldése
Miután létrehozta az e-mailek funkcióval e-mailt, elküldheti azt a SendGrid által biztosított webes API használatával. 

### <a name="web-api"></a>Webes API
    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [!NOTE]
> A fenti példák azt szemléltetik sikeres egy e-mailek objektum és a visszahívás függvényben, amíg e-mailes tulajdonságai közvetlenül megadásával közvetlenül is hívhat meg a Küldés függvény. Példa:  
> 
> `````
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> `````
> 
> 

## <a name="how-to-add-an-attachment"></a>Hogyan: hozzá mellékletet
Mellékletek lehet hozzáadni egy üzenetet a fájl neve és elérési útjait a megadásával a **fájlok** tulajdonság. A következő példa bemutatja a melléklet küldése:

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

> [!NOTE]
> Használatakor a **fájlok** tulajdonság, a fájl elérhetőnek kell lennie keresztül [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Ha a fájlt csatolni szeretné az Azure Storage, például egy Blob tároló kell másolnia a fájlt helyi tárolóhoz vagy egy Azure meghajtó egy mellékletet használatával küldése előtt a **fájlok** tulajdonság.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>How to: Enable láblécek és követési szűrők használata
SendGrid szűrők segítségével további e-mail-funkciókat biztosítja. Ezek a beállítások, amelyek e-mailt ahhoz, hogy bizonyos funkciók, például követési kattintson, Google analytics, követési előfizetés, és így tovább is hozzáadhatók. Szűrők teljes listáját lásd: [szűrőbeállítások][Filter Settings].

Szűrők használatával is alkalmazható egy üzenetet a **szűrők** tulajdonság.
Minden egyes szűrő szűrő-specifikus beállításokat tartalmazó kivonat szerint van megadva.
Az alábbi példák bemutatják, a láblécben, majd kattintson a szűrő nyomon követése:

### <a name="footer"></a>Lábléc
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

### <a name="click-tracking"></a>Kattintson a nyomon követése
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

## <a name="how-to-update-email-properties"></a>Útmutató: E-mail tulajdonságainak frissítése
Néhány e-mail tulajdonság használatával felülírható **beállítása * tulajdonság***, illetve használatával **hozzáadása*tulajdonság x. Például hozzáadhat további címzetteket használatával

    email.addTo('jeff@contoso.com');

vagy állítsa be a szűrő használatával

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

További információkért lásd: [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Útmutató: további SendGrid szolgáltatásokkal
SendGrid webes API-k segítségével kihasználhatja az Azure alkalmazásról további SendGrid funkciókat kínál. Teljes részletekért lásd: a [SendGrid API-JÁNAK dokumentációja][SendGrid API documentation].

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a SendGrid E-mail szolgáltatás alapjait, az alábbi hivatkozásokból további.

* SendGrid Node.js modul tárház: [sendgrid-nodejs][sendgrid-nodejs]
* SendGrid API-dokumentáció: <https://sendgrid.com/docs>
* SendGrid a különleges ajánlat Azure ügyfelek esetén: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[felhőalapú szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mailben kézbesítésre]: https://sendgrid.com/transactional-email
