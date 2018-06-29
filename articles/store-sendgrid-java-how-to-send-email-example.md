---
title: Store-sendgrid-Java-How-to-send-email-example
description: Hogyan e-mailek küldése a Java SendGrid Azure-telepítés
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
ms.openlocfilehash: fab76467025518bc455295da681d0b0127a9cbfe
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098729"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Hogyan e-mailek küldése a Java SendGrid Azure-telepítés
A következő példa bemutatja, hogyan használhatja SendGrid e-mailek küldése az Azure-ban üzemeltetett weblapról. Az eredményül kapott alkalmazás fogja kérni a felhasználót a e-mail-értékeket, az az alábbi képernyőfelvételen látható módon.

![E-mailek képernyő][emailform]

Az eredményül kapott e-mailt az alábbi képernyőfelvételhez hasonlóan fog kinézni.

![E-mail-üzenet][emailsent]

A következő használatára az ebben a témakörben lévő lesz szüksége:

1. Megjelenítheti a javax.mail JAR-fájlok kivételével, például a <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. A Java build elérési útját adja hozzá a JAR-fájlok kivételével.
3. Ha Eclipse létrehozni a Java-alkalmazást használ, a SendGrid-könyvtárak is megadhat a az alkalmazás központi telepítési fájl (WAR) Eclipse meg központi telepítési szerelvény funkció használata. Ha nem használ Eclipse létrehozni a Java-alkalmazást, győződjön meg arról, a szalagtárak rendszer belüli, a Java-alkalmazások Azure ugyanarra a szerepkörre, és hozzáadja az osztály az alkalmazás elérési útja.

A saját SendGrid felhasználónevet és jelszót, az e-mailt küldhet kell rendelkeznie. Első lépésként sendgrid, lásd: [küldése az e-mailek Java a SendGrid](store-sendgrid-java-how-to-send-email.md).

Emellett az információkért ismeretét [egy Hello World alkalmazás létrehozása az Azure az eclipse-ben](http://msdn.microsoft.com/library/windowsazure/hh690944), vagy az egyéb technikák üzemeltetéséhez Java-alkalmazások az Azure-ban, ha nem használja az eclipse-ben, erősen ajánlott.

## <a name="create-a-web-form-for-sending-email"></a>E-mailek küldéséhez a webes űrlap létrehozása
A következő kód bemutatja, hogyan hozzon létre egy webes űrlap e-mailek küldéséhez a felhasználói adatok beolvasása. A tartalom célját, a a JSP-fájl neve **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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
Az alábbi kód, amely nevezzük, amikor befejezte az űrlap emailform.jsp, az e-mail-üzenetet hoz létre, és elküldi azt. A tartalom célját, a a JSP-fájl neve **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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

Az e-mailek küldéséhez, mellett emailform.jsp eredményt biztosít a felhasználó; Példa: az alábbi képernyőfelvételhez:

![Mail eredmény küldése][emailresult]

## <a name="next-steps"></a>További lépések
A compute emulator az alkalmazás telepítése és egy böngészőből emailform.jsp futtassa, adja meg az értékeket a képernyőn, kattintson a **az e-mailek küldése**, és olvassa el a sendemail.jsp eredményez.

Ez a kód mutatjuk be, a Java SendGrid használata Azure lett megadva. Mielőtt telepítené az Azure éles környezetben, érdemes lehet további hibakezelés vagy egyéb szolgáltatások. Példa: 

* Az Azure storage blobsba vagy SQL-adatbázis segítségével tárolja az e-mail-címeket és e-mailek, egy webes űrlap használata helyett. Az Azure storage-blobot, amely Java használatával kapcsolatos információkért lásd: [használata a Blob Storage szolgáltatást Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). SQL-adatbázis a Java használatával kapcsolatos információkért lásd: [SQL-adatbázis használata a Java](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connect-query-java).
* Használhat `RoleEnvironment.getConfigurationSettings` lekérdezni a SendGrid-felhasználónevet és jelszót a telepítési konfigurációt, beolvasni ezeket az értékeket a webes űrlap használata helyett. További információ a `RoleEnvironment` osztály című [JSP az Azure-szolgáltatás futásidejű kódtár használatával](http://msdn.microsoft.com/library/windowsazure/hh690948) és az Azure szolgáltatás futásidejű csomag dokumentációját a <http://dl.windowsazure.com/javadoc>.
* Java nyelven SendGrid használatával kapcsolatos további információkért lásd: [küldése az e-mailek Java a SendGrid](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
