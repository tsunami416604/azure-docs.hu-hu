---
title: A SendGrid e-mail szolgáltatás (PHP) használata |} A Microsoft Docs
description: Ismerje meg, hogyan e-mail küldése a SendGrid e-mail szolgáltatás az Azure-ban. A PHP nyelven írt kódmintákat.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: 846002264d5f709f7cef6bba67927fc8959a9ccb
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "42055520"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Php-ből a SendGrid E-mail szolgáltatás használata
Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat, a SendGrid e-mail szolgáltatással az Azure-ban. A minta PHP nyelven íródtak.
Az ismertetett forgatókönyvek között megtalálható **hozhat létre, amely e-mailek**, **e-mail-küldési**, és **mellékletek hozzáadása**. A SendGrid és az e-mailt küld a további információkért lásd: a [lépések](#next-steps) szakaszban.

## <a name="what-is-the-sendgrid-email-service"></a>Mi az a SendGrid E-mail szolgáltatással?
A SendGrid van egy [e-mail felhő alapú szolgáltatás] , amely megbízható biztosít [tranzakciós e-mail kézbesítési], a méretezhetőség és a valós idejű analitikát és rugalmas API-kat, amelyek elérhetőbbé teszik egyéni integrációs egyszerű. A SendGrid gyakori használati forgatókönyvek a következők:

* Automatikusan adatokat küldjenek a visszaigazolások az ügyfelek számára
* Ügyfelek küldéséhez a havi e-közleményekben és ajánlatok küldésére terjesztési felügyelete listája
* Valós idejű metrikák, például a blokkolt e-mail és a vásárlói válaszkészségének gyűjtése
* Azonosíthatja a trendeket,-jelentések létrehozása
* Továbbítás vásárlói kapcsolatos kérdésekben
* E-mail-értesítések az alkalmazásból

További információkért lásd: [ https://sendgrid.com ] [ https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>A SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>A PHP-alkalmazások a SendGrid használatával
Egy Azure PHP-alkalmazások a SendGrid használatával kell semmilyen speciális konfiguráció és a kódolási. Mivel a SendGrid-szolgáltatás, hozzáférhetők pontosan ugyanúgy a felhőalapú alkalmazások eltárolni a helyszíni alkalmazásból.

## <a name="how-to-send-an-email"></a>Útmutató: E-mail küldése
Küldhet e-mailt SMTP vagy a SendGrid által biztosított webes API használatával.

### <a name="smtp-api"></a>SMTP API
E-mailt a SendGrid SMTP API-val, használja a *Swift levelezőprogrammal*, egy összetevő-alapú kódtár a e-mailek küldését a PHP-alkalmazások. Letöltheti a *Swift levelezőprogrammal* kódtárat [http://swiftmailer.org/download] [https://swiftmailer.symfony.com/] v5.3.0 (használata [Composer] Swift levelezőprogrammal telepítéséhez). Példányainak létrehozása és az e-mail-küldési beletartozik a <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_levelezőprogrammal</span>, és <span class="auto-style2">Swift\_üzenet </span> osztályok, a megfelelő tulajdonságokat beállítást, és hívása a <span class="auto-style2">Swift\_Mailer::send</span> metódust.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the receiver is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';

     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');

     // send message
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### <a name="web-api"></a>Webes API
Használható a PHP a [függvény curl] [ curl function] e-mail küldése a SendGrid webes API használatával.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );

     $request = $url.'api/mail.send.json';

     // Generate curl request
     $session = curl_init($request);

     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);

     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

     // obtain response
     $response = curl_exec($session);
     curl_close($session);

     // print everything out
     print_r($response);

SendGrid webes API nagyon hasonlít egy REST API-t, ha az nem igazi RESTful API-t, mivel a legtöbb hívásban mindkét LEKÉRÉSE, és a POST műveletek felcserélhetők.

## <a name="how-to-add-an-attachment"></a>Útmutató: a melléklet hozzáadása
### <a name="smtp-api"></a>SMTP API
Egy további kódsort a példaként megadott parancsfájlt tartalmazó Swift levelezőprogrammal egy e-mailt küld az SMTP API-val a melléklet foglalja magában.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');

     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';

     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

     // send message
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

A további kódsort a következőképpen történik:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Ez kódsort az attach metódus meghívja a a <span class="auto-style2">Swift\_üzenet</span> objektumra, és statikus metódust <span class="auto-style2">fromPath</span> a a <span class="auto-style2">Swift\_melléklet</span> fájl csatolása egy üzenetet, és osztály.

### <a name="web-api"></a>Webes API
A webes API-val melléklet küldése nagyon hasonlít a webes API-val e-mail küldése. Vegye azonban figyelembe, hogy a következő példában a paraméternek tömbnek tartalmaznia kell ezt az elemet:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Példa:

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';

     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );

     print_r($params);

     $request = $url.'api/mail.send.json';

     // Generate curl request
     $session = curl_init($request);

     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);

     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

     // obtain response
     $response = curl_exec($session);
     curl_close($session);

     // print everything out
     print_r($response);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Útmutató: a szűrők segítségével élőlábak, nyomon követés és az Analytics engedélyezése
A SendGrid használatával "szűrők" e-mail további funkciókat biztosít. Ezek a beállítások, amelyek e-mailbe ahhoz, hogy bizonyos funkciók, például engedélyezheti a kampányban, a Google analytics, előfizetés nyomon követése és így tovább lehet hozzáadni.

Szűrők, egy üzenet, a szűrők tulajdonság használatával alkalmazhatók. Minden szűrő egy szűrő-specifikus beállításokat tartalmazó kivonat szerint van megadva. Az alábbi példa lehetővé teszi, hogy a lábléc szűrőt, és adja meg a szöveges üzenet, amely az e-mail-üzenet alján lesz hozzáfűzve.
Ebben a példában használjuk [sendgrid-php-könyvtár].
Használat [Composer] erőforrástár telepítése:

    php composer.phar require sendgrid/sendgrid 2.1.1

Példa:    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version).
     # text is your plain-text email
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html>
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az alapokat, a SendGrid E-mail szolgáltatás, kövesse az alábbi hivatkozások további.

* A SendGrid-dokumentációja: <https://sendgrid.com/docs>
* A SendGrid PHP-könyvtár: <https://github.com/sendgrid/sendgrid-php>
* A SendGrid a különleges ajánlat Azure-ügyfelek számára: <https://sendgrid.com/windowsazure.html>

További információ: a [PHP fejlesztői központ](https://azure.microsoft.com/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: http://php.net/curl
[e-mail felhő alapú szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mail kézbesítési]: https://sendgrid.com/transactional-email
[sendgrid-php-könyvtár]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Composer]: https://getcomposer.org/download/
