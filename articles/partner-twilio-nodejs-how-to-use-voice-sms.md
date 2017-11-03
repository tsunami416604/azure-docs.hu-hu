---
title: "Twilio használ hang, a VoIP és az SMS-üzenetkezelés az Azure-ban"
description: "Útmutató a telefonhívás, és a Twilio API szolgáltatás SMS üzenet küldése az Azure-on. A Kódminták a Node.js-ben."
services: 
documentationcenter: nodejs
author: devinrader
manager: wpickett
editor: 
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: wpickett
ms.openlocfilehash: f347540c78be712fc46d1d36b47ca4e23a62e28a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Twilio használ hang, a VoIP és az SMS-üzenetkezelés az Azure-ban
Ez az útmutató bemutatja, hogyan kommunikálni Twilio és az Azure-on node.js alkalmazásokat lehet készíteni.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Mi az, hogy a Twilio?
Twilio egy API-platform, amely megkönnyíti a fejlesztők és telefonhívásokat fogadja, küldése és a szöveges üzeneteket, és beágyazási böngészőalapú és natív mobil alkalmazásokba VoIP hívása. Most röviden ismerteti ennek működéséről belevágna előtt.

### <a name="receiving-calls-and-text-messages"></a>Hívások és a szöveges üzenetek fogadása
Twilio lehetővé teszi a fejlesztők számára [programozható telefonszámok beszerzési] [ purchase_phone] való küldenie és fogadnia hívások és a szöveges üzenetek is használható. Ha a Twilio-számnak fogad egy bejövő hívás vagy szöveges, Twilio elküld a webes alkalmazás egy HTTP POST vagy GET kérés, kéri a hívást vagy SMS kezeléséről további tájékoztatást. A kiszolgáló válaszol a Twilio tartozó HTTP-kérelem [TwiML][twiml], egyszerű hívás vagy SMS kezelésére vonatkozó útmutatást tartalmazó XML-címkék-készlettel. Kis türelmet a TwiML példái lesz látható.

