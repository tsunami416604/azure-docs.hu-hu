---
title: A Twilio használata hang-, VoIP- és SMS-üzenetekhez az Azure-ban
description: Ismerje meg, hogyan kezdeményezhetsz telefonhívást, és hogyan küldhet SMS-üzenetet az Azure-beli Twilio API-szolgáltatással. Node.js nyelven írt kódminták.
services: ''
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 164bedffcf9a1aca9f1fa46dea254fb928abcf04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637268"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>A Twilio használata hang-, VoIP- és SMS-üzenetekhez az Azure-ban
Ez az útmutató bemutatja, hogyan hozhat létre olyan alkalmazásokat, amelyek kommunikálnak a Twilio és a node.js az Azure-ban.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Mi az a Twilio?
A Twilio egy API-platform, amely megkönnyíti a fejlesztők számára telefonhívások kezdeményezését és fogadását, szöveges üzenetek küldését és fogadását, valamint a VoIP-hívások beágyazását a böngészőalapú és natív mobilalkalmazásokba. Vegyük át röviden, hogy működik ez, mielőtt belemerülünk.

### <a name="receiving-calls-and-text-messages"></a>Hívások és szöveges üzenetek fogadása
A Twilio lehetővé teszi a fejlesztők számára, hogy [programozható telefonszámokat vásároljanak,][purchase_phone] amelyek hívások és szöveges üzenetek küldésére és fogadására is használhatók. Amikor egy Twilio-szám bejövő hívást vagy szöveget kap, a Twilio http-post vagy GET kérést küld a webalkalmazásnak, és útmutatást kér a hívás vagy a szöveg kezeléséhez. A kiszolgáló válaszol a Twilio HTTP-kérést [TwiML,][twiml]egy egyszerű XML-címkék, amelyek utasításokat tartalmaznak, hogyan kell kezelni a hívást vagy szöveget. Egy pillanat alatt látni fogjuk a TwiML példáit.

### <a name="making-calls-and-sending-text-messages"></a>Hívások kezdeményezése és szöveges üzenetek küldése
Http-kérelmek a Twilio webszolgáltatás API-t, a fejlesztők küldhetnek szöveges üzeneteket, vagy kezdeményezzen kimenő telefonhívásokat. Kimenő hívások esetén a fejlesztőnek meg kell adnia egy URL-címet is, amely a twiML-utasításokat adja vissza a kimenő hívás kezeléséhez, miután csatlakoztatta.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>VoIP-képességek beágyazása ui kódba (JavaScript, iOS vagy Android)
A Twilio egy ügyféloldali SDK-t biztosít, amely bármely asztali webböngészőt, iOS-alkalmazást vagy Android-alkalmazást VoIP-telefonlá alakíthat. Ebben a cikkben a VoIP hívás böngészőben való használatára összpontosítunk. A böngészőben futó *Twilio JavaScript SDK* mellett egy kiszolgálóoldali alkalmazást (a node.js alkalmazást) kell használni egy "képességtoken" kiadásához a JavaScript-ügyfél számára. A VoIP és a node.js használatával kapcsolatban többet is elolvashat [a Twilio fejlesztői blogon.][voipnode]

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Regisztráció a Twilio szolgáltatásra (Microsoft kedvezmény)
A Twilio-szolgáltatások használata előtt először regisztrálnia kell [egy fiókot.][signup] A Microsoft Azure-ügyfelek különleges kedvezményben részesülnek – [itt mindenképpen iratkozzon fel!][signup]

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Node.js Azure-webhely létrehozása és üzembe helyezése
Ezután létre kell hoznia egy azure-beli node.js webhelyet. [Ennek hivatalos dokumentációja itt található.][azure_new_site] Magas szinten a következőket fogja tenni:

