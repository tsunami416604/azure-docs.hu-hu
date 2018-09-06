---
title: Az Azure Service Bus alapjainak áttekintése | Microsoft Docs
description: Bevezetés a Service Bus használatába az Azure-alkalmazások más szoftverekhez való csatlakoztatásához.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: get-started-article
ms.date: 05/23/2018
ms.author: spelluru
ms.openlocfilehash: 1f9362ae102ffce6fb9ccc31362973d58964610f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697226"
---
# <a name="azure-service-bus"></a>Azure Service Bus

Függetlenül attól, hogy egy alkalmazás vagy szolgáltatás a felhőben vagy a helyszínen fut, gyakran kell más alkalmazásokkal vagy szolgáltatásokkal interakcióba lépnie. A Microsoft Azure ehhez a Service Bus révén kínál széles körben használható megoldást. Ez a témakör áttekintést nyújt erről a technológiáról, ismerteti, mi is ez pontosan, és miért érdemes használni.

## <a name="service-bus-fundamentals"></a>Service Bus fundamentals (A Service Bus alapjai)

A különféle helyzetekben különféle stílusú kommunikáció lehet szükséges. Néha az a legjobb megoldás, ha az alkalmazások egy egyszerű üzenetsoron keresztül küldik és fogadják az üzeneteket. Más helyzetekben a hagyományos üzenetsorok nem elegendőek, és a közzétételi-előfizetési mechanizmus a jobb megoldás. Egyes esetekben mindössze kapcsolatra van szükség az alkalmazások között, és nincs szükség üzenetsorokra. Az Azure Service Bus mindhárom lehetőséget biztosítja, lehetővé téve az alkalmazások számára a különféle módokon zajló interakciót.

A Service Bus egy több-bérlős felhőszolgáltatás, ami azt jelenti, hogy a szolgáltatást több felhasználó megosztva használja. Minden egyes felhasználó, például az alkalmazásfejlesztő, létrehoz egy *névteret*, majd meghatározza a szükséges kommunikációs mechanizmusokat az adott névtérben. Az 1. ábra ezt az architektúrát mutatja be:

![][1]

**1. ábra: A Service Bus egy több-bérlős szolgáltatást kínál az alkalmazások összekapcsolására a felhőn keresztül.**

Egy adott névtéren belül a négy különböző kommunikációs mechanizmus egy vagy több példányát használhatja, amelyek mindegyike különböző módokon kapcsol össze alkalmazásokat. Az alábbiak közül választhat:

* *Üzenetsorok*, amelyek egyirányú kommunikációt tesznek lehetővé. Az egyes üzenetsorok *közvetítőként* működnek, amely az üzeneteket tárolja a fogadásukig. Mindegyik üzenetet egyetlen fogadó fogadja.
* *Témakörök*, amelyek egyirányú kommunikációt tesznek lehetővé *előfizetések* segítségével. Egy témakör több előfizetéssel is rendelkezhet. Az üzenetsorokhoz hasonlóan a témakörök is közvetítőként szolgálnak, azonban az egyes előfizetések szűrőt használhatnak, amelynek révén csak az adott feltételeknek megfelelő üzenetek fogadása történik meg.
* *Továbbítók*, amelyek kétirányú kommunikációt tesznek lehetővé. Az üzenetsoroktól és témaköröktől eltérően a továbbító nem tárolja átvitel közben az üzeneteket, azaz nem működik közvetítőként. Ehelyett egyszerűen továbbítja azokat a célalkalmazás számára.

Amikor létrehoz egy üzenetsort, témakört vagy továbbítót, el kell neveznie azt. A névtér nevével kombinálva így egyedi azonosítót hoz létre az objektum számára. Az alkalmazások megadhatják ezt a nevet a Service Bus számára, majd az adott üzenetsor, témakör vagy továbbító használatával kommunikálhatnak egymással.

 >**Példa:**   
     *https://&lt;servicebus-névtér-neve&gt;.servicebus.windows.net/&lt;üzenetsor-neve&gt;*  
     *https://&lt;servicebus-névtér-neve&gt;.servicebus.windows.net/&lt;témakör-neve&gt;*  
     *https://&lt;servicebus-névtér-neve&gt;.servicebus.windows.net/&lt;relay-neve&gt;*  

