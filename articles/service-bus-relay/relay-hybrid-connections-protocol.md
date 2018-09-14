---
title: Az Azure Relay hibrid kapcsolatai protokoll útmutató |} A Microsoft Docs
description: Az Azure Relay hibrid kapcsolatai protokoll – útmutató.
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
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: 913e702cc72472e81937bfe3b0939695daadc011
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543523"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Az Azure Relay hibrid kapcsolatai protokoll

Az Azure Relay egyike a képesség kulcsfontosságú területei legyenek elérhetők az Azure Service Bus-platform. Az új _hibrid kapcsolatok_ a továbbítási szolgáltatás érhető el egy biztonságos, nyílt protokoll szerinti továbbfejlesztése a HTTP és a websockets szabványon alapul. A korábbi, ugyanilyen nevű felülírt szoftverfrissítésé _BizTalk Services_ funkció, amely a saját fejlesztésű protokollja foundation épült. Az Azure App Services integrálása a hibrid kapcsolatok továbbra is működhet-van.

Hibrid kapcsolatok lehetővé teszi, hogy kétirányú bináris adatfolyam és datagram egyszerű folyamat két hálózati alkalmazás között. Egyik vagy mindkét fél NAT vagy tűzfal mögött találhatók.

Ez a cikk ismerteti a hibrid kapcsolatok relay csatlakozó ügyfeleken a figyelőt és a küldő szerepkörök ügyféloldali interakció. Azt is ismerteti, hogyan működtethet az új kapcsolatainak és kérelmeinek fogadja el.

## <a name="interaction-model"></a>Interakció modell

A hibrid kapcsolatok relay két fél azáltal, hogy az Azure-felhőben, amelyek felek felderítése és a saját hálózati szempontból csatlakozni egy szinkronizálási pont csatlakozik. A szinkronizálási pont neve "Hibrid kapcsolat" Ebben és egyéb dokumentáció, az API-k és az Azure Portalon. A hibrid kapcsolatok szolgáltatásvégpont Ez a cikk további részének "szolgáltatás" nevezzük.

A szolgáltatás lehetővé teszi a webes szoftvercsatorna-kapcsolatok és a HTTP (S)-kérelmek és válaszok továbbításához.

A kapcsolati modell leans a számos más hálózati API-k által létrehozott. Egy figyelő, amely először azt jelzi, hogy készen állnak a bejövő kapcsolatok kezelésére, és ezt követően fogadja el őket, akkor van. A másik oldalon egy ügyfél kapcsolódik, a figyelő vár, hogy kapcsolatot lehessen létesíteni egy kétirányú kommunikációs útvonalat el kell fogadni felé. "Csatlakozás", "Figyelés" és "Elfogadása" is az azonos feltételeket a legtöbb szoftvercsatorna API-k is megtalálhatók.

Bármely továbbítón keresztüli kommunikáció modellnek bármelyik fél felé egy végpontot a kimenő kapcsolatokat. Ez lehetővé teszi a "figyelő" is "ügyfél" köznyelvi használja, és okozhat más terminológiát túlterheléssel. A pontos, ezért a hibrid kapcsolatokhoz használt terminológia a következőképpen történik:

A kapcsolat mindkét oldalán programok nevezik "ügyfél", mivel azok az ügyfelek számára, hogy a szolgáltatás. Az ügyfél, amely fogad kapcsolatokat, és megvárja, amíg a "figyelő", vagy tekinthető a szerepkörben"figyelőt." Az ügyfél, amely egy figyelőt a a szolgáltatás felé új kapcsolatot kezdeményez a "feladó" nevezzük, vagy ha a "feladó szerepkör."

### <a name="listener-interactions"></a>Figyelő interakciók

A figyelő a szolgáltatásban; öt interakciók rendelkezik az összes átviteli részletei a hivatkozási szakaszban Ez a cikk egy későbbi része ismerteti.

A figyelés, a elfogadás és a kérelem fogadása a szolgáltatásból. A megújítás, és a figyelő által küldött Ping műveleteket.

#### <a name="listen-message"></a>Figyelés az üzenet

Jelzi, hogy a szolgáltatás, amely egy figyelő készen áll a fogadja a csatlakozásokat, létrehoz egy kimenő WebSocket-kapcsolatot. A kapcsolati kézfogás sorozatéhoz beállítani a Relay-névtér és a egy biztonsági jogkivonatot, amely a "figyelés" közvetlenül a ruház ezt a nevet a hibrid kapcsolat neve.

A WebSocket elfogadja a szolgáltatást, ha a regisztráció befejeződött, és a bevett WebSocket életben vezérlőcsatornaként"" az összes későbbi interakciók engedélyezése megőrzi. A szolgáltatás lehetővé teszi, hogy legfeljebb 25 párhuzamos figyelők egy hibrid kapcsolat. A AppHooks nem lehet meghatározni.

A hibrid kapcsolatokhoz Ha két vagy több aktív figyelők, a bejövő kapcsolatok között oszlanak el őket véletlenszerű sorrendben; igazságos elosztási kísérlet történik a lehető legjobb.

#### <a name="accept-message"></a>Üzenet elfogadása

