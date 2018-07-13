---
title: A SendGrid e-mail szolgáltatással (Java) használatával |} A Microsoft Docs
description: Ismerje meg, hogyan e-mail küldése a SendGrid e-mail szolgáltatás az Azure-ban. A Java nyelven írt kódmintákat.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: cadd771a516270faf23f18a36f54fa9a1541e6d5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698670"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Hogyan lehet E-mail küldése a SendGrid, a Java használatával
Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat, a SendGrid e-mail szolgáltatással az Azure-ban. A minták Java nyelven íródtak. Az ismertetett forgatókönyvek között megtalálható **hozhat létre, amely e-mailek**, **e-mail-küldési**, **mellékletek hozzáadása**, **ezekkel a szűrőkkel**, és **tulajdonságainak frissítése**. A SendGrid és az e-mailt küld a további információkért lásd: a [további lépések](#next-steps) szakaszban.

## <a name="what-is-the-sendgrid-email-service"></a>Mi az a SendGrid E-mail szolgáltatással?
A SendGrid van egy [e-mail felhő alapú szolgáltatás] , amely megbízható biztosít [tranzakciós e-mail kézbesítési], a méretezhetőség és a valós idejű analitikát és rugalmas API-kat, amelyek elérhetőbbé teszik egyéni integrációs egyszerű. A SendGrid gyakori használati forgatókönyvek a következők:

* Automatikusan adatokat küldjenek a visszaigazolások az ügyfelek számára
* Ügyfelek küldéséhez a havi e-közleményekben és ajánlatok küldésére terjesztési felügyelete listája
* Valós idejű metrikák, például a blokkolt e-mail és a vásárlói válaszkészségének gyűjtése
* Azonosíthatja a trendeket,-jelentések létrehozása
* Továbbítás vásárlói kapcsolatos kérdésekben
* E-mail-értesítések az alkalmazásból

További információkért lásd: <http://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>A SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Hogyan: javax.mail könyvtárak
Szerezze be a javax.mail kódtárakat, például <http://www.oracle.com/technetwork/java/javamail> , és importálja őket a kód. Egy magas szintű, az SMTP használatával történő levélküldéshez a javax.mail kódtár használatával történik, tegye a következőket:

1. Adja meg az SMTP-értékeket, többek között az SMTP-kiszolgáló, amely a SendGrid smtp.sendgrid.net.

```
        import java.util.Properties;
        import javax.activation.*;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. Kiterjesztheti a *javax.mail.Authenticator* osztályt, és a végrehajtása során a *getPasswordAuthentication* metódus, a SendGrid-felhasználónevét és jelszavát adja vissza.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Hozzon létre egy hitelesített e-mail munkamenet keresztül egy *javax.mail.Session* objektum.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Hozza létre az üzenetet, és rendelje hozzá **való**, **a**, **tulajdonos** és értékeket. Ez látható a [Útmutató: hozzon létre egy e-mailt](#how-to-create-an-email) szakaszban.
4. A keresztül küldött egy *javax.mail.Transport* objektum. Ez látható a [Útmutató: E-mail küldése] [# útmutatóval-to-egy – e-mail küldésének] szakaszban.

## <a name="how-to-create-an-email"></a>Hogyan: hozzon létre egy e-mailt
A következő bemutatja, hogyan adja meg az értékeket egy e-mailt.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>Útmutató: e-mail küldése
Az alábbiakban látható egy e-mailek küldése.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Útmutató: a melléklet hozzáadása
A következő kód bemutatja, hogyan mellékletet.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Útmutató: a szűrők segítségével élőlábak, nyomon követés és az analytics engedélyezése
A SendGrid használatával e-mail további funkciókat biztosít *szűrők*. Ezek a beállítások, amelyek e-mailbe ahhoz, hogy bizonyos funkciók, például engedélyezheti a kampányban, a Google analytics, előfizetés nyomon követése és így tovább lehet hozzáadni. Szűrők teljes listáját lásd: [szűrőbeállítások][Filter Settings].

* Az alábbiakban látható, amely az e-mailt küld a rendszer alján megjelenő HTML-szöveg eredményez élőláb szűrőt be, hogyan kell.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* A szűrő egy másik példa követési van kattintson. Tegyük fel, hogy az e-mail szövege tartalmaz egy hivatkozást, például a következő, és szeretné nyomon követni a kattintás sebesség:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* A kampányban engedélyezéséhez használja a következő kódot:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Útmutató: e-mail-tulajdonságok frissítése
Néhány e-mail-tulajdonságok használatával lehet felülírni **tulajdonságot** , illetve használatával **tulajdonság hozzáadása**.

Adja meg például **ReplyTo** címek, használja a következő:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Hozzáadása egy **Cc** címzett, használja a következő:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Hogyan: további SendGrid-szolgáltatások használata
A SendGrid kínál a webes API-kat használhatja az Azure-alkalmazásból további SendGrid funkciói kihasználhatók. További részletek: a [SendGrid API-dokumentáció][SendGrid API documentation].

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az alapokat, a SendGrid E-mail szolgáltatás, kövesse az alábbi hivatkozások további.

* Ez a minta azt mutatja be a SendGrid használatával az Azure-környezetben: [hogyan e-mail küldése a SendGrid, a Java használatával Azure-környezetben](store-sendgrid-java-how-to-send-email-example.md)
* A SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* A SendGrid API-dokumentáció: <https://sendgrid.com/docs/API_Reference/index.html>
* A SendGrid a különleges ajánlat Azure-ügyfelek számára: <https://sendgrid.com/windowsazure.html>

[http://sendgrid.com]: https://sendgrid.com
[http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[http://sendgrid.com/features]: https://sendgrid.com/features
[http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[e-mail felhő alapú szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mail kézbesítési]: https://sendgrid.com/transactional-email
