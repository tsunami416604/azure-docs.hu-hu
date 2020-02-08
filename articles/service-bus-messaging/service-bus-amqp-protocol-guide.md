---
title: AMQP 1,0 Azure Service Bus és Event Hubs protokoll útmutatójában | Microsoft Docs
description: A Azure Service Bus és Event Hubs AMQP 1,0-es kifejezésekre és leírására vonatkozó protokoll-útmutató
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
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086142"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1,0 Azure Service Bus és Event Hubs protokoll útmutatója

Az 1,0-as speciális üzenetsor-kezelési protokoll egy szabványosított kialakítási és adatátviteli protokoll a két fél közötti üzenetek aszinkron, biztonságos és megbízható átviteléhez. Ez az Azure Service Bus üzenetkezelés és az Azure Event Hubs elsődleges protokollja. Mindkét szolgáltatás támogatja a HTTPS protokollt is. A védett SBMP protokoll is támogatott a AMQP mellett.

A AMQP 1,0 az olyan széleskörű iparági együttműködés eredménye, amely többek között a Microsoft és a Red Hat köztes, a nagyvállalati szintű üzenetkezeléssel rendelkező felhasználókkal, például a JP Morgan Chasetel közösen egyesítve a pénzügyi szolgáltatások iparágát. A AMQP protokoll és a bővítmények specifikációinak technikai szabványosítási fóruma az OASIS, és nemzetközi szabványként, ISO/IEC 19494-ként megszerezte a formális jóváhagyást.

## <a name="goals"></a>Célok

Ez a cikk röviden összefoglalja az AMQP 1,0 üzenetkezelési specifikációjának alapvető fogalmait, valamint az AMQP technikai bizottságban jelenleg véglegesítésre kerülő draft-bővítményekre vonatkozó specifikációkat, valamint ismerteti, hogyan Azure Service Bus implementálja és építi fel ezeket a specifikációkat.

A cél minden olyan fejlesztő számára, aki bármilyen platformon meglévő AMQP 1,0-alapú ügyfélszoftvert használ, hogy a AMQP 1,0-on keresztül képes legyen kommunikálni a Azure Service Bus használatával.

Az általános célú AMQP 1,0 stackek, például az Apache proton vagy a AMQP.NET Lite, már implementálják az összes Core AMQP 1,0-protokollt. Ezek az alapvető kézmozdulatok időnként magasabb szintű API-val vannak becsomagolva; Az Apache proton még két, a kötelező Messenger API-t és a reaktív reaktor API-t is kínálja.

A következő vitában feltételezzük, hogy a AMQP-kapcsolatok,-munkamenetek és-hivatkozások, valamint a keretek és a folyamatok vezérlésének kezelése a megfelelő verem (például az Apache proton-C) használatával történik, és nem igényel sok figyelmet a konkrét beavatkozásra. az alkalmazás fejlesztőitől. Összevontuk a néhány API-primitív létezését, például a csatlakozás lehetőségét, valamint a *küldő* és a *fogadó* absztrakciós objektumok valamilyen formáját, amelyek ezután a `send()` és a `receive()` műveletek valamilyen formáját alkotják.

A Azure Service Bus speciális képességeinek, például az üzenetek böngészésének vagy a munkamenetek kezelésének megvitatásakor ezek a funkciók AMQP feltételekkel vannak elmagyarázva, de a feltételezett API-absztrakcióhoz hasonlóan rétegzett pszeudo-implementációnak is.

## <a name="what-is-amqp"></a>Mi az a AMQP?

A AMQP egy keretezési és adatátviteli protokoll. A keretezés azt jelenti, hogy olyan bináris adatfolyamok szerkezetét biztosítja, amelyek a hálózati kapcsolatok bármelyik irányában áramlanak. A struktúra körvonalazza az *adatkeretek*elnevezésű különböző adatblokkokat a csatlakoztatott felek között. Az átvitel képességei biztosítják, hogy mindkét kommunikációs fél közösen megértse a keretek átadásának időpontját, és az átvitelek teljesnek tekintendők.

A korábbi lejárt, a AMQP munkacsoport által létrehozott verzióktól eltérően, amelyeket néhány üzenet-bróker még használ, a munkacsoport végleges és szabványosított AMQP 1,0-es protokollja nem írja elő az üzenet-átvitelszervező vagy bármely olyan speciális topológia jelenlétét, amelynek az üzenet-átvitelszervezőben lévő entitások.

A protokoll használható a szimmetrikus társ-társ kommunikációhoz, a várólistákat és a közzétételi/előfizetési entitásokat támogató üzenetküldési műveletekhez, ahogy az Azure Service Bus. Az üzenetkezelési infrastruktúrával való interakcióhoz is használható, ahol az interakciós minták eltérnek a normál várólistáktól, az Azure Event Hubs esetében is. Az Event hub úgy viselkedik, mint egy üzenetsor, amikor az eseményeket elküldjük, de a soros tárolási szolgáltatáshoz hasonlóan viselkednek, ha az események beolvasása megtörtént. némileg hasonlít egy szalagos meghajtóra. Az ügyfél egy eltolást választ a rendelkezésre álló adatfolyamba, majd az összes olyan eseményt kiszolgálja, amely az eltolástól a legújabb elérhetővé válik.

A AMQP 1,0 protokollt úgy tervezték, hogy kiterjeszthető legyen, és további specifikációkat engedélyezzen a képességeinek javítása érdekében. A jelen dokumentumban ismertetett három kiterjesztési specifikáció ezt mutatja be. A meglévő HTTPS/WebSockets infrastruktúrával való kommunikációhoz nehéz lehet a natív AMQP TCP-portok konfigurálása. A kötési specifikáció határozza meg, hogyan lehet a AMQP-t websocketek fölé felvenni. Ahhoz, hogy az üzenetkezelési infrastruktúrát egy kérés/válasz módon kezelje felügyeleti célokra, vagy speciális funkciókat nyújtson, a AMQP-kezelési specifikáció meghatározza a szükséges alapszintű interakciós primitíveket. Az összevont hitelesítési modell integrációja esetében a AMQP-jogcím-alapú biztonsági specifikáció határozza meg, hogyan kell társítani és megújítani a hivatkozásokhoz társított engedélyezési jogkivonatokat.

## <a name="basic-amqp-scenarios"></a>Alapszintű AMQP forgatókönyvek

Ez a szakasz a AMQP 1,0 alapszintű használatát ismerteti Azure Service Bus, amely magában foglalja a kapcsolatok, a munkamenetek és a hivatkozások létrehozását, valamint az üzenetek átvitelét Service Bus entitásokból, például várólistákból, témakörökből és előfizetésből.

A leghitelesebb forrás, amelyből megismerheti, hogy a AMQP hogyan működik a AMQP 1,0 specifikációban, de a specifikációt a megvalósítás pontos útmutatója, és nem a protokoll tanítása érdekében írták. Ez a szakasz az AMQP 1,0-et használó Service Bus használatának leírásához szükséges nagy terminológia bevezetését ismerteti. A AMQP átfogóbb bevezetéséhez, valamint a AMQP 1,0 szélesebb körű megvitatására tekintse át [ezt a videó tanfolyamot][this video course].