* Feliratkozás Azure-fiókra, ha még nem rendelkezik ilyen
* Új webhely létrehozása az Azure felügyeleti konzolon
* Forrásvezérlés támogatás hozzáadása (feltételezzük, hogy git-et használt)
* Fájl `server.js` létrehozása egyszerű node.js webalkalmazással
* Ennek az egyszerű alkalmazásnak az Üzembe helyezése az Azure-ba

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>A Twilio-modul konfigurálása
Ezután elkezdjük írni egy egyszerű node.js alkalmazást, amely a Twilio API-t használja. Mielőtt elkezdené, konfigurálnunk kell a Twilio-fiók hitelesítő adatait.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Twilio-hitelesítő adatok konfigurálása a rendszerkörnyezeti változókban
Annak érdekében, hogy a Twilio háttérrendszer ével szemben hitelesített kérelmeket, szükségünk van a fiók SID és hitelesítési token, amely a Twilio-fiók felhasználónévként és jelszóként működik. A legbiztonságosabb módja annak, hogy konfigurálja ezeket a csomópont modul az Azure-ban a rendszerkörnyezeti változók, amelyek közvetlenül az Azure felügyeleti konzolon állítható be.

Válassza ki a node.js webhelyet, és kattintson a "CONFIGURE" hivatkozásra.  Ha egy kicsit lefelé görget, megjelenik egy terület, ahol beállíthatja az alkalmazás konfigurációs tulajdonságait.  Adja meg a Twilio-fiók hitelesítő adatait[(a Twilio konzolon található) az][twilio_console]ábrán látható módon - győződjön meg róla, hogy elnevezi őket, `TWILIO_ACCOUNT_SID` és `TWILIO_AUTH_TOKEN`a, illetve:

![Azure felügyeleti konzol][azure-admin-console]

Miután konfigurálta ezeket a változókat, indítsa újra az alkalmazást az Azure konzolon.

### <a name="declaring-the-twilio-module-in-packagejson"></a>A Twilio modul deklarálása a package.json ban
Ezután létre kell hoznunk egy package.json-t a csomópontmodul-függőségeink [kezelésére az npm-en]keresztül. Az *Azure/node.js* oktatóanyagban létrehozott `package.json` `server.js` fájllal azonos szinten hozzon létre egy nevű fájlt.  A fájlba helyezze a következőket:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

Ez függőségként deklarálja a twilio modult, valamint a népszerű [Express webes keretrendszert][express] és az EJS sablonmotort.  Oké, most már minden készen áll- írjunk egy kódot!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Kimenő hívás kezdeményezése
Hozzunk létre egy egyszerű űrlapot, amely hívást kezdeményez egy általunk választott számra. Nyissa `server.js`meg a mezőt, és írja be a következő kódot. Megjegyzés: hol azt mondja: "CHANGE_ME" - tegye a nevét az azure honlapján van:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Ezután hozzon `views` létre egy könyvtárat nevű `index.ejs` - ebben a könyvtárban, hozzon létre egy fájlt nevű a következő tartalommal:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Most telepítse webhelyét az Azure-ba, és nyissa meg otthonát. Meg kell tudnia adni a telefonszámát a szövegmezőbe, és kap egy hívást a Twilio szám!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>SMS küldése
Most állítsunk be egy felhasználói felületet és űrlapkezelési logikát szöveges üzenet küldéséhez. Nyissa `server.js`meg a , és adja hozzá `app.post`a következő kódot az utolsó hívás után:

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

A `views/index.ejs`alkalmazásban adjon hozzá egy másik űrlapot az első alá, amely számot és szöveges üzenetet küld:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Telepítse újra az alkalmazást az Azure-ba, és most már be küldheti az űrlapot, és küldhet magának (vagy bármely legközelebbi barátjának) egy szöveges üzenetet!

<a id="nextsteps"/>

## <a name="next-steps"></a>Következő lépések
Most már megtanulta a node.js és a Twilio használatával kommunikáló alkalmazások létrehozásának alapjait. De ezek a példák alig karcolja meg a twilio és a node.js felületét. A Twilio és a node.js használatával kapcsolatos további információkért tekintse meg az alábbi forrásokat:

* [Hivatalos moduldokumentumok][docs]
* [Tutorial VoIP a node.js alkalmazások][voipnode]
* [Votr - egy valós idejű SMS szavazási alkalmazás node.js és CouchDB (három részből áll)][votr]
* [Programozás párosítása a böngészőben a node.js][pair]

Reméljük, hogy szereti a node.js és a Twilio feltörését az Azure-ban!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