Ezen objektumok valamelyikének a Relay forgatókönyvben történő használatához a Windows-alkalmazások a Windows Communication Foundation (WCF) szolgáltatást használhatják. Ez a szolgáltatás a [WCF Relay](../service-bus-relay/relay-what-is-it.md). Az üzenetsorok és a témakörök esetében a Windows-alkalmazások a Service Bus által definiált üzenettovábbítási API-kat használhatják. Az objektumok nem Windows-alkalmazásokból való használatának egyszerűbbé tétele érdekében a Microsoft SDK-kat biztosít a Java, Node.js és egyéb nyelvekhez. Az üzenetsorokat és a témaköröket [REST API-k](/rest/api/servicebus/) használatával is elérheti HTTP(s)-en keresztül. 

Fontos megérteni, hogy bár a Service Bus maga a felhőben fut (azaz a Microsoft Azure adatközpontjaiban), a szolgáltatást igénybe vevő alkalmazások bárhol futhatnak. A Service Bus használatával összekapcsolhat például az Azure-ban vagy a saját adatközpontjában futó alkalmazásokat. A szolgáltatással összekapcsolhat az Azure-ban vagy más felhőplatformon futó alkalmazást egy helyszíni alkalmazással vagy táblagépekkel és telefonokkal is. A Service Bus egy olyan kommunikációs mechanizmus a felhőben, amely lényegében bárhonnan elérhető. A használatának módja attól függ, hogy milyen célt szolgálnak az alkalmazások.

## <a name="queues"></a>Üzenetsorok

Tegyük fel, hogy két alkalmazás egy Service Bus-üzenetsorral való csatlakoztatása mellett dönt. A 2. ábra ezt a helyzetet mutatja be:

![][2]

**2. ábra: A Service Bus-üzenetsorok egyirányú aszinkron sorkezelést biztosítanak.**

A küldő egy üzenetet küld a Service Bus-üzenetsorba, a fogadó pedig egy későbbi időpontban fogadja az üzenetet. Az egyes üzenetsorok rendelkezhetnek egyetlen fogadóval, amint az a 2. ábrán látható. Esetleg több alkalmazás is olvashat ugyanabból az üzenetsorból. Az utóbbi esetben az egyes üzeneteket csak egyetlen fogadó olvassa. A csoportos küldési szolgáltatáshoz inkább témakört használjon.

Mindegyik üzenet két részből áll: egy tulajdonságkészletből, amely kulcs/érték párokat tartalmaz, valamint az üzenet hasznos adattartalmából. A hasznos adattartalom lehet bináris, szöveges vagy akár XML formátumú is. A használatuk módja attól függ, hogy mire szolgál az alkalmazás. A legutóbbi értékesítésről üzenetet küldő alkalmazás például az **Értékesítő="Ava"** és az **Összeg=10000** tulajdonságot tartalmazhatja. Az üzenettörzs tartalmazhatja az aláírt értékesítési szerződés beolvasott képét, vagy ha nincs ilyen, üres is lehet.

A fogadó kétféleképpen olvashatja az üzeneteket a Service Bus-üzenetsorból. A *[ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode)* nevű első lehetőség üzenetet kap a sorból, és azonnal törli. Ez a lehetőség egyszerű, ha azonban a fogadó összeomlik, mielőtt végzett az üzenet feldolgozásával, az üzenet elveszik. Mivel már el lett távolítva az üzenetsorból, más fogadó nem férhet hozzá. 

A második lehetőség, a *[PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)*, ezt a problémát hivatott kiküszöbölni. A **ReceiveAndDelete** lehetőséghez hasonlóan a **PeekLock** olvasás is eltávolítja az üzenetet a sorból. Nem törli azonban az üzenetet. Helyette zárolja, és a többi fogadó számára láthatatlanná teszi, majd vár az alábbi három esemény valamelyikének a bekövetkezésére:

* Ha a fogadó sikeresen feldolgozza az üzenetet, meghívja a [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) metódust, és az üzenetsor törli az üzenetet. 
* Ha a fogadó úgy dönt, hogy nem tudja sikeresen feldolgozni az üzenetet, meghívja az [Abandon()](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metódust. Az üzenetsor ekkor eltávolítja a zárolást az üzenetről, és elérhetővé teszi a többi fogadó számára.
* Ha a fogadó egyik metódust sem hívja meg a konfigurált időtartamon belül (alapértelmezés szerint 60 másodperc), az üzenetsor feltételezi, hogy a fogadó meghibásodott. Ebben az esetben úgy viselkedik, mintha a fogadó meghívta volna az **Abandon** metódust, és elérhetővé teszi az üzenetet a többi fogadó számára.

Figyelje meg, mi történhet ebben az esetben: ugyanazon üzenet kézbesítésére kétszer is sor kerülhet, akár két különböző fogadónak is. A Service Bus-üzenetsorokat használó alkalmazásokat fel kell készíteni erre az esetre. A duplikált üzenetek észlelésének megkönnyítésére mindegyik üzenet rendelkezik egy egyedi [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid#Microsoft_Azure_ServiceBus_Message_MessageId) tulajdonsággal, amely alapértelmezés szerint változatlan marad, függetlenül attól, hogy hányszor történt meg az adott üzenet olvasása az üzenetsorból. 

Az üzenetsorok számos helyzetben lehetnek hasznosak. A használatukkal az alkalmazások akkor is kommunikálhatnak, ha nem egy időben futnak, ami különösen a kötegelt és a mobilalkalmazások esetén praktikus. A több fogadóval rendelkező üzenetsorok emellett automatikus terheléselosztást is biztosítanak, mivel a küldött üzenetek megoszlanak a fogadók között.

## <a name="topics"></a>Témakörök

Bármennyire hasznosak is, az üzenetsorok nem minden esetben bizonyulnak a megfelelő megoldásnak. Esetenként célszerűbb témaköröket használni. A 3. ábra ezt az elképzelést mutatja be:

![][3]

**3. ábra: Az előfizető alkalmazás az általa megadott szűrő alapján a Service Bus-témakörbe küldött üzenetek közül csak néhányat vagy mindegyiket is fogadhatja.**

A *témakörök* sok szempontból hasonlóak az üzenetsorokhoz. A küldők ugyanúgy küldik az üzeneteket a témakörökbe, ahogy az üzenetsorokba, és az üzenetek is ugyanúgy néznek ki, mint az üzenetsorok esetén. A különbség az, hogy a témakörök használatával mindegyik fogadó alkalmazás létrehozhatja saját *előfizetését*, valamint definiálhat egy *szűrőt*. Az előfizető megkapja a témakör összes üzenetének a másolatát, azonban ha szűrőt használ, csak az adott szűrőnek megfelelő üzeneteket kaphatja meg. A 3. ábrán például egy küldő és egy 3 előfizetővel rendelkező témakör látható, mely előfizetők mindegyike saját szűrővel rendelkezik:

* Az 1. előfizető csak azokat az üzeneteket fogadja, amelyek tartalmazzák az **Értékesítő="Ava"** tulajdonságot.
* Az 2. előfizető csak azokat az üzeneteket fogadja, amelyek tartalmazzák az **Értékesítő="Ruby"** tulajdonságot és/vagy tartalmaznak egy **Összeg** tulajdonságot, amelynek az értéke nagyobb mint 100 000. Lehet, hogy Ruby az értékesítési igazgató, és szeretné látni a saját értékesítéseit, valamint minden nagy értékű értékesítést az értékesítő személyétől függetlenül.
* A 3. előfizető a szűrőt **Igaz** értékűre állította, ami azt jelenti, hogy minden üzenetet megkap. Ez az alkalmazás például a naplózásért lehet felelős; ezért minden üzenetet meg kell kapnia.

Ahogy az üzenetsorok esetében is, az üzenetek olvasásakor a témakörök előfizetői is választhatnak a [ReceiveAndDelete és a PeekLock mód](/dotnet/api/microsoft.azure.servicebus.receivemode) között. Az üzenetsoroktól eltérően azonban a témakörökbe küldött egyes üzeneteket több előfizetés is fogadhatja. Ez a gyakran *közzététel és előfizetés* (vagy *pub/sub*) néven ismert megközelítés hasznos, ha több alkalmazás is érdeklődik ugyanazon üzenetek iránt. A megfelelő szűrő meghatározásával mindegyik előfizető az üzenetfolyamnak csak azon részét látja, amelyet látnia kell.

## <a name="relays"></a>Továbbítók

Az üzenetsorok és a témakörök egyaránt egyirányú aszinkron kommunikációt tesznek lehetővé egy közvetítőn keresztül. A forgalom csak egy irányban folyik, és nincs közvetlen kapcsolat a küldők és a fogadók közt. De mi történik, ha nem szeretné ezt a kapcsolatot? Tegyük fel, hogy az alkalmazásainak küldenie és fogadnia is kell üzeneteket, vagy talán közvetlen kapcsolatot szeretne közöttük, és nincs szüksége közvetítőre az üzenetek tárolásához. Az ilyen forgatókönyvek kezeléséhez a Service Bus *továbbítókat* biztosít, amint az a 4. ábrán látható:

![][4]

**4. ábra: A Service Bus Relay kétirányú szinkron kommunikációt tesz lehetővé az alkalmazások között.**

A továbbítókkal kapcsolatban felmerül a nyilvánvaló kérdés: miért használnék ilyet? Annak ellenére, hogy nincs szükségem üzenetsorokra, miért kommunikáljanak az alkalmazások egy felhőszolgáltatáson keresztül a közvetlen interakció helyett? A válasz az, hogy a közvetlen kommunikáció néha nehezebb, mint gondolná.

Tegyük fel, hogy két helyszíni alkalmazást kíván összekapcsolni, és mindkettő vállalati adatközpontban fut. Mindkét alkalmazás egy tűzfal mögött található, és mindkét adatközpont valószínűleg hálózati címfordítást (NAT) használ. A tűzfal néhány port kivételével az összes porton blokkolja a bejövő adatokat, és a NAT miatt az a számítógép, amelyen az egyes alkalmazások futnak, nem rendelkezik rögzített IP-címmel, amelyet közvetlenül elérhetne az adatközponton kívülről. Külön segítség nélkül ezeknek az alkalmazásoknak az összekapcsolása a nyilvános interneten keresztül problémás lehet.

A Service Bus Relay használata ezt megkönnyítheti. A továbbítón keresztüli kétirányú kommunikációhoz mindegyik alkalmazás létrehoz egy kimenő TCP-kapcsolatot a Service Busszal, és nyitva tartja azt. A két alkalmazás közötti minden kommunikáció ezeken a kapcsolatokon keresztül történik. Mivel mindegyik kapcsolat az adatközponton belül lett létrehozva, a tűzfal engedélyezi a bejövő forgalmat mindegyik alkalmazás felé, új portok megnyitása nélkül. Ez a megközelítés megkerüli a NAT miatti problémát, mivel mindegyik alkalmazás állandó végponttal rendelkezik a felhőben a kommunikáció során. Az adatoknak a továbbítón keresztüli cseréjével az alkalmazások elkerülhetik azokat a problémákat, amelyek egyébként megnehezítenék a kommunikációt. 

A Service Bus-továbbítók használatához az alkalmazásoknak a Windows Communication Foundation (WCF) szolgáltatásra kell támaszkodniuk. A Service Bus WCF-kötéseket biztosít, amelyek segítségével a Windows-alkalmazások egyszerűen kommunikálhatnak a továbbítókon keresztül. Azon alkalmazásoknak, amelyek már használják a WCF-et, általában elegendő megadniuk az egyik ilyen kötést ahhoz, hogy kommunikálhassanak egymással Relayen keresztül. Az üzenetsoroktól és témaköröktől eltérően a továbbítók használata nem Windows alkalmazásokból – bár lehetséges – némi programozást igényel, mivel nem állnak rendelkezésre szabványos könyvtárak.

Az üzenetsoroktól és témaköröktől eltérően az alkalmazások nem hoznak létre kifejezetten továbbítókat. Ehelyett, amikor egy üzeneteket fogadni szándékozó alkalmazás TCP-kapcsolatot létesít a Service Busszal, a rendszer automatikusan létrehoz egy továbbítót. A kapcsolat megszakadásakor a rendszer törli a továbbítót. Annak érdekében, hogy egy alkalmazás megtaláljon egy adott hallgató által létrehozott továbbítót, a Service Bus egy jegyzéket biztosít, amelyben az alkalmazások név alapján megtalálhatják a továbbítót.

A továbbító a megfelelő megoldás, ha közvetlen kommunikációra van szükség az alkalmazások között. Vegyük például egy légitársaság foglalási rendszerét, amely egy olyan helyszíni adatközpontban fut, amelynek elérhetőnek kell lennie bejelentkezési pultokról, mobileszközökről és egyéb számítógépekről. Az ezeken a rendszereken futó alkalmazások Service Bus Relayeket használhatnak a felhőben a kommunikációhoz, függetlenül attól, hogy hol futnak.

## <a name="summary"></a>Összegzés

Az alkalmazások összekapcsolása mindig is részét képezte a teljes megoldások összeállításának, és az olyan forgatókönyvek száma, amelyekben az alkalmazásoknak és szolgáltatásoknak kommunikálniuk kell egymással, óhatatlanul nőni fog, ahogy egyre több alkalmazás és eszköz csatlakozik az internetre. A Service Bus célja az, hogy ezt az alapvető fontosságú funkciót könnyebben implementálhatóvá és szélesebb körben elérhetővé tegye azáltal, hogy felhőalapú technológiákat biztosít a kommunikációhoz az üzenetsorok, témakörök és Relayek révén.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure Service Bus alapjait, az alábbi hivatkozásokat követve olvashat további információkat.

* A [Service Bus-üzenetsorok](service-bus-dotnet-get-started-with-queues.md) használata
* A [Service Bus-témakörök](service-bus-dotnet-how-to-use-topics-subscriptions.md) használata
* A [Service Bus-továbbító](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) használata
* [Service Bus-minták](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png
