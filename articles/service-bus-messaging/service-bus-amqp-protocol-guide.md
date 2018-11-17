---
title: Az Azure Service Bus és Event Hubs protokoll – útmutató az AMQP 1.0-s |} A Microsoft Docs
description: Protokoll – útmutató, kifejezések és az Azure Service Bus és Event Hubs az AMQP 1.0-t leírása
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: clemensv
manager: timlt
editor: ''
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: 0801e3a0e9217ab0855d09df8a054926b488d759
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821548"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Az AMQP 1.0-s verziójában az Azure Service Bus és Event Hubs protokoll – útmutató

Az Advanced Message üzenetsor protokoll 1.0 egy olyan szabványosított keretező és az átviteli protokoll aszinkron módon, biztonságosan és megbízhatóan a két fél közötti üzenetek átvitelére. Az Azure Service Bus-üzenetkezelés és az Azure Event Hubs elsődleges protokoll. Mindkét szolgáltatás is támogatja a HTTPS. A szellemi tulajdont képező SBMP protokollt is támogatott AMQP értéke van megszűnik.

Az AMQP 1.0-s széles körű iparági együttműködés, amely összefoglalni közbenső szállítók, például a Microsoft és a Red Hat, például JP Morgan belőlük az ember jelölő a pénzügyi szolgáltatások iparágát több üzenetkezelési közbenső felhasználós eredménye. A szabványosítás technikai fórum AMQP protokoll és a bővítmény leírásában OASIS, és azt elérje a szabvány nemzetközi, ISO/IEC 19494 formális jóváhagyásáról.

## <a name="goals"></a>Célok

Ez a cikk röviden összefoglalja az AMQP 1.0 specifikáció mellett bővítmény előírásokat, amely jelenleg az OASIS AMQP műszaki bizottság véglegesítése folyamatban van egy kis készletét üzenetkezelés az alapfogalmakat, és elmagyarázza, az Azure Service Bus valósítja meg, és ezek a specifikációk épül.

A célunk, hogy a bármely fejlesztő bármely meglévő AMQP 1.0-ügyfél stack használatával bármilyen platformon fogják tudni használni az Azure Service Bus AMQP 1.0-n keresztül.

Közös általános célú AMQP 1.0 sablonjaitokat, például az Apache Proton vagy AMQP.NET Lite már megvalósítása az összes mag AMQP 1.0 protokollokat. Ezen alapvető kézmozdulatok néha burkolja magasabb szintű API-val; Az Apache Proton két, az imperatív Messenger API-t és a reaktív jellegű reaktor API is kínál.

A következő hozzászólás feltételezzük, hogy a kapcsolatok, a munkamenetek, és a hivatkozások AMQP felügyeletét, és a keret adatátvitel és adatfolyam vezérlés kezelését a megfelelő verem (például Apache Proton C) által kezelt és nem igényel sok, ha van ilyen különös figyelmet az alkalmazásfejlesztők. Absztraktként feltételezzük, hogy létezik-e néhány API primitívek például a csatlakozáshoz, és valamilyen létrehozása *küldő* és *fogadó* absztrakciós objektumokat, amely azután rendelkezik néhány alakját `send()` és `receive()` műveleteket, illetve.

Speciális funkciókat biztosítanak az Azure Service Bus, mint az üzenetek közötti böngészés vagy a munkamenetek, felügyeleti ismertetésekor AMQP használati feltételeiben szereplő, hanem egy rétegzett ily módon kvázi megvalósítása a feltételezett API absztrakciós felett, ezek a funkciók magyarázata.

## <a name="what-is-amqp"></a>Mi az AMQP?

Az AMQP egy olyan keretező és az átviteli protokoll. Keretező azt jelenti, hogy a hálózati kapcsolat mindkét irányban flow bináris adatfolyamok struktúra biztosítja. A struktúra biztosítja a különböző adatblokk, nevű körülhatárolásához *keretek*, ki kell cserélni a csatlakoztatott fél között. A fájlátviteli képességek győződjön meg arról, hogy mindkét kommunikáló fél létesíthet egy megosztott ismertetése, amikor keretek át kell, és amikor átvitelek befejezése minősül.

Lejárt draft korábbi verzióktól eltérően az AMQP munkacsoportja által előállított néhány üzenetközvetítők használja továbbra is szerepelnek a munkacsoport végleges, és a szabványos AMQP 1.0 protokoll nem határozza meg egy közvetítő- vagy bármely adott topológia a jelenléte entitások egy közvetítő belül.

A protokoll használható szimmetrikus társ-társ közötti kommunikáció, mint az Azure Service Bus-üzenetsorok és entitások közzététele vagy előfizetése támogató üzenetközvetítők való együttműködéshez szükséges. Azt is használható az üzenetkezelési infrastruktúra-szal, az interakció minták különböznek a rendszeres várólisták, hasonlóan az Azure Event Hubs. Egy Eseményközpont úgy viselkedik, mint egy üzenetsorba amikor események neki küldött, de úgy viselkedik, több mint egy soros társzolgáltatás; az események olvasása szalagos meghajtó némileg fog hasonlítani. Az ügyfél eltolás szerzi be a rendelkezésre álló adatfolyam, majd biztosítja a legújabb elérhető, hogy az eltolást összes eseménye.

Az AMQP 1.0 protokoll bővíthető, a képességek javításához specifikációk további engedélyezése tervezték. A dokumentumban ismertetett három bővítmény specifikációk ezt mutatják be. A meglévő HTTPS vagy Websocketek infrastruktúrára közötti kommunikáció a natív AMQP TCP-portok beállítása nehéz lehet. Egy kötelező érvényű specification AMQP réteg keresztül a websockets protokoll hogyan határozza meg. A kérelem/válasz módon felügyeleti célból vagy speciális funkciókat biztosít az üzenetkezelési infrastruktúra használatához, az AMQP-management specifikáció a szükséges alapszintű interakció primitívek határozza meg. Az összevont hitelesítési modell integráció az AMQP jogcím-alapú biztonsági specifikáció társítani, és újítsa meg a hitelesítési tokenek hivatkozások társított határozza meg.

## <a name="basic-amqp-scenarios"></a>Alapszintű AMQP-forgatókönyvek

Ez a szakasz ismerteti az alapvető használatát az AMQP 1.0-t az Azure Service busszal, például a kapcsolatokat, munkamenetek és hivatkozások létrehozása és átvitele az üzeneteket a Service Bus-entitások, például az üzenetsorok, témakörök és előfizetések.

A legtöbb mérvadó forrás AMQP működésével kapcsolatos további az AMQP 1.0 specifikáció, de a specifikációnak íródott pontosan a megvalósítási útmutató és a tanórákon a protokollt. Ez a szakasz tárgyalja, amely leírja, hogyan használja a Service Bus az AMQP 1.0 szükséges lehető terminológia ismertetése. Az AMQP-re egy átfogóbb bemutatása, valamint az AMQP 1.0-s szélesebb körű hatásának a megbeszélését, áttekintheti a [a videós képzésünket][this video course].

