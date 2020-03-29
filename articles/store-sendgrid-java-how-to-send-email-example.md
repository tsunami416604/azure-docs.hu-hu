---
title: store-sendgrid-java-how-to-send-email-példa
description: E-mailek küldése a SendGrid segítségével Java-ból egy Azure-telepítésben
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
ms.author: erikre
ms.reviewer: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: 35307848c09391ae4468afc00adafd8171aaaa7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876481"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>E-mail küldése a SendGrid használatával Java-ról egy Azure-telepítésben
A következő példa bemutatja, hogyan használhatja a SendGrid segítségével e-maileket az Azure-ban üzemeltetett weboldalról. Az eredményül kapott alkalmazás kéri a felhasználót az e-mail értékek, ahogy az a következő képernyőképen látható.

![E-mail űrlap][emailform]

Az eredményül kapott e-mail a következő képernyőképhez hasonlóan fog kinézni.

![E-mail üzenet][emailsent]

A jelen témakörben szereplő kód használatához az alábbiakat kell tennie:

1. Szerezze be a javax.mail JARs-t, például a-ból. <https://www.oracle.com/technetwork/java/javamail/index.html>
2. Adja hozzá a JARs-t a Java build elérési úthoz.
3. Ha az Eclipse segítségével hozza létre ezt a Java alkalmazást, az Eclipse telepítési összeállítási szolgáltatásával felveheti a SendGrid-tárakat az alkalmazástelepítési fájlba (WAR). Ha nem használja az Eclipse-t a Java-alkalmazás létrehozásához, győződjön meg arról, hogy a könyvtárak ugyanabban az Azure-szerepkörben vannak, mint a Java-alkalmazás, és hozzáadódnak az alkalmazás osztályelérési úthoz.

Az e-mail elküldéséhez saját SendGrid felhasználónévvel és jelszóval is rendelkeznie kell. A SendGrid használatának megkezdéséhez olvassa el [Az e-mailek küldése a SendGrid használatával Java-ból](store-sendgrid-java-how-to-send-email.md).

Emellett erősen ajánlott a [Hello World Application for Azure eclipse-ben való létrehozása,](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable)illetve az Eclipse használata esetén az Azure-ban a Java-alkalmazások Azure-ban való üzemeltetésére szolgáló egyéb technikák ismerete.

## <a name="create-a-web-form-for-sending-email"></a>Webes űrlap létrehozása e-mailek küldéséhez
A következő kód bemutatja, hogyan hozhat létre webes űrlapot az e-mailek küldéséhez a felhasználói adatok lekéréséhez. Ennek a tartalomnak az alkalmazásában a JSP-fájl neve **emailform.jsp**.

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

## <a name="create-the-code-to-send-the-email"></a>Az e-mail elküldéséhez hozzon létre egy kódot
A következő kód, amelyet az e-mailform.jsp formátumban az űrlap kitöltésekor hívnak meg, létrehozza az e-mailt, és elküldi azt. Ennek a tartalomnak az alkalmazásában a JSP fájl neve **sendemail.jsp**.

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

Az e-mail elküldése mellett az emailform.jsp is eredményt ad a felhasználónak; egy példa a következő screenshot:

![E-mail eredményküldése][emailresult]

## <a name="next-steps"></a>További lépések
Telepítse az alkalmazást a számítási emulátorés egy böngészőben futtassa emailform.jsp, írja be az értékeket az űrlapon, kattintson **az e-mail küldése**gombra , majd tekintse meg az eredményeket sendemail.jsp.

Ez a kód a SendGrid Java-ban azure-beli használatát mutatja be. Mielőtt éles környezetben üzembe helyezne az Azure-ba, érdemes lehet további hibakezelést vagy egyéb funkciókat hozzáadnia. Példa: 

* Az Azure storage blobok vagy az SQL Database segítségével tárolhatja az e-mail címeket és az e-mail üzeneteket, webes űrlap használata helyett. Az Azure storage blobok Java-ban való használatáról [a Blob Storage Service Java-ból történő használata](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/)című témakörben talál további információt. Az SQL Database Java-ban való használatáról az [SQL Database használata Java-ban](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java)című témakörben talál további információt.
* A SendGrid Java-ban való használatáról további információt A [SendGrid használatával Java-ból című témakörben](store-sendgrid-java-how-to-send-email.md)talál.

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
