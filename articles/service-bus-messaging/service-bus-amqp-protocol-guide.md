---
title: Az Azure Service Bus és az Event Hubs protokoll útmutatóban AMQP 1.0-s |} Microsoft Docs
description: Protokoll útmutató kifejezések és az Azure Service Bus és az Event Hubs AMQP 1.0 leírása
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
ms.date: 04/30/2018
ms.author: clemensv
ms.openlocfilehash: e124ea3f932a81634191785e7ee69c2492cb32fa
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 Azure Service Bus és az Event Hubs protokoll útmutató

A speciális üzenet-Várólistázást protokoll 1.0 egy olyan szabványosított keretezési és átviteli protokoll aszinkron módon biztonságosan és megbízhatóan a felek közötti üzenetek átvitelére. Az Azure Service Bus üzenetkezelés és az Azure Event Hubs elsődleges protokoll. Mindkét szolgáltatás is a HTTPS támogatására. Az egyéni SBMP protokollt is támogatja az hamarosan megszűnnek, AMQP helyett.

AMQP 1.0-s, amellyel elindította együtt köztes szállítók, például a Microsoft és a Red Hat több felhasználós üzenetkezelési köztes például a pénzügyi szolgáltatásokat iparági képviselő JP Morgan Chase széleskörű iparági együttműködés eredménye. Az AMQP protokoll és a bővítmény paramétereknek műszaki szabványosítás fóruma OASIS, és elért formális jóváhagyási, ISO/IEC 19494 nemzetközi szabvány szerint.

## <a name="goals"></a>Célok

Ez a cikk röviden összefoglalja az AMQP 1.0 együtt a bővítmény specifikációt, amely a OASIS AMQP műszaki Bizottság jelenleg véglegesített egy kis készletét specification üzenetküldési alapfogalmakat, és elmagyarázza, az Azure Service Bus valósítja meg, és ezek a specifikációk épül.

Bármelyik fejlesztő segítségével bármely létező AMQP 1.0-ügyfél verem bármilyen platformon kell képesek legyenek kommunikálni az Azure Service Bus keresztül AMQP 1.0 célja.

Általános célú AMQP 1.0 csomagokat, például az Apache Proton vagy AMQP.NET Lite már összes core AMQP 1.0-s protokollt valósítja meg. E eligazodást kézmozdulatok néha csomagolt be egy magasabb szintű API-t; Apache Proton két, a imperatív Messenger API és a reaktív reaktor API is kínál.

A következő ismertető feltételezzük, hogy a felügyeleti kapcsolatokhoz, a munkamenetek, és a hivatkozások AMQP és keret átvitel és az adatfolyam vezérlés kezelése a megfelelő verem (például az Apache Proton-C) kezeli, és nem igényel sok, ha van ilyen különös figyelmet az alkalmazásfejlesztők. Absztraktként feltételezzük, hogy létezik-e néhány API primitívek hasonlóan lehetővé teszi a, és hozzon létre valamilyen *küldő* és *fogadó* absztrakciós objektumokat, amely ezután rendelkezik néhány alakját `send()` és `receive()` műveleteket, illetve.

Üzenet megkeresésével, vagy a felügyeleti munkamenetek, például az Azure Service Bus fejlett funkcióinak ismertetésekor ezekről a szolgáltatásokról magyarázatát AMQP feltételeket, de is, a réteges látszólagosan megvalósítása a feltételezett API absztrakciós fölött.

## <a name="what-is-amqp"></a>Mi az az AMQP?

AMQP egy olyan keretezési és átviteli protokoll. A keretezési azt jelenti, hogy a struktúra biztosítja, hogy a hálózati kapcsolat mindkét irányban bináris adatok adatfolyamok. A struktúra biztosítja a különböző blokk ismert körülhatárolásához *keretek*, hogy a csatlakoztatott felek között. Az átviteli lehetőségeket győződjön meg arról, hogy a kommunikáló felek létesíthet egy megosztott ismertetése, amikor keretek át kell, és ha átvitelek teljes tekintendő.

Lejárt vázlat korábbi verzióktól eltérően az AMQP-munkacsoportja által előállított, amelyek még a néhány üzenet brókerek használják a munkacsoport-végleges, és a szabványos AMQP 1.0 protokoll nem írja elő egy üzenet broker vagy bármely adott topológia egy üzenet broker belül entitások.

A protokoll, amely támogatja a várólisták és a közzétételi/előfizetési entitások, mint Azure Service Bus message brókerek való együttműködéshez szükséges szimmetrikus társ-társ kommunikációhoz használható. Azt is alkalmas üzenetkezelési infrastruktúra való együttműködéshez ahol interakció mintázatokat eltérnek rendszeres várólisták, mint az Azure Event Hubs esetében. Az Eseményközpontok az üzenetsorokhoz hasonlóan úgy működik, ha az események küldhetők az, de úgy viselkedik, több mint egy soros társzolgáltatás események olvasása, a következőhöz hasonló szalagos meghajtó némileg. Az ügyfél eltolással szerzi be a rendelkezésre álló adatfolyamban, és majd kiszolgált eltolásánál kezdődő, hogy a legújabb elérhető összes eseményt.

Az AMQP 1.0 protokoll kialakításánál fogva bővíthető további javítása érdekében a képességei specifikációk engedélyezése. A jelen dokumentumban tárgyalt három bővítmény specifikációk ezt mutatják be. Kommunikáció a meglévő HTTPS/websocket elemek infrastruktúrára, ahol a natív AMQP TCP-portok konfigurálása nehéz lehet a kötés specifikációval határozza meg a réteg AMQP keresztül websocket elemek módját. Az üzenetkezelési infrastruktúra felügyelet céljából, vagy speciális funkciókat biztosítanak egy kérelem-válasz módon való kommunikáció, az AMQP-management specifikáció határozza meg a szükséges alapvető interakció primitívek. Az összevont engedélyezési modell integráció az AMQP jogcím-alapú biztonsági specifikáció határozza meg a rendelni, majd újítsa meg a hitelesítési tokenek hivatkozások társított módját.

## <a name="basic-amqp-scenarios"></a>Alapszintű AMQP-forgatókönyvek

Ez a szakasz ismerteti az AMQP 1.0-s, Azure Service Bus, beleértve a kapcsolatok, munkamenetek és hivatkozások létrehozása és átvitele üzeneteket a Service Bus-entitások, például várólisták, témakörök és előfizetések alapvető használatát.

A mérvadó forrás AMQP működésével kapcsolatos további az AMQP 1.0 megadását, de a meghatározás írt pontosan útmutató végrehajtása, és nem mutatja meg a protokoll. Ez a szakasz összpontosít mértékű terminológia keresztül mutatja, hogyan használja a Service Bus az AMQP 1.0 igény szerint bemutatása. Az AMQP egy átfogóbb bemutatása, valamint egy szélesebb körű leírását az AMQP 1.0-s, tekintse át [ezt a videót megoldást][this video course].