### <a name="connections-and-sessions"></a>Kapcsolatok és a munkamenetek

AMQP meghívja a kommunikáló programok *tárolók*; ezeket tartalmaznia *csomópontok*, melyek a kommunikáló entitások készregyártásának belül. A várólista ilyen egy csomópont lehet. Az AMQP lehetővé teszi, hogy multiplexálási, így sok kommunikációs útvonalak csomópontok; közötti egyetlen kapcsolaton is használható. például egy alkalmazás-ügyfél egyszerre egy üzenetsorból és -küldésre használható egy másik üzenetsornak az azonos hálózati kapcsolaton keresztül.

![][1]

A hálózati kapcsolat az így rögzített, a tárolón. Ez a tároló egy kimenő TCP szoftvercsatorna-kapcsolatot egy tároló létrehozása a fogadó szerepkört, amely figyeli, és fogadja a bejövő TCP-kapcsolatokat a szerepkör ügyfél által kezdeményezett. A kapcsolati kézfogás magában foglalja a egyeztetése deklaráló, vagy átviteli szintű Security (TLS/SSL) használata, és a egy hitelesítési kézfogás a SASL alapuló kapcsolat hatókörben egyeztetett protokoll verziója.

Az Azure Service Bus mindig a TLS használatát igényli. Támogatja a kapcsolatokat, 5671-es TCP-porton keresztül, amelynek során a TCP-kapcsolat első átfedésben van a TLS az AMQP protokoll kézfogási megadása előtt, és is támogatja kapcsolatok 5672 TCP-porton keresztül, amelynek során a kiszolgáló azonnal kínál egy kötelező frissítés kapcsolat a TLS AMQP előírt modellt használja. Az AMQP WebSockets kötés alagutat hoz létre a TCP 443-as portot, amely majd egyenértékű 5671-es AMQP-kapcsolatok felett.

Miután beállította a kapcsolatot és a TLS, a Service Bus SASL-mechanizmust két lehetőség kínál:

* SASL egyszerű felhasználónévvel és jelszóval hitelesítő adatokat továbbítja egy kiszolgálót gyakran használják. A Service Bus nem rendelkezik a partnerekkel, de elnevezett [megosztott hozzáférési biztonsági szabályok](service-bus-sas.md), amely rights ruháznak és -kulcs társítva. A szabály nevét használja a felhasználónév és a kulcs (a base64 kódolású szöveget) használt jelszót. A kiválasztott szabály hozzárendelt jogosultságok a kapcsolatot az engedélyezett műveletek szabályozásához.
* NÉVTELEN SASL használható SASL engedélyezési kihagyásával, amikor az ügyfél kíván a jogcím-alapú biztonság (CBS) modellt használja, a későbbiekben olvashat. Ezzel a beállítással egy ügyfél kapcsolat névtelenül egy rövid ideig, amely során az ügyfél csak használhatják a CBS-végpont, és a CBS-kézfogás kell végrehajtania.

A szállítási kapcsolat létrejötte után az egyes tárolókat deklarálja a keret maximális mérete hajlandó kezelni, és üresjárati időkorlátot után lesz egyoldalúan megszakad nincs tevékenység esetén a kapcsolat.

Akkor is deklarálja, hány egyidejű csatornák támogatottak. A csatorna egy olyan felül a kapcsolat egy egyirányú, kimenő, virtuális átviteli elérési utat. A munkamenet egy csatornát, az egyes kialakításához arra az egy kétirányú kommunikációs útvonalat az összekapcsolt tárolók vesz igénybe.

Munkamenetek rendelkezik egy Windows-alapú adatfolyam-vezérlési modell; a munkamenet létrehozását követően mindkét fél deklarálja hány keretek arra, hogy az fogadja el a fogadási ablak. A felek exchange keretek, átvitt keretek kitöltés ablakot, és átvitel állítsa le, amikor az ablak megtelik, és addig, amíg az ablak alaphelyzetbe állítása, illetve bővített használatával lekérdezi, mint a *performative folyamat* (*performative* van az AMQP kifejezés a két fél között cserélődő protokollszintű kézmozdulatok).

Ez a Windows-alapú modell nagyjából hasonlatos TCP fogalma a Windows-alapú adatfolyam vezérlés, de a szoftvercsatorna a munkamenet szintjén. A protokoll fogalmát, ami lehetővé teszi több egyidejű munkamenetek létezik, így magas prioritású forgalom sikerült kell rushed korábbi szabályozott szokásos forgalmi, például a highway expressz sávonkénti elemek.