### <a name="connections-and-sessions"></a>Kapcsolatok és munkamenetek

A AMQP meghívja a kommunikáló programok *tárolóit*; Ezek olyan *csomópontokat*tartalmaznak, amelyek a tárolóban lévő entitásokat kommunikálják. A várólista lehet egy csomópont. A AMQP lehetővé teszi a többszörös használatot, így egyetlen kapcsolat használható a csomópontok közötti számos kommunikációs útvonalhoz. egy alkalmazás-ügyfél például egyidejűleg tud fogadni egy várólistából, és ugyanazon a hálózati kapcsolaton keresztül küld egy másik várólistára.

![][1]

A hálózati kapcsolat így a tárolóra van rögzítve. Az ügyfél szerepkör tárolója kezdeményezi a kimenő TCP szoftvercsatorna-kapcsolatot a fogadó szerepkör egyik tárolójában, amely figyeli és fogadja a bejövő TCP-kapcsolatokat. A kapcsolati kézfogás magában foglalja a protokoll verziószámának egyeztetését, a Transport Level Security (TLS/SSL) használatának bejelentését vagy egyeztetését, valamint egy hitelesítési/engedélyezési kézfogást a SASL alapuló kapcsolati hatókörben.

Azure Service Bus a TLS-t mindig használni kell. Támogatja a 5671-es TCP-porton keresztüli kapcsolatokat, így a TCP-kapcsolat először bekerül a TLS-be a AMQP protokoll kézfogásának megadása előtt, valamint támogatja a 5672-es TCP-porton keresztüli kapcsolatokat, amelyekkel a kiszolgáló azonnal a kapcsolat kötelező frissítését biztosítja a TLS-t a AMQP által előírt modell használatával. A AMQP WebSockets kötés létrehoz egy alagutat a 443-as TCP-porton, amely ezután egyenértékű a AMQP 5671-kapcsolatokkal.

A kapcsolatok és a TLS beállítása után Service Bus két SASL mechanizmust kínál:

* SASL PLAIN általában a Felhasználónév és a jelszó hitelesítő adatainak egy kiszolgálóra való átadására szolgál. Service Bus nem rendelkezik fiókkal, de névvel ellátott [megosztott hozzáférésű biztonsági szabályok](service-bus-sas.md), amelyek jogosultságokat biztosítanak, és egy kulccsal vannak társítva. A rendszer a Felhasználónév és a kulcs (Base64 kódolású szöveg) nevét használja jelszóként. A választott szabállyal társított jogosultságok szabályozzák a kapcsolatban engedélyezett műveleteket.
* A SASL NÉVTELENül használja a SASL-engedélyezés megkerülését, ha az ügyfél szeretné használni a később ismertetett jogcímbarát (CBS) modellt. Ezzel a beállítással egy ügyfélkapcsolatot névtelenül lehet létrehozni egy rövid ideig, amíg az ügyfél csak a CBS-végponttal tud kommunikálni, és a CBS-kézfogásnak be kell fejeződnie.

A szállítási kapcsolat létrejötte után a tárolók kinyilvánítják a maximálisan kezelni kívánt képkockákat, és az Üresjárati időkorlát után egyoldalúan bontja a kapcsolatot, ha nincs tevékenység a kapcsolaton.

Azt is nyilatkozzák, hogy hány párhuzamos csatorna támogatott. A csatorna egy egyirányú, kimenő, virtuális átviteli útvonal a kapcsolatok tetején. A munkamenetek minden összekapcsolt tárolóból egy kétirányú kommunikációs útvonalat alkotnak.

A munkamenetek ablakos folyamat-ellenőrzési modellel rendelkeznek; a munkamenetek létrehozásakor mindkét fél kijelenti, hogy hány képkockát szeretne fogadni a fogadási ablakába. Ahogy a felek Exchange-keretek, az átvitt keretek kitöltik az ablakot, és az átvitel leáll, amikor az ablak megtelt, és amíg az ablak alaphelyzetbe nem áll a *flow performatív* (a*performatív* a két fél között cserélt protokoll szintű kézmozdulatok AMQP kifejezése).

Ez az ablak alapú modell nagyjából hasonlít az ablakos folyamatok vezérlésének TCP-fogalmára, de a szoftvercsatornán belüli munkamenet szintjén. A protokoll azon koncepciója, amely lehetővé teszi több egyidejű munkamenet használatát, így a magas prioritású forgalmat a korábbi, az országúti expressz sávban, például a normál forgalomnál lehet kirohanni.