### <a name="connections-and-sessions"></a>Kapcsolatok száma és a munkamenetek

AMQP meghívja a kommunikáló programok *tárolók*; ezek tartalmazzák *csomópontok*, ezek a kommunikáló entitások belsejében lévő összes blobhoz. A várólista ilyen egy csomópont lehet. AMQP lehetővé teszi, hogy multiplexáló, így egyetlen kapcsolatból használható sok kommunikációs útvonala; csomópontok között például egy alkalmazás ügyfél lehet egyidejűleg fogadása egy üzenetsorból és az azonos hálózati kapcsolaton kellene küldenie egy másik várólistához.

![][1]

A hálózati kapcsolat így rögzített a tárolóra. Ez a tároló az ügyfél szerepkörre, így egy kimenő TCP szoftvercsatorna-kapcsolat tárolót a fogadó szerepkörben, amelyet figyeli, és elfogadja a bejövő TCP-kapcsolatok által kezdeményezett. A kapcsolati kézfogás tartalmaz egyeztetése deklaráló vagy a Transport Level Security (TLS/SSL), és egy hitelesítési/engedélyezési kézfogás a kapcsolat hatókörben SASL alapuló egyeztetése protokoll verziója.

Az Azure Service Bus mindig TLS használatát igényli. Támogatja a kapcsolatokat keresztül TCP-port: 5671, amely lehetővé teszi, az TCP-kapcsolat van először átfedett a TLS a AMQP protokoll kézfogás megadása előtt, és is támogatja kapcsolatok 5672 TCP-porton keresztül, amelynek során a kiszolgáló azonnal nyújt egy kötelező frissítés kapcsolat a TLS AMQP-előírt modellt használja. Az AMQP websocket elemek kötés alagutat hoz létre TCP 443-as portot, amely majd AMQP 5671 kapcsolatok egyenértékű keresztül.

A kapcsolat és a TLS beállítása után a Service Bus két SASL mechanizmus lehetőségeket tesz lehetővé:

* SASL egyszerű felhasználónév és jelszó hitelesítő adatok átadni egy kiszolgálót gyakran használják. A Service Bus nincs partnerekkel, de elnevezett [megosztott hozzáférési szabályok](service-bus-sas.md), amely jogosultsággal rendelkezik, és társított kulcs. A szabály neve lesz a felhasználónév és a kulcs (a base64 kódolású szöveget) a jelszó szolgál. A kiválasztott szabály kapcsolódó jogosultságok szabályozására, a kapcsolat engedélyezett műveletek.
* NÉVTELEN SASL használható SASL engedélyezési megkerülése, ha az ügyfél a jogcím-alapú biztonsági (CBS) modell, amely a későbbiekben olvashat használni szeretné. Ezzel a beállítással egy ügyfél kapcsolatot lehetne létesíteni névtelenül rövid időre során, ami az ügyfél és a CBS-től végpont csak kommunikációját, és a CBS-kézfogás kell végrehajtania.

A szállítási kapcsolat létrejötte után minden egyes tárolók deklarálja a maximális keret méretét, azok szeretne kezelni, és üresjárati időkorlátot után lesz egyoldalúan megszakad nincs tevékenység esetén a kapcsolat.

Ezek deklarálhatják hány egyidejű csatornák használata támogatott. A csatorna egy olyan fölött a kapcsolat egy egyirányú, kimenő, virtuális átviteli elérési utat. A munkamenet egy csatornát a összekapcsolt tárolók és a kétirányú kommunikáció elérési út minden vesz igénybe.

Munkamenetek rendelkezik egy Windows-alapú vezérlő folyamatmodell; egy munkamenet létrejöttekor felek deklarálja hány keretek hajlandó azokat a fogadási ablak. A felek exchange keretek átvitt keretek kitöltés ablakot, és az átvitel leállítása, ha az ablak megtelik, és mindaddig, amíg az ablak lekérdezi alaphelyzetbe állítása vagy a kiterjesztett használatával, mint a *performative folyamata* (*performative* van az AMQP elnevezése a két fél között továbbított protocol szintű kézmozdulatok).

A Windows-alapú modell nagyjából megfelel a Windows-alapú átvitelvezérlés, de a munkamenet szintű belül a szoftvercsatorna TCP fogalmát. A protokoll lehetővé teszi a több egyidejű munkamenetek fogalmát, hogy a magas prioritású virtuális gép forgalom sikerült kell rushed túli szabályozottan halmozott normál forgalom, például a highway expressz lane létezik.

