---
title: Hogyan telefonhívás a Twilio (PHP) |} Microsoft Docs
description: Útmutató a telefonhívás, és a Twilio API szolgáltatás SMS üzenet küldése az Azure-on. A mintákat a PHP-alkalmazás.
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
ms.openlocfilehash: 9866a196b3be10548d7a431430e570b41c190fc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865756"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Hogyan telefonhívás Twilio használatát a PHP-alkalmazások az Azure-on
A következő példa bemutatja, hogyan használható fel a Twilio weblapról PHP Azure-ban üzemeltetett hívást. Az eredményül kapott alkalmazás fogja kérni a felhasználót a telefonhívás-értékeket, az az alábbi képernyőfelvételen látható módon.

![Az Azure hívás űrlap Twilio és a PHP használatával][twilio_php]

A következő használatára az ebben a témakörben lévő lesz szüksége:

1. Szerezzen be egy Twilio-fiókja és a hitelesítési jogkivonat a a [Twilio-konzol][twilio_console]. Twilio megkezdéséhez, értékelje ki a következő árképzési [http://www.twilio.com/pricing][twilio_pricing]. Regisztrálhat a próbafiókra: [https://www.twilio.com/try-twilio][try_twilio].
2. Szerezze be a [Twilio-könyvtár a PHP](https://github.com/twilio/twilio-php) vagy KÖRTEFÁK csomag telepítéséhez. További információkért lásd: a [információs fájl](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Telepítse a PHP az Azure SDK-t. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>A következő hívással webes űrlap létrehozása
A következő HTML kód bemutatja, hogyan hozhat létre weblapot (**callform.html**), amely lekérdezi a felhasználó adatai egy hívás:

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
A következő kód bemutatja, hogyan hozhat létre **makecall.php**, amely más néven, amikor a felhasználó ad meg az űrlap által megjelenített **callform.html**. Az alábbi kódot a hívás-üzenetet hoz létre, és állít elő, a hívást. Is, ügyeljen arra, hogy a Twilio-fiókja és -hitelesítés használata a jogkivonat a [Twilio-konzol] [ twilio_console] helyett a helyőrző értékeket rendelt **$sid** és  **$token** az alábbi kódot.

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
            array('url' => http://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Azonkívül, hogy a hívás **makecall.php** megjeleníti az egyes hívás metaadatait, az alábbi képen látható módon. Hívás metaadatok kapcsolatos további információkért lásd: [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Azure hívási válasz Twilio és a PHP használatával][twilio_php_response]

## <a name="run-the-application"></a>Az alkalmazás futtatása
A következő lépés [Azure Web Apps a Git az alkalmazás telepítése](app-service/app-service-web-get-started-php.md) (bár nem minden az adatokat a megfelelő). 

## <a name="next-steps"></a>Következő lépések
Ez a kód Twilio használata Azure PHP alapvető funkciókat mutatjuk be lett megadva. Mielőtt telepítené az Azure éles környezetben, érdemes lehet további hibakezelés vagy egyéb szolgáltatások. Példa:

* Egy webes űrlap használata helyett segítségével az Azure storage blobsba vagy SQL-adatbázis tárolja a telefonszámokat, és hívja meg a szöveg. További információ az Azure storage blobs használata a PHP: [Azure Storage használata PHP-alkalmazások][howto_blob_storage_php]. SQL-adatbázis a PHP használatával kapcsolatos információkért lásd: [SQL-adatbázisok használata PHP-alkalmazások][howto_sql_azure_php].
* A **makecall.php** használ a Twilio-megadott URL-címet ([http://twimlets.com/message][twimlet_message_url]), amely arról értesíti a Twilio hogyan Twilio Markup Language (TwiML) választ a a hívás folytatásához. A visszaadott TwiML tartalmazhat például egy `<Say>` művelet, amely a hívás címzett felolvasását szöveg eredményez. Ahelyett, hogy a Twilio-megadott URL-címet, a saját Twilio tartozó kérelem; szolgáltatás létrehozása sikerült További információkért lásd: [hang-és a PHP SMS képességek használatát Twilio hogyan][howto_twilio_voice_sms_php]. További információ a TwiML található [http://www.twilio.com/docs/api/twiml][twiml], és további információ a `<Say>` és egyéb Twilio művelet található a [http:// www.twilio.com/docs/API/twiml/Say][twilio_say].
* Olvassa el a Twilio biztonsági irányelvek a [https://www.twilio.com/docs/security][twilio_docs_security].

További információ a Twilio: [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Lásd még:
* [Hang-és a PHP SMS lehetővé tevő szolgáltatásaival Twilio használata](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