Azure Service Bus jelenleg pontosan egy munkamenetet használ az egyes kapcsolatokhoz. A Service Bus maximális mérete 262 144 bájt (256-K bájt) Service Bus standard és Event Hubs. A Service Bus Premium esetében 1 048 576 (1 MB). A Service Bus nem határoz meg egy adott munkamenet-szintű szabályozási időszakot, de a kapcsolati szintű folyamatvezérlés részeként rendszeresen alaphelyzetbe állítja az ablakot (lásd [a következő szakaszt](#links)).

A kapcsolatok, a csatornák és a munkamenetek elmúlóak. Ha az alapul szolgáló kapcsolat összeomlik, a kapcsolatok, a TLS-alagút, a SASL engedélyezési környezet, és a munkameneteket újra kell létrehozni.

### <a name="amqp-outbound-port-requirements"></a>AMQP kimenő portokra vonatkozó követelmények

A TCP protokollon keresztül AMQP-kapcsolatokat használó ügyfeleknek a helyi tűzfalon kell megnyitnia a 5671-es és a 5672-es portot. A portok mellett szükség lehet további portok megnyitására is, ha a [EnableLinkRedirect](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect?view=azure-dotnet) szolgáltatás engedélyezve van. a `EnableLinkRedirect` egy új üzenetkezelési funkció, amely segít kihagyni az egyugrásos üzeneteket az üzenetek fogadása közben, így segítve az átviteli sebesség növelését. Az ügyfél az alábbi képen látható módon elkezdi a közvetlen kommunikációt a 104XX a porttartomány-tartományon keresztül. 

![Cél portok listája][4]

A .NET-ügyfelek meghiúsulnak a SocketException ("a hozzáférési engedélyeik által tiltott szoftvercsatornák elérésére tett kísérlet"), ha a tűzfal blokkolja ezeket a portokat. A szolgáltatás letiltható a történt karakterláncban `EnableAmqpLinkRedirect=false` beállításával, amely arra kényszeríti az ügyfeleket, hogy az 5671-as porton keresztül kommunikáljanak a távoli szolgáltatással.


### <a name="links"></a>Hivatkozások

A AMQP a hivatkozásokon keresztül továbbítja az üzeneteket. A hivatkozás egy munkameneten keresztül létrehozott kommunikációs útvonal, amely lehetővé teszi, hogy az üzeneteket az egyik irányba vigye át. az átvitel állapotának egyeztetése a kapcsolaton keresztül történik, és kétirányú a csatlakoztatott felek között.

![][2]

A hivatkozásokat a tárolók bármikor létrehozhatják egy meglévő munkamenet során, így a AMQP számos más protokolltól, például a HTTP-től és a MQTT-tól eltérő módon hozhatók létre, ahol az átvitel és az átadás útvonalának kezdeményezése kizárólagos jogosultságot biztosít a szoftvercsatorna-kapcsolat.

A csatolást kezdeményező tároló megkéri a szemközti tárolót, hogy fogadjon egy hivatkozást, és kiválasztja a küldő vagy a fogadó szerepkört. Ebből kifolyólag a tárolók nem hozhatnak létre egyirányú vagy kétirányú kommunikációs útvonalakat, és az utóbbit a hivatkozások pár hivatkozása is létrehozhatja.

A hivatkozások neve és a csomópontokhoz van társítva. Ahogy azt az elején is említettük, a csomópontok egy tárolón belüli kommunikáló entitások.

Service Bus a csomópontok közvetlenül egyenértékűek egy üzenetsor, egy témakör, egy előfizetés vagy egy üzenetsor vagy előfizetés kézbesítetlen levelek-alvárólistájának. A AMQP használt csomópont neve ezért a Service Bus névtérben található entitás relatív neve. Ha a várólista neve `myqueue`, akkor az a AMQP-csomópont neve is. A témakör-előfizetés a HTTP API-konvenciót követi a "Subscriptions" (előfizetések) erőforrás-gyűjteménybe rendezve, így a témakör **mytopic** **a AMQP** csomópont neve **mytopic/Subscriptions/sub**.

A Connecting ügyfélnek a hivatkozások létrehozásához helyi csomópont nevét is kell használnia. Service Bus nem a csomópontok neveivel kapcsolatos előírásokat tartalmaz, és nem értelmezi őket. A AMQP 1,0-ügyfelek általában egy sémát használnak annak biztosítására, hogy az ideiglenes csomópontok nevei egyediek legyenek az ügyfél hatókörében.

### <a name="transfers"></a>Transzferek

A kapcsolat létrejötte után az üzenetek átvihetők a hivatkozás fölé. A AMQP az átvitelt egy explicit protokoll-kézmozdulattal (az *átvitel* performatív) hajtja végre, amely egy hivatkozáson keresztül áthelyezi az üzenetet a feladótól a fogadóba. Az átvitel akkor fejeződik be, amikor a rendszer "rendezi", ami azt jelenti, hogy mindkét fél közösen megértette az átvitel eredményét.

![][3]

A legegyszerűbb esetben a küldő dönthet úgy, hogy "előre rendezve" üzenetet küld, ami azt jelenti, hogy az ügyfél nem érdekli az eredmény, és a fogadó nem ad visszajelzést a művelet eredményéről. Ezt a módot a Service Bus a AMQP protokoll szintjén támogatja, de az ügyféloldali API-k egyikében sincs kitéve.

A rendszeres eset az, hogy az üzeneteket a rendszer nem rendezi, és a fogadó ezt követően az elfogadást vagy elutasítást jelzi a *törlési* performatív használatával. Elutasítás akkor következik be, ha a fogadó semmilyen okból nem fogadja el az üzenetet, és az Elutasítási üzenet a AMQP által definiált hibával kapcsolatos információkat tartalmaz. Ha az üzenetek elutasítása a Service Bus belső hibái miatt történik, a szolgáltatás az adott struktúrán belül további adatokat ad vissza, amelyek a támogatási kérések bejelentésére szolgáló diagnosztikai útmutatók biztosításához használhatók. További információ a hibákról.

Az elutasítás egy speciális formája a *felszabadított* állapot, amely azt jelzi, hogy a fogadó nem rendelkezik technikai kifogással az átvitelhez, de nem érdekli az átvitel rendezése is. Ez az eset például akkor áll fenn, ha egy üzenetet egy Service Bus ügyfélnek küldenek, és az ügyfél úgy dönt, hogy "elhagyja" az üzenetet, mert nem tudja végrehajtani az üzenet feldolgozásával létrejövő munkát. az üzenet kézbesítése nem vétkes. Ennek az állapotnak a variációja a *módosított* állapot, amely lehetővé teszi, hogy az üzenet a megjelenésének megfelelően módosítható legyen. A Service Bus jelenleg nem használja ezt az állapotot.

A AMQP 1,0 specifikáció a *kapott*további állapotot határozza meg, amely különösen segít a kapcsolat helyreállításának kezelésében. A link Recovery lehetővé teszi egy hivatkozás állapotának, valamint az új kapcsolaton és munkameneten felüli függőben lévő kézbesítések helyreállítását, ha az előző kapcsolat és a munkamenet megszakadt.

Service Bus nem támogatja a csatolások helyreállítását; Ha az ügyfél elveszíti a kapcsolatot, hogy Service Bus egy nem teljesített üzenetküldési folyamatban, az üzenet átvitele megszakad, és az ügyfélnek újra kell kapcsolódnia, újra létre kell hoznia a hivatkozást, majd újra kell próbálkoznia az átvitelsel.

Ennek megfelelően a Service Bus és Event Hubs támogatja a "legalább egyszer" olyan átvitelt, ahol a küldő biztos lehet abban, hogy az üzenetet tárolják és elfogadták, de nem támogatják a "pontosan egyszeri" átvitelt a AMQP szinten, ahol a rendszer megkísérli helyreállítani a hivatkozást, és folytassa a kézbesítési állapot egyeztetésével, hogy elkerülje az üzenetek átvitelének ismétlődését.

A lehetséges duplikált küldések kompenzálása érdekében a Service Bus támogatja az ismétlődő észlelést a várólisták és témakörök választható funkciójaként. A duplikált észlelési funkció az összes bejövő üzenet üzenet-azonosítóját rögzíti egy felhasználó által meghatározott időablakban, majd csendesen eldobja az azonos üzenet-azonosítókkal küldött üzeneteket az adott ablakban.

### <a name="flow-control"></a>Folyamat vezérlése

A korábban tárgyalt munkamenet-szintű folyamat-vezérlési modellen kívül minden hivatkozás saját flow-vezérlési modellel rendelkezik. A munkamenet-szintű folyamat vezérlése megvédi a tárolót attól, hogy egyszerre túl sok képkockát kezeljen, a kapcsolati szintű flow-vezérlés pedig az alkalmazás felügyeletét, hogy hány üzenetet szeretne kezelni egy hivatkozásról és mikor.

![][4]

Egy hivatkozáson az átvitelek csak akkor történnek, ha a küldőnek van elegendő *hivatkozása*. A link Credit a fogadó által a *flow* performatív használatával beállított számláló, amely egy hivatkozásra terjed ki. Ha a küldőhöz hivatkozás-jóváírás van hozzárendelve, az üzenetek kézbesítésével megkísérli ezt a jóváírást használni. Minden üzenet kézbesítése eggyel csökkenti a fennmaradó hivatkozás-jóváírást. A hivatkozás felhasználása után a kézbesítések leállnak.

Ha Service Bus a fogadó szerepkörben van, az azonnal biztosítja a küldő számára a bőséges hivatkozási jóváírást, így az üzenetek azonnal elküldhetők. A kapcsolati kreditek használatakor Service Bus alkalmanként egy *flow* -performatív küld a küldőnek a kapcsolati kreditek egyenlegének frissítéséhez.

A küldő szerepkörben Service Bus üzeneteket küld az összes függőben lévő hivatkozás jóváírásának használatára.

Az API szintjén a "Receive" hívás egy olyan *folyamatot* performatív, amelyet az ügyfél Service Bus küld, és Service Bus a hitelkeretet az első elérhető, kinyitott üzenet alapján, zárolással és a sorból való átvitelsel. Ha nincs elérhető üzenet a kézbesítéshez, az adott entitással létesített bármelyik kapcsolatra vonatkozó, az adott entitáshoz tartozó fennmaradó kreditet az érkezési sorrendben rögzíti a rendszer, és az üzenetek zárolva lesznek, és az elérhetővé válnak, hogy a rendelkezésre álló krediteket használják.

Az üzenet zárolása akkor jelenik meg, ha az átvitel bekerül az egyik olyan terminál-állapotba, amelyet *elfogadtak*, *visszautasítanak*vagy *felszabadítanak*. Az üzenet el lesz távolítva Service Bus a terminál állapotának *elfogadásakor*. Service Bus marad, és a következő fogadónak küldi el, amikor az átvitel eléri a többi állapotot. Service Bus automatikusan áthelyezi az üzenetet az entitás kézbesítetlen levelek-sorába, amikor eléri az entitás számára engedélyezett maximális kézbesítési értéket az ismételt elutasítás vagy kiadás miatt.

Annak ellenére, hogy a Service Bus API-k közvetlenül nem tesznek elérhetővé ilyen lehetőséget, az alacsonyabb szintű AMQP protokoll-ügyfél használhatja a link-Credit modellt úgy, hogy a "lekéréses" interakciót kiadja az egyes fogadási kérelmek "leküldéses stílus" modelljébe nagyszámú hivatkozási kredit kiállítása, majd az üzenetek fogadása, ha további interakció nélkül válnak elérhetővé. A leküldéses szolgáltatás a [MessagingFactory. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) vagy a [MessageReceiver. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) tulajdonság beállításain keresztül támogatott. Ha nem nulla, a AMQP-ügyfél a hivatkozási jóváírásként használja.

Ebben a kontextusban fontos tisztában lenni azzal, hogy az entitáson belüli üzenet zárolásának lejárati ideje akkor kezdődik el, amikor az üzenetet az entitásról küldi el, nem pedig az üzenet a drótra helyezésekor. Ha az ügyfél készenléti állapotot jelez az üzenetek küldéséhez a hivatkozás jóváírásával, ezért a rendszer az üzeneteket aktívan a hálózaton keresztül húzza, és készen áll a kezelésre. Ellenkező esetben előfordulhat, hogy az üzenet zárolása még az üzenet lejárta előtt lejár. A kapcsolati kreditek vezérlésének használata közvetlenül tükrözi az azonnali készültséget a fogadónak küldött, elérhető üzenetek kezeléséhez.

Az alábbi szakaszokban a performatív folyamat sematikus áttekintése található a különböző API-interakciók során. Mindegyik szakasz egy másik logikai műveletet ír le. Előfordulhat, hogy az interakciók némelyike "lusta", ami azt jelenti, hogy csak szükség esetén hajthatók végre. Előfordulhat, hogy az üzenet küldőjének létrehozása nem okoz hálózati interakciót az első üzenet küldése vagy kérése előtt.

Az alábbi táblázatban látható nyilak a performatív irányát mutatják.

#### <a name="create-message-receiver"></a>Üzenet fogadójának létrehozása

| Ügyfél | Service Bus |
| --- | --- |
| – > csatolás (<br/>név = {hivatkozás neve},<br/>Handle = {numerikus leíró},<br/>szerepkör =**fogadó**,<br/>forrás = {entitás neve},<br/>Target = {ügyfél-hivatkozási azonosító}<br/>) |Az ügyfél az entitást fogadóként csatolja |
| Service Bus a hivatkozás végét csatoló válaszok |< – csatolás (<br/>név = {hivatkozás neve},<br/>Handle = {numerikus leíró},<br/>szerepkör =**Feladó**,<br/>forrás = {entitás neve},<br/>Target = {ügyfél-hivatkozási azonosító}<br/>) |

#### <a name="create-message-sender"></a>Üzenet feladójának létrehozása

| Ügyfél | Service Bus |
| --- | --- |
| – > csatolás (<br/>név = {hivatkozás neve},<br/>Handle = {numerikus leíró},<br/>szerepkör =**Feladó**,<br/>forrás = {ügyfél-hivatkozási azonosító},<br/>Target = {entitás neve}<br/>) |Nincs művelet |
| Nincs művelet |< – csatolás (<br/>név = {hivatkozás neve},<br/>Handle = {numerikus leíró},<br/>szerepkör =**fogadó**,<br/>forrás = {ügyfél-hivatkozási azonosító},<br/>Target = {entitás neve}<br/>) |

#### <a name="create-message-sender-error"></a>Üzenet feladójának létrehozása (hiba)

| Ügyfél | Service Bus |
| --- | --- |
| – > csatolás (<br/>név = {hivatkozás neve},<br/>Handle = {numerikus leíró},<br/>szerepkör =**Feladó**,<br/>forrás = {ügyfél-hivatkozási azonosító},<br/>Target = {entitás neve}<br/>) |Nincs művelet |
| Nincs művelet |< – csatolás (<br/>név = {hivatkozás neve},<br/>Handle = {numerikus leíró},<br/>szerepkör =**fogadó**,<br/>forrás = null,<br/>target=null<br/>)<br/><br/>< – leválasztás (<br/>Handle = {numerikus leíró},<br/>lezárva =**igaz**,<br/>hiba = {hiba adatai}<br/>) |

#### <a name="close-message-receiversender"></a>Üzenet fogadójának/feladójának lezárása

| Ügyfél | Service Bus |
| --- | --- |
| – > leválasztás (<br/>Handle = {numerikus leíró},<br/>lezárva =**igaz**<br/>) |Nincs művelet |
| Nincs művelet |< – leválasztás (<br/>Handle = {numerikus leíró},<br/>lezárva =**igaz**<br/>) |

#### <a name="send-success"></a>Küldés (sikeres)

| Ügyfél | Service Bus |
| --- | --- |
| – > átvitel (<br/>kézbesítési azonosító = {numerikus leíró},<br/>Delivery-tag = {bináris leíró},<br/>kiegyenlített =**hamis**,, több =**hamis**,<br/>állapot =**Null**,<br/>Folytatás =**hamis**<br/>) |Nincs művelet |
| Nincs művelet |< – hajlam (<br/>szerepkör = fogadó,<br/>első = {kézbesítési azonosító},<br/>utolsó = {kézbesítési azonosító},<br/>kiegyenlített =**igaz**,<br/>állapot =**elfogadva**<br/>) |

#### <a name="send-error"></a>Küldés (hiba)

| Ügyfél | Service Bus |
| --- | --- |
| – > átvitel (<br/>kézbesítési azonosító = {numerikus leíró},<br/>Delivery-tag = {bináris leíró},<br/>kiegyenlített =**hamis**,, több =**hamis**,<br/>állapot =**Null**,<br/>Folytatás =**hamis**<br/>) |Nincs művelet |
| Nincs művelet |< – hajlam (<br/>szerepkör = fogadó,<br/>első = {kézbesítési azonosító},<br/>utolsó = {kézbesítési azonosító},<br/>kiegyenlített =**igaz**,<br/>állapot =**visszautasítva**(<br/>hiba = {hiba adatai}<br/>)<br/>) |

#### <a name="receive"></a>Fogadás

| Ügyfél | Service Bus |
| --- | --- |
| – > folyamat (<br/>link-credit=1<br/>) |Nincs művelet |
| Nincs művelet |< átvitel (<br/>kézbesítési azonosító = {numerikus leíró},<br/>Delivery-tag = {bináris leíró},<br/>kiegyenlített =**hamis**,<br/>további =**hamis**,<br/>állapot =**Null**,<br/>Folytatás =**hamis**<br/>) |
| – >i hajlam (<br/>szerepkör =**fogadó**,<br/>első = {kézbesítési azonosító},<br/>utolsó = {kézbesítési azonosító},<br/>kiegyenlített =**igaz**,<br/>állapot =**elfogadva**<br/>) |Nincs művelet |

#### <a name="multi-message-receive"></a>Több üzenet fogadása

| Ügyfél | Service Bus |
| --- | --- |
| – > folyamat (<br/>link-credit=3<br/>) |Nincs művelet |
| Nincs művelet |< átvitel (<br/>kézbesítési azonosító = {numerikus leíró},<br/>Delivery-tag = {bináris leíró},<br/>kiegyenlített =**hamis**,<br/>további =**hamis**,<br/>állapot =**Null**,<br/>Folytatás =**hamis**<br/>) |
| Nincs művelet |< átvitel (<br/>kézbesítési azonosító = {numerikus leíró + 1},<br/>Delivery-tag = {bináris leíró},<br/>kiegyenlített =**hamis**,<br/>további =**hamis**,<br/>állapot =**Null**,<br/>Folytatás =**hamis**<br/>) |
| Nincs művelet |< átvitel (<br/>kézbesítési azonosító = {numerikus leíró + 2},<br/>Delivery-tag = {bináris leíró},<br/>kiegyenlített =**hamis**,<br/>további =**hamis**,<br/>állapot =**Null**,<br/>Folytatás =**hamis**<br/>) |
| – >i hajlam (<br/>szerepkör = fogadó,<br/>első = {kézbesítési azonosító},<br/>utolsó = {kézbesítési azonosító + 2},<br/>kiegyenlített =**igaz**,<br/>állapot =**elfogadva**<br/>) |Nincs művelet |

### <a name="messages"></a>Üzenetek

A következő szakaszokból megtudhatja, hogy a rendszer milyen tulajdonságokat használ a standard AMQP-üzenet szakaszokból Service Bus és hogyan képezi le a Service Bus API-készletet.

Az alkalmazás által definiált összes tulajdonságot le kell képezni a AMQP `application-properties` térképére.

#### <a name="header"></a>header

| Mezőnév | Használat | API neve |
| --- | --- | --- |
| durable |- |- |
| priority |- |- |
| ttl |Az üzenet élettartama |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| first-acquirer |- |- |
| delivery-count |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Mezőnév | Használat | API neve |
| --- | --- | --- |
| üzenet-azonosító |Az üzenet alkalmazás által definiált, szabad formátumú azonosítója. Ismétlődő észleléshez használatos. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Az alkalmazás által definiált felhasználói azonosító, Service Bus nem értelmezhető. |Nem érhető el a Service Bus API-n keresztül. |
| erre: |Az alkalmazás által definiált cél-azonosító, amelyet a Service Bus nem értelmez. |[Címzett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| subject |Az alkalmazás által definiált üzenet céljának azonosítója Service Bus szerint nem értelmezhető. |[Címke](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| reply-to |Az alkalmazás által definiált válasz-elérésiút jelző, amelyet a Service Bus nem értelmez. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Az alkalmazás által definiált korrelációs azonosító Service Bus nem értelmezhető. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Content-Type |Az alkalmazás által definiált Content-Type jelző a törzshöz, amelyet a Service Bus nem értelmez. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Content-Encoding |Az alkalmazás által definiált tartalom-kódolási jelző a törzshöz, amelyet a Service Bus nem értelmez. |Nem érhető el a Service Bus API-n keresztül. |
| abszolút – lejárat – idő |Kijelenti, hogy az üzenet abszolút azonnali érvényessége lejár. Figyelmen kívül hagyva a bemeneten (a fejléc ÉLETTARTAMa megfigyelt), a mérvadó a kimeneten. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| creation-time |Deklarálja, hogy az üzenet mikor lett létrehozva. Nem használja Service Bus |Nem érhető el a Service Bus API-n keresztül. |
| csoport azonosítója |Az alkalmazás által definiált azonosító egy kapcsolódó üzenethez. Service Bus-munkamenetekhez használatos. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| group-sequence |Az üzenet relatív sorszámát azonosító számláló a munkameneten belül. Service Bus figyelmen kívül hagyva. |Nem érhető el a Service Bus API-n keresztül. |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Üzenetek megjegyzései

Néhány más Service Bus-üzenet tulajdonságai is vannak, amelyek nem részei a AMQP üzenet tulajdonságainak, és az üzenet `MessageAnnotations`.

| Jegyzet leképezési kulcsa | Használat | API neve |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | Deklarálja, hogy mikor jelenjen meg az üzenet az entitáson |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-Partition-Key | Az alkalmazás által definiált kulcs, amely azt határozza meg, hogy az üzenet melyik partíción belül kell, hogy legyen. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x – opt-on-Partition-Key | Az alkalmazás által definiált partíciós kulcs értéke, ha tranzakciót kell használni az üzenetek átviteli várólistán keresztüli küldéséhez. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-enqueued-time | A szolgáltatás által definiált UTC-idő, amely az üzenet enqueuing tényleges időpontját jelöli. A bemenet figyelmen kívül hagyva. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-sequence-number | A szolgáltatás által meghatározott egyedi szám, amely egy üzenethez van rendelve. | [Sorszám](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x – opt-offset | Az üzenet szolgáltatás által definiált várólistán lévő sorszáma. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x – opt-Locked – csak | Szolgáltatás által definiált. Az a dátum és idő, ameddig az üzenet zárolva lesz a várólistában/előfizetésben. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-source | Szolgáltatás által definiált. Ha az üzenet a kézbesítetlen levelek várólistáról érkezik, az eredeti üzenet forrása. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Tranzakciós képesség

Egy tranzakció két vagy több műveletet egyesít egy végrehajtási hatókörbe. Természeténél fogva az ilyen tranzakciónak biztosítania kell, hogy az adott műveleti csoportba tartozó összes művelet a sikeres vagy a sikertelen műveletekkel együtt járjon el.
A műveletek egy azonosító `txn-id`szerint vannak csoportosítva.

A tranzakciós interakcióhoz az ügyfél `transaction controller`ként működik, amely a csoportosítani kívánt műveleteket vezérli. Service Bus a szolgáltatás `transactional resource`ként működik, és a `transaction controller`által kért munkát hajt végre.

Az ügyfél és a szolgáltatás egy `control link`on keresztül kommunikál, amelyet az ügyfél létesített. A vezérlő a vezérlő hivatkozásán keresztül elküldi a `declare` és `discharge` üzeneteket a tranzakciók lefoglalásához és befejezéséhez (nem a tranzakciós munka körülhatárolása). A tényleges küldés/fogadás nem történik meg ezen a hivatkozáson. A kért tranzakciós műveletek explicit módon vannak azonosítva a kívánt `txn-id`, ezért előfordulhat, hogy a kapcsolaton bármilyen kapcsolaton fordulnak elő. Ha a vezérlő hivatkozása le van zárva, amíg léteznek nem kiosztható tranzakciók, akkor a rendszer azonnal visszaállítja az összes ilyen tranzakciót, és megkísérli a további tranzakciós munkát végezni rajtuk. A vezérlési hivatkozás üzenetei nem lehetnek előre rendezve.

Minden kapcsolatnak meg kell indítania a saját vezérlési hivatkozását, hogy képes legyen elindítani és leállítani a tranzakciókat. A szolgáltatás olyan speciális célt határoz meg, amely `coordinator`ként működik. Az ügyfél/vezérlő egy vezérlőelem-hivatkozást hoz létre ehhez a célhoz. A vezérlési hivatkozás egy entitás határán kívül esik, azaz ugyanaz a vezérlési kapcsolat használható több entitás tranzakcióinak elindításához és kiteljesítéséhez.

#### <a name="starting-a-transaction"></a>Tranzakció indítása

A tranzakciós munka megkezdéséhez. a vezérlőnek be kell szereznie egy `txn-id` a koordinátortól. Ezt egy `declare` típusú üzenet küldésével végezheti el. Ha a deklaráció sikeres, a koordinátor egy, a hozzárendelt `txn-id`t tartalmazó törlési eredménnyel válaszol.

| Ügyfél (vezérlő) | | Service Bus (koordinátor) |
| --- | --- | --- |
| csatolja<br/>név = {hivatkozás neve},<br/>... ,<br/>szerepkör =**Feladó**,<br/>Target =**koordinátor**<br/>) | ------> |  |
|  | <------ | csatolja<br/>név = {hivatkozás neve},<br/>... ,<br/>target=Coordinator()<br/>) |
| transzfer<br/>kézbesítési azonosító = 0,...)<br/>{AmqpValue (**deklarálás ()** )}| ------> |  |
|  | <------ | törlése <br/> első = 0, utolsó = 0, <br/>állapot =**deklarált**(<br/>**tranzakció-ID**= {tranzakció azonosítója}<br/>))|

#### <a name="discharging-a-transaction"></a>Tranzakció kitöltése

A vezérlő `discharge` üzenetet küld a koordinátornak, és a tranzakciós munkát arra következteti. A vezérlő azt jelzi, hogy a tranzakciós munka végrehajtásához vagy visszavonásához a `fail` jelzőt kell beállítania a mentesítő törzsön. Ha a koordinátor nem tudja befejezni a mentesítést, a rendszer elutasítja az üzenetet, és ezzel az eredménnyel a `transaction-error`folytatja.

> Megjegyzés: a sikertelen = igaz érték egy tranzakció visszaállítására utal, és a sikertelen = hamis a véglegesítés.

| Ügyfél (vezérlő) | | Service Bus (koordinátor) |
| --- | --- | --- |
| transzfer<br/>kézbesítési azonosító = 0,...)<br/>{AmqpValue (deklarálás ())}| ------> |  |
|  | <------ | törlése <br/> első = 0, utolsó = 0, <br/>állapot = deklarált (<br/>txn-id={transaction ID}<br/>))|
| | . . . <br/>Tranzakciós munka<br/>egyéb hivatkozásokon<br/> . . . |
| transzfer<br/>kézbesítés-azonosító = 57,...)<br/>{ AmqpValue (<br/>**Mentesítés (tranzakció-ID = 0,<br/>sikertelen = false)** )}| ------> |  |
| | <------ | törlése <br/> első = 57, utolsó = 57, <br/>állapot =**elfogadva ()** )|