Az Azure Service Bus pontosan egy munkamenet jelenleg használ minden egyes kapcsolathoz. A Service Bus maximális keret-mérete a Service Bus szabványos és az Event Hubs 262 144 bájt (256 KB). 1 048 576 (1 MB), a Service Bus prémium. A Service Bus bármely adott munkamenet szintű szabályozási windows nem ír elő, de hivatkozás szintű átvitelvezérlés részeként rendszeresen alaphelyzetbe állítja a az ablak (lásd: [a következő szakaszban](#links)).

Kapcsolatok száma, a csatornák és a munkamenetek nincs rövid élettartamú. Ha az alapul szolgáló kapcsolatot összecsukó, kapcsolatok, TLS-alagútjának SASL engedélyezési környezetet és munkamenetek kell hozni.

### <a name="links"></a>Hivatkozások

AMQP-kapcsolaton keresztül üzenetek átvitelére. Egy hivatkozást, amely lehetővé teszi egy irányban; átadó üzenetek munkameneten keresztül létrehozott kommunikációs elérési úttal az adatátviteli állapot egyeztetés a hivatkozást és a csatlakoztatott felek közötti kétirányú felett van.

![][2]

Hivatkozások hozhat létre vagy tároló bármikor, valamint egy meglévő munkamenetben, így az AMQP eltér sok más protokoll, beleértve a HTTP és a MQTT, egy kizárólagos jogosultsággal a fél létrehozása esetén az átvitel és az átvitel elérési kezdeményezése a szoftvercsatorna-kapcsolat.

A hivatkozás kezdeményezése tároló kéri a ellentétes tárolót, hogy fogadja el a hivatkozást, és azt úgy dönt, hogy egy szerepkör vagy a feladó, vagy a címzett. Ezért tároló is kezdeményezhető létrehozása egyirányú vagy kétirányú kommunikációs útvonala, ez utóbbi hivatkozások értékpár modellezve.

Hivatkozások vannak nevű társított és csomópontok. A kezdő leírtaknak csomópontja a kommunikáló entitások belül egy tárolót.

A Service Bus egy csomópont megegyezik közvetlenül egy várólista, a témakör, előfizetés vagy a kézbesítetlen levelek várakozási sor vagy előfizetés. A csomópont neve szerepel az AMQP ezért belül a Service Bus-névtér az entitás relatív neve. Ha a várólista neve **Várólista_neve**, ez is a AMQP csomópont neve. Üzenettémakör-előfizetésben követi a HTTP API konvenció szerint rendezve alatt álló "előfizetések" erőforrás-gyűjteményt, és így előfizetés **sub** vagy egy téma **mytopic** AMQP csomópont neve  **mytopic/előfizetések/sub**.

A kapcsolódó ügyfél is egy helyi csomópontnév hivatkozásokra, létrehozásához használatához szükséges A Service Bus nincs csomópont neveket kapcsolatos előírásoknak megfelelő, és azok nem értelmezhetők. AMQP 1.0-ügyfél verem általában azt használja a rendszer ahhoz, hogy biztosítsa a rövid élettartamú csomópont nevek szerepelnek egyedi hatókörében az ügyfél.

### <a name="transfers"></a>Átvitel

Hivatkozás létrehozása után üzenetek átvihetők adott hivatkozáson keresztül. AMQP, egy átviteli egy explicit protokoll hitelesítési módok végre (az *átviteli* performative), amely egy üzenetet helyezi a feladótól fogadó kapcsolaton keresztül. Egy átviteli kész, amikor az "rendezik", ami azt jelenti, hogy a két fél rendelkezik-e létre a megosztott ismeretének átvitel eredményeit.

![][3]

A legegyszerűbb esetben a küldő szeretné, ami azt jelenti, hogy az ügyfél nem eredménye iránt érdeklődik, és a fogadó nem biztosít olyan visszajelzést, a művelet eredményéről "előre rendezni," üzeneteket küldeni. Ebben a módban az AMQP protokoll szintjén Service Bus által támogatott, de nincs felfedve, sem az ügyfél API-k.

A normál esetben, hogy üzenetek küldése határidőig, és a fogadó megjeleníti elfogadása vagy elutasítása használatával a *törlése* performative. Elutasítása esetén a fogadó bármilyen okból nem tudja elfogadni az üzenetet, és az elutasítás üzenet a OK, amely egy AMQP által definiált hiba struktúra információt tartalmaz. Üzenetek visszautasítja a Service Bus belül belső hiba miatt, ha a szolgáltatás további információ a támogatási személyzetnek, ha támogatási kérelmek biztosítani a diagnosztika mutatók használható struktúra belül adja vissza. Később megismerheti hibákkal kapcsolatos további részletekért.

Egy speciális formája, amelyet elutasítási van a *kiadott* állapotát, amely azt jelzi, hogy a fogadó nem műszaki kifogást átvitelét, de is rendezni az átvitel nem iránt. Adott esetben létezik, például amikor egy üzenetet a rendszer egy Service Bus-ügyfélalkalmazást, és az ügyfél úgy dönt, hogy a "abandon" az üzenet mert nem tudja végrehajtani a munka az üzenet; az üzenet kézbesítése maga nincs hibás. Egy adott állapotban változata van a *módosított* állapotát, amely lehetővé teszi, hogy az üzenet módosítása, megjelent. Az állapotban nem jelenleg a Service Bus használják.

Az AMQP 1.0-specifikáció határozza meg a további törlése állapot nevű *kapott*, amely kifejezetten hivatkozás helyreállítás kezeléséhez nyújt segítséget. Hivatkozás helyreállítási lehetővé teszi, hogy újbóli megállapításának módjaival egy hivatkozást, és minden függőben lévő fölött egy új kapcsolat és a munkamenet, ha az előző kapcsolat és a munkamenet elvesztek kézbesítések állapotát.

A Service Bus nem támogatja a helyreállítási hivatkozásra; Ha egy határidőig üzenettel az ügyfél elveszti a Service Bus a kapcsolat át függőben lévő, üzenet átvitel elvész, és az ügyfél kell újracsatlakozni, újra létrehozni a hivatkozást, és próbálja megismételni az átvitel.

A Service Bus és az Event Hubs támogatja a "legalább egyszeri" átvitelek, amikor a küldő tárolja, és amelyek fogadták el az üzenet biztos lehet benne, de nem támogatja "pontosan egyszeri" átvitelek AMQP szinten, ahol a rendszer kísérletet a hivatkozás helyreállításához tegye és továbbra is egyezteti az üzenetátvitel elkerülése kézbesítési állapotát.

Kiegyensúlyozása érdekében lehetséges duplikált küld, a Service Bus által támogatott egyik opcionális szolgáltatása, kettős észlelés üzenetsorok és témakörök. Kettős észlelés a bejövő üzenetek üzenet azonosítók rögzíti a felhasználó által megadott időszak alatt, majd csendes elutasítja azokat az adott azonos időszakban ugyanazon üzenet-azonosítókkal küldött összes üzenet.

### <a name="flow-control"></a>Adatfolyam vezérlés

A munkamenet szintű vezérlő folyamatmodell, amely a korábban tárgyalt, felül minden hivatkozás rendelkeznek a saját folyamatmodell vezérlő. Kapcsolati szintű átvitelvezérlés nem kell kezelni, túl sok keretek után hivatkozás szintű átvitelvezérlés hivatkozás kezelni kíván, hogy hány üzenet feladata az alkalmazás helyezi, és ha a tároló védi.

![][4]

Hivatkozásra, átvitelek csak akkor szükséges, ha a küldő van elegendő *jóváírás hivatkozás*. Hivatkozás jóváírás egy számláló, a fogadó használatával állítja be a *folyamata* performative, amelynek hatóköre a következő hivatkozást. A küldő hivatkozás jóváírás hozzárendelése esetén megkísérli üzenetek kézbesítése által, hogy a jóváírási felhasználásra. Minden üzenet kézbesítési csökkenti a fennmaradó hivatkozást követel 1. A hivatkozás jóváírás használatakor kézbesítések leállítása.

A fogadó szerepkör Service Bus esetén azonnal biztosít a küldő bőséges hivatkozás kreditet, hogy az üzenetek is küldhetők el azonnal. Hivatkozás jóváírás használt, a Service Bus alkalmanként küld egy *folyamata* performative a feladó a hivatkozás kreditegyenlege frissíteni.

A küldő szerepkörben a Service Bus való felhasználásra a függőben lévő hivatkozás kreditegyenlege üzeneteket küld.

Az API-szintű "kap" hívás fordítja le a *folyamata* performative küldi el a Service Bus által az ügyfél és a Service Bus-t használ fel, hogy a jóváírási alapul véve az első elérhető, nem zárolt üzenetet az üzenetsorból, zárolással, és áthelyezte azt. Nincs azonnal elérhetők legyenek kézbesítésre hibaüzenet, ha a kapcsolat nyitott kreditegyenlege létrehozni, hogy adott entitás rögzített érkezési sorrendben marad, és az üzenetek zárolva van és elérhető, a fennmaradó kreditegyenlege a használandó átvitele.

Az üzenetben zárolás úgy átvitelét a Terminálszolgáltatások állapotokba kiegyenlítését *elfogadott*, *elutasított*, vagy *kiadott*. Az üzenet van Service Bus eltávolítva, ha a Terminálszolgáltatások állapota *elfogadott*. A Service Bus marad és a rendszer a következő fogadóra, ha az átvitel eléri a más állapotok valamelyikében. A Service Bus automatikusan áthelyezi az üzenetet az entitás kézbesítetlen levelek várólistájára, amikor eléri a maximális száma miatt ismételt elutasítások vagy kiadásokban az entitás engedélyezett.

Annak ellenére, hogy a Service Bus API-k közvetlenül nem fedi fel az ilyen lehetőség ma, alacsonyabb szintű AMQP protokoll az ügyfél használhatja a hivatkozás-jóváírási modell kapcsolja be a "pull-style stílust" interakció minden fogadási kérést kreditet egy egységének kibocsátó be a "leküldéses-style stílust" modell a nagy számú kreditek hivatkozásra, és majd üzeneteket fogadni, amint azok elérhetővé további beavatkozás nélkül válnak. Leküldéses támogatott keresztül a [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PrefetchCount) vagy [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount) tulajdonságbeállítások. Ha nullától eltérő, az AMQP ügyfél használja, a hivatkozás jóváírás.

Ebben a környezetben fontos tudni, hogy az óra, az üzenetben az entitás belül a zárolás lejáratának kezdődik, amikor az üzenet forrása az entitás nem az üzenet az üzembe helyezés a keresztülhaladnak a hálózaton. Amikor az ügyfél azt jelzi, hogy készültségi hivatkozás jóváírás kiállításával üzeneteket fogadni, ezért várt üzenetek aktívan kell húzza a hálózaton keresztül, és készen áll arra, kezelni azokat. Az üzenet zár ellenkező esetben lejártak, mielőtt a rendszer még akkor is kézbesíteni az üzenetet. A hivatkozás-jóváírási adatfolyam vezérlés használatát közvetlenül tükröznie kell azonnali készültségét érhető el a címzett részére küldött üzenetek kezelésére.

Összefoglalva az alábbi szakaszokban a performative folyamat vázlatos áttekintése különböző API interakció során. Az egyes szakaszokon egy másik logikai működését ismerteti. Lehet, hogy egy adott kapcsolati "Lusta," tehát, hogy azok csak hajtható végre, ha szükséges. Egy üzenet küldője létrehozása nem okozhat a hálózati kommunikáció csak az első üzenet küldése vagy a kért.

A következő táblázat a nyilak a performative folyamat iránya.

#### <a name="create-message-receiver"></a>Üzenet fogadó létrehozása

| Ügyfél | Service Bus |
| --- | --- |
| --> () csatolása<br/>Name = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**fogadó**,<br/>forrás = {egyednév}<br/>cél = {Ügyfélazonosító hivatkozás}<br/>) |Entitás fogadni csatol ügyfél |
| A Service Bus válaszokat a hivatkozás végét csatolása |<--(csatolása<br/>Name = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**küldő**,<br/>forrás = {egyednév}<br/>cél = {Ügyfélazonosító hivatkozás}<br/>) |

#### <a name="create-message-sender"></a>Üzenet küldőjének létrehozása

| Ügyfél | Service Bus |
| --- | --- |
| --> () csatolása<br/>Name = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**küldő**,<br/>forrás = {Ügyfélazonosító hivatkozás}<br/>cél = {entitás neve}<br/>) |Nincs művelet |
| Nincs művelet |<--(csatolása<br/>Name = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**fogadó**,<br/>forrás = {Ügyfélazonosító hivatkozás}<br/>cél = {entitás neve}<br/>) |

