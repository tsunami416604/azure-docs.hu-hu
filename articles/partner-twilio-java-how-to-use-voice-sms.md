---
title: "Hang-és SMS (Java) Twilio használata |} Microsoft Docs"
description: "Útmutató a telefonhívás, és a Twilio API szolgáltatás SMS üzenet küldése az Azure-on. A Kódminták Java nyelven."
services: 
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 5a1b2ffa160a31b639605242b651dc8d14e7a01b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Hang-és SMS képességei Java Twilio használata
Ez az útmutató bemutatja, hogyan Azure a Twilio API szolgáltatás közös programozási feladatok elvégzéséhez. A tárgyalt forgatókönyvekben szerepel, így a telefonhívás, és egy rövid üzenetet szolgáltatás (SMS) üzenet küldésekor. A Twilio- és hang- és SMS használata az alkalmazások további információkért lásd: a [lépések](#NextSteps) szakasz.

## <a id="WhatIs"></a>Mi az, hogy a Twilio?
Twilio egy telefonos webszolgáltatás API, amely lehetővé teszi, hogy a meglévő webes nyelv és képességeik felhasználásával hang- és SMS alkalmazások készítéséhez. Twilio egy olyan külső szolgáltatás (nem az Azure szolgáltatásai és nem Microsoft-termékek).

**Twilio hang** lehetővé teszi, hogy az alkalmazások és a telefonhívásokat fogadja. **Twilio SMS** lehetővé teszi, hogy az alkalmazások és az SMS-üzeneteket fogadni. **Twilio-ügyfél** lehetővé teszi, hogy a meglévő internetes kapcsolattal, többek között a mobil kapcsolatok hang kommunikáció engedélyezése az alkalmazások.

## <a id="Pricing"></a>Twilio árak és a különleges ajánlatokkal
Információk a díjszabásról Twilio érhető el: [Twilio árképzési][twilio_pricing]. Az Azure-ügyfelek egy [a különleges ajánlat][special_offer]: 1000 szövegek szabad követel vagy bejövő perc 1000. Iratkozzon fel a szolgáltatásokat, vagy további információért látogasson el [http://ahoy.twilio.com/azure][special_offer].

## <a id="Concepts"></a>Alapfogalmak
A Twilio API egy RESTful API, hang- és SMS-funkciókat biztosít az alkalmazások számára. Ügyféloldali kódtáraknál érhetők el több nyelven is; az útmutató, [Twilio API függvénytárai][twilio_libraries].

A Twilio API fő szempontjait Twilio-műveletek és Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-műveletek
Az API lehetővé teszi, hogy a Twilio használja műveletek; például a  **&lt;szóbeli&gt;**  parancs utasítja a Twilio hallhatóan hívás az üzenet kézbesítését.

A Twilio-műveletek listáját a következő:

* **&lt;Telefonos kapcsolat&gt;**: a hívó kapcsolódik egy másik telefonon.
* **&lt;Gyűjtsön&gt;**: adta meg a telefon billentyűzetén számjegyek gyűjti.
* **&lt;Vonalbontás&gt;**: hívás véget ér.
* **&lt;Lejátszási&gt;**: hangfájl lejátszása.
* **&lt;Várólista&gt;**: vegye fel a hívók várólistába.
* **&lt;Felfüggesztés&gt;**: Csendes megvárja-e a megadott számú másodpercnél tovább.
* **&lt;Rekord&gt;**: a hívó hang rögzíti, és a felvétel tartalmazó fájl URL-címet adja vissza.
* **&lt;Átirányítási&gt;**: hívás vagy SMS irányítását átviszi a TwiML egy másik URL-címen.
* **&lt;Elutasítása&gt;**: a Twilio-szám egy bejövő hívás elutasítása a nélkül, számlázási.
* **&lt;Tegyük fel például&gt;**: konvertálja szöveg-beszéd átalakítás hívás készült.
* **&lt;SMS&gt;**: SMS üzenetet küld.

### <a id="TwiML"></a>TwiML
TwiML olyan XML-alapú utasítások a Twilio-műveletek, amely tájékoztatja a Twilio hogyan lehet feldolgozni a hívást vagy SMS alapján.

Tegyük fel, a következő TwiML a szöveg volna átalakítása **Hello World!** a beszédfelismerés.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Ha egy alkalmazás meghívja a Twilio API-t, az API paraméterek egyike az URL-címet, a TwiML választ ad vissza. Fejlesztési célra a Twilio által megadott URL-címek segítségével az alkalmazások által használt TwiML visszajelzést. Akkor is elhelyezheti a TwiML válaszok létrehozásához a saját URL-címeket, és egy másik lehetőség az **TwiMLResponse** objektum.

Twilio műveletek, az attribútumokat és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Amikor készen áll arra, hogy a Twilio-fiókot, feliratkozhat [próbálja Twilio][try_twilio]. Egy ingyenes fiókot kezdődnie, és később frissítse a fiókját.

Amikor regisztrál egy Twilio-fiókja, kapni fog egy Fiókazonosító és egy hitelesítési jogkivonatot. Is szükség lesz Twilio API-hívások indítása. Jogosulatlan hozzáférés elkerülése érdekében a fiókját, hogy a hitelesítési jogkivonat biztonságának megőrzése. A Fiókazonosítót és a hitelesítési jogkivonat megtekinthető a rendszer a [Twilio-konzol][twilio_console], a mezőket, címkével **fiók SID** és **hitelesítési JOGKIVONAT**, illetve.

## <a id="create_app"></a>Java-alkalmazás létrehozása
1. Szerezze be a Twilio-JAR, és adja hozzá a Java build elérési útját és a WAR-telepítési szerelvény. A [https://github.com/twilio/twilio-java][twilio_java], töltse le a GitHub-adatforrások és létrehozhat saját JAR, vagy egy előre elkészített JAR (vagy anélkül függőségek) letöltése.
2. Győződjön meg arról a JDK **cacerts** keystore az MD5 ujjlenyomat 67:CB:9 D Equifax biztonságos hitelesítésszolgáltató tanúsítványát tartalmazza: (a sorozatszám 35:DE:F4:CF és az SHA1 C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 ujjlenyomat D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Ez az tanúsítvány hitelesítésszolgáltatói tanúsítványa a [https://api.twilio.com] [ twilio_api_service] szolgáltatást, amelyet a Twilio API-k használatakor nevezik. További információ biztosítása a JDK **cacerts** keystore tartalmaz a megfelelő CA-tanúsítvány című [tanúsítvány hozzáadása a Java Hitelesítésszolgáltatói tanúsítványok tárolójába][add_ca_cert].

Részletes utasítások a Twilio ügyféloldali kódtára a Javához készült érhetők el [hogyan végezheti el a telefonhívás használatával Twilio Azure Java-alkalmazásokban][howto_phonecall_java].

## <a id="configure_app"></a>Állítsa be az alkalmazását Twilio-könyvtárak használatára
A kód is hozzáadhat **importálása** utasításokat a felső részen a Twilio-csomagokat vagy az alkalmazásban használni kívánt osztályok számára a forrásfájlok.

Java forrásfájljait tároló:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Java kiszolgáló lap (JSP) forrásfájljait tároló:

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Attól függően, hogy mely Twilio-csomagokat vagy osztályok szeretné használni, a **importálása** utasítások eltérő lehet.

## <a id="howto_make_call"></a>Hogyan: végezhet
A következő bemutatja, hogyan végezheti el egy kimenő hívás használatával a **hívás** osztály. Ezt a kódot a Twilio által biztosított hely is használja a Twilio Markup Language (TwiML) válasz vissza. Helyettesítse a saját értékeit a **a** és **való** telefonszámokat, és győződjön meg arról, hogy ellenőrizze a **a** telefonszám a Twilio-fiók a kód futtatása előtt.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("http://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

További információ a továbbított paraméterek a **Call.creator** módszer, lásd: [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Ahogy azt korábban említettük, ez a kód Twilio által biztosított hely használatával a TwiML választ küldi vissza. Helyette a saját webhely segítségével adja meg a TwiML válasz; További információkért lásd: [TwiML válaszok adja meg az Azure Java-alkalmazások hogyan](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Útmutató: az SMS-üzenet küldése
A következő bemutatja, hogyan használ egy SMS üzenetet küldeni a **üzenet** osztály. A **a** szám **4155992671**, SMS-üzenetek küldéséhez a próba fiókok Twilio biztosítja. A **való** számát a Twilio-fiókját a kód futtatása előtt ellenőrizni kell.

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

További információ a továbbított paraméterek a **Message.creator** módszer, lásd: [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Hogyan: Adja meg a saját webhelyén TwiML válaszok
Ha az alkalmazás kezdeményezi a Twilio API hívása például keresztül a **CallCreator.create** metódus, Twilio küldi a kérést TwiML választ várt URL-címet. A fenti példában a Twilio-megadott URL-címet [http://twimlets.com/message][twimlet_message_url]. (Közben TwiML Web Services való használatra tervezték, megtekintheti a TwiML a böngészőben. Kattintson például [http://twimlets.com/message] [ twimlet_message_url] egy üres megjelenítéséhez  **&lt;válasz&gt;**  elem; másik példaként, kattintson a [http://twimlets.com/message?Message%5B0%5D=Hello%20World%21] [ twimlet_message_url_hello_world] megtekintéséhez egy  **&lt;válasz&gt;**  elem, amely tartalmazza a  **&lt;szóbeli &gt;**  elem.)

Ahelyett, hogy a Twilio által megadott URL-címen, létrehozhat saját URL-cím hely, amely HTTP-válaszokat ad vissza. A hely bármilyen nyelven HTTP-válaszok; visszaadó hozhat létre Ez a témakör azt feltételezi, hogy lesz a JSP lap URL-címet futtató.

A következő JSP lap eredményez, amely szerint TwiML választ **Hello World!** a hívás.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

A következő JSP lap egy TwiML választ, amely szerint a szöveget, több szünet van, és információk szerint a Twilio API-verzió és az Azure szerepkörnév eredményez.

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

A **ApiVersion** paraméter érhető el a Twilio hang kérések (SMS kérelmek nem). A rendelkezésre álló kérelemben szereplő paraméterek Twilio hang-és SMS-kérelmeket, olvassa el <https://www.twilio.com/docs/api/twiml/twilio_request> és <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, illetve. A **RoleName** környezeti változó is rendelkezésre áll egy Azure-telepítés részeként. (Ha hozzá szeretne adni az egyéni környezeti változók, így azok sikerült felvenni a **System.getenv**, lásd a témakör a környezeti változók [vegyes szerepkör konfigurációs beállításai] [ misc_role_config_settings].)

Miután a TwiML visszajelzést beállítva JSP oldal, használja a JSP lap URL-átadott URL-CÍMÉT a **Call.creator** metódust. Például ha egy webes alkalmazás nevű központi telepítése egy Azure MyTwiML üzemeltetett szolgáltatás, és a JSP-oldal neve mytwiml.jsp, az URL-cím adható át **Call.creator** látható a következő módon:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Keresztül van lehetősége, hogy TwiML válaszol a **VoiceResponse** osztályt, amely megtalálható a **com.twilio.twiml** csomag.

Az Azure-ban Java Twilio használatával kapcsolatos további információkért lásd: [hogyan végezheti el a telefonhívás használatával Twilio Azure Java-alkalmazásokban][howto_phonecall_java].

## <a id="AdditionalServices"></a>Útmutató: további Twilio-szolgáltatásokkal
Az itt bemutatott példák Twilio lehetőséget biztosít webes API-k segítségével kihasználhatja az Azure alkalmazásról további Twilio-funkciókat. Teljes részletekért lásd: a [Twilio API-JÁNAK dokumentációja][twilio_api_documentation].

## <a id="NextSteps"></a>Következő lépések
Most, hogy megismerte a Twilio szolgáltatáshoz alapjait, az alábbi hivatkozásokból további:

* [Twilio biztonsági irányelvek][twilio_security_guidelines]
* [Twilio útmutató és példakódot][twilio_howtos]
* [Twilio gyors üzembe helyezési oktatóanyag][twilio_quickstarts]
* [A Githubon Twilio][twilio_on_github]
* [Kérdezze meg a Twilio-támogatás][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/docs
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