Az Azure Service Bus jelenleg minden egyes kapcsolathoz pontosan egy munkamenet használja. A Service Bus maximális keret-mérete a standard szintű Service Bus és Event Hubs 262 144 bájt (256 KB). 1 048 576 (1 MB), a Service Bus prémium szintű. A Service Bus bármely adott munkamenet-szint sávszélesség-szabályozási windows nem ír elő, de a hivatkozás-szintű átvitelvezérlés részeként rendszeresen alaphelyzetbe állítja az ablak (lásd: [a következő szakaszban](#links)).

Kapcsolatok, csatornák és a munkamenetek olyan rövid élettartamú. Ha az alapul szolgáló kapcsolatot magasságúra csökken, kapcsolatok, a TLS-alagút, a SASL engedélyezési környezet és a munkamenetek kell hozni.

### <a name="links"></a>Hivatkozások

Az AMQP üzenetek kapcsolatokon keresztül továbbítja. Egy kommunikációs útvonal, amely lehetővé teszi egy irányban; való továbbítása során üzenetek munkameneten keresztül létrehozott hivatkozása az Átadás állapota egyeztetés a hivatkozást és a csatlakoztatott fél közötti kétirányú felett van.

![][2]

Bármikor és a egy meglévő munkamenethez, ami lehetővé teszi számos egyéb protokollok, beleértve a HTTP és az MQTT, ahol a átvitelek és az átvitel elérési kezdeményezése az a entitás létrehozása egy kizárólagos jogosultsággal eltérő AMQP keresztül hivatkozások hozható létre vagy a tároló a szoftvercsatorna-kapcsolatot.

A hivatkozást kezdeményező tárolót kéri, fogadja el a hivatkozást a ellentétes tároló és a egy szerepkört a küldő vagy fogadó úgy dönt, azt. Ezért vagy kezdeményezheti a tároló létrehozása egyirányú, vagy kétirányú kommunikációs útvonalat, ez utóbbi modellezett párokként mutató hivatkozásokat.

Hivatkozások nevű, és a csomópontok társított. A kezdő leírtaknak csomópontokat tartalmazza a kommunikáló entitásokat a tárolókon belül.

A Service Bus a csomópont megegyezik közvetlenül egy üzenetsor, témakör, előfizetés vagy a kézbesíthetetlen üzenetek sorába,-üzenetsor vagy előfizetés. A csomópont nevét, az AMQP használt ezért belül a Service Bus-névtér az entitás relatív neve. Ha egy üzenetsor neve `myqueue`, ez is a AMQP csomópont nevét. Egy témakör-előfizetésben a HTTP API egyezmény a következő szerint rendezi az "előfizetések" erőforrás-gyűjtemény, és így egy előfizetés **sub** vagy -témakör **mytopic** AMQP a csomópont neve  **mytopic/előfizetés/sub**.

A kapcsolódó ügyfelek is kell használnia a helyi csomópont nevét; hivatkozások létrehozása A Service Bus nem csomópont nevére vonatkozó előírásoknak megfelelő és nem értelmezi őket. AMQP 1.0-ügyfél implementációt általánosan használatával egy sémát, hogy biztosítsa, hogy ezeket a rövid élettartamú csomópont egyedinek kell lennie az ügyfél hatókörén belül.

### <a name="transfers"></a>Adatátvitel

A kapcsolat létrejöttét követően üzeneteket továbbíthatók a kapcsolaton keresztül. AMQP és átadási egy kifejezett protokoll hitelesítési módok végre (az *átviteli* performative), amely egy üzenetet helyezi a feladótól fogadó kapcsolaton keresztül. A átvitel befejeződött, "kiegyenlítését", ami azt jelenti, hogy a két fél létrehozott egy megosztott ismertetése az átvitel eredményéről.

![][3]

A legegyszerűbb esetben a küldő lehet váltani, ami azt jelenti, hogy az ügyfél nem érdekli az eredmény, és a fogadó nem biztosít semmilyen visszajelzés a művelet eredményéről "előre rendezni," üzenetek küldéséhez. Ebben a módban az AMQP protokoll szintjén a Service Bus által támogatott, de nem jelennek meg az ügyfél API-k valamelyikét.

A normál esetben, hogy az üzenetek küldése kiegyenlítetlen, és a fogadó majd azt jelzi, hogy elfogadása vagy elutasítása használatával a *disposition* performative. Elutasítási akkor fordul elő, ha a fogadó bármilyen okból nem tud fogadni az üzenetet, és az elutasítás üzenet okát, és egy hiba struktúra AMQP által definiált információt tartalmaz. Ha az üzenetek Service Bus alapjait belső hiba miatt a rendszer elutasítja, a szolgáltatás diagnosztikai mutatók, amelyek biztosítják a támogatási személyzetnek, ha támogatáskérések felhasználható struktúra lévő további információkat adja vissza. Később megtudhatja, hibákkal kapcsolatos további részletekért.

Egy speciális formája, elutasító a *kiadott* állapotát, amely azt jelzi, hogy a címzett az átadást nem műszaki kifogást, de még a nem érdekli az átvitel stabilizálódási rendelkezik-e. Adott esetben létezik, például amikor egy üzenetet a Service Bus-ügyfélalkalmazást, és az ügyfél úgy dönt, hogy "üzenet zárolásának feloldása" a munkát, az üzenet; eredő nem hajtható végre, mert az üzenetek kézbesítését, maga nem hibás. Egy adott állapotot változata van a *módosított* állapotát, amely lehetővé teszi, hogy az üzenet módosításai akkor szabadul fel, mert. Az állapotban nem, jelenleg a Service Bus használják.

Az AMQP 1.0 specifikáció határozza meg a további disposition nevű állapot *kapott*, kezelni a helyreállítási hivatkozásra, amely kifejezetten segítséget nyújt. Hivatkozás recovery lehetővé teszi, hogy újbóli megállapításának módjaival egy hivatkozást, és minden függő kézbesítések felül egy új kapcsolat és a munkamenetet, ha az előző kapcsolat és a munkamenet elvesztek állapotát.

A Service Bus nem támogatja a helyreállítási hivatkozásra; Ha kiegyenlítetlen üzenetet az ügyfél elveszíti a Service Bus a kapcsolat átvitele a függőben lévő, az üzenet átvitel megszakad, és az ügyfél kell újracsatlakozni, megszüntette a hivatkozásra, és próbálja megismételni az átvitel.

Emiatt a Service Bus és Event Hubs támogatja a "legalább egyszeri" átvitel, amikor a küldő tárolt, és hogy elfogadta az üzenet lehet abban is, de nem támogatja "pontosan egyszeri" adatátvitel AMQP szinten, ahol a rendszer megpróbálja helyreállítani a hivatkozás ehhez és továbbra is a szállítási állapotot üzenetátvitel elkerülendő egyeztetéséhez.

A meghiúsult lépések kompenzációjához lehetséges ismétlődő küld, a Service Bus üzenetsorok és témakörök támogatja a választható szolgáltatás ismétlődő észlelését. Duplikáltelem-észlelési üzenet azonosítóját az összes bejövő üzenetek rögzíti a felhasználó által megadott időszak alatt, majd csendes csökken a ugyanazon üzenet-azonosítóval rendelkező, ugyanazon időszakban küldött összes üzenet.

### <a name="flow-control"></a>Átvitelvezérlés

A munkamenet-szintű folyamat vezérlési modellhez, amely a korábban tárgyalt, mellett mindegyik csatolása rendelkezik a saját folyamat vezérlési modellt. Kapcsolati szintű átvitelvezérlés nem kell kezelni a túl sok képkockákat, miután hivatkozás szintű átvitelvezérlés-be illeszti be az alkalmazás felelős szeretné kezelni, egy hivatkozás, hogy hány üzeneteket, és ha a tároló védi.

![][4]

A hivatkozáshoz, adatátvitel csak fordulhat elő, amikor a küldő rendelkezik elegendő *kredit hivatkozás*. Hivatkozás kredit egy számláló használ a fogadó által az *folyamat* performative, amely hivatkozást hatókörét. A küldő hivatkozás kredit hozzárendelésekor próbál használni őket, hogy a hitelintézetek azáltal, hogy az üzeneteket. Mindegyik üzenet kézbesítési csökkenti a fennmaradó hivatkozás pedig 1. A hivatkozás kredit használata esetén a szállítások állítsa le.

Ha a fogadó szerepkör Service Bus, azonnal biztosít a küldő bőséges hivatkozás kreditet kap, így azonnal elküldött üzenetek. Hivatkozás kredit használt, a Service Bus alkalmanként küld egy *folyamat* performative hivatkozás kreditegyenlegét frissíteni a küldőnek.

A küldő szerepkör Service Bus elhasználja a szálankénti függőben lévő hivatkozás kreditegyenlege üzeneteket küld.

Az API szintjén "jelenik meg" hívás azt jelenti, hogy egy *folyamat* performative küld a Service Bus által az ügyfél és a Service Bus használ fel, hogy a kreditek tovább az első elérhető, nem zárolt üzenetet az üzenetsorból, zárolással, és áthelyezte azt. Nem jelenik üzenet kézbesítési könnyen használható, ha bármely csatlakozásonkénti szálankénti függőben lévő kreditegyenlege létrehozni, hogy adott entitás rögzített érkezési sorrendben marad, és az üzenetek zárolva van és használatához a szálankénti függőben lévő kreditegyenlege elérhetővé váló át.

Lévő üzenet zárolását akkor szabadul fel, ha az átvitel kiegyenlítése a terminál állapotokba *elfogadott*, *elutasított*, vagy *kiadott*. Ha az állapotot az üzenet törlődik a Service Bus *elfogadott*. Ez a Service Bus marad, és kézbesíti a rendszer a következő fogadó amikor az átvitel eléri a többi állapotok valamelyikében. A Service Bus automatikusan áthelyezi az üzenetet az entitás kézbesítetlen levelek várólistájára, amikor eléri az engedélyezett ismétlődő elutasítások vagy kiadásai miatt az entitás kézbesítések maximális száma.

Annak ellenére, hogy a Service Bus API-k közvetlenül nem teszik elérhetővé a ilyen beállítást még ma, alacsonyabb szintű AMQP protokoll ügyfél használhatja-e a hivatkozás-kredit modell kapcsolhatja a kredit az egyes fogadási kérést egy egység kiállító a "leküldéses stílusú" modellbe "pull-stílusú" közötti sok kiállító kreditek hivatkozásra, és további felhasználói beavatkozás nélkül elérhetővé váló majd üzenetfogadáshoz. Leküldéses keresztül támogatott a [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PrefetchCount) vagy [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount) eszköztulajdonság-beállítások. Ha nullától eltérő, az AMQP-ügyfél használja a hivatkozás kreditet.

Ebben a környezetben fontos megérteni, hogy az üzenet az entitás belül a zárolás lejárta órája elindul, amikor az üzenet forrása az entitás nem az üzenet az üzembe helyezés a keresztülhaladnak a hálózaton. Minden alkalommal, amikor az ügyfél azt jelzi, hogy üzeneteket fogadni hivatkozás kredit kiállításával készültségi, ezért várt lehet aktívan beolvasás üzenetek a hálózaton keresztül, és készen áll az őket. Az üzenet zárolási egyébként lejártak, mielőtt még az üzenetek kézbesítése. Hivatkozás-kredit adatfolyam vezérlés használatát közvetlenül tükröznie kell azonnal készen áll-e elküldeni, a fogadó elérhető üzenetek kezelésére.

Összefoglalva a következő szakaszok performative folyamat sematikus áttekintése különböző API-interakció során. Minden szakasz azt ismerteti, hogy egy másik logikai művelet. Néhány párbeszéd lehet "Lusta," ami azt jelenti, akkor előfordulhat, hogy csak lehet végrehajtani, ha szükséges. Egy üzenet küldője létrehozása nem okozhat a hálózati kapcsolati mindaddig, amíg az első üzenet küldött vagy a kért.

Az alábbi táblázat a nyilak a performative folyamat iránya.

#### <a name="create-message-receiver"></a>Üzenetet fogadó létrehozása

| Ügyfél | Service Bus |
| --- | --- |
| --> () csatolása<br/>név = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**fogadó**,<br/>forrás = {entitás neve},<br/>cél = {link Ügyfélazonosító}<br/>) |Entitás fogadóként csatolja ügyfél |
| A Service Bus-válaszok csatolása a kapcsolat vége |<--csatolása ()<br/>név = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**küldő**,<br/>forrás = {entitás neve},<br/>cél = {link Ügyfélazonosító}<br/>) |

