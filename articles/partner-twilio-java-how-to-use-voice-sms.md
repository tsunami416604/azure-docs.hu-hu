---
title: Hogyan használjuk a Twilio-t hang- és SMS-hez (Java) | Microsoft dokumentumok
description: Ismerje meg, hogyan kezdeményezhetsz telefonhívást, és hogyan küldhet SMS-üzenetet az Azure-beli Twilio API-szolgáltatással. Java nyelven írt kódminták.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 18e93ce18ed746612996399dc1aeb258abd26165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637211"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>A Twilio használata hang- és SMS-képességekhez javadban
Ez az útmutató bemutatja, hogyan hajthat végre közös programozási feladatokat az Azure-beli Twilio API-szolgáltatással. A tárgyalt forgatókönyvek közé tartozik a telefonhívás és a rövid üzenetszolgáltatás (SMS) küldése. A Twilio használatával, valamint a hang- és SMS-használattal kapcsolatos további információkért tekintse meg a [Következő lépések](#NextSteps) című szakaszt.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Mi az a Twilio?
A Twilio egy telefonos webszolgáltatás API- amely lehetővé teszi a meglévő webes nyelvek és készségek használatát hang- és SMS-alkalmazások létrehozásához. A Twilio egy külső szolgáltatás (nem Azure-szolgáltatás, és nem Microsoft-termék).

**A Twilio Voice** lehetővé teszi, hogy az alkalmazások telefonhívásokat kezdeményezzenek és fogadjanak. **Twilio SMS** lehetővé teszi, hogy az alkalmazások, hogy és sms-üzeneteket. **A Twilio-ügyfél** lehetővé teszi, hogy az alkalmazások lehetővé tegyék a hangalapú kommunikációt a meglévő internetkapcsolatokkal, beleértve a mobilkapcsolatokat is.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio árképzés és különleges ajánlatok
A Twilio-díjszabásról a [Twilio-díjszabás][twilio_pricing]oldalon talál információt. Az Azure-ügyfelek [különleges ajánlatot][special_offer]kapnak: 1000 sms-t vagy 1000 bejövő percet. Ha feliratkozik erre az ajánlatra, [https://ahoy.twilio.com/azure][special_offer]vagy további információt szeretne kapni, kérjük, látogasson el a .

## <a name="concepts"></a><a id="Concepts"></a>Fogalmak
A Twilio API egy RESTful API, amely hang- és SMS-funkciókat biztosít az alkalmazások számára. Az ügyféltárak több nyelven is elérhetők; a listát a [Twilio API Libraries című témakörben található.][twilio_libraries]

A Twilio API fő szempontjai a Twilio-műveletek és a Twilio markup nyelv (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio igék
Az API Twilio-igéket használ; A ** &lt;Say&gt; ** ige például arra utasítja a Twilio-t, hogy hallhatóan kézbesítsen egy üzenetet egy hívásra.

Az alábbi lista a Twilio-igéket tartalmazza.

* **Tárcsázás&gt;: A hívót egy másik telefonhoz csatlakoztatja. &lt;**
* Gather : A telefon billentyűzetén megadott numerikus számjegyeket gyűjti. ** &lt;&gt;**
* **Hangup&gt;: Hívás &lt;** befejezése.
* Lejátszás : Hangfájl lejátszása. ** &lt;&gt;**
* **Várólista&gt;: Adja hozzá a hívók várólistájába. &lt;**
* Szünet : A megadott számú másodpercig csendben várakozik. ** &lt;&gt;**
* Rekord : Rögzíti a hívó hangját, és visszaadja a felvételt tartalmazó fájl URL-címét. ** &lt;&gt;**
* **Átirányítás&gt;: Egy hívás vagy SMS vezérlésének átvitele a TwiML-re egy másik URL-címen. &lt;**
* **Elutasítás:&gt;Elutasítja a bejövő hívást a Twilio-szám számlázás &lt;** nélkül.
* **Kimondás&gt;: A hívás közben készített szöveget beszédmé alakítja. &lt;**
* Sms : SMS-üzenetet küld. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML
A TwiML a Twilio-műveleteken alapuló XML-alapú utasítások készlete, amelyek tájékoztatják a Twilio-t a hívás vagy SMS feldolgozásáról.

Példaként, a következő TwiML lenne konvertálni a szöveget **Hello World!** beszédre.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Amikor az alkalmazás meghívja a Twilio API-t, az API-paraméterek egyike az URL-címet, amely a TwiML-választ adja vissza. Fejlesztési célokra használhatja a Twilio által biztosított URL-címeket az alkalmazások által használt TwiML-válaszok biztosításához. A TwiML-válaszok létrehozásához saját URL-eket is üzemeltethet, egy másik lehetőség pedig a **TwiMLResponse** objektum használata.

A Twilio-műveletekről, azok attribútumairól és a TwiML-ről a [TwiML][twiml]című témakörben talál további információt. A Twilio API-ról további információt a [Twilio API című témakörben][twilio_api]talál.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll a Twilio-fiók leválasztására, regisztráljon a [Try Twilio][try_twilio]című naphoz. Kezdheti egy ingyenes fiókkal, és később frissítheti fiókját.

Amikor regisztrál egy Twilio-fiókot, kap egy fiókazonosítót és egy hitelesítési jogkivonatot. Mindkettő re van szükség a Twilio API-hívások. A fiókjához való jogosulatlan hozzáférés megakadályozása érdekében tartsa biztonságban a hitelesítési jogkivonatot. A fiókazonosító és a hitelesítési jogkivonat a [Twilio konzolon][twilio_console]tekinthető megtekinthető, a **ACCOUNT SID** és az **AUTH TOKEN**mezőkben.

## <a name="create-a-java-application"></a><a id="create_app"></a>Java alkalmazás létrehozása
1. Szerezze be a Twilio JAR, és adja hozzá a Java build elérési útja és a WAR üzembe helyezési szerelvény. A [https://github.com/twilio/twilio-java][twilio_java]helyen letöltheti a GitHub-forrásokat, és létrehozhatja saját JAR-ját, vagy letölthet egy előre elkészített JAR-t (függőségekkel vagy függőségek nélkül).
2. Győződjön meg arról, hogy a JDK **cacerts** kulcstárolója tartalmazza az Equifax Secure Certificate Authority tanúsítványt MD5 ujjlenyomattal 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (a sorozatszám 3 5:DE:F4:CF és az SHA1 ujjlenyomat: D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Ez a [https://api.twilio.com][twilio_api_service] szolgáltatás hitelesítésszolgáltatói tanúsítványa, amely a Twilio API-k használatakor van meghívva. A JDK **cacerts** kulcstárolója megfelelő hitelesítésszolgáltatói tanúsítványt tartalmaz, a [Tanúsítvány hozzáadása a Java hitelesítésszolgáltatótanúsítvány-tárolóhoz című témakörben][add_ca_cert]talál további információt.

A Twilio-ügyfélkódtár Java-hoz való használatának részletes útmutatása a [Twilio használatával a Twilio használatával az Azure-beli Java alkalmazásban][howto_phonecall_java]található telefonhívás lebonyolítása című webhelyen található.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Az alkalmazás konfigurálása twilio-tárak használatára
A kódon belül a twilio-csomagok vagy az alkalmazásban használni kívánt osztályok a forrásfájlok tetején adhat hozzá **importálási** kimutatásokat.

Java forrásfájlok esetén:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Java Server Page (JSP) forrásfájlok esetén:

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Attól függően, hogy mely Twilio-csomagokat vagy osztályokat szeretné használni, az **importálási** utasítások eltérőek lehetnek.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Útmutató: Kimenő hívás
Az alábbiakban bemutatjuk, hogyan kezdeményezz kimenő hívást a **Hívás** osztály használatával. Ez a kód is használ egy Twilio által biztosított hely a Twilio Markup Language (TwiML) válasz ad vissza. Helyettesítse az értékeket a **be-** és **telefonszámokkal,** és győződjön meg arról, hogy a kód futtatása előtt ellenőrizze a Twilio-fiók **telefonszámát.**

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

A **Call.creator** metódusnak átadott paraméterekről a [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Mint említettük, ez a kód egy Twilio által biztosított hely a TwiML-válasz visszaadásához. Ehelyett használhatja a saját webhelyét a TwiML-válasz megadására; További információt a [TwiML-válaszok szolgáltatás a Java-alkalmazásokban az Azure-ban című témakörben talál.](#howto_provide_twiml_responses)

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Útmutató: SMS küldése
Az alábbiakban bemutatjuk, hogyan küldhet SMS-üzenetet az **Üzenet** osztály használatával. A **4155992671-es**számú számlálót a Twilio biztosítja az SMS-üzenetek küldéséhez szükséges próbafiókokhoz. **from** A **to** number ellenőrizni kell a Twilio-fiók a kód futtatása előtt.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

A **Message.creator** metódusnak átadott paraméterekről a [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms]következő témakörben talál további információt: .

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Hogyan: TwiML válaszok biztosítása a saját webhelyéről
Amikor az alkalmazás kezdeményezegy hívást a Twilio API-t, például a **CallCreator.create** metóduson keresztül, twilio elküldi a kérelmet egy URL-címre, amely várhatóan egy TwiML-választ ad vissza. A fenti példa a Twilio [https://twimlets.com/message][twimlet_message_url]által megadott URL-címet használja. (Bár a TwiML-t webszolgáltatások számára tervezték, a TwiML a böngészőben is megtekinthető. Ide kattintva [https://twimlets.com/message][twimlet_message_url] például egy üres ** &lt;Válasz&gt; ** elem jelenik meg; másik példaként [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] kattintson ide egy ** &lt;&gt; ** ** &lt;Say&gt; ** elemet tartalmazó Válasz elem megtekintéséhez.)

Ahelyett, hogy a Twilio által megadott URL-címre támaszkodna, létrehozhat saját URL-címet, amely HTTP-válaszokat ad vissza. A webhelyet bármely olyan nyelven létrehozhatja, amely HTTP-válaszokat ad vissza; ez a témakör feltételezi, hogy az URL-t egy JSP-oldalon fogja üzemeltetni.

A következő JSP oldal egy TwiML választ eredményez, amely szerint **a Hello World!** a hívásra.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

A következő JSP-lap egy TwiML-választ eredményez, amely néhány szöveget mond, több szünetet tartalmaz, és a Twilio API-verzióés az Azure-szerepkör neve információkat tartalmaz.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

Az **ApiVersion** paraméter twilio-hangkérelmekben érhető el (nem SMS-kérelmekben). A Twilio hang- és SMS-kérelmek rendelkezésre <https://www.twilio.com/docs/api/twiml/twilio_request> <https://www.twilio.com/docs/api/twiml/sms/twilio_request>álló kérelemparamétereinek megtekintéséhez tekintse meg, és. A **RoleName** környezeti változó egy Azure-telepítés részeként érhető el. (Ha egyéni környezeti változókat szeretne hozzáadni, hogy azok a **System.getenv**fájlból is felvehessék őket, olvassa el a Környezeti változók című részt a [Vegyes szerepkörkonfigurációs beállítások című][misc_role_config_settings]témakörben.)

Miután beállította a JSP-lapot a TwiML-válaszok megadására, használja a JSP-oldal URL-címét a **Call.creator** metódusnak átadott URL-címként. Ha például egy MyTwiML nevű webalkalmazás telepítve van egy Azure-üzemeltetett szolgáltatásban, és a JSP-lap neve mytwiml.jsp, az URL-cím átadható a **Call.creator** webhelynek a következők éppen látható módon:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Egy másik lehetőség a TwiML-lel való válaszadásra a **VoiceResponse** osztályon keresztül történik, amely a **com.twilio.twiml** csomagban érhető el.

A Twilio Azure-beli Java-val való használatáról a [Twilio használatával a Twilio használatával az Azure-beli Java-alkalmazásokban][howto_phonecall_java]című témakörben talál további információt.

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Útmutató: További Twilio-szolgáltatások használata
Az itt bemutatott példák mellett a Twilio webalapú API-kat is kínál, amelyek segítségével további Twilio-funkciókat használhat az Azure-alkalmazásból. A részletekért tekintse meg a [Twilio API dokumentációját.][twilio_api_documentation]

## <a name="next-steps"></a><a id="NextSteps"></a>Következő lépések
Most, hogy megtanulta a Twilio szolgáltatás alapjait, kövesse az alábbi linkeket, hogy többet megtudjon:

* [A Twilio biztonsági irányelvei][twilio_security_guidelines]
* [Twilio HowTo és példakód][twilio_howtos]
* [Twilio rövid útmutatók][twilio_quickstarts]
* [Twilio a GitHubon][twilio_on_github]
* [Beszéljen a Twilio ügyfélszolgálatával][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
