---
title: "Hogyan Twilio (Java) a telefonhívás |} Microsoft Docs"
description: "Útmutató: Azure Java-alkalmazások használatával Twilio weblapról telefonhívás."
services: 
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
ms.openlocfilehash: 04ecb80a2a9e15b549b47138caf71c7e64bda500
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Hogyan telefonhívás Twilio Azure Java-alkalmazások használata
A következő példa bemutatja, hogyan használható fel a Twilio Azure-ban üzemeltetett weblapok hívást. Az eredményül kapott alkalmazás fogja kérni a felhasználót a telefonhívás-értékeket, az az alábbi képernyőfelvételen látható módon.

![Az Azure hívás űrlap Twilio és Java használatával][twilio_java]

A következő használatára az ebben a témakörben lévő lesz szüksége:

1. Szerezzen be egy Twilio-fiókja és a hitelesítési jogkivonat. Twilio megkezdéséhez, értékelje ki a következő árképzési [http://www.twilio.com/pricing][twilio_pricing]. Iratkozzon fel a következő [https://www.twilio.com/try-twilio][try_twilio]. Az API-t Twilio által biztosított kapcsolatos információkért lásd: [http://www.twilio.com/api][twilio_api].
2. Szerezze be a Twilio JAR. A [https://github.com/twilio/twilio-java][twilio_java_github], töltse le a GitHub-adatforrások és létrehozhat saját JAR, vagy egy előre elkészített JAR (vagy anélkül függőségek) letöltése.
   Ebben a témakörben a kódot az előre elkészített TwilioJava-3.3.8-az-függőségek JAR használatával készült.
3. A JAR hozzáadása a Java build elérési útját.
4. Használatakor Eclipse létrehozni a Java-alkalmazást, vegye fel a Twilio-JAR az alkalmazás központi telepítési fájl (WAR) Eclipse meg központi telepítési szerelvény funkció használata. Ha nem használ Eclipse létrehozni a Java-alkalmazást, győződjön meg arról, a Twilio-JAR belüli, a Java-alkalmazások Azure ugyanarra a szerepkörre, és megadható az alkalmazás osztály elérési.
5. Győződjön meg arról, a cacerts keystore az MD5 ujjlenyomat 67:CB:9 D Equifax biztonságos hitelesítésszolgáltató tanúsítványát tartalmazza: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (sorozatszám 35:DE:F4:CF pedig a SHA1 ujjlenyomat D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Ez az tanúsítvány hitelesítésszolgáltatói tanúsítványa a [https://api.twilio.com] [ twilio_api_service] szolgáltatást, amelyet a Twilio API-k használatakor nevezik. A CA-tanúsítványt a JDK cacert tárolóban történő hozzáadásával kapcsolatos további információkért lásd: [tanúsítvány hozzáadása a Java hitelesítésszolgáltató tanúsítványtároló][add_ca_cert].

Emellett az információkért ismeretét [létrehozása egy Hello World használó Eclipse Azure eszköztára][azure_java_eclipse_hello_world], vagy az egyéb technikák üzemeltetéséhez Java-alkalmazások az Azure-ban, ha nem használja az eclipse-ben, erősen ajánlott.

## <a name="create-a-web-form-for-making-a-call"></a>A következő hívással webes űrlap létrehozása
A következő kód bemutatja, hogyan hozzon létre egy webes űrlap egy hívás a felhasználói adatok beolvasása. Ebben a példában egy új dinamikus webes projekt alkalmazásában nevű **TwilioCloud**, hozták létre, és **callform.jsp** JSP-fájlként lett hozzáadva.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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
Az alábbi kód, amely nevezzük, amikor a felhasználó befejezi az űrlap callform.jsp által megjelenített, a hívás-üzenetet hoz létre, és állít elő, a hívást. Az ebben a példában a JSP-fájl neve **makecall.jsp** és hozzá lett adva a **TwilioCloud** projekt. (Twilio-fiókja és -hitelesítési token helyett a rendelt helyőrző értékeket használja **accountSID** és **authToken** az alábbi kódban.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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
         String Url="http://twimlets.com/message";
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

Azonkívül, hogy a hívás, makecall.jsp a Twilio-végpont, API-verziót és a hívás állapotát jeleníti meg. Példa: az alábbi képernyőfelvételhez:

![Azure hívási válasz Twilio és Java használatával][twilio_java_response]

## <a name="run-the-application"></a>Az alkalmazás futtatása
Az alábbiakban a magas szintű lépéseket kell futtatni az alkalmazást; a következő lépéseket találhatók információk [létrehozása egy Hello World használó Eclipse Azure eszköztára][azure_java_eclipse_hello_world].

1. Exportálja a TwilioCloud WAR az Azure **approot** mappát. 
2. Módosítsa **startup.cmd** a TwilioCloud WAR kibontásához.
3. A compute Emulator az alkalmazás fordítása.
4. A központi telepítés elindítása a compute emulator.
5. Nyisson meg egy böngészőt, és futtassa **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Adja meg az értékeket a képernyőn, kattintson a **hívás kezdeményezéséhez**, majd tekintse meg az eredményeket a makecall.jsp.

Amikor készen áll az Azure-ba, recompile a felhőbe történő központi telepítéséhez telepítse az Azure, és futtassa a http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp a böngészőben (helyettesítse be a következő *your_hosted_name*).

## <a name="next-steps"></a>Következő lépések
Ez a kód Azure Java nyelven Twilio használatával alapvető funkciókat mutatjuk be lett megadva. Mielőtt telepítené az Azure éles környezetben, érdemes lehet további hibakezelés vagy egyéb szolgáltatások. Példa:

* Egy webes űrlap használata helyett segítségével az Azure storage blobsba vagy SQL-adatbázis tárolja a telefonszámokat, és hívja meg a szöveg. Az Azure storage-blobot, amely Java használatával kapcsolatos információkért lásd: [használata a Blob Storage szolgáltatást Java][howto_blob_storage_java]. SQL-adatbázis a Java használatával kapcsolatos információkért lásd: [SQL-adatbázis használata a Java][howto_sql_azure_java].
* Használhat **RoleEnvironment.getConfigurationSettings** beolvasni a Twilio Fiókazonosítót és a hitelesítési token a központi telepítés konfigurációs beállításai, bekódolásának makecall.jsp szereplő értékek helyett. További információ a **roleenvironment-et** osztály című [JSP az Azure-szolgáltatás futásidejű kódtár használatával] [ azure_runtime_jsp] és az Azure szolgáltatás futásidejű csomag dokumentációját a [http://dl.windowsazure.com/javadoc][azure_javadoc].
* A makecall.jsp kódot hozzárendel egy Twilio-megadott URL-címet, [http://twimlets.com/message][twimlet_message_url], hogy a **URL-cím** változó. Az URL-cím, amely arról értesíti a Twilio való hívása Twilio Markup Language (TwiML) választ biztosít. A visszaadott TwiML tartalmazhat például egy  **&lt;szóbeli&gt;**  művelet, amely a hívás címzett felolvasását szöveg eredményez. Ahelyett, hogy a Twilio-megadott URL-címet, a saját Twilio tartozó kérelem; szolgáltatás létrehozása sikerült További információkért lásd: [hang-és SMS-képességek a Java használatát Twilio hogyan][howto_twilio_voice_sms_java]. További információ a TwiML található [http://www.twilio.com/docs/api/twiml][twiml], és további információ a  **&lt;szóbeli&gt;**  és egyéb Twilio művelet található a [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Olvassa el a Twilio biztonsági irányelvek a [https://www.twilio.com/docs/security][twilio_docs_security].

További információ a Twilio: [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Lásd még:
* [Hang-és SMS képességei Java Twilio használata][howto_twilio_voice_sms_java]
* [A tanúsítvány felvétele a Java Hitelesítésszolgáltatói tanúsítványok tárába][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
