---
title: Telefonon hívja fel a Twilióból (Java) Győződjön meg arról, hogyan |} A Microsoft Docs
description: Ismerje meg, hogyan telefonhívás egy weboldalról, a Twilio használata Java-alkalmazásokban az Azure-ban.
services: ''
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 84c37927eda65be71eb837aef2cb4968a121ee29
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426893"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>A Twilio használata Java-alkalmazás az Azure-ban a telefonhívás módját
Az alábbi példa bemutatja, hogyan használhatja a Twilio segítségével az Azure-ban üzemeltetett weboldalakról hívást. Az eredményül kapott alkalmazás fogja kérni a felhasználót, telefonhívás-értékek, az alábbi képernyőfelvételen látható módon.

![A Twilio és a Java használatával Azure hívás űrlap][twilio_java]

Ez a témakör a programkód használatával a következőket kell:

1. A Twilio-fiók és a hitelesítési jogkivonat. Első lépések a Twilio, kiértékelheti a díjszabás [ https://www.twilio.com/pricing ] [ twilio_pricing]. Iratkozzon fel a következő [ https://www.twilio.com/try-twilio ] [ try_twilio]. A Twilio által biztosított API-val kapcsolatos további információkért lásd: [ https://www.twilio.com/api ] [ twilio_api].
2. Szerezze be a Twilio JAR. A [ https://github.com/twilio/twilio-java ] [ twilio_java_github], töltse le a GitHub-források, és létrehozhat saját JAR, vagy egy előre elkészített JAR (vagy anélkül függőségek) letöltése.
   Ebben a témakörben a kódot az előre elkészített TwilioJava-3.3.8-az-függőségek JAR használatával készült.
3. A JAR hozzáadása a Java build elérési útja.
4. Ha Eclipse a Java-alkalmazás létrehozásához használ, adathordozófájlba felvenni kívánt a Twilio JAR az alkalmazás központi telepítési (WAR-) eclipse-ben a központi telepítési szerelvény funkció használata. Ha az Eclipse a Java-alkalmazás létrehozása nem használ, győződjön meg arról, a Twilio JAR található, a Java-alkalmazás az Azure ugyanarra a szerepkörre, és az osztály az alkalmazás elérési útja hozzá.
5. Győződjön meg arról, a cacerts keystore az MD5-tel ujjlenyomat 67:CB:9 D Equifax biztonságos hitelesítésszolgáltató tanúsítványát tartalmazza: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (sorozatszám 35:DE:F4:CF pedig az SHA1-ujjlenyomatot D2:32:09:AD:23:D 3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Ez a tanúsítvány hitelesítésszolgáltató (CA) tanúsítványa az [ https://api.twilio.com ] [ twilio_api_service] szolgáltatás, amely nevezzük, amikor a Twilio API-kkal. A CA-tanúsítvány a JDK cacert tárolóban történő hozzáadásával kapcsolatos további információkért lásd: [tanúsítvány hozzáadása a Java Hitelesítésszolgáltatói tanúsítvány Store][add_ca_cert].

Ezenkívül a adatait ismeretét [létrehozása egy Hello World alkalmazás használatával az Azure Toolkit for Eclipse][azure_java_eclipse_hello_world], vagy más módszerek az Azure-ban a Java-alkalmazások üzemeltetésére, ha Ön az Eclipse nem használ, erősen ajánlott.

## <a name="create-a-web-form-for-making-a-call"></a>Hozzon létre egy webes űrlap egy hívás
A következő kód bemutatja, hogyan hozhat létre egy webes űrlap, hogy a hívás felhasználói adatok lekéréséhez. Ebben a példában egy új dinamikus webes projekt alkalmazásában nevű **TwilioCloud**, lett létrehozva, és **callform.jsp** JSP-fájlként lett hozzáadva.

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

## <a name="create-the-code-to-make-the-call"></a>A kód a híváshoz létrehozása
A következő kódra, amely nevezzük, amikor a felhasználó befejezi az űrlap callform.jsp által megjelenített, a hívás üzenetet hoz létre, és állít elő, a hívást. Az ebben a példában a JSP-fájl neve **makecall.jsp** és hozzá lett adva a **TwilioCloud** projekt. (Használja a Twilio-fiók és a hitelesítési token helyett a helyőrző értékeket rendelt **accountSID** és **Authtokent** az alábbi kódban.)

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

Amellett, hogy a hívás, makecall.jsp a Twilio-végpont, API-verzió és a hívás állapotát jeleníti meg. Egy példa az alábbi képernyőfelvételen:

![A Twilio és a Java használatával Azure hívás válasz][twilio_java_response]

## <a name="run-the-application"></a>Az alkalmazás futtatása
Az alábbiakban a magas szintű lépéseket kell az alkalmazás futtatása Ezeket a lépéseket címen találja részletesen [létrehozása egy Hello World alkalmazás használatával az Azure Toolkit for Eclipse][azure_java_eclipse_hello_world].

1. Az Azure-ban a TwilioCloud WAR exportálása **approot** mappát. 
2. Módosítsa **startup.cmd** kicsomagolni a TwilioCloud WAR.
3. A compute Emulator az alkalmazás fordításához.
4. A compute emulatorban, indítsa el a központi telepítés.
5. Nyisson meg egy böngészőt, és futtassa **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Adja meg az értékeket a képernyőn, és kattintson a **a következő hívás**, és tekintse meg az eredményeket a makecall.jsp.

Amikor készen áll az Azure-ba, a felhőben való üzembe helyezéshez újrafordítás üzembe helyezéséhez üzembe helyezése az Azure-ba, és futtassa a http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp a böngészőben (helyettesítse be az értékét  *your_hosted_name*).

## <a name="next-steps"></a>További lépések
Ez a kód mutatni, Twilio, a Java használatával Azure-on alapvető funkciói lett megadva. Mielőtt üzembe helyezni az Azure éles környezetben, érdemes lehet további hibakezelés vagy más szolgáltatások hozzáadása. Példa:

* Egy webes űrlap használata helyett használhatja az Azure storage blobból vagy az SQL Database telefonszámok tárolására, és hívja a szöveget. Az Azure storage-blobok a Java használatával kapcsolatos információkért lásd: [használata a Blob Storage szolgáltatás a Javával][howto_blob_storage_java]. 
* Használhat **RoleEnvironment.getConfigurationSettings** beolvasni a Twilio-fiókja Azonosítóját és hitelesítési tokent a telepítési konfigurációt, fix kódolása makecall.jsp szereplő értékek helyett. További információ a **RoleEnvironment** osztály, lásd: [JSP-ben az Azure szolgáltatás futtatókörnyezeti kódtárának használata] [ azure_runtime_jsp] és az Azure szolgáltatás futtatókörnyezete csomag dokumentációját a [ http://dl.windowsazure.com/javadoc][azure_javadoc].
* A makecall.jsp kód hozzárendel egy Twilio-megadott URL-címet, [ https://twimlets.com/message ] [ twimlet_message_url], az a **URL-cím** változó. Az URL-címet biztosít, amely tájékoztatja a Twilio, a hívás való Twilio Markup Language (TwiML) választ. Ha például a visszaadott TwiML tartalmazhat egy **&lt;Say&gt;** művelet, amely folyamatban van a hívott a kimondott szöveget eredményez. A Twilio által megadott URL-cím helyett is létrehozhatja a saját szolgáltatás Twilio a kérelem; További információkért lásd: [a Twilio használata Hanghívási és SMS-funkciókhoz javában hogyan][howto_twilio_voice_sms_java]. TwiML további információ található [ https://www.twilio.com/docs/api/twiml ] [ twiml], és további információ a **&lt;Say&gt;** és egyéb Twilio-művelet címen [ https://www.twilio.com/docs/api/twiml/say ] [ twilio_say].
* Olvassa el a Twilio biztonsági irányelveknek, [ https://www.twilio.com/docs/security ] [ twilio_docs_security].

A Twilio kapcsolatos további információkért lásd: [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="see-also"></a>Lásd még:
* [Hogyan lehet a Twilio használata Hanghívási és SMS-funkciókhoz Java nyelven][howto_twilio_voice_sms_java]
* [Tanúsítvány hozzáadása a Java Hitelesítésszolgáltatói tanúsítvány Store][add_ca_cert]

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
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