### <a name="making-calls-and-sending-text-messages"></a>Hívások és a szöveges üzenetek küldése
HTTP-kérelmekre, így a Twilio webszolgáltatási API, fejlesztők szöveges üzenetek küldéséhez vagy kimenő telefonhívást kezdeményezni. Kimenő hívások a fejlesztői is meg kell adnia egy URL-címet, amely visszaadja az TwiML című cikkben talál útmutatást a kimenő hívás kezelésére, amennyiben van csatlakoztatva.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>VoIP képességek beágyazása a felhasználói felület kódot (JavaScript, iOS vagy Android)
Twilio biztosít egy ügyféloldali SDK, amely bármely asztali böngészőben, iOS-alkalmazás vagy Android-alkalmazás lehet kikapcsolni, a VoIP-telefont. Ebben a cikkben tárgyaljuk használata VoIP hívja a böngészőben. Kívül a *Twilio JavaScript SDK* a böngésző fut, a kiszolgálóoldali alkalmazás (a node.js-alkalmazás) kell használni egy "funkció token" kiadni a JavaScript-ügyfél. További VoIP használata node.js [a Twilio-fejlesztői blog][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Regisztrálás a Twilio (a Microsoft kedvezményes)
Twilio-szolgáltatások használatához először [egy fiók][signup]. Microsoft Azure-ügyfelek kap egy különös kedvezményes - [ügyeljen arra, hogy itt regisztrálhat][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Létrehozhat és telepíthet egy node.js Azure-webhely
A következő szüksége lesz egy Azure-on futó node.js-webhelyet hoz létre. [Ezzel a hivatalos dokumentációját a következő helyen található][azure_new_site]. Magas szinten, akkor lesz kell a következő módon:

* Regisztrál az Azure-fiók esetén, ha még nem rendelkezik már
* Új webhely létrehozása az Azure felügyeleti konzolját használja
* Forrás-vezérlés támogatásának (indulunk követte git) hozzáadása
* A fájl létrehozásakor a következő `server.js` az egyszerű node.js-webalkalmazás
* Az Azure-bA az egyszerű alkalmazás üzembe helyezéséhez

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>A Twilio-modul konfigurálása
Megkezdődik a következő azt írni egy egyszerű node.js-alkalmazást, amely a Twilio API-t használja. Az első lépések, igazolnia kell a Twilio-fiók hitelesítő adatainak konfigurálása.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Rendszerszintű környezeti változókat a Twilio-hitelesítő adatok konfigurálása
Ahhoz, hogy a Twilio-háttér elleni hitelesített kéréseket, igazolnia kell a fiók SID azonosítója és a hitelesítési jogkivonat, mely a felhasználónevet és jelszót a Twilio-fiókjában funkciót. A legbiztonságosabb konfigurálásukkal használható az Azure-ban a csomópont modullal módja keresztül rendszerszintű környezeti változókat, amelyek közvetlenül az Azure felügyeleti konzolon állíthatja be.

Jelölje ki a node.js-webhelyet, és kattintson a "Beállítása" hivatkozásra.  Ha görgessen lefelé kissé a folyamatot, akkor jelenik meg egy olyan területre, amelyen meg a konfigurációs tulajdonságok az alkalmazáshoz.  Adja meg a Twilio-fiók hitelesítő adatait ([megtalálható-e a Twilio-konzol][twilio_console]) látható - ügyeljen arra, hogy azok neve `TWILIO_ACCOUNT_SID` és `TWILIO_AUTH_TOKEN`, illetve:

![Az Azure felügyeleti konzol][azure-admin-console]

Miután konfigurálta a változókat, indítsa újra az alkalmazást az Azure-konzolon.

### <a name="declaring-the-twilio-module-in-packagejson"></a>A Twilio-modul a Package.JSON kódjában deklaráló
Következő lépésként létre kell hoznunk egy package.json kezelheti a csomópont modul függőségek keresztül [npm]. Ugyanazon a szinten az `server.js` létrehozott fájlt a *Azure/node.js* oktatóanyag, hozzon létre egy fájlt `package.json`.  Ez a fájl helye a következő:

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

Ez a twilio-modul deklarál, egy függőségi, valamint a népszerű [webes keretrendszer Express] [ express] és a EJS sablon motor.  Rendben most azt Mindennel - most kódírást!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Egy kimenő hívást
Hozzon létre helyezi el a szám választjuk hívás egyszerű űrlapot. Nyissa meg `server.js`, és írja be a következő kódot. Megjegyzés: Ha "CHANGE_ME" - felirat látható az azure-webhely neve nincs put:

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

Ezután hozzon létre egy könyvtárat `views` - belül ebben a könyvtárban, hozzon létre egy fájlt `index.ejs` a következő tartalommal:

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

Most a webhely telepítése az Azure-ba, és nyissa meg az új kezdőlapja. A szövegmezőbe írja be a megadott telefonszámot, és a felhívja a Twilio több képesnek kell lennie!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>SMS küldése
Most állítsa be a felhasználói felület és az űrlap logika kezelése szöveges üzenetet küldeni. Nyissa meg `server.js`, és adja hozzá a következő kódot után utolsó hívása `app.post`:

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

A `views/index.ejs`, egy másik űrlapon egy számot és egy szöveges üzenetet küldeni az elsőt a hozzáadása:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Hozza létre újra az alkalmazás az Azure-ba, és meg kell küldeni, amelyek kialakításához, és saját kezűleg (vagy bármely legközelebbi ismerősei) tartalmazó szöveges üzenetet küldjön!

<a id="nextsteps"/>

## <a name="next-steps"></a>Következő lépések
Most megtanulta, node.js és Twilio kommunikáló alkalmazások használatának alapjaival. De ezek a példák alig ideiglenes a felületet, hogy a Twilio és node.js lehetséges. Twilio használata node.js további információkért tekintse meg a következőket:

* [Hivatalos modul docs][docs]
* [Node.js-alkalmazások a VoIP-oktatóanyag][voipnode]
* [Votr - egy valós idejű SMS szavazás alkalmazás a node.js és CouchDB (három rész)][votr]
* [A böngészőben a node.js pár programozás][pair]

Reméljük, node.js és Twilio támadásoknak Azure kedvelt!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