#### <a name="create-message-sender"></a>Üzenet küldője létrehozása

| Ügyfél | Service Bus |
| --- | --- |
| --> () csatolása<br/>név = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**küldő**,<br/>forrás = {ügyfél hivatkozás azonosítója},<br/>cél = {entitás neve}<br/>) |Nincs művelet |
| Nincs művelet |<--csatolása ()<br/>név = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**fogadó**,<br/>forrás = {ügyfél hivatkozás azonosítója},<br/>cél = {entitás neve}<br/>) |

#### <a name="create-message-sender-error"></a>Hozzon létre az üzenet küldője (hiba)

| Ügyfél | Service Bus |
| --- | --- |
| --> () csatolása<br/>név = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**küldő**,<br/>forrás = {ügyfél hivatkozás azonosítója},<br/>cél = {entitás neve}<br/>) |Nincs művelet |
| Nincs művelet |<--csatolása ()<br/>név = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**fogadó**,<br/>forrás = null,<br/>cél = null<br/>)<br/><br/><--leválasztása ()<br/>kezelni = {numerikus leíró},<br/>lezárt =**igaz**,<br/>Hiba = {hibainformáció}<br/>) |

#### <a name="close-message-receiversender"></a>Üzenet bezárása címzett/feladó

| Ügyfél | Service Bus |
| --- | --- |
| --> () leválasztása<br/>kezelni = {numerikus leíró},<br/>lezárt =**igaz**<br/>) |Nincs művelet |
| Nincs művelet |<--leválasztása ()<br/>kezelni = {numerikus leíró},<br/>lezárt =**igaz**<br/>) |

#### <a name="send-success"></a>Elküldése (sikeres)

| Ügyfél | Service Bus |
| --- | --- |
| --> átvitel)<br/>kézbesítési-id = {numerikus leíró},<br/>kézbesítési-címke = {bináris leíró},<br/>Kiegyenlített =**hamis**,, több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**false (hamis)**<br/>) |Nincs művelet |
| Nincs művelet |<--törlése ()<br/>szerepkör, fogadó =<br/>első = {kézbesítési azonosítója},<br/>utolsó = {kézbesítési azonosítója},<br/>Kiegyenlített =**igaz**,<br/>állapot =**elfogadva**<br/>) |

#### <a name="send-error"></a>Küldése (hiba)

| Ügyfél | Service Bus |
| --- | --- |
| --> átvitel)<br/>kézbesítési-id = {numerikus leíró},<br/>kézbesítési-címke = {bináris leíró},<br/>Kiegyenlített =**hamis**,, több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**false (hamis)**<br/>) |Nincs művelet |
| Nincs művelet |<--törlése ()<br/>szerepkör, fogadó =<br/>első = {kézbesítési azonosítója},<br/>utolsó = {kézbesítési azonosítója},<br/>Kiegyenlített =**igaz**,<br/>állapot =**elutasított**()<br/>Hiba = {hibainformáció}<br/>)<br/>) |

#### <a name="receive"></a>Fogadás

| Ügyfél | Service Bus |
| --- | --- |
| a folyamat (--><br/>hivatkozás-kredit = 1<br/>) |Nincs művelet |
| Nincs művelet |< transfer ()<br/>kézbesítési-id = {numerikus leíró},<br/>kézbesítési-címke = {bináris leíró},<br/>Kiegyenlített =**hamis**,<br/>több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**false (hamis)**<br/>) |
| --> () törlése<br/>szerepkör =**fogadó**,<br/>első = {kézbesítési azonosítója},<br/>utolsó = {kézbesítési azonosítója},<br/>Kiegyenlített =**igaz**,<br/>állapot =**elfogadva**<br/>) |Nincs művelet |

