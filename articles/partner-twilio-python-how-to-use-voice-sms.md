---
title: A Twilio használata hang- és SMS-hez (Python) | Microsoft dokumentumok
description: Ismerje meg, hogyan kezdeményezhetsz telefonhívást, és hogyan küldhet SMS-üzenetet az Azure-beli Twilio API-szolgáltatással. Pythonban írt kódminták.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.openlocfilehash: edbc9eef6b5f0af2e70152b66228cdf09ef31110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242186"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>A Twilio használata hang- és SMS-képességekhez a Pythonban
Ez az útmutató bemutatja, hogyan hajthat végre közös programozási feladatokat az Azure-beli Twilio API-szolgáltatással. A tárgyalt forgatókönyvek közé tartozik a telefonhívás és a rövid üzenetszolgáltatás (SMS) küldése. A Twilio használatával, valamint a hang- és SMS-használattal kapcsolatos további információkért tekintse meg a [Következő lépések](#NextSteps) című szakaszt.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Mi az a Twilio?
A Twilio az üzleti kommunikáció jövőjét működteti, lehetővé téve a fejlesztők számára, hogy hang- és VoIP-üzeneteket ágyaznak be az alkalmazásokba. Virtualizálják az összes szükséges infrastruktúrát egy felhőalapú, globális környezetben, és a Twilio communications API platformon keresztül teszik ki. Az alkalmazások egyszerűen elkészíthetők és méretezhetők. A díjfizetésdíj-fizetéssel rugalmasan vehet igénybe, és élvezheti a felhő megbízhatóságának előnyeit.

**A Twilio Voice** lehetővé teszi, hogy az alkalmazások telefonhívásokat kezdeményezzenek és fogadjanak.
**A Twilio SMS** lehetővé teszi, hogy az alkalmazás szöveges üzeneteket küldjön és fogadjon.
**Twilio ügyfél** lehetővé teszi, hogy voIP hívásokat bármilyen telefonról, táblagépről vagy böngészőből, és támogatja a WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio árképzés és különleges ajánlatok
Az Azure-ügyfelek [a][special_offer] Twilio-fiók frissítésekén $10 twilio-kreditet kapnak. Ez a Twilio-kredit bármely Twilio-használatra alkalmazható ($10 kredit egyenértékű 1000 SMS-üzenet küldésével vagy akár 1000 bejövő hangperc fogadásával, a telefonszám és az üzenet vagy a hívás céljának helyétől függően). Váltsa be ezt a [Twilio-kreditet,][special_offer] és kezdje el.

A Twilio egy felosztó-kiosztó szolgáltatás. Nincsenek beállítási díjak, és bármikor megszüntetheti fiókját. További részleteket a [Twilio Pricing][twilio_pricing]oldalon talál.

## <a name="concepts"></a><a id="Concepts"></a>Fogalmak
A Twilio API egy RESTful API, amely hang- és SMS-funkciókat biztosít az alkalmazások számára. Az ügyféltárak több nyelven is elérhetők; a listát a [Twilio API Libraries című témakörben található.][twilio_libraries]

A Twilio API fő szempontjai a Twilio-műveletek és a Twilio markup nyelv (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio igék
Az API Twilio-igéket használ; A ** &lt;Say&gt; ** ige például arra utasítja a Twilio-t, hogy hallhatóan kézbesítsen egy üzenetet egy hívásra.

Az alábbi lista a Twilio-igéket tartalmazza. Ismerje meg a többi igét és képességet a [Twilio Markup Language dokumentációban][twiml]keresztül.

* **Tárcsázás&gt;: A hívót egy másik telefonhoz csatlakoztatja. &lt;**
* Gather : A telefon billentyűzetén megadott numerikus számjegyeket gyűjti. ** &lt;&gt;**
* **Hangup&gt;: Hívás &lt;** befejezése.
* Szünet : A megadott számú másodpercig csendben várakozik. ** &lt;&gt;**
* Lejátszás : Hangfájl lejátszása. ** &lt;&gt;**
* **Várólista&gt;: Adja hozzá a hívók várólistájába. &lt;**
* Rekord : Rögzíti a hívó hangját, és visszaadja a felvételt tartalmazó fájl URL-címét. ** &lt;&gt;**
* **Átirányítás&gt;: Egy hívás vagy SMS vezérlésének átvitele a TwiML-re egy másik URL-címen. &lt;**
* **Elutasítás:&gt;Elutasítja a bejövő hívást a Twilio-szám számlázás &lt;** nélkül.
* **Kimondás&gt;: A hívás közben készített szöveget beszédmé alakítja. &lt;**
* Sms : SMS-üzenetet küld. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML
A TwiML a Twilio-műveleteken alapuló XML-alapú utasítások készlete, amelyek tájékoztatják a Twilio-t a hívás vagy SMS feldolgozásáról.

Például a következő TwiML a **Hello World** szöveget beszédmé alakítja.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Amikor az alkalmazás meghívja a Twilio API-t, az API-paraméterek egyike az URL-címet, amely a TwiML-választ adja vissza. Fejlesztési célokra használhatja a Twilio által biztosított URL-címeket az alkalmazások által használt TwiML-válaszok biztosításához. A TwiML-válaszok létrehozásához saját URL-eket is üzemeltethet, egy `TwiMLResponse` másik lehetőség pedig az objektum használata.

A Twilio-műveletekről, azok attribútumairól és a TwiML-ről a [TwiML][twiml]című témakörben talál további információt. A Twilio API-ról további információt a [Twilio API című témakörben][twilio_api]talál.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll a Twilio-fiók leválasztására, regisztráljon a [Try Twilio][try_twilio]című naphoz. Kezdheti egy ingyenes fiókkal, és később frissítheti fiókját.

Amikor regisztrál egy Twilio-fiókot, kap egy fiók biztonsági azonosítóját és egy hitelesítési jogkivonatot. Mindkettő re van szükség a Twilio API-hívások. A fiókjához való jogosulatlan hozzáférés megakadályozása érdekében tartsa biztonságban a hitelesítési jogkivonatot. A fiók SID-je és a hitelesítési token a [Twilio konzolon][twilio_console]tekinthető megtekinthető, a **ACCOUNT SID** és az **AUTH TOKEN**mezőkben.

## <a name="create-a-python-application"></a><a id="create_app"></a>Python-alkalmazás létrehozása
Egy Python-alkalmazás, amely a Twilio-szolgáltatást használja, és az Azure-ban fut, nem különbözik bármely más Python-alkalmazástól, amely a Twilio-szolgáltatást használja. Bár a Twilio-szolgáltatások REST-alapúak, és többféleképpen hívhatók a Pythonból, ez a cikk a Twilio-szolgáltatások és a [Twilio-kódtár használata a GitHubról][twilio_python]való használatára összpontosít. A Twilio-kódtár Pythonhoz való [https://www.twilio.com/docs/libraries/python][twilio_lib_docs]használatáról a.

Először [egy új Azure Linux virtuális gép][azure_vm_setup] az új Python-webalkalmazás gazdagépeként működik. A virtuális gép futása után az alkalmazást az alábbiakban leírtak szerint nyilvános porton kell elérhetővé tenni.

### <a name="add-an-incoming-rule"></a>Bejövő szabály hozzáadása
  1. Nyissa meg a [Network Security Group][azure_nsg] lapot.
  2. Válassza ki a virtuális gépnek megfelelő hálózati biztonsági csoportot.
  3. Add és **kimenő szabály** a **80-as porthoz.** Ügyeljen arra, hogy a bejövő bármely címet.

### <a name="set-the-dns-name-label"></a>A DNS-névcímke beállítása
  1. Nyissa meg a [Nyilvános IP-címek][azure_ips] lapot.
  2. Válassza ki a virtuális gépnek megfelelő nyilvános IP-címet.
  3. Állítsa be a **DNS-névfeliratot** a **Konfiguráció** szakaszban. Ebben a példában ez a *tartomány-címke*.centralus.cloudapp.azure.com

Miután képes csatlakozni ssh a virtuális gép telepítheti a webes keretrendszer az Ön által kiválasztott (a két legismertebb Python, hogy [Flask](http://flask.pocoo.org/) és [Django](https://www.djangoproject.com)). Bármelyiket telepítheti csak a `pip install` parancs futtatásával.

Ne feledje, hogy úgy konfiguráltuk a virtuális gépet, hogy csak a 80-as porton engedélyezze a forgalmat. Ezért győződjön meg róla, hogy konfigurálja az alkalmazást, hogy használja ezt a portot.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Az alkalmazás konfigurálása twilio-tárak használatára
Az alkalmazást kétféleképpen konfigurálhatja a Twilio-kódtár python-hoz való használatára:

* Telepítse a Twilio-kódtár python pip csomagként. A következő parancsokkal telepíthető:
   
        $ pip install twilio

    -VAGY-

* Töltse le a Python Twilio-könyvtárát a GitHubról ([https://github.com/twilio/twilio-python][twilio_python]) és telepítse a következőképpen:

        $ python setup.py install

Miután telepítette a Twilio-könyvtárat `import` a Pythonhoz, ezt a Python-fájlokban teheti meg:

        import twilio

További információ: [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Útmutató: Kimenő hívás
Az alábbiakban bemutatjuk, hogyan lehet kimenő hívást kezdeményezni. Ez a kód is használ egy Twilio által biztosított hely a Twilio Markup Language (TwiML) válasz ad vissza. Helyettesítse az értékeket a **from_number** és **to_number** telefonszámok, és győződjön meg arról, hogy ellenőrizte a **from_number** telefonszámot a Twilio-fiók a kód futtatása előtt.

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

Mint említettük, ez a kód egy Twilio által biztosított hely a TwiML-válasz visszaadásához. Ehelyett használhatja a saját webhelyét a TwiML-válasz megadására; További információt a [TwiML-válaszok saját webhelyről történő nyújtása című témakörben talál.](#howto_provide_twiml_responses)

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Útmutató: SMS küldése
Az alábbiakban bemutatjuk, hogyan `TwilioRestClient` kell SMS-t küldeni az osztály használatával. A **from_number** számot a Twilio adja meg az SMS-üzenetek küldéséhez próba-fiókok számára. A **to_number** számot ellenőrizni kell a Twilio-fiók a kód futtatása előtt.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Hogyan: TwiML válaszok biztosítása a saját webhelyéről
Amikor az alkalmazás kezdeményezegy hívást a Twilio API-t, twilio elküldi a kérést egy URL-címet, amely várhatóan egy TwiML-válasz visszaadása. A fenti példa a Twilio [https://twimlets.com/message][twimlet_message_url]által megadott URL-címet használja. (Bár a TwiML-t a Twilio használja, megtekintheti azt a böngészőben. Ide kattintva [https://twimlets.com/message][twimlet_message_url] például `<Response>` egy üres elem jelenik meg; másik példaként [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] kattintson `<Response>` ide egy `<Say>` elemet tartalmazó elem megtekintéséhez.)

Ahelyett, hogy a Twilio által megadott URL-címre támaszkodna, létrehozhat saját webhelyet, amely HTTP-válaszokat ad vissza. A webhelyet bármilyen nyelven létrehozhatja, amely XML-válaszokat ad vissza; ez a témakör feltételezi, hogy a Python twiML-t fogja használni a TwiML létrehozásához.

A következő példák egy TwiML-választ adnak ki, amely a **hello world-et** mondja a hívásra.

Flaskával:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

A Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Amint az a fenti példából is látható, a TwiML-válasz egyszerűen EGY XML-dokumentum. A Python Twilio-könyvtára olyan osztályokat tartalmaz, amelyek twiML-t hoznak létre. Az alábbi példa a fenti módon hozza `twiml` létre az egyenértékű választ, de a Modult használja a Twilio-kódtárban a Pythonhoz:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

A TwiML-ről további [https://www.twilio.com/docs/api/twiml][twiml_reference]információt a .

Miután beállította a Python-alkalmazást a TwiML-válaszok megadására, használja az `client.calls.create` alkalmazás URL-címét a metódusnak átadott URL-címként. Ha például egy **MyTwiML** nevű webalkalmazás telepítve van egy Azure-üzemeltetett szolgáltatásban, az URL-címét webhookként használhatja, ahogy az a következő példában látható:

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

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Útmutató: További Twilio-szolgáltatások használata
Az itt bemutatott példák mellett a Twilio webalapú API-kat is kínál, amelyek segítségével további Twilio-funkciókat használhat az Azure-alkalmazásból. A részletekért tekintse meg a [Twilio API dokumentációját.][twilio_api]

## <a name="next-steps"></a><a id="NextSteps"></a>Következő lépések
Most, hogy megtanulta a Twilio szolgáltatás alapjait, kövesse az alábbi linkeket, hogy többet megtudjon:

* [A Twilio biztonsági irányelvei][twilio_security_guidelines]
* [Twilio HowTo útmutatók és példakód][twilio_howtos]
* [Twilio rövid útmutatók][twilio_quickstarts]
* [Twilio a GitHubon][twilio_on_github]
* [Beszéljen a Twilio ügyfélszolgálatával][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
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
