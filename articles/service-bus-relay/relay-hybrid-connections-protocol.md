---
title: Az Azure Relay hybrid connections protokoll útmutatója | Microsoft dokumentumok
description: Ez a cikk ismerteti az ügyféloldali interakciók a hibrid kapcsolatok továbbító ügyfelek csatlakoztatása figyelő és küldő szerepkörökben.
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: ''
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: clemensv
ms.openlocfilehash: 68668452152064584d1c419a3053ccb642b103f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514952"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay hibrid kapcsolatok protokoll

Az Azure Relay az Azure Service Bus platform egyik legfontosabb képességpillére. A Relay új _hibrid kapcsolatok_ funkciója egy biztonságos, nyílt protokollalapú fejlesztés, amely HTTP- és WebSockets-alapú. Ez hatályon kívül helyezi az előbbi, egyhasonlóan nevű _BizTalk Services_ funkció, amely egy saját protokoll alapja. A hibrid kapcsolatok integrálása az Azure App Services továbbra is működik, ahogy van.

A hibrid kapcsolatok kétirányú, bináris adatfolyam-kommunikációt és egyszerű datagram-áramlást tesznek lehetővé két hálózati alkalmazás között. Az egyik vagy mindkét fél nats vagy tűzfal mögött tartózkodhat.

Ez a cikk ismerteti az ügyféloldali interakciók a hibrid kapcsolatok továbbító ügyfelek csatlakoztatása figyelő és küldő szerepkörökben. Azt is ismerteti, hogyan fogadja a figyelők az új kapcsolatokat és kéréseket.

## <a name="interaction-model"></a>Interakciós modell

A hibrid kapcsolatok továbbítása két felet kapcsol össze azáltal, hogy egy olyan találkozási pontot biztosít az Azure-felhőben, amelyet a felek saját hálózatuk szemszögéből fedezhetnek fel és csatlakozhatnak. A találkozási pont neve "hibrid kapcsolat" ebben és más dokumentációban, az API-kban, valamint az Azure Portalon. A hibrid kapcsolatok szolgáltatás végpontja a cikk többi részében "szolgáltatás".The Hybrid Connections service endpoint is target is a "service" for the rest of this article.

A szolgáltatás lehetővé teszi a websocket-kapcsolatok és a HTTP(S) kérelmek és válaszok továbbítását.

Az interakciós modell a számos más hálózati API által létrehozott nómenklatúrára támaszkodik. Van egy figyelő, amely először azt jelzi, hogy készen áll a bejövő kapcsolatok kezelésére, majd elfogadja őket, amikor megérkeznek. A másik oldalon egy ügyfél csatlakozik a figyelő, arra számítva, hogy a kapcsolat elfogadható a kétirányú kommunikációs útvonal létrehozásához. A "Csatlakozás", a "Figyelés" és az "Elfogadás" kifejezés a legtöbb szoftvercsatorna API-ban megtalálható kifejezés.

Bármely továbbított kommunikációs modell bármelyik fél kimenő kapcsolatot létesít egy szolgáltatásvégpont felé. Ez teszi a "hallgató" is egy "ügyfél" a köznyelvi használatra, és azt is okozhat más terminológia túlterhelés. A hibrid kapcsolatokra ezért használt pontos terminológia a következő:

A kapcsolat mindkét oldalán lévő programokat "ügyfeleknek" nevezzük, mivel a szolgáltatás ügyfelei. Az ügyfél, amely megvárja és elfogadja a kapcsolatokat a "figyelő", vagy azt mondják, hogy a "figyelő szerepet." Az ügyfél, amely új kapcsolatot kezdeményez a figyelővel a szolgáltatáson keresztül az úgynevezett "feladó", vagy a "feladó szerepkörben".

### <a name="listener-interactions"></a>Figyelőinterakciók

A figyelő öt interakciót a szolgáltatással; minden vezeték részleteit ismertetik később ezt a cikket a referencia szakaszban.

A figyelési, elfogadási és kérésüzenetek a szolgáltatástól érkeznek. A megújítási és pingelési műveleteket a figyelő küldi.

#### <a name="listen-message"></a>Üzenet meghallgatása

Annak jelzésére, hogy készen áll a szolgáltatás, amely a figyelő készen áll a kapcsolatok fogadására, létrehoz egy kimenő WebSocket-kapcsolatot. A kapcsolati kézfogás a továbbító névtéren konfigurált hibrid kapcsolat nevét, valamint egy olyan biztonsági jogkivonatot hordoz, amely a "Listen" jogot adja erre a névre.

Amikor a szolgáltatás elfogadja a WebSocket-t, a regisztráció befejeződik, és a létrehozott WebSocket marad életben, mint a "vezérlőcsatorna", amely lehetővé teszi az összes további interakciók engedélyezéséhez. A szolgáltatás lehetővé teszi, hogy akár 25 egyidejű figyelők egy hibrid kapcsolat. Az AppHooks kvótáját meg kell határozni.

Hibrid kapcsolatok esetén, ha két vagy több aktív figyelők, bejövő kapcsolatok között véletlenszerű sorrendben vannak kiegyenlítve; a tisztességes elosztást a legjobb erőfeszítéssel kísérelik meg.

#### <a name="accept-message"></a>Üzenet elfogadása