#### <a name="multi-message-receive"></a>Több üzenet fogadása

| Ügyfél | Service Bus |
| --- | --- |
| a folyamat (--><br/>hivatkozás-kredit = 3<br/>) |Nincs művelet |
| Nincs művelet |< transfer ()<br/>kézbesítési-id = {numerikus leíró},<br/>kézbesítési-címke = {bináris leíró},<br/>Kiegyenlített =**hamis**,<br/>több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**false (hamis)**<br/>) |
| Nincs művelet |< transfer ()<br/>kézbesítési-id = {numerikus leíró + 1},<br/>kézbesítési-címke = {bináris leíró},<br/>Kiegyenlített =**hamis**,<br/>több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**false (hamis)**<br/>) |
| Nincs művelet |< transfer ()<br/>kézbesítési-id = {numerikus leíró + 2},<br/>kézbesítési-címke = {bináris leíró},<br/>Kiegyenlített =**hamis**,<br/>több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**false (hamis)**<br/>) |
| --> () törlése<br/>szerepkör, fogadó =<br/>első = {kézbesítési azonosítója},<br/>utolsó = {kézbesítési azonosító + 2},<br/>Kiegyenlített =**igaz**,<br/>állapot =**elfogadva**<br/>) |Nincs művelet |

### <a name="messages"></a>Üzenetek

Az alábbi szakaszok ismertetik a Service Bus AMQP üzenet standard szakaszokban mely tulajdonságok használja, és hogyan leképezik a Service Bus API-készlethez.

Bármely vlastnost alkalmazása szükséges határozza meg kell feleltetni AMQP a `application-properties` térképet.

#### <a name="header"></a>header

| Mezőnév | Használat | API neve |
| --- | --- | --- |
| tartós |- |- |
| prioritás |- |- |
| TTL |Az üzenet élettartama |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) |
| első-beszerző |- |- |
| kézbesítések-száma |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) |

#### <a name="properties"></a>properties

| Mezőnév | Használat | API neve |
| --- | --- | --- |
| üzenetazonosító |Ez az üzenet alkalmazás által meghatározott, a szabad formátumú azonosítója. Duplikáltelem-észlelési használja. |[Üzenetazonosító](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) |
| felhasználói azonosító |Alkalmazás által meghatározott felhasználói azonosító, a Service Bus nem értelmezi. |Nem a Service Bus API-n keresztül érhető el. |
| erre: |Alkalmazás által meghatározott cél azonosítója nem értelmezi a Service Bus. |[Címzett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_To) |
| tárgy |Alkalmazás által meghatározott üzenetet célú azonosítója, a Service Bus nem értelmezi. |[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) |
| Válasz címzettje |Alkalmazás által meghatározott válasz-elérési út mutató, a Service Bus nem értelmezi. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyTo) |
| korrelációs azonosító |Alkalmazás által meghatározott korrelációs azonosító, nem értelmezi a Service Bus. |[korrelációs azonosító](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_CorrelationId) |
| a Content-type |Alkalmazás által meghatározott tartalomtípus-mutató nem értelmezi a Service Bus, a szervezet számára. |[a contentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType) |
| tartalom kódolása |Alkalmazás által meghatározott tartalom-kódolás mutató nem értelmezi a Service Bus, a szervezet számára. |Nem a Service Bus API-n keresztül érhető el. |
| abszolút-lejárati-idő |Deklarálja, mely abszolút azonnali üzenet lejár. A bemeneti (fejléc meg TTL), figyelmen kívül hagyja a kimenetet mérvadó. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ExpiresAtUtc) |
| létrehozás – ideje |Kijelenti, hogy mely az üzenet létrehozásának. A Service Bus által nem használt |Nem a Service Bus API-n keresztül érhető el. |
| csoport-azonosítója |Egy kapcsolódó üzenetkészletet alkalmazás által meghatározott azonosítója. Service Bus-munkamenetekkel használja. |[munkamenet-azonosító](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) |
| csoport-sorozat |A számláló azonosítására az üzenet egy munkamenet belüli relatív sorszáma. A Service Bus figyelmen kívül hagyja. |Nem a Service Bus API-n keresztül érhető el. |
| válasz a csoport azonosítója |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyToSessionId) |

#### <a name="message-annotations"></a>Üzenet jegyzetek

Van néhány service bus üzenet tulajdonságok is, amelyek nem részei az AMQP üzenet tulajdonságait, és mint továbbítódnak `MessageAnnotations` üzenetet.

| Jegyzet térkép kulcs | Használat | API neve |
| --- | --- | --- |
| x-jóváhagyás – ütemezett-sorba-idő | Deklarálja ekkor az üzenetnek meg kell jelennie az entitás |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-jóváhagyás – partíciós kulccsal | Alkalmazás által meghatározott kulcs, amely előírja, hogy melyik partíciót az üzenetet a megnyitja. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-jóváhagyás – keresztül-partíciós kulccsal | Alkalmazás által meghatározott partíciókulcs érték, amikor egy tranzakció átviteli keresztül üzenetek küldéséhez használt. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-jóváhagyás – várólistán lévő-idő | Szolgáltatás által definiált UTC idő a tényleges idő, az üzenet sorba jelző. Figyelmen kívül hagyja, a bemeneti. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-jóváhagyás –-sorozatszám | Szolgáltatás által definiált egyedi száma egy üzenetet. | [sequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x opt eltolása | Az üzenet sorba szolgáltatás által definiált sorszáma. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-jóváhagyás – zárolva-ig | Szolgáltatás által definiált. A dátum és idő, ameddig az üzenet zárolva lesz a várólista /-előfizetéséből. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-jóváhagyás – kézbesítetlen – forrás | Szolgáltatás által definiált. Ha az üzenetet a kézbesíthetetlen levelek sorában, a forrás az eredeti üzenet. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Tranzakció képesség

Egy tranzakció csoportosítja a két vagy több művelet-végrehajtási hatókör egyesítheti. Természetéből ilyen egy tranzakció biztosítania kell, hogy a műveletek egy adott csoporthoz tartozó összes művelet sikeres vagy sikertelen lehet közösen.
A műveletek-azonosító szerint vannak csoportosítva `txn-id`.

A tranzakciós műveletek, az ügyfél funkcionál a `transaction controller` , amely szabályozza a műveleteket, együtt kell csoportosítani. Service Bus szolgáltatás funkcionál, a `transactional resource` és munkát elvégzi a kérésének megfelelően a `transaction controller`.

Az ügyfél és a szolgáltatás keresztül kommunikálnak a `control link` , az ügyfél létrehozott. A `declare` és `discharge` üzenetek lefoglalni, és a tranzakciók elvégzéséhez, illetve a vezérlőelem-kapcsolaton keresztül a vezérlő által küldött (tranzakciós feladatainak határait nem képviselnek). A tényleges küldése/fogadása küldje erre a hivatkozásra nem történik meg. Minden tranzakciós műveletet kért explicit módon van a kívánt azonosított `txn-id` , és ezért fordulhat elő, a kapcsolat bármelyik hivatkozásra. Ha a vezérlési kapcsolat le van zárva, bár léteznek a létrehozást nem kibocsátott tranzakciók, majd az összes ilyen tranzakció azonnal vissza legyen állítva, és a rajtuk további tranzakciós feladatainak végrehajtására tett kísérletet eredményezi, hogy sikertelen lesz. Vezérlő hivatkozásra üzeneteket nem lehet rendezni előtti.

Minden kapcsolat kezdeményezése a saját vezérlő hivatkozás tudják kezdő és záró tranzakciók rendelkezik. A szolgáltatás határozza meg egy speciális cél, amely úgy működik, mint egy `coordinator`. Az ügyfél/vezérlő a cél vezérlő hivatkozást hoz létre. Vezérlő hivatkozás egy entitás határán kívül, azt jelenti, egyazon vezérlő kapcsolat kezdeményezéséhez és teljesíti a tranzakciók több entitás esetében használható.

#### <a name="starting-a-transaction"></a>Tranzakció indítása

Tranzakciós munka megkezdéséhez. a vezérlő be kell szereznie egy `txn-id` az koordinátortól. Ezt nem küld egy `declare` típusú üzenetet. Ha a nyilatkozat sikeres, a koordinátor fűzi hozzá a disposition eredményét, amely rendelkezik-e a hozzárendelt `txn-id`.

| Ügyfél (vezérlő) | | A Service Bus (koordinátor) |
| --- | --- | --- |
| Csatlakoztassa)<br/>név = {link name}.<br/>... ,<br/>szerepkör =**küldő**,<br/>cél =**koordinátora**<br/>) | ------> |  |
|  | <------ | Csatlakoztassa)<br/>név = {link name}.<br/>... ,<br/>TARGET=Coordinator()<br/>) |
| átvitel)<br/>kézbesítési-id = 0,...)<br/>{AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | törlése) <br/> első = 0, 0, utolsó = <br/>állapot =**Declared**()<br/>**tranzakciók – azonosító**= {Tranzakcióazonosító}<br/>))|