#### <a name="create-message-sender-error"></a>Hozzon létre az üzenet feladójának (hiba)

| Ügyfél | Service Bus |
| --- | --- |
| --> () csatolása<br/>Name = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**küldő**,<br/>forrás = {Ügyfélazonosító hivatkozás}<br/>cél = {entitás neve}<br/>) |Nincs művelet |
| Nincs művelet |<--(csatolása<br/>Name = {link name}.<br/>kezelni = {numerikus leíró},<br/>szerepkör =**fogadó**,<br/>forrás = null,<br/>cél = null<br/>)<br/><br/><--leválasztani ()<br/>kezelni = {numerikus leíró},<br/>lezárt =**igaz**,<br/>Hiba = {hibaadatainak}<br/>) |

#### <a name="close-message-receiversender"></a>Bezárási üzenetet fogadó/feladó

| Ügyfél | Service Bus |
| --- | --- |
| --> () leválasztása<br/>kezelni = {numerikus leíró},<br/>lezárt =**igaz**<br/>) |Nincs művelet |
| Nincs művelet |<--leválasztani ()<br/>kezelni = {numerikus leíró},<br/>lezárt =**igaz**<br/>) |

#### <a name="send-success"></a>Küldési (sikeres)

| Ügyfél | Service Bus |
| --- | --- |
| --> átvitel)<br/>kézbesítési-id = {numerikus leíró},<br/>kézbesítési-címke = {bináris leíró},<br/>rendezni =**hamis**,, több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**hamis**<br/>) |Nincs művelet |
| Nincs művelet |<--törlése ()<br/>szerepkör = fogadó,<br/>első = {kézbesítési azonosító}<br/>utolsó = {kézbesítési azonosító}<br/>rendezni =**igaz**,<br/>állapot =**elfogadva**<br/>) |

#### <a name="send-error"></a>Küldési (hiba)

| Ügyfél | Service Bus |
| --- | --- |
| --> átvitel)<br/>kézbesítési-id = {numerikus leíró},<br/>kézbesítési-címke = {bináris leíró},<br/>rendezni =**hamis**,, több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**hamis**<br/>) |Nincs művelet |
| Nincs művelet |<--törlése ()<br/>szerepkör = fogadó,<br/>első = {kézbesítési azonosító}<br/>utolsó = {kézbesítési azonosító}<br/>rendezni =**igaz**,<br/>állapot =**elutasított**()<br/>Hiba = {hibaadatainak}<br/>)<br/>) |

#### <a name="receive"></a>Fogadás

| Ügyfél | Service Bus |
| --- | --- |
| --> folyamata)<br/>hivatkozás-jóváírási = 1<br/>) |Nincs művelet |
| Nincs művelet |< transfer ()<br/>kézbesítési-id = {numerikus leíró},<br/>kézbesítési-címke = {bináris leíró},<br/>rendezni =**hamis**,<br/>több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**hamis**<br/>) |
| --> törlése)<br/>szerepkör =**fogadó**,<br/>első = {kézbesítési azonosító}<br/>utolsó = {kézbesítési azonosító}<br/>rendezni =**igaz**,<br/>állapot =**elfogadva**<br/>) |Nincs művelet |