Amikor egy feladó új kapcsolatot nyit meg a szolgáltatáson, a szolgáltatás kiválasztja és értesíti a hibrid kapcsolat egyik aktív figyelőjét. Ezt az értesítést a figyelő a nyílt vezérlőcsatornán keresztül JSON-üzenetként küldi el. Az üzenet tartalmazza annak a WebSocket végpontnak az URL-címét, amelyhez a figyelőnek csatlakoznia kell a kapcsolat elfogadásához.

Az URL-t közvetlenül a hallgató használhatja és kell használnia további munka nélkül.
A kódolt információ csak rövid ideig érvényes, lényegében addig, amíg a feladó hajlandó megvárni a kapcsolat végponttól végpontig való létrejöttét. A maximális feltételezés 30 másodperc. Az URL-cím csak egy sikeres csatlakozási kísérlethez használható. Amint létrejön a WebSocket-kapcsolat a randevú URL-címével, a WebSocket minden további tevékenysége továbbításra kerül a feladótól és a feladóhoz. Ez a szolgáltatás beavatkozása vagy értelmezése nélkül történik.

### <a name="request-message"></a>Üzenet kérése

A WebSocket-kapcsolatok mellett a figyelő http-kérelemkereteket is fogadhat egy feladótól, ha ez a képesség explicit módon engedélyezve van a hibrid kapcsolaton.

A HTTP-támogatással rendelkező hibrid kapcsolatokhoz kapcsolódó figyelőknek kezelniük kell a `request` kézmozdulatot. A figyelő, amely nem `request` kezeli, és ezért okoz ismétlődő időtúllépésre hibák, miközben csatlakozik lehet feketelistára a szolgáltatás a jövőben.

A HTTP-keretfejléc metaadatai jsonná alakítják le a figyelő keretrendszer általi egyszerűbb kezelhetőséget, mivel a HTTP-fejlécelemző könyvtárak ritkábbak, mint a JSON-elemzők. A http-metaadatok, amelyek csak a feladó és a továbbító HTTP-átjáró közötti kapcsolat szempontjából relevánsak, beleértve az engedélyezési adatokat is, nem lesznek továbbítva. A HTTP-kérelemkezelő kontőket a rendszer transzparens módon, bináris WebSocket-keretként továbbítja.

A figyelő egy egyenértékű válaszmozdulattal válaszolhat a HTTP-kérésekre.

A kérelem/válasz folyamat alapértelmezés szerint a vezérlőcsatornát használja, de szükség esetén "frissíthető" egy különálló randevú websocketre. A különböző WebSocket-kapcsolatok minden ügyfélbeszélgetés hez javítja az átviteli csatornázást, de a figyelőt több, kezelendő kapcsolattal terhelik, amelyek nem feltétlenül képesek a könnyű ügyfelek számára.

