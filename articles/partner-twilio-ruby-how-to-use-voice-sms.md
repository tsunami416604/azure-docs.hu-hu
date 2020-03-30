---
title: Hogyan használjuk a Twilio-t hang- és SMS-hez (Ruby) | Microsoft dokumentumok
description: Ismerje meg, hogyan kezdeményezhetsz telefonhívást, és hogyan küldhet SMS-üzenetet az Azure-beli Twilio API-szolgáltatással. Ruby-val írt kódminták.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 4822e6feb29f5a17c653a60937b895ec584e0ee4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637202"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Hogyan használjuk a Twilio-t hang- és SMS-funkciókhoz a Ruby-ban
Ez az útmutató bemutatja, hogyan hajthat végre közös programozási feladatokat az Azure-beli Twilio API-szolgáltatással. A tárgyalt forgatókönyvek közé tartozik a telefonhívás és a rövid üzenetszolgáltatás (SMS) küldése. A Twilio használatával, valamint a hang- és SMS-használattal kapcsolatos további információkért tekintse meg a [Következő lépések](#NextSteps) című szakaszt.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Mi az a Twilio?
A Twilio egy telefonos webszolgáltatás API- amely lehetővé teszi a meglévő webes nyelvek és készségek használatát hang- és SMS-alkalmazások létrehozásához. A Twilio egy külső szolgáltatás (nem Azure-szolgáltatás, és nem Microsoft-termék).

**A Twilio Voice** lehetővé teszi, hogy az alkalmazások telefonhívásokat kezdeményezzenek és fogadjanak. **Twilio SMS** lehetővé teszi, hogy az alkalmazások, hogy és sms-üzeneteket. **A Twilio-ügyfél** lehetővé teszi, hogy az alkalmazások lehetővé tegyék a hangalapú kommunikációt a meglévő internetkapcsolatokkal, beleértve a mobilkapcsolatokat is.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio árképzés és különleges ajánlatok
A Twilio-díjszabásról a [Twilio-díjszabás][twilio_pricing]oldalon talál információt. Az Azure-ügyfelek [különleges ajánlatot][special_offer]kapnak: 1000 sms-t vagy 1000 bejövő percet. Ha feliratkozik erre az ajánlatra, [https://ahoy.twilio.com/azure][special_offer]vagy további információt szeretne kapni, kérjük, látogasson el a .  

## <a name="concepts"></a><a id="Concepts"></a>Fogalmak
A Twilio API egy RESTful API, amely hang- és SMS-funkciókat biztosít az alkalmazások számára. Az ügyféltárak több nyelven is elérhetők; a listát a [Twilio API Libraries című témakörben található.][twilio_libraries]

### <a name="twiml"></a><a id="TwiML"></a>TwiML
A TwiML XML-alapú utasítások készlete, amely tájékoztatja a Twilio-t a hívás vagy SMS feldolgozásáról.

Például a következő TwiML a **Hello World** szöveget beszédmé alakítja.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Minden TwiML-dokumentum nak van `<Response>` gyökéreleme. Innen twilio-igei segítségével határozhatja meg az alkalmazás viselkedését.

### <a name="twiml-verbs"></a><a id="Verbs"></a>TwiML-műveletek
A Twilio-műveletek olyan XML-címkék, amelyek megmondják a **Twilio-nak,** hogy mit tegyen. A ** &lt;Say&gt; ** ige például arra utasítja a Twilio-t, hogy hallhatóan kézbesítsen egy üzenetet egy hívásra. 

Az alábbi lista a Twilio-igéket tartalmazza.

* **Tárcsázás&gt;: A hívót egy másik telefonhoz csatlakoztatja. &lt;**
* Gather : A telefon billentyűzetén megadott numerikus számjegyeket gyűjti. ** &lt;&gt;**
* **Hangup&gt;: Hívás &lt;** befejezése.
* Lejátszás : Hangfájl lejátszása. ** &lt;&gt;**
* Szünet : A megadott számú másodpercig csendben várakozik. ** &lt;&gt;**
* Rekord : Rögzíti a hívó hangját, és visszaadja a felvételt tartalmazó fájl URL-címét. ** &lt;&gt;**
* **Átirányítás&gt;: Egy hívás vagy SMS vezérlésének átvitele a TwiML-re egy másik URL-címen. &lt;**
* Elutasítás : Elutasítja a Bejövő hívást a Twilio-számra anélkül, hogy kiszámlázna ** &lt;&gt;**
* **Kimondás&gt;: A hívás közben készített szöveget beszédmé alakítja. &lt;**
* Sms : SMS-üzenetet küld. ** &lt;&gt;**

A Twilio-műveletekről, azok attribútumairól és a TwiML-ről a [TwiML][twiml]című témakörben talál további információt. A Twilio API-ról további információt a [Twilio API című témakörben][twilio_api]talál.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll a Twilio-fiók leválasztására, regisztráljon a [Try Twilio][try_twilio]című naphoz. Kezdheti egy ingyenes fiókkal, és később frissítheti fiókját.

Amikor regisztrál egy Twilio-fiókra, ingyenes telefonszámot kap az alkalmazáshoz. Egy fiók biztonsági azonosítóját és egy hitelesítési tokent is kap. Mindkettő re van szükség a Twilio API-hívások. A fiókjához való jogosulatlan hozzáférés megakadályozása érdekében tartsa biztonságban a hitelesítési jogkivonatot. A fiók SID és hitelesítési token látható a [Twilio fiók lapon,][twilio_account]a mezők feliratú **ACCOUNT SID** és **AUTH TOKEN**, illetve.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Telefonszámok ellenőrzése
A Twilio által megadott számon kívül ellenőrizheti azokat a számokat is, amelyeket ön vezérel (azaz a mobiltelefonját vagy az otthoni telefonszámát) az alkalmazásokban való használatra. 

A telefonszám ellenőrzéséről a Számok [kezelése című][verify_phone]témakörben talál további információt.

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Ruby alkalmazás létrehozása
A Ruby-alkalmazás, amely a Twilio-szolgáltatást használja, és az Azure-ban fut, nem különbözik bármely más Ruby-alkalmazás, amely a Twilio-szolgáltatást használja. Bár a Twilio-szolgáltatások RESTful és lehet hívni a Ruby több szempontból is, ez a cikk középpontjában a Twilio-szolgáltatások használata [a Twilio segítő könyvtár Ruby.][twilio_ruby]

Először [hozzon létre egy új Azure Linux virtuális gép,][azure_vm_setup] hogy működjön az új Ruby webalkalmazás gazdagépeként. Figyelmen kívül hagyja a rails-alkalmazás létrehozásával kapcsolatos lépéseket, csak állítsa be a virtuális gép. Győződjön meg arról, hogy hozzon létre egy végpontot egy 80-as külső porttal és egy 5000-es belső porttal.

Az alábbi példákban a [Sinatra-t][sinatra]fogjuk használni, egy nagyon egyszerű webes keretet a Ruby számára. De minden bizonnyal használhatja a Twilio segítő könyvtár Ruby bármely más webes keret, beleértve a Ruby on Rails.

SSH-t az új virtuális gépbe, és hozzon létre egy könyvtárat az új alkalmazáshoz. Belül, hogy a könyvtár, hozzon létre egy fájlt nevű Gemfile és másolja a következő kódot bele:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

A parancssorból `bundle install`fut . Ez telepíti a fenti függőségeket. Ezután hozzon `web.rb`létre egy nevű fájlt . Ez lesz az, ahol a webalkalmazás kódja él. Illessze be a következő kódot a következőbe:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Ezen a ponton meg kell tudni `ruby web.rb -p 5000`futtatni a parancsot . Ez egy kis webszervert hoz létre az 5000-es porton. Az Azure-beli virtuális géphez beállított URL-cím felkeresésével megkeresheti ezt az alkalmazást a böngészőben. Miután elérte a webalkalmazást a böngészőben, készen áll a Twilio-alkalmazás létrehozásának megkezdésére.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Az alkalmazás konfigurálása a Twilio használatára
Beállíthatja, hogy a webalkalmazás a Twilio-könyvtárat használja, ha frissíti a sort, hogy tartalmazza ezt a `Gemfile` sort:

    gem 'twilio-ruby'

A parancssorban futtassa a parancsot. `bundle install` Most `web.rb` nyitott, és beleértve ezt a sort a tetején:

    require 'twilio-ruby'

Most már minden készen áll a Twilio segítő könyvtár ruby a webalkalmazásban.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Útmutató: Kimenő hívás
Az alábbiakban bemutatjuk, hogyan lehet kimenő hívást kezdeményezni. A legfontosabb fogalmak közé tartozik a Twilio-segítő kódtár használata a Ruby számára a REST API-hívások kezdeményezéséhez és a TwiML rendereléséhez. Helyettesítse az értékeket a **be-** és a **telefonszámokkal,** és győződjön meg arról, hogy a kód futtatása előtt ellenőrizze a Twilio-fiók **From** phone number-t.

Adja hozzá `web.md`ezt a függvényt a következőhöz:

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

Ha megnyílik `http://yourdomain.cloudapp.net/make_call` a böngészőben, amely elindítja a hívást a Twilio API-t, hogy a telefonhívást. Az első két `client.account.calls.create` paraméter meglehetősen magától értetődő: a hívás `from` száma és a `to`hívás száma. 

A harmadik`url`paraméter ( ) az URL-címet, amely Twilio kéri, hogy utasításokat kapjon, hogy mi a teendő, ha a hívás csatlakoztatva van. Ebben az esetben egy olyan`http://yourdomain.cloudapp.net`URL-t ( ) állítunk be, amely egy egyszerű TwiML dokumentumot ad vissza, és az igét használja a `<Say>` szövegfelolvasáshoz, és azt mondja, hogy "Hello Monkey" a hívást fogadó személynek.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Útmutató: SMS-üzenet fogadása
Az előző példában **elindítottunk** egy kimenő telefonhívást. Ezúttal használjuk a Twilio által a regisztráció során megadott telefonszámot egy **bejövő** SMS-üzenet feldolgozásához.

Először jelentkezzen be a [Twilio irányítópultjára.][twilio_account] Kattintson a "Számok" a felső nav, majd kattintson a Twilio számot kapott. Két konfigurálható URL-t fog látni. Hangkérelem URL-címe és SMS-kérelem URL-címe. Ezek azok az URL-címek, amelyeket a Twilio hív, amikor telefonhívás történik, vagy sms-t küldenek a számra. Az URL-ek is ismert "web horgok".

Szeretnénk feldolgozni a bejövő SMS-üzeneteket, ezért `http://yourdomain.cloudapp.net/sms_url`frissítsük az URL-t a rendszerre. Folytassa a Módosítások mentése gombra a lap alján. Most, vissza `web.rb` nézzük program alkalmazásunk kezelni ezt:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

A módosítás elvégzése után mindenképpen indítsa újra a webalkalmazást. Most vegye ki a telefont, és küldjön egy SMS-t a Twilio-számra. Meg kell azonnal kap egy SMS választ, hogy azt mondja: "Hé, köszönöm a ping! Twilio és Azure rock!".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Útmutató: További Twilio-szolgáltatások használata
Az itt bemutatott példák mellett a Twilio webalapú API-kat is kínál, amelyek segítségével további Twilio-funkciókat használhat az Azure-alkalmazásból. A részletekért tekintse meg a [Twilio API dokumentációját.][twilio_api_documentation]

### <a name="next-steps"></a><a id="NextSteps"></a>Következő lépések
Most, hogy megtanulta a Twilio szolgáltatás alapjait, kövesse az alábbi linkeket, hogy többet megtudjon:

* [A Twilio biztonsági irányelvei][twilio_security_guidelines]
* [Twilio HowTos és példakód][twilio_howtos]
* [Twilio rövid útmutatók][twilio_quickstarts] 
* [Twilio a GitHubon][twilio_on_github]
* [Beszéljen a Twilio ügyfélszolgálatával][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