#### <a name="multi-message-receive"></a>Több üzenet fogadása

| Ügyfél | Service Bus |
| --- | --- |
| --> folyamata)<br/>hivatkozás-jóváírási = 3<br/>) |Nincs művelet |
| Nincs művelet |< transfer ()<br/>kézbesítési-id = {numerikus leíró},<br/>kézbesítési-címke = {bináris leíró},<br/>rendezni =**hamis**,<br/>több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**hamis**<br/>) |
| Nincs művelet |< transfer ()<br/>kézbesítési-id = {numerikus leíró + 1},<br/>kézbesítési-címke = {bináris leíró},<br/>rendezni =**hamis**,<br/>több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**hamis**<br/>) |
| Nincs művelet |< transfer ()<br/>kézbesítési-id = {numerikus leíró + 2},<br/>kézbesítési-címke = {bináris leíró},<br/>rendezni =**hamis**,<br/>több =**hamis**,<br/>állapot =**null**,<br/>folytatása =**hamis**<br/>) |
| --> törlése)<br/>szerepkör = fogadó,<br/>első = {kézbesítési azonosító}<br/>utolsó = {kézbesítési azonosító + 2},<br/>rendezni =**igaz**,<br/>állapot =**elfogadva**<br/>) |Nincs művelet |

### <a name="messages"></a>Üzenetek

Az alábbi szakaszok ismertetik a Service Bus által használt tulajdonságok a szabványos AMQP üzenet szakaszok, és hogyan leképezik a Service Bus API-készlethez.

Bármely tulajdonságot, amelyet a alkalmazás meghatározása AMQP meg kell rendelni `application-properties` leképezés.

#### <a name="header"></a>header

| Mező neve | Használat | API neve |
| --- | --- | --- |
| tartós |- |- |
| prioritás |- |- |
| TTL |Az üzenet élettartama |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) |
| első-beszerző |- |- |
| kézbesítési-száma |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) |

#### <a name="properties"></a>properties

| Mező neve | Használat | API neve |
| --- | --- | --- |
| üzenetazonosító |Alkalmazás által meghatározott, szabad formátumú azonosító az üzenethez. Használja az ismétlődő észlelésére. |[messageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) |
| felhasználói azonosító |Alkalmazás által meghatározott felhasználói azonosítóját, a Service Bus által nem értelmezhető. |Nem érhető el a Service Bus API-n keresztül. |
| erre: |Alkalmazás által meghatározott cél azonosítója, a Service Bus által nem értelmezhető. |[Címzett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_To) |
| Tulajdonos |Alkalmazás által meghatározott célú üzenetazonosító, Service Bus által nem értelmezhető. |[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) |
| Válaszcím |Alkalmazás által meghatározott válasz-elérési út mutató, Service Bus által nem értelmezhető. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyTo) |
| korrelációs azonosító |Alkalmazás által meghatározott korrelációs azonosító, a Service Bus által nem értelmezhető. |[correlationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_CorrelationId) |
| tartalomtípus |Alkalmazás által meghatározott tartalomtípus mutató törzséhez, Service Bus által nem értelmezhető. |[A ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType) |
| tartalom kódolása |Alkalmazás által meghatározott tartalom kódolása mutató törzséhez, Service Bus által nem értelmezhető. |Nem érhető el a Service Bus API-n keresztül. |
| abszolút-lejárati-időpont |Deklarálja, mely abszolút azonnali üzenet lejár. A bemeneti (TTL követi fejlécet), figyelmen kívül hagyja a kimenetet mérvadó. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ExpiresAtUtc) |
| Létrehozás-időpontja |Deklarálja ekkor az üzenet jött létre. Nem használja a Service Bus |Nem érhető el a Service Bus API-n keresztül. |
| csoport-azonosítója |Az üzenetek kapcsolódó készletének, alkalmazásszinten megadott azonosító. A Service Bus-munkamenetek használt. |[Munkamenet-azonosító](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) |
| csoport-sorozat |A számláló azonosítására az üzenet egy munkamenet belül relatív sorszámát. A Service Bus figyelmen kívül hagyja. |Nem érhető el a Service Bus API-n keresztül. |
| válasz a csoport azonosítója |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyToSessionId) |

#### <a name="message-annotations"></a>Üzenet jegyzetek

Néhány más service bus message tulajdonság nem AMQP üzenettulajdonságok részét képezik, és mint továbbítódnak `MessageAnnotations` az üzenetben.

| A jegyzet ábrázolási kulcs | Használat | API neve |
| --- | --- | --- |
| x-opt-ütemezett-sorba helyezni-idő | Deklarálja időtartamban az üzenet meg kell jelennie az entitás |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partíció-kulcs | Alkalmazás által meghatározott, határozzák meg, hogy mely partíciós kulcs az üzenet kell megnyílik a. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-keresztül-partíció-kulcs | Alkalmazás által meghatározott partíciókulcs érték, amikor egy tranzakció keresztül átviteli üzenetek küldéséhez használt. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-várólistán lévő-idő | Szolgáltatás által definiált UTC idő képviselő enqueuing az üzenet tényleges ideje. Bemeneti figyelmen kívül hagyható. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt--sorszáma | Szolgáltatás által definiált egyedi száma üzenetet. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x opt eltolása | Az üzenet a várólistában levő szolgáltatás által definiált sorszámát. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-részt vesz-zárolva-ig | Szolgáltatás által definiált. A dátum és idő, ameddig az az üzenet zárolva lesz a várólista/előfizetést. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-kézbesítetlen levelek-forrás | Szolgáltatás által definiált. Ha az üzenet jelenik meg a halott levél várósor, az eredeti üzenet forrása. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Tranzakció funkció

Egy tranzakció két vagy több műveletek együtt egy végrehajtási hatókör felsorolását tartalmazza. Természetüknél ilyen tranzakció győződjön meg arról, hogy a műveletek adott csoporthoz tartozó összes művelet sikeres vagy sikertelen lehet közösen.
A műveletek azonosító szerint vannak csoportosítva `txn-id`.

Tranzakciós interakció, az ügyfél működik egy `transaction controller` amely meghatározza, hogy a műveletek a csoportosított lehet. Service Bus szolgáltatás úgy működik, mint egy `transactional resource` és végez a munkát, amint azt a `transaction controller`.