#### <a name="discharging-a-transaction"></a>Tranzakciók ellátása

A vezérlő arra a következtetésre jut a tranzakciós feladatainak küldésével egy `discharge` a koordinátor üzenetet. A vezérlő azt jelzi, hogy véglegesítse vagy állítsa vissza a tranzakciós feladatainak állításával felhasználja a `fail` mentesítés törzsében jelzővel. Ha a koordinátor nem tudja végrehajtani a mentesítés, a szállító serkenti az eredményt a visszautasítja az üzenetet a `transaction-error`.

> Megjegyzés: nem sikerült = true hivatkozik egy tranzakciót, és a sikertelen visszavonás = false véglegesítési hivatkozik.

| Ügyfél (vezérlő) | | A Service Bus (koordinátor) |
| --- | --- | --- |
| átvitel)<br/>kézbesítési-id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | törlése) <br/> első = 0, 0, utolsó = <br/>állapot deklarált () =<br/>tranzakciós-id = {Tranzakcióazonosító}<br/>))|
| | . . . <br/>Tranzakciós feladatainak<br/>egyéb hivatkozások<br/> . . . |
| átvitel)<br/>kézbesítési-id = 57,...)<br/>{AmqpValue)<br/>**Ideje (tranzakciós-id = 0,<br/>sikertelen = false)**)}| ------> |  |
| | <------ | törlése) <br/> első = 57, 57, utolsó = <br/>állapot =**az Accepted() művelet**)|

#### <a name="sending-a-message-in-a-transaction"></a>Üzenet küldése egy tranzakció

Összes tranzakciós munkát állapotú tranzakciós kézbesítési `transactional-state` , amely végzi, a tranzakciós-azonosítóhoz. Üzenetek küldése a tranzakciós állapot szerint az üzenet átviteli keret van végezni. 

| Ügyfél (vezérlő) | | A Service Bus (koordinátor) |
| --- | --- | --- |
| átvitel)<br/>kézbesítési-id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | törlése) <br/> első = 0, 0, utolsó = <br/>állapot deklarált () =<br/>tranzakciós-id = {Tranzakcióazonosító}<br/>))|
| átvitel)<br/>kezelni = 1,<br/>kézbesítési-id = 1, <br/>**állapot =<br/>TransactionalState (<br/>tranzakciók-id = 0)**)<br/>{adattartalom}| ------> |  |
| | <------ | törlése) <br/> először = 1, legutóbbi = 1, <br/>állapot =**TransactionalState (<br/>tranzakciók-id = 0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Egy üzenet valamely tranzakcióban értékesítésére

Üzenet törlése műveletek tartoznak, mint `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`. Ezeket a műveleteket a tranzakción belül, át kell adnia a `transactional-state` a disposition.

| Ügyfél (vezérlő) | | A Service Bus (koordinátor) |
| --- | --- | --- |
| átvitel)<br/>kézbesítési-id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | törlése) <br/> első = 0, 0, utolsó = <br/>állapot deklarált () =<br/>tranzakciós-id = {Tranzakcióazonosító}<br/>))|
| | <------ |átvitel)<br/>kezelni = 2,<br/>kézbesítési-id = 11, <br/>állapot = null)<br/>{adattartalom}|  
| törlése) <br/> első = 11, 11, utolsó = <br/>állapot =**TransactionalState (<br/>tranzakciók-id = 0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>A Service Bus speciális képességek

Ez a szakasz ismerteti a speciális funkciókat biztosítanak az Azure Service Bus jelenleg fejlesztés alatt áll az OASIS műszaki bizottság AMQP az AMQP és bővítmények draft alapuló. A Service Bus valósítja meg a legújabb ezek Piszkozatok, és ezeket Piszkozatok érni a normál üzenetterhelésen bevezetett változások fogad el.

> [!NOTE]
> Service Bus üzenetkezelési szolgáltatásának speciális műveletek támogatottak, egy kérés/válasz mintán keresztül. Ezek a műveletek részleteit a cikkben ismertetett [a Service Bus AMQP 1.0-s: kérés-válasz alapú műveletek](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>Az AMQP-kezelés

Az AMQP-management specifikáció az első a draft-bővítmények a cikkben leírtak szerint. Ez az meghatározás határozza meg azon egymás felett az AMQP protokollt protokollok, amelyek lehetővé teszik az üzenetkezelési infrastruktúra történő felügyeleti interakciókat amqp-n keresztül. A specifikációnak meghatározása általános műveletek, például *létrehozása*, *olvasási*, *frissítése*, és *törlése* entitások belüli kezelésére szolgáló egy üzenetküldési infrastruktúra, és többféle lekérdezési művelet.

Ezek hitelesítési módok szükséges egy kérés/válasz közötti interakció az ügyfél és az üzenetkezelési infrastruktúra, és ezért a specifikációnak határozza meg a kommunikációs felett AMQP modellezésére: az ügyfél csatlakozik az üzenetkezelési infrastruktúra munkamenet kezdeményez, és ekkor létrehoz egy pár mutató hivatkozásokat. Egy hivatkozásra az ügyfél a küldő funkcionál, és a többi úgy működik, mint fogadó, így az olyan virtuálisgép-pár, amelyek kétirányú csatornán hivatkozások létrehozása.

| Logikai művelet | Ügyfél | Service Bus |
| --- | --- | --- |
| Kérés-válasz elérési utat hoz létre |--> () csatolása<br/>név = {*hivatkozásnév*},<br/>kezelni = {*numerikus leíró*},<br/>szerepkör =**küldő**,<br/>forrás =**null**,<br/>target = "kezelési myentity / $"<br/>) |Nincs művelet |
| Kérés-válasz elérési utat hoz létre |Nincs művelet |\<--csatolása ()<br/>név = {*hivatkozásnév*},<br/>kezelni = {*numerikus leíró*},<br/>szerepkör =**fogadó**,<br/>forrás = null,<br/>target = "myentity"<br/>) |
| Kérés-válasz elérési utat hoz létre |--> () csatolása<br/>név = {*hivatkozásnév*},<br/>kezelni = {*numerikus leíró*},<br/>szerepkör =**fogadó**,<br/>forrás = "kezelési myentity / $",<br/>target = "myclient$ id"<br/>) | |
| Kérés-válasz elérési utat hoz létre |Nincs művelet |\<--csatolása ()<br/>név = {*hivatkozásnév*},<br/>kezelni = {*numerikus leíró*},<br/>szerepkör =**küldő**,<br/>forrás = "myentity",<br/>target = "myclient$ id"<br/>) |