#### <a name="sending-a-message-in-a-transaction"></a>Üzenet küldése egy tranzakcióban

Az összes tranzakciós tevékenység a tranzakciós kézbesítési állapottal `transactional-state`, amely a tranzakció-azonosítót végzi. Üzenetek küldése esetén a tranzakciós állapotot az üzenet adatátviteli kerete végzi. 

| Ügyfél (vezérlő) | | Service Bus (koordinátor) |
| --- | --- | --- |
| transzfer<br/>kézbesítési azonosító = 0,...)<br/>{AmqpValue (deklarálás ())}| ------> |  |
|  | <------ | törlése <br/> első = 0, utolsó = 0, <br/>állapot = deklarált (<br/>txn-id={transaction ID}<br/>))|
| transzfer<br/>Handle = 1,<br/>kézbesítés-azonosító = 1, <br/>**State =<br/>TransactionalState (<br/>tranzakció-ID = 0)** )<br/>adattartalom| ------> |  |
| | <------ | törlése <br/> első = 1, utolsó = 1, <br/>State =**TransactionalState (<br/>tranzakció-ID = 0,<br/>eredmény = elfogadva ()** ))|

#### <a name="disposing-a-message-in-a-transaction"></a>Üzenet ártalmatlanítása egy tranzakcióban