Az ügyfél és a szolgáltatás protokollt használó kommunikációra egy `control link` az ügyfél létrehozott. A `declare` és `discharge` üzeneteket foglal le, majd fejezze be a tranzakciók rendre vezérlő-kapcsolaton keresztül küldik a tartományvezérlő (azok nem felelnek meg a tranzakciós munkahelyi határait). A tényleges Küldés/fogadás nem történik meg a hivatkozáson keresztül. Minden kért tranzakciós művelet explicit módon van, egy a kívánt `txn-id` , és ezért előfordulhat, hogy a kapcsolatra vonatkozó bármelyik hivatkozása. Ha a vezérlő kapcsolat le van zárva, azt nem környezetébe tranzakciók léteznek, majd az összes ilyen van azonnal tranzakciók visszaállítása, és hiba tapasztalható, további tranzakciós munkájuk elvégzéséhez kísérletek irányítja. A vezérlő hivatkozásra üzenetek nem lehet rendezni előtti.

Minden kapcsolat kezdeményezése a saját kell kezdődnie és végződnie tranzakciók vezérlő hivatkozás van. A szolgáltatás határozza meg, mely különleges célja egy `coordinator`. Az ügyfél/tartományvezérlő a cél vezérlő kapcsolatot hoz létre. Vezérlő hivatkozás kívüli az entitás, azaz, egyazon vezérlő kapcsolat kezdeményezéséhez és lezárására több entitások tranzakciói használható.

#### <a name="starting-a-transaction"></a>A tranzakció elindítása

Tranzakciós munka megkezdéséhez. a vezérlő be kell szereznie egy `txn-id` a koordinátortól. Ennek érdekében küld egy `declare` típusú üzenetet. Ha a deklaráció sikeres, a koordinátor válaszol, a témakör eredményét `declared` amely hordoz magában, ha a hozzárendelt `txn-id`.

| Ügyfél (vezérlő) | | A Service Bus (koordinátor) |
| --- | --- | --- |
| (csatolása<br/>Name = {link name}.<br/>... ,<br/>szerepkör =**küldő**,<br/>cél =**koordinátora**<br/>) | ------> |  |
|  | <------ | (csatolása<br/>Name = {link name}.<br/>... ,<br/>TARGET=Coordinator()<br/>) |
| átvitel)<br/>kézbesítési-id = 0,...)<br/>{AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | törlése) <br/> első = 0, 0, utolsó = <br/>állapot =**Declared**()<br/>**túlhasználat-id**= {tranzakcióazonosító}<br/>))|

#### <a name="discharging-a-transaction"></a>Egy tranzakció lezáró

A tartományvezérlő a tranzakciós munka kipróbálásában küldésével egy `discharge` a koordinátor üzenetet. A vezérlő azt jelzi, hogy kíván véglegesítési vagy a visszaállítási a tranzakciós munkahelyi úgy, hogy a `fail` jelzőt a kibocsátás törzsében. Ha a koordinátor nem tudja végrehajtani a kibocsátás, az üzenet a eredménye végrehajtsa lesz elutasítva a `transaction-error`.

> Megjegyzés: sikertelen = true hivatkozik egy tranzakció, és sikertelen visszaállításának = false véglegesítési hivatkozik.

| Ügyfél (vezérlő) | | A Service Bus (koordinátor) |
| --- | --- | --- |
| átvitel)<br/>kézbesítési-id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | törlése) <br/> első = 0, 0, utolsó = <br/>állapot deklarált () =<br/>túlhasználat-id = {tranzakcióazonosító}<br/>))|
| | . . . <br/>Tranzakciós munka<br/>az egyéb hivatkozások<br/> . . . |
| átvitel)<br/>kézbesítési-id = 57,...)<br/>{AmqpValue)<br/>**Lezárására (túlhasználat-id = 0,<br/>sikertelen = false)**)}| ------> |  |
| | <------ | törlése) <br/> első = 57, utolsó = 57, <br/>állapot =**az Accepted() művelet**)|

#### <a name="sending-a-message-in-a-transaction"></a>Üzenet küldése egy tranzakcióban

Minden tranzakciós munkát tranzakciós kézbesítési állapotával `transactional-state` , amely hordoz magában, ha a tranzakciók-azonosító. Üzenetek küldése, ha a tranzakciós állapot végzi az üzenet átviteli keret. 

| Ügyfél (vezérlő) | | A Service Bus (koordinátor) |
| --- | --- | --- |
| átvitel)<br/>kézbesítési-id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | törlése) <br/> első = 0, 0, utolsó = <br/>állapot deklarált () =<br/>túlhasználat-id = {tranzakcióazonosító}<br/>))|
| átvitel)<br/>kezelni = 1,<br/>kézbesítési-id = 1, <br/>**állapot =<br/>TransactionalState (<br/>túlhasználat-id = 0)**)<br/>{hasznos}| ------> |  |
| | <------ | törlése) <br/> első = 1, a legutóbbi = 1, <br/>állapot =**TransactionalState (<br/>túlhasználat-id = 0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Egy üzenet valamely tranzakcióban ártalmatlanítása

Üzenet törlése olyan műveletek, például a `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`. A tranzakción belül műveletek végrehajtására, adja át a `transactional-state` a rendezése.

| Ügyfél (vezérlő) | | A Service Bus (koordinátor) |
| --- | --- | --- |
| átvitel)<br/>kézbesítési-id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | törlése) <br/> első = 0, 0, utolsó = <br/>állapot deklarált () =<br/>túlhasználat-id = {tranzakcióazonosító}<br/>))|
| | <------ |átvitel)<br/>kezelni = 2,<br/>kézbesítési-id = 11, <br/>állapot = null)<br/>{hasznos}|  
| törlése) <br/> első = 11, utolsó = 11, <br/>állapot =**TransactionalState (<br/>túlhasználat-id = 0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Speciális Service Bus-képességek

Ez a fejezet speciális képességek az Azure Service Bus vázlat bővítmények számára az AMQP, jelenleg fejlesztés alatt áll a OASIS műszaki bizottság az AMQP alapuló. A Service Bus valósítja meg a Piszkozatok legújabb verzióját, és fogad el, ezek Piszkozatok elérni a normál üzenetterhelésen bevezetett változások.

> [!NOTE]
> Service Bus üzenetkezelés egy kérelem-válasz mintát keresztül speciális műveletek támogatottak. Ezek a műveletek részleteit a cikkben bemutatott [AMQP 1.0 a Service Bus: kérelem-válasz-alapú műveletek](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>AMQP kezelése

Az AMQP-management specifikáció az első, a cikkben szereplő vázlat Extensions. Ez az előírás az AMQP protokoll rétegre protokollok, amelyek lehetővé teszik az üzenetkezelési infrastruktúra felügyeleti interakciók AMQP alatt álló készletet határoz meg. A megadását, mint meghatározása általános műveletek *létrehozása*, *olvasási*, *frissítése*, és *törlése* entitások belüli kezelésére szolgáló egy üzenetkezelési infrastruktúra és a lekérdezési műveletek.

E kézmozdulatok egy kérelem-válasz közötti interakció az ügyfél és az üzenetkezelési infrastruktúra szükséges, és hogy a meghatározás ezért határozza meg a fölött AMQP interakció minta modellek hogyan: az ügyfél csatlakozik az üzenetkezelési infrastruktúra egy munkamenet kezdeményezi, és létrehozza a hivatkozások két. Egy hivatkozásra az ügyfél úgy működik, mint a küldő és a többi úgy működik, mint fogadó, így a két hivatkozásokat tartalmaz, amelyek működhet, és a kétirányú csatorna létrehozása.

| Logikai művelet | Ügyfél | Service Bus |
| --- | --- | --- |
| Kérelem-válasz elérési utat hoz létre |--> () csatolása<br/>name = {*hivatkozásnév*},<br/>kezelni = {*numerikus leíró*},<br/>szerepkör =**küldő**,<br/>forrás =**null**,<br/>target = "myentity / $kezelése"<br/>) |Nincs művelet |
| Kérelem-válasz elérési utat hoz létre |Nincs művelet |\<--(csatolása<br/>name = {*hivatkozásnév*},<br/>kezelni = {*numerikus leíró*},<br/>szerepkör =**fogadó**,<br/>forrás = null,<br/>target = "myentity"<br/>) |
| Kérelem-válasz elérési utat hoz létre |--> () csatolása<br/>name = {*hivatkozásnév*},<br/>kezelni = {*numerikus leíró*},<br/>szerepkör =**fogadó**,<br/>forrás = "myentity / $felügyelet",<br/>target = "myclient$ id"<br/>) | |
| Kérelem-válasz elérési utat hoz létre |Nincs művelet |\<--(csatolása<br/>name = {*hivatkozásnév*},<br/>kezelni = {*numerikus leíró*},<br/>szerepkör =**küldő**,<br/>forrás = "myentity"<br/>target = "myclient$ id"<br/>) |

