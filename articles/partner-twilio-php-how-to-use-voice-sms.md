---
title: Hogyan használjuk a Twilio-t hang- és SMS-hez (PHP) | Microsoft dokumentumok
description: Ismerje meg, hogyan kezdeményezhetsz telefonhívást, és hogyan küldhet SMS-üzenetet az Azure-beli Twilio API-szolgáltatással. PHP-ben írt kódminták.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: f9fb250109a1c9000eae8da0d6337c96f19f0f89
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410539"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Hogyan használjuk a Twilio-t hang- és SMS-képességekhez a PHP-ben
Ez az útmutató bemutatja, hogyan hajthat végre közös programozási feladatokat az Azure-beli Twilio API-szolgáltatással. A tárgyalt forgatókönyvek közé tartozik a telefonhívás és a rövid üzenetszolgáltatás (SMS) küldése. A Twilio használatával, valamint a hang- és SMS-használattal kapcsolatos további információkért tekintse meg a [Következő lépések](#NextSteps) című szakaszt.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Mi az a Twilio?
A Twilio az üzleti kommunikáció jövőjét működteti, lehetővé téve a fejlesztők számára, hogy hang- és VoIP-üzeneteket ágyaznak be az alkalmazásokba. Virtualizálják az összes szükséges infrastruktúrát egy felhőalapú, globális környezetben, és a Twilio communications API platformon keresztül teszik ki. Az alkalmazások egyszerűen elkészíthetők és méretezhetők. A díjfizetésdíj-fizetéssel rugalmasan vehet igénybe, és élvezheti a felhő megbízhatóságának előnyeit.

**A Twilio Voice** lehetővé teszi, hogy az alkalmazások telefonhívásokat kezdeményezzenek és fogadjanak. **A Twilio SMS** lehetővé teszi, hogy az alkalmazás szöveges üzeneteket küldjön és fogadjon. **Twilio ügyfél** lehetővé teszi, hogy voIP hívásokat bármilyen telefonról, táblagépről vagy böngészőből, és támogatja a WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio árképzés és különleges ajánlatok
Az Azure-ügyfelek [különleges ajánlatot](https://www.twilio.com/azure)kapnak: a Twilio-fiók frissítésekén $10 ingyenes 10 usd Twilio-kreditet. Ez a Twilio-kredit bármely Twilio-használatra alkalmazható ($10 kredit egyenértékű 1000 SMS-üzenet küldésével vagy akár 1000 bejövő hangperc fogadásával, a telefonszám és az üzenet vagy a hívás céljának helyétől függően). Váltsa be ezt a Twilio-jóváírást, és kezdje el a következő helyen: [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure).

A Twilio egy felosztó-kiosztó szolgáltatás. Nincsenek beállítási díjak, és bármikor megszüntetheti fiókját. További részleteket a [Twilio Pricing][twilio_pricing]oldalon talál.

## <a name="concepts"></a><a id="Concepts"></a>Alapelvek
A Twilio API egy RESTful API, amely hang- és SMS-funkciókat biztosít az alkalmazások számára. Az ügyféltárak több nyelven is elérhetők; a listát a [Twilio API Libraries című témakörben található.][twilio_libraries]

A Twilio API fő szempontjai a Twilio-műveletek és a Twilio markup nyelv (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio igék
Az API Twilio-igéket használ; A ** &lt;Say&gt; ** ige például arra utasítja a Twilio-t, hogy hallhatóan kézbesítsen egy üzenetet egy hívásra.

Az alábbi lista a Twilio-igéket tartalmazza. Ismerje meg a többi igét és képességet a [Twilio Markup Language dokumentációban](https://www.twilio.com/docs/api/twiml)keresztül.

* **Tárcsázás&gt;: A hívót egy másik telefonhoz csatlakoztatja. &lt;**
* Gather : A telefon billentyűzetén megadott numerikus számjegyeket gyűjti. ** &lt;&gt;**
* **Hangup&gt;: Hívás &lt;** befejezése.
* Lejátszás : Hangfájl lejátszása. ** &lt;&gt;**
* Szünet : A megadott számú másodpercig csendben várakozik. ** &lt;&gt;**
* Rekord : Rögzíti a hívó hangját, és visszaadja a felvételt tartalmazó fájl URL-címét. ** &lt;&gt;**
* **Átirányítás&gt;: Egy hívás vagy SMS vezérlésének átvitele a TwiML-re egy másik URL-címen. &lt;**
* Elutasítás : Elutasítja a Bejövő hívást a Twilio-számra anélkül, hogy kiszámlázna ** &lt;&gt;**
* **Kimondás&gt;: A hívás közben készített szöveget beszédmé alakítja. &lt;**
* Sms : SMS-üzenetet küld. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML
A TwiML a Twilio-műveleteken alapuló XML-alapú utasítások készlete, amelyek tájékoztatják a Twilio-t a hívás vagy SMS feldolgozásáról.

Például a következő TwiML a **Hello World** szöveget beszédmé alakítja.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Amikor az alkalmazás meghívja a Twilio API-t, az API-paraméterek egyike az URL-címet, amely a TwiML-választ adja vissza. Fejlesztési célokra használhatja a Twilio által biztosított URL-címeket az alkalmazások által használt TwiML-válaszok biztosításához. A TwiML-válaszok létrehozásához saját URL-eket is üzemeltethet, egy másik lehetőség pedig a **TwiMLResponse** objektum használata.

A Twilio-műveletekről, azok attribútumairól és a TwiML-ről a [TwiML][twiml]című témakörben talál további információt. A Twilio API-ról további információt a [Twilio API című témakörben][twilio_api]talál.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll a Twilio-fiók leválasztására, regisztráljon a [Try Twilio][try_twilio]című naphoz. Kezdheti egy ingyenes fiókkal, és később frissítheti fiókját.

Amikor regisztrál egy Twilio-fiókot, kap egy fiókazonosítót és egy hitelesítési jogkivonatot. Mindkettő re van szükség a Twilio API-hívások. A fiókjához való jogosulatlan hozzáférés megakadályozása érdekében tartsa biztonságban a hitelesítési jogkivonatot. A fiókazonosító és a hitelesítési jogkivonat a [Twilio-fiók lapon][twilio_account]tekinthető meg, a **ACCOUNT SID,** illetve az **AUTH TOKEN**mezőkben.

## <a name="create-a-php-application"></a><a id="create_app"></a>PHP alkalmazás létrehozása
A Twilio-szolgáltatást használó és az Azure-ban futó PHP-alkalmazás nem különbözik bármely más, a Twilio szolgáltatást használó PHP-alkalmazástól. Bár a Twilio-szolgáltatások REST-alapúak, és többféleképpen hívhatók a PHP-ből, ez a cikk a Twilio-szolgáltatások és a [Twilio-könyvtár használatával foglalkozik a GitHubról.][twilio_php] A Twilio-könyvtár PHP-hez való [https://www.twilio.com/docs/libraries/php][twilio_lib_docs]használatáról további információt a.

A Twilio/PHP alkalmazás Azure-beli létrehozásához és üzembe helyezéséhez részletes utasítások a [Twilio használatával történő telefonhívás lehívása az Azure-beli PHP-alkalmazásban][howto_phonecall_php]találhatók.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Az alkalmazás konfigurálása twilio-tárak használatára
Beállíthatja, hogy az alkalmazás a Twilio könyvtár php kétféleképpen:

1. Töltse le a Twilio-könyvtárat[https://github.com/twilio/twilio-php][twilio_php]a Php-ről a GitHubról ( ), és adja hozzá a **Services** könyvtárat az alkalmazáshoz.
   
    -VAGY-
2. Telepítse a Twilio könyvtár PHP pear csomagként. A következő parancsokkal telepíthető:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Miután telepítette a Php Twilio könyvtárát, hozzáadhat egy **require_once** nyilatkozatot a PHP fájlok tetején, hogy hivatkozzon a könyvtárra:

        require_once 'Services/Twilio.php';

További információ: [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Útmutató: Kimenő hívás
Az alábbiakban bemutatjuk, hogyan kezdeményezz kimenő hívást a **Services_Twilio** osztály használatával. Ez a kód is használ egy Twilio által biztosított hely a Twilio Markup Language (TwiML) válasz ad vissza. Helyettesítse az értékeket a **be-** és a **telefonszámokkal,** és győződjön meg arról, hogy a kód futtatása előtt ellenőrizze a Twilio-fiók **From** phone number-t.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "https://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Mint említettük, ez a kód egy Twilio által biztosított hely a TwiML-válasz visszaadásához. Ehelyett használhatja a saját webhelyét a TwiML-válasz megadására; További információt a [TwiML-válaszok saját webhelyről történő nyújtása című témakörben talál.](#howto_provide_twiml_responses)

* **Megjegyzés:** A TLS/SSL tanúsítványérvényesítési hibák elhárításához lásd:[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Útmutató: SMS küldése
Az alábbiakban bemutatjuk, hogyan küldhet SMS-üzenetet a **Services_Twilio** osztály használatával. A **Feladó** számot a Twilio biztosítja az SMS-üzenetek küldéséhez próbafiókok számára. A **to** szám ellenőrizni kell a Twilio-fiók a kód futtatása előtt.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Hogyan: TwiML válaszok biztosítása a saját webhelyéről
Amikor az alkalmazás kezdeményezegy hívást a Twilio API-t, twilio elküldi a kérést egy URL-címet, amely várhatóan egy TwiML-válasz visszaadása. A fenti példa a Twilio [https://twimlets.com/message][twimlet_message_url]által megadott URL-címet használja. (Bár a TwiML-t a Twilio használja, megtekintheti a böngészőben. Ide kattintva [https://twimlets.com/message][twimlet_message_url] például `<Response>` egy üres elem jelenik meg; másik példaként [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] kattintson `<Response>` ide egy `<Say>` elemet tartalmazó elem megtekintéséhez.)

Ahelyett, hogy a Twilio által megadott URL-címre támaszkodna, létrehozhat saját webhelyet, amely HTTP-válaszokat ad vissza. A webhelyet bármilyen nyelven létrehozhatja, amely XML-válaszokat ad vissza; ez a témakör feltételezi, hogy a PHP-t fogod használni a TwiML létrehozásához.

A következő PHP oldal egy TwiML választ eredményez, amely azt **mondja,** hogy hello world a hívás.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Amint az a fenti példából is látható, a TwiML-válasz egyszerűen EGY XML-dokumentum. A PHP Twilio könyvtára olyan osztályokat tartalmaz, amelyek TwiML-t hoznak létre. Az alábbi példa a fenti módon adja meg az egyenértékű választ, de a Php Twilio könyvtárában található **Twilio\_\_Twiml-osztályt** használja:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

A TwiML-ről további [https://www.twilio.com/docs/api/twiml][twiml_reference]információt a . 

Miután beállította a PHP oldalt, hogy TwiML válaszokat adjon, használja a `Services_Twilio->account->calls->create` PHP oldal URL-jét a metódusnak átadott URL-ként. Ha például egy **MyTwiML** nevű webalkalmazás telepítve van egy Azure-üzemeltetett szolgáltatásban, és a PHP lap neve **mytwiml.php,** az URL-címet át lehet adni **a Services_Twilio->fiók->>hozzon létre** az alábbi példában látható módon:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

A Twilio azure-beli PHP-ben való használatáról a [Twilio használatával a Twilio használatával az Azure-ban][howto_phonecall_php]című témakörben talál további információt.

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Útmutató: További Twilio-szolgáltatások használata
Az itt bemutatott példák mellett a Twilio webalapú API-kat is kínál, amelyek segítségével további Twilio-funkciókat használhat az Azure-alkalmazásból. A részletekért tekintse meg a [Twilio API dokumentációját.][twilio_api_documentation]

## <a name="next-steps"></a><a id="NextSteps"></a>Következő lépések
Most, hogy megtanulta a Twilio szolgáltatás alapjait, kövesse az alábbi linkeket, hogy többet megtudjon:

* [A Twilio biztonsági irányelvei][twilio_security_guidelines]
* [Twilio HowTo és példakód][twilio_howtos]
* [Twilio rövid útmutatók][twilio_quickstarts] 
* [Twilio a GitHubon][twilio_on_github]
* [Beszéljen a Twilio ügyfélszolgálatával][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
