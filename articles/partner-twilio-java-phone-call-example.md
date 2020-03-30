---
title: Hogyan készítsünk egy telefonhívást Twilio (Java) | Microsoft dokumentumok
description: Ismerje meg, hogyan kezdeményezhetsz telefonhívást egy weboldalról a Twilio használatával egy Java-alkalmazásban az Azure-ban.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 168ec65cfd0ff4e87c33324daa353b554111c8aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838558"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Telefonhívás lebonyolítása a Twilio használatával egy Java-alkalmazásban az Azure-ban
A következő példa bemutatja, hogyan használhatja a Twilio hívást egy azure-ban üzemeltetett weboldalról. Az eredményül kapott alkalmazás kéri a felhasználót a telefonhívás értékeit, ahogy az a következő képernyőképen látható.

![Azure-hívási űrlap Twilio és Java használatával][twilio_java]

A jelen témakörben szereplő kód használatához az alábbiakat kell tennie:

1. Twilio-fiók és hitelesítési jogkivonat beszerzése. A Twilio első lépéseihez [https://www.twilio.com/pricing][twilio_pricing]értékelje ki az árakat a(z) szinten. Akkor iratkozzon [https://www.twilio.com/try-twilio][try_twilio]fel a . A Twilio által biztosított API-ról a témakörben talál [https://www.twilio.com/api][twilio_api]további információt.
2. Szerezze be a Twilio JAR.Obtain the Twilio JAR. A [https://github.com/twilio/twilio-java][twilio_java_github]helyen letöltheti a GitHub-forrásokat, és létrehozhatja saját JAR-ját, vagy letölthet egy előre elkészített JAR-t (függőségekkel vagy függőségek nélkül).
   A jelen témakörben szereplő kódot az előre elkészített TwilioJava-3.3.8-with-függőségek JAR használatával írta.
3. Adja hozzá a JAR-t a Java build elérési úthoz.
4. Ha az Eclipse használatával hozza létre ezt a Java-alkalmazást, az Eclipse telepítési szerelvény funkciójával adja meg a Twilio JAR-t az alkalmazás telepítési fájljában (WAR). Ha nem használja az Eclipse-t a Java-alkalmazás létrehozásához, győződjön meg arról, hogy a Twilio JAR ugyanabban az Azure-szerepkörben található, mint a Java-alkalmazás, és hozzá adja az alkalmazás osztályelérési útjához.
5. Győződjön meg róla, hogy a cacerts keystore tartalmazza az Equifax Secure Certificate Authority tanúsítványt MD5 ujjlenyomat67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (a sorozatszám 35:DE:F4:CF és az SHA1 ujjlenyomat D2:32:09:AD:23:D 3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Ez a [https://api.twilio.com][twilio_api_service] szolgáltatás hitelesítésszolgáltatói tanúsítványa, amely a Twilio API-k használatakor van meghívva. A hitelesítésszolgáltatói tanúsítvány JDK-tárolóhoz való hozzáadásáról a [Tanúsítvány hozzáadása a Java hitelesítésszolgáltatótanúsítvány-tárolóhoz című témakörben][add_ca_cert]talál további információt.

Emellett erősen ajánlott az [Azure Toolkit for Eclipse használatával létrehozott Hello World alkalmazás][azure_java_eclipse_hello_world]létrehozása, illetve az Eclipse használata esetén az Azure-alkalmazások Azure-beli üzemeltetésére szolgáló egyéb technikák ismerete.

## <a name="create-a-web-form-for-making-a-call"></a>Webes űrlap létrehozása híváshoz
A következő kód bemutatja, hogyan hozhat létre webes űrlapot a híváshoz való felhasználói adatok lekéréséhez. Ebben a példában egy új dinamikus webes projekt, **twilioCloud**, jött létre, és **callform.jsp** került jsp fájlként.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>A hívás hozása a kódhoz
A következő kód, amelynek neve akkor jön létre, amikor a felhasználó befejezi a callform.jsp által megjelenített űrlapot, létrehozza a hívási üzenetet, és létrehozza a hívást. Ebben a példában a JSP-fájl neve **makecall.jsp,** és hozzáadva lett a **TwilioCloud** projekthez. (Használja a Twilio-fiókot és a hitelesítési jogkivonatot a **fiókSID-hez** és **az authToken-hez** rendelt helyőrző értékek helyett az alábbi kódban.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="https://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

A hívás on kívül a makecall.jsp megjeleníti a Twilio-végpontot, az API-verziót és a hívás állapotát. Egy példa a következő képernyőkép:

![Azure-hívásválasz Twilio és Java használatával][twilio_java_response]

## <a name="run-the-application"></a>Az alkalmazás futtatása
Az alábbiakban az alkalmazás futtatásához szükséges magas szintű lépéseket taszítjuk; ezeknek a lépéseknek a részletei a [Hello World alkalmazás létrehozása az Eclipse azure-eszközkészlet használatával][azure_java_eclipse_hello_world]című helyen találhatók.

1. Exportálja a TwilioCloud WAR-t az Azure **alkalmazásgyökér** mappába. 
2. Módosítsa **a startup.cmd-t** a TwilioCloud WAR kicipzárazásához.
3. Állítsa össze a számítási emulátor jelentkezését.
4. Indítsa el a központi telepítést a számítási emulátorban.
5. Nyisson meg egy `http://localhost:8080/TwilioCloud/callform.jsp`böngészőt, és futtassa a futtassa a futtassa a böngészőt.
6. Írjon be értékeket az űrlapon, kattintson **a Hívás lefolytatása**gombra, majd tekintse meg az eredményeket a makecall.jsp alkalmazásban.

Ha készen áll az Azure-ra való üzembe helyezésre, újralefordítani a felhőbe való üzembe helyezéshez, az Azure-ba való üzembe helyezést, és futtatni http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp a böngészőben (az érték helyett *your_hosted_name).*

## <a name="next-steps"></a>További lépések
Ez a kód a Twilio java-beli Azure-beli alapvető funkcióinak megjelenítéséhez lett biztosítva. Mielőtt éles környezetben üzembe helyezne az Azure-ba, érdemes lehet további hibakezelést vagy egyéb funkciókat hozzáadnia. Példa:

* Webes űrlap használata helyett azure storage blobok vagy az SQL Database segítségével tárolja a telefonszámokat, és hívja a szöveget. Az Azure storage blobok Java-ban való használatáról [a Blob Storage Service Java-ból történő használata][howto_blob_storage_java]című témakörben talál további információt. 
* A **RoleEnvironment.getConfigurationSettings** használatával lekérheti a Twilio-fiók azonosítóját és a hitelesítési jogkivonatot a központi telepítés konfigurációs beállításaiból, ahelyett, hogy a makecall.jsp értékeket kódolja. A **RoleEnvironment** osztályról az [Azure Service Runtime Library használata a JSP-ben című témakörben][azure_runtime_jsp]talál további információt.
* A makecall.jsp kód twilio által megadott [https://twimlets.com/message][twimlet_message_url]URL-címet rendel az **URL-változóhoz.** Ez az URL-cím egy Twilio Markup Language (TwiML) választ biztosít, amely tájékoztatja a Twilio-t a hívás folytatásáról. A visszaadott TwiML például tartalmazhat ** &lt;&gt; ** egy Say műveletet, amelynek eredményeként a rendszer szöveget szólít a hívás címzettjével. A Twilio által biztosított URL-cím használata helyett saját szolgáltatást hozhat létre a Twilio kérésének válaszolására; További információ: [A Twilio használata a Hang- és SMS-képességekhez Java-ban című témakörben.][howto_twilio_voice_sms_java] További információ a TwiML-ről a területen [https://www.twilio.com/docs/api/twiml][twiml]található, a ** &lt;Say&gt; ** és más [https://www.twilio.com/docs/api/twiml/say][twilio_say]Twilio-műveletekről pedig a.
* Olvassa el a Twilio biztonsági irányelveit a. [https://www.twilio.com/docs/security][twilio_docs_security]

A Twilio-ról további [https://www.twilio.com/docs][twilio_docs]információt a.

## <a name="see-also"></a>Lásd még:
* [A Twilio használata hang- és SMS-képességekhez javadban][howto_twilio_voice_sms_java]
* [Tanúsítvány hozzáadása a Java hitelesítésszolgáltatótanúsítvány-tárolóhoz][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: https://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
