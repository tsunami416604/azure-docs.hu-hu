# <a name="azure-service-bus"></a>Azure Service Bus
Függetlenül attól, hogy egy alkalmazás vagy szolgáltatás a felhőben vagy a helyszínen fut, gyakran kell más alkalmazásokkal vagy szolgáltatásokkal interakcióba lépnie. Széles körben használható megoldást ennek biztosításához az Azure Service Bus révén kínál. Ez a cikk ezt a technológiát mutatja be, ismerteti, hogy mi is ez pontosan, és miért érdemes használnia.

## <a name="service-bus-fundamentals"></a>Service Bus fundamentals (A Service Bus alapjai)
A különféle helyzetekben különféle stílusú kommunikáció lehet szükséges. Néha az a legjobb megoldás, ha az alkalmazások egy egyszerű üzenetsoron keresztül küldik és fogadják az üzeneteket. Más helyzetekben a hagyományos üzenetsorok nem elegendőek, és a közzétételi-előfizetési mechanizmus a jobb megoldás. És néhány esetben szükség egy kapcsolatot az alkalmazások &#151;szükség üzenetsorokra. A Service Bus mindhárom lehetőséget biztosítja, ha engedélyezi, hogy az alkalmazások számos különböző módon működjön.

A Service Bus egy több-bérlős felhőszolgáltatás, ami azt jelenti, hogy a szolgáltatást több felhasználó megosztva használja. Minden felhasználó, például az alkalmazásfejlesztő, létrehoz egy *névteret*, amely definiálja a szükséges kommunikációs mechanizmusokat az adott névtérben. [1. ábra](#Fig1) ezt mutatja be.

<a name="Fig1"></a>![Az Azure Service Bus diagramja][svc-bus]

**1. ábra: A Service Bus egy több-bérlős szolgáltatást kínál az alkalmazások összekapcsolására a felhőn keresztül.**

Egy adott névtéren belül a négy különböző kommunikációs mechanizmus egy vagy több példányát használhatja, amelyek mindegyike különböző módokon kapcsol össze alkalmazásokat. Az alábbiak közül választhat:

* *Üzenetsorok*, amelyek egyirányú kommunikációt tesznek lehetővé. Az egyes üzenetsorok *közvetítőként* működnek, amely az üzeneteket tárolja a fogadásukig. Mindegyik üzenetet egyetlen fogadó fogadja.
* *Témakörök*, amelyek egyirányú kommunikációt tesznek lehetővé *előfizetések* segítségével – egy témakör több előfizetéssel is rendelkezhet. Az üzenetsorokhoz hasonlóan a témakörök is közvetítőként szolgálnak, azonban az egyes előfizetések szűrőt használhatnak, amelynek révén csak az adott feltételeknek megfelelő üzenetek fogadása történik meg.
* *Továbbítók*, amelyek kétirányú kommunikációt tesznek lehetővé. Az üzenetsoroktól és témaköröktől eltérően a továbbító nem tárolja átvitel közben az üzeneteket – nem közvetítő. Ehelyett egyszerűen továbbítja azokat a célalkalmazás számára.
* *Eseményközpontok*, amelyek a felhőbe irányuló, nagy léptékű esemény- és telemetriabevitelt biztosítanak alacsony késéssel és nagy megbízhatósággal.

Amikor létrehoz egy üzenetsort, témakört, továbbítót vagy eseményközpontot, el kell neveznie azt. A névtér nevével kombinálva így egyedi azonosítót hoz létre az objektum számára. Az alkalmazások megadhatják ezt a nevet a Service Bus számára, majd az adott üzenetsor, témakör, továbbító vagy eseményközpont használatával kommunikálhatnak egymással. 

Ezen objektumok valamelyikének a használatához a Windows-alkalmazások a Windows Communication Foundation (WCF) szolgáltatást használhatják. Az üzenetsorok, témakörök és eseményközpontok esetében a Windows-alkalmazások a Service Bus által definiált üzenettovábbítási API-kat is használhatják. Az objektumok nem Windows-alkalmazásokból való használatának egyszerűbbé tétele érdekében a Microsoft SDK-kat biztosít a Java, Node.js és egyéb nyelvekhez. Az üzenetsorokat, témaköröket és eseményközpontokat REST API-k használatával is elérheti HTTP-n keresztül. 

Fontos megérteni, hogy jóllehet a Service Bus maga a felhőben fut (azaz a Microsoft Azure adatközpontjaiban), a szolgáltatást igénybe vevő alkalmazások bárhol futhatnak. A Service Bus használatával összekapcsolhat például az Azure-ban vagy a saját adatközpontjában futó alkalmazásokat. A szolgáltatással összekapcsolhat az Azure-ban vagy más felhőplatformon futó alkalmazást egy helyszíni alkalmazással vagy táblagépekkel és telefonokkal is. Akár háztartási készülékeket, érzékelőket és egyéb eszközöket is csatlakoztathat egy központi alkalmazáshoz vagy egymáshoz. A Service Bus egy általános kommunikációs mechanizmus a felhőben, amely lényegében bárhonnan elérhető. A használatának módja attól függ, hogy milyen célt szolgálnak az alkalmazások.

## <a name="queues"></a>Üzenetsorok
Tegyük fel, hogy két alkalmazás egy Service Bus-üzenetsorral való csatlakoztatása mellett dönt. [2. ábra](#Fig2) ezt a helyzetet mutatja be.

<a name="Fig2"></a>![Service Bus-üzenetsorok ábrája][queues]

**2. ábra: A Service Bus-üzenetsorok egyirányú aszinkron sorkezelést biztosítanak.**

A folyamat egyszerű: A küldő egy üzenetet küld a Service Bus-üzenetsorba, a fogadó pedig egy későbbi időpontban fogadja az üzenetet. Egyes üzenetsorok rendelkezhetnek egyetlen fogadóval, mint a [2. ábra](#Fig2) mutat be, vagy több alkalmazást is olvasható ugyanazon a várólistán. Az utóbbi esetben az egyes üzeneteket csak egyetlen fogadó olvassa – csoportos küldési szolgáltatáshoz inkább témakört kell használnia.

Mindegyik üzenet két részből áll: egy sor tulajdonságból, amelyek mindegyike egy kulcs/érték pár, valamint egy bináris üzenettörzsből. A használatuk módja attól függ, hogy mire szolgál az alkalmazás. A legutóbbi értékesítésről üzenetet küldő alkalmazás például az *Értékesítő="Ava"* és az *Összeg=10000* tulajdonságot tartalmazhatja. Az üzenettörzs tartalmazhatja az aláírt értékesítési szerződés beolvasott képét vagy, ha nincs ilyen, maradhat üresen.

A fogadó kétféleképpen olvashatja az üzeneteket a Service Bus-üzenetsorból. Az első lehetőség, a *ReceiveAndDelete*, eltávolítja az üzenetet a sorból, és azonnal törli. Ez egyszerű, ha azonban a fogadó összeomlik, mielőtt végzett az üzenet feldolgozásával, az üzenet elveszik. Mivel már el lett távolítva az üzenetsorból, más fogadó nem férhet hozzá. 

A második lehetőség, a *PeekLock*, ezt a problémát hivatott kiküszöbölni. ReceiveAndDelete, például egy PeekLock olvasási eltávolítja az üzenetet az üzenetsorból. Nem törli azonban az üzenetet. Helyette zárolja, és a többi fogadó számára láthatatlanná teszi, majd vár az alábbi három esemény valamelyikének a bekövetkezésére:

* Ha a fogadó sikeresen feldolgozza az üzenetet, meghívja a *Complete* metódust, és az üzenetsor törli az üzenetet. 
* Ha a fogadó úgy dönt, hogy nem tudja sikeresen feldolgozni az üzenetet, meghívja az *Abandon* metódust. Az üzenetsor ekkor eltávolítja a zárolást az üzenetről, és elérhetővé teszi a többi fogadó számára.
* Ha a fogadó egyik metódust sem hívja meg a konfigurált időtartamon belül (alapértelmezés szerint 60 másodperc), az üzenetsor feltételezi, hogy a fogadó meghibásodott. Ebben az esetben úgy viselkedik, mintha a fogadó meghívta volna Szolgáltatásműveletnek elérhetővé teszi az üzenetet a többi fogadó.

Figyelje meg, mi történhet ebben az esetben: Ugyanazon üzenet kézbesítése kétszer is megtörténhet, akár két különböző fogadónak is. A Service Bus-üzenetsorokat használó alkalmazásokat fel kell készíteni erre az esetre. Könnyebben kettős észlelés, mindegyik üzenet rendelkezik egy egyedi MessageID tulajdonságot, amelynek alapértelmezés szerint ugyanaz, függetlenül attól, hogy hány alkalommal marad az adott üzenet olvasása az üzenetsorból. 

Az üzenetsorok számos helyzetben lehetnek hasznosak. A használatukkal az alkalmazások akkor is kommunikálhatnak, ha nem egy időben futnak, ami különösen a kötegelt és a mobilalkalmazások esetén praktikus. A több fogadóval rendelkező üzenetsorok emellett automatikus terheléselosztást is biztosítanak, mivel a küldött üzenetek megoszlanak a fogadók között.

## <a name="topics"></a>Témakörök
Bármennyire hasznosak is, az üzenetsorok nem minden esetben bizonyulnak a megfelelő megoldásnak. Esetenként célszerűbb Service Bus-témaköröket használni. [3. ábra](#Fig3) ezt az elképzelést mutatja be.

<a name="Fig3"></a>![Service Bus-Üzenettémák és előfizetések ábrája][topics-subs]

**3. ábra: Az előfizető alkalmazás az általa megadott szűrő alapján a Service Bus-témakörbe küldött üzenetek közül csak néhányat vagy mindegyiket fogadhatja.**

A témakörök sok szempontból hasonlóak az üzenetsorokhoz. A küldők ugyanúgy küldik az üzeneteket a témakörökbe, ahogy az üzenetsorokba, és az üzenetek is ugyanúgy néznek ki, mint az üzenetsorok esetén. A nagy különbség az, hogy a témakörök mindegyik fogadó alkalmazás létrehozhatja a saját előfizetését megadásával lehetővé teszik egy *szűrő*. Az előfizető csak azokat az üzeneteket látja majd, amelyek megfelelnek a szűrőnek. Például [3. ábra](#Fig3) jeleníti meg a küldő és a témakör a mely előfizetők mindegyike saját szűrővel:

* Az 1. előfizető csak azokat az üzeneteket fogadja, amelyek tartalmazzák az *Értékesítő="Ava"* tulajdonságot.
* Az 2. előfizető csak azokat az üzeneteket fogadja, amelyek tartalmazzák az *Értékesítő="Ruby"* tulajdonságot és/vagy tartalmaznak egy *Összeg* tulajdonságot, amelynek az értéke nagyobb mint 100 000. Ruby lehet, hogy az értékesítési igazgató, és így szeretné tudni, mind a saját értékesítéseit, és a összes nagy értékesítési személyétől függetlenül.
* A 3. előfizető a szűrőt *Igaz* értékűre állította, ami azt jelenti, hogy minden üzenetet megkap. Ez az alkalmazás például a naplózásért lehet felelős, és ezért minden üzenetet meg kell kapnia.

Az üzenetsorok esetén a témakörök előfizetői olvashatja ReceiveAndDelete vagy PeekLock használatával. Az üzenetsoroktól eltérően azonban a témakörökbe küldött egyes üzeneteket több előfizető is fogadhatja. Ezt a módszert, más néven *közzététel és előfizetés*, akkor hasznos, amikor több alkalmazás előfordulhat, hogy érdeklődik ugyanazon üzenetek. A megfelelő szűrő meghatározásával mindegyik előfizető az üzenetfolyamnak csak azon részét látja, amelyet látnia kell.

## <a name="relays"></a>Továbbítók
Az üzenetsorok és a témakörök egyaránt egyirányú aszinkron kommunikációt tesznek lehetővé egy közvetítőn keresztül. A forgalom csak egy irányban folyik, és nincs közvetlen kapcsolat a küldők és a fogadók közt. De mi van, ha nem ezt szeretné? Tegyük fel, hogy az alkalmazásainak küldenie és fogadnia is kell üzeneteket, vagy talán közvetlen kapcsolatot szeretne közöttük, és nincs szüksége közvetítőre az üzenetek tárolásához. Az ilyen forgatókönyvek kezeléséhez, a Service Bus továbbítókat biztosít, mint a [4. ábra](#Fig4) jeleníti meg.

<a name="Fig4"></a>![Service Bus Relay ábrája][relay]

**4. ábra: A Service Bus Relay kétirányú szinkron kommunikációt tesz lehetővé az alkalmazások között.**

A továbbítókkal kapcsolatban felmerül a nyilvánvaló kérdés: Miért használnék ilyet? Annak ellenére, hogy nincs szükségem üzenetsorokra, miért kommunikáljanak az alkalmazások egy felhőszolgáltatáson keresztül a közvetlen interakció helyett? A válasz az, hogy a közvetlen kommunikáció néha nehezebb, mint gondolná.

Tegyük fel, hogy két helyszíni alkalmazást kíván összekapcsolni, és mindkettő vállalati adatközpontban fut. Mindkét alkalmazás egy tűzfal mögött található, és mindkét adatközpont valószínűleg hálózati címfordítást (NAT) használ. A tűzfal néhány port kivételével az összes porton blokkolja a bejövő adatokat, és a NAT miatt az a gép, amelyen az egyes alkalmazások futnak, nem rendelkezik rögzített IP-címmel, amelyet közvetlenül elérhetne az adatközponton kívülről. Külön segítség nélkül ezeknek az alkalmazásoknak az összekapcsolása a nyilvános interneten keresztül problémás lehet.

A Service Bus-továbbító biztosítja ezt a segítséget. A továbbítón keresztüli kétirányú kommunikációhoz mindegyik alkalmazás létrehoz egy kimenő TCP-kapcsolatot a Service Busszal, és nyitva tartja azt. A két alkalmazás közötti minden kommunikáció ezeken a kapcsolatokon keresztül történik. Mivel mindegyik kapcsolat az adatközponton belül lett létrehozva, a tűzfal engedélyezni fogja a bejövő forgalmat mindegyik alkalmazás felé új portok megnyitása nélkül. Ez a megközelítés megkerüli a NAT miatti problémát, mivel mindegyik alkalmazás állandó végponttal rendelkezik a felhőben a kommunikáció során. Az adatoknak a továbbítón keresztüli cseréjével az alkalmazások elkerülhetik azokat a problémákat, amelyek egyébként megnehezítenék a kommunikációt. 

A Service Bus-továbbítók használatához az alkalmazások a Windows Communication Foundation (WCF) függ. A Service Bus WCF-kötéseket biztosít, amelyek segítségével a Windows-alkalmazások egyszerűen kommunikálhatnak a továbbítókon keresztül. Azon alkalmazásoknak, amelyek már használják a WCF szolgáltatást, általában elegendő megadniuk az egyik ilyen kötést ahhoz, hogy kommunikálhassanak egymással továbbítón keresztül. Az üzenetsoroktól és témaköröktől eltérően a továbbítók használata nem Windows alkalmazásokból – bár lehetséges – némi programozást igényel, mivel nem állnak rendelkezésre szabványos könyvtárak.

Az üzenetsoroktól és témaköröktől eltérően az alkalmazások nem hoznak létre kifejezetten továbbítókat. Ehelyett, amikor egy üzeneteket fogadni szándékozó alkalmazás TCP-kapcsolatot létesít a Service Busszal, a rendszer automatikusan létrehoz egy továbbítót. A kapcsolat megszakadásakor a rendszer törli a továbbítót. Ahhoz, hogy egy alkalmazás, egy adott hallgató által létrehozott továbbítót található, a Service Bus biztosít egy beállításjegyzéket, amely lehetővé teszi az alkalmazások név alapján megtalálhatják kereséséhez.

A továbbító a megfelelő megoldás, ha közvetlen kommunikációra van szükség az alkalmazások között. Vegyük például egy légitársaság foglalási rendszerét, amely egy olyan helyszíni adatközpontban fut, amelynek elérhetőnek kell lennie bejelentkezési pultokról, mobileszközökről és egyéb számítógépekről. Az ezeken a rendszereken futó alkalmazások Service Bus-továbbítókat használhatnak a felhőben a kommunikációhoz, függetlenül attól, hogy hol futnak.

## <a name="event-hubs"></a>Event Hubs
Az Event Hubs egy kiválóan méretezhető fogadórendszer, amely másodpercenként több millió esemény feldolgozására képes, így az alkalmazás a csatlakoztatott eszközök és alkalmazások által létrehozott nagy mennyiségű adatot egyszerűen feldolgozhatja és elemezheti. Egy eseményközpont használható például egy járműflotta élő motorteljesítmény-adatainak gyűjtésére. Az adatoknak az Event Hubs szolgáltatásban való összegyűjtését követően, az adatokat átalakíthatja és tárolhatja bármilyen valós idejű elemzési szolgáltató vagy tárolási fürt használatával. Az Event Hubs kapcsolatos további információkért tekintse meg a [Event Hubs – áttekintés][Event Hubs overview].

## <a name="summary"></a>Összefoglalás
Az alkalmazások összekapcsolása mindig is részét képezte a teljes megoldások kialakításának, és az olyan forgatókönyvek száma, amelyekben az alkalmazásoknak és szolgáltatásoknak kommunikálniuk kell egymással, óhatatlanul nőni fog, ahogy egyre több alkalmazás és eszköz csatlakozik az internetre. A Service Bus célja az, hogy ezt az alapvető fontosságú funkciót könnyebben megvalósíthatóvá és szélesebb körben elérhetővé tegye, felhőalapú technológiákat biztosítva ehhez az üzenetsorok, témakörök, továbbítók és eseményközpontok révén.

[svc-bus]: ./media/hybrid-solutions/SvcBus_01_architecture.png
[queues]: ./media/hybrid-solutions/SvcBus_02_queues.png
[topics-subs]: ./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[relay]: ./media/hybrid-solutions/SvcBus_04_relay.png
[Event Hubs overview]: https://msdn.microsoft.com/library/azure/dn836025.aspx