A vezérlőcsatornán a kérés- és válaszszervek mérete legfeljebb 64 kB. A HTTP-fejléc metaadatai összesen 32 kB-ra korlátozódnak. Ha a kérés vagy a válasz meghaladja ezt a küszöbértéket, a figyelőnek frissítenie kell egy randevúra WebSocket egy olyan kézmozdulattal, amely egyenértékű az [Accept kezeléssel.](#accept-message)

A kérelmek esetében a szolgáltatás dönti el, hogy a kérelmeket a vezérlőcsatornán keresztül továbbítja-e. Ez magában foglalja, de nem korlátozódhat azokra az esetekre, amikor a kérelem meghaladja a 64 kB-t (fejlécek és törzs) véglegesen, vagy ha a kérelmet ["darabolt" átviteli kódolással küldik,](https://tools.ietf.org/html/rfc7230#section-4.1) és a szolgáltatás nak oka van arra számítani, hogy a kérelem meghaladja a 64 kB-ot, vagy a kérés olvasása nem azonnali. Ha a szolgáltatás úgy dönt, hogy a kérelmet a randevú felett, csak átadja a randevú címét a figyelő.
A figyelő ezután létre kell hoznia a rendezvous WebSocket és a szolgáltatás azonnal kézbesíti a teljes kérelmet, beleértve a szervek et a rendezvous WebSocket. A válasznak a websocket randevút is használnia kell.

Az ellenőrző csatornán keresztül érkező kérések esetén a figyelő dönti el, hogy a vezérlőcsatornán keresztül vagy randevún keresztül válaszol-e. A szolgáltatásnak tartalmaznia kell egy randevú címet minden, a vezérlőcsatornán keresztül irányított kérelemmel. Ez a cím csak az aktuális kérésből való frissítésre érvényes.

Ha a figyelő úgy dönt, hogy frissít, csatlakozik, és azonnal kézbesíti a választ a létrehozott randevú aljzaton keresztül.

Miután a rendezvous WebSocket létrejött, a figyelő nek fenn kell tartania azt az ugyanazon ügyféltől érkező kérések és válaszok további kezeléséhez. A szolgáltatás mindaddig fenntartja a WebSocket szolgáltatást, amíg a feladóval létesített HTTPS-szoftvercsatorna-kapcsolat megmarad, és az adott feladótól érkező minden további kérést a karbantartott WebSocket-en keresztül továbbítja. Ha a figyelő úgy dönt, hogy a rendezvous WebSocket az oldalán, a szolgáltatás is megszakad a kapcsolatot a küldő, függetlenül attól, hogy egy későbbi kérelem már folyamatban van.

#### <a name="renew-operation"></a>Művelet megújítása

A figyelő regisztrálásához és a vezérlőcsatorna karbantartásához használandó biztonsági jogkivonat lejárhat, amíg a figyelő aktív. A jogkivonat lejárata nem befolyásolja a folyamatban lévő kapcsolatokat, de ez okozza a vezérlőcsatornát, hogy a szolgáltatás a lejárat pillanatában vagy nem sokkal azt követően. A "megújítási" művelet egy JSON-üzenet, amelyet a figyelő elküldhet a vezérlőcsatornához társított jogkivonat lecserélésére, hogy a vezérlőcsatorna hosszabb ideig karbantartható legyen.

#### <a name="ping-operation"></a>Pingelési művelet

Ha a vezérlőcsatorna hosszú ideig tétlen marad, az úton lévő közvetítők, például a terheléselosztók vagy a NAT-ok eldobhatják a TCP-kapcsolatot. A "ping" művelet elkerüli, hogy egy kis mennyiségű adatot küld a csatornán, amely emlékeztet mindenkit a hálózati útvonalon, hogy a kapcsolat célja, hogy életben van, és ez is szolgál, mint egy "élő" teszt a hallgató. Ha a ping sikertelen, a vezérlőcsatornát használhatatlannak kell tekinteni, és a figyelőnek újra kell csatlakoznia.

### <a name="sender-interaction"></a>Feladó itevékenysége

A feladó nak két kapcsolata van a szolgáltatással: webszoftvert köt, vagy https-en keresztül küld kéréseket. A kérelmek nem küldhetők websocketen a feladói szerepkörből.

#### <a name="connect-operation"></a>Csatlakoztatási művelet

A "connect" művelet megnyit egy WebSocket a szolgáltatás, amely a hibrid kapcsolat nevét, és (opcionálisan, de alapértelmezés szerint szükséges) a biztonsági jogkivonatot, amely "Küldés" engedélyt a lekérdezési karakterláncban. A szolgáltatás ezután a korábban leírt módon kommunikál a figyelővel, és a figyelő létrehoz egy randevú kapcsolatot, amely ehhez a WebSockethez csatlakozik. A WebSocket elfogadása után az adott WebSocket minden további interakciója egy csatlakoztatott figyelővel van.

#### <a name="request-operation"></a>Kérelem művelete

Hibrid kapcsolatok, amelyekhez a szolgáltatás engedélyezve van, a küldő küldhet nagyrészt korlátlan HTTP-kérelmeket a figyelőknek.

A lekérdezési karakterláncba vagy a kérelem HTTP-fejlécébe ágyazott továbbítási hozzáférési jogkivonat kivételével a továbbítás teljes mértékben átlátszó a továbbítási cím és a továbbító címelérési útvonalának összes utótagja számára, így a figyelő teljes mértékben a végpontok között engedélyezés és a HTTP-bővítmény szolgáltatások, például a [CORS](https://www.w3.org/TR/cors/)vezérlésében marad.

A továbbító végponttal rendelkező feladói engedélyezés alapértelmezés szerint be van kapcsolva, de nem kötelező. A hibrid kapcsolat tulajdonosa engedélyezheti a névtelen küldőket. A szolgáltatás az alábbiak szerint fogja meg, ellenőrzi és eltávolítja az engedélyezési adatokat:

1. Ha a lekérdezési `sb-hc-token` karakterlánc tartalmaz egy kifejezést, a kifejezés mindig lesz távolítva a lekérdezési karakterláncból. A rendszer kiértékeli, ha a továbbítási engedély be van kapcsolva.
2. Ha a kérelemfejlécek `ServiceBusAuthorization` fejlécet tartalmaznak, a fejléckifejezés mindig kilesz távolítva a fejlécgyűjteményből.
   A rendszer kiértékeli, ha a továbbítási engedély be van kapcsolva.
3. Csak akkor, ha a Továbbítás engedélyezés be `Authorization` van kapcsolva, és ha a kérelemfejlécek fejlécet tartalmaznak, és egyik korábbi kifejezés sem jelen van, a rendszer kiértékeli és eltávolítja a fejlécet. Ellenkező esetben `Authorization`a mindig átadják, ahogy van.

Ha nincs aktív figyelő, a szolgáltatás egy 502-es "Bad Gateway" hibakódot ad vissza. Ha úgy tűnik, hogy a szolgáltatás nem kezeli a kérést, a szolgáltatás 60 másodperc után egy 504-es "átjáróidő-túltöltést" ad vissza.

### <a name="interaction-summary"></a>Kapcsolatitevékenység összefoglalása

Ennek az interakciós modellnek az az eredménye, hogy a küldő ügyfél jön ki a kézfogás egy "tiszta" WebSocket, amely csatlakozik a figyelő, és hogy nem igényel további preambulumok vagy előkészítés. Ez a modell lehetővé teszi, hogy gyakorlatilag minden meglévő WebSocket-ügyfélimplementáció könnyen kihasználhassa a hibrid kapcsolatok szolgáltatás előnyeit egy megfelelően felépített URL-cím biztosításával a WebSocket ügyfélréteghez.

A websocket-kapcsolat, amelyet a figyelő az elfogadási interakción keresztül szerez be, szintén tiszta, és átadható bármely meglévő WebSocket kiszolgáló implementációnak néhány minimális extra absztrakcióval, amely megkülönbözteti az "elfogadási" műveleteket a keretrendszer helyi hálózati hallgatói és a hibrid kapcsolatok távoli "accept" műveletek.

A HTTP-kérelem/válasz modell a feladónak egy nagyrészt korlátlan HTTP protokollfelületet biztosít opcionális engedélyezési réteggel. A figyelő kap egy előre elemezt HTTP-kérelem fejlécszakasz, amely visszafordítható egy alsóbb rétegbeli HTTP-kérelem, vagy kezelni, ahogy van, a bináris keretek hordozó HTTP-szervek. A válaszok ugyanazt a formátumot használják. A 64 KB-nál kevesebb kérés- és választörzskel végzett interakciók egyetlen webszoftver-szoftvercsatornán keresztül kezelhetők, amely az összes feladó számára meg van osztva. A nagyobb kérések és válaszok a randevú modell használatával kezelhetők.

## <a name="protocol-reference"></a>Protokollhivatkozás

Ez a szakasz a korábban ismertetett protokoll-interakciók részleteit ismerteti.

Minden WebSocket-kapcsolat a 443-as porton https 1.1-es frissítésként jön létre, amelyet általában egyes WebSocket-keretrendszer vagy API absztrakt. A leírás itt tartják végrehajtás semleges, anélkül, hogy konkrét keretet.

### <a name="listener-protocol"></a>Figyelő protokoll

A figyelő protokoll két csatlakozási kézmozdulatból és három üzenetműveletből áll.

#### <a name="listener-control-channel-connection"></a>Figyelő vezérlőcsatorna-kapcsolata

A vezérlőcsatornát websocket-kapcsolat létrehozásával nyitja meg a következő:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

Az `namespace-address` az Azure Relay névtér teljesen minősített tartományneve, amely a hibrid `{myname}.servicebus.windows.net`kapcsolatot, általában az űrlapot üzemelteti.

A lekérdezési karakterlánc paraméterbeállításai a következők.

| Paraméter        | Kötelező | Leírás
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Igen      | A figyelő szerepkörhöz a paraméternek **sb-hc-action=listen**
| `{path}`         | Igen      | Az előre konfigurált hibrid kapcsolat URL-kódolású névtérelérési útja a figyelő regisztrálásához. Ez a kifejezés hozzáfűzi a rögzített `$hc/` elérési út részét.
| `sb-hc-token`    | igen\*    | A figyelőnek meg kell adnia egy érvényes, URL-kódolású Service Bus megosztott hozzáférési jogkivonatot a névtérhez vagy a hibrid kapcsolathoz, amely a **listen** jogot biztosítja.
| `sb-hc-id`       | Nem       | Ez az ügyfél által biztosított opcionális azonosító lehetővé teszi a végpontok között diagnosztikai nyomkövetést.

Ha a WebSocket-kapcsolat sikertelen, mert a hibrid kapcsolat elérési útja nincs regisztrálva, vagy érvénytelen vagy hiányzó jogkivonat, vagy más hiba, a hibavisszajelzést a http 1.1 állapot-visszajelzési modell használatával biztosítjuk. Az állapotleírás tartalmaz egy hibakövetési azonosítót, amely az Azure támogatási munkatársainak közölhető:

| Kód | Hiba          | Leírás
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nem található      | A hibrid kapcsolat elérési útja érvénytelen, vagy az alap URL-cím hibásan formázott.
| 401  | Nem engedélyezett   | A biztonsági jogkivonat hiányzik, hibásan formázott vagy érvénytelen.
| 403  | Forbidden      | A biztonsági jogkivonat nem érvényes ehhez az elérési úthoz ehhez a művelethez.
| 500  | Belső hiba | Valami rosszul sült el a szertartáson.

Ha a websocket-kapcsolatot a szolgáltatás az eredeti beállítás után szándékosan leállítja, ennek oka egy megfelelő WebSocket protokollhibakód, valamint egy leíró hibaüzenet, amely egy nyomkövetési azonosítót is tartalmaz. A szolgáltatás nem állítja le a vezérlőcsatornát hibaállapot nélkül. A tiszta leállítás ügyféláltal vezérelt.

| WS állapot | Leírás
| --------- | -------------------------------------------------------------------------------
| 1001      | A hibrid kapcsolat elérési útját törölték vagy letiltották.
| 1008      | A biztonsági jogkivonat lejárt, ezért az engedélyezési házirend sérül.
| 1011      | Valami rosszul sült el a szertartáson.

#### <a name="accept-handshake"></a>Kézfogás elfogadása

Az "elfogadás" értesítést a szolgáltatás elküldi a figyelőnek a korábban létrehozott vezérlőcsatornán keresztül JSON-üzenetként egy WebSocket szövegkeretben. Erre az üzenetre nincs válasz.

Az üzenet egy "accept" nevű JSON-objektumot tartalmaz, amely jelenleg a következő tulajdonságokat határozza meg:

* **cím** – a WebSocket létrehozásához a szolgáltatáshoz a bejövő kapcsolat elfogadásához használandó URL-karakterlánc.
* **id** – a kapcsolat egyedi azonosítója. Ha az azonosítót a küldő ügyfél adta meg, akkor az a feladó által megadott érték, ellenkező esetben a rendszer által generált érték.
* **connectHeaders** – minden HTTP-fejléc, amelyet a küldő a továbbító végponthoz szállított, amely tartalmazza a Sec-WebSocket-Protocol és a Sec-WebSocket-Extensions fejléceket is.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

A JSON-üzenetben megadott cím URL-címet a figyelő a WebSocket létrehozásához használja a feladó szoftvercsatornájának elfogadásához vagy elutasításához.

##### <a name="accepting-the-socket"></a>A szoftvercsatorna elfogadása

Az elfogadáshoz a figyelő websocket kapcsolatot hoz létre a megadott címmel.

Ha az "accept" üzenet `Sec-WebSocket-Protocol` fejlécet hordoz, a figyelő várhatóan csak akkor fogadja el a WebSocket protokollt, ha támogatja azt a protokollt. Emellett beállítja a fejlécet a WebSocket létrehozásakor.

Ugyanez vonatkozik `Sec-WebSocket-Extensions` a fejlécre is. Ha a keretrendszer támogatja a bővítményt, a fejlécet a `Sec-WebSocket-Extensions` bővítményhez szükséges kézfogás kiszolgálóoldali válaszára kell állítania.

Az URL-címet az elfogadási szoftvercsatorna létrehozásához kell használni, de a következő paramétereket tartalmazza:

| Paraméter      | Kötelező | Leírás
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Igen      | A szoftvercsatorna elfogadásához a paraméternek`sb-hc-action=accept`
| `{path}`       | Igen      | (lásd a következő bekezdést)
| `sb-hc-id`     | Nem       | Lásd az **id**.

`{path}`Az előre konfigurált hibrid kapcsolat URL-kódolású névtérelérési útja, amelyen regisztrálni szeretné ezt a figyelőt. Ez a kifejezés hozzáfűzi a rögzített `$hc/` elérési út részét.

A `path` kifejezés kiterjeszthető egy utótaggal és egy lekérdezési karakterlánc-kifejezéssel, amely a reguláris perelválasztó perelválasztó után követi a regisztrált nevet.
Ez lehetővé teszi, hogy a küldő ügyfél adja át a feladási argumentumokat az elfogadó figyelőnek, ha nem lehet HTTP-fejléceket felvenni. Az elvárás az, hogy a figyelő keretrendszer elemzi ki a rögzített elérési út része és a regisztrált nevet az `sb-`elérési útról, és a fennmaradó, esetleg anélkül, hogy lekérdezési karakterlánc argumentumok előtaggal, elérhetővé teszi az alkalmazás annak eldöntésére, hogy elfogadja a kapcsolatot.

További információt a "Feladó protokollja" című részében talál.

Hiba esetén a szolgáltatás a következőképpen válaszolhat:

| Kód | Hiba          | Leírás
| ---- | -------------- | -----------------------------------
| 403  | Forbidden      | Az URL-cím érvénytelen.
| 500  | Belső hiba | Valami elromlott a szolgáltatásban.

 A kapcsolat létrejötte után a kiszolgáló leállítja a WebSocket szoftvert, amikor a sender WebSocket leáll, vagy a következő állapottal:

| WS állapot | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | A küldő ügyfél leállítja a kapcsolatot.                                    |
| 1001      | A hibrid kapcsolat elérési útját törölték vagy letiltották.                        |
| 1008      | A biztonsági jogkivonat lejárt, ezért az engedélyezési házirend sérül. |
| 1011      | Valami rosszul sült el a szertartáson.                                            |

##### <a name="rejecting-the-socket"></a>A szoftvercsatorna elutasítása

 A szoftvercsatorna elutasítása `accept` az üzenet vizsgálata után hasonló kézfogást igényel, így az elutasítás okát közlő állapotkód és állapotleírás visszafolyhat a feladóhoz.

 A protokoll tervezési választás itt az, hogy egy WebSocket kézfogás (amelynek célja, hogy vessen véget egy meghatározott hibaállapot), hogy a figyelő ügyfél-megvalósítások továbbra is támaszkodhat a WebSocket-ügyfél, és nem kell alkalmazni egy extra, csupasz HTTP-ügyfél.

 A szoftvercsatorna elutasításához az ügyfél veszi `accept` a cím URI-ját az üzenetből, és hozzáfűz hozzá két lekérdezési karakterlánc-paramétert, az alábbiak szerint:

| Param                   | Kötelező | Leírás                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusKód        | Igen      | Numerikus HTTP-állapotkód.                |
| sb-hc-statusDescription | Igen      | Emberileg olvasható oka az elutasításnak. |

Az eredményül kapott URI ezután websocket-kapcsolat létrehozásához használatos.

A megfelelő befejezéskor ez a kézfogás szándékosan meghibásodik a 410-es HTTP-hibakóddal, mivel nem hozott létre WebSocket. Ha valami elromlik, a következő kódok írják le a hibát:

| Kód | Hiba          | Leírás                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Forbidden      | Az URL-cím érvénytelen.                |
| 500  | Belső hiba | Valami rosszul sült el a szertartáson. |

#### <a name="request-message"></a>Üzenet kérése

Az `request` üzenetet a szolgáltatás küldi a figyelő nek a vezérlő csatornán keresztül. Ugyanez az üzenet is küldött át a randevú WebSocket egyszer létre.

A `request` két részből áll: egy fejlécből és egy bináris karosszériakeretből.
Ha nincs test, a testkeretek kimaradnak. A jelző, hogy egy törzs jelen `body` van-e a logikai tulajdonság a kérelem üzenetben.

A kérelem törzsével rendelkező kérelmek esetén a struktúra a következőkre néz ki:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

A figyelőnek kezelnie kell a kérelemtörzs fogadását több bináris képkockák között [(lásd: WebSocket töredékek).](https://tools.ietf.org/html/rfc6455#section-5.4)
A kérelem akkor ér véget, amikor a FIN jelzőkészlettel rendelkező bináris keret beérkezik.

Törzs nélküli kérelemesetén csak egy szövegkeret van.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

A JSON-tartalom a `request` következő:

* **address** - URI karakterlánc. Ez a kérelemhez használandó randevúcím. Ha a bejövő kérelem nagyobb, mint 64 kB, az üzenet fennmaradó része üresen marad, és `accept` az ügyfélnek az alább leírt műveletnek megfelelő találkozókézfogást kell kezdeményeznie. A szolgáltatás ezután `request` a teljes egészét a létrehozott webszoftverezésre helyezi. Ha a válasz várhatóan meghaladja a 64 kB-ot, a figyelőnek egy randevú kézfogást is kezdeményeznie kell, majd a választ a létrehozott webszoftvercsatornán keresztül kell átvinnie.
* **id** – karakterlánc. A kérelem egyedi azonosítója.
* **requestHeaders** – ez az objektum tartalmazza az összes HTTP-fejlécet, amelyet a feladó adott meg a végpontnak, kivéve a [fent](#request-operation)ismertetett engedélyezési információkat, valamint az átjáróval való kapcsolathoz szorosan kapcsolódó fejléceket. Pontosabban az [RFC7230-ban](https://tools.ietf.org/html/rfc7230) `Via`definiált vagy lefoglalt ÖSSZES fejlécet a program eltávolítja és nem továbbítja:

  * `Connection`(RFC7230, 6.1. szakasz)
  * `Content-Length`(RFC7230, 3.3.2. szakasz)
  * `Host`(RFC7230, 5.4. szakasz)
  * `TE`(RFC7230, 4.3. szakasz)
  * `Trailer`(RFC7230, 4.4. szakasz)
  * `Transfer-Encoding`(RFC7230, 3.3.1. szakasz)
  * `Upgrade`(RFC7230, 6.7. szakasz)
  * `Close`(RFC7230, 8.1. szakasz)

* **requestTarget** – karakterlánc. Ez a tulajdonság rendelkezik a kérelem ["Request Target" (RFC7230, 5.3 szakasz)](https://tools.ietf.org/html/rfc7230#section-5.3) a kérelem. Ez magában foglalja a lekérdezési karakterlánc `sb-hc-` részét, amely eltávolítja az ÖSSZES előrögzített paramétert.
* **metódus** - karakterlánc. Ez a kérelem módszere, [rFC7231, 4.](https://tools.ietf.org/html/rfc7231#section-4) A `CONNECT` módszert NEM szabad használni.
* **test** – boolean. Azt jelzi, hogy egy vagy több bináris törzskeret következik-e.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Válaszadás a kérésekre

A vevőnek válaszolnia kell. A kérelmek ismételt megválaszolásának sikertelensége a kapcsolat fenntartása közben azt eredményezheti, hogy a figyelő feketelistára kerül.

A válaszok at bármilyen sorrendben elküldhetjük, de minden kérésre 60 másodpercen belül válaszolni kell, különben a kézbesítés sikertelennek fog jelenteni. A 60 másodperces határidőt `response` addig kell számolni, amíg a szolgáltatás meg nem érkezik a kerethez. A több bináris képkockával rendelkező folyamatos válasz nem válhat 60 másodpercnél tovább tétlenné, vagy megszakad.

Ha a kérés a vezérlőcsatornán keresztül érkezik, a választ vagy azon a vezérlőcsatornán kell elküldeni, ahonnan a kérés érkezett, vagy egy randevú csatornán keresztül kell elküldeni.

A válasz egy "response" nevű JSON-objektum. A testtartalom kezelésére vonatkozó szabályok `request` pontosan olyanok, mint az üzenet, és a `body` tulajdonságon alapulnak.

* **requestId** – karakterlánc. Szükséges. A `id` megválaszolandó `request` üzenet tulajdonságértéke.
* **statusCode** – szám. Szükséges. egy numerikus HTTP-állapotkód, amely az értesítés eredményét jelzi. Az [RFC7231 6.](https://tools.ietf.org/html/rfc7231#section-6) [502 "Bad Gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.3) [504 "Gateway Timeout"](https://tools.ietf.org/html/rfc7231#section-6.6.5)
* **statusDescription** - karakterlánc. Választható. HTTP status-code ok kifejezés [RFC7230 rfc7230, 3.1.2 szakasz](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – A HTTP-fejlécek külső HTTP-válaszban állíthatók be.
  A , `request`rfc7230 definiált fejlécekhez nem használható.
* **test** – boolean. Azt jelzi, hogy a bináris karosszériakeret(ek) követi(ke)t.

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Válasz a randevún keresztül

A 64 kB-ot meghaladó válaszok esetén a választ randevúfoglalaton keresztül kell kézbesíteni. Továbbá, ha a kérelem meghaladja a `request` 64 kB-ot, és csak a címmezőt tartalmazza, létre kell hozni egy randevú aljzatot a `request`. A randevú szoftvercsatorna létrehozása után a megfelelő ügyfélnek adott válaszokat és az adott ügyféltől érkező későbbi kéréseket a rendezvous szoftvercsatornán keresztül kell kézbesíteni, amíg az megmarad.

A `address` címzési szoftvercsatorna létrehozásához a `request` címzési szoftvercsatorna létrehozásához a címzési szoftvercsatorna létrehozásakor kell használni az URL-címet, de a következő paramétereket tartalmazza:

| Paraméter      | Kötelező | Leírás
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Igen      | A szoftvercsatorna elfogadásához a paraméternek`sb-hc-action=request`

Hiba esetén a szolgáltatás a következőképpen válaszolhat:

| Kód | Hiba           | Leírás
| ---- | --------------- | -----------------------------------
| 400  | Érvénytelen kérelem | Ismeretlen művelet vagy URL érvénytelen.
| 403  | Forbidden       | Az URL lejárt.
| 500  | Belső hiba  | Valami elromlott a szolgáltatásban.

 A kapcsolat létrejötte után a kiszolgáló leállítja a WebSocket szoftvert, amikor az ügyfél HTTP-szoftvercsatornája leáll, vagy a következő állapottal:

| WS állapot | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | A küldő ügyfél leállítja a kapcsolatot.                                    |
| 1001      | A hibrid kapcsolat elérési útját törölték vagy letiltották.                        |
| 1008      | A biztonsági jogkivonat lejárt, ezért az engedélyezési házirend sérül. |
| 1011      | Valami rosszul sült el a szertartáson.                                            |


#### <a name="listener-token-renewal"></a>Figyelő jogkivonatának megújítása

Amikor a figyelő jogkivonat hamarosan lejár, akkor cserélje le egy szöveges keret üzenetet a szolgáltatásnak a létrehozott vezérlőcsatornán keresztül. Az üzenet egy JSON-objektumot `renewToken`tartalmaz, amely jelenleg a következő tulajdonságot határozza meg:

* **jogkivonat** – egy érvényes, URL-kódolású Service Bus megosztott hozzáférési jogkivonat a névtér vagy a hibrid kapcsolat, amely biztosítja a **listen** jogot.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Ha a jogkivonat érvényesítése sikertelen, a rendszer megtagadja a hozzáférést, és a felhőszolgáltatás hibával bezárja a WebSocket vezérlőcsatornát. Ellenkező esetben nincs válasz.

| WS állapot | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | A biztonsági jogkivonat lejárt, ezért az engedélyezési házirend sérül. |

### <a name="web-socket-connect-protocol"></a>Web socket csatlakozási protokoll

A küldő protokoll hatékonyan megegyezik a figyelő létrehozásának módjával.
A cél a teljes körű WebSocket maximális átláthatósága. A cím, amelyhez csatlakozni kell, megegyezik a figyelővel, de a "művelet" különbözik, és a jogkivonatnak más engedélyre van szüksége:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

A _névtér-cím_ a hibrid kapcsolatot tartalmazó Azure Relay névtér teljesen minősített tartományneve, amely általában az űrlapon található. `{myname}.servicebus.windows.net`

A kérelem tetszőleges további HTTP-fejléceket tartalmazhat, beleértve az alkalmazás által definiált fejléceket is. Az összes megadott fejléc a figyelőhöz áramlik, és megtalálható az `connectHeader` **elfogadásvezérlő** üzenet objektumán.

A lekérdezési karakterlánc paraméterbeállításai a következők:

| Param          | Kötelező? | Leírás
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Igen       | A küldőszerepkörhöz a paraméternek kell lennie. `sb-hc-action=connect`
| `{path}`       | Igen       | (lásd a következő bekezdést)
| `sb-hc-token`  | igen\*     | A figyelőnek meg kell adnia egy érvényes, URL-kódolású Service Bus megosztott hozzáférési jogkivonatot a névtérhez vagy a hibrid kapcsolathoz, amely a **küldési** jogot biztosítja.
| `sb-hc-id`     | Nem        | Egy opcionális azonosító, amely lehetővé teszi a végpontok között diagnosztikai nyomkövetést, és elérhetővé válik a figyelő számára az elfogadási kézfogás során.

 A `{path}` az előre konfigurált hibrid kapcsolat URL-kódolású névtérelérési útja, amelyen regisztrálni szeretné ezt a figyelőt. A `path` kifejezés egy utótaggal és egy lekérdezési karakterlánc-kifejezéssel bővíthető a további kommunikációhoz. Ha a hibrid kapcsolat regisztrálva van az elérési út `hyco`alatt, a `path` kifejezést az itt megadott lekérdezési karakterlánc-paraméterek követhetik. `hyco/suffix?param=value&...` A teljes kifejezés ezután a következő lehet:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

A `path` kifejezés az "elfogadás" vezérlőüzenetben található cím URI-jában jut át a figyelőhöz.

Ha a WebSocket-kapcsolat sikertelen, mert a hibrid kapcsolat elérési útja nincs regisztrálva, érvénytelen vagy hiányzó jogkivonat, vagy más hiba, a hibavisszajelzést a http 1.1-es állapot-visszajelzési modell használatával biztosítjuk. Az állapotleírás tartalmaz egy hibakövetési azonosítót, amely az Azure támogatási munkatársaival közölhető:

| Kód | Hiba          | Leírás
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nem található      | A hibrid kapcsolat elérési útja érvénytelen, vagy az alap URL-cím hibásan formázott.
| 401  | Nem engedélyezett   | A biztonsági jogkivonat hiányzik, hibásan formázott vagy érvénytelen.
| 403  | Forbidden      | A biztonsági jogkivonat nem érvényes erre az elérési útra és erre a műveletre.
| 500  | Belső hiba | Valami rosszul sült el a szertartáson.

Ha a szolgáltatás az eredeti beállítás után szándékosan leállítja a WebSocket-kapcsolatot, ennek oka a megfelelő WebSocket protokollhibakód, valamint egy leíró hibaüzenet jelenik meg, amely egy nyomon követési azonosítót is tartalmaz .

| WS állapot | Leírás
| --------- | ------------------------------------------------------------------------------- 
| 1000      | A figyelő leállította a szoftvercsatornát.
| 1001      | A hibrid kapcsolat elérési útját törölték vagy letiltották.
| 1008      | A biztonsági jogkivonat lejárt, ezért az engedélyezési házirend sérül.
| 1011      | Valami rosszul sült el a szertartáson.

### <a name="http-request-protocol"></a>HTTP-kérelem protokoll

A HTTP-kérelem protokoll tetszőleges HTTP-kéréseket tesz lehetővé, kivéve a protokollfrissítéseket.
A HTTP-kérelmek az entitás normál futásidejű címére mutatnak, a hibrid kapcsolatokhoz használt websocket-ügyfelekhez használt $hc infix nélkül.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

A _névtér-cím_ a hibrid kapcsolatot tartalmazó Azure Relay névtér teljesen minősített tartományneve, amely általában az űrlapon található. `{myname}.servicebus.windows.net`

A kérelem tetszőleges további HTTP-fejléceket tartalmazhat, beleértve az alkalmazás által definiált fejléceket is. Az Összes megadott fejléc, kivéve a közvetlenül meghatározott RFC7230 (lásd a [kérésüzenet)](#Request message)áramlását a figyelő, és megtalálható az `requestHeader` objektum a **kérelem** üzenet.

A lekérdezési karakterlánc paraméterbeállításai a következők:

| Param          | Kötelező? | Leírás
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | igen\*     | A figyelőnek meg kell adnia egy érvényes, URL-kódolású Service Bus megosztott hozzáférési jogkivonatot a névtérhez vagy a hibrid kapcsolathoz, amely a **küldési** jogot biztosítja.

A jogkivonat a vagy `ServiceBusAuthorization` a `Authorization` HTTP fejlécben is hordozható. A jogkivonat elhagyható, ha a hibrid kapcsolat névtelen kérelmek engedélyezésére van konfigurálva.

Mivel a szolgáltatás hatékonyan működik proxyként, még akkor is, `Via` ha nem valódi HTTP-proxyként, vagy hozzáad egy fejlécet, vagy jegyzetekkel egészíti ki az `Via` [RFC7230 5.7.1 szakaszának](https://tools.ietf.org/html/rfc7230#section-5.7.1)megfelelő meglévő fejlécet.
A szolgáltatás hozzáadja a Relay `Via`névtér állomásnevét a hoz.

| Kód | Üzenet  | Leírás                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | A kérést legalább egy figyelő kezelte.  |
| 202  | Elfogadva | A kérelmet legalább egy figyelő elfogadta. |

Hiba esetén a szolgáltatás a következőképpen válaszolhat. Az, hogy a válasz a szolgáltatásból vagy a figyelőből `Via` származik-e, a fejléc jelenlétével azonosítható. Ha a fejléc jelen van, a válasz a figyelőtől származik.

| Kód | Hiba           | Leírás
| ---- | --------------- |--------- |
| 404  | Nem található       | A hibrid kapcsolat elérési útja érvénytelen, vagy az alap URL-cím hibásan formázott.
| 401  | Nem engedélyezett    | A biztonsági jogkivonat hiányzik, hibásan formázott vagy érvénytelen.
| 403  | Forbidden       | A biztonsági jogkivonat nem érvényes erre az elérési útra és erre a műveletre.
| 500  | Belső hiba  | Valami rosszul sült el a szertartáson.
| 503  | Hibás átjáró     | A kérés nem irányítható egyetlen figyelőhöz sem.
| 504  | Átjáró időmeghosszabbítása | A kérelem egy figyelőhöz lett irányítva, de a figyelő nem nyugtázta a nyugtázást a szükséges idő alatt.

## <a name="next-steps"></a>További lépések

* [Relay – gyakori kérdések](relay-faq.md)
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)