A küldő a szolgáltatás az új kapcsolat megnyitása után a szolgáltatás úgy dönt, és értesíti a hibrid kapcsolat az aktív figyelők egyike. A rendszer értesítést küld a figyelő nyissa meg a vezérlőcsatorna keresztül egy JSON-ként. Az üzenet tartalmaz, amelyek a figyelő csatlakoznia kell a kapcsolat fogadásához a WebSocket-végpont URL-CÍMÉT.

Az URL-címet is, és közvetlenül további munka nélkül a figyelőt kell használni.
A kódolt információt csak a érvényes rövid idő alatt, lényegében, az addig, amíg a küldő arra, hogy Várjon, amíg a kapcsolat teljes körű létrehozott lehet. Tegyük fel, hogy a maximális érték 30 másodperc. Az URL-cím csak egy sikeres kapcsolódási kísérlet esetében használható. Amint a szinkronizálási URL-címet a WebSocket-kapcsolat létrejött, a WebSocket minden további tevékenység a kezdő és a küldő a továbbítón keresztüli. Ez akkor fordul elő értelmezését és beavatkozás nélkül a szolgáltatás által.

### <a name="request-message"></a>Kérelem üzenet

WebSocket kapcsolatok, valamint a figyelő is fogadhassanak HTTP-kérelem keretek a küldő, ha ezt a képességet explicit módon engedélyezve van a hibrid kapcsolat.

Figyelők, amelyhez a hibrid kapcsolatok a HTTP-támogatás kell kezelnie a `request` kézmozdulat. Egy figyelő, amely nem kezeli az `request` és ezért ismétlődő időtúllépési hibákat okoz, amíg csatlakoztatva is lehet tiltólistán a szolgáltatás által a jövőben.

HTTP-keret fejléc metaadatok alakítja át a rendszer JSON egyszerűbb kezelésére. a figyelő keretrendszer által is, mert a HTTP-fejléc elemzési függvénytárak is, mint a JSON-elemzők ritkább. Amely csak a küldő és a Relay HTTP-átjáró, többek között az engedélyezési információkat közötti kapcsolatot a megfelelő HTTP-metaadatok nem lesznek továbbítva. HTTP-kéréstörzsekkel transzparens módon bináris WebSocket keretek kerülnek át.

A figyelő egy azzal egyenértékű válasz kézmozdulat HTTP-kérések tud válaszolni.

A kérelem/válasz folyamat alapértelmezés szerint a vezérlőcsatorna használ, de "frissíthetők" egy különálló megcélozva WebSocket, amikor szükséges. Különböző WebSocket-kapcsolatok esetében minden egyes ügyfél beszélgetés teljesítmény növelését, de azok terheljék a figyelőjét a következő további kapcsolatokat szeretne kezelni, ami nem feltétlenül egyszerűsített ügyfelek képesek törekszik.