Hivatkozások, hogy a két helyen, a kérelem/válasz végrehajtására, akkor az egyszerű: a vonatkozó kérés, mert az üzenetkezelési infrastruktúra, amely együttműködik a ebben a mintában belüli entitást egy levelet. A kérelemüzenet, az a *Válaszcím* mező mellett a *tulajdonságok* állítja a *cél* azonosítóját, a hivatkozás, amelyre, hogy a választ. A kezelési entitás feldolgozza a kérést, és kézbesíti a válasz-kapcsolaton keresztül amelynek *cél* azonosítója megegyezik a jelzett *Válaszcím* azonosítója.

A minta nyilvánvalóan megköveteli, hogy az ügyfél tároló és az ügyfél által generált azonosítójának a válasz cél legyenek egyedi összes ügyfélre, és a biztonsági okokból is nehezen becsülhető.

A felügyeleti protokoll és az egyéb protokollok, az ugyanilyen mintájú használó használt üzenetváltásokban fordulhat elő, az alkalmazás szintjén; Ezek új AMQP protokoll szintű kézmozdulatok nem határoznak meg. Ez szándékos, úgy, hogy az alkalmazások azonnali használhatják a megfelelő AMQP 1.0 verem bővítmény.

A Service Bus jelenleg valósítja meg a management specifikáció az alapvető szolgáltatások egyikét sem, de a kérelem/válasz minta felügyeleti specifikációja által definiált nem eligazodást, a jogcím-alapú biztonsági szolgáltatás és a speciális szinte minden Az alábbiakban ismertetett képességeit.

### <a name="claims-based-authorization"></a>Jogcímalapú engedélyezési

Az AMQP jogcímek alapú engedélyezési (CBS) specifikáció vázlat management specifikáció kérelem/válasz minta épül, és ismerteti, hogyan használható az összevont biztonsági jogkivonatokat az AMQP általánosított modellt.

A Bevezetés című témakörben írtaknak AMQP alapértelmezett biztonsági modelljét SASL alapul, és az AMQP kapcsolati kézfogás integrálható. SASL használatával azzal az előnnyel jár, amely egy bővíthető, amelynek mechanizmusok készlete minden protokoll, amely a SASL hivatalosan leans kihasználhassa a meghatározott modellt biztosít. Ezek a mechanizmusok között vannak "Egyszerű" átvitel felhasználónevek és jelszavak, a "Külső" kötődni a TLS-level security, a "Névtelen" explicit hitelesítési/engedélyezési és kiegészítő mechanizmusok, amelyek lehetővé teszik a sikeres széles választékában hiányában Express hitelesítés és/vagy engedélyezési hitelesítő adatok és a jogkivonatok.

Az AMQP tartozó SASL integráció két hátrányai is tartalmaz:

* A kapcsolat hitelesítő adatait, és a tokeneket hatóköre. Üzenetkezelési infrastruktúra is szeretne biztosítani a differenciált hozzáférés entitás alapon; például a várólista A azonban nem várólista b elküldeni egy jogkivonat tulajdonosi engedélyezése Az engedélyezési környezetet, a kapcsolat rögzített, és nincs lehetőség egyetlen kapcsolat használatára, és különböző hozzáférési jogkivonatok várólista A és b várólista még használni
* Hozzáférési jogkivonatok érvényesek általában csak korlátozott ideig. Az érvényességi rendszeresen újbóli jogkivonatokat a felhasználónak, és lehetőséget nyújt a jogkivonat-kibocsátó elutasítására friss jogkivonatot kibocsátó, a felhasználói hozzáférési engedélyek változása esetén. AMQP kapcsolatok hosszú ideig tarthatnak. A SASL modell csak biztosít arra, hogy állítsa be megfelelően a jogkivonat kapcsolat ideje, ami azt jelenti, hogy az üzenetkezelési infrastruktúra vagy rendelkezik az ügyfél leválasztása, amikor a jogkivonat lejár, vagy fogadja el a kockázat, ha engedélyezi az ügyfél folyamatos kommunikálni kell ki meg hozzáférési jogosultsággal is visszavont kihasználását.

Az AMQP CBS megadását, a Service Bus által megvalósított mindkét ismertetünk lehetővé teszi egy elegáns megkerülő megoldás: lehetővé teszi olyan ügyfelet rendelje hozzá a hozzáférési jogkivonatok egyes csomópontok és a jogkivonatok frissítése előtt járnak, az üzenet folyamat megszakítása nélkül.

CBS határozza meg a virtuális felügyeleti nevű csomópont *$cbs*, meg kell adni az üzenetkezelési infrastruktúra. A csomópontot az üzenetkezelési infrastruktúra többi csomópontjának nevében jogkivonatokat fogad el.

A protokoll hitelesítési mód egy kérelem/válasz az exchange felügyeleti specifikációja által definiált konfigurációjának kialakításához. Hogy azt jelenti, hogy az ügyfél létesít kapcsolatokat két a *$cbs* csomópont ezután továbbítja a kérést a kimenő hivatkozásra, és várakozik a válasz a bejövő hivatkozásra.

A kérelemüzenetben a következő alkalmazás tulajdonságokkal rendelkeznek:

