---
title: Twilio használata Hanghívási és SMS (Java) |} A Microsoft Docs
description: Útmutató a telefonhívás, és a Twilio API-szolgáltatással egy SMS-üzenet küldése az Azure-ban. A Java nyelven írt kódmintákat.
services: ''
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
ms.openlocfilehash: 386b4b8440c74f6599e7147996b5843ea0f67e68
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423364"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Hogyan lehet a Twilio használata Hanghívási és SMS-funkciókhoz Java nyelven
Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat, a Twilio API-t szolgáltatással az Azure-ban. Az ismertetett forgatókönyvek között megtalálható, így a telefonhívás, és a egy rövid üzenetet szolgáltatást (SMS) üzenetet küld. A Twilio és az alkalmazások használata hanghívási és SMS további információkért lásd: a [lépések](#NextSteps) szakaszban.

## <a id="WhatIs"></a>Mit jelent a Twilio?
A Twilio egy telefonos webszolgáltatás API-t, amely lehetővé teszi a meglévő webes nyelv és képességek használata hanghívási és SMS-alkalmazásokat hozhat létre. A Twilio egy külső szolgáltatás (nem az Azure szolgáltatásai és nem Microsoft-termékek).

**A twilio használata Hanghívási** lehetővé teszi, hogy telefonhívásokat fogadja, és az alkalmazások. **A Twilio SMS** lehetővé teszi, hogy az alkalmazások és az SMS-üzenetek fogadása. **A Twilio-ügyfél** lehetővé teszi az alkalmazások hangalapú kommunikációt folytatni a meglévő internetkapcsolatokon keresztül, beleértve a mobilhálózati kapcsolatokat engedélyezi.

## <a id="Pricing"></a>A Twilio-díjszabás és ajánlatok küldésére
Információ a Twilio-díjszabás érhető el: [Twilio díjszabás][twilio_pricing]. Az Azure-ügyfelek kap egy [a különleges ajánlat][special_offer]: 1000 szövegek ingyenes kreditet, vagy bejövő perc 1000. Iratkozzon fel az ajánlatra, illetve további információért látogasson el [ https://ahoy.twilio.com/azure ] [ special_offer].

## <a id="Concepts"></a>Fogalmak
A Twilio API egy RESTful API-t hanghívási és SMS-funkciókat biztosító alkalmazások. Ügyfélkódtárai több nyelven érhetők el egy listát lásd: [Twilio API függvénytárai][twilio_libraries].

Fontos szempontjai a Twilio API-t a, Twilio-műveletek és a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>A Twilio-műveletek
Az API-t felhasznál Twilio műveleteket; Ha például a **&lt;Say&gt;** művelet arra utasítja a Twilio hallhatóan kézbesíteni az üzenetet hívás.

Az alábbiakban látható egy Twilio-műveletek listájához.

* **&lt;Tárcsázás&gt;**: a hívó kapcsolódik egy másik telefonszámot.
* **&lt;Gyűjtse össze&gt;**: összegyűjti a telefon billentyűzeten beírt számjegyeket.
* **&lt;Vonalbontás&gt;**: ér véget a hívást.
* **&lt;Play&gt;**: fájlból játszik le egy hangfájlt.
* **&lt;Várólista&gt;**: Adja hozzá a hívók egy üzenetsorba.
* **&lt;Felfüggesztés&gt;**: Csendes megvárja a megadott számú másodperc.
* **&lt;Rekord&gt;**: a hívó hangalapú rögzíti, és a felvétel tartalmazó fájl URL-címet adja vissza.
* **&lt;Átirányítási&gt;**: átadja a vezérlést a hívást vagy SMS a TwiML egy másik URL-címen.
* **&lt;Elutasítás&gt;**: a Twilio-számra egy bejövő hívás elutasítása a számlázási, nélkül.
* **&lt;Tegyük fel, hogy&gt;**: alakíthatók át egymásba szöveg-beszéd átalakítás, amely a hívást.
* **&lt;SMS&gt;**: SMS üzenetet küld.

### <a id="TwiML"></a>TwiML
TwiML XML-alapú utasítások alapján a Twilio-műveleteket tartalmazó tájékoztatja a hívás feldolgozása Twilio vagy SMS.

Tegyük fel, a következő TwiML konvertálná szöveg **Hello World!** a beszédfelismerés.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Amikor az alkalmazás meghívja a Twilio API-t, az API-paraméterek egyik az URL-cím, amely a TwiML választ ad vissza. Fejlesztési célokra a Twilio által megadott URL-címek segítségével az alkalmazások által használt TwiML visszajelzést. Akkor is elhelyezheti, a saját URL-címek a TwiML válaszok előállításához, és a egy másik lehetőség a **TwiMLResponse** objektum.

A Twilio műveletek, az attribútumokat és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [Twilio API-t][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Amikor készen áll a Twilio-fiók létrehozása, regisztráció: [próbálja meg Twilio][try_twilio]. Kezdje egy ingyenes fiókot, és később frissítse a fiókját.

Amikor regisztrál egy Twilio-fiók, kap egy fiók Azonosítóját és a egy hitelesítési tokent. Mindkét lesz szükség a Twilio API-hívásokat. Fiókja a jogosulatlan hozzáférés megakadályozása érdekében, hogy a hitelesítési jogkivonat biztonságának megőrzése. A fiók Azonosítóját és a hitelesítési jogkivonat teljesítményobjektumok, a [Twilio-konzol][twilio_console], a címkével ellátott mezőket **fiók SID** és **hitelesítési JOGKIVONATÁT**, illetve.

## <a id="create_app"></a>Java-alkalmazás létrehozása
1. Szerezze be a Twilio JAR, és adja hozzá a Java build elérési út és a WAR-üzembe helyezési sestavení. A [ https://github.com/twilio/twilio-java ] [ twilio_java], töltse le a GitHub-források, és létrehozhat saját JAR, vagy egy előre elkészített JAR (vagy anélkül függőségek) letöltése.
2. Győződjön meg, hogy a JDK **cacerts** keystore az MD5-tel ujjlenyomat 67:CB:9 D Equifax biztonságos hitelesítésszolgáltató tanúsítványát tartalmazza: (a sorozatszám tekinthető 35:DE:F4:CF és az SHA1 C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 ujjlenyomattal történő D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Ez a tanúsítvány hitelesítésszolgáltató (CA) tanúsítványa az [ https://api.twilio.com ] [ twilio_api_service] szolgáltatás, amely nevezzük, amikor a Twilio API-kkal. További információ biztosítása a JDK **cacerts** keystore tartalmazza a megfelelő CA-tanúsítvány, lásd: [tanúsítvány hozzáadása a Java Hitelesítésszolgáltatói tanúsítvány Store][add_ca_cert].

Részletes útmutatás a Twilio ügyféloldali kódtára a Javához készült esetén érhető el [egy telefonhívás használatával Twilio Java-alkalmazásokban az Azure-on győződjön meg arról, hogyan][howto_phonecall_java].

## <a id="configure_app"></a>Az alkalmazás használhatja a Twilio szalagtárak konfigurálása
A kód is hozzáadhat **importálása** a forrásfájljait a Twilio-csomagokat vagy az alkalmazásban használni kívánt osztályokat tetején lévő utasításokat.

Java forrásfájlokhoz:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Java kiszolgálói oldal (JSP) forrásfájlokhoz:

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Attól függően, melyik Twilio-csomagokat vagy osztályok szeretné használni, a **importálása** utasítások eltérőek lehetnek.

## <a id="howto_make_call"></a>Hogyan: végezhet
A következőket mutatja be, hogy egy kimenő hívás használatával a **hívás** osztály. Ez a kód is egy Twilio által biztosított helyet használ a Twilio Markup Language (TwiML) választ adja vissza. Helyettesítse be a saját értékeit a **a** és **való** telefonszámai, és győződjön meg arról, hogy ellenőrizze a **a** telefonszám a Twilio-fiók, a kód futtatása előtt.

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

További információ az átadott paraméterek a **Call.creator** metódus, lásd: [ https://www.twilio.com/docs/api/rest/making-calls ] [ twilio_rest_making_calls].

Ahogy már említettük, ez a kód egy Twilio által biztosított hely használatával TwiML a választ adja. Ehelyett használhatja a saját hely adja meg a TwiML válasz; További információkért lásd: [TwiML válaszok adjon meg egy Java-alkalmazás az Azure-ban hogyan](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Útmutató: az SMS-üzenet küldése
A következő példa használatával SMS üzenet küldése a **üzenet** osztály. A **a** szám **4155992671**, Twilio próbaverziós fiókok küldése SMS-ek biztosítják. A **való** száma a Twilio-fiók, a kód futtatása előtt ellenőrizni kell.

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

További információ az átadott paraméterek a **Message.creator** metódus, lásd: [ https://www.twilio.com/docs/api/rest/sending-sms ] [ twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Hogyan: Adja meg a saját webhelyén TwiML válaszok
Ha az alkalmazás kezdeményezi a hívást a Twilio API-hoz, például keresztül a **CallCreator.create** metódus, Twilio a kérelmet küld egy URL-címet, amely várhatóan egy TwiML választ adja vissza. A fenti példában a Twilio-megadott URL-címet használ [ https://twimlets.com/message ] [ twimlet_message_url]. (Amíg TwiML használata webszolgáltatások lett tervezve, megtekintheti a TwiML a böngészőben. Kattintson például [ https://twimlets.com/message ] [ twimlet_message_url] megtekintéséhez egy üres **&lt;válasz&gt;** elem; másik példaként, kattintson a [ https://twimlets.com/message?Message%5B0%5D=Hello%20World%21 ] [ twimlet_message_url_hello_world] megtekintéséhez egy **&lt;válasz&gt;** elem, amely tartalmazza a **&lt;Say&gt;** elem.)

Így nem kell hagyatkoznia a Twilio-megadott URL-címet, a saját URL-címet a hely által visszaadott HTTP-válaszok is létrehozhat. A hely által visszaadott HTTP-válaszok; bármilyen nyelven hozhat létre Ez a témakör azt feltételezi, hogy fogja üzemeltetni a JSP-oldal URL-CÍMÉT.

A következő új JSP-oldal eredményez, amely szerint TwiML választ **Hello World!** a hívás.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

A következő új JSP-oldal, amely szerint a valamilyen szöveget, több szünetel rendelkezik és a Twilio API-verzió és az Azure szerepkörnév kapcsolatos információk szerint TwiML választ eredményez.

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

A **ApiVersion** paraméter Twilio hangutasítások (nem az SMS-kérelmek) érhető el. A twilio használata hanghívási és SMS-kérések elérhető kérelem paramétereit, olvassa el <https://www.twilio.com/docs/api/twiml/twilio_request> és <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, illetve. A **RoleName** környezeti változóban érhető el az Azure-telepítés részeként. (Ha azt szeretné, adja hozzá az egyéni környezeti változókat, hogy azok sikerült visszajusson az **System.getenv**, lásd a következő szakasz a környezeti változók [egyéb szerepkör-konfigurációs beállítások] [ misc_role_config_settings].)

Miután a JSP-oldalt állított TwiML válaszokat nyújt, használja a JSP-oldal URL-CÍMÉT az átadott URL-CÍMÉT a **Call.creator** metódust. Például ha nevű webalkalmazást az Azure-ban üzembe helyezett MyTwiML üzemeltetett szolgáltatás, és a JSP-oldal neve mytwiml.jsp, az URL-cím adható át **Call.creator** az alábbiakban látható módon:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Keresztül van egy másik lehetőség a TwiML válaszol a **VoiceResponse** osztály, amely a **com.twilio.twiml** csomagot.

Az Azure-ban a Java a Twilio használatával kapcsolatos további információkért lásd: [egy telefonhívás használatával Twilio Java-alkalmazásokban az Azure-on győződjön meg arról, hogyan][howto_phonecall_java].

## <a id="AdditionalServices"></a>How to: további Twilio-szolgáltatások használata
Itt látható példák, mellett Twilio kínál a webes API-kat használhatja az Azure-alkalmazásból további Twilio funkciói kihasználhatók. További részletek: a [Twilio API-dokumentáció][twilio_api_documentation].

## <a id="NextSteps"></a>Következő lépések
Most, hogy megismerte a Twilio szolgáltatás alapjait, kövesse az alábbi hivatkozások további:

* [A Twilio szolgáltatásra vonatkozó biztonsági irányelvek][twilio_security_guidelines]
* [A Twilio útmutatók és példakód][twilio_howtos]
* [A Twilio-Gyorsútmutatók][twilio_quickstarts]
* [Twilio a Githubon][twilio_on_github]
* [Beszéljen a Twilio-támogatás][twilio_support]

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
