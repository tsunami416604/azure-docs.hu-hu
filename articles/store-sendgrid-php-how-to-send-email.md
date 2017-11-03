---
title: "A SendGrid e-mail szolgáltatás (PHP) használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan küldjön e-maileket a SendGrid e-mail szolgáltatás az Azure-on. Kódminták PHP."
documentationcenter: php
services: 
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
ms.openlocfilehash: 523b986f66a2e48685e9707903194856f0dcf4a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Php-ből a SendGrid E-mail szolgáltatás használata
Ez az útmutató bemutatja, hogyan Azure a SendGrid e-mail szolgáltatás közös programozási feladatok elvégzéséhez. A minták PHP nyelven íródtak.
Az ismertetett forgatókönyvek **hozhat létre, e-mailek**, **e-mailek küldéséhez**, és **mellékletek hozzáadása**. A SendGrid és e-mailek küldéséhez további információkért lásd: a [lépések](#next-steps) szakasz.

## <a name="what-is-the-sendgrid-email-service"></a>Mi az a SendGrid E-mail szolgáltatás?
SendGrid van egy [felhőalapú szolgáltatás] biztosít megbízható [tranzakciós e-mailben kézbesítésre], a méretezhetőség és a valós idejű elemzési rugalmas API-k, amelyek egyéni integrációs könnyedén együtt. Gyakori SendGrid használati forgatókönyvek a következők:

* Automatikusan az ügyfél számára a visszaigazolások küldésére
* Az ügyfelek havi e-szórólapok és a különleges ajánlatokkal terjesztési felügyelete listája
* Többek között a blokkolt e-mail, és az ügyfél reakcióidőt valós idejű metrikáját gyűjtése
* Jelentések segítségével azonosíthatja a trendeket létrehozása
* Ügyfél-lekérdezések továbbítása
* E-mail értesítések az alkalmazásról

További információkért lásd: [https://sendgrid.com][https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>A SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>A PHP-alkalmazás a SendGrid használatával
Nincsenek különleges SendGrid használatát egy Azure PHP-alkalmazás szükséges konfiguráció vagy a kódolást. SendGrid egy olyan szolgáltatás, mert hozzáférhetők pontosan ugyanúgy felhő-alkalmazásból használhatja a helyszíni alkalmazásból.

## <a name="how-to-send-an-email"></a>Útmutató: az e-mailek küldése
Elküldheti e-mailek SMTP vagy a SendGrid által biztosított webes API használatával.

### <a name="smtp-api"></a>SMTP API
A SendGrid SMTP API használatával e-mailt küldeni, használja a *Swift levelezőprogrammal*, egy összetevő-alapú kódtár a PHP-alkalmazások az e-mailek küldésekor. Letöltheti a *Swift levelezőprogrammal* kódtárat [http://swiftmailer.org/download] [ http://swiftmailer.org/download] v5.3.0 (használata [szerkesztő] Swift telepítése Levelezőprogrammal). A könyvtárhoz e-mailek küldéséhez magában foglalja a példányok a <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_levelezőprogrammal</span>, és <span class="auto-style2">Swift\_üzenet </span> osztályok, a megfelelő tulajdonságokat, és hívja a <span class="auto-style2">Swift\_Mailer::send</span> metódust.

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
Használja a PHP tartozó [függvény curl] [ curl function] a SendGrid webes API-jával e-mail üzenetek küldéséhez.

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

A SendGrid tartozó webes API nagyon hasonlít a REST API-t, ha az nem valóban RESTful API, mivel a legtöbb hívások, mind az beszerzése és POST műveletek felcserélhetők.

## <a name="how-to-add-an-attachment"></a>Hogyan: hozzá mellékletet
### <a name="smtp-api"></a>SMTP API
Az SMTP API-val melléklet küldése magában foglalja egy további kódsort a Swift levelezőprogrammal az e-mailt küld a példaként megadott parancsfájlt.

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

A kódsort az attach metódus meghívja a <span class="auto-style2">Swift\_üzenet</span> objektumra, és használja a statikus metódus <span class="auto-style2">fromPath</span> a a <span class="auto-style2">Swift\_melléklet</span> fájl csatolása egy üzenetet, és az osztály.

### <a name="web-api"></a>Webes API
A webes API-jával melléklet küldése nagyon hasonlít a webes API-jával e-mail. Vegye figyelembe azonban, hogy az alábbi példában a paraméter tömbjének tartalmaznia kell ezt az elemet:

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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Útmutató: a szűrők segítségével engedélyezze a láblécben, nyomon követési és elemzés
SendGrid "szűrők" révén további e-mail-funkciókat biztosítja. Ezek a beállítások, amelyek e-mailt ahhoz, hogy bizonyos funkciók, például követési kattintson, Google analytics, követési előfizetés, és így tovább is hozzáadhatók.

Szűrők üzenet szűrők tulajdonságának használatával alkalmazhatók. Minden egyes szűrő szűrő-specifikus beállításokat tartalmazó kivonat szerint van megadva. Az alábbi példa lehetővé teszi, hogy a lábléc szűrőt, és határozza meg, hogy a rendszer hozzáfűzi az e-mailt szöveges üzenetet.
Ehhez a példához használjuk [sendgrid-php könyvtár].
Használjon [szerkesztő] telepíteni szalagtár:

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

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a SendGrid E-mail szolgáltatás alapjait, az alábbi hivatkozásokból további.

* SendGrid dokumentáció: <https://sendgrid.com/docs>
* SendGrid PHP könyvtár: <https://github.com/sendgrid/sendgrid-php>
* SendGrid különleges ajánlat Azure ügyfeleknek: <https://sendgrid.com/windowsazure.html>

További információ: a [PHP fejlesztői központ](/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: http://php.net/curl
[felhőalapú szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mailben kézbesítésre]: https://sendgrid.com/transactional-email
[sendgrid-php könyvtár]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[szerkesztő]: https://getcomposer.org/download/
