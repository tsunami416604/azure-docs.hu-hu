---
title: Twilio használata Hanghívási, a VoIP és a SMS-üzenetkezelés az Azure-ban
description: Útmutató a telefonhívás, és a Twilio API-szolgáltatással egy SMS-üzenet küldése az Azure-ban. Node.js nyelven írt kódmintákat.
services: ''
documentationcenter: nodejs
author: devinrader
manager: wpickett
editor: ''
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: wpickett
ms.openlocfilehash: d9f419c48f64ba697e031dfc680bc9cb12bba5c4
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52421964"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Twilio használata Hanghívási, a VoIP és a SMS-üzenetkezelés az Azure-ban
Ez az útmutató bemutatja, hogyan hozhat létre alkalmazásokat, amelyek kommunikálnak a Twilio és a node.js az Azure-ban.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Mit jelent a Twilio?
A Twilio egy API-platform, amely megkönnyíti a fejlesztők számára, győződjön meg arról, és a telefonhívásokat fogadja, küldése és a szöveges üzenetet fogadni és a beágyazási böngészőalapú és natív mobilalkalmazások-be irányuló VoIP-hívás. Nézzük röviden haladnak át, hogyan is működik mindez mielőtt belevágna.

### <a name="receiving-calls-and-text-messages"></a>Hívások és a szöveges üzenetek fogadása
A Twilio lehetővé teszi a fejlesztők számára, hogy [programozható telefonszámokat vásárlása] [ purchase_phone] , és képes hívásokat és szöveges üzenetek küldésére használható. Amikor Twilio-számnak fogad egy bejövő hívás vagy szöveges üzenet, Twilio küld a webalkalmazás egy HTTP POST vagy GET kérést, lát kezeléséről a hívást vagy SMS-utasításokat. A kiszolgáló válaszol a HTTP-kérés a Twilio [TwiML][twiml], egy utasításokat tartalmazó hívást vagy SMS kezelése tartalmazó XML-címkék egyszerű készletét. Kis türelmet fog láthatjuk TwiML példái.

