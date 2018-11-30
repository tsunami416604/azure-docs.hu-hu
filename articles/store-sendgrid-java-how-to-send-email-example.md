---
title: Store-sendgrid-Java-How-to-send-email-example
description: Hogyan lehet e-mail küldése a SendGrid, a Java használatával egy Azure-környezetben
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: c2f2a6e5f2dd551cfb300d333081874fef2f405c
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52421947"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Hogyan lehet E-mail küldése a SendGrid, a Java használatával Azure-környezetben
Az alábbi példa bemutatja, hogyan használhatja a SendGrid e-mailek küldése az Azure-ban üzemeltetett weboldalakról. Az eredményül kapott alkalmazás fogja kérni a felhasználót e-mail-értékek, az alábbi képernyőfelvételen látható módon.

![E-mailek űrlap][emailform]

Az eredményül kapott e-mailt az alábbi képernyőfelvételhez hasonlóan néz ki.

![E-mail-üzenet][emailsent]

Ez a témakör a programkód használatával a következőket kell:

1. Szerezze be a javax.mail JAR-fájlok kivételével, például <https://www.oracle.com/technetwork/java/javamail/index.html>.
2. A Java build elérési útját adja hozzá a JAR-fájlok kivételével.
3. Az Eclipse a Java-alkalmazás létrehozásához használ, ha az alkalmazás üzembe helyezési fájl (WAR-) eclipse-ben a központi telepítési szerelvény funkcióval is felvehet a SendGrid-kódtárakat. Ha az Eclipse a Java-alkalmazás létrehozása nem használ, győződjön meg arról, a könyvtárak található azonos Azure szerepét, a Java-alkalmazás és az osztály az alkalmazás elérési útja hozzá.

A saját SendGrid-felhasználónevét és jelszavát, az e-mailt küldhet a is kell rendelkeznie. Első lépések a SendGrid, lásd: [e-mail küldése a SendGrid, a Java használatával hogyan](store-sendgrid-java-how-to-send-email.md).

Ezenkívül a adatait ismeretét [létrehozása egy Hello World alkalmazást az Azure-ban az eclipse-ben](https://msdn.microsoft.com/library/windowsazure/hh690944), vagy a más módszerek az Azure-ban a Java-alkalmazások üzemeltetésére, ha nem használja az eclipse-ben, erősen ajánlott.

## <a name="create-a-web-form-for-sending-email"></a>E-mail-küldési webes űrlap létrehozása
A következő kód bemutatja, hogyan hozhat létre egy webes űrlap, e-mail-küldési felhasználói adatok lekéréséhez. Ez a tartalom alkalmazásában, a JSP-fájl neve **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Az e-mailt küldhet a kód létrehozása
A következő kódra, amely nevezzük, amikor befejezte az űrlap emailform.jsp, az e-mail-üzenetet hoz létre, és elküldi azokat. Ez a tartalom alkalmazásában, a JSP-fájl neve **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%

     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");

     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;

            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {

         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";

         Properties properties;

         properties = new Properties();

         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");

         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();

         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);

         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);

         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 

         message = new MimeMessage(mailSession);

         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            

         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);

         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();

        out.println("<p>Email processing completed.</p>");

     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>

    </body>
    </html>

Az e-mailt küld, mellett emailform.jsp eredményét, egy a felhasználó; egy példa az alábbi képernyőfelvételen:

![Eredmény e-mail küldése][emailresult]

## <a name="next-steps"></a>További lépések
A compute emulator az alkalmazás üzembe helyezéséhez és futtatásához egy böngészőben a emailform.jsp, adja meg az értékeket a képernyőn, kattintson **e-mailt**, és olvassa el a sendemail.jsp eredményez.

Ez a kód megmutatjuk, hogyan Java nyelven a SendGrid használata az Azure-ban megadva. Mielőtt üzembe helyezni az Azure éles környezetben, érdemes lehet további hibakezelés vagy más szolgáltatások hozzáadása. Példa: 

* Használhatja az Azure storage blobból vagy az SQL Database tárolja az e-mail-címeket és e-mail-üzenetekről, egy webes űrlap használata helyett. Az Azure storage-blobok a Java használatával kapcsolatos információkért lásd: [használata a Blob Storage szolgáltatás a Javával](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). SQL Database-hez a Java használatával kapcsolatos információkért lásd: [SQL Database használata Java nyelven](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java).
* Használhat `RoleEnvironment.getConfigurationSettings` lekérdezni a SendGrid-felhasználónevét és jelszavát a telepítési konfigurációt, ezeket az értékeket beolvasni a webes űrlap használata helyett. További információ a `RoleEnvironment` osztály, lásd: [JSP-ben az Azure szolgáltatás futtatókörnyezeti kódtárának használata](https://msdn.microsoft.com/library/windowsazure/hh690948) és az Azure szolgáltatás futtatókörnyezete csomag dokumentációját a <http://dl.windowsazure.com/javadoc>.
* Java-környezetben a SendGrid használatával kapcsolatos további információkért lásd: [e-mail küldése a SendGrid, a Java használatával hogyan](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