Az üzenet-törlés olyan műveleteket tartalmaz, mint például a `Complete` / `Abandon` / `DeadLetter` / `Defer`. Ha tranzakción belül szeretné elvégezni ezeket a műveleteket, adja át a `transactional-state`t a rendelkezésére.

| Ügyfél (vezérlő) | | Service Bus (koordinátor) |
| --- | --- | --- |
| transzfer<br/>kézbesítési azonosító = 0,...)<br/>{AmqpValue (deklarálás ())}| ------> |  |
|  | <------ | törlése <br/> első = 0, utolsó = 0, <br/>állapot = deklarált (<br/>txn-id={transaction ID}<br/>))|
| | <------ |transzfer<br/>Handle = 2,<br/>delivery-id=11, <br/>állapot = null)<br/>adattartalom|  
| törlése <br/> első = 11, utolsó = 11, <br/>State =**TransactionalState (<br/>tranzakció-ID = 0,<br/>eredmény = elfogadva ()** ))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Speciális Service Bus képességek

Ez a szakasz az Azure Service Bus fejlett képességeit mutatja be, amelyek a AMQP vázlatos bővítményein alapulnak, és jelenleg a AMQP-i oázis technikai bizottságban vannak kifejlesztve. Service Bus megvalósítja ezeknek a vázlatoknak a legújabb verzióit, és bevezeti azokat a módosításokat, amelyek a standard állapot eléréséhez szükségesek.

