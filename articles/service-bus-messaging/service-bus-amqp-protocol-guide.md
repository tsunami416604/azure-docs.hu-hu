---
title: AMQP 1.0 az Azure Service Bus és az Event Hubs protokollútmutatójában | Microsoft dokumentumok
description: Protokollútmutató az AMQP 1.0 kifejezéseinek és leírásának az Azure Service Bus ban és az Event Hubs-ban
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: d706e9b3351b0693a1f352e15b6b9b0cc5c7a65d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086142"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 az Azure Service Bus és az Event Hubs protokollútmutatójában

Az Advanced Message Queueing Protocol 1.0 egy szabványosított keretezési és átviteli protokoll az aszinkron, biztonságos és megbízható adatátvitelhez két fél között. Ez az Azure Service Bus Messaging és az Azure Event Hubs elsődleges protokollja. Mindkét szolgáltatás támogatja a HTTPS protokollt is. A szabadalmaztatott SBMP protokoll, amely szintén támogatott, fokozatosan megszűnik az AMQP javára.

AMQP 1.0 van a eredmény -ból széles ipar együttműködés amit összehoz együtt middleware eladó, mint Mikroszkóp és Piros Kalap, -val sok üzenet middleware használók mint JP Morgan Hajsza ábrázol a pénzügyi szolgáltatás ipar. Az AMQP protokoll és a bővítési előírások műszaki szabványosítási fóruma az OASIS, és nemzetközi szabványként iSO/IEC 19494-ként hivatalos jóváhagyást kapott.

## <a name="goals"></a>Célok

Ez a cikk röviden összefoglalja az AMQP 1.0 üzenetküldési specifikáció alapvető fogalmait, valamint az OASIS AMQP műszaki bizottságában jelenleg véglegesített tervezetbővítmény-specifikációk kis készletét, és elmagyarázza, hogyan ismerteti az Azure Service Bus ezeket az előírásokat.

A cél az, hogy minden olyan fejlesztő, aki bármely platformon bármilyen meglévő AMQP 1.0 ügyfélveremben használja, hogy az AMQP 1.0-s alkalmazáson keresztül kommunikáljon az Azure Service Bus szolgáltatással.

A gyakori általános célú AMQP 1.0-s halmok, például az Apache Proton vagy a AMQP.NET Lite már megvalósítják az összes alapvető AMQP 1.0 protokollt. Ezeket az alapvető gesztusokat néha egy magasabb szintű API-val csomagolják; Az Apache Proton még kettőt is kínál, a elengedhetetlen Messenger API-t és a reaktív reactor API-t.

A következő vita során azt feltételezzük, hogy az AMQP-kapcsolatok, -munkamenetek és -hivatkozások kezelését, valamint a keretátvitelek és az áramlásvezérlés kezelését az adott verem (például Apache Proton-C) kezeli, és nem igényel sok figyelmet, ha külön figyelmet igényel az alkalmazásfejlesztőktől. Absztraktan feltételezzük, hogy létezik néhány API primitívek, mint a képes csatlakozni, és hozzon létre valamilyen formában `send()` `receive()` *a küldő* és *a fogadó* absztrakciós objektumok, amelyek aztán valamilyen alakú és műveletek, illetve.

Az Azure Service Bus speciális képességeinek, például az üzenetböngészésnek vagy a munkamenetek kezelésének megvitatása során ezeket a funkciókat a mqp-feltételek, de a feltételezett API-absztrakción felül réteges pszeudoimplementációként is ismertetik.

## <a name="what-is-amqp"></a>Mi az AMQP?

AMQP egy keretezési és átviteli protokoll. A keretezés azt jelenti, hogy a hálózati kapcsolat mindkét irányában áramló bináris adatfolyamok szerkezetét biztosítja. A szerkezet a különböző adatblokkok, az úgynevezett *keretek*határát jelöli a kapcsolódó felek között. Az átviteli képességek biztosítják, hogy mindkét kommunikáló fél közös megegyezést alakítson ki arról, hogy mikor kell a kereteket átruházni, és mikor kell az átadásokat teljesnek tekinteni.

Ellentétben a korábbi lejárt tervezetek által készített AMQP munkacsoport, amely még mindig használja néhány üzenet brókerek, a munkacsoport végleges, és szabványosított AMQP 1.0 protokoll nem írja elő a jelenléte egy üzenet bróker vagy bármely adott topológia az üzenetközvetítőn belül.

A protokoll használható szimmetrikus társközi kommunikáció, a várólistákat támogató üzenetbrókerek és közzétételi/előfizetési entitások közötti interakcióhoz, ahogy az Azure Service Bus is. Azt is használható az üzenetkezelési infrastruktúrával való interakció, ahol az interakciós minták eltérnek a rendszeres várólisták, mint az Azure Event Hubs esetében. Az Event Hub úgy működik, mint egy várólista, amikor eseményeket küld hozzá, de inkább egy soros tárolási szolgáltatásként működik, amikor az eseményeket beolvassa; ez némileg hasonlít egy szalagos meghajtót. Az ügyfél kiválaszt egy eltolást a rendelkezésre álló adatfolyamba, és ezután az összes eseményt kiszolgálja az adott eltolástól a legújabb elérhetőig.

Az AMQP 1.0 protokoll bővíthető, így további specifikációk at fejlesztheti képességeit. Ezt a dokumentumban tárgyalt három kiterjesztési előírás szemlélteti. A meglévő HTTPS/WebSockets infrastruktúrán keresztüli kommunikációhoz a natív AMQP TCP-portok konfigurálása nehéz lehet. A kötési specifikáció határozza meg, hogyan kell rétegezi az AMQP-t a WebSockets-en keresztül. Az üzenetkezelési infrastruktúrával felügyeleti célokra vagy speciális funkciók biztosítása érdekében az üzenetkezelési infrastruktúrával való interakcióhoz az AMQP felügyeleti specifikáció határozza meg a szükséges alapvető interakciós primitíveket. Az összevont engedélyezési modell integrációja esetén az AMQP jogcímalapú biztonsági specifikációja határozza meg, hogyan társíthatja és újíthatja meg a hivatkozásokhoz társított engedélyezési jogkivonatokat.

## <a name="basic-amqp-scenarios"></a>Alapvető AMQP-forgatókönyvek

Ez a szakasz ismerteti az AMQP 1.0 alapvető használatát az Azure Service Bus szolgáltatással, amely magában foglalja a kapcsolatok, munkamenetek és hivatkozások létrehozását, valamint a Service Bus-entitásokba, például várólistákba, témakörökbe és előfizetésekbe történő átvitelt.

Az AMQP működésének megismeréséhez a leghitelesebb forrás az AMQP 1.0 specifikáció, de a specifikáció pontosan útmutatóa kontinamán, és nem a protokoll tanításához. Ez a szakasz az AMQP 1.0-s rendszerhasználó Service Bus-használat leírásához szükséges annyi terminológia bevezetésére összpontosít. Az AMQP átfogóbb bevezetéséhez, valamint az AMQP 1.0 szélesebb körű megvitatásához tekintse át [ezt a videótanfolyamot.][this video course]