A virtuálisgép-pár hivatkozásokat kellene helyen, a kérelem/válasz végrehajtására nagyon egyszerű: egy kérelme, mert egy entitás belül az üzenetküldési infrastruktúra, amely együttműködik a ezt a mintát egy üzenetet. A kérelem-üzenetet a *Válaszcím* mezőbe a *tulajdonságok* szakasz értékre van állítva a *cél* azonosítója a hivatkozás, amelyre a választ. A kezelési entitás feldolgozza a kérést, és ezután továbbítja a kapcsolaton keresztül a válasz amelynek *cél* azonosítója megegyezik a jelzett *Válaszcím* azonosítója.

A minta nyilvánvalóan megköveteli, hogy az ügyfél-tárolót és az ügyfél által generált azonosítójának a válasz cél egyediek-e az összes ügyfélre, és a biztonsági okokból is nehéz előre jelezni.

Az alkalmazás szintjén történik a üzenetváltásokban a felügyeleti protokollhoz tartozó, és az egyéb protokollok, amelyek ugyanazt a mintát használni a használt új AMQP protokoll-szintű kézmozdulatok nem definiálják. Ez szándékos, így az alkalmazások azonnali kihasználhatják a megfelelő AMQP 1.0-t az ilyen bővítmények.

A Service Bus nem jelenleg valósítja meg a management specifikáció alapvető szolgáltatások, de ha a kérés/válasz mintának határozzák meg a felügyeleti specifikációjának alapvető, a jogcím-alapú biztonsági szolgáltatás, és a speciális szinte az összes Az alábbiakban tárgyalt funkciók:

### <a name="claims-based-authorization"></a>Jogcímalapú engedélyezési

Az AMQP-jogcímek alapú hitelesítést (CBS) specifikáció draft épül, amely a management specifikáció kérés/válasz minta, és a egy összevont biztonsági jogkivonatokat az AMQP használatával általánosított modellt ismerteti.

Az alapértelmezett biztonsági modellt az AMQP tárgyalja a bevezetés SASL alapul, és az AMQP-kapcsolat kézfogást integrálható. Az előnye, hogy egy bővíthető modellt, amelyhez mechanizmusok készletét bármely korábbi leans a SASL protokoll előnyös lehet a definiált SASL használatával rendelkezik. Ezek a mechanizmusok közé tartoznak a felhasználónevek és jelszavak, átviteléhez kötést létrehozni a TLS-level security, Express explicit hitelesítés/engedélyezés és széles megadásának engedélyezése a következő kiegészítő mechanizmust hiányában a "névtelen" a "külső" "Egyszerű" hitelesítés és/vagy engedélyezési hitelesítő adatok vagy jogkivonatokat.

Az AMQP a SASL integrációs két hátrányai rendelkezik:

* A kapcsolat hitelesítő adatait, és a jogkivonatok hatóköre. Az üzenetkezelő előfordulhat, hogy szeretne biztosítani egy entitásonkénti adatkivitelt; egyedi hozzáférés-vezérlés például lehetővé téve a tulajdonosi jogkivonatok küldése egy üzenetsorba, de nem várólista B. Az engedélyezési környezet, a kapcsolat rögzített, és már nem használhatja egyetlen kapcsolaton keresztül, és még a várólista A és b várólista a másik hozzáférési kódok használata
* Hozzáférési jogkivonatok érvényesek általában csak korlátozott ideig. Az érvényességi a felhasználónak, hogy időnként visszanyerje a jogkivonatok, és lehetőséget biztosít a jogkivonat kibocsátója friss jogkivonat kiadása, a felhasználói hozzáférési engedélyek rendelkeznek megváltozásakor elutasítására. Az AMQP-kapcsolatok hosszú időn át maradhatnak. A SASL modell csak biztosít arra, hogy állítsa be a jogkivonatot kapcsolat időben, ami azt jelenti, hogy az üzenetkezelési infrastruktúra, akár rendelkezik az ügyfél leválasztása a jogkivonat lejár, vagy fogadja el a folyamatos kommunikáció az ügyfél kockázatát kell ki a hozzáférési jogosultságok előfordulhat, hogy visszavonták addig.

Megvalósította a Service Bus AMQP CBS-specifikáció mind ezeket a hibákat, lehetővé teszi egy elegáns megkerülő megoldás: lehetővé teszi az ügyfél hozzáférési jogkivonatok rendelni az egyes csomópontokon, és a jogkivonatok frissíteni, mielőtt azok lejár, az üzenet a folyamat megszakítása nélkül.

CBS határozza meg a virtuális felügyeleti csomópont, nevű *$cbs*, az üzenetkezelési infrastruktúra által. A felügyeleti csomópont bármely más csomópontok az üzenetküldési infrastruktúra nevében jogkivonatokat fogad.

A protokoll hitelesítési módok egy kérés/válasz az exchange felügyeleti specifikációja által meghatározott módon. Hogy azt jelenti, hogy az ügyfél létesít az hivatkozásokat párjai a *$cbs* csomópont ezután továbbítja a kérést a kimenő hivatkozásra, és megvárja a válasz a bejövő hivatkozásra.

