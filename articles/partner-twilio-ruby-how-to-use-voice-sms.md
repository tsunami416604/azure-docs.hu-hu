---
title: A Twilio használata Hanghívási és SMS (Ruby) hogyan |} A Microsoft Docs
description: Útmutató a telefonhívás, és a Twilio API-szolgáltatással egy SMS-üzenet küldése az Azure-ban. Ruby nyelven írt kódmintákat.
services: ''
documentationcenter: ruby
author: devinrader
manager: twilio
editor: ''
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 128eff5f628a0a4bf9f67e988ab7d3e2f22ed767
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823741"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Hogyan lehet a Twilio használata Hanghívási és SMS-funkciókhoz Ruby nyelven
Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat, a Twilio API-t szolgáltatással az Azure-ban. Az ismertetett forgatókönyvek között megtalálható, így a telefonhívás, és a egy rövid üzenetet szolgáltatást (SMS) üzenetet küld. A Twilio és az alkalmazások használata hanghívási és SMS további információkért lásd: a [lépések](#NextSteps) szakaszban.

## <a id="WhatIs"></a>Mit jelent a Twilio?
A Twilio egy telefonos webszolgáltatás API-t, amely lehetővé teszi a meglévő webes nyelv és képességek használata hanghívási és SMS-alkalmazásokat hozhat létre. A Twilio egy külső szolgáltatás (nem az Azure szolgáltatásai és nem Microsoft-termékek).

**A twilio használata Hanghívási** lehetővé teszi, hogy telefonhívásokat fogadja, és az alkalmazások. **A Twilio SMS** lehetővé teszi, hogy az alkalmazások és az SMS-üzenetek fogadása. **A Twilio-ügyfél** lehetővé teszi az alkalmazások hangalapú kommunikációt folytatni a meglévő internetkapcsolatokon keresztül, beleértve a mobilhálózati kapcsolatokat engedélyezi.

## <a id="Pricing"></a>A Twilio-díjszabás és ajánlatok küldésére
Információ a Twilio-díjszabás érhető el: [Twilio díjszabás][twilio_pricing]. Az Azure-ügyfelek kap egy [a különleges ajánlat][special_offer]: 1000 szövegek ingyenes kreditet, vagy bejövő perc 1000. Iratkozzon fel az ajánlatra, illetve további információért látogasson el [ http://ahoy.twilio.com/azure ] [ special_offer].  

## <a id="Concepts"></a>Fogalmak
A Twilio API egy RESTful API-t hanghívási és SMS-funkciókat biztosító alkalmazások. Ügyfélkódtárai több nyelven érhetők el egy listát lásd: [Twilio API függvénytárai][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML XML-alapú utasítások, amely tájékoztatja a hívás feldolgozása Twilio vagy SMS.

Tegyük fel, a következő TwiML konvertálná szöveg **Hello World** át.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Az összes TwiML dokumentumok rendelkeznek `<Response>` , azok gyökérelem. Itt használatával a Twilio-műveletek az alkalmazás viselkedésének megadása.

### <a id="Verbs"></a>TwiML műveletek
A Twilio-műveletek XML-címkéket, hogy mit kell tudniuk Twilio **tegye**. Ha például a **&lt;Say&gt;** művelet arra utasítja a Twilio hallhatóan kézbesíteni az üzenetet hívás. 

Az alábbiakban látható egy Twilio-műveletek listájához.

* **&lt;Tárcsázás&gt;**: a hívó kapcsolódik egy másik telefonszámot.
* **&lt;Gyűjtse össze&gt;**: összegyűjti a telefon billentyűzeten beírt számjegyeket.
* **&lt;Vonalbontás&gt;**: ér véget a hívást.
* **&lt;Play&gt;**: fájlból játszik le egy hangfájlt.
* **&lt;Felfüggesztés&gt;**: Csendes megvárja a megadott számú másodperc.
* **&lt;Rekord&gt;**: a hívó hangalapú rögzíti, és a felvétel tartalmazó fájl URL-címet adja vissza.
* **&lt;Átirányítási&gt;**: átadja a vezérlést a hívást vagy SMS a TwiML egy másik URL-címen.
* **&lt;Elutasítás&gt;**: a Twilio-számra egy bejövő hívás elutasítása a számlázási, nélkül
* **&lt;Tegyük fel, hogy&gt;**: alakíthatók át egymásba szöveg-beszéd átalakítás, amely a hívást.
* **&lt;SMS&gt;**: SMS üzenetet küld.

A Twilio műveletek, az attribútumokat és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [Twilio API-t][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Amikor készen áll a Twilio-fiók létrehozása, regisztráció: [próbálja meg Twilio][try_twilio]. Kezdje egy ingyenes fiókot, és később frissítse a fiókját.

Amikor regisztrál egy Twilio-fiók, az alkalmazás ingyenes telefonszám fog kapni. Egy fiók biztonsági azonosítója és a egy hitelesítési tokent is kap. Mindkét lesz szükség a Twilio API-hívásokat. Fiókja a jogosulatlan hozzáférés megakadályozása érdekében, hogy a hitelesítési jogkivonat biztonságának megőrzése. A fiók SID-je teljesítményobjektumok és -hitelesítési token, a [Twilio-fiók oldalán][twilio_account], a címkével ellátott mezőket **fiók SID** és **hitelesítési JOGKIVONATÁT**, jelölik.

### <a id="VerifyPhoneNumbers"></a>Telefonszám ellenőrzése
A szám kívül, adja meg Twilio, azt is ellenőrizheti, hogy számok által vezérelt (azaz a mobiltelefonjára vagy otthoni telefonszámát) használható az alkalmazásokban. 

Telefonszám ellenőrzése a további információkért lásd: [számok kezelésére][verify_phone].

## <a id="create_app"></a>Ruby alkalmazás létrehozása
Egy Ruby-alkalmazás, amely a Twilio szolgáltatás használja, és az Azure-ban fut-e semmiben nem különbözik bármely más Ruby-alkalmazás, amely a Twilio szolgáltatás használ. Bár a Twilio-szolgáltatások RESTful, és nem hívható meg a Ruby többféle módon, ez a cikk a Twilio-szolgáltatások használata összpontosítanak [Twilio segítő kódtára a Rubyhoz][twilio_ruby].

Először [beállítás egy új Azure Linux virtuális gép] [ azure_vm_setup] segítségével egy állomás új Ruby webalkalmazása számára. Hagyja figyelmen kívül használata esetén a Rails-alkalmazás, csak beállítás a virtuális gép létrehozásának lépéseit. Ellenőrizze, hogy egy külső port a 80-as és a egy belső port 5000-es egy végpontot hoz létre.

Az alábbi példákban fogjuk használni [Sinatra][sinatra], egy nagyon egyszerű webes keretrendszer, a Ruby. Azonban természetesen használhat a Twilio segédkódtárba helyezni a Rubyhoz bármilyen más webes keretrendszer használatával, többek között a Ruby on Rails.

SSH-t az új virtuális gép és a egy könyvtárat, az új alkalmazás létrehozása. Belüli könyvtárban hozzon létre egy Gemfile nevű fájlt, és másolja az alábbi kódot a fájlba:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

A parancssorban futtassa `bundle install`. Ez telepíti az alábbi függőségeket. Ezután hozzon létre egy fájlt, nevű `web.rb`. Ez lesz, ahol a webalkalmazás kódját él. Illessze be a következő kód azt:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Ezen a ponton kell látnia a futtassa a parancsot `ruby web.rb -p 5000`. Ez lesz léptetéses – legfeljebb 5000-es porton lévő kis webkiszolgáló. Böngészhet az alkalmazás a böngészőben az URL-cím felkeresésével, beállítás az Azure virtuális gép kell lennie. A webalkalmazást a böngészőben érheti el, ha készen áll a Twilio-alkalmazás elindításához.

## <a id="configure_app"></a>A Twilio használata az alkalmazás konfigurálása
A webalkalmazás a Twilio-könyvtár használata frissítésével konfigurálhatja a `Gemfile` tartalmazza ezt a sort:

    gem 'twilio-ruby'

A parancssorban futtassa `bundle install`. Most nyissa meg a `web.rb` ezt a felső sort is beleértve:

    require 'twilio-ruby'

Most már készen használható a Twilio segítő kódtára a Rubyhoz, a webalkalmazásban.

## <a id="howto_make_call"></a>Hogyan: végezhet
A következő bemutatja, hogyan végezhet. Fő fogalmak használatával a Twilio segítő kódtára a Rubyhoz a REST API-hívásokat, és a renderelési TwiML tartalmazza. Helyettesítse be a saját értékeit a **a** és **való** telefonszámai, és győződjön meg arról, hogy ellenőrizze a **a** telefonszám a Twilio-fiók, a kód futtatása előtt.

Adja hozzá ezt a funkciót `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the call.
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

Ha Ön nyílt felfelé `http://yourdomain.cloudapp.net/make_call` egy böngészőben, amely elindítja a Twilio API-t, hogy a telefonhívás hívása. Az első két paraméterei `client.account.calls.create` magától értetődő viszonylag: a szám a hívás nem `from` és szám a hívás nem `to`. 

A harmadik paraméter (`url`) Twilio kéri, hogy mi a teendő, ha van csatlakoztatva van a hívás útmutatást kaphat az URL-cím. Ebben az esetben mi egy URL-cím beállítása (`http://yourdomain.cloudapp.net`), amely egy egyszerű TwiML dokumentumot ad vissza, és használja a `<Say>` néhány szöveg-hang transzformációs és a hívási fogadó személy legyen például "Hello alfaja" művelet.

## <a id="howto_receive_sms"></a>Útmutató: az SMS-üzenet fogadása
Az előző példában azt kezdeményezett egy **kimenő** telefonhívás. Ez alkalommal, használja a telefonszámot, amelyet a Twilio megadott során regisztrációs folyamatot egy **bejövő** SMS-üzenet.

Először is, jelentkezzen be a [Twilio-irányítópult][twilio_account]. Kattintson a "Számok" a felső navigációs, és kattintson a a Twilio-szám van megadva. Látni fogja a két URL-címeket, amelyeket konfigurálhat. Egy hang-kérelem URL-CÍMÉT és a egy SMS kérelem URL-címe. Ezek a Twilio-hívások, ha telefonos hívást kezdeményez vagy SMS elküldenék a szám URL-címeket. Az URL-címeket "webhook" is nevezik.

Azt szeretnénk, bejövő SMS-üzenetek feldolgozása, úgyhogy frissítése az URL-címe `http://yourdomain.cloudapp.net/sms_url`. Lépjen tovább, és kattintson a módosítások mentése az oldal alján. Most, a biztonsági `web.rb` hozzunk program kezelje ezt az alkalmazást:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

A módosítás elvégzése után győződjön meg arról, hogy indítsa újra a webalkalmazást. Most vegye ki a telefonjára, és SMS küldése a Twilio-számra. Azonnal kapja meg az SMS-választ, amely szerint a "Hey, a ping parancs Köszönjük! A Twilio és az Azure rock! ".

## <a id="additional_services"></a>How to: további Twilio-szolgáltatások használata
Itt látható példák, mellett Twilio kínál a webes API-kat használhatja az Azure-alkalmazásból további Twilio funkciói kihasználhatók. További részletek: a [Twilio API-dokumentáció][twilio_api_documentation].

### <a id="NextSteps"></a>Következő lépések
Most, hogy megismerte a Twilio szolgáltatás alapjait, kövesse az alábbi hivatkozások további:

* [A Twilio szolgáltatásra vonatkozó biztonsági irányelvek][twilio_security_guidelines]
* [A Twilio HowTos és példakód][twilio_howtos]
* [A Twilio-Gyorsútmutatók][twilio_quickstarts] 
* [Twilio a Githubon][twilio_on_github]
* [Beszéljen a Twilio-támogatás][twilio_support]

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
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