> [!NOTE]
> A Service Bus üzenetkezelés speciális műveletei a kérelem/válasz mintán keresztül támogatottak. Ezeknek a műveleteknek a részleteiről a [Service Bus AMQP 1,0-es cikkében, a kérelem-válasz alapú műveletek](service-bus-amqp-request-response.md)című cikkben olvashat.
> 
> 

### <a name="amqp-management"></a>AMQP-kezelés

A AMQP-felügyeleti specifikáció a jelen cikkben tárgyalt draft-bővítmények közül az első. Ez a specifikáció olyan protokollok készletét határozza meg, amelyek a AMQP protokollon felül vannak, amelyek lehetővé teszik a felügyeleti interakciót a AMQP-en keresztül az üzenetkezelési infrastruktúrával. A specifikáció általános műveleteket (például *Létrehozás*, *olvasás*, *frissítés*és *Törlés* ) határoz meg az üzenetkezelési infrastruktúrán belüli entitások és a lekérdezési műveletek készletének kezeléséhez.

Ezek a kézmozdulatok az ügyfél és az üzenetkezelési infrastruktúra közötti kérés/válasz interakciót igénylik, ezért a specifikáció meghatározza, hogyan modellezhető az interakciós minta a AMQP: az ügyfél csatlakozik az üzenetkezelési infrastruktúrához, elindítja a munkamenetet, majd létrehoz egy pár hivatkozást. Egy hivatkozáson az ügyfél feladóként működik, a másik pedig fogadóként viselkedik, így egy pár olyan hivatkozást hoz létre, amely kétirányú csatornaként működhet.