### <a name="connections-and-sessions"></a>Kapcsolatok és munkamenetek

Az AMQP meghívja a kommunikációs programok *tárolóit*; ezek *olyan csomópontokat*tartalmaznak , amelyek a tárolókon belüli kommunikáló entitások. Egy várólista lehet ilyen csomópont. Az AMQP lehetővé teszi a multiplexelést, így egyetlen kapcsolat használható a csomópontok közötti számos kommunikációs útvonalhoz; például egy alkalmazásügyfél egyidejűleg fogadhat egy várólistából, és ugyanazon a hálózati kapcsolaton keresztül küldhet egy másik várólistába.

![][1]

A hálózati kapcsolat így a tárolón van rögzítve. Az ügyfélszerepkörtároló kezdeményezi, amely kimenő TCP-szoftvercsatorna-kapcsolatot létesít a fogadószerepkör ben lévő tárolóval, amely figyeli és elfogadja a bejövő TCP-kapcsolatokat. A kapcsolati kézfogás magában foglalja a protokollverzió egyeztetését, a Transport Level Security (TLS/SSL) használatának deklarálását vagy egyeztetését, valamint a SASL-en alapuló hitelesítési/engedélyezési kézfogást a kapcsolathatókörnél.

Az Azure Service Bus mindig a TLS használatát igényli. Támogatja az 5671-es TCP-porton keresztüli kapcsolatokat, amelyek során a TCP-kapcsolatot először takarja el a TLS az AMQP protokoll kézfogásába való belépés előtt, és támogatja az 5672-es TCP-porton keresztüli kapcsolatokat is, amelyek során a kiszolgáló azonnal felajánlja a kapcsolat kötelező frissítését a TLS-hez az AMQP által előírt modell használatával. Az AMQP WebSockets kötés létrehoz egy alagutat a 443-as TCP-porton keresztül, amely ezután egyenértékű az AMQP 5671-es kapcsolatokkal.

A kapcsolat és a TLS beállítása után a Service Bus két SASL-mechanizmusi lehetőséget kínál:

* A SASL PLAIN gyakran használatos a felhasználónév és a jelszó hitelesítő adatainak kiszolgálóra történő átadására. A Service Bus nem rendelkezik fiókkal, de [megosztott hozzáférésű biztonsági szabályokkal rendelkezik,](service-bus-sas.md)amelyek jogokat biztosítanak, és kulcshoz vannak társítva. A szabály neve a felhasználónév, a kulcs (base64 kódolású szöveg) pedig jelszóként használatos. A választott szabályhoz társított jogok szabályozzák a kapcsolaton engedélyezett műveleteket.
* A SASL ANONYMOUS a SASL-hitelesítés megkerülésére szolgál, ha az ügyfél a később ismertetett jogcímalapú biztonsági (CBS) modellt kívánja használni. Ezzel a beállítással az ügyfélkapcsolat névtelenül is létrehozható egy rövid időre, amely alatt az ügyfél csak a CBS-végpontot használhatja, és a CBS-kézfogásnak be kell fejeződnie.

A szállítási kapcsolat létrehozása után a tárolók mindegyike deklarálja a maximális keretméretet, amelyet hajlandó kezelni, és egy üresjárati időtúllépés után egyoldalúan bontják a kapcsolatot, ha nincs tevékenység a kapcsolaton.

Azt is kijelentik, hogy hány egyidejű csatorna támogatott. A csatorna egy egyirányú, kimenő, virtuális átviteli útvonal a kapcsolat tetején. A munkamenet az egyes összekapcsolt tárolók csatornáját veszi igénybe, hogy kétirányú kommunikációs útvonalat alakítson ki.

A munkamenetek ablakalapú folyamatvezérlési modellel rendelkeznek; munkamenet létrehozásakor mindkét fél kijelenti, hogy hány keretet hajlandó elfogadni a fogadási ablakban. Ahogy a felek kicserélik a kereteket, az átvitt keretek kitöltik ezt az ablakot, és az átvitelek leállnak, amikor az ablak megtelik, és amíg az ablak vissza nem áll vagy ki nem bővül a *flow performatív* használatával *(a performatív* az AMQP kifejezés a két fél között kicserélt protokollszintű kézmozdulatokra).

Ez az ablakalapú modell nagyjából hasonló az ablakalapú folyamatvezérlés TCP-koncepciójához, de a szoftvercsatorna munkamenetszintjén. A protokoll koncepciója, amely lehetővé teszi a több egyidejű munkamenetek létezik, hogy a magas prioritású forgalom lehet rohant múlt fojtott normál forgalom, mint egy autópálya expressz sávban.