| Kulcs | Nem kötelező | Érték típusa | Érték tartalma |
| --- | --- | --- | --- |
| művelet |Nem |karakterlánc |**a PUT-jogkivonat** |
| típus |Nem |karakterlánc |Mivel ez egy put a jogkivonat típusa. |
| név |Nem |karakterlánc |A "célközönség", amely a token vonatkozik. |
| lejárat |Igen |időbélyeg |A jogkivonat lejárati idejét. |

A *neve* tulajdonság azonosítja az entitást, amellyel a token társítva kell lennie. A Service Bus az elérési útját a várólista, illetve témakört/előfizetést is. A *típus* tulajdonság azonosítja a jogkivonat típusa:

| A jogkivonat típusa | Token leírása | Törzs típusa | Megjegyzések |
| --- | --- | --- | --- |
| amqp:jwt |JSON webes jogkivonat (JWT) |AMQP érték (karakterlánc) |Még nem érhető el. |
| amqp:swt |Egyszerű webes jogkivonat (SWT) |AMQP érték (karakterlánc) |Csak a támogatott AAD/ACS által kiállított SWT jogkivonatokat |
| servicebus.Windows.NET:sastoken |Service Bus SAS-jogkivonat |AMQP érték (karakterlánc) |- |

Jogkivonatok jogosultságokkal rendelkezik. A Service Bus ismer három alapvető jogokat: "Send" lehetővé teszi a "Figyelés" lehetővé teszi, hogy a fogadás, és a "Kezelése" lehetővé teszi, hogy a testreszabhatóvá entitások küld. SWT tokenek kifejezetten az AAD/ACS által kiadott jogcímeket ezeket a jogokat közé. Service Bus SAS-tokenje tekintse meg a névtér vagy entitás beállított szabályokat, és ezeket a szabályokat jogosultságokkal van konfigurálva. A jogkivonat aláírása a kulccsal, hogy a szabályhoz társított így lehetővé teszi a token express a megfelelő jogosultságokat. Egy entitás használatával társított token *put-jogkivonat* lehetővé teszi a kapcsolódó ügyfél / token jogok entitás kommunikál. Egy hivatkozás, amennyiben az ügyfél időt vesz igénybe az a *küldő* telepítése szükséges a "Send" jobb; véve a *fogadó* telepítése a "figyelés" jogosultság szükséges.

A válaszüzenet rendelkezik-e a következő *alkalmazástulajdonságok* értékek

| Kulcs | Nem kötelező | Érték típusa | Érték tartalma |
| --- | --- | --- | --- |
| állapotkód-: |Nem |int |HTTP-válaszkód **[RFC2616]**. |
| állapot-leírása |Igen |karakterlánc |Az állapot leírása. |

Az ügyfél *put-jogkivonat* ismételten és egyetlen entitás az üzenetkezelési infrastruktúrára. A jogkivonatok az aktuális ügyfél hatókörű, és az aktuális kapcsolatot, ami azt jelenti, a kiszolgáló megszakítja a szükséges megtartott jogkivonatokhoz, amikor a kapcsolat csökken a rögzített.

A Service Bus jelenlegi implementációja csak lehetővé teszi, hogy CBS együtt a következő SASL metódussal "Névtelen". SSL/TLS-kapcsolatot a SASL kézfogás előtt mindig tartalmazniuk kell.

A névtelen mechanizmus ezért támogatnia kell a kiválasztott AMQP 1.0-ügyfelet. Névtelen hozzáférés azt jelenti, hogy, hogy a kezdeti kapcsolati kézfogás történik, többek között a kezdeti munkamenet létrehozása a Service Bus nélkül ismerete, akik hoz létre a kapcsolat.

Miután létrejött a kapcsolat és a munkamenet, az alábbi hivatkozásokat csatolása a *$cbs* csomópont és a Küldés a *put-jogkivonat* kérelem, az egyetlen engedélyezett műveletek. Egy érvényes tokent sikeresen használatával állíthatók be a *put-jogkivonat* kérelem néhány entitás csomóponton belül 20 másodperc után a kapcsolat létrejött, ellenkező esetben a kapcsolat egyoldalúan megszakad a Service Bus által.

Az ügyfél ezt követően felelős nyomon követése céljából jogkivonat lejáratáról. A Service Bus egy jogkivonat lejár, azonnal esik összes hivatkozás a megfelelő entitás-kapcsolaton. Ennek megelőzése érdekében az ügyfél is cserélje le a jogkivonatot a csomópont egy új virtuális keresztül bármikor *$cbs* azonos csomópontot *put-jogkivonat* akár többérintéses kézmozdulatokkal is, és a nélkül úton, a a különböző kapcsolatokon forgalomáramlás hasznos forgalom.

### <a name="send-via-functionality"></a>Küldési keresztül funkció

[Küldési keresztül / küldő Transfer](service-bus-transactions.md#transfers-and-send-via) egyik funkciója, amely lehetővé teszi, hogy a szolgáltatás egy cél entitásra keresztül egy másik entitás előre üzenetekkel buszhoz van. Ez főleg használható egy tranzakción belül entitások közötti műveletek végrehajtásához.

Ezzel a funkcióval egy küldő létrehozása és való kapcsolatot létesíteni a `via-entity`. A hivatkozás létrehozásakor további információt az üzenetek/átvitelek erre a hivatkozásra a true cél létrehozásához lett átadva. Miután a csatolási sikeres volt, ez a hivatkozás a küldött összes üzenet a rendszer automatikusan továbbítja a *cél-entitás* keresztül *keresztül entitás*. 

> Megjegyzés: A hitelesítést kifejezetten az is végre kell hajtani *keresztül entitás* és *cél-entitás* Ez a hivatkozás létrehozása előtt.

| Ügyfél | | Service Bus |
| --- | --- | --- |
| (csatolása<br/>Name = {link name}.<br/>szerepkör = küldő,<br/>forrás = {Ügyfélazonosító hivatkozás}<br/>cél =**{keresztül-entitás}**,<br/>**Tulajdonságok térkép = [(<br/>com.microsoft:transfer célcím =<br/>{cél-entitás})]** ) | ------> | |
| | <------ | (csatolása<br/>Name = {link name}.<br/>szerepkör = fogadó,<br/>forrás = {Ügyfélazonosító hivatkozás}<br/>cél = {keresztül-entitás},<br/>Tulajdonságok térkép [() =<br/>célcím com.Microsoft:Transfer =<br/>{cél-entitás})] ) |

## <a name="next-steps"></a>További lépések

AMQP kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Service Bus AMQP áttekintése]
* [Particionált Service Bus-üzenetsorok és témakörök AMQP 1.0 támogatása]
* [A Service Bus a Windows Server AMQP]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[Particionált Service Bus-üzenetsorok és témakörök AMQP 1.0 támogatása]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[A Service Bus a Windows Server AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