| Logikai művelet | Ügyfél | Service Bus |
| --- | --- | --- |
| Kérelemre adott válasz elérési útjának létrehozása |– > csatolás (<br/>név = {*hivatkozás neve*},<br/>Handle = {*numerikus leíró*},<br/>szerepkör =**Feladó**,<br/>forrás =**Null**,<br/>Target = "myentity/$management"<br/>) |Nincs művelet |
| Kérelemre adott válasz elérési útjának létrehozása |Nincs művelet |\<– csatolás (<br/>név = {*hivatkozás neve*},<br/>Handle = {*numerikus leíró*},<br/>szerepkör =**fogadó**,<br/>forrás = null,<br/>target=”myentity”<br/>) |
| Kérelemre adott válasz elérési útjának létrehozása |– > csatolás (<br/>név = {*hivatkozás neve*},<br/>Handle = {*numerikus leíró*},<br/>szerepkör =**fogadó**,<br/>forrás = "myentity/$management",<br/>target=”myclient$id”<br/>) | |
| Kérelemre adott válasz elérési útjának létrehozása |Nincs művelet |\<– csatolás (<br/>név = {*hivatkozás neve*},<br/>Handle = {*numerikus leíró*},<br/>szerepkör =**Feladó**,<br/>forrás = "myentity",<br/>target=”myclient$id”<br/>) |

Ennek a hivatkozásnak a helyén a kérelem/válasz implementáció egyszerű: a kérelem az üzenetküldési infrastruktúrán belüli, a mintát értelmező entitásnak küldött üzenet. Abban az esetben, ha a Request-Message ( *Tulajdonságok* ) szakaszban a *Válasz címzettje* mező arra a *cél* azonosítóra van beállítva, amelyre a választ kézbesíteni kívánja. A kezelő entitás feldolgozza a kérést, majd továbbítja a választ a hivatkozáson keresztül, amelynek a *célként* megadott azonosítója megegyezik a jelzett *Válasz-* azonosítóval.

A minta nyilvánvalóan megköveteli, hogy az ügyfél tárolója és a válasz céljának ügyfél által generált azonosítója egyedi legyen az összes ügyfélnél, és biztonsági okokból is nehéz megjósolni.

A felügyeleti protokollhoz és az összes többi olyan protokollhoz használt üzenetváltás, amely ugyanazt a mintát használja az alkalmazás szintjén; nem határozzák meg az új AMQP protokoll szintű kézmozdulatokat. Ez szándékos, így az alkalmazások azonnal kihasználhatják ezeket a bővítményeket a megfelelő AMQP 1,0-veremmel.

Service Bus jelenleg nem valósítja meg a felügyeleti specifikáció alapvető funkcióit, de a felügyeleti specifikáció által meghatározott kérelem/válasz minta alapja a jogcímek biztonsági funkciója, és csaknem az összes, a következő szakaszokban tárgyalt fejlett képesség:

### <a name="claims-based-authorization"></a>Jogcím-alapú hitelesítés

A AMQP jogcím-alapú engedélyezési (CBS) specifikációjának vázlata a felügyeleti specifikáció kérés/válasz mintáján alapul, és általános modellt ismertet az összevont biztonsági jogkivonatok AMQP-vel való használatáról.

A bevezetésben tárgyalt AMQP alapértelmezett biztonsági modellje a SASL-on alapul, és integrálható a AMQP-kapcsolatok kézfogásával. A SASL használata azzal az előnnyel jár, hogy olyan bővíthető modellt biztosít, amelyhez olyan mechanizmusok vannak meghatározva, amelyekben bármely, a SASL-ra hivatalosan támaszkodó protokoll kihasználható. Ezen mechanizmusok között "egyszerű" a felhasználónevek és a jelszavak átvitele, a "külső" a TLS-szintű biztonsághoz való kötéshez, "névtelen", hogy kifejezze a explicit hitelesítés/engedélyezés hiányát, valamint számos további mechanizmust, amelyek lehetővé teszik a hitelesítés és/vagy az engedélyezési hitelesítő adatok vagy tokenek továbbítását.

A AMQP SASL-integrációja két hátránya van:

* Minden hitelesítő adat és jogkivonat hatóköre a kapcsolatra terjed ki. Az üzenetkezelési infrastruktúra eltérő hozzáférés-vezérlést biztosíthat az egyes entitások alapján; Tegyük fel például, hogy egy jogkivonat tulajdonosának az A várólistára kell küldenie, de a B várólistára nem. A kapcsolaton rögzített engedélyezési környezettel nem lehet egyetlen kapcsolatot használni, de az A és A B üzenetsor esetében eltérő hozzáférési tokeneket is használhat.
* A hozzáférési jogkivonatok jellemzően csak korlátozott ideig érvényesek. Ez az érvényesség megköveteli, hogy a felhasználó rendszeresen újraszerezze a jogkivonatokat, és lehetőséget biztosít a jogkivonat kiállítójának, hogy megtagadja a friss token kiküldését, ha a felhasználó hozzáférési engedélyei megváltoztak. A AMQP-kapcsolatok hosszú ideig tarthatnak. A SASL-modell csak a jogkivonat beállítását biztosítja a kapcsolat időpontjában, ami azt jelenti, hogy az üzenetkezelési infrastruktúrának le kell bontania az ügyfelet a jogkivonat lejárata után, vagy el kell fogadnia annak kockázatát, hogy továbbra is kommunikáljon egy olyan ügyféllel, amely Előfordulhat, hogy a hozzáférési jogosultságokat az átmeneti időszakra visszavonták.

A Service Bus által megvalósított AMQP CBS-specifikáció lehetővé teszi a két probléma elegáns megkerülő megoldását: lehetővé teszi, hogy az ügyfél a hozzáférési jogkivonatokat társítsa az egyes csomópontokhoz, és a jogkivonatokat a lejáratuk előtt frissítse, anélkül, hogy az üzenet áramlását megszakítja.

A CBS definiál egy *$CBS*nevű virtuális felügyeleti csomópontot, amelyet az üzenetkezelési infrastruktúra biztosít. A felügyeleti csomópont az üzenetkezelési infrastruktúra bármely más csomópontjának nevében fogadja el a jogkivonatokat.

