---
title: A Twilio használata hang-, VoIP-és SMS-üzenetküldéshez az Azure-ban
description: Megtudhatja, hogyan készíthet telefonhívást, és hogyan küldhet SMS-üzenetet a Twilio API szolgáltatással az Azure-ban. A Node. js-ben írt kód mintái.
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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637268"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>A Twilio használata hang-, VoIP-és SMS-üzenetküldéshez az Azure-ban
Ez az útmutató bemutatja, hogyan hozhat létre olyan alkalmazásokat, amelyek a Twilio és a Node. js használatával kommunikálnak az Azure-ban.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Mi az a Twilio?
A Twilio egy API-platform, amely megkönnyíti a fejlesztők számára a telefonhívások, a szöveges üzenetek küldését és fogadását, valamint a VoIP-hívások beágyazását böngészőalapú és natív mobil alkalmazásokba. Röviden ugorjon át, hogy ez hogyan működik, mielőtt bekerül a szolgáltatásba.

### <a name="receiving-calls-and-text-messages"></a>Hívások és szöveges üzenetek fogadása
A Twilio lehetővé teszi a fejlesztők számára, hogy olyan [programozható telefonszámokat vásároljanak][purchase_phone] , amelyek mind a küldési, mind a fogadási hívásokat és szöveges üzeneteket egyaránt felhasználhatják. Ha egy Twilio bejövő hívást vagy szöveget kap, akkor a Twilio egy HTTP POST vagy GET kérést küld a webalkalmazásnak, amely a hívás vagy a szöveg kezelésével kapcsolatos utasításokat kéri. A kiszolgáló válaszol a Twilio HTTP-kérelmére a [TwiML][twiml], egy egyszerű XML-címkével, amely utasításokat tartalmaz a hívás vagy szöveg kezeléséhez. Néhány példát láthatunk a TwiML.

### <a name="making-calls-and-sending-text-messages"></a>Hívások kezdeményezése és szöveges üzenetek küldése
A Twilio Web Service API-nak küldött HTTP-kérések segítségével a fejlesztők szöveges üzeneteket küldhetnek vagy kimenő telefonhívásokat indíthatnak. A kimenő hívások esetében a fejlesztőnek olyan URL-címet is meg kell adnia, amely a TwiML vonatkozó utasításokat adja vissza a kimenő hívás kezeléséhez a csatlakozás után.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>A VoIP-képességek beágyazása a felhasználói felület kódjába (JavaScript, iOS vagy Android)
A Twilio egy ügyféloldali SDK-t biztosít, amely bármilyen asztali webböngészőt, iOS-alkalmazást vagy Android-alkalmazást bekapcsolhat egy VoIP-telefonra. Ebben a cikkben arra fogunk összpontosítani, hogyan használható a VoIP-hívás a böngészőben. A böngészőben futó *Twilio JavaScript SDK* mellett egy kiszolgálóoldali alkalmazást (a Node. js-alkalmazást) kell használni a "képesség token" a JavaScript-ügyfélhez való kibocsátásához. További információ a [Twilio dev blogon][voipnode]található Node. js-sel rendelkező VoIP használatáról.

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Regisztráció a Twilio (Microsoft kedvezménnyel)
A Twilio-szolgáltatások használata előtt regisztrálnia kell [egy fiókot][signup]. Microsoft Azure ügyfeleink különleges kedvezményt kapnak – [itt regisztrálhat][signup].

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Node. js-alapú Azure-webhely létrehozása és üzembe helyezése
Ezután létre kell hoznia egy, az Azure-on futó Node. js-webhelyet. [Ennek elvégzéséhez a hivatalos dokumentációja itt található][azure_new_site]. Magas szinten a következő műveleteket hajtja végre:

* Regisztráció Azure-fiókra, ha még nem rendelkezik ilyennel
* Új webhely létrehozása az Azure felügyeleti konzol használatával
* Verziókövetés támogatásának hozzáadása (feltételezzük, hogy a git-t használta)
* Egyszerű Node. `server.js` JS-alapú webalkalmazással rendelkező fájl létrehozása
* Az egyszerű alkalmazás üzembe helyezése az Azure-ban

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>A Twilio modul konfigurálása
Ezután egy egyszerű Node. js-alkalmazást fogunk írni, amely a Twilio API-t használja. Mielőtt elkezdené, be kell állítania a Twilio-fiók hitelesítő adatait.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>A Twilio hitelesítő adatainak konfigurálása a rendszerkörnyezet változói között
Ahhoz, hogy hitelesített kérelmeket Twilio a háttérrendszer számára, szükség van a fiók SID-és hitelesítési jogkivonatára, amely a Twilio-fiókhoz beállított felhasználónévként és jelszóként működik. Az Azure-ban a Node modullal való használatra a legbiztonságosabb módszer a rendszerkörnyezet változói, amelyet közvetlenül az Azure felügyeleti konzolon lehet beállítani.

Válassza ki a Node. js-webhelyet, és kattintson a "Konfigurálás" hivatkozásra.  Ha Legörget egy kicsit, egy olyan területen fog megjelenni, amelyen beállíthatja az alkalmazás konfigurációs tulajdonságait.  Adja meg a Twilio-fiókja hitelesítő adatait (a[Twilio-konzolon található][twilio_console]), az ábrán látható módon – ügyeljen arra `TWILIO_ACCOUNT_SID` `TWILIO_AUTH_TOKEN`, hogy nevezze el őket, illetve:

![Azure felügyeleti konzol][azure-admin-console]

Miután konfigurálta ezeket a változókat, indítsa újra az alkalmazást az Azure-konzolon.

### <a name="declaring-the-twilio-module-in-packagejson"></a>A Twilio modul deklarálása a Package. JSON fájlban
Ezután létre kell hoznia egy Package. JSON fájlt a Node modul függőségeinek [NPM]-n keresztüli kezeléséhez. Az `server.js` *Azure/Node. js* oktatóanyagban létrehozott fájllal megegyező szinten hozzon létre egy nevű `package.json`fájlt.  A fájl belsejében helyezze a következőket:

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

Ez deklarálja a twilio modult függőségként, valamint a népszerű [expressz webes keretrendszert][express] és a EJS-sablon motorját.  Oké, most már készen is van, írj egy kódot!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Kimenő hívás létrehozása
Hozzunk létre egy egyszerű űrlapot, amely egy tetszőleges számú hívást fog elhelyezni. Nyissa meg a t ,ésírjabeakövetkezőkódot.`server.js` Vegye figyelembe, hogy a "CHANGE_ME" kifejezéssel írja be az Azure-webhely nevét:

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

Ezután hozzon létre egy nevű `views` könyvtárat ebben a könyvtárban, és hozzon létre `index.ejs` egy nevű fájlt a következő tartalommal:

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

Most telepítse a webhelyet az Azure-ba, és nyissa meg a kezdőlapját. Meg kell adnia a telefonszámát a szövegmezőben, és hívást kell fogadnia a Twilio számáról.

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>SMS-üzenet küldése
Most állítson be egy felhasználói felületet és egy űrlap-kezelési logikát szöveges üzenet küldéséhez. Nyissa meg a következő kódot `app.post` ,ésadjahozzáazutolsóhívásután:`server.js`

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

A `views/index.ejs`alkalmazásban adjon hozzá egy másik űrlapot az első alatt egy szám és egy szöveges üzenet elküldéséhez:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Telepítse újra az alkalmazást az Azure-ba, és most küldje el az űrlapot, és küldje el magát (vagy a legközelebbi barátok bármelyikét) SMS-üzenetben.

<a id="nextsteps"/>

## <a name="next-steps"></a>További lépések
Ezzel megtanulta a Node. js és a Twilio használatának alapjait a kommunikációt támogató alkalmazások létrehozásához. Ezek a példák azonban alig megkarcolják a Twilio és a Node. js-vel kapcsolatos lehetséges felületet. A Twilio és a Node. js használatával kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Hivatalos modul – dokumentumok][docs]
* [Oktatóanyag a VoIP-vel Node. js-alkalmazásokkal][voipnode]
* [Votr – valós idejű SMS-szavazási alkalmazás Node. js-vel és CouchDB (három részből)][votr]
* [Pair Programming a böngészőben a Node. js-sel][pair]

Reméljük, hogy szereti a hacker Node. js-t és a Twilio az Azure-ban!

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
