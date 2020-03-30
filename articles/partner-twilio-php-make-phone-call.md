---
title: Hogyan készítsünk egy telefonhívást Twilio (PHP) | Microsoft dokumentumok
description: Ismerje meg, hogyan kezdeményezhetsz telefonhívást, és hogyan küldhet SMS-üzenetet az Azure-beli Twilio API-szolgáltatással. A minták PHP alkalmazáshoz valók.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: fb1623c4a409f1c6cba94bad56d773e166d2b182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637323"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Telefonhívás lehívása a Twilio használatával egy PHP-alkalmazásban az Azure-ban
A következő példa bemutatja, hogyan használhatja a Twilio-t az Azure-ban üzemeltetett PHP-weblapról történő híváshoz. Az eredményül kapott alkalmazás kéri a felhasználót a telefonhívás értékeit, ahogy az a következő képernyőképen látható.

![Azure call form twilio és PHP használatával][twilio_php]

A jelen témakörben szereplő kód használatához az alábbiakat kell tennie:

1. Twilio-fiók és hitelesítési jogkivonat beszerzése a [Twilio konzolról.][twilio_console] A Twilio első lépéseihez [https://www.twilio.com/pricing][twilio_pricing]értékelje ki az árakat a(z) szinten. A próbafiókot a sorban [https://www.twilio.com/try-twilio][try_twilio]regisztrálhatja.
2. Szerezze be a [Php Twilio könyvtárát,](https://github.com/twilio/twilio-php) vagy telepítse PEAR csomagként. További információt a [readme fájlban talál.](https://github.com/twilio/twilio-php/blob/master/README.md)
3. Telepítse az Azure SDK PHP.Install the Azure SDK for PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Webes űrlap létrehozása híváshoz
A következő HTML-kód bemutatja, hogyan lehet olyan weblapot létrehozni (**callform.html**), amely lekéri a felhasználói adatokat a híváshoz:

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

## <a name="create-the-code-to-make-the-call"></a>A hívás hozása a kódhoz
A következő kód bemutatja, hogyan kell építeni **makecall.php**, amely a neve, amikor a felhasználó elküldi az űrlapot jelenik meg **callform.html**. Az alábbi kód létrehozza a hívási üzenetet, és létrehozza a hívást. Is győződjön meg róla, hogy a Twilio-fiók és a [Twilio-konzolhitelesítési][twilio_console] jogkivonat használata helyett a helyőrző értékek $sid **és** **$token** az alábbi kódban.

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

Amellett, hogy a hívás, **makecall.php** megjeleníti néhány hívás metaadatok, amint az az alábbi képen látható. A hívási metaadatokról [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties]a témakörben talál további információt.

![Azure-hívásválasz Twilio és PHP használatával][twilio_php_response]

## <a name="run-the-application"></a>Az alkalmazás futtatása
A következő lépés az, hogy [az alkalmazás üzembe helyezése az Azure Web Apps git](app-service/app-service-web-get-started-php.md) (bár nem minden információ van releváns). 

## <a name="next-steps"></a>További lépések
Ez a kód a Twilio használatával az Azure-ban a Twilio használatával való alapvető funkciók megjelenítéséhez lett biztosítva. Mielőtt éles környezetben üzembe helyezne az Azure-ba, érdemes lehet további hibakezelést vagy egyéb funkciókat hozzáadnia. Példa:

* Webes űrlap használata helyett azure storage blobok vagy az SQL Database segítségével tárolja a telefonszámokat, és hívja a szöveget. Az Azure storage blobjainak PHP-ben való használatáról az [Azure Storage használata PHP alkalmazásokkal][howto_blob_storage_php]című témakörben talál további információt. Az SQL Database PHP-ben való használatáról az [SQL Database használata PHP alkalmazásokkal][howto_sql_azure_php]című témakörben talál további információt.
* A **makecall.php** kód twilio által[https://twimlets.com/message][twimlet_message_url]megadott URL-címet ( ) használ, hogy twilio markup nyelv (TwiML) választ adjon, amely tájékoztatja a Twilio-t a hívás folytatásáról. A visszaadott TwiML például tartalmazhat `<Say>` olyan igét, amelynek eredményeként a rendszer szöveget szólít a hívás címzettjével. A Twilio által biztosított URL-cím használata helyett saját szolgáltatást hozhat létre a Twilio kérésének válaszolására; További információ: [A Twilio használata a PHP hang- és SMS-képességeihez című témakörben.][howto_twilio_voice_sms_php] További információ a TwiML-ről a [https://www.twilio.com/docs/api/twiml][twiml]itt `<Say>` található, és további információ a [https://www.twilio.com/docs/api/twiml/say][twilio_say]twilio-műveletekről a.
* Olvassa el a Twilio biztonsági irányelveit a. [https://www.twilio.com/docs/security][twilio_docs_security]

A Twilio-ról további [https://www.twilio.com/docs][twilio_docs]információt a.

## <a name="see-also"></a>Lásd még:
* [Hogyan használjuk a Twilio-t hang- és SMS-képességekhez a PHP-ben](partner-twilio-php-how-to-use-voice-sms.md)

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