Az Azure Service Bus jelenleg pontosan egy munkamenetet használ minden kapcsolathoz. A Service Bus maximális frame-mérete 262.144 bájt (256-K bájt) a Service Bus standard és Event Hubs. Ez 1.048.576 (1 MB) a Service Bus Premium. A Service Bus nem ír elő adott munkamenetszintű szabályozási ablakokat, de a kapcsolatszintű folyamatvezérlés részeként rendszeresen visszaállítja az ablakot (lásd [a következő szakaszt](#links)).

A kapcsolatok, a csatornák és az munkamenetek rövid élettartamúak. Ha az alapul szolgáló kapcsolat összeomlik, a kapcsolatokat, a TLS-alagutat, a SASL engedélyezési környezetet és a munkameneteket újra létre kell hozni.

### <a name="amqp-outbound-port-requirements"></a>Az AMQP kimenő portra vonatkozó követelményei

A TCP-n keresztül AMQP-kapcsolatokat használó ügyfelek nek meg kell nyitniuk az 5671-es és az 5672-es portot a helyi tűzfalon. Ezeka portok mellett szükség lehet további portok megnyitására, ha az [EnableLinkRedirect](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect?view=azure-dotnet) funkció engedélyezve van. `EnableLinkRedirect`egy új üzenetküldő funkció, amely segít kihagyni az egyugrásos üzenetek fogadása közben, így segítve az átviteli hangfokozását. Az ügyfél közvetlenül kommunikálna a back-end szolgáltatással a 104XX porttartományon keresztül, ahogy az az alábbi képen látható. 

![A célportok listája][4]

A .NET-ügyfél meghibásodna egy SocketException ("Kísérlet történt a szoftvercsatorna hozzáférési engedélyei által tiltott módon való elérésére"), ha ezeket a portokat a tűzfal blokkolja. A szolgáltatás letiltható `EnableAmqpLinkRedirect=false` a connectiong karakterlánc beállításával, amely arra kényszeríti az ügyfeleket, hogy az 5671-es porton keresztül kommunikáljanak a távoli szolgáltatással.


### <a name="links"></a>Hivatkozások

Az AMQP hivatkozásokon keresztül továbbítja az üzeneteket. A kapcsolat olyan kommunikációs útvonal, amelyet egy munkamenet en keresztül hoznak létre, és amely lehetővé teszi az üzenetek átvitelét egy irányban; az átadási állapot egyeztetése a kapcsolaton keresztül imidzsel és kétirányú a kapcsolódó felek között.

![][2]

A hivatkozásokat bármikor és egy meglévő munkamenet en keresztül is létrehozhatja a hivatkozások bármelyik tárolója, ami megkülönbözteti az AMQP-t számos más protokolltól, beleértve a HTTP-t és az MQTT-t, ahol az átvitelek és az átviteli útvonal kezdeményezése a aljzati kapcsolatot.

A kapcsolat-kezdeményező tároló kéri a másik tároló elfogadja a kapcsolatot, és kiválasztja a feladó vagy a fogadó szerepét. Ezért bármelyik tároló kezdeményezheti egyirányú vagy kétirányú kommunikációs útvonalak létrehozását, az utóbbit pedig kapcsolatpárokként modellezve.

A hivatkozások neve és csomópontjai vannak társítva. Ahogy az elején, csomópontok a kommunikáló entitások egy tárolóban.

A Service Busban egy csomópont közvetlenül egyenértékű egy várólistával, egy témakörrel, egy előfizetéssel vagy egy várólistávagy előfizetés kézbesítetlen levelek alvárólistájával. Az AMQP-ben használt csomópontnév ezért a Service Bus névterén belüli entitás relatív neve. Ha egy várólista neve, `myqueue`ez is az AMQP csomópont neve. A témakör-előfizetés követi a HTTP API-konvenciót azáltal, hogy egy "előfizetések" erőforrás-gyűjteménybe rendezi, és így a **mytopic** témakör **előfizetési alja** az AMQP csomópont neve **mytopic/subscriptions/sub.**

A csatlakozó ügyfélnek helyi csomópontnevet is kell használnia a hivatkozások létrehozásához; A Service Bus nem előíró jellegű ezekkel a csomópontnevekkel kapcsolatban, és nem értelmezi azokat. Az AMQP 1.0 ügyfélhalmok általában egy sémát használnak annak biztosítására, hogy ezek az ideiglenes csomópontnevek egyediek az ügyfél hatókörében.

### <a name="transfers"></a>Transzferek

A hivatkozás létrehozása után az üzenetek átvihetők a hivatkozáson keresztül. Az AMQP-ben az átvitel egy explicit protokoll-kézmozdulattal (az *átviteli* performatív) történik, amely egy üzenetet helyez át a feladóról a fogadóra egy hivatkozáson keresztül. Az átruházás akkor fejeződik be, ha "rendezték", ami azt jelenti, hogy mindkét fél közösen értelmezte az átruházás kimenetelét.

![][3]

A legegyszerűbb esetben a feladó választhat, hogy "előre kiegyenlített" üzeneteket küld, ami azt jelenti, hogy az ügyfelet nem érdekli az eredmény, és a fogadó nem ad visszajelzést a művelet kimeneteléről. Ezt a módot a Service Bus támogatja az AMQP protokoll szintjén, de az ügyfél API-k egyikében sem érhető el.

A rendszeres eset az, hogy az üzenetek küldése rendezetlen, és a fogadó ezután jelzi elfogadás vagy elutasítás segítségével *intézkedés* performatív. Elutasítás akkor fordul elő, ha a címzett bármilyen okból nem tudja fogadni az üzenetet, és az elutasító üzenet az okokkal kapcsolatos információkat tartalmaz, amely az AMQP által meghatározott hibastruktúra. Ha az üzeneteket a Service Bus belső hibái miatt utasítják el, a szolgáltatás további információkat ad vissza a struktúrán belül, amelyek diagnosztikai tippeket adhatnak a támogatási személyzetnek, ha támogatási kérelmeket nyújt be. Később további részleteket talál a hibákról.

Az elutasítás különleges formája a *kiadott* állapot, amely azt jelzi, hogy a címzettnek nincs technikai kifogása az átruházással szemben, de nem áll érdekében az átruházás rendezése. Ez az eset például akkor áll fenn, amikor egy üzenetet kézbesítenek egy Service Bus-ügyfélnek, és az ügyfél úgy dönt, hogy "elhagyja" az üzenetet, mert nem tudja elvégezni az üzenet feldolgozásából eredő munkát; maga az üzenet kézbesítése nem hibás. Ennek az állapotnak a változata a *módosított* állapot, amely lehetővé teszi az üzenet módosítását a kiadásakor. Ezt az állapotot jelenleg nem használja a Service Bus.

Az AMQP 1.0 specifikáció jain egy további, *fogadott*intézkedési állapot ot határoz meg, amely kifejezetten segít a kapcsolat helyreállításának kezelésében. A kapcsolat helyreállítása lehetővé teszi a kapcsolat állapotának és a függőben lévő szállítások helyreállítását egy új kapcsolat és munkamenet tetején, amikor az előző kapcsolat és munkamenet megszakadt.

A Service Bus nem támogatja a kapcsolat helyreállítását; ha az ügyfél elveszíti a kapcsolatot a Service Bus egy rendezetlen üzenet átvitel e folyamatban van, hogy az üzenet átvitele elvész, és az ügyfél újra kell csatlakoznia, újra létre kell hoznia a kapcsolatot, és próbálja meg újra az átvitelt.

Mint ilyen, a Service Bus és az Event Hubs támogatja a "legalább egyszer" átvitelt, ahol a feladó biztosítható az üzenet tárolásához és elfogadásához, de nem támogatja a "pontosan egyszer" átvitelt az AMQP szintjén, ahol a rendszer megpróbálja helyreállítani a kapcsolatot, és az üzenetátvitel megkettőzésének elkerülése érdekében folytassa a egyeztetést a kézbesítési állapotról.

A lehetséges duplikált küldések kompenzálása érdekében a Service Bus támogatja a duplikáltelemek észlelését a várólistákon és a témaköröken választható szolgáltatásként. A duplikáltelem-észlelés rögzíti az összes bejövő üzenet üzenetazonosítóját a felhasználó által megadott időablakban, majd csendben eldobja az azonos üzenetazonosítókkal küldött összes üzenetet ugyanabban az ablakban.

### <a name="flow-control"></a>Folyamatvezérlés

A korábban tárgyalt munkamenetszintű folyamatvezérlési modell mellett minden kapcsolat saját folyamatvezérlési modellel is rendelkezik. A munkamenetszintű folyamatvezérlés megvédi a tárolót attól, hogy túl sok keretet kelljen egyszerre kezelnie, a kapcsolatszintű folyamatvezérlés az alkalmazást helyezi felelősnek akapcsolatból kezelni kívánandó üzenetek et, és mikor.

![][4]

Egy linken az átutalások csak akkor történhetnek meg, ha a feladónak elegendő *hivatkozási jóváírása*van. A hivatkozásjóváírás a vevő által *flow* a folyamat-performatív használatával beállított számláló, amely egy hivatkozásra van kitéve. Amikor a feladóhoz hivatkozási jóváírás van rendelve, az üzenetek kézbesítésével megpróbálja felhasználni a jóváírást. Minden egyes üzenetkézbesítés 1-vel csökkenti a fennmaradó hivatkozásjóváírást. Amikor a kapcsolat hitel felhasznált, szállítások megáll.

Ha a Service Bus a fogadó szerepkörben van, azonnal bőséges hivatkozási jóváírást biztosít a feladónak, így az üzenetek azonnal elküldhetők. A kapcsolat jóváírás a rendszer, service bus alkalmanként küld egy *folyamat* performative a feladónak, hogy frissítse a kapcsolat hitel egyenlegét.

A feladói szerepkörben a Service Bus üzeneteket küld a függőben lévő hivatkozási jóváírások felhasználásához.

Az API-szintű "fogadás" hívás egy *flow* folyamat-performative-t eredményez, amelyet az ügyfél küld a Service Busnak, és a Service Bus ezt a jóváírást az első elérhető, feloldott üzenet nek a várólistából való zárolásával, zárolásával és átvitelével használja fel. Ha nincs olyan üzenet, amely azonnal elérhető lenne a kézbesítéshez, az adott entitással létesített bármely kapcsolatból lemaradt bármely fennálló hitel az érkezés sorrendjében marad nyilvántartva, és az üzeneteket zárolják és továbbítják, amint azok elérhetővé válnak, hogy felhasználhassa a fennálló hiteleket.

Az üzenet zárolása akkor szabadul fel , amikor az átvitel az *elfogadott*, *elutasított*vagy kiadott terminálállapotok egyikébe *van rendezve*. A terminál állapotának *elfogadásakor*az üzenet törlődik a Service Bus szolgáltatásból. A Service Bus ban marad, és a következő fogadóba kerül, amikor az átvitel eléri a többi állapot bármelyikét. A Service Bus automatikusan áthelyezi az üzenetet az entitás kézbesítetlen levelek várólistájába, amikor eléri az entitás számára engedélyezett maximális kézbesítési számot az ismételt elutasítások vagy kiadások miatt.

Annak ellenére, hogy a Service Bus API-k nem közvetlenül teszik ki egy ilyen lehetőség ma, egy alacsonyabb szintű AMQP protokoll ügyfél használhatja a link-credit modell viszont a "pull-style" kölcsönhatás a kibocsátó egy egység hitel minden egyes kérelem egy "push-style" modell nagyszámú hivatkozási kreditet bocsát ki, majd üzeneteket kap, amint azok további interakció nélkül elérhetővé válnak. A leküldéses leküldéset a [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) vagy a [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) tulajdonságbeállítások támogatják. Ha nem nulla, az AMQP-ügyfél azt használja hivatkozási jóváírásként.

Ebben az összefüggésben fontos megérteni, hogy az entitáson belüli üzenet zárolásának lejárati órája akkor kezdődik, amikor az üzenet az entitásból származik, nem akkor, amikor az üzenet a vezetékre kerül. Amikor az ügyfél jelzi, hogy kész üzeneteket fogadni a kapcsolatjóváírás kibocsátásával, ezért várhatóan aktívan húzza át az üzeneteket a hálózaton, és készen áll azok kezelésére. Ellenkező esetben előfordulhat, hogy az üzenet zárolása lejárt, mielőtt az üzenet egyáltalán kézbesítené. A kapcsolat-hitel áramlás-szabályozás használatának közvetlenül tükröznie kell a fogadónak elküldött rendelkezésre álló üzenetek kezelésére való azonnali készséget.

Összefoglalva, a következő szakaszok egy sematikus áttekintést nyújtanak a különböző API-interakciók során a performatív folyamatról. Minden szakasz más logikai műveletet ír le. Néhány ilyen kölcsönhatások lehet "lusta", ami azt jelenti, hogy csak akkor lehet elvégezni, ha szükséges. Előfordulhat, hogy az üzenetküldő létrehozása nem okoz hálózati interakciót, amíg az első üzenetet el nem küldi vagy nem kéri.

Az alábbi táblázatban látható nyilak a performatív áramlás irányát mutatják.

#### <a name="create-message-receiver"></a>Üzenetfogadó létrehozása

| Ügyfél | Service Bus |
| --- | --- |
| --> csatolja(<br/>name={link name},<br/>handle={numerikus leíró},<br/>role =**vevő,**<br/>source={entitásnév},<br/>target={ügyfélkapcsolat azonosítója}<br/>) |Az ügyfél entitáshoz csatlakozik fogadóként |
| A Service Bus válaszai a kapcsolat végét csatolva |<- csatolja(<br/>name={link name},<br/>handle={numerikus leíró},<br/>role=**feladó**,<br/>source={entitásnév},<br/>target={ügyfélkapcsolat azonosítója}<br/>) |

#### <a name="create-message-sender"></a>Üzenetküldő létrehozása

| Ügyfél | Service Bus |
| --- | --- |
| --> csatolja(<br/>name={link name},<br/>handle={numerikus leíró},<br/>role=**feladó**,<br/>source={ügyfélkapcsolat azonosítója},<br/>target={entitás neve}<br/>) |Nincs művelet |
| Nincs művelet |<- csatolja(<br/>name={link name},<br/>handle={numerikus leíró},<br/>role =**vevő,**<br/>source={ügyfélkapcsolat azonosítója},<br/>target={entitás neve}<br/>) |

#### <a name="create-message-sender-error"></a>Üzenetküldő létrehozása (hiba)

| Ügyfél | Service Bus |
| --- | --- |
| --> csatolja(<br/>name={link name},<br/>handle={numerikus leíró},<br/>role=**feladó**,<br/>source={ügyfélkapcsolat azonosítója},<br/>target={entitás neve}<br/>) |Nincs művelet |
| Nincs művelet |<- csatolja(<br/>name={link name},<br/>handle={numerikus leíró},<br/>role =**vevő,**<br/>source=null,<br/>target=null<br/>)<br/><br/><- leválasztása(<br/>handle={numerikus leíró},<br/>zárva =**igaz**,<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>Üzenetfogadó/-feladó bezárása

| Ügyfél | Service Bus |
| --- | --- |
| --> leválása(<br/>handle={numerikus leíró},<br/>closed =**igaz**<br/>) |Nincs művelet |
| Nincs művelet |<- leválasztása(<br/>handle={numerikus leíró},<br/>closed =**igaz**<br/>) |

#### <a name="send-success"></a>Küldés (siker)

| Ügyfél | Service Bus |
| --- | --- |
| --> átutalás(<br/>delivery-id={numerikus leíró},<br/>delivery-tag={binary handle},<br/>kiegyenlített =**hamis**,, more =**hamis**,<br/>state =**null**,<br/>resume =**hamis**<br/>) |Nincs művelet |
| Nincs művelet |<- hajlam(<br/>role=vevő,<br/>first={delivery ID},<br/>last={delivery ID},<br/>kiegyenlített =**igaz**,<br/>state =**elfogadva**<br/>) |

#### <a name="send-error"></a>Küldés (hiba)

| Ügyfél | Service Bus |
| --- | --- |
| --> átutalás(<br/>delivery-id={numerikus leíró},<br/>delivery-tag={binary handle},<br/>kiegyenlített =**hamis**,, more =**hamis**,<br/>state =**null**,<br/>resume =**hamis**<br/>) |Nincs művelet |
| Nincs művelet |<- hajlam(<br/>role=vevő,<br/>first={delivery ID},<br/>last={delivery ID},<br/>kiegyenlített =**igaz**,<br/>state =**elutasítva**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>Fogadás

| Ügyfél | Service Bus |
| --- | --- |
| --> áramlású(<br/>link-credit=1<br/>) |Nincs művelet |
| Nincs művelet |< átadása(<br/>delivery-id={numerikus leíró},<br/>delivery-tag={binary handle},<br/>kiegyenlített =**hamis**,<br/>more =**hamis**,<br/>state =**null**,<br/>resume =**hamis**<br/>) |
| --> hajlamú(<br/>role =**vevő,**<br/>first={delivery ID},<br/>last={delivery ID},<br/>kiegyenlített =**igaz**,<br/>state =**elfogadva**<br/>) |Nincs művelet |

#### <a name="multi-message-receive"></a>Többüzenetes fogadás

| Ügyfél | Service Bus |
| --- | --- |
| --> áramlású(<br/>link-credit=3<br/>) |Nincs művelet |
| Nincs művelet |< átadása(<br/>delivery-id={numerikus leíró},<br/>delivery-tag={binary handle},<br/>kiegyenlített =**hamis**,<br/>more =**hamis**,<br/>state =**null**,<br/>resume =**hamis**<br/>) |
| Nincs művelet |< átadása(<br/>delivery-id={numerikus fogantyú+1},<br/>delivery-tag={binary handle},<br/>kiegyenlített =**hamis**,<br/>more =**hamis**,<br/>state =**null**,<br/>resume =**hamis**<br/>) |
| Nincs művelet |< átadása(<br/>delivery-id={numerikus fogantyú+2},<br/>delivery-tag={binary handle},<br/>kiegyenlített =**hamis**,<br/>more =**hamis**,<br/>state =**null**,<br/>resume =**hamis**<br/>) |
| --> hajlamú(<br/>role=vevő,<br/>first={delivery ID},<br/>last={delivery ID+2},<br/>kiegyenlített =**igaz**,<br/>state =**elfogadva**<br/>) |Nincs művelet |

### <a name="messages"></a>Üzenetek

A következő szakaszok ismertetik, hogy a Service Bus mely tulajdonságokat használja a standard AMQP-üzenetszakaszokból, és hogyan vannak leképezve a Service Bus API-készletre.

Minden olyan tulajdonságot, amelyet az alkalmazásnak meg kell `application-properties` határoznia, le kell képezni az AMQP térképére.

#### <a name="header"></a>header

| Mezőnév | Használat | API-név |
| --- | --- | --- |
| Tartós |- |- |
| Prioritás |- |- |
| ttl |Ideje élni ezt az üzenetet |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| első felvásárló |- |- |
| szállítás-szám |- |[DeliveryCount (Kézbesítési szám)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Mezőnév | Használat | API-név |
| --- | --- | --- |
| üzenet-azonosító |Az üzenet alkalmazás által definiált, szabadformátumú azonosítója. Duplikáltelem-észleléshez szolgál. |[MessageID azonosító](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| felhasználói azonosító |Alkalmazás által definiált felhasználói azonosító, amelyet a Service Bus nem értelmez. |Nem érhető el a Service Bus API-n keresztül. |
| erre: |Alkalmazás által definiált célazonosító, amelyet a Service Bus nem értelmez. |[Címzett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Tárgy |Alkalmazás által definiált üzenetcél-azonosító, amelyet a Service Bus nem értelmez. |[Címke](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| válasz-válasz |Alkalmazás által definiált válaszelérési út jelző, amelyet a Service Bus nem értelmez. |[Válasz](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| korrelációs azonosító |Alkalmazás által definiált korrelációs azonosító, amelyet a Service Bus nem értelmez. |[Korrelációs azonosító](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| tartalomtípus |Alkalmazás által definiált tartalomtípus-jelző a törzshöz, amelyet a Service Bus nem értelmez. |[ContentType (Tartalomtípus)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| tartalomkódolás |Alkalmazás által definiált tartalomkódolási jelző a törzs, nem értelmezi a Service Bus. |Nem érhető el a Service Bus API-n keresztül. |
| abszolút lejárati idő |Deklarálja, hogy az üzenet mely abszolút pillanatban jár le. Figyelmen kívül hagyja a bemeneti (fejléc TTL figyelhető meg), mérvadó a kimeneten. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| létrehozás-idő |Az üzenet létrehozásának időpontjában deklarálja. Nem használja a Service Bus |Nem érhető el a Service Bus API-n keresztül. |
| csoport-azonosító |Alkalmazás által definiált azonosító egy kapcsolódó üzenethalmazhoz. A Service Bus munkamenetekhez használatos. |[Munkamenet](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| csoport-szekvencia |A munkameneten belüli üzenet relatív sorszámát azonosító számláló. A Service Bus figyelmen kívül hagyja. |Nem érhető el a Service Bus API-n keresztül. |
| válasz a csoport-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Üzenetjegyzetek

Kevés más szolgáltatásbusz-üzenettulajdonság van, amelyek nem részei az AMQP-üzenet tulajdonságainak, és az `MessageAnnotations` üzenetben is megjelennek.

| Jegyzettérkép-kulcs | Használat | API-név |
| --- | --- | --- |
| x-opt-scheduled-enqueue-idő | Deklarálja, hogy az üzenet mikor jelenjen meg az entitáson |[ÜtemezettenqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-kulcs | Alkalmazás által definiált kulcs, amely meghatározza, hogy melyik partícióaz üzenet kell landolnia. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partíció-kulcs | Alkalmazás által definiált partíció-kulcs érték, ha egy tranzakciót kell használni, hogy üzeneteket küldeni egy átviteli várólistán keresztül. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-enqueued-time | Szolgáltatás által meghatározott UTC idő, amely az üzenet enqueuing tényleges idejét jelöli. Figyelmen kívül hagyva a bemeneten. | [VárólistánTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-szekvencia-szám | Az üzenethez szolgáltatás által definiált egyedi szám. | [SequenceNumber (SequenceNumber)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Az üzenet szolgáltatás által definiált sorsorba sorszáma. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-zárt-amíg | Szolgáltatás által definiált. Az a dátum és időpont, ameddig az üzenet zárolva lesz a várólistában/előfizetésben. | [ZárolvaUtcig](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-forrás | Szolgáltatás-definiálva. Ha az üzenet kézbesítetlen levelek várólistájából érkezik, az eredeti üzenet forrása. | [DeadLetterForrás](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Tranzakciós képesség

Egy tranzakció két vagy több műveletet kapcsol össze egyetlen végrehajtási hatókörbe. Az ilyen ügyletnek természeténél fogva biztosítania kell, hogy egy adott műveletcsoporthoz tartozó valamennyi művelet sikeres legyen, vagy együttesen sikertelen legyen.
A műveletek azonosító `txn-id`szerint vannak csoportosítva.

A tranzakciós interakció esetén az `transaction controller` ügyfél a , amely szabályozza a műveleteket, amelyeket össze kell csoportosítani. A Service Bus `transactional resource` Service a néven működik, `transaction controller`és a. által kért munkát végzi.

Az ügyfél és a `control link` szolgáltatás kommunikál egy , amely az ügyfél által létrehozott. A `declare` `discharge` és az üzeneteket az adatkezelő küldi az ellenőrzési linken keresztül a tranzakciók lefoglalásához és befejezéséhez (ezek nem jelentik a tranzakciós munka elhatárolását). A tényleges küldés/fogadás nem hajtva végre ezen a hivatkozáson. Minden kért tranzakciós művelet kifejezetten `txn-id` azonosítható a kívánt módon, ezért a kapcsolat bármely hivatkozásán előfordulhat. Ha a vezérlőkapcsolat le van zárva, miközben léteznek nem lemerült tranzakciók, akkor az összes ilyen tranzakció azonnal visszalesz állítva, és a további tranzakciós munka végrehajtására tett kísérletek kudarchoz vezetnek. A vezérlőkapcsolaton lévő üzeneteket nem szabad előre rendezni.

Minden kapcsolatnak saját vezérlőkapcsolatot kell kezdeményeznie a tranzakciók indításához és befejezéséhez. A szolgáltatás egy speciális célt határoz `coordinator`meg, amely a rendszerként működik. Az ügyfél/vezérlő létrehoz egy vezérlőkapcsolatot ehhez a célhoz. A vezérlőkapcsolat kívül esik egy entitás határain, azaz ugyanaz a vezérlőkapcsolat több entitás tranzakcióinak kezdeményezésére és mentesítésére használható.

#### <a name="starting-a-transaction"></a>Tranzakció indítása

A tranzakciós munka megkezdéséhez. az adatkezelőnek `txn-id` be kell szereznie egy olyan adatkezelőt, amelyet a koordinátortól kell beszereznie. Ezt egy szöveges `declare` üzenet küldésével teszi. Ha a deklaráció sikeres, a koordinátor a megadott állapottal `txn-id`válaszol.

| Ügyfél (kontroller) | | Szolgáltatási busz (koordinátor) |
| --- | --- | --- |
| csatolása(<br/>name={link name},<br/>... ,<br/>role=**feladó**,<br/>target =**Koordinátor**<br/>) | ------> |  |
|  | <------ | csatolása(<br/>name={link name},<br/>... ,<br/>target=Koordinátor()<br/>) |
| átvitel(<br/>szállítás-id=0, ...)<br/>{ AmqpValue (**declare()**)}| ------> |  |
|  | <------ | hajlam( <br/> first=0, utolsó=0, <br/>state =**Deklarálva**(<br/>**txn-id**={transaction ID}<br/>))|

#### <a name="discharging-a-transaction"></a>Tranzakció teljesítése

Az adatkezelő a tranzakciós munkát `discharge` a koordinátornak küldött üzenettel zárja. Az adatkezelő jelzi, hogy a tranzakciós munkát a `fail` kibocsátási szerv rekedésének beállításával kívánja elkövetni vagy visszavonni. Ha a koordinátor nem tudja befejezni a kibocsátást, az `transaction-error`üzenetet elutasítják, és ezzel az eredménnyel a .

> Megjegyzés: a fail=true egy tranzakció visszaállítása, a fail=false pedig a Véglegesítés.

| Ügyfél (kontroller) | | Szolgáltatási busz (koordinátor) |
| --- | --- | --- |
| átvitel(<br/>szállítás-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | hajlam( <br/> first=0, utolsó=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | . . . <br/>Tranzakciós munka<br/>más linkeken<br/> . . . |
| átvitel(<br/>szállítás-id =57, ...)<br/>{ AmqpValue (<br/>**Discharge(txn-id=0,<br/>fail=false)**)}| ------> |  |
| | <------ | hajlam( <br/> first=57, utolsó=57, <br/>state =**Elfogadva()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Üzenet küldése tranzakcióban

Minden tranzakciós munka a txn-id-t hordozó tranzakciós kézbesítési állapottal `transactional-state` történik. Üzenetek küldése esetén a tranzakciós állapotot az üzenet átviteli kerete viszi. 

| Ügyfél (kontroller) | | Szolgáltatási busz (koordinátor) |
| --- | --- | --- |
| átvitel(<br/>szállítás-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | hajlam( <br/> first=0, utolsó=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| átvitel(<br/>fogantyú=1,<br/>szállítási azonosító=1, <br/>**state=<br/>TransactionalState(<br/>txn-id=0)**)<br/>{ hasznos adat }| ------> |  |
| | <------ | hajlam( <br/> első=1, utolsó=1, <br/>state=**<br/>TransactionalState( txn-id=0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Üzenet elküldése tranzakcióban

Az üzenetelhelyezés `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`olyan műveleteket tartalmaz, mint a. Ezeket a műveleteket egy tranzakción belül, adja át a `transactional-state` a hajlam.

| Ügyfél (kontroller) | | Szolgáltatási busz (koordinátor) |
| --- | --- | --- |
| átvitel(<br/>szállítás-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | hajlam( <br/> first=0, utolsó=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | <------ |átvitel(<br/>fogantyú=2,<br/>szállítási azonosító=11, <br/>state=null)<br/>{ hasznos adat }|  
| hajlam( <br/> first=11, utolsó=11, <br/>state=**<br/>TransactionalState( txn-id=0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Speciális Service Bus képességek

Ez a szakasz az Azure Service Bus speciális képességeit ismerteti, amelyek az AMQP-bővítménytervezeteken alapulnak, amelyeket jelenleg az OASIS AMQP technikai bizottsága fejleszt. A Service Bus megvalósítja a vázlatok legújabb verzióit, és elfogadja a bevezetett módosításokat, amint ezek a piszkozatok elérik a szabványos állapotot.

> [!NOTE]
> A Service Bus Messaging speciális műveleteit kérés-válasz minta támogatja. Ezeknek a műveleteknek a részleteit a [Service Bus AMQP 1.0 cikkében ismertetjük.](service-bus-amqp-request-response.md)
> 
> 

### <a name="amqp-management"></a>AMQP-kezelés

Az AMQP felügyeleti specifikáció az első a cikkben tárgyalt bővítménytervezetek közül. Ez a specifikáció az AMQP protokoll on-ra rétegzett protokollokat határozza meg, amelyek lehetővé teszik az amqp-n keresztüli üzenetkezelési infrastruktúrával való felügyeleti interakciókat. A specifikáció olyan általános műveleteket határoz meg, mint *a létrehozás,* *olvasás,* *frissítés*és *törlés* az üzenetkezelési infrastruktúrán belüli entitások és a lekérdezési műveletek készletének kezeléséhez.

Mindezek a kézmozdulatok az ügyfél és az üzenetkezelési infrastruktúra közötti kérés-válasz interakciót igényelnek, ezért a specifikáció meghatározza, hogyan modellezze az interakciós mintát az AMQP-n felül: az ügyfél csatlakozik az üzenetkezelési infrastruktúrához, munkamenetet kezdeményez, majd létrehoz egy pár kapcsolatot. Az egyik linken az ügyfél küldőként, a másikon pedig fogadóként működik, így létrehozva egy kétirányú csatornaként használható hivatkozásokat.

| Logikai művelet | Ügyfél | Service Bus |
| --- | --- | --- |
| Kérelem-válasz elérési útjának létrehozása |--> csatolja(<br/>name={*hivatkozásneve*},<br/>handle={*numerikus leíró*},<br/>role=**feladó**,<br/>forrás =**null**,<br/>target="saját entitás/$management"<br/>) |Nincs művelet |
| Kérelem-válasz elérési útjának létrehozása |Nincs művelet |\<-- csatolja(<br/>name={*hivatkozásneve*},<br/>handle={*numerikus leíró*},<br/>role =**vevő,**<br/>source=null,<br/>target="saját entitás"<br/>) |
| Kérelem-válasz elérési útjának létrehozása |--> csatolja(<br/>name={*hivatkozásneve*},<br/>handle={*numerikus leíró*},<br/>role =**vevő,**<br/>forrás="saját entitás/$management",<br/>target="myclient$id"<br/>) | |
| Kérelem-válasz elérési útjának létrehozása |Nincs művelet |\<-- csatolja(<br/>name={*hivatkozásneve*},<br/>handle={*numerikus leíró*},<br/>role=**feladó**,<br/>forrás="saját entitás",<br/>target="myclient$id"<br/>) |

Miután a pár linket a helyén, a kérelem/válasz megvalósítása egyszerű: a kérelem egy üzenetet küldött egy entitás az üzenetkezelési infrastruktúrán belül, amely megérti ezt a mintát. Ebben a kérés-üzenetben a *tulajdonságok* szakasz *válasz-to* mezője annak a hivatkozásnak a *célazonosítójára* van beállítva, amelyre a választ küldeni szeretné. A kezelő entitás feldolgozza a kérelmet, majd a választ azon a hivatkozáson keresztül adja meg, amelynek *célazonosítója* megegyezik a jelzett *válaszazonosítóval.*

A minta nyilvánvalóan megköveteli, hogy az ügyfél tároló és az ügyfél által létrehozott azonosító a válasz cél egyedi minden ügyfél, és biztonsági okokból is nehéz megjósolni.

A felügyeleti protokollhoz és az azonos mintát használó összes többi protokollhoz használt üzenetváltások az alkalmazás szintjén történnek; nem határoznak meg új AMQP protokollszintű kézmozdulatokat. Ez szándékos, így az alkalmazások azonnal kitudják használni ezeket a bővítményeket a megfelelő AMQP 1.0-s veremekkel.

A Service Bus jelenleg nem valósítja meg a felügyeleti specifikáció egyik alapvető jellemzőjét sem, de a felügyeleti specifikációáltal meghatározott kérés/válasz minta alapja a jogcímalapú biztonsági szolgáltatás és a következő szakaszokban tárgyalt speciális képességek szinte mindegyikéhez:

### <a name="claims-based-authorization"></a>Jogcímalapú engedélyezés

Az AMQP jogcímalapú engedélyezési (CBS) specifikációs tervezete a felügyeleti specifikációkérési/válaszmintára épül, és egy általánosított modellt ír le az összevont biztonsági jogkivonatok AMQP-vel való használatára vonatkozóan.

Az AMQP bevezetőben tárgyalt alapértelmezett biztonsági modellje a SASL-en alapul, és integrálható az AMQP-kapcsolat imázsának kézfogásával. A SASL használatával rendelkezik azzal az előnnyel, hogy egy bővíthető modellt biztosít, amelyhez olyan mechanizmusok készletét határozták meg, amelyekből a SASL-re hivatalosan támaszkodó bármely protokoll előnyös lehet. E mechanizmusok között szerepel a felhasználónevek és jelszavak átvitelének "PLAIN", a TLS-szintű biztonsághoz való kötődés "EXTERNAL", az "ANONYMOUS" a kifejezett hitelesítés/engedélyezés hiányának kifejezésére, valamint számos olyan további mechanizmus, amely lehetővé teszi a hitelesítési és/vagy engedélyezési hitelesítő adatok vagy tokenek átadását.

Az AMQP SASL integrációjának két hátránya van:

* Minden hitelesítő adatok és jogkivonatok hatóköre a kapcsolathoz. Előfordulhat, hogy az üzenetkezelési infrastruktúra entitásonként differenciált hozzáférés-vezérlést szeretne biztosítani; például lehetővé teszi, hogy egy token tulajdonosának küldjön az A várólistába, de ne a B várólistába. Ha az engedélyezési környezet a kapcsolathoz van rögzítve, nem lehet egyetlen kapcsolatot használni, és mégis különböző hozzáférési jogkivonatokat használni az A és a B várólistához.
* A hozzáférési jogkivonatok általában csak korlátozott ideig érvényesek. Ez az érvényesség megköveteli, hogy a felhasználó rendszeresidőközönként újraszerezze a jogkivonatokat, és lehetőséget biztosít a jogkivonat kibocsátója számára, hogy megtagadja egy friss jogkivonat kiadását, ha a felhasználó hozzáférési engedélyei megváltoztak. Az AMQP-kapcsolatok hosszú ideig tarthatnak. A SASL modell csak lehetőséget biztosít a jogkivonat beállítására a kapcsolat időpontjában, ami azt jelenti, hogy az üzenetkezelési infrastruktúrának vagy le kell választania az ügyfelet, amikor a jogkivonat lejár, vagy el kell fogadnia annak kockázatát, hogy lehetővé teszi a folyamatos kommunikációt egy olyan ügyféllel, aki a hozzáférési jogokat időközben visszavonták.

Az AMQP CBS specifikáció, amelyet a Service Bus valósít meg, lehetővé teszi, hogy mindkét probléma elegáns megoldást biztosítson: Lehetővé teszi az ügyfél számára, hogy hozzáférési jogkivonatokat társítson az egyes csomópontokhoz, és frissítse ezeket a jogkivonatokat azok lejárta előtt, az üzenetfolyamat megszakítása nélkül.

A CBS egy *$cbs*nevű virtuális felügyeleti csomópontot határoz meg, amelyet az üzenetküldő infrastruktúra biztosít. A felügyeleti csomópont az üzenetkezelési infrastruktúra bármely más csomópontja nevében fogad el jogkivonatokat.

A protokoll kézmozdulat a felügyeleti specifikációban meghatározott kérés-válasz csere. Ez azt jelenti, hogy az ügyfél létrehoz egy pár kapcsolatot a *$cbs* csomóponttal, majd továbbítja a kimenő kapcsolaton lévő kérelmet, majd megvárja a választ a bejövő kapcsolaton.

A kérésüzenet alkalmazástulajdonságai a következőek:

| Kulcs | Optional | Érték típusa | Érték tartalma |
| --- | --- | --- | --- |
| Művelet |Nem |sztring |**put-token** |
| type |Nem |sztring |A kerülő token típusa. |
| név |Nem |sztring |Az a "közönség", amelyre a token vonatkozik. |
| Lejárati |Igen |időbélyeg |A jogkivonat lejárati ideje. |

A *name* tulajdonság azonosítja azt az entitást, amelyhez a jogkivonatot társkell társválasztani. A Service Bus ez az elérési út a várólista, vagy a témakör/előfizetés. A *típustulajdonság* azonosítja a token típusát:

| Token típusa | Token leírása | Konstitula | Megjegyzések |
| --- | --- | --- | --- |
| amqp:jwt |JSON webes token (JWT) |AMQP-érték (karakterlánc) |Még nem érhető el. |
| amqp:swt |Egyszerű webes token (SWT) |AMQP-érték (karakterlánc) |Csak az AAD/ACS által kibocsátott SWT-tokenek esetén támogatott |
| servicebus.windows.net:sastoken |Szolgáltatási busz SAS-token |AMQP-érték (karakterlánc) |- |

A tokenek jogokat biztosítanak. A Service Bus három alapvető jogról tud: a "Küldés" lehetővé teszi a küldést, a "Figyelés" lehetővé teszi a fogadást, a "Kezelés" pedig az entitások kezelését. Az AAD/ACS által kibocsátott SWT-tokenek kifejezetten jogcímként tartalmazzák ezeket a jogokat. A Service Bus SAS-jogkivonatok a névtérben vagy entitáson konfigurált szabályokra hivatkoznak, és ezek a szabályok jogosultságokkal vannak konfigurálva. A jogkivonat aláírása a szabályhoz társított kulccsal így a jogkivonat ot a megfelelő jogok kifejezésére teszi. A *put-token* használatával rendelkező entitáshoz társított jogkivonat lehetővé teszi, hogy a csatlakoztatott ügyfél a jogkivonat-jogok szerint kommunikáljon az entitással. Olyan hivatkozás, amelyben az ügyfél átveszi a *feladói* szerepkört, a "Küldés" jogot igényli; vevői *receiver* szerepkör bevétele a "Listen" jogot igényli.

A válaszüzenet a következő *alkalmazástulajdonságokat tartalmaz*

| Kulcs | Optional | Érték típusa | Érték tartalma |
| --- | --- | --- | --- |
| állapotkód |Nem |int |HTTP-válaszkód **[RFC2616]**. |
| állapotleírás |Igen |sztring |Az állapot leírása. |

Az ügyfél hívhatja *a put-token* többször és az üzenetkezelési infrastruktúra bármely entitás. A jogkivonatok hatóköre az aktuális ügyfélhez, és az aktuális kapcsolathoz van rögzítve, ami azt jelenti, hogy a kiszolgáló eldobja a megőrzött jogkivonatokat, amikor a kapcsolat megszakad.

A Service Bus jelenlegi megvalósítása csak az "ANONYMOUS" SASL-módszerrel együtt engedélyezi a CBS-t. Az SSL/TLS-kapcsolatnak mindig léteznie kell a SASL-kézfogás előtt.

Az ANONYMOUS mechanizmust ezért a kiválasztott AMQP 1.0 ügyfélnek kell támogatnia. A névtelen hozzáférés azt jelenti, hogy a kezdeti kapcsolati kézfogás, beleértve a kezdeti munkamenet létrehozását is, anélkül történik, hogy a Service Bus tudná, ki hozza létre a kapcsolatot.

A kapcsolat és a munkamenet létrejötte után *a* $cbs csomóponthoz csatolt hivatkozások csatolása és a *put-token* kérelem küldése az egyetlen engedélyezett művelet. Egy érvényes jogkivonatot kell beállítani sikeresen egy *put-token* kérelmet néhány entitás csomópont 20 másodpercen belül a kapcsolat létrehozása után, ellenkező esetben a kapcsolat egyoldalúan megszakad a Service Bus.

Az ügyfél ezt követően felelős a jogkivonat lejáratának nyomon követéséért. Amikor egy jogkivonat lejár, a Service Bus azonnal eldobja az összes kapcsolatot az adott entitáshoz való kapcsolaton. A probléma előfordulásának megelőzése érdekében az ügyfél bármikor lecserélheti a csomópont jogkivonatát egy újra a virtuális *$cbs* felügyeleti csomóponton keresztül ugyanazzal a *put-token* gesztussal, és anélkül, hogy a különböző kapcsolatokon forgalomban lévő hasznos forgalmi folyamat útjába állna.

### <a name="send-via-functionality"></a>Küldés funkció

[Send-via / Transfer sender](service-bus-transactions.md#transfers-and-send-via) egy olyan funkció, amely lehetővé teszi, hogy a szolgáltatásbusz továbbítsa egy adott üzenetet egy célentitásegy másik entitáson keresztül. Ez a funkció egyetlen tranzakcióban hajthatja végre az entitások közötti műveleteket.

Ezzel a funkcióval létrehoz egy feladót, és `via-entity`létrehozza a kapcsolatot a . A hivatkozás létrehozása során további információkat továbbít a rendszer a kapcsolaton található üzenetek/átvitelek valódi céljának megállapításához. Miután a csatolás sikeres volt, a hivatkozáson küldött összes üzenet automatikusan továbbításra kerül a *célentitásnak* *a via-entity*on keresztül. 

> Megjegyzés: A hitelesítést mind az *entitáson keresztül,* mind a *célentitáson* végre kell hajtani a kapcsolat létrehozása előtt.

| Ügyfél | | Service Bus |
| --- | --- | --- |
| csatolása(<br/>name={link name},<br/>role=feladó,<br/>source={ügyfélkapcsolat azonosítója},<br/>target=**{via-entity}**,<br/>**properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) | ------> | |
| | <------ | csatolása(<br/>name={link name},<br/>role=vevő,<br/>source={ügyfélkapcsolat azonosítója},<br/>target={via-entity},<br/>properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{célentitás} )] ) |

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az AMQP-ről, látogasson el az alábbi linkekre:

* [Service Bus AMQP – áttekintés]
* [AMQP 1.0 támogatása Service Bus particionált várólisták és témakörök]
* [AMQP a Windows Server service busszolgáltatásban]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus AMQP – áttekintés]: service-bus-amqp-overview.md
[AMQP 1.0 támogatása Service Bus particionált várólisták és témakörök]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP a Windows Server service busszolgáltatásban]: https://msdn.microsoft.com/library/dn574799.aspx