### <a name="making-calls-and-sending-text-messages"></a>Hívások és a szöveges üzenetek küldéséhez
HTTP-kéréseket, így a Twilio webszolgáltatási API, a fejlesztők szöveges üzenetek küldéséhez, vagy kimenő telefonhívásokat kezdeményezni. Kimenő hívások a fejlesztőnek meg kell adnia egy URL-címet, amely a kimenő hívás kezelése a csatlakozás után TwiML útmutatást ad vissza.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>VoIP-képességek beágyazása a felhasználói felület kódot (a JavaScript, iOS vagy Android)
A Twilio egy ügyféloldali SDK-t mely kapcsolhatja be minden olyan asztali böngésző, az iOS-alkalmazás vagy az Android-alkalmazás, a VoIP telefon biztosít. Ebben a cikkben fogunk koncentrálni VoIP hívja meg a böngésző használata. Mellett a *Twilio JavaScript SDK* a böngészőben futó, egy kiszolgálóoldali alkalmazáshibák (a node.js-alkalmazás) kell használni a JavaScript-kliens: képesség token"kiadására. Tudjon meg többet a VoIP használatával node.js-szel kapcsolatos [a Twilio-fejlesztői blog][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Feliratkozás a Twilio (a Microsoft kedvezményes)
Mielőtt Twilio-szolgáltatások használatával, előbb futtatnia kell [egy fiók][signup]. Microsoft Azure-ügyfelek kap egy különleges kedvezményes - [ügyeljen arra, hogy itt regisztráció][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Létre és helyezhet üzembe egy node.js az Azure-webhelyek
Ezután szüksége lesz egy Azure-on futó node.js-webhely létrehozása. [Ennek a hivatalos dokumentáció megtalálható itt][azure_new_site]. Magas szinten Ön végzi el a következőket:

* Ha még nincs Azure-fiókot regisztrál
* Új webhely létrehozása az Azure felügyeleti konzolját használja
* Forrás vezérlés támogatást (indulunk használt git)
* Hozzon létre egy fájlt `server.js` az egyszerű node.js-webalkalmazás
* Az Azure-bA egyszerű alkalmazás üzembe helyezéséhez

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>A Twilio-modul konfigurálása
Ezt követően megkezdjük írhat egy egyszerű node.js-alkalmazást, amely a Twilio API-t használja. Az első lépések előtt kell a Twilio-fiók hitelesítő adatainak konfigurálása.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Rendszerszintű környezeti változókat a Twilio hitelesítő adatok konfigurálása
Győződjön meg arról, a Twilio háttérbe irányuló hitelesített kérések, a fiók SID azonosítója és a hitelesítési token, a felhasználónevet és jelszót megadni a Twilio-fiók, mely függvény kell. A legbiztonságosabb konfigurálásukkal használható az Azure-ban, a csomópont modullal rendszerszintű környezeti változókat, amelyek közvetlenül az Azure felügyeleti konzolján beállíthatja keresztül van.

Válassza ki a node.js-webhelyet, és kattintson a "KONFIGURÁLÁS" hivatkozásra.  Egy kis lefelé görgetve láthatja, amelyen beállíthatja konfigurációs tulajdonságaiban az alkalmazás egy adott területre.  Adja meg a Twilio-fiókja hitelesítő adatait ([a Twilio konzoljában található][twilio_console]) módon – ügyeljen arra, hogy adja nekik `TWILIO_ACCOUNT_SID` és `TWILIO_AUTH_TOKEN`, illetve:

![Az Azure felügyeleti konzol][azure-admin-console]

Miután konfigurálta ezeket a változókat, indítsa újra az alkalmazást az Azure-konzolon.

### <a name="declaring-the-twilio-module-in-packagejson"></a>A Twilio-modul a package.json deklaráló
Következő lépésként létre kell hozni egy kezelheti a csomópont modul függőségeihez a package.json [npm]. Azonos szinten, a `server.js` létrehozott fájlt a *Azure/node.js* oktatóanyag, hozzon létre egy fájlt `package.json`.  Ez a fájl helye a következő:

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

Ez a twilio-modul deklarálja, a függőség, valamint a népszerű [expressz webes keretrendszer] [ express] és a EJS sablonmotor.  Rendben most azt minden beállítás kész – most írjon egy kódrészletet!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Egy kimenő hívás
Hozzunk létre egy egyszerű képernyő, amely számos lehetőséget választjuk hívása fogja elhelyezni. Nyisson meg `server.js`, és adja meg a következő kódot. Ha azt írja, "CHANGE_ME" - el az azure-webhely neve:

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

Következő lépésként hozzon létre egy könyvtárat, nevű `views` – ebben a könyvtárban, belül hozzon létre egy fájlt `index.ejs` a következő tartalommal:

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

Most a webhelyet üzembe helyezni az Azure, és nyissa meg a kezdőlap. A szövegmezőben adja meg a telefonszámát, és felhívja a Twilio számból képesnek kell lennie!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Küldjön SMS-t
Most állítsa be a felhasználói felület és az űrlap kezelése logikai szöveges üzenet küldése. Nyisson meg `server.js`, és adja hozzá a következő kód utolsó hívása után `app.post`:

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

A `views/index.ejs`, egy szám és a egy szöveges üzenet elküldéséhez első szövegmező alatt egy másik képernyő hozzáadása:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Telepítse újra az alkalmazás az Azure-ba, és most meg kell tudni nyújt, amely alkotnak, és küldhet saját magának (vagy bármelyik legközelebbi barátai) SMS-ben!

<a id="nextsteps"/>

## <a name="next-steps"></a>További lépések
Most már megismerkedett alapjait node.js-t és a Twilio segítségével olyan alkalmazások fordítása, kommunikációhoz. Azonban ezekben a példákban Jéghegy a mi lehetséges a Twilio és a node.js használatával. További információ a Twilio használatával node.js-szel tekintse meg az alábbi forrásanyagokat:

* [Hivatalos modul docs][docs]
* [A node.js-alkalmazások VoIP-oktatóanyag][voipnode]
* [Votr - szavazóalkalmazás a node.js és CouchDB (három részből áll) a valós idejű SMS][votr]
* [Páros programozás a böngészőben a node.js használatával][pair]

Reméljük, hogy az Azure-ban node.js-t és a Twilio feltöréssel kedvenc!

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
