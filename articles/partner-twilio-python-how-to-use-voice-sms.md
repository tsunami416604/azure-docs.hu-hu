---
title: "Hang-és SMS (Python) Twilio használata |} Microsoft Docs"
description: "Útmutató a telefonhívás, és a Twilio API szolgáltatás SMS üzenet küldése az Azure-on. Kódminták Python."
services: 
documentationcenter: python
author: devinrader
manager: twilio
editor: 
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: f4a02bb7a7c46e7a0e3c75b870c522eae8294339
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Hang-és Python SMS képességei Twilio használata
Ez az útmutató bemutatja, hogyan Azure a Twilio API szolgáltatás közös programozási feladatok elvégzéséhez. A tárgyalt forgatókönyvekben szerepel, így a telefonhívás, és egy rövid üzenetet szolgáltatás (SMS) üzenet küldésekor. A Twilio- és hang- és SMS használata az alkalmazások további információkért lásd: a [lépések](#NextSteps) szakasz.

## <a id="WhatIs"></a>Mi az, hogy a Twilio?
Twilio a jövőbeli üzleti kommunikáció, így a fejlesztők hang, a VoIP és a üzenetküldési alkalmazásokba történő beágyazásához van működtetéséhez. Ezek virtualizálása keresztül a Twilio API kommunikációs platform, az ilyen felhőalapú, a globális környezetben szükséges összes infrastruktúrát. Alkalmazások olyan egyszerű hozhat létre és méretezhető. Élvezze a rugalmasságot Önnel fizetési-, nyissa meg árképzési, és igénybe vehesse az felhő megbízhatóság.

**Twilio hang** lehetővé teszi, hogy az alkalmazások és a telefonhívásokat fogadja.
**Twilio SMS** lehetővé teszi, hogy az alkalmazás szöveges üzeneteket küldjön és fogadjon.
**Twilio-ügyfél** lehetővé teszi a VoIP hívást a telefon, a táblagép vagy a böngésző és WebRTC támogatja.

## <a id="Pricing"></a>Twilio árak és a különleges ajánlatokkal
Az Azure-ügyfelek egy [a különleges ajánlat] [ special_offer] 10 $ Twilio-kredit a Twilio-fiók frissítésekor. A Twilio-jóváírási alkalmazhatók minden Twilio-használati (akár 1000 SMS-üzenetek küldésekor vagy fogadásakor 1000 bejövő hang perc, attól függően, hogy a telefon és üzenet vagy hívás cél helye egyenértékűek $10 követel). Ez beváltani [Twilio-jóváírási] [ special_offer] használatának első lépéseihez.

Twilio egy olyan használatalapú szolgáltatás. Nincsenek nem beállításról díjakat, és bármikor bezárhatja a fiókját. További részletei: [Twilio árképzési][twilio_pricing].

## <a id="Concepts"></a>Alapfogalmak
A Twilio API egy RESTful API, hang- és SMS-funkciókat biztosít az alkalmazások számára. Ügyféloldali kódtáraknál érhetők el több nyelven is; az útmutató, [Twilio API függvénytárai][twilio_libraries].

A Twilio API fő szempontjait Twilio-műveletek és Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-műveletek
Az API lehetővé teszi, hogy a Twilio használja műveletek; például a  **&lt;szóbeli&gt;**  parancs utasítja a Twilio hallhatóan hívás az üzenet kézbesítését.

A Twilio-műveletek listáját a következő: További információk az egyéb műveletek és képességek keresztül [Twilio Markup Language dokumentáció][twiml].

* **&lt;Telefonos kapcsolat&gt;**: a hívó kapcsolódik egy másik telefonon.
* **&lt;Gyűjtsön&gt;**: adta meg a telefon billentyűzetén számjegyek gyűjti.
* **&lt;Vonalbontás&gt;**: hívás véget ér.
* **&lt;Felfüggesztés&gt;**: Csendes megvárja-e a megadott számú másodpercnél tovább.
* **&lt;Lejátszási&gt;**: hangfájl lejátszása.
* **&lt;Várólista&gt;**: vegye fel a hívók várólistába.
* **&lt;Rekord&gt;**: a hang a hívó rögzíti, és a felvétel tartalmazó fájl URL-címet adja vissza.
* **&lt;Átirányítási&gt;**: hívás vagy SMS irányítását átviszi a TwiML egy másik URL-címen.
* **&lt;Elutasítása&gt;**: a Twilio-szám egy bejövő hívás elutasítása a nélkül, számlázási.
* **&lt;Tegyük fel például&gt;**: konvertálja szöveg-beszéd átalakítás hívás készült.
* **&lt;SMS&gt;**: SMS üzenetet küld.

### <a id="TwiML"></a>TwiML
TwiML olyan XML-alapú utasítások a Twilio-műveletek, amely tájékoztatja a Twilio hogyan lehet feldolgozni a hívást vagy SMS alapján.

Tegyük fel, a következő TwiML a szöveg volna átalakítása **Hello World** beszéddé.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Ha egy alkalmazás meghívja a Twilio API-t, az API paraméterek egyike az URL-címet, a TwiML választ ad vissza. Fejlesztési célra a Twilio által megadott URL-címek segítségével az alkalmazások által használt TwiML visszajelzést. Akkor is elhelyezheti a TwiML válaszok létrehozásához a saját URL-címeket, és egy másik lehetőség az `TwiMLResponse` objektum.

Twilio műveletek, az attribútumokat és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll arra, hogy a Twilio-fiókot, feliratkozhat [próbálja Twilio][try_twilio]. Egy ingyenes fiókot kezdődnie, és később frissítse a fiókját.

Amikor regisztrál egy Twilio-fiókja, kap egy fiók SID azonosítója és egy hitelesítési jogkivonatot. Is szükség lesz Twilio API-hívások indítása. Jogosulatlan hozzáférés elkerülése érdekében a fiókját, hogy a hitelesítési jogkivonat biztonságának megőrzése. A fiók SID azonosítója és a hitelesítési jogkivonat megtekinthető a [Twilio-konzol][twilio_console], a mezőket, címkével **fiók SID** és **hitelesítési JOGKIVONAT**, illetve.

## <a id="create_app"></a>Python-alkalmazás létrehozása
Egy Python-alkalmazás, amely a Twilio-szolgáltatás és az Azure-ban fut. ugyanolyan helyzetet teremt, mint bármely más Python alkalmazást, hogy a Twilio-szolgáltatást használ. Twilio-szolgáltatások REST-alapú, és a Python többféleképpen hívható, ez a cikk a Twilio-szolgáltatások használatával összpontosítanak [Twilio-kódtára a Pythonhoz a Githubról][twilio_python]. Python a Twilio-könyvtár használatával kapcsolatos további információkért lásd: [http://readthedocs.org/docs/twilio-python/en/latest/index.html][twilio_lib_docs].

Első, a [telepítés egy új Azure Linux virtuális gép] [azure_vm_setup] az új Python webalkalmazás az állomás járhasson el. Ha a virtuális gép fut, akkor teszi közzé az alkalmazást egy nyilvános portot, az alább ismertetett.

### <a name="add-an-incoming-rule"></a>Bejövő szabály felvétele
  1. Nyissa meg az [a hálózati biztonsági csoport] [azure_nsg] oldalra.
  2. Válassza ki a hálózati biztonsági csoport, amely megfelel a virtuális géppel.
  3. Adja hozzá és **kimenő szabály** a **80-as port**. Győződjön meg arról, hogy bármilyen címről bejövő.

### <a name="set-the-dns-name-label"></a>Állítsa be a DNS-névcímke
  1. Nyissa meg az [a nyilvános IP címet adott meg] [azure_ips] oldalra.
  2. Válassza ki a nyilvános IP-címet, hogy correspends a virtuális géppel.
  3. Állítsa be a **DNS-névcímke** a a **konfigurációs** szakasz. E példában fog megjelenni ehhez hasonló *a tartománycímke*. centralus.cloudapp.azure.com

Miután a virtuális gép SSH-n keresztül kapcsolódni tud a webes keretrendszer, az Ön által választott telepíthető (a legtöbb közismert a Python kívánt két [Flask](http://flask.pocoo.org/) és [Django](https://www.djangoproject.com)). Valamelyiken csak futtatásával telepítheti a `pip install` parancsot.

Ne feledje, hogy a virtuális gép csak 80-as porton forgalom engedélyezésére konfigurált. Ezért győződjön meg arról, hogy az alkalmazás ezen a porton történő konfigurálásához.

## <a id="configure_app"></a>Állítsa be az alkalmazását Twilio-könyvtárak használatára
Az alkalmazás a Twilio-könyvtár használandó Python két módon konfigurálhatja:

* Telepítse a Twilio-könyvtár Python Pip csomag. Az alábbi parancsokkal telepíthető:
   
        $ pip install twilio

    -VAGY-

* Töltse le a Githubról Python a Twilio-könyvtár ([https://github.com/twilio/twilio-python][twilio_python]), a telepítés:

        $ python setup.py install

Miután telepítette a Twilio-könyvtárhoz a Python, a következőket teheti majd `import` azt a Python-fájlokban:

        import twilio

További információkért lásd: [https://github.com/twilio/twilio-python/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Hogyan: végezhet
A következő bemutatja, hogyan végezhet. Ezt a kódot a Twilio által biztosított hely is használja a Twilio Markup Language (TwiML) válasz vissza. Helyettesítse a saját értékeit a **from_number** és **to_number** telefonszámokat, és győződjön meg arról, hogy ellenőrizte, hogy a **from_number** telefonszám Twilio-fiókja a kód futtatásához.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "http://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Ahogy azt korábban említettük, ez a kód Twilio által biztosított hely használatával a TwiML választ küldi vissza. Helyette a saját webhely segítségével adja meg a TwiML válasz; További információkért lásd: [hogyan TwiML válaszok adja meg a saját webhelyről](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Útmutató: az SMS-üzenet küldése
A következő bemutatja, hogyan használ egy SMS üzenetet küldeni a `TwilioRestClient` osztály. A **from_number** száma az SMS-üzenetek küldéséhez a próba fiókok Twilio biztosítja. A **to_number** szám ellenőrizni kell a Twilio-fiókja a kód futtatása előtt.

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
Amikor az alkalmazás a Twilio API hívása kezdeményez, Twilio TwiML választ várt URL-címet a kérést küld. A fenti példában a Twilio-megadott URL-címet [http://twimlets.com/message][twimlet_message_url]. (TwiML által Twilio használatra szolgál, míg tekintheti a böngészőben. Kattintson például [http://twimlets.com/message] [ twimlet_message_url] egy üres megjelenítéséhez `<Response>` elem; másik példaként, kattintson a [http://twimlets.com/message? % 5B0 üzenet: %5 D = Hello % 20World] [ twimlet_message_url_hello_world] megtekintéséhez egy `<Response>` elem, amely tartalmazza a `<Say>` elem.)

Ahelyett, hogy a Twilio által megadott URL-címen, létrehozhat saját webhelyén, amely HTTP-válaszokat ad vissza. A hely bármilyen nyelven, amely visszaadja az XML-válaszok; hozhat létre Ez a témakör azt feltételezi, hogy a TwiML létrehozásához Python használ.

Az alábbi példák kimeneteként TwiML választ, amely szerint **Hello World** hívásakor.

A Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

A django alkalmazással:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Ahogy látja, a fenti példa, TwiML a rendszer a választ csak az XML-dokumentum. A Twilio-kódtára a Pythonhoz TwiML hoz létre az Ön osztályokat tartalmazza. Az alábbi példa hozza létre a megfelelő választ, ahogy fent látható, de használja a `twiml` modul a Python Twilio könyvtárában:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

TwiML kapcsolatos további információkért lásd: [https://www.twilio.com/docs/api/twiml][twiml_reference].

Miután az TwiML visszajelzést beállítva Python alkalmazást, használja az alkalmazás URL-CÍMÉT az URL-cím lett átadva a `client.calls.create` metódust. Ha egy webes alkalmazás neve például **MyTwiML** az Azure szolgáltatásban telepített szolgáltatás, akkor használható az URL-címét a következő példában látható módon webhook:

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

## <a id="AdditionalServices"></a>Útmutató: további Twilio-szolgáltatásokkal
Az itt bemutatott példák Twilio lehetőséget biztosít webes API-k segítségével kihasználhatja az Azure alkalmazásról további Twilio-funkciókat. Teljes részletekért lásd: a [Twilio API-JÁNAK dokumentációja][twilio_api].

## <a id="NextSteps"></a>Következő lépések
Most, hogy a Twilio-szolgáltatás alapjai megtanulta, ezek hivatkozásokat követve tudhat meg többet:

* [Twilio biztonsági irányelvek][twilio_security_guidelines]
* [Útmutató a Twilio-útmutatók és példakódot][twilio_howtos]
* [Twilio gyors üzembe helyezési oktatóanyag][twilio_quickstarts]
* [A Githubon Twilio][twilio_on_github]
* [Kérdezze meg a Twilio-támogatás][twilio_support]

[special_offer]: http://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
