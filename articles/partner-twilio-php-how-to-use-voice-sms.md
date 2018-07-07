---
title: Twilio használata Hanghívási és SMS (PHP) |} A Microsoft Docs
description: Útmutató a telefonhívás, és a Twilio API-szolgáltatással egy SMS-üzenet küldése az Azure-ban. A PHP nyelven írt kódmintákat.
documentationcenter: php
services: ''
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
ms.openlocfilehash: 27df7d306b55b7280c871d4638dc34c8fcd33acb
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903665"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Hogyan lehet a Twilio használata Hanghívási és SMS-funkciókhoz php
Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat, a Twilio API-t szolgáltatással az Azure-ban. Az ismertetett forgatókönyvek között megtalálható, így a telefonhívás, és a egy rövid üzenetet szolgáltatást (SMS) üzenetet küld. A Twilio és az alkalmazások használata hanghívási és SMS további információkért lásd: a [lépések](#NextSteps) szakaszban.

## <a id="WhatIs"></a>Mit jelent a Twilio?
A Twilio jövőjének vállalati kommunikáció, így a fejlesztők hang, a VoIP és a üzenetkezelési alkalmazásokba történő beágyazása van. A Twilio API kommunikációs platformon keresztül adatokhoz hozzáférést biztosító felhőalapú, a globális környezetben szükséges összes infrastruktúra virtualizálása azokat. Alkalmazásokat hozhat létre egyszerű és méretezhető. Használja ki a rugalmasan használatalapú – mint-akkor lépjen a díjszabás, és kihasználhatják a felhő megbízhatóságát.

**A twilio használata Hanghívási** lehetővé teszi, hogy telefonhívásokat fogadja, és az alkalmazások. **A Twilio SMS** lehetővé teszi, hogy az alkalmazás szöveges üzeneteket küldjön és fogadjon. **A Twilio-ügyfél** lehetővé teszi bármely telefonon, táblagépen vagy böngészőben VoIP hívásait, és támogatja a WebRTC.

## <a id="Pricing"></a>A Twilio-díjszabás és ajánlatok küldésére
Az Azure-ügyfelek kap egy [a különleges ajánlat](http://www.twilio.com/azure): díjtalan 10 USD kredit Twilio, a Twilio-fiók frissítésekor. A Twilio-Kredit bármely Twilio-használati (10 USD kredit egyenértékű, akár 1000 SMS-üzenetek küldése vagy fogadása legfeljebb 1000 bejövő hangalapú perc, a telefon száma és az üzenet vagy hívás cél helyétől függően) is alkalmazható. A Twilio-kredit beváltása és első lépések: [ http://ahoy.twilio.com/azure ](http://ahoy.twilio.com/azure).

A Twilio szolgáltatás használatalapú fizetést biztosító. Nincsenek díjak sincsenek beállítás, és a fiókot bármikor bezárhatja. További információt talál [Twilio díjszabás][twilio_pricing].

## <a id="Concepts"></a>Fogalmak
A Twilio API egy RESTful API-t hanghívási és SMS-funkciókat biztosító alkalmazások. Ügyfélkódtárai több nyelven érhetők el egy listát lásd: [Twilio API függvénytárai][twilio_libraries].

Fontos szempontjai a Twilio API-t a, Twilio-műveletek és a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>A Twilio-műveletek
Az API-t felhasznál Twilio műveleteket; Ha például a **&lt;Say&gt;** művelet arra utasítja a Twilio hallhatóan kézbesíteni az üzenetet hívás.

Az alábbiakban látható egy Twilio-műveletek listájához. Ismerje meg az egyéb műveletek és funkciókat [Twilio Markup Language dokumentáció](http://www.twilio.com/docs/api/twiml).

* **&lt;Tárcsázás&gt;**: a hívó kapcsolódik egy másik telefonszámot.
* **&lt;Gyűjtse össze&gt;**: összegyűjti a telefon billentyűzeten beírt számjegyeket.
* **&lt;Vonalbontás&gt;**: ér véget a hívást.
* **&lt;Play&gt;**: fájlból játszik le egy hangfájlt.
* **&lt;Felfüggesztés&gt;**: Csendes megvárja a megadott számú másodperc.
* **&lt;Rekord&gt;**: a hívó hangalapú rögzíti, és a felvétel tartalmazó fájl URL-címet adja vissza.
* **&lt;Átirányítási&gt;**: átadja a vezérlést a hívást vagy SMS a TwiML egy másik URL-címen.
* **&lt;Elutasítás&gt;**: a Twilio-számra egy bejövő hívás elutasítása a számlázási, nélkül
* **&lt;Tegyük fel, hogy&gt;**: alakíthatók át egymásba szöveg-beszéd átalakítás, amely a hívást.
* **&lt;SMS&gt;**: SMS üzenetet küld.

### <a id="TwiML"></a>TwiML
TwiML XML-alapú utasítások alapján a Twilio-műveleteket tartalmazó tájékoztatja a hívás feldolgozása Twilio vagy SMS.

Tegyük fel, a következő TwiML konvertálná szöveg **Hello World** át.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Amikor az alkalmazás meghívja a Twilio API-t, az API-paraméterek egyik az URL-cím, amely a TwiML választ ad vissza. Fejlesztési célokra a Twilio által megadott URL-címek segítségével az alkalmazások által használt TwiML visszajelzést. Akkor is elhelyezheti, a saját URL-címek a TwiML válaszok előállításához, és a egy másik lehetőség a **TwiMLResponse** objektum.

A Twilio műveletek, az attribútumokat és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [Twilio API-t][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Amikor készen áll a Twilio-fiók létrehozása, regisztráció: [próbálja meg Twilio][try_twilio]. Kezdje egy ingyenes fiókot, és később frissítse a fiókját.

Amikor regisztrál egy Twilio-fiók, kap egy fiók Azonosítóját és a egy hitelesítési tokent. Mindkét lesz szükség a Twilio API-hívásokat. Fiókja a jogosulatlan hozzáférés megakadályozása érdekében, hogy a hitelesítési jogkivonat biztonságának megőrzése. A fiók Azonosítóját és a hitelesítési jogkivonat teljesítményobjektumok, a [Twilio-fiók oldalán][twilio_account], a címkével ellátott mezőket **fiók SID** és **hitelesítési JOGKIVONATÁT**, illetve.

## <a id="create_app"></a>PHP-alkalmazás létrehozása
PHP-alkalmazás, amely a Twilio szolgáltatás használja, és az Azure-ban fut-e semmiben nem különbözik bármely más PHP-alkalmazások a Twilio-szolgáltatást használ. Bár a Twilio-szolgáltatások REST-alapú, és nem hívható meg a PHP számos módon, ez a cikk a Twilio-szolgáltatások használata összpontosít [Twilio kódtára a PHP, a Githubról][twilio_php]. A php-hez a Twilio-könyvtár használatával kapcsolatos további információkért lásd: [ http://readthedocs.org/docs/twilio-php/en/latest/index.html ] [ twilio_lib_docs].

Részletes utasításokat és az Azure-bA Twilio/PHP-alkalmazások telepítése esetén érhető el [hogyan végezze el az Azure-ban PHP-alkalmazások a telefonhívás használatával Twilio][howto_phonecall_php].

## <a id="configure_app"></a>Az alkalmazás használhatja a Twilio szalagtárak konfigurálása
Az alkalmazás a Twilio-könyvtár használata php-hez készült kétféleképpen konfigurálhatja:

1. A Twilio-kódtár letöltése a Githubról PHP-hez ([https://github.com/twilio/twilio-php][twilio_php]), és adja hozzá a **szolgáltatások** könyvtárat az alkalmazás.
   
    -VAGY-
2. A Twilio-tár telepítse a php-hez KÖRTE csomagként. Az alábbi parancsokkal telepíthető:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Miután telepítette a Twilio-könyvtár a php-hez, majd hozzáadhat egy **require_once** nyilatkozat tetején található a PHP-fájlok való hivatkozáshoz a tár:

        require_once 'Services/Twilio.php';

További információkért lásd: [ https://github.com/twilio/twilio-php/blob/master/README.md ] [ twilio_github_readme].

## <a id="howto_make_call"></a>Hogyan: végezhet
A következőket mutatja be, hogy egy kimenő hívás használatával a **Services_Twilio** osztály. Ez a kód is egy Twilio által biztosított helyet használ a Twilio Markup Language (TwiML) választ adja vissza. Helyettesítse be a saját értékeit a **a** és **való** telefonszámai, és győződjön meg arról, hogy ellenőrizze a **a** telefonszám a Twilio-fiók, a kód futtatása előtt.

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

Ahogy már említettük, ez a kód egy Twilio által biztosított hely használatával TwiML a választ adja. Ehelyett használhatja a saját hely adja meg a TwiML válasz; További információkért lásd: [hogyan TwiML válaszokat biztosít a saját webhelyről](#howto_provide_twiml_responses).

* **Megjegyzés:**: SSL-tanúsítvány érvényesítési hibák elhárításához lásd: [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Útmutató: az SMS-üzenet küldése
A következő példa használatával SMS üzenet küldése a **Services_Twilio** osztály. A **a** száma az SMS-üzenetek küldéséhez a próbafiókokon Twilio által biztosított. A **való** száma a Twilio-fiók, a kód futtatása előtt ellenőrizni kell.

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
Amikor az alkalmazás a Twilio API-hívást kezdeményez, Twilio, küldjön TwiML választ vár egy URL-címet a kérelmet küld. A fenti példában a Twilio-megadott URL-címet használ [ http://twimlets.com/message ] [ twimlet_message_url]. (Amíg TwiML használatra szolgál a Twilio, megtekintheti az informatikai a böngészőben. Kattintson például [ http://twimlets.com/message ] [ twimlet_message_url] megtekintéséhez egy üres `<Response>` elem; másik példaként kattintson [ http://twimlets.com/message?Message%5B0%5D=Hello%20World ] [ twimlet_message_url_hello_world]megtekintéséhez egy `<Response>` elem, amely tartalmazza a `<Say>` elem.)

Így nem kell hagyatkoznia a Twilio-megadott URL-címet, a saját hely által visszaadott HTTP-válaszok is létrehozhat. A hely bármilyen nyelven, amely visszaadja az XML-válaszok; hozhat létre Ez a témakör azt feltételezi, hogy fogja használni a PHP a TwiML létrehozásához.

A következő PHP oldal arról, hogy a TwiML választ eredményez **Hello World** hívásakor.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Ahogy a fenti példában látható, a TwiML válasz egyszerűen egy XML-dokumentumot. A PHP Twilio-könyvtárban osztályokat, amelyek az Ön számára TwiML hoz létre. Az alábbi példa hoz létre a megfelelő választ, ahogy fent látható, de használ a **szolgáltatások\_Twilio\_Twiml** osztály a Twilio-könyvtárban a php-hez:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

TwiML kapcsolatos további információkért lásd: [ https://www.twilio.com/docs/api/twiml ] [ twiml_reference]. 

Miután a PHP-oldalt állított TwiML válaszokat nyújt, használja az URL-címét a PHP-oldalt átadott URL-CÍMÉT a `Services_Twilio->account->calls->create` metódust. Például, ha rendelkezik egy nevű webalkalmazást **MyTwiML** üzembe helyezett egy Azure-ban üzemeltetett szolgáltatást, és a PHP-oldalt neve **mytwiml.php**, az URL-cím adható át **Services_Twilio -> fiók -> hívások -> hozzon létre** az alábbi példában látható módon:

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

Az Azure-ban PHP Twilio használatával kapcsolatos további információkért lásd: [hogyan végezze el az Azure-ban PHP-alkalmazások a telefonhívás használatával Twilio][howto_phonecall_php].

## <a id="AdditionalServices"></a>How to: további Twilio-szolgáltatások használata
Itt látható példák, mellett Twilio kínál a webes API-kat használhatja az Azure-alkalmazásból további Twilio funkciói kihasználhatók. További részletek: a [Twilio API-dokumentáció][twilio_api_documentation].

## <a id="NextSteps"></a>Következő lépések
Most, hogy megismerte a Twilio szolgáltatás alapjait, kövesse az alábbi hivatkozások további:

* [A Twilio szolgáltatásra vonatkozó biztonsági irányelvek][twilio_security_guidelines]
* [A Twilio útmutatók és példakód][twilio_howtos]
* [A Twilio-Gyorsútmutatók][twilio_quickstarts] 
* [Twilio a Githubon][twilio_on_github]
* [Beszéljen a Twilio-támogatás][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
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
