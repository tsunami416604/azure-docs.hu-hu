---
title: "Hang-és SMS (Ruby) Twilio használata |} Microsoft Docs"
description: "Útmutató a telefonhívás, és a Twilio API szolgáltatás SMS üzenet küldése az Azure-on. Kódminták Ruby."
services: 
documentationcenter: ruby
author: devinrader
manager: twilio
editor: 
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 69e50e7fe0e1f302e96c309878b8dea6869dff4a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Hang-és SMS képességei Ruby Twilio használata
Ez az útmutató bemutatja, hogyan Azure a Twilio API szolgáltatás közös programozási feladatok elvégzéséhez. A tárgyalt forgatókönyvekben szerepel, így a telefonhívás, és egy rövid üzenetet szolgáltatás (SMS) üzenet küldésekor. A Twilio- és hang- és SMS használata az alkalmazások további információkért lásd: a [lépések](#NextSteps) szakasz.

## <a id="WhatIs"></a>Mi az, hogy a Twilio?
Twilio egy telefonos webszolgáltatás API, amely lehetővé teszi, hogy a meglévő webes nyelv és képességeik felhasználásával hang- és SMS alkalmazások készítéséhez. Twilio egy olyan külső szolgáltatás (nem az Azure szolgáltatásai és nem Microsoft-termékek).

**Twilio hang** lehetővé teszi, hogy az alkalmazások és a telefonhívásokat fogadja. **Twilio SMS** lehetővé teszi, hogy az alkalmazások és az SMS-üzeneteket fogadni. **Twilio-ügyfél** lehetővé teszi, hogy a meglévő internetes kapcsolattal, többek között a mobil kapcsolatok hang kommunikáció engedélyezése az alkalmazások.

## <a id="Pricing"></a>Twilio árak és a különleges ajánlatokkal
Információk a díjszabásról Twilio érhető el: [Twilio árképzési][twilio_pricing]. Az Azure-ügyfelek egy [a különleges ajánlat][special_offer]: 1000 szövegek szabad követel vagy bejövő perc 1000. Iratkozzon fel a szolgáltatásokat, vagy további információért látogasson el [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Alapfogalmak
A Twilio API egy RESTful API, hang- és SMS-funkciókat biztosít az alkalmazások számára. Ügyféloldali kódtáraknál érhetők el több nyelven is; az útmutató, [Twilio API függvénytárai][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML olyan XML-alapú utasításokat, amelyek tájékoztatják a Twilio hogyan lehet feldolgozni a hívást vagy SMS készlete.

Tegyük fel, a következő TwiML a szöveg volna átalakítása **Hello World** beszéddé.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Minden TwiML dokumentum rendelkezik `<Response>` , mint a legfelső szintű elem. Ott segítségével Twilio-műveletek az alkalmazás viselkedésének meghatározása.

### <a id="Verbs"></a>TwiML műveletek
Twilio-műveletek a következők: XML-címkék, hogy a Twilio arról, mit kell **tegye**. Például a  **&lt;szóbeli&gt;**  parancs utasítja a Twilio hallhatóan hívás az üzenet kézbesítését. 

A Twilio-műveletek listáját a következő:

* **&lt;Telefonos kapcsolat&gt;**: a hívó kapcsolódik egy másik telefonon.
* **&lt;Gyűjtsön&gt;**: adta meg a telefon billentyűzetén számjegyek gyűjti.
* **&lt;Vonalbontás&gt;**: hívás véget ér.
* **&lt;Lejátszási&gt;**: hangfájl lejátszása.
* **&lt;Felfüggesztés&gt;**: Csendes megvárja-e a megadott számú másodpercnél tovább.
* **&lt;Rekord&gt;**: a hívó hang rögzíti, és a felvétel tartalmazó fájl URL-címet adja vissza.
* **&lt;Átirányítási&gt;**: hívás vagy SMS irányítását átviszi a TwiML egy másik URL-címen.
* **&lt;Elutasítása&gt;**: a Twilio-szám egy bejövő hívás elutasítása a nélkül, számlázási
* **&lt;Tegyük fel például&gt;**: konvertálja szöveg-beszéd átalakítás hívás készült.
* **&lt;SMS&gt;**: SMS üzenetet küld.

Twilio műveletek, az attribútumokat és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Amikor készen áll arra, hogy a Twilio-fiókot, feliratkozhat [próbálja Twilio][try_twilio]. Egy ingyenes fiókot kezdődnie, és később frissítse a fiókját.

Amikor regisztrál egy Twilio-fiókot, később is lesz egy ingyenes telefonszámot az alkalmazáshoz. Egy fiók SID azonosítója és egy hitelesítési jogkivonatot is kap. Is szükség lesz Twilio API-hívások indítása. Jogosulatlan hozzáférés elkerülése érdekében a fiókját, hogy a hitelesítési jogkivonat biztonságának megőrzése. A fiók SID azonosítója és a hitelesítési jogkivonat is látható, a [Twilio fióklapját][twilio_account], a mezőket, címkével **fiók SID** és **hitelesítési JOGKIVONAT**, kulcsattribútumokkal.

### <a id="VerifyPhoneNumbers"></a>Ellenőrizze a telefonszámok
A szám kívül lehetősége van által Twilio, azt is ellenőrizheti, hogy legyen az alkalmazások által vezérelt (azaz a mobiltelefon vagy otthoni telefonszámot) számok. 

Hogyan lehet ellenőrizni a telefonszám további információkért lásd: [kezelése számok][verify_phone].

## <a id="create_app"></a>Ruby-alkalmazás létrehozása
Egy Ruby alkalmazás, amely a Twilio-szolgáltatás és az Azure-ban fut. ugyanolyan helyzetet teremt, mint bármely más Ruby használó alkalmazások a Twilio szolgáltatáshoz. Amíg Twilio-szolgáltatások RESTful, és a Ruby többféleképpen hívható, ez a cikk a Twilio-szolgáltatások használatával összpontosítanak [Twilio segítő kódtára a Rubyhoz][twilio_ruby].

Első, [beállításról egy új Azure Linux virtuális gép] [ azure_vm_setup] új Ruby webes alkalmazás állomás nevében járhasson el. Hagyja figyelmen kívül használata esetén egy sínek alkalmazáshoz, csak beállításról a virtuális gép létrehozásának lépéseit. Ellenőrizze, hogy egy külső port a 80-as és egy belső portjával 5000 hozzon létre egy végpontot.

Az alábbi példákban fogjuk használni [Sinatra][sinatra], egy nagyon egyszerű webes keretrendszer, a Ruby. De alapértékekkel használata a Twilio-segédkódtárba helyezni a Rubyhoz bármely más webes keretrendszer, többek között a Ruby sínen.

SSH-ból az új virtuális gép, és hozzon létre egy könyvtárat, az új alkalmazás. Hozzon létre egy Gemfile nevű fájlt, és a következő kódot bemásolhatja belül könyvtárban:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

A parancssorban futtassa `bundle install`. Ez telepíti az alábbi függőségeket. Ezután hozzon létre egy nevű fájlt `web.rb`. Ez lesz, ahol a kódot a webalkalmazás él. Az alábbi kód beillesztése azt:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Ekkor meg kell majd futtassa a parancsot `ruby web.rb -p 5000`. Ez fogja léptetéses-legfeljebb 5000-es port kis webkiszolgáló. Meg kell tudni keresse ki ezt az alkalmazást a böngészőben az URL-cím felkeresésével meg beállításról az Azure virtuális gép. A webalkalmazás a böngészőben érheti el, ha készen áll a Twilio-alkalmazások készítéséhez.

## <a id="configure_app"></a>Állítsa be az alkalmazását Twilio használata
Beállíthatja, hogy a Twilio-könyvtár használata frissítésével a webalkalmazás a `Gemfile` ezt a sort tartalmazza:

    gem 'twilio-ruby'

A parancssorban futtassa `bundle install`. Most nyissa meg a `web.rb` és a sor tetején, így:

    require 'twilio-ruby'

Most már készen is szeretné használni a Twilio-segédkódtárba helyezni a Ruby a webes alkalmazást.

## <a id="howto_make_call"></a>Hogyan: végezhet
A következő bemutatja, hogyan végezhet. Alapfogalmak közé tartozik a REST API-hívások indítása a Twilio segítő kódtára a Rubyhoz segítségével, és TwiML megjelenítése. Helyettesítse a saját értékeit a **a** és **való** telefonszámokat, és győződjön meg arról, hogy ellenőrizze a **a** telefonszám a Twilio-fiók a kód futtatása előtt.

Adja hozzá ezt a funkciót `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";

    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);

      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Ha Ön nyílt felfelé `http://yourdomain.cloudapp.net/make_call` egy böngészőben, amely akkor indul el, a telefonhívás a Twilio API hívása. Az első két paraméterek `client.account.calls.create` magától értetődő viszonylag: száma a hívás nem `from` és szám a hívás nem `to`. 

A harmadik paraméter (`url`) az URL-cím, amelyet Twilio utasításokat hozzáférhet Mi a teendő, ha a csatlakozás igényel. Ebben az esetben azt URL-címet a telepítés (`http://yourdomain.cloudapp.net`), amely egy egyszerű TwiML dokumentumot ad vissza, és használja a `<Say>` hajtsa végre az egyes szöveg-beszéd átalakítás, és mondja ki a "Hello, Monkey" annak a személynek a hívás fogadása.

## <a id="howto_recieve_sms"></a>Útmutató: az SMS-üzenet fogadása
Az előző példában a Microsoft által kezdeményezett egy **kimenő** telefonhívás. Ez alkalommal, most használja a telefonszámot, amelyet a Twilio megadott során regisztrációs folyamat egy **bejövő** SMS-üzenet.

Első, jelentkezzen be a [Twilio-irányítópult][twilio_account]. Kattintson a "Numbers" a felső navigációs, és kattintson a Twilio számára lett megadva. Látni fogja, két URL-címeket, amelyeket konfigurálhat. Egy hang kérelem URL-CÍMÉT és az SMS kérelem URL-CÍMÉT. Ezek a Twilio meghívja a phone kezdeményezték, és az SMS a rendszer elküldi a használni kívánt URL-címek. Az URL-címek "webes hurkok" is nevezik.

Bejövő SMS-üzenetek feldolgozásához, ezért frissíti az URL-CÍMÉT kell `http://yourdomain.cloudapp.net/sms_url`. Lépjen tovább, és kattintson a módosítások mentése a lap alján. Most, újból `web.rb` ennek kezelése az alkalmazás most program:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

A módosítás után győződjön meg arról, hogy indítsa újra a webes alkalmazást. Most vegye ki a telefonjára, és a Twilio-szám SMS küldése. Azonnal kapja meg az SMS-válasz, amely szerint "Hey, a ping Köszönjük! Twilio és az Azure rock! ".

## <a id="additional_services"></a>Útmutató: további Twilio-szolgáltatásokkal
Az itt bemutatott példák Twilio lehetőséget biztosít webes API-k segítségével kihasználhatja az Azure alkalmazásról további Twilio-funkciókat. Teljes részletekért lásd: a [Twilio API-JÁNAK dokumentációja][twilio_api_documentation].

### <a id="NextSteps"></a>Következő lépések
Most, hogy megismerte a Twilio szolgáltatáshoz alapjait, az alábbi hivatkozásokból további:

* [Twilio biztonsági irányelvek][twilio_security_guidelines]
* [Twilio HowTos és példakódot][twilio_howtos]
* [Twilio gyors üzembe helyezési oktatóanyag][twilio_quickstarts] 
* [A Githubon Twilio][twilio_on_github]
* [Kérdezze meg a Twilio-támogatás][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