A kérelemüzenet a következő alkalmazás tulajdonságokkal rendelkezik:

| Kulcs | Optional | Érték típusa | Érték tartalma |
| --- | --- | --- | --- |
| művelet |Nem |sztring |**PUT-token** |
| type |Nem |sztring |A token használatát típusa. |
| név |Nem |sztring |A "célközönség", amely a token vonatkozik. |
| lejárat |Igen |időbélyeg |A jogkivonat lejárati idejét. |

A *neve* tulajdonság azonosítja az entitást, amellyel a token társítva kell lennie. A Service Bus az üzenetsorok vagy üzenettémák/előfizetések elérési útját. A *típus* tulajdonság azonosítja a jogkivonat típusa:

| Jogkivonat típusa | Jogkivonat leírása | Törzs típusa | Megjegyzések |
| --- | --- | --- | --- |
| amqp:jwt |JSON webes jogkivonat (JWT) |Az AMQP-érték (karakterlánc) |Még nem érhető el. |
| amqp:swt |Egyszerű webes jogkivonat (SWT) |Az AMQP-érték (karakterlánc) |Csak a támogatott AAD/ACS által kiállított SWT-jogkivonatok |
| servicebus.Windows.NET:sastoken |Service Bus SAS-jogkivonat |Az AMQP-érték (karakterlánc) |- |

Jogkivonatok ruháznak jogokat. A Service Bus három alapvető jogokat ismer: "Küldés" lehetővé teszi a "Figyelés" lehetővé teszi, hogy a fogadását, és "a felügyelet" lehetővé teszi, hogy adatműveletekkel entitások küldhet. Aad-ben és az ACS által kiadott explicit módon SWT-jogkivonatok jogcímként szerepeljenek ezeket a jogokat. Service Bus SAS-tokeneket tekintse meg a névtér vagy entitás konfigurált szabályokat, és ezek a szabályok úgy vannak konfigurálva, jogosultságokkal. A jogkivonat aláírása, hogy a szabályhoz társított kulccsal így lehetővé teszi a token express a megfelelő jogosultságokat. A jogkivonat egy entitást a társított *put-token* lehetővé teszi a csatlakoztatott ügyfél kommunikálhat az entitás / token jogok. Egy hivatkozás, amennyiben az ügyfél fogja elvégezni a a *küldő* a szerepkör megköveteli a "Küldés" megfelelő; véve a *fogadó* a szerepkör megköveteli a "figyelés" jobb.

A válaszüzenet rendelkezik a következő *alkalmazástulajdonságok* értékek

| Kulcs | Optional | Érték típusa | Érték tartalma |
| --- | --- | --- | --- |
| állapotkód |Nem |int |HTTP-válaszkód **[RFC2616]**. |
| állapot – leírás |Igen |sztring |Az állapot leírása. |

Az ügyfél meghívhat *put-token* ismételt és az üzenetkezelési infrastruktúra bármelyik entitáshoz. A jogkivonatok az aktuális ügyfél hatóköre és az aktuális kapcsolatot, ami azt jelenti, a kiszolgáló megszakítja a megőrzött jogkivonatokat, amikor a kapcsolat csökken a rögzített.

A Service Bus jelenlegi implementációja csak lehetővé teszi a CBS-től együtt a SASL módszerrel "Névtelen". SSL/TLS-kapcsolatot a SASL-kézfogás előtt mindig léteznie kell.

A névtelen mechanizmus ezért támogatnia kell a kiválasztott AMQP 1.0-ügyfél. Névtelen hozzáférés azt jelenti, hogy, hogy a kezdeti kapcsolati kézfogás történik, mint például a kezdeti munkamenet létrehozása a Service Bus nélkül, hogy a kapcsolat létrehozása.

Ha a kapcsolat és a munkamenet létrejött, csatolása mutató hivatkozásokat a *$cbs* csomópontot, és elküldi a *put-token* kérése az egyetlen engedélyezett műveletek. Sikeresen használják az érvényes tokent kell beállítani egy *put-token* kérelem néhány entitás csomóponton 20 másodperc után a kapcsolat létrejött, ellenkező esetben a kapcsolat egyoldalúan megszakad a Service Bus által.

Az ügyfél ezt követően feladata a jogkivonat lejárati szerinti nyomon követést. A Service Bus egy jogkivonat lejár, azonnal csökken minden kapcsolat az adott entitás-kapcsolaton. Akadályozni, hogy a probléma lépett fel, hogy az ügyfél is cserélje le a jogkivonatot a csomópont egy új virtuális keresztül bármikor *$cbs* ugyanazzal a felügyeleti csomópont *put-token* kézmozdulatokkal és nélkül első a a hasznos módja a folyamatok különböző kapcsolatokon forgalmat.

### <a name="send-via-functionality"></a>Send via funkció

[Send via / átviteli küldő](service-bus-transactions.md#transfers-and-send-via) , amely lehetővé teszi, hogy a szolgáltatási busz-továbbítás egy adott üzenetet egy célentitást keresztül egy másik entitás az a funkciók. Ez a szolgáltatás egy tranzakción belül entitások közötti műveletek végrehajtásához használható.

Ezzel a funkcióval, hozzon létre egy küldő és csatolást a `via-entity`. További információt közben a hivatkozás, létrehozására, az üzenetek/átvitelek erre a hivatkozásra a valódi cél átadott. A csatolás sikeres volt, ha erre a hivatkozásra küldött összes üzenet automatikusan továbbítja a *célentitás* keresztül *keresztül entitás*. 

> Megjegyzés: A hitelesítés van az is végre kell hajtani *keresztül entitás* és *célentitás* a kapcsolat kialakítása előtt.

| Ügyfél | | Service Bus |
| --- | --- | --- |
| Csatlakoztassa)<br/>név = {link name}.<br/>szerepkör küldő =<br/>forrás = {ügyfél hivatkozás azonosítója},<br/>cél =**{keresztül-entitás}**,<br/>**Tulajdonságok térkép = [(<br/>com.microsoft:transfer célcím =<br/>{célentitás})]** ) | ------> | |
| | <------ | Csatlakoztassa)<br/>név = {link name}.<br/>szerepkör, fogadó =<br/>forrás = {ügyfél hivatkozás azonosítója},<br/>cél = {keresztül-entitás},<br/>Tulajdonságok térkép [() =<br/>célcím com.Microsoft:Transfer =<br/>{Célentitás})] ) |

## <a name="next-steps"></a>További lépések

Az AMQP kapcsolatos további információkért látogasson el az alábbi hivatkozásokat:

* [Service Bus AMQP áttekintése]
* [A Service Bus AMQP 1.0 támogatása particionált üzenetsorok és témakörök]
* [A Windows Server a Service Bus AMQP]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[A Service Bus AMQP 1.0 támogatása particionált üzenetsorok és témakörök]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[A Windows Server a Service Bus AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
