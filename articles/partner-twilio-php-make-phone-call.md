---
title: A Twilio (PHP) biztosítása a telefonhívás |} A Microsoft Docs
description: Útmutató a telefonhívás, és a Twilio API-szolgáltatással egy SMS-üzenet küldése az Azure-ban. Minta PHP-alkalmazás vonatkoznak.
documentationcenter: php
services: ''
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 03b74f5a931e1cfbf09433af76c250607b7fc80c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444438"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Hogyan lehet az Azure-ban PHP-alkalmazások a Twilio használatával telefonhívás
Az alábbi példa bemutatja, hogyan használhatja a Twilio segítségével egy Azure-ban üzemeltetett PHP weboldalról hívást. Az eredményül kapott alkalmazás fogja kérni a felhasználót, telefonhívás-értékek, az alábbi képernyőképen látható módon.

![Az Azure hívás űrlap Twilio és a PHP használatával][twilio_php]

Ez a témakör a programkód használatával a következőket kell:

1. Szerezzen egy Twilio-fiók és egy hitelesítési tokent a [Twilio konzol][twilio_console]. Első lépések a Twilio, kiértékelheti a díjszabás [ https://www.twilio.com/pricing ] [ twilio_pricing]. A egy próbaverziós fiókot regisztrálhatnak [ https://www.twilio.com/try-twilio ] [ try_twilio].
2. Szerezze be a [Twilio kódtára a PHP](https://github.com/twilio/twilio-php) vagy KÖRTE csomagként telepítheti. További információkért lásd: a [információs fájl](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Telepítse az Azure SDK a php-hez. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Hozzon létre egy webes űrlap egy hívás
Az alábbi HTML-kód bemutatja, hogyan hozhat létre weblapot (**callform.html**), hogy a hívás felhasználói adatokat:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>A kód a híváshoz létrehozása
A következő kód bemutatja, hogyan hozhat létre **makecall.php**, amikor a felhasználó ad az űrlap által megjelenített nevezett **callform.html**. Az alábbi kódot a hívás üzenetet hoz létre, és állít elő, a hívást. Is, ügyeljen arra, használja a Twilio-fiók és a hitelesítési tokent a [Twilio konzol] [ twilio_console] helyett a helyőrző értékeket rendelt **$sid** és  **$token** az alábbi kódot.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Amellett, hogy a hívás **makecall.php** néhány hívás metaadatokat jeleníti meg, ahogy az alábbi képen látható. Hívás metaadatok kapcsolatos további információkért lásd: [ https://www.twilio.com/docs/api/rest/call#instance-properties ] [ twilio_call_properties].

![Az Azure hívás válasz Twilio és a PHP használatával][twilio_php_response]

## <a name="run-the-application"></a>Az alkalmazás futtatása
A következő lépés [az alkalmazás telepítése az Azure Web Apps, a Git](app-service/app-service-web-get-started-php.md) (bár nem minden az adatokat a nem megfelelő). 

## <a name="next-steps"></a>További lépések
Ez a kód mutatni, alapvető funkció a Twilio használatával az Azure-ban php lett megadva. Mielőtt üzembe helyezni az Azure éles környezetben, érdemes lehet további hibakezelés vagy más szolgáltatások hozzáadása. Példa:

* Egy webes űrlap használata helyett használhatja az Azure storage blobból vagy az SQL Database telefonszámok tárolására, és hívja a szöveget. További információ az Azure storage blobs használata php: [Azure Storage használata PHP-alkalmazások a][howto_blob_storage_php]. A PHP az SQL Database használatával kapcsolatos információkért lásd: [az SQL Database használata PHP-alkalmazások][howto_sql_azure_php].
* A **makecall.php** használ a Twilio-megadott URL-címet ([https://twimlets.com/message][twimlet_message_url]), amely tájékoztatja a Twilio való Twilio Markup Language (TwiML) választ a hívás. Ha például a visszaadott TwiML tartalmazhat egy `<Say>` művelet, amely folyamatban van a hívott a kimondott szöveget eredményez. A Twilio által megadott URL-cím helyett is létrehozhatja a saját szolgáltatás Twilio a kérelem; További információkért lásd: [a Twilio használata Hanghívási és SMS-funkciókhoz, a PHP hogyan][howto_twilio_voice_sms_php]. További információ a TwiML található [ https://www.twilio.com/docs/api/twiml ] [ twiml], és további információ a `<Say>` és egyéb Twilio művelet található a [ https://www.twilio.com/docs/api/twiml/say ][twilio_say].
* Olvassa el a Twilio biztonsági irányelveknek, [ https://www.twilio.com/docs/security ] [ twilio_docs_security].

A Twilio kapcsolatos további információkért lásd: [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="see-also"></a>Lásd még:
* [Hogyan lehet a Twilio használata Hanghívási és SMS-funkciókhoz php](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: https://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: https://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
