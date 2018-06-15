---
title: A SendGrid e-mail szolgáltatás (Java) használatával |} Microsoft Docs
description: Megtudhatja, hogyan küldjön e-maileket a SendGrid e-mail szolgáltatás az Azure-on. A Kódminták Java nyelven.
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
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364899"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>E-mailek Java a SendGrid küldése
Ez az útmutató bemutatja, hogyan Azure a SendGrid e-mail szolgáltatás közös programozási feladatok elvégzéséhez. A minták Java nyelven íródtak. Az ismertetett forgatókönyvek **hozhat létre, e-mailek**, **e-mailek küldéséhez**, **mellékletek hozzáadása**, **szűrőkkel**, és **tulajdonságainak frissítése**. A SendGrid és e-mailek küldéséhez további információkért lásd: a [további lépések](#next-steps) szakasz.

## <a name="what-is-the-sendgrid-email-service"></a>Mi az a SendGrid E-mail szolgáltatás?
SendGrid van egy [felhőalapú szolgáltatás] biztosít megbízható [tranzakciós e-mailben kézbesítésre], a méretezhetőség és a valós idejű elemzési rugalmas API-k, amelyek egyéni integrációs könnyedén együtt. Gyakori SendGrid használati forgatókönyvek a következők:

* Automatikusan az ügyfél számára a visszaigazolások küldésére
* Az ügyfelek havi e-szórólapok és a különleges ajánlatokkal terjesztési felügyelete listája
* Többek között a blokkolt e-mail, és az ügyfél reakcióidőt valós idejű metrikáját gyűjtése
* Jelentések segítségével azonosíthatja a trendeket létrehozása
* Ügyfél-lekérdezések továbbítása
* E-mail értesítések az alkalmazásról

További információkért lásd: <http://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>A SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Útmutató: a javax.mail könyvtárak használatára
Megjelenítheti a javax.mail könyvtárak, például a <http://www.oracle.com/technetwork/java/javamail> és importálhatja őket a kódot. A magas szintű a folyamat a javax.mail könyvtárban a használatát, e-mailek SMTP küldése történik, a következő:

1. Adja meg az SMTP értékeket, beleértve az SMTP-kiszolgáló, amely a SendGrid smtp.sendgrid.net.

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

1. Kiterjeszti a *javax.mail.Authenticator* osztály, és a végrehajtása során a *getPasswordAuthentication* metódus, a SendGrid felhasználónevet és jelszót szabad visszaadnia.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. A hitelesített e-mail-munkamenetet létrehozni egy *javax.mail.Session* objektum.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Hozza létre az üzenetet, és rendelje hozzá **való**, **a**, **tulajdonos** és értékek tartalmakat. Ezt mutatják be a [Útmutató: hozzon létre egy e-mailt](#how-to-create-an-email) szakasz.
4. A keresztül küldött egy *javax.mail.Transport* objektum. Ezt mutatják be a [Útmutató: az e-mailek küldése] [#how-to--az-e-mail küldése] szakasz.

## <a name="how-to-create-an-email"></a>Hogyan: hozzon létre egy e-mailt
A következő bemutatja, hogyan adható meg érték a e-mailt.

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

## <a name="how-to-send-an-email"></a>Útmutató: az e-mailek küldése
Az alábbiakban látható egy e-mailek küldése.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Hogyan: hozzá mellékletet
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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Útmutató: a szűrők segítségével engedélyezze a láblécben, nyomon követési és elemzés
SendGrid használatával további e-mail helyeire *szűrők*. Ezek a beállítások, amelyek e-mailt ahhoz, hogy bizonyos funkciók, például követési kattintson, Google analytics, követési előfizetés, és így tovább is hozzáadhatók. Szűrők teljes listáját lásd: [szűrőbeállítások][Filter Settings].

* A következő bemutatja, hogyan lehet beszúrni egy élőláb szűrőt, amely az e-mailben küldött alján megjelenő HTML szöveg eredményez.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Egy másik példa a szűrő nyomon követési van kattintson. Tegyük fel, hogy az e-mailek szöveg tartalmazza-e egy hivatkozás, például a következő, és szeretné nyomon követni a kattintson aránya:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Kattintson a követési engedélyezéséhez használja a következő kódot:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Útmutató: frissítés e-mailes tulajdonságai
Néhány e-mail tulajdonság használatával felülírható **tulajdonsága** , illetve használatával **tulajdonság hozzáadása**.

Adja meg például **ReplyTo** címek, használja a következő:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Hozzáadása egy **Cc** címzett, használja a következő:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Útmutató: további SendGrid szolgáltatás használata
SendGrid webes API-k segítségével kihasználhatja az Azure alkalmazásról további SendGrid funkciókat kínál. Teljes részletekért lásd: a [SendGrid API-JÁNAK dokumentációja][SendGrid API documentation].

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a SendGrid E-mail szolgáltatás alapjait, az alábbi hivatkozásokból további.

* Minta azt mutatja be, az Azure-telepítés SendGrid használatával: [küldése az e-mailek SendGrid Java az Azure-telepítés](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API-dokumentáció: <https://sendgrid.com/docs/API_Reference/index.html>
* SendGrid a különleges ajánlat Azure ügyfelek esetén: <https://sendgrid.com/windowsazure.html>

[http://sendgrid.com]: https://sendgrid.com
[http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[http://sendgrid.com/features]: https://sendgrid.com/features
[http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[felhőalapú szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mailben kézbesítésre]: https://sendgrid.com/transactional-email