A vezérlőcsatornán a kérések és válaszok szervek korlátozva, legfeljebb 64 KB-os méretig. Összesen 32 KB-os HTTP-fejléc metaadatok korlátozódik. Ha a kérelem vagy válasz meghaladja a küszöbértéket, a figyelő egy szinkronizálási egy kézmozdulattal kezelési egyenértékű WebSocket kell frissítenie a [elfogadás](#accept-message).

A kéréseket a szolgáltatás úgy dönt, hogy e irányíthatja a kérelmeket a vezérlő csatornán. Ez magában foglalja, de nem lehet csak azokra az esetekre, ha egy kérés mérete meghaladja a 64 KB-os (fejlécek és törzs) egyből, vagy a kérelem elküldésekor a ["darabolásos" transfer-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) , és a szolgáltatás várt a kérelem meghaladja a 64 KB-os vagy a kérelem olvasása nem azonnali. Ha a szolgáltatás úgy dönt, hogy kézbesíteni a kérelmet keresztül megcélozva, csak adja át, a szinkronizálási címet megcélozva a figyelőt.
A figyelő majd kell létesítenie a WebSocket megcélozva, és a szolgáltatás azonnal továbbítja a teljes kérést, többek között a szervezetek a WebSocket megcélozva keresztül. A válasz a szinkronizálási WebSocket is használnia kell.

A vezérlőcsatorna keresztül érkező kérelmeket, a a figyelő úgy dönt, hogy a vezérlőcsatorna keresztül, vagy szinkronizálási keresztül válaszol-e. A szolgáltatás a szinkronizálási címet megcélozva tartalmaznia kell a vezérlőcsatorna keresztül minden egyes kérelemmel. Ez a cím csak akkor érvényes az aktuális kérelem termékről.

A figyelő úgy dönt, hogy frissítse, ha csatlakozik, és azonnal továbbítja a választ a meglévő szinkronizálási szoftvercsatornán keresztül.

Egyszer a szinkronizálási WebSocket létrejött, a figyelő fenn kell tartania, a kérelmek és válaszok az ugyanazon ügyféltől érkező további kezeléséhez. A szolgáltatás megőrzi a WebSocket mindaddig, amíg a HTTPS szoftvercsatorna-kapcsolatot a küldő továbbra is fennáll, és a karbantartott websocketen irányítja át a feladótól az összes további kérelmet. Ha a figyelő úgy dönt, a szinkronizálási WebSocket eldobása saját oldalán, a szolgáltatás is dobja el a kapcsolatot a küldő, függetlenül attól, hogy egy későbbi kérése már lehetnek folyamatban.

#### <a name="renew-operation"></a>Újítsa meg a műveletet

A biztonsági jogkivonat, amely a figyelő regisztrálása és karbantartása a vezérlőcsatorna kell használni lehet, hogy érvényessége lejár, amíg a figyelő aktív. A jogkivonat lejárati nem befolyásolja a folyamatban lévő kapcsolatok, de azt okozhat a vezérlőcsatorna, dobható el, illetve hamarosan a pillanatban a lejárat után a szolgáltatás által. A "megújítása" műveletet, amelyek a figyelő küldhet, hogy a vezérlőcsatorna hosszabb ideig lehessen őrizni, cserélje le a jogkivonatot a vezérlőcsatorna társított JSON üzenet.

#### <a name="ping-operation"></a>Pingelési művelet

Ha hosszú ideje, a a módszerrel a közvetítők tétlen marad a vezérlőcsatorna például a terhelés terheléselosztók vagy a NAT előfordulhat, hogy közvetlen TCP-kapcsolatot. A "ping" művelet elkerülhető, amely a csatornán, amely emlékezteti a hálózati útvonal, amely a kapcsolat hivatott élünk, és azt is lehetővé teszi, egy "élő" teszt esetében a figyelő a kisebb mennyiségű adatot küld. Ha a pingelés sikertelen, a vezérlőcsatorna használhatatlan kell tekinteni, és a figyelő elméletileg újracsatlakozik.

### <a name="sender-interaction"></a>Küldő interakció

A küldő a szolgáltatás két interakció rendelkezik: egy Web Socket kapcsolódik vagy a HTTPS-kapcsolaton keresztül kéréseket küld. Kérelmek nem lehet elküldeni egy Web Socket keresztül a küldő szerepkörből.

#### <a name="connect-operation"></a>Kapcsolódási műveletet.

A "Csatlakozás" művelet megnyitja a szolgáltatás a WebSocket egy biztonsági jogkivonat megadása (ha szükséges, de alapértelmezés szerint), és a hibrid kapcsolat neve a lekérdezési karakterláncban eredményező "Küldés" engedéllyel. A figyelő, amely a WebSocket megcélozva kapcsolatot hoz létre, és a szolgáltatás majd beküldje a figyelő a korábban leírt módon. A WebSocket elfogadását követően minden további interakciók a WebSocket a kapcsolódó figyelőkként vannak.

#### <a name="request-operation"></a>Kérési művelet

Hibrid kapcsolatok, amely a szolgáltatás engedélyezve lett a küldő küldhet nagymértékben korlátlan HTTP-kérések figyelők.

Relay hozzáférési jogkivonatot, amely vagy a lekérdezési karakterláncban vagy a kérelem HTTP-fejlécben lévő beágyazott, kivéve a továbbító egy olyan teljes mértékben átlátható a továbbítási címhez az összes HTTP-műveletek és a Relay-cím elérési útjának minden utótagok teljes és a figyelő en ellenőrzése d teljes engedélyezési és még akkor is, HTTP-bővítmény szolgáltatások például [CORS](https://www.w3.org/TR/cors/).

A Relay-végponttal rendelkező feladójának engedélyezési alapértelmezés szerint be van kapcsolva, de nem kötelező. A hibrid kapcsolat tulajdonosa választhat, hogy névtelen feladók. A szolgáltatás intercept, vizsgálja meg, és engedélyezési információ a következő sáv:

1. Ha a lekérdezési karakterláncot tartalmaz egy `sb-hc-token` kifejezés, a kifejezés mindig fogja eltávolítani a lekérdezési karakterláncból. Ha a Relay-engedélyezési be van kapcsolva kiértékelendő.
2. Ha a kérelem fejlécében tartalmaz egy `ServiceBusAuthorization` fejléc, a fejléc kifejezés mindig fogja eltávolítani a fejléc gyűjteményből.
   Ha a Relay-engedélyezési be van kapcsolva kiértékelendő.
3. Csak akkor, ha a Relay-engedélyezési be van kapcsolva, és ha a kérelem fejlécében tartalmaz egy `Authorization` fejlécére, majd a korábbi kifejezések egyike sem szerepel, az fejléc fog értékelni és eltávolítani. Ellenkező esetben a `Authorization`mindig átkerül az-van.

Ha nincs aktív figyelő, a szolgáltatás visszaadja a "Hibás átjáró" 502 hibakód. Ha a szolgáltatás nem jelenik meg kezelni a kérést, akkor a szolgáltatás 60 másodperc után egy 504 "időtúllépés" vissza.

### <a name="interaction-summary"></a>Interakció összegzése

Ez a kapcsolati modell eredménye, hogy a küldő ügyfél ki a "tiszta" WebSocket, amely egy figyelő csatlakozik, és nincs további preambulumok vagy előkészítési igénylő kézfogást származik. Ez a modell lehetővé teszi, hogy gyakorlatilag bármely meglévő WebSocket ügyfél megvalósítása azonnal kihasználásához a hibrid kapcsolatok szolgáltatás megfelelően kialakított URL-cím megadásával a WebSocket ügyfél rétegbe.

A szinkronizálási kapcsolat, amely a figyelő lekéri az elfogadás interakció keresztül WebSocket is tiszta és átadható bármely meglévő WebSocket kiszolgáló megvalósításának egyes minimális extra absztrakció, amely megkülönbözteti az "elfogadás" művelet között a keretrendszer helyi hálózati figyelők és a hibrid kapcsolatok és a távoli "elfogadása" műveletek.

A HTTP-kérés/válasz modellt biztosít a küldő egy nagy mértékben korlátozott HTTP protokoll támadási egy nem kötelező hitelesítési réteggel. A figyelő egy előre elemzett HTTP kérés fejlécében szakasz vissza egy alsóbb rétegbeli HTTP-kérelem be van kapcsolva vagy van, a bináris keretek végző HTTP szervek megfelelően kezeli a beolvasása. Válaszok ugyanazt a formátumot használja. Az összes feladó megosztott egyetlen webes szoftvercsatorna keresztül lehet kezelni, legfeljebb 64 KB-os kérés-válasz törzsében interakció. Nagyobb kérelmek és válaszok lehet kezelni, a szinkronizálási modell használatával.

## <a name="protocol-reference"></a>Protokoll referenciája

Ez a szakasz a korábban leírt protokoll interakciókat részleteit ismerteti.

Az összes WebSocket kapcsolatok hozhatók létre a 443-as porton frissítéseként, HTTPS 1.1-es, amelyeket általában néhány WebSocket-keretrendszert vagy API-t. A leírás helye másolatok megvalósítási semleges, anélkül, hogy egy adott keretében javaslat.

### <a name="listener-protocol"></a>Figyelő protokoll

A figyelő protokoll két kapcsolat hitelesítési módok és három message operations áll.

#### <a name="listener-control-channel-connection"></a>Figyelő vezérlő csatornán keresztül

A vezérlőcsatorna, ahol a WebSocket-kapcsolat:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

A `namespace-address` a teljesen minősített tartományneve az Azure Relay-névteret, amely futtatja a hibrid kapcsolatot, általában a következő formában `{myname}.servicebus.windows.net`.

A lekérdezési karakterlánc paraméter beállítások a következők.

| Paraméter        | Szükséges | Leírás
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Igen      | A figyelő szerepkör paraméternek kell lennie **sb-hibrid kapcsolat-action = figyelés**
| `{path}`         | Igen      | Regisztrálni. Ez a figyelő az előre konfigurált hibrid kapcsolat URL-kódolású névtér elérési útja. Ez a kifejezés a rendszer hozzáfűzi a rögzített `$hc/` elérési út részével.
| `sb-hc-token`    | igen\*    | A figyelő adjon meg egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési tokent a névtér vagy hibrid kapcsolatot, amely ruház a **figyelésére** megfelelő.
| `sb-hc-id`       | Nem       | Ez az ügyfél által megadott választható azonosító lehetővé teszi, hogy a teljes körű diagnosztikai nyomkövetés.

A WebSocket-kapcsolatot a hibrid kapcsolat elérési út nincs folyamatban van regisztrálva, vagy egy érvénytelen vagy hiányzó jogkivonatot, vagy más hiba miatt nem sikerül, ha a hiba visszajelzés érkezett a HTTP 1.1-es állapot rendszeres visszajelzés modell használatával. Az állapot leírása hiba követési-azonosítót tartalmaz, amely az Azure támogatási személyzete tájékoztatni lehet:

| Kód | Hiba          | Leírás
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nem található      | A hibrid kapcsolat elérési út érvénytelen, vagy az alap URL-cím formátuma.
| 401  | Nem engedélyezett   | A biztonsági token hiányzik vagy helytelen formátumú vagy érvénytelen.
| 403  | Tiltott      | A biztonsági token nem érvényes ehhez a művelet az elérési úthoz.
| 500  | Belső hiba | Probléma merült fel a szolgáltatásban.

Ha a WebSocket kapcsolaton szándékosan a szolgáltatás által után leáll kezdetben hozták létre, így a megfelelő WebSocket protokoll hibakódot és a egy is magában foglalja a nyomkövetési azonosítót. részletes hibaüzenet használatával továbbítsák az az oka A szolgáltatás nem állítja le a vezérlőcsatorna nélkül hajt végre, amikor hiba történik. Bármely tiszta Leállítás utáni állapotba az ügyfél szabályozza.

| WS-állapot | Leírás
| --------- | -------------------------------------------------------------------------------
| 1001      | A hibrid kapcsolat elérési törölték vagy le van tiltva.
| 1008      | A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirend sérül.
| 1011      | Probléma merült fel a szolgáltatásban.

#### <a name="accept-handshake"></a>Fogadja el a kézfogás

Az "elfogadás" értesítés érkezik a szolgáltatás által a figyelő a korábban meghatározott vezérlési csatornán WebSocket szöveg keret JSON-ként. Nem érkezik válasz erre az üzenetre.

Az üzenet JSON-objektum "elfogadás" nevű meghatározó jelenleg a következő tulajdonságokat tartalmazza:

* **cím** – az URL-karakterlánc, fogadja el a bejövő kapcsolatokat lehessen létesíteni a WebSocket a szolgáltatáshoz használandó.
* **ID** – Ez a kapcsolat egyedi azonosítója. Ha az azonosító lett megadva, a küldő ügyfél, a feladó megadott értéke, különben a rendszer által létrehozott értéket.
* **connectHeaders** – az összes HTTP-fejléceket adják meg van adva a továbbítási végpontra a feladó, az mp-WebSocket-protokoll és az mp-WebSocket-bővítmények fejlécet is tartalmazza.

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

A JSON-üzenetben megadott URL-címe elfogadása vagy elutasítása a küldő szoftvercsatorna a WebSocket létesíteni a figyelő használják.

##### <a name="accepting-the-socket"></a>A szoftvercsatorna elfogadása

Fogadja el, hogy a figyelő a megadott cím WebSocket kapcsolatot létesít.

Ha az "accept" üzenetet végzi egy `Sec-WebSocket-Protocol` fejléc, valószínű, hogy a figyelő csak elfogadja a WebSocket, ha támogatja az adott protokollt. Ezenkívül a fejléc állítja a WebSocket meghatározott.

Ugyanez vonatkozik a `Sec-WebSocket-Extensions` fejléc. Ha a keretrendszer bővítmény támogatja, a szükséges, a kiszolgálóoldali válaszolnia kell beállítania a fejléc `Sec-WebSocket-Extensions` kézfogás a bővítmény.

Az URL-címet kell használható – az elfogadás szoftvercsatorna létrehozásához szükséges, de az alábbi paramétereket tartalmazza:

| Paraméter      | Szükséges | Leírás
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Igen      | A szoftvercsatorna fogadásához, a paraméternek kell lennie `sb-hc-action=accept`
| `{path}`       | Igen      | (lásd a következő bekezdés)
| `sb-hc-id`     | Nem       | Előző leírása, **azonosító**.

`{path}` a névtér URL-kódolású elérési útja, amelyen ez a figyelő regisztrálása a előre konfigurált hibrid kapcsolat van. Ez a kifejezés a rendszer hozzáfűzi a rögzített `$hc/` elérési út részével.

A `path` kifejezés is kiterjeszthető az utótag és a egy lekérdezési karakterlánc-kifejezés, amely a regisztrált név elválasztó perjellel után következik.
Ez lehetővé teszi, hogy a küldő ügyfél dispatch argumentumok átadása a beküldhetők figyelő, ha az nem HTTP-fejléceket is tartalmazhatnak. Az elvárás, hogy a figyelő keretrendszer rögzített elérési út részével és a regisztrált név elérési úton található elemzi, és esetleg lehetővé teszi a fennmaradó lekérdezési karakterlánc argumentum nélkül előtagja `sb-`, az alkalmazások mellett dönt az elérhető elfogadja-e a kapcsolat.

További információkért tekintse meg a következő "Küldő protokoll" szakaszt.

Ha hiba történik, a szolgáltatás is válasz a következő:

| Kód | Hiba          | Leírás
| ---- | -------------- | -----------------------------------
| 403  | Tiltott      | Az URL-cím érvénytelen, nem.
| 500  | Belső hiba | Hiba történt a szolgáltatásban

 A kapcsolat létrejötte után a kiszolgáló leáll a WebSocket, amikor a küldő WebSocket kikapcsolják, vagy a következő állapotkóddal:

| WS-állapot | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | A küldő ügyfél leállítja a kapcsolatot.                                    |
| 1001      | A hibrid kapcsolat elérési törölték vagy le van tiltva.                        |
| 1008      | A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirend sérül. |
| 1011      | Probléma merült fel a szolgáltatásban.                                            |

##### <a name="rejecting-the-socket"></a>A szoftvercsatorna elutasítása

 A szoftvercsatorna visszautasítja ellenőrzése után a `accept` üzenet használatához szükség van a hasonló kézfogás az állapotkód, állapot leírása kommunikáció okát a áramolhasson az elutasítás biztonsági másolatot a küldőnek.

 Itt a protokoll tervezési szempontokat, hogy a WebSocket-kézfogás (, amely egy meghatározott hibás állapotú vége) használja, hogy a figyelő ügyfélmegvalósítások esetén továbbra is számíthat a WebSocket ügyfél, és nem kell egy plusz a alkalmazni, HTTP-alapú operációs.

 A szoftvercsatorna elutasításához az ügyfél URI-cím vesz igénybe, a `accept` üzenet, és hozzáfűzi a két lekérdezési karakterlánc paraméterei, a következő:

| Param                   | Szükséges | Leírás                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB-hibrid kapcsolat-állapotkód        | Igen      | Numerikus HTTP-állapotkódot.                |
| SB-hibrid kapcsolat-statusDescription | Igen      | Emberi olvasható ok az elutasítás. |

Az eredményül kapott URI szolgál majd WebSocket kapcsolatot létesíteni.

Befejezése megfelelően, a kézfogás szándékosan sikertelen lesz, a HTTP-hibakód: 410-es, mivel nincs WebSocket létrejött. Ha hiba lép fel, a következő kód adja meg a hiba:

| Kód | Hiba          | Leírás                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Tiltott      | Az URL-cím érvénytelen, nem.                |
| 500  | Belső hiba | Probléma merült fel a szolgáltatásban. |

#### <a name="request-message"></a>Kérelem üzenet

A `request` üzenetet küld a szolgáltatás által a figyelő a vezérlőcsatorna keresztül. A szinkronizálási WebSocket többnél is átküldött ugyanazt az üzenetet.

A `request` két részből áll: egy és a bináris fejléc szervezet kocka.
Ha nem a szervezet, a szervezet keretek hiányoznak. A jelen-e a szervezet a kijelző a logikai `body` a kérelemüzenet tulajdonságot.

A kérelem törzsében a kérelmek a struktúra néz ki:

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

A figyelő kell kezelnie a kérelem törzsében elosztja a több bináris keretek fogadása (lásd: [WebSocket töredék](https://tools.ietf.org/html/rfc6455#section-5.4)).
A kérelem véget ér, amikor a PÉNZÜGY jelző be van állítva a bináris keret érkezett.

A szervezet nélküli kérést csak egy szöveges keret nincs.

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

A JSON-tartalmak `request` a következő:

* **cím** -URI-karakterláncot. Ez az a szinkronizálási címet megcélozva használata ehhez a kérelemhez. Ha a bejövő kérelem 64 kB-nál nagyobb méretű, ez az üzenet része üresen, és az ügyfél egy egyenértékű megcélozva kézfogás kell kezdeményezni a `accept` alább ismertetett művelet. A szolgáltatás majd helyezze a teljes `request` a létrehozott webalkalmazás szoftvercsatornán. Ha a válasz várhatóan túllépi a 64 KB-os, a figyelő kell is kezdeményezheti a szinkronizálási kézfogás, és utána a választ a meglévő Web socket keresztül.
* **ID** – karakterlánc. A kérelem egyedi azonosítója.
* **requestHeaders** – Ez az objektum tartalmazza az összes HTTP-fejléceket adják meg van adva a végpontra a feladó, az engedélyezési adatok kivétellel leírtak [fent](#request-operation), és a fejlécek, amelyek feltétlenül vonatkoznak az az átjáró kapcsolatot. Pontosabban, minden fejléc definiálva, vagy fenntartott [RFC7230](https://tools.ietf.org/html/rfc7230), kivéve `Via`, eltávolítani, és nem továbbítják:

  * `Connection` (RFC7230 szakasz 6.1)
  * `Content-Length`  (RFC7230 3.3.2 szakaszát)
  * `Host`  (RFC7230 szakasz 5.4)
  * `TE`  (RFC7230 4.3. szakasz)
  * `Trailer`  (RFC7230 szakasz 4.4)
  * `Transfer-Encoding`  (RFC7230 3.3.1-es szakasz)
  * `Upgrade` (RFC7230 szakasz 6.7)
  * `Close`  (RFC7230 szakasz 8.1)

* **requestTarget** – karakterlánc. Ez a tulajdonság tárolja a ["Kérelem Target" (RFC7230, 5.3. szakasz)](https://tools.ietf.org/html/rfc7230#section-5.3) a kérelem. Ez magában foglalja a eltávolítani az összes lekérdezési karakterlánc része `sb-hc-` paraméterek elé.
* **módszer** -karakterlánc. Ez a mód az a kérelem [RFC7231, 4. szakasz](https://tools.ietf.org/html/rfc7231#section-4). A `CONNECT` metódus nem használható.
* **törzs** – logikai. Azt jelzi-e legalább egy bináris törzs keret követi.

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

##### <a name="responding-to-requests"></a>Válaszol a kérelmekre

A fogadó válaszolnia kell. Ismétlődő sikertelen kérelmek megválaszolására a kapcsolat fenntartása mellett a figyelő első tiltólistán eredményezhet.

Válaszok küldhetők előfordulhat, hogy bármilyen sorrendben, de az egyes kérések kell válaszolt 60 másodpercen belül, vagy kézbesítését, hogy nem lesz jelentve. Amíg a 60 másodperces határidő után kell fizetnie a `response` keret érkezett a szolgáltatás által. Nem bináris több képkockát tartalmazó folyamatban lévő választ több mint 60 másodpercig inaktív válhat, vagy megszakad.

A kérelem érkezik a vezérlő csatornán, ha a válasz kell vagy küldeni a vezérlőcsatornán, ahol a kérés érkezett, vagy el kell küldenie egy szinkronizálási csatornán.

A válasz a "response" nevű JSON-objektum. A kérelemtörzs-tartalom kezelésében a szabályok vannak például pontosan a `request` üzenet, és alapján a `body` tulajdonság.

* **Kérelemazonosító: %** – karakterlánc. SZÜKSÉGES. A `id` tulajdonság értékét a `request` üzenet válaszolt folyamatban van.
* **statusCode** – száma. SZÜKSÉGES. a numerikus HTTP-állapotkódot, amely azt jelzi, hogy az értesítés kimenetelét. Az összes állapotkódok [RFC7231, 6. szakasz](https://tools.ietf.org/html/rfc7231#section-6) engedélyezve vannak, az alábbiakat kivéve [502 "Hibás átjáró"](https://tools.ietf.org/html/rfc7231#section-6.6.3) és [504 "időtúllépés"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** -karakterlánc. NEM KÖTELEZŐ. HTTP-állapotkód indoklás kiszolgálónként [RFC7230, szakasz 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – HTTP-fejléceket, a külső HTTP-válasz kell beállítani.
  Ahogy a `request`, RFC7230 előre definiált fejléc adható nem használható.
* **törzs** – logikai. Azt jelzi, hogy a szervezet bináris kocka follow(s).

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

##### <a name="responding-via-rendezvous"></a>Szinkronizálási keresztül válaszol

A 64 KB-os keretet túllépő válaszok a válasz megcélozva szoftvercsatornán keresztül tesz elérhetővé. Is, ha a kérés túllépi a 64 KB-os, és a `request` csak tartalmazza a cím mező megcélozva szoftvercsatorna beszerzése kell létrehozni a `request`. Egy szinkronizálási szoftvercsatorna létrehozása után, hogy az ügyfél megfelelő parancsválaszait az érintett ügyfél és a későbbi kérelmeket érkeznek a szinkronizálási szoftvercsatornán keresztül miközben ez továbbra is fennáll.

A `address` URL-címet a `request` kell használni-létrehozásához szükséges a szinkronizálási szoftvercsatorna, de az alábbi paramétereket tartalmazza:

| Paraméter      | Szükséges | Leírás
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Igen      | A szoftvercsatorna fogadásához, a paraméternek kell lennie `sb-hc-action=request`

Ha hiba történik, a szolgáltatás is válasz a következő:

| Kód | Hiba           | Leírás
| ---- | --------------- | -----------------------------------
| 400  | Érvénytelen kérelem | Ismeretlen művelet vagy URL-cím nem érvényes.
| 403  | Tiltott       | Az URL-cím elévült.
| 500  | Belső hiba  | Hiba történt a szolgáltatásban

 A kapcsolat létrejötte után a kiszolgáló leáll a WebSocket, amikor az ügyfél HTTP-szoftvercsatorna leállítja, vagy a következő állapotkóddal:

| WS-állapot | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | A küldő ügyfél leállítja a kapcsolatot.                                    |
| 1001      | A hibrid kapcsolat elérési törölték vagy le van tiltva.                        |
| 1008      | A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirend sérül. |
| 1011      | Probléma merült fel a szolgáltatásban.                                            |


#### <a name="listener-token-renewal"></a>Figyelő token megújítása

Ha a figyelő-jogkivonat hamarosan lejár, akkor lecserélheti keret szöveges üzenetet küld a meghatározott vezérlési csatornán keresztül a szolgáltatás. Az üzenet tartalmaz egy nevű JSON-objektum `renewToken`, amely a következő tulajdonság határozza meg most:

* **token** – egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési jogkivonatot a névtér vagy hibrid kapcsolatot, amely ruház a **figyelésére** megfelelő.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Ha a jogkivonat érvényesítése sikertelen, a hozzáférés megtagadva, és a felhőszolgáltatás bezárja a vezérlőcsatorna WebSocket egy hiba miatt. Ellenkező esetben nem érkezik válasz.

| WS-állapot | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirend sérül. |

### <a name="web-socket-connect-protocol"></a>Webes szoftvercsatorna csatlakoztatása protokoll

A küldő protokoll hatékonyan megegyezik a módját, létrejön egy figyelőt.
A cél, a teljes körű websocket teljes átláthatóságát. Szeretne csatlakozni a cím ugyanaz, mint a figyelő, de az "action" eltér, és a tokent egy másik engedélyre van szüksége:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

A _névtér-cím_ a teljesen minősített tartományneve az Azure Relay-névteret, amely futtatja a hibrid kapcsolatot, általában a következő formában `{myname}.servicebus.windows.net`.

A kérés tartalmazhat tetszőleges további HTTP-fejlécek található, beleértve az alkalmazás által meghatározott azokat. Az összes megadott fejlécek áramlanak a figyelő a, és találhatók a `connectHeader` objektumát a **fogadja el** felügyeletének üzenetében.

A lekérdezési karakterlánc paraméter beállítások a következők:

| Param          | Kötelező? | Leírás
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Igen       | A küldő szerepkörhöz a paraméter lehet `sb-hc-action=connect`.
| `{path}`       | Igen       | (lásd a következő bekezdés)
| `sb-hc-token`  | igen\*     | A figyelő adjon meg egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési tokent a névtér vagy hibrid kapcsolatot, amely ruház a **küldése** megfelelő.
| `sb-hc-id`     | Nem        | Nem kötelező azonosítója, amely lehetővé teszi, hogy a teljes körű diagnosztikai nyomkövetés, és szeretné elérhetővé tenni a figyelővel az accept-kézfogás során.

 A `{path}` , amelyen ez a figyelő regisztrálása a előre konfigurált hibrid kapcsolat URL-kódolású névtér elérési útját. A `path` kifejezés utótag és a egy lekérdezési karakterlánc-kifejezés való kommunikációhoz további ki lehet terjeszteni. Ha a hibrid kapcsolat regisztrálva van az elérési út alatt `hyco`, a `path` kifejezés lehet `hyco/suffix?param=value&...` követi az itt megadott lekérdezési karakterlánc paraméterei. Egy teljes kifejezés majd a következő lehet:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

A `path` kifejezés kerül át a figyelő az "elfogadás" ellenőrző üzenet található URI-címét.

A WebSocket-kapcsolatot a hibrid kapcsolat elérési út nem regisztrált, egy érvénytelen vagy hiányzó jogkivonatot vagy más hiba miatt nem sikerül, ha a hiba visszajelzés érkezett a HTTP 1.1-es állapot rendszeres visszajelzés modell használatával. Az állapot leírása tartalmazza a követési azonosító: tájékoztatni kell, hogy az Azure-támogatási személyzet hiba:

| Kód | Hiba          | Leírás
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nem található      | A hibrid kapcsolat elérési út érvénytelen, vagy az alap URL-cím formátuma.
| 401  | Nem engedélyezett   | A biztonsági token hiányzik vagy helytelen formátumú vagy érvénytelen.
| 403  | Tiltott      | A biztonsági token nem érvényes ehhez az elérési úthoz, és ez a művelet.
| 500  | Belső hiba | Probléma merült fel a szolgáltatásban.

Ha a WebSocket kapcsolaton szándékosan a szolgáltatás által után leáll, először be lett állítva, így a megfelelő WebSocket protokoll hibakódot és a egy részletes hibaüzenet, amely szintén tartalmazza a követési azonosító használatával továbbítsák az az oka .

| WS-állapot | Leírás
| --------- | ------------------------------------------------------------------------------- 
| 1000      | A figyelő a szoftvercsatorna leállítása.
| 1001      | A hibrid kapcsolat elérési törölték vagy le van tiltva.
| 1008      | A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirend sérül.
| 1011      | Probléma merült fel a szolgáltatásban.

### <a name="http-request-protocol"></a>HTTP-kérelem protokoll

A HTTP-kérelem protokoll lehetővé teszi tetszőleges HTTP-kérelmekre, kivéve a protokoll-frissítések.
A HTTP-kérések címen az entitás rendszeres runtime, anélkül, hogy a WebSocket-ügyfelek a hibrid kapcsolatokhoz használt $hc vpony vannak mutatott.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

A _névtér-cím_ a teljesen minősített tartományneve az Azure Relay-névteret, amely futtatja a hibrid kapcsolatot, általában a következő formában `{myname}.servicebus.windows.net`.

A kérés tartalmazhat tetszőleges további HTTP-fejlécek található, beleértve az alkalmazás által meghatározott azokat. Az összes megadott fejlécek, kivéve azokat közvetlenül meghatározott RFC7230 (lásd: [kérelemüzenet](#Request message)) áramlanak a figyelő a és találhatók a `requestHeader` objektumát a **kérelem** üzenet.

A lekérdezési karakterlánc paraméter beállítások a következők:

| Param          | Kötelező? | Leírás
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | igen\*     | A figyelő adjon meg egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési tokent a névtér vagy hibrid kapcsolatot, amely ruház a **küldése** megfelelő.

A jogkivonat is is kell végezni, akár a `ServiceBusAuthorization` vagy `Authorization` HTTP-fejléc. A jogkivonat elhagyható, ha a hibrid kapcsolat beállításai lehetővé teszi a névtelen kérelmek.

Mivel a szolgáltatás hatékonyan proxyként működik, akkor is, ha igaz HTTP-proxyt, nem pedig vagy hozzáadja egy `Via` fejléc jelzi, hogy a meglévő vagy `Via` felelnek meg a fejléc [RFC7230, szakasz 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
A szolgáltatás hozzáadja a Relay-névtér állomásnév való `Via`.

| Kód | Üzenet  | Leírás                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | A kérelem legalább egy figyelő által kezelve van.  |
| 202  | Elfogadva | A kérelmet elfogadta legalább egy figyelőt. |

Ha hiba történik, a szolgáltatás válaszolhatnak módon. Származik-e a válasz a a szolgáltatásból vagy a figyelő jelenléte segítségével azonosíthatók a `Via` fejléc. Ha a fejlécben található, a válasz a figyelő származik.

| Kód | Hiba           | Leírás
| ---- | --------------- |--------- |
| 404  | Nem található       | A hibrid kapcsolat elérési út érvénytelen, vagy az alap URL-cím formátuma.
| 401  | Nem engedélyezett    | A biztonsági token hiányzik vagy helytelen formátumú vagy érvénytelen.
| 403  | Tiltott       | A biztonsági token nem érvényes ehhez az elérési úthoz, és ez a művelet.
| 500  | Belső hiba  | Probléma merült fel a szolgáltatásban.
| 503  | Hibás átjáró     | A kérelem nem lesznek irányítva minden olyan figyelőt.
| 504  | Időtúllépés az átjárón | A kérelem egy figyelő lett irányítva, de a figyelő nem nyugtázta fogadását a szükséges időt.

## <a name="next-steps"></a>További lépések

* [Relay – gyakori kérdések](relay-faq.md)
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)
