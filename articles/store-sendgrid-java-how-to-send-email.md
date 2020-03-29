---
title: A SendGrid e-mail szolgáltatás (Java) használata | Microsoft dokumentumok
description: Ismerje meg, hogyan küldhet e-mailt az Azure SendGrid e-mail szolgáltatásával. Java nyelven írt kódminták.
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
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876506"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Hogyan küldjünk e-mailt a SendGrid használatával Java-ról
Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat az Azure-beli SendGrid e-mail szolgáltatással. A minták Java nyelven íródnak. A tárgyalt forgatókönyvek közé tartozik **az e-mailek összeállítása**, **az e-mailek küldése**, a **mellékletek hozzáadása**, szűrők **használata**és a **tulajdonságok frissítése**. A SendGridről és az e-mailek küldéséről a Következő lépések című szakaszban talál további [információt.](#next-steps)

## <a name="what-is-the-sendgrid-email-service"></a>Mi a SendGrid e-mail szolgáltatás?
A SendGrid egy [felhőalapú e-mail szolgáltatás,] amely megbízható [tranzakciós e-mail kézbesítést,]méretezhetőséget és valós idejű elemzést, valamint rugalmas API-kat biztosít, amelyek megkönnyítik az egyéni integrációt. A SendGrid gyakori használati forgatókönyvei a következők:

* Nyugták automatikus küldése a vevőknek
* Terjesztési listák felügyelete az ügyfelek havi e-szórólapok és különleges ajánlatok küldéséhez
* Valós idejű mérőszámok gyűjtése például a blokkolt e-mailekhez és az ügyfelek válaszkészségéhez
* Jelentések létrehozása a trendek azonosításához
* Vevői lekérdezések továbbítása
* Értesítések küldése az alkalmazásból

További információ: <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Útmutató: A javax.mail könyvtárak használata
Szerezze be a javax.mail <https://www.oracle.com/technetwork/java/javamail> könyvtárakat, például a kódból, és importálja őket a kódba. Magas szinten a javax.mail könyvtár smtp-vel történő e-mail küldésének folyamata a következő:

1. Adja meg az SMTP-értékeket, beleértve az SMTP-kiszolgálót is, amely a SendGrid számára smtp.sendgrid.net.

```
        import java.util.Properties;
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

1. Bővítse ki a *javax.mail.Authenticator* osztályt, és a *getPasswordAuthentication* metódus megvalósításában adja vissza a SendGrid felhasználónevét és jelszavát.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Hitelesített e-mail munkamenet létrehozása *javax.mail.Session* objektumon keresztül.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Hozza létre az üzenetet, és rendelje hozzá **a,** **a forrásból**, **a tárgy** és a tartalom értékeit. Ez a [Hogyan: E-mail létrehozása](#how-to-create-an-email) szakaszban látható.
4. Küldje el az üzenetet egy *javax.mail.Transport* objektumon keresztül. Ez a [Hogyan: E-mail küldése] [#how-to-send-an-email] szakaszban látható.

## <a name="how-to-create-an-email"></a>Útmutató: E-mail létrehozása
Az alábbiakban bemutatjuk, hogyan adhatók meg értékek egy e-mailhez.

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

## <a name="how-to-send-an-email"></a>Útmutató: E-mail küldése
Az alábbiakban bemutatjuk, hogyan kell e-mailt küldeni.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Útmutató: Melléklet hozzáadása
Az alábbi kód bemutatja, hogyan vehet fel mellékletet.

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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Útmutató: Szűrők használata az élőlábak, a követés és az elemzés engedélyezéséhez
A SendGrid további e-mail funkciókat biztosít szűrők *használatával.* Ezek olyan beállítások, amelyek hozzáadhatók egy e-mail üzenethez, hogy lehetővé tegyék bizonyos funkciókat, például a kattintáskövetés engedélyezését, a Google Analytics szolgáltatást, az előfizetések nyomon követését és így tovább. A szűrők teljes listáját a [Szűrőbeállítások (Szűrő beállításai) menülapban találja.][Filter Settings]

* Az alábbiakban bemutatjuk, hogyan szúrhat be olyan élőlábszűrőt, amely az elküldött e-mail alján megjelenő HTML-szöveget eredményezi.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* A szűrő másik példája a kattintáskövetés. Tegyük fel, hogy az e-mail szövege egy hivatkozást tartalmaz, például az alábbiakat, és nyomon szeretné követni a kattintási arányt:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* A kattintáskövetés engedélyezéséhez használja a következő kódot:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Útmutató: E-mail tulajdonságainak frissítése
Egyes e-mail tulajdonságok felülírhatók **a set Property** használatával, vagy hozzáfűzhetők a Tulajdonság hozzáadása **használatával.**

A **ReplyTo** címek megadásához például használja a következőket:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

**Másolatot kap** címzett hozzáadásához használja az alábbiakat:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Útmutató: További SendGrid-szolgáltatások használata
A SendGrid webalapú API-kat kínál, amelyeket az Azure-alkalmazás további SendGrid-funkcióinak kihasználására használhat. További részleteket a [SendGrid API dokumentációjában][SendGrid API documentation]talál.

## <a name="next-steps"></a>További lépések
Most, hogy megtanulta a SendGrid E-mail szolgáltatás alapjait, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg.

* Minta, amely bemutatja a SendGrid használatát egy Azure-telepítésben: [E-mailek küldése a SendGrid használatával Java-ból egy Azure-telepítésben](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK:<https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API dokumentáció:<https://sendgrid.com/docs/API_Reference/index.html>
* SendGrid különleges ajánlat az Azure-ügyfelek számára:<https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[felhőalapú e-mail szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mail kézbesítés]: https://sendgrid.com/transactional-email
