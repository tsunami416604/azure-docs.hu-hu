---
title: "Hang-és SMS (PHP) Twilio használata |} Microsoft Docs"
description: "Útmutató a telefonhívás, és a Twilio API szolgáltatás SMS üzenet küldése az Azure-on. Kódminták PHP."
documentationcenter: php
services: 
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: bd50eac7390e8639f77894689388e6926cdb619c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Hang-és a PHP SMS lehetővé tevő szolgáltatásaival Twilio használata
Ez az útmutató bemutatja, hogyan Azure a Twilio API szolgáltatás közös programozási feladatok elvégzéséhez. A tárgyalt forgatókönyvekben szerepel, így a telefonhívás, és egy rövid üzenetet szolgáltatás (SMS) üzenet küldésekor. A Twilio- és hang- és SMS használata az alkalmazások további információkért lásd: a [lépések](#NextSteps) szakasz.

## <a id="WhatIs"></a>Mi az, hogy a Twilio?
Twilio a jövőbeli üzleti kommunikáció, így a fejlesztők hang, a VoIP és a üzenetküldési alkalmazásokba történő beágyazásához van működtetéséhez. Ezek virtualizálása keresztül a Twilio API kommunikációs platform, az ilyen felhőalapú, a globális környezetben szükséges összes infrastruktúrát. Alkalmazások olyan egyszerű hozhat létre és méretezhető. Élvezze a rugalmasságot Önnel fizetési-, nyissa meg árképzési, és igénybe vehesse az felhő megbízhatóság.

**Twilio hang** lehetővé teszi, hogy az alkalmazások és a telefonhívásokat fogadja. **Twilio SMS** lehetővé teszi, hogy az alkalmazás szöveges üzeneteket küldjön és fogadjon. **Twilio-ügyfél** lehetővé teszi a VoIP hívást a telefon, a táblagép vagy a böngésző és WebRTC támogatja.

## <a id="Pricing"></a>Twilio árak és a különleges ajánlatokkal
Az Azure-ügyfelek egy [a különleges ajánlat](http://www.twilio.com/azure): udvarias $10 Twilio-kredit a Twilio-fiók frissítésekor. A Twilio-jóváírási alkalmazhatók minden Twilio-használati (akár 1000 SMS-üzenetek küldésekor vagy fogadásakor 1000 bejövő hang perc, attól függően, hogy a telefon és üzenet vagy hívás cél helye egyenértékűek $10 követel). A Twilio-jóváírási beváltani és első lépések: [http://ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio egy olyan használatalapú szolgáltatás. Nincsenek nem beállításról díjakat, és bármikor bezárhatja a fiókját. További részletei: [Twilio árképzési][twilio_pricing].

## <a id="Concepts"></a>Alapfogalmak
A Twilio API egy RESTful API, hang- és SMS-funkciókat biztosít az alkalmazások számára. Ügyféloldali kódtáraknál érhetők el több nyelven is; az útmutató, [Twilio API függvénytárai][twilio_libraries].

A Twilio API fő szempontjait Twilio-műveletek és Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-műveletek
Az API lehetővé teszi, hogy a Twilio használja műveletek; például a  **&lt;szóbeli&gt;**  parancs utasítja a Twilio hallhatóan hívás az üzenet kézbesítését.

A Twilio-műveletek listáját a következő: További információk az egyéb műveletek és képességek keresztül [Twilio Markup Language dokumentáció](http://www.twilio.com/docs/api/twiml).

* **&lt;Telefonos kapcsolat&gt;**: a hívó kapcsolódik egy másik telefonon.
* **&lt;Gyűjtsön&gt;**: adta meg a telefon billentyűzetén számjegyek gyűjti.
* **&lt;Vonalbontás&gt;**: hívás véget ér.
* **&lt;Lejátszási&gt;**: hangfájl lejátszása.
* **&lt;Felfüggesztés&gt;**: Csendes megvárja-e a megadott számú másodpercnél tovább.
* **&lt;Rekord&gt;**: a hívó hang rögzíti, és a felvétel tartalmazó fájl URL-címet adja vissza.
* **&lt;Átirányítási&gt;**: hívás vagy SMS irányítását átviszi a TwiML egy másik URL-címen.
* **&lt;Elutasítása&gt;**: a Twilio-szám egy bejövő hívás elutasítása a nélkül, számlázási
* **&lt;Tegyük fel például&gt;**: konvertálja szöveg-beszéd átalakítás hívás készült.
* **&lt;SMS&gt;**: SMS üzenetet küld.

### <a id="TwiML"></a>TwiML
TwiML olyan XML-alapú utasítások a Twilio-műveletek, amely tájékoztatja a Twilio hogyan lehet feldolgozni a hívást vagy SMS alapján.

Tegyük fel, a következő TwiML a szöveg volna átalakítása **Hello World** beszéddé.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Ha egy alkalmazás meghívja a Twilio API-t, az API paraméterek egyike az URL-címet, a TwiML választ ad vissza. Fejlesztési célra a Twilio által megadott URL-címek segítségével az alkalmazások által használt TwiML visszajelzést. Akkor is elhelyezheti a TwiML válaszok létrehozásához a saját URL-címeket, és egy másik lehetőség az **TwiMLResponse** objektum.

Twilio műveletek, az attribútumokat és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Amikor készen áll arra, hogy a Twilio-fiókot, feliratkozhat [próbálja Twilio][try_twilio]. Egy ingyenes fiókot kezdődnie, és később frissítse a fiókját.

Amikor regisztrál egy Twilio-fiókja, kapni fog egy Fiókazonosító és egy hitelesítési jogkivonatot. Is szükség lesz Twilio API-hívások indítása. Jogosulatlan hozzáférés elkerülése érdekében a fiókját, hogy a hitelesítési jogkivonat biztonságának megőrzése. A Fiókazonosítót és a hitelesítési jogkivonat megtekinthető a rendszer a [Twilio fióklapját][twilio_account], a mezőket, címkével **fiók SID** és **hitelesítési TOKEN**, illetve.

## <a id="create_app"></a>PHP-alkalmazás létrehozása
A PHP-alkalmazások, hogy a Twilio-szolgáltatás és az Azure-ban fut. ugyanolyan helyzetet teremt, mint bármely más PHP-alkalmazás, hogy a Twilio-szolgáltatást használ. Twilio-szolgáltatások REST-alapú, és a php-ből többféle módon is nevezik, ez a cikk a Twilio-szolgáltatások használatával összpontosítanak [Twilio-könyvtár a PHP a Githubról][twilio_php]. Php-hez tartozó a Twilio-könyvtár használatával kapcsolatos további információkért lásd: [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Részletes útmutatást létrehozása és telepítése az Azure-bA Twilio/PHP-alkalmazások érhetők el [hogyan végezheti el a telefonhívás használatával Twilio a PHP-alkalmazások Azure][howto_phonecall_php].

## <a id="configure_app"></a>Állítsa be az alkalmazását Twilio-könyvtárak használatára
Az alkalmazás a Twilio könyvtár használata php-hez tartozó két módon konfigurálhatja:

1. Töltse le a Githubról PHP a Twilio-könyvtár ([https://github.com/twilio/twilio-php][twilio_php]), és adja hozzá a **szolgáltatások** könyvtárhoz, hogy az alkalmazást.
   
    -VAGY-
2. Telepítse a PHP a Twilio-könyvtár KÖRTEFÁK csomag. Az alábbi parancsokkal telepíthető:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Miután telepítette a Twilio-könyvtár php, amelyet ezután felvehet egy **require_once** nyilatkozat tetején található a PHP fájlok a könyvtárban hivatkozni:

        require_once 'Services/Twilio.php';

További információkért lásd: [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Hogyan: végezhet
A következő bemutatja, hogyan végezheti el egy kimenő hívás használatával a **Services_Twilio** osztály. Ezt a kódot a Twilio által biztosított hely is használja a Twilio Markup Language (TwiML) válasz vissza. Helyettesítse a saját értékeit a **a** és **való** telefonszámokat, és győződjön meg arról, hogy ellenőrizze a **a** telefonszám a Twilio-fiók a kód futtatása előtt.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

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

Ahogy azt korábban említettük, ez a kód Twilio által biztosított hely használatával a TwiML választ küldi vissza. Helyette a saját webhely segítségével adja meg a TwiML válasz; További információkért lásd: [hogyan TwiML válaszok adja meg a saját webhelyről](#howto_provide_twiml_responses).

* **Megjegyzés:**: SSL-tanúsítvány érvényesítési hibák elhárításához lásd: [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Útmutató: az SMS-üzenet küldése
A következő bemutatja, hogyan használ egy SMS üzenetet küldeni a **Services_Twilio** osztály. A **a** száma az SMS-üzenetek küldéséhez a próba fiókok Twilio biztosítja. A **való** számát a Twilio-fiókját a kód futtatása előtt ellenőrizni kell.

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

## <a id="howto_provide_twiml_responses"></a>Hogyan: Adja meg a saját webhelyén TwiML válaszok
Amikor az alkalmazás a Twilio API hívása kezdeményez, Twilio TwiML választ várt URL-címet a kérést küld. A fenti példában a Twilio-megadott URL-címet [http://twimlets.com/message][twimlet_message_url]. (Közben TwiML a Twilio végzi, megtekintheti az informatikai a böngészőben. Kattintson például [http://twimlets.com/message] [ twimlet_message_url] egy üres megjelenítéséhez `<Response>` elem; másik példaként, kattintson a [http://twimlets.com/message? % 5B0 üzenet: %5 D = Hello % 20World] [ twimlet_message_url_hello_world] megtekintéséhez egy `<Response>` elem, amely tartalmazza a `<Say>` elem.)

Ahelyett, hogy a Twilio által megadott URL-címen, létrehozhat saját webhelyén, amely HTTP-válaszokat ad vissza. A hely bármilyen nyelven, amely visszaadja az XML-válaszok; hozhat létre Ez a témakör azt feltételezi, hogy fogja használni a PHP a TwiML létrehozásához.

A következő lap a PHP eredményez, amely szerint TwiML választ **Hello World** hívásakor.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Ahogy látja, a fenti példa, TwiML a rendszer a választ csak az XML-dokumentum. A PHP Twilio-könyvtárban osztályokkal rendelkezik, amelyek az Ön TwiML hoz létre. Az alábbi példa hozza létre a megfelelő választ, ahogy fent látható, de használja a **szolgáltatások\_Twilio\_Twiml** osztály a PHP Twilio könyvtárában:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

TwiML kapcsolatos további információkért lásd: [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Miután a TwiML visszajelzést beállítva PHP oldal, használja a PHP lap URL-átadott URL-CÍMÉT a `Services_Twilio->account->calls->create` metódust. Ha egy webes alkalmazás neve például **MyTwiML** telepített egy Azure üzemeltetett szolgáltatás, és a PHP-oldal neve **mytwiml.php**, az URL-cím adható át **Services_Twilio -> fiók -> hívások -> hozzon létre** a következő példában látható módon:

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

Az Azure-ban PHP Twilio használatával kapcsolatos további információkért lásd: [hogyan végezheti el a telefonhívás használatával Twilio a PHP-alkalmazások Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Útmutató: további Twilio-szolgáltatásokkal
Az itt bemutatott példák Twilio lehetőséget biztosít webes API-k segítségével kihasználhatja az Azure alkalmazásról további Twilio-funkciókat. Teljes részletekért lásd: a [Twilio API-JÁNAK dokumentációja][twilio_api_documentation].

## <a id="NextSteps"></a>Következő lépések
Most, hogy megismerte a Twilio szolgáltatáshoz alapjait, az alábbi hivatkozásokból további:

* [Twilio biztonsági irányelvek][twilio_security_guidelines]
* [Twilio útmutató és példakódot][twilio_howtos]
* [Twilio gyors üzembe helyezési oktatóanyag][twilio_quickstarts] 
* [A Githubon Twilio][twilio_on_github]
* [Kérdezze meg a Twilio-támogatás][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
