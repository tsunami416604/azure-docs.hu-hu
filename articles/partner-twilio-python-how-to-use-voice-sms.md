---
title: Twilio használata Hanghívási és SMS (Python) |} A Microsoft Docs
description: Útmutató a telefonhívás, és a Twilio API-szolgáltatással egy SMS-üzenet küldése az Azure-ban. Python nyelven írt kódmintákat.
services: ''
documentationcenter: python
author: devinrader
manager: twilio
editor: ''
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: f6d144daa165d350c6615f323b25f8860697f2c1
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422493"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Hogyan lehet a Twilio használata Hanghívási és SMS-funkciókhoz a Pythonban
Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat, a Twilio API-t szolgáltatással az Azure-ban. Az ismertetett forgatókönyvek között megtalálható, így a telefonhívás, és a egy rövid üzenetet szolgáltatást (SMS) üzenetet küld. A Twilio és az alkalmazások használata hanghívási és SMS további információkért lásd: a [lépések](#NextSteps) szakaszban.

## <a id="WhatIs"></a>Mit jelent a Twilio?
A Twilio jövőjének vállalati kommunikáció, így a fejlesztők hang, a VoIP és a üzenetkezelési alkalmazásokba történő beágyazása van. A Twilio API kommunikációs platformon keresztül adatokhoz hozzáférést biztosító felhőalapú, a globális környezetben szükséges összes infrastruktúra virtualizálása azokat. Alkalmazásokat hozhat létre egyszerű és méretezhető. Használja ki a rugalmasan használatalapú – mint-akkor lépjen a díjszabás, és kihasználhatják a felhő megbízhatóságát.

**A twilio használata Hanghívási** lehetővé teszi, hogy telefonhívásokat fogadja, és az alkalmazások.
**A Twilio SMS** lehetővé teszi, hogy az alkalmazás szöveges üzeneteket küldjön és fogadjon.
**A Twilio-ügyfél** lehetővé teszi bármely telefonon, táblagépen vagy böngészőben VoIP hívásait, és támogatja a WebRTC.

## <a id="Pricing"></a>A Twilio-díjszabás és ajánlatok küldésére
Az Azure-ügyfelek kap egy [a különleges ajánlat] [ special_offer] 10 USD kredit Twilio, a Twilio-fiók frissítésekor. A Twilio-Kredit bármely Twilio-használati (10 USD kredit egyenértékű, akár 1000 SMS-üzenetek küldése vagy fogadása legfeljebb 1000 bejövő hangalapú perc, a telefon száma és az üzenet vagy hívás cél helyétől függően) is alkalmazható. Ez beváltása [Twilio kredit] [ special_offer] és az első lépések.

A Twilio szolgáltatás használatalapú fizetést biztosító. Nincsenek díjak sincsenek beállítás, és a fiókot bármikor bezárhatja. További információt talál [Twilio díjszabás][twilio_pricing].

## <a id="Concepts"></a>Fogalmak
A Twilio API egy RESTful API-t hanghívási és SMS-funkciókat biztosító alkalmazások. Ügyfélkódtárai több nyelven érhetők el egy listát lásd: [Twilio API függvénytárai][twilio_libraries].

Fontos szempontjai a Twilio API-t a, Twilio-műveletek és a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>A Twilio-műveletek
Az API-t felhasznál Twilio műveleteket; Ha például a **&lt;Say&gt;** művelet arra utasítja a Twilio hallhatóan kézbesíteni az üzenetet hívás.

Az alábbiakban látható egy Twilio-műveletek listájához. Ismerje meg az egyéb műveletek és funkciókat [Twilio Markup Language dokumentáció][twiml].

* **&lt;Tárcsázás&gt;**: a hívó kapcsolódik egy másik telefonszámot.
* **&lt;Gyűjtse össze&gt;**: összegyűjti a telefon billentyűzeten beírt számjegyeket.
* **&lt;Vonalbontás&gt;**: ér véget a hívást.
* **&lt;Felfüggesztés&gt;**: Csendes megvárja a megadott számú másodperc.
* **&lt;Play&gt;**: fájlból játszik le egy hangfájlt.
* **&lt;Várólista&gt;**: Adja hozzá a hívók egy üzenetsorba.
* **&lt;Rekord&gt;**: rögzíti a hívó a hang és a felvétel tartalmazó fájl URL-címet adja vissza.
* **&lt;Átirányítási&gt;**: átadja a vezérlést a hívást vagy SMS a TwiML egy másik URL-címen.
* **&lt;Elutasítás&gt;**: a Twilio-számra egy bejövő hívás elutasítása a számlázási, nélkül.
* **&lt;Tegyük fel, hogy&gt;**: alakíthatók át egymásba szöveg-beszéd átalakítás, amely a hívást.
* **&lt;SMS&gt;**: SMS üzenetet küld.

### <a id="TwiML"></a>TwiML
TwiML XML-alapú utasítások alapján a Twilio-műveleteket tartalmazó tájékoztatja a hívás feldolgozása Twilio vagy SMS.

Tegyük fel, a következő TwiML konvertálná szöveg **Hello World** át.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Amikor az alkalmazás meghívja a Twilio API-t, az API-paraméterek egyik az URL-cím, amely a TwiML választ ad vissza. Fejlesztési célokra a Twilio által megadott URL-címek segítségével az alkalmazások által használt TwiML visszajelzést. Akkor is elhelyezheti, a saját URL-címek a TwiML válaszok előállításához, és a egy másik lehetőség a `TwiMLResponse` objektum.

A Twilio műveletek, az attribútumokat és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [Twilio API-t][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Amikor készen áll a Twilio-fiók létrehozása, regisztráció: [próbálja meg Twilio][try_twilio]. Kezdje egy ingyenes fiókot, és később frissítse a fiókját.

Amikor regisztrál egy Twilio-fiók, kap egy fiók biztonsági azonosítója és a egy hitelesítési tokent. Mindkét lesz szükség a Twilio API-hívásokat. Fiókja a jogosulatlan hozzáférés megakadályozása érdekében, hogy a hitelesítési jogkivonat biztonságának megőrzése. A fiók SID azonosítója és a hitelesítési jogkivonat megtekinthető a [Twilio-konzol][twilio_console], a címkével ellátott mezőket **fiók SID** és **hitelesítési JOGKIVONATÁT**, illetve.

## <a id="create_app"></a>Python-alkalmazás létrehozása
Egy Python-alkalmazás, amely a Twilio szolgáltatás használja, és az Azure-ban fut-e semmiben nem különbözik bármely egyéb Python-alkalmazás, amely a Twilio szolgáltatás használ. Bár a Twilio szolgáltatás REST-alapú, és nem hívható meg a Python többféle módon, ez a cikk a Twilio-szolgáltatások használata összpontosítanak [Twilio kódtára a Pythonhoz a Githubról][twilio_python]. A Pythonhoz készült a Twilio-könyvtár használatával kapcsolatos további információkért lásd: [ https://readthedocs.org/docs/twilio-python/en/latest/index.html ] [ twilio_lib_docs].

Első, [új Azure Linux virtuális gép beállítás] [azure_vm_setup] való működésre gazdagépként, az új Python-webalkalmazás számára. Ha a virtuális gép fut, tegye elérhetővé alkalmazását egy nyilvános portot, az alább ismertetett kell.

### <a name="add-an-incoming-rule"></a>Adjon hozzá egy bejövő szabályt
  1. Nyissa meg az [a hálózati biztonsági csoport] [azure_nsg] oldalát.
  2. Válassza ki a hálózati biztonsági csoportot, amely megfelel a virtuális géppel.
  3. Adja hozzá és **kimenő szabály** a **80-as port**. Ügyeljen arra, hogy engedélyezi a bejövő bármilyen címről.

### <a name="set-the-dns-name-label"></a>Állítsa be a DNS-névcímke
  1. Nyissa meg az [a nyilvános IP-címek] [azure_ips] lapját.
  2. Válassza ki a nyilvános IP-címet adott correspends, a virtuális géppel.
  3. Állítsa be a **DNS-névcímke** a a **konfigurációs** szakaszban. Esetén ebben a példában ez lesz a következőhöz hasonló *your tartománycímkéjét*. centralus.cloudapp.azure.com

Miután a virtuális gép ssh-n keresztül kapcsolódni tud a webes keretrendszer, a választott telepítheti (a legtöbb jól ismert Python folyamatban lévő két [Flask](http://flask.pocoo.org/) és [Django](https://www.djangoproject.com)). Bármelyiket csak futtatásával telepítheti a `pip install` parancsot.

Ne feledje, hogy a virtuális gép csak a 80-as port forgalmát engedélyező konfiguráltuk. Ezért ügyeljen arra, hogy ezt a portot használja az alkalmazás konfigurálása.

## <a id="configure_app"></a>Az alkalmazás használhatja a Twilio szalagtárak konfigurálása
Az alkalmazás használhatja a Twilio-könyvtár a Pythonhoz készült kétféleképpen konfigurálhatja:

* Telepítse a Twilio-tár Python Pip csomagként. Az alábbi parancsokkal telepíthető:
   
        $ pip install twilio

    -VAGY-

* A Twilio-kódtár letöltése a Githubról a Pythonhoz készült ([https://github.com/twilio/twilio-python][twilio_python]), és ehhez hasonló telepítse:

        $ python setup.py install

A Twilio-könyvtár a Python telepítése után használhatja majd `import` , a Python-fájlokban:

        import twilio

További információkért lásd: [ https://github.com/twilio/twilio-python/blob/master/README.md ] [ twilio_github_readme].

## <a id="howto_make_call"></a>Hogyan: végezhet
A következő bemutatja, hogyan végezhet. Ez a kód is egy Twilio által biztosított helyet használ a Twilio Markup Language (TwiML) választ adja vissza. Helyettesítse be a saját értékeit a **from_number** és **to_number** telefonszámai, és győződjön meg arról, hogy ellenőrizte a **from_number** telefonszám a Twilio-fiók Mielőtt futtatná a kódot.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Ahogy már említettük, ez a kód egy Twilio által biztosított hely használatával TwiML a választ adja. Ehelyett használhatja a saját hely adja meg a TwiML válasz; További információkért lásd: [hogyan TwiML válaszokat biztosít a saját webhelyről](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Útmutató: az SMS-üzenet küldése
A következő példa használatával SMS üzenet küldése a `TwilioRestClient` osztály. A **from_number** száma az SMS-üzenetek küldéséhez a próbafiókokon Twilio által biztosított. A **to_number** szám ellenőrizni kell a Twilio-fiók a kód futtatása előtt.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>Hogyan: Adja meg a saját webhelyén TwiML válaszok
Amikor az alkalmazás a Twilio API-hívást kezdeményez, Twilio, küldjön TwiML választ vár egy URL-címet a kérelmet küld. A fenti példában a Twilio-megadott URL-címet használ [ https://twimlets.com/message ] [ twimlet_message_url]. (Amíg TwiML használatra szolgál a Twilio, megtekintheti azt a böngészőben. Kattintson például [ https://twimlets.com/message ] [ twimlet_message_url] megtekintéséhez egy üres `<Response>` elem; másik példaként kattintson [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ] [ twimlet_message_url_hello_world]megtekintéséhez egy `<Response>` elem, amely tartalmazza a `<Say>` elem.)

Így nem kell hagyatkoznia a Twilio-megadott URL-címet, a saját hely által visszaadott HTTP-válaszok is létrehozhat. A hely bármilyen nyelven, amely visszaadja az XML-válaszok; hozhat létre Ez a témakör azt feltételezi, hogy a TwiML létrehozása Python használni.

Az alábbi példákban arról, hogy a TwiML választ kimenete **Hello World** hívásakor.

A flask segítségével:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

A django segítségével:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Ahogy a fenti példában látható, a TwiML válasz egyszerűen egy XML-dokumentumot. A Twilio-kódtára a Pythonhoz osztályokat, amelyek TwiML fog létrehozni, tartalmazza. Az alábbi példa hoz létre a megfelelő választ, ahogy fent látható, de használ a `twiml` modul Python a Twilio-könyvtárban:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

TwiML kapcsolatos további információkért lásd: [ https://www.twilio.com/docs/api/twiml ] [ twiml_reference].

Miután a Python-alkalmazás állított TwiML válaszokat nyújt, használja az alkalmazás URL-CÍMÉT az átadott URL-CÍMÉT a `client.calls.create` metódust. Például, ha rendelkezik egy nevű webalkalmazást **MyTwiML** üzembe helyezett egy Azure-ban üzemeltetett szolgáltatást, használhatja az URL-címét, webhookot, az alábbi példában látható módon:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>How to: további Twilio-szolgáltatások használata
Itt látható példák, mellett Twilio kínál a webes API-kat használhatja az Azure-alkalmazásból további Twilio funkciói kihasználhatók. További részletek: a [Twilio API-dokumentáció][twilio_api].

## <a id="NextSteps"></a>Következő lépések
Most, hogy megismerte a Twilio szolgáltatás alapjait, kövesse az alábbi hivatkozások további:

* [A Twilio szolgáltatásra vonatkozó biztonsági irányelvek][twilio_security_guidelines]
* [A Twilio HowTo útmutatók és példakód][twilio_howtos]
* [A Twilio-Gyorsútmutatók][twilio_quickstarts]
* [Twilio a Githubon][twilio_on_github]
* [Beszéljen a Twilio-támogatás][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