A protokoll-kézmozdulat a felügyeleti specifikáció által meghatározott kérelem/válasz típusú Exchange. Ez azt jelenti, hogy az ügyfél összekapcsolja az *$CBS* csomópontot, majd átadja a kérést a kimenő hivatkozáson, majd megvárja a választ a bejövő hivatkozáson.

A kérelem üzenete a következő alkalmazás-tulajdonságokkal rendelkezik:

| Paraméter | Optional | Érték típusa | Érték tartalma |
| --- | --- | --- | --- |
| operation |Nem |sztring |**Put-token** |
| type |Nem |sztring |A felhelyezni kívánt jogkivonat típusa. |
| név |Nem |sztring |A "hallgatóság", amelyre a jogkivonat vonatkozik. |
| Lejárati |Igen |időbélyeg |A jogkivonat lejárati ideje. |

A *Name (név* ) tulajdonság azonosítja azt az entitást, amelyhez a token társítva van. Service Bus a várólista elérési útja, vagy témakör/előfizetés. A *Type* tulajdonság azonosítja a jogkivonat típusát:

| Jogkivonat típusa | Jogkivonat leírása | Törzs típusa | Megjegyzések |
| --- | --- | --- | --- |
| amqp: JWT |JSON Web Token (JWT) |AMQP érték (karakterlánc) |Még nem érhető el. |
| amqp: SWT |Egyszerű webes jogkivonat (SWT) |AMQP érték (karakterlánc) |Csak a HRE/ACS által kiállított SWT-tokenek esetében támogatott |
| servicebus.windows.net:sastoken |SAS-token Service Bus |AMQP érték (karakterlánc) |- |

Tokenek ruházza fel a jogosultságokat. A Service Bus három alapvető jogosultsággal rendelkezik: a Küldés lehetővé teszi a küldést, a "Figyelés" lehetőséget, és a "kezelés" lehetővé teszi az entitások kezelését. A HRE/ACS által kiállított SWT-tokenek explicit módon tartalmazzák a jogcímeket. Service Bus SAS-tokenek a névtéren vagy az entitáson konfigurált szabályokra vonatkoznak, és ezek a szabályok jogosultságokkal vannak konfigurálva. Ha aláírja a jogkivonatot a szabályhoz társított kulccsal, így a token kifejezi a megfelelő jogosultságokat. A *put-tokent* használó entitáshoz társított jogkivonat lehetővé teszi, hogy a csatlakoztatott ügyfél a jogkivonat-jogosultságok alapján kommunikáljon az entitással. Egy hivatkozás, amelyben az ügyfél a *küldő* szerepkört igényli, a "Küldés" jogosultság szükséges. a *fogadó* szerepkörnek a "Listen" jogosultsággal kell rendelkeznie.

A válaszüzenet a következő *alkalmazás-tulajdonságok* értékekkel rendelkezik

| Paraméter | Optional | Érték típusa | Érték tartalma |
| --- | --- | --- | --- |
| állapot kódja |Nem |int |HTTP-válasz kódja **[RFC2616]** . |
| status-description |Igen |sztring |Az állapot leírása. |

Az ügyfél többször is meghívhatja a *put-tokent* , illetve az üzenetkezelési infrastruktúra bármely entitására. A jogkivonatok hatóköre az aktuális ügyfél, és az aktuális kapcsolatra van rögzítve, ami azt jelenti, hogy a kiszolgáló eldobja a megőrzött jogkivonatokat, amikor a kapcsolat megszakad.

A jelenlegi Service Bus implementáció csak a "névtelen" SASL metódussal együtt engedélyezi a CBS használatát. Az SASL-kézfogás előtt mindig léteznie kell egy SSL/TLS-kapcsolatnak.

A névtelen mechanizmust ezért a kiválasztott AMQP 1,0-ügyfélnek kell támogatnia. A névtelen hozzáférés azt jelenti, hogy a kezdeti kapcsolati kézfogás, beleértve a kezdeti munkamenet létrehozását is, Service Bus nem tudja, hogy ki hozza létre a kapcsolatot.

A kapcsolat és a munkamenet létrejötte után csatolja az *$CBS* csomópontra mutató hivatkozásokat, és a *put-token* kérés küldését az egyetlen engedélyezett művelet. Egy érvényes jogkivonatot a kapcsolat létrejötte után 20 másodpercen belül egy *put-jogkivonat* -kérelemmel kell beállítani, ha a kapcsolat létrejött, de a kapcsolat egyoldalúan eldobásra kerül a Service Bus használatával.

Az ügyfél ezt követően felelős a jogkivonat lejáratának nyomon követéséhez. Ha egy jogkivonat lejár, Service Bus azonnal elveszíti a kapcsolat összes hivatkozását a megfelelő entitáshoz. A probléma megelőzése érdekében az ügyfél bármikor lecserélheti a csomóponthoz tartozó jogkivonatot egy újat a virtuális *$CBS* felügyeleti csomóponton, ugyanazzal az *üzembe helyezett jogkivonat-* kézmozdulattal, és anélkül, hogy a különböző hivatkozásokon futó hasznos adatforgalomhoz kellene fordulnia.

### <a name="send-via-functionality"></a>Küldési funkciók

A [küldési/továbbítási küldő](service-bus-transactions.md#transfers-and-send-via) olyan funkció, amely lehetővé teszi, hogy a Service Bus egy adott üzenetet továbbítson egy cél entitásnak egy másik entitáson keresztül. Ezzel a funkcióval egyetlen tranzakcióban végezheti el az entitások közötti műveleteket.

Ezzel a funkcióval létre kell hoznia egy küldőt, és létre kell hoznia a `via-entity`re mutató hivatkozást. A hivatkozás létrehozása közben további információk is átkerülnek a hivatkozáson található üzenetek/átvitelek valódi céljának meghatározásához. Miután a csatolás sikeres volt, a hivatkozáson küldött összes üzenet automatikusan továbbítva lesz a *cél – entitás* felé a következőn keresztül: *entitás*. 

> Megjegyzés: a hivatkozás létrehozása előtt el kell indítani a hitelesítést mind az *entitások* , mind a *rendeltetési entitás* számára.

| Ügyfél | | Service Bus |
| --- | --- | --- |
| csatolja<br/>név = {hivatkozás neve},<br/>szerepkör = feladó,<br/>forrás = {ügyfél-hivatkozási azonosító},<br/>Target = **{on-Entity}** ,<br/>**Properties = Map [(<br/>com. Microsoft: átvitel-cél-címe =<br/>{Destination-Entity})]** ) | ------> | |
| | <------ | csatolja<br/>név = {hivatkozás neve},<br/>szerepkör = fogadó,<br/>forrás = {ügyfél-hivatkozási azonosító},<br/>target={via-entity},<br/>tulajdonságok = Térkép [(<br/>com.microsoft:transfer-destination-address=<br/>{cél-entitás})] ) |

## <a name="next-steps"></a>Következő lépések

A AMQP kapcsolatos további információkért tekintse meg a következő hivatkozásokat:

* [Service Bus AMQP áttekintése]
* [AMQP 1,0 támogatás Service Bus particionált várólisták és témakörök számára]
* [A Windows Server Service Bus AMQP]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[AMQP 1,0 támogatás Service Bus particionált várólisták és témakörök számára]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[A Windows Server Service Bus AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
