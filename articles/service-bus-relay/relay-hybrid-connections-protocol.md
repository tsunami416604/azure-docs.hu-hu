---
title: Az Azure hibrid kapcsolatok protokoll útmutató |} Microsoft Docs
description: Az Azure hibrid kapcsolatok protokoll útmutató.
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
ms.openlocfilehash: 306a21add76261dce99c954a2ba373e4b5047a75
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Az Azure hibrid kapcsolatok protokoll

Az Azure továbbítási az Azure Service Bus platform a fő funkció oszlopok egyike. Az új _hibrid kapcsolatok_ továbbító egy biztonságos, nyílt-protokoll alakulása a HTTP és a websocket elemek alapján. Azt írja felül a volt, ugyanilyen nevű _BizTalk szolgáltatások_ funkciója, amely a saját fejlesztésű protokollja alaprendszert lett létrehozva. Hibrid kapcsolatok integrálása Azure App Service szolgáltatások továbbra is működjön-van.

Hibrid kapcsolatok lehetővé teszi a kommunikáció kétirányú, bináris adatfolyam és a datagram egyszerű folyamat két hálózati alkalmazások között. Egyik vagy mindkét fél NAT vagy tűzfal mögött találhatók.

Ez a cikk ismerteti a hibrid kapcsolatok relay csatlakozó ügyfeleken figyelő és a küldőt szerepkörökben ügyféloldali interakció. Azt is bemutatja, hogyan figyelői fogadni új kapcsolatokat és a kérelmek.

## <a name="interaction-model"></a>Interakció modell

A hibrid kapcsolatok továbbító csatlakozik a két fél Azure felhőben felek felderítése és a saját hálózati szempontból csatlakozni a szinkronizálási pont megadásával. A szinkronizálási pont "Hibrid kapcsolat" Ebben és egyéb dokumentációt neve API-kban és az Azure portálon. A hibrid kapcsolatok szolgáltatási végpont a "szolgáltatás" Ez a cikk a többi nevezzük.

A szolgáltatás lehetővé teszi, hogy továbbításához webes szoftvercsatorna-kapcsolat és a HTTP (S) kérelmeit és válaszait.

A kommunikáció modell leans a sok más hálózati API-k által létrehozott. Egy figyelő, amely először azt jelzi, hogy készültségi bejövő kapcsolat kezelésére, és ezt követően fogadja el őket, akkor van. A másik oldalon egy ügyfél kapcsolódik, a figyelő vár, hogy kapcsolatot létrehozó kétirányú kommunikáció elérési elfogadja felé. "Csatlakozás", "Figyelés" és "Elfogadása" is az azonos feltételeket legtöbb szoftvercsatorna API-kat is megtalálhatók.

A továbbítón keresztüli kommunikáció modellnek vagy fél kimenő kapcsolatok szolgáltatásvégpont felé. Ez lehetővé teszi a "figyelő" is "ügyfél" köznyelvi használja, és más terminológiát túlterhelések okozhat. A pontos terminológia ezért hibrid kapcsolatok a következőképpen történik:

A kapcsolat mindkét oldalán programok "ügyfelek," nevezzük, mivel azok az ügyfelek számára a szolgáltatás. Megvárja, és a kapcsolatokat fogad ügyfél a "figyelő", vagy a "figyelő szerepkörben.", különállónak Az ügyfélnek, amelyik a szolgáltatáson keresztül figyelő felé új kapcsolatot kezdeményez a "feladó" nevezik, vagy a "feladó szerepkör."

### <a name="listener-interactions"></a>Figyelő interakciók

A figyelő rendelkezik a szolgáltatás; öt interakciók összes átviteli részletes ismertetett referencia című cikkben.

A figyelés, elfogadás és kérelem üzenetek a szolgáltatástól érkező. A megújítás, és a figyelő által küldött Ping műveletek.

#### <a name="listen-message"></a>Figyelési üzenet

Jelzi a szolgáltatás, amely egy figyelő készen állnak készen áll kapcsolatok fogadására létrehoz egy kimenő WebSocket-kapcsolat. A kapcsolati kézfogás hordoz magában, ha a továbbítási névteret, és egy biztonsági jogkivonatot, amely a "figyelés" jogot a ruház neve konfigurálva hibrid kapcsolat neve.

A WebSocket a szolgáltatás által elfogadható, ha a regisztrálása sikeresen befejeződött, és a meglévő WebSocket életben csatornaként"vezérlő" minden ezt követő kapcsolati engedélyezésének megőrzi. A szolgáltatás lehetővé teszi, hogy legfeljebb 25 párhuzamos figyelők egy hibrid kapcsolat. A kvóta AppHooks meghatározott van.

A hibrid kapcsolatok Ha két vagy több aktív figyelők, a bejövő kapcsolatok elosztását ezek között véletlenszerű sorrendben; igazságos elosztási kísérlet történik a lehető legkedvezőbb módon.

#### <a name="accept-message"></a>Üzenet elfogadása

A küldő a szolgáltatás az új kapcsolat megnyitása után a szolgáltatás úgy dönt, és értesíti a hibrid kapcsolat aktív figyelőinek egyikét. A rendszer értesítést küld a figyelő a nyitott vezérlő csatornán JSON üzenetben. Az üzenet tartalmaz, amelyek a figyelő csatlakoztatni kell a kapcsolat elfogadása a WebSocket-végpont URL-CÍMÉT.

Az URL-címet is, és közvetlenül a figyelő extra munka nélkül kell használniuk.
A kódolt információt idő, lényegében addig, amíg a küldője várja meg a meghatározott-végpont lehet csatlakozni kívánó rövid ideig csak esetén érvényes. Tegyük fel, hogy a maximális érték 30 másodperc. Az URL-cím csak egy sikeres kapcsolódási kísérlet portbesorolása. Amint a szinkronizálási URL-címet a WebSocket-kapcsolat létrejött, a WebSocket minden további tevékenység a kezdő és a küldő a továbbítón keresztüli. Ez akkor fordul elő értelmezése és beavatkozás nélkül a szolgáltatás.

### <a name="request-message"></a>Kérelemüzenet

WebSocket-kapcsolatokat, mellett a figyelő is fogadhat HTTP-kérelem keretek a küldőhöz, ha ezt a képességet explicit módon engedélyezve van a hibrid kapcsolat.

Amely a HTTP-támogatás a hibrid kapcsolatok csatlakoztatni figyelőként kezelni kell a `request` hitelesítési mód. Egy figyelő, amely nem kezeli az `request` , és ezért leállítja az ismétlődő időtúllépést közben való csatlakozás is lehet feketelistára teszi a szolgáltatás által a jövőben.

HTTP keretet fejlécet metaadatok lefordítását JSON egyszerűbb célból figyelő keretrendszer is mert HTTP-fejléc elemzési szalagtárak ritkább JSON elemzők-nál. Csak a feladó és engedélyezési információ, beleértve a továbbítási HTTP átjáró közötti kapcsolathoz megfelelő HTTP metaadatokat a rendszer nem továbbítja. HTTP-kérelem szervek transzparens módon bináris WebSocket keretek kerülnek át.

A figyelő tud válaszolni egy egyenértékű válasz kézmozdulattal HTTP-kérelmekre.

A kérelem/válasz folyamat alapértelmezés szerint a vezérlőcsatorna használ, de "frissíthető" a különálló szinkronizálási WebSocket, amikor szükséges. Különböző WebSocket-kapcsolatokat javítása minden ügyfél beszélgetés adatátviteli sebességét, de azok terheljék további kapcsolatokat kell kezelni, a figyelő nem lehet, amelyek egyszerűsített ügyfelek képesek desire.

A vezérlőcsatornán kérelem-válasz törzsek legfeljebb 64 kB méretű korlátozódik. HTTP-fejléc metaadatai nem legfeljebb 32 KB. Ha a kérés vagy válasz hossza meghaladja a küszöbértéket, a figyelő verzióra kell frissíteni a szinkronizálási WebSocket egy kézmozdulattal kezelési egyenértékű a [elfogadás](#accept-message).

A kéréseket a szolgáltatás úgy dönt, hogy a vezérlő csatornán kérelmeket. Ez magában foglalja, de nem korlátozható azokra az esetekre, ha a kérelem meghaladja a 64 KB-os (fejlécek és body) végleges, vagy ha a kérelem nem jut ["darabolásos" transfer-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) , és a szolgáltatás a kérelem legfeljebb 64 KB lehet várt vagy a kérelem olvasása nincs azonnali. Ha a szolgáltatás úgy dönt, hogy a kérelmet képes szinkronizálási keresztül, a szinkronizálási címet csak a figyelő továbbítja.
A figyelő majd kell létesítenie a szinkronizálási WebSocket, és a szolgáltatás azonnal továbbítja a teljes kérést, beleértve a szervezetek a szinkronizálási WebSocket keresztül. A válasz a szinkronizálási WebSocket is használnia kell.

A vezérlő csatornán keresztül érkező kérelmeket, az a figyelő úgy dönt, hogy a vezérlő csatornán vagy szinkronizálási keresztül válaszol-e. A szolgáltatás a szinkronizálási címet tartalmaznia kell a vezérlőcsatorna útválasztása minden kérelemhez. Ez a cím csak érvényes az aktuális kérelem verzióról.

A figyelő úgy dönt, hogy frissítse, ha csatlakozik, és azonnal továbbítja a válasz a meglévő szinkronizálási szoftvercsatorna keresztül.

Egyszer a szinkronizálási WebSocket lett létrehozva, a figyelő kell fenntartásához további kérések és válaszok az azonos ügyféltől kezelésére. A szolgáltatás a WebSocket mindaddig, amíg a HTTPS-kapcsolatot a küldő szoftvercsatorna továbbra is fennáll, és a karbantartott WebSocket keresztül átirányítja az összes további kérelmet feladó meddig tárolja. Ha a figyelő úgy dönt, hogy a szinkronizálási WebSocket eldobni az oldalról, a szolgáltatás is dobja el a kapcsolatot a küldő, függetlenül attól, hogy egy későbbi kérés már lehetnek folyamatban.

#### <a name="renew-operation"></a>Újítsa meg a műveletet

A biztonsági jogkivonatot kell használni a figyelő regisztrálása és karbantartása a vezérlőcsatorna előfordulhat, hogy lejár, amíg a figyelő aktív. A jogkivonat lejáratának nem befolyásolja a folyamatban lévő kapcsolatok, de ennek következtében a vezérlőcsatorna, illetve hamarosan lejáró időpontjában után a szolgáltatás megszakad. A "megújítása" művelet egy JSON-üzenetet, amely a figyelő küldhet, hogy a vezérlőcsatorna hosszabb ideig is megőrizhető, cserélje le a jogkivonatot a vezérlőcsatorna társított.

#### <a name="ping-operation"></a>Pingelési művelet

Ha hosszú ideje, a a módszerrel a közvetítők tétlen marad a vezérlőcsatorna terhelés például terheléselosztó vagy NAT dobhatja TCP-kapcsolatot. A "ping" művelet elkerülhető, hogy úgy, hogy a csatornán, is emlékezteti, hogy a kapcsolat az célja, hogy kell életben, és azt is szolgál egy "élő" tesztet a figyelő a hálózati útvonalhoz mindenki kisebb mennyiségű adatot küld. Ha a pingelés nem sikerül, a vezérlőcsatorna érdemes figyelembe venni használható, és a figyelő elméletileg újracsatlakozik.

### <a name="sender-interaction"></a>Küldő interakció

A feladó rendelkezik-e a szolgáltatás két interakció: webes szoftvercsatorna csatlakozik, vagy HTTPS használatával kérelmeket küld. Kérelmek nem lehet elküldeni egy webes szoftvercsatorna keresztül a küldő szerepkörből.

#### <a name="connect-operation"></a>Kapcsolódási műveletet.

A "Csatlakozás" művelet megnyitja a WebSocket szolgáltatás, a nevét, a hibrid kapcsolat és (ha szükséges, de alapértelmezés szerint) a biztonsági token eredményező "Send" engedéllyel a lekérdezési karakterláncban. A szolgáltatás majd kommunikál a figyelő a korábban ismertetett módon, és a figyelő létrehoz egy szinkronizálási kapcsolat, amely a WebSocket kapcsolódik. A WebSocket elfogadását követően az adott WebSocket minden további interakciók vannak egy csatlakoztatott figyelő.

#### <a name="request-operation"></a>Kérési művelet

A hibrid kapcsolatok, amelynek a szolgáltatás engedélyezve van a küldő küldhet nagymértékben korlátlan HTTP-kérelmek figyelők.

A továbbító hozzáférési jogkivonatot, amely akár a lekérdezési karakterláncban vagy a kérelem HTTP-fejlécekben beágyazott, kivéve a továbbítási teljesen átlátható a továbbítási cím minden HTTP-műveletek és a továbbítási cím elérési út minden utótagok hagyja a figyelő teljesen en ellenőrzi az d körű hitelesítést és a HTTP bővítmény még akkor is, szolgáltatások, mint [CORS](https://www.w3.org/TR/cors/).

A továbbító végponttal küldő engedélyezési alapértelmezés szerint be van kapcsolva, de nem kötelező. A hibrid kapcsolat tulajdonosának beállíthatja a névtelen feladók engedélyezése. A szolgáltatás intercept, vizsgálja meg, és engedélyezési információ sáv az alábbiak szerint:

1. Ha a lekérdezési karakterláncot tartalmaz egy `sb-hc-token` kifejezését kifejezés mindig kell tisztító lekérdezési karakterlánc. Ha a továbbítási engedélyezési be van kapcsolva történik.
2. Ha a kérelem fejlécében tartalmaz egy `ServiceBusAuthorization` fejléc, a fejléc kifejezés mindig kell tisztító a fejléc gyűjteményből.
   Ha a továbbítási engedélyezési be van kapcsolva történik.
3. Csak akkor, ha a továbbítási engedélyezési be van kapcsolva, és ha a kérelem fejlécében tartalmaz egy `Authorization` fejlécet, és a korábbi kifejezések egyike sem található, a fejlécben a rendszer értékeli ki, majd a tisztító. Ellenkező esetben a `Authorization`mindig átadódik-van.

Ha nincs aktív figyelő, a szolgáltatás visszaadható egy "Hibás átjáró" 502 hibakód. Ha a szolgáltatás nem jelenik meg a kérelem kezelésére, akkor a szolgáltatás 504 "átjáró időtúllépése" vissza 60 másodperc múlva.

### <a name="interaction-summary"></a>Interakció összefoglaló

Ez a kommunikáció modell eredménye, hogy a küldő ügyfél származik-e a "tiszta" WebSocket, amelyek csatlakoztatva van egy figyelő, és további preambulumok vagy előkészítési nem igénylő kézfogást kívül. Ez a modell lehetővé teszi gyakorlatilag bármely létező WebSocket ügyfél megvalósítását, hogy könnyen előnyeit a hibrid kapcsolatok szolgáltatás úgy, hogy megadja a megfelelően felépített URL-címet a WebSocket ügyfél rétegbe.

A szinkronizálási kapcsolat, amely a figyelő olvassa be a elfogadás interakció keresztül WebSocket is tiszta, és minden meglévő WebSocket server végrehajtása néhány minimális extra absztrakciós, amelyik megkülönbözteti az "elfogadás" műveleteket végez a keretrendszer helyi hálózati figyelők és a hibrid kapcsolatok távoli "elfogadás" műveletek között is kell adni.

A HTTP-kérelem-válasz modellt biztosít a küldő nagymértékben korlátlan HTTP protokoll terület egy választható engedélyezési réteggel. A figyelő lekérdezi egy előre elemzett HTTP kérelem fejlécszakasza, amely bekapcsolja az alsóbb rétegbeli HTTP-kérelem vagy megfelelően van, a bináris keretek végrehajtsa HTTP szervek kezeli. Válaszok ugyanazt a formátumot használja. Kisebb, mint 64 KB-os kérelem-válasz törzsében interakció, amelyet az összes feladó egyetlen webes foglalaton keresztül lehet kezelni. Nagyobb kérelmeit és válaszait a szinkronizálási modell használatával lehet kezelni.

## <a name="protocol-reference"></a>Protokoll referenciája

Ez a szakasz ismerteti a fentiekben ismertetett protokoll kapcsolati adatait.

Az összes WebSocket-kapcsolat készült a 443-as porton frissítéseként HTTPS 1.1, amelyeket gyakran néhány WebSocket keretrendszer vagy API-val. A leírását tartják megvalósítási semleges, anélkül, hogy egy adott keretrendszert javasolhat.

### <a name="listener-protocol"></a>Figyelő protokoll

A figyelő protokoll két kapcsolat kézmozdulatok és három üzenetművelet áll.

#### <a name="listener-control-channel-connection"></a>Figyelő vezérlő adatcsatorna-kapcsolatot

A vezérlőcsatorna, ahol a WebSocket-kapcsolat létrehozása:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

A `namespace-address` teljesen minősített tartományneve, amelyen a hibrid kapcsolatot, általában az űrlap Azure továbbítási névtér `{myname}.servicebus.windows.net`.

A lekérdezési karakterlánc paraméter lehetőségek a következők:

| Paraméter        | Szükséges | Leírás
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Igen      | A figyelő szerepkörhöz a paramétert kell **sb-hc-művelet figyelési =**
| `{path}`         | Igen      | Az előre konfigurált hibrid kapcsolat regisztrálni. Ez a figyelő URL-kódolású névtér elérési útját. A kifejezés a rendszer hozzáfűzi a rögzített `$hc/` elérési út részével.
| `sb-hc-token`    | igen\*    | A figyelő adjon meg egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési jogkivonat a névtér vagy a hibrid kapcsolat, amely ruház a **figyelésére** jobb.
| `sb-hc-id`       | Nem       | Az ügyfél által megadott választható azonosítója lehetővé teszi, hogy a végpont a diagnosztikai nyomkövetés.

A WebSocket-kapcsolat sikertelen a hibrid kapcsolat elérési út nem regisztrált, vagy egy érvénytelen vagy hiányzó jogkivonatot, vagy valamilyen egyéb hiba miatt, ha a hiba visszajelzés érkezett a HTTP 1.1 állapot rendszeres visszajelzés modell használatával. Az állapot leírása hiba követési-azonosítót tartalmaz, amely az Azure támogatási személyzet tájékoztatni lehet:

| Kód | Hiba          | Leírás
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nem található      | A hibrid kapcsolat elérési út érvénytelen, vagy az alap URL-cím formátuma hibás.
| 401  | Nem engedélyezett   | A biztonsági token hiányzik vagy helytelen formátumú vagy érvénytelen.
| 403  | Tiltott      | A biztonsági token érvénytelen ehhez az elérési úthoz ehhez a művelethez.
| 500  | Belső hiba | Valami hiba történt a szolgáltatásban.

Ha a WebSocket-kapcsolat szándékosan állítja le a szolgáltatás után kezdetben hozták létre, így a információcserére kerül sor, használja a megfelelő WebSocket protokoll hibakódot együtt egy is magában foglalja a nyomkövetési azonosítót. részletes hibaüzenet az az oka A szolgáltatás nem állítja le a vezérlőcsatorna hibaállapotot elolvasása nélkül. A tiszta Leállítás utáni a felügyelt ügyfél.

| WS-állapot | Leírás
| --------- | -------------------------------------------------------------------------------
| 1001      | A hibrid kapcsolati útvonal törölve lett vagy le van tiltva.
| 1008      | A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirendben sérül.
| 1011      | Valami hiba történt a szolgáltatásban.

#### <a name="accept-handshake"></a>Fogadja el a kézfogás

A "elfogadása" rendszer értesítést küld a szolgáltatás által a figyelő a korábban létrehozott vezérlő csatornán WebSocket szöveg keretben JSON üzenetben. Nincs válasz ezzel az üzenettel.

Az üzenet JSON objektumot tartalmaz "elfogadás" nevű meghatározó jelenleg a következő tulajdonságokkal:

* **cím** – az URL karakterlánc használandó a szolgáltatásnak a WebSocket létrehozása egy bejövő kapcsolatok fogadására.
* **azonosító** – az ehhez a kapcsolathoz tartozó egyedi azonosító. Az azonosító a küldő ügyfél által biztosított, ha a feladó megadott érték, különben a rendszer által létrehozott értéket.
* **connectHeaders** – meg van adva a továbbítási végpontra a feladó, amely magában foglalja az mp-WebSocket-protokoll és az mp-WebSocket-bővítmények fejlécek HTTP-fejlécek.

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

Cím URL-cím a JSON-üzenetben a figyelő létrehozására szolgál a elfogadása vagy elutasítása a küldő szoftvercsatorna WebSocket.

##### <a name="accepting-the-socket"></a>A szoftvercsatorna elfogadásakor

Fogadja el, hogy a figyelő a megadott cím WebSocket kapcsolat jön létre.

Ha a "elfogadása" üzenetének egy `Sec-WebSocket-Protocol` fejléc, várható, hogy a figyelő csak elfogadja a WebSocket, ha az támogatja-e a protokoll. Emellett a fejléc állítja a, a WebSocket jön létre.

Ugyanez érvényes a `Sec-WebSocket-Extensions` fejléc. Ha a keretrendszer egy bővítmény támogatja, a fejléc azt úgy beállítani, hogy a szükséges a kiszolgálóoldali válasz `Sec-WebSocket-Extensions` kézfogás a bővítmény.

Az URL-címet kell használni, mint-létrehozásához az elfogadás szoftvercsatorna, de tartalmazza a következő paramétereket:

| Paraméter      | Szükséges | Leírás
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Igen      | A szoftvercsatorna elfogadásakor, a paraméternek kell lennie `sb-hc-action=accept`
| `{path}`       | Igen      | (lásd a következő)
| `sb-hc-id`     | Nem       | Tekintse meg az előző leírása **azonosító**.

`{path}` az URL-kódolású névterek elérési útjai, amelyen ez a figyelő regisztrálása az előre konfigurált hibrid kapcsolat van. A kifejezés a rendszer hozzáfűzi a rögzített `$hc/` elérési út részével.

A `path` kifejezés ki, amelynek utótag és a regisztrált név elválasztó perjellel után a következő lekérdezési karakterlánc-kifejezés.
Ez lehetővé teszi, hogy a küldő ügyfél feladó argumentumok átadása a átvevő figyelő, amikor nincs lehetőség a HTTP-fejlécek. Az elvárás, hogy a figyelő keretrendszer elemzi a rögzített elérési út részével és a regisztrált név elérési úton található, és valószínűleg lehetővé teszi a fennmaradó által meghatározott lekérdezési karakterlánc argumentum nélkül `sb-`, eldönti, hogy fogadja el a kapcsolatot az alkalmazás számára elérhető.

További információkért lásd a következő "Küldő protokoll".

Ha nem sikerül, a szolgáltatás válaszolhatnak az alábbiak szerint:

| Kód | Hiba          | Leírás
| ---- | -------------- | -----------------------------------
| 403  | Tiltott      | Az URL-cím érvénytelen.
| 500  | Belső hiba | Hiba történt a szolgáltatásban

 A kapcsolat létrejötte után a kiszolgáló leáll a WebSocket, amikor a küldő WebSocket kikapcsolják, vagy a következő állapottal:

| WS-állapot | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | A küldő ügyfél leállítja a kapcsolatot.                                    |
| 1001      | A hibrid kapcsolati útvonal törölve lett vagy le van tiltva.                        |
| 1008      | A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirendben sérül. |
| 1011      | Valami hiba történt a szolgáltatásban.                                            |

##### <a name="rejecting-the-socket"></a>A szoftvercsatorna elutasítása

 A szoftvercsatorna elutasítása ellenőrzése után a `accept` üzenet megköveteli a hasonló kézfogás, amelyen az állapotkódot és a kommunikáció a OK elutasítását áramolhasson az állapot leírása a küldőnek.

 A protokoll tervezési szempontokat itt, hogy a WebSocket-kézfogás (, amely egy meghatározott hibaállapot végződésű) használja, hogy a figyelő ügyfélmegvalósítások továbbra is a WebSocket ügyfél alapulnak, és nem kell egy plusz alkalmaz, a HTTP-alapú operációs rendszer.

 A szoftvercsatorna, hogy az ügyfél által URI-cím a a `accept` üzenet, és hozzáfűzi a lekérdezési karakterlánc-paraméterrel, az alábbiak szerint:

| Param                   | Szükséges | Leírás                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB-hc-statusCode        | Igen      | Numerikus HTTP-állapotkódot.                |
| SB-hc-statusDescription | Igen      | Az elutasítás emberi olvasható oka. |

Az eredményül kapott URI majd WebSocket-kapcsolat létrehozásához használt.

Megfelelően befejezése a kézfogás szándékosan sikertelen lesz, hibakódú HTTP 410, mivel nem WebSocket létrehozása. Ha valamilyen hiba, a következő kódokkal mutatják be, a hiba:

| Kód | Hiba          | Leírás                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Tiltott      | Az URL-cím érvénytelen.                |
| 500  | Belső hiba | Valami hiba történt a szolgáltatásban. |

#### <a name="request-message"></a>Kérelemüzenet

A `request` üzenetet küld a szolgáltatás által a figyelő a vezérlő csatornán keresztül. Ugyanazt az üzenetet is lett van küldve a szinkronizálási után WebSocket keresztül.

A `request` két részből áll: egy fejléc és a bináris törzs kocka.
Ha törzs, a szervezet keretek hiányoznak. A kijelző e törzs az jelen a logikai `body` tulajdonság request üzenetben.

Rendelkező kérelmek esetében egy kérelemtörzset a struktúra néz ki:

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

A figyelő kell kezelni, a kérelem törzsében-e osztani több bináris keretek fogadása (lásd: [WebSocket töredék](https://tools.ietf.org/html/rfc6455#section-5.4)).
A kérelem véget ér, amikor a FIN jelzőjének beállítása a bináris keret érkezett.

A törzs nélküli kérelem csak egy szöveges keret van.

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

A JSON-tartalmak `request` a következőképpen történik:

* **cím** -URI-karakterlánc. Ez az a szinkronizálási címet ehhez a kérelemhez használatára. Ha a bejövő kérelem 64 kB-nál nagyobb, ez az üzenet további része üresen marad, és az ügyfél a megfelelő szinkronizálási kézfogás kell kezdeményezni a `accept` alább ismertetett művelet. A szolgáltatás majd tegye a teljes `request` a meglévő webes szoftvercsatornán. A válasz várhatóan legfeljebb 64 KB lehet, ha a figyelő kell is kezdeményezheti a szinkronizálási kézfogás, és a válasz majd átvitel a meglévő webes szoftvercsatorna.
* **azonosító** – karakterlánc. A kérelemhez tartozó egyedi azonosítója.
* **requestHeaders** – Ez az objektum tartalmaz, amely meg van adva a végpontnak a feladó, hitelesítési információ kivétellel leírtak szerint az összes HTTP-fejlécek [fent](#request-operation), és a fejlécek szigorúan kapcsolódik a az átjáró-kapcsolatot. Pontosabban, minden fejléc definiálva, vagy a fenntartott [RFC7230](https://tools.ietf.org/html/rfc7230), kivéve `Via`, tisztító és nem továbbítja:

  * `Connection` (RFC7230, szakasz 6.1)
  * `Content-Length`  (RFC7230, 3.3.2 szakaszát)
  * `Host`  (RFC7230, szakasz 5.4)
  * `TE`  (RFC7230, 4.3. szakasz)
  * `Trailer`  (RFC7230, szakasz 4.4)
  * `Transfer-Encoding`  (RFC7230, 3.3.1 szakaszát)
  * `Upgrade` (RFC7230, szakasz 6.7)
  * `Close`  (RFC7230, szakasz 8.1)

* **requestTarget** – karakterlánc. Ez a tulajdonság tárolja az ["Kérelem Target" (RFC7230, szakasz 5.3-as)](https://tools.ietf.org/html/rfc7230#section-5.3) a kérelem. Ez magában foglalja az üres karaktert törölni az összes lekérdezési karakterlánc része `sb-hc-` paraméterek előtaggal.
* **módszer** -karakterlánc. Ez a mód az a kérelem [RFC7231, szakasz 4](https://tools.ietf.org/html/rfc7231#section-4). A `CONNECT` metódus nem használható.
* **törzs** – logikai. Azt jelzi, hogy a következő intenzívebben bináris törzs keret egy.

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

A fogadó kell válaszolnia. A kapcsolat fenntartása mellett a kérelmek megválaszolásához ismételt hiba a figyelő első feketelistára teszi eredményezheti.

Válaszok küldhet bármilyen sorrendben, de az egyes kérelmek kell válaszolt 60 másodpercen belül, vagy a kézbesítését, hogy nem készül. A 60 másodperc határidőt csak akkor számít a `response` keret érkezett a szolgáltatás. A folyamatban lévő választ több bináris keretek nem üresjáratba több mint 60 másodpercen keresztül, vagy megszakad.

Ha a kérelem érkezik a vezérlő csatornán keresztül, a válasz kell vagy küldhető a vezérlőcsatornán a, ha a kérés érkezett, vagy szinkronizálási-csatornán keresztül kell elküldeni.

A válasz egy JSON-objektum "válasz" nevű. A szervezet tartalom szabályai vannak, mint a `request` üzenet, és alapján a `body` tulajdonság.

* **Kérelemazonosító** – karakterlánc. SZÜKSÉGES. A `id` tulajdonság értékének a `request` üzenet válaszol.
* **statusCode** – száma. SZÜKSÉGES. egy numerikus HTTP-állapotkód, amely jelzi, az értesítés kimenetelét. Az összes állapotkódok [RFC7231, 6. szakasz](https://tools.ietf.org/html/rfc7231#section-6) engedélyezettek, kivéve a [502 "Hibás átjáró"](https://tools.ietf.org/html/rfc7231#section-6.6.3) és [504 "átjáró időtúllépése"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** -karakterlánc. NEM KÖTELEZŐ. HTTP-állapotkód indoklás / [RFC7230, 3.1.2 szakasz](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – HTTP-fejlécek kell beállítani a külső HTTP-választ.
  Például a `request`, RFC7230 meghatározott fejlécek nem használható.
* **törzs** – logikai. Azt jelzi, hogy bináris törzs kocka follow(s).

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

##### <a name="responding-via-rendezvous"></a>Szinkronizálási válaszol

Olyan válaszok, amelyek mérete meghaladja a 64 kB a válasz egy szinkronizálási szoftvercsatorna keresztül érkeznek. Is, ha a kérelem meghaladja a 64 KB-os, és a `request` csak az a cím mezőt tartalmaz egy szinkronizálási szoftvercsatorna kell létrehozni az beszerzése a `request`. A szinkronizálási szoftvercsatorna létrehozása után válaszokat ad az érintett ügyfél és a későbbi kérelmeket, hogy megfelelő ügyfél kézbesíteni a szinkronizálási szoftvercsatorna keresztül miközben az továbbra is fennáll.

A `address` URL-címet a `request` kell használni-létrehozásához a szinkronizálási szoftvercsatorna, de tartalmazza a következő paramétereket:

| Paraméter      | Szükséges | Leírás
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Igen      | A szoftvercsatorna elfogadásakor, a paraméternek kell lennie `sb-hc-action=request`

Ha nem sikerül, a szolgáltatás válaszolhatnak az alábbiak szerint:

| Kód | Hiba           | Leírás
| ---- | --------------- | -----------------------------------
| 400  | Érvénytelen kérelem | Ismeretlen action vagy URL-címe nem érvényes.
| 403  | Tiltott       | Az URL-cím érvényessége lejárt.
| 500  | Belső hiba  | Hiba történt a szolgáltatásban

 A kapcsolat létrejötte után a kiszolgáló leáll a WebSocket, amikor az ügyfél HTTP szoftvercsatorna leáll, vagy a következő állapottal:

| WS-állapot | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | A küldő ügyfél leállítja a kapcsolatot.                                    |
| 1001      | A hibrid kapcsolati útvonal törölve lett vagy le van tiltva.                        |
| 1008      | A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirendben sérül. |
| 1011      | Valami hiba történt a szolgáltatásban.                                            |


#### <a name="listener-token-renewal"></a>Figyelő jogkivonat megújítási

Ha a figyelő-token érvényessége lejár, akkor lecserélheti keret szöveges üzenetet küld a szolgáltatás a kijelölt ellenőrzési csatornán keresztül. Az üzenet tartalmaz egy JSON-objektum neve `renewToken`, amely megadja, hogy a következő tulajdonság ekkor:

* **token** – egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési jogkivonat a névtér vagy a hibrid kapcsolat, amely ruház a **figyelésére** jobb.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Ha a jogkivonatok érvényesség-ellenőrzése sikertelen, nincs hozzáférése, és a felhőalapú szolgáltatás bezárja a vezérlőcsatorna WebSocket hiba történt. Ellenkező esetben nincs válasz.

| WS-állapot | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirendben sérül. |

### <a name="web-socket-connect-protocol"></a>Webes szoftvercsatorna csatlakozás protokoll

A küldő protokoll hatékonyan megegyezik a módot, létrejön egy figyelő.
A végpont WebSocket maximális átlátszóságának célja. Való csatlakozás címét ugyanúgy történik, mint a figyelő, de az "action" eltér, és a tokent egy másik engedélyre van szüksége:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

A _névtér-cím_ teljesen minősített tartományneve, amelyen a hibrid kapcsolatot, általában az űrlap Azure továbbítási névtér `{myname}.servicebus.windows.net`.

A kérelem tartalmazhat tetszőleges extra HTTP-fejlécek található, beleértve az alkalmazás által meghatározott néhányat a meglévők közül. Minden megadott fejléc a figyelő a áramló, és megtalálható a `connectHeader` objektum a **fogadja el** felügyeletének üzenetében.

A lekérdezési karakterlánc paraméter lehetőségek a következők:

| Param          | Kötelező? | Leírás
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Igen       | A küldő szerepkörhöz a paramétert kell `sb-hc-action=connect`.
| `{path}`       | Igen       | (lásd a következő)
| `sb-hc-token`  | igen\*     | A figyelő adjon meg egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési jogkivonat a névtér vagy a hibrid kapcsolat, amely ruház a **küldése** jobb.
| `sb-hc-id`     | Nem        | Egy nem kötelező Azonosítót, amely lehetővé teszi, hogy a végpont a diagnosztikai nyomkövetés és teszi elérhetővé a figyelő az elfogadás kézfogás során.

 A `{path}` URL-kódolású névtér elérési útja az előre konfigurált hibrid kapcsolat, amelyen ez a figyelő regisztrálása. A `path` kifejezés ki lehet terjeszteni utótag és a további kommunikációt a lekérdezési karakterlánc-kifejezés. Ha a hibrid kapcsolat regisztrálva van az elérési úthoz tartozó `hyco`, a `path` kifejezés lehet `hyco/suffix?param=value&...` az itt megadott lekérdezési karakterlánc paraméterekhez követ. Egy teljes kifejezést majd a következő lehet:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

A `path` az átadott kifejezés keresztül a figyelő a címben URI vezérlő "elfogadása" üzenet.

A WebSocket-kapcsolat sikertelen a hibrid kapcsolat elérési út nem regisztrált, egy érvénytelen vagy hiányzó tokent vagy valamilyen egyéb hiba miatt, ha a hiba visszajelzés érkezett a HTTP 1.1 állapot rendszeres visszajelzés modell használatával. Az állapot leírása tartalmazza nyomon követését a továbbíthatók azonosító az Azure támogatási személyzet hiba:

| Kód | Hiba          | Leírás
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nem található      | A hibrid kapcsolat elérési út érvénytelen, vagy az alap URL-cím formátuma hibás.
| 401  | Nem engedélyezett   | A biztonsági token hiányzik vagy helytelen formátumú vagy érvénytelen.
| 403  | Tiltott      | A biztonsági token érvénytelen ehhez az elérési úthoz, és ehhez a művelethez.
| 500  | Belső hiba | Valami hiba történt a szolgáltatásban.

Ha a WebSocket-kapcsolat szándékosan állítja le a szolgáltatás után azt kezdetben be van állítva, így a információcserére kerül sor, használja a megfelelő WebSocket protokoll hibakódot együtt egy is magában foglalja a nyomkövetési azonosítót. részletes hibaüzenet az az oka

| WS-állapot | Leírás
| --------- | ------------------------------------------------------------------------------- 
| 1000      | A figyelő a szoftvercsatorna leállítása.
| 1001      | A hibrid kapcsolati útvonal törölve lett vagy le van tiltva.
| 1008      | A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirendben sérül.
| 1011      | Valami hiba történt a szolgáltatásban.

### <a name="http-request-protocol"></a>HTTP-kérelem protokoll

A HTTP-kérelem protokoll lehetővé teszi, hogy tetszőleges HTTP-kérelmekre, kivéve protokoll frissítések.
HTTP-kérelmek hivatkozott vannak az entitás rendszeres futásidejű címet, a hibrid kapcsolatok WebSocket ügyfelek használt $hc infix nélkül.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

A _névtér-cím_ teljesen minősített tartományneve, amelyen a hibrid kapcsolatot, általában az űrlap Azure továbbítási névtér `{myname}.servicebus.windows.net`.

A kérelem tartalmazhat tetszőleges extra HTTP-fejlécek található, beleértve az alkalmazás által meghatározott néhányat a meglévők közül. Az összes megadott fejlécek RFC7230 közvetlenül definiált kivételével (lásd: [kérelemüzenet](#Request message)) a figyelő a áramló és megtalálható a `requestHeader` objektum a **kérelem** üzenet.

A lekérdezési karakterlánc paraméter lehetőségek a következők:

| Param          | Kötelező? | Leírás
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | igen\*     | A figyelő adjon meg egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési jogkivonat a névtér vagy a hibrid kapcsolat, amely ruház a **küldése** jobb.

A token is címként kell végezni a `ServiceBusAuthorization` vagy `Authorization` HTTP-fejléc. A token kihagyható, ha a hibrid kapcsolat névtelen kérések engedélyezéséhez.

Mivel a szolgáltatás hatékonyan proxyjaként működik, akkor is, ha nem, igaz HTTP-proxyt, vagy hozzáadja egy `Via` fejléc vagy a meglévő jelzi `Via` megfelelő fejléc [RFC7230, szakasz 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
A szolgáltatás felveszi a továbbító névtér állomásnév való `Via`.

| Kód | Üzenet  | Leírás                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | A kérelem kezelve van legalább egy figyelő.  |
| 202  | Elfogadva | A kérés elfogadva legalább egy figyelő. |

Ha nem sikerül, a szolgáltatás válaszolhatnak az alábbiak szerint. E válasz származik, a szolgáltatás vagy a figyelő keresztül jelenléte azonosítható a `Via` fejléc. Ha a fejléc található, a figyelő a rendszer a választ.

| Kód | Hiba           | Leírás
| ---- | --------------- |--------- |
| 404  | Nem található       | A hibrid kapcsolat elérési út érvénytelen, vagy az alap URL-cím formátuma hibás.
| 401  | Nem engedélyezett    | A biztonsági token hiányzik vagy helytelen formátumú vagy érvénytelen.
| 403  | Tiltott       | A biztonsági token érvénytelen ehhez az elérési úthoz, és ehhez a művelethez.
| 500  | Belső hiba  | Valami hiba történt a szolgáltatásban.
| 503  | Hibás átjáró     | A kérelem nem csatlakoznak bármely figyelő.
| 504  | Átjáró időtúllépése | A kérelem egy figyelő lett irányítva, de a figyelő nem nyugtázott fogadását szükséges időt.

## <a name="next-steps"></a>További lépések

* [Relay – gyakori kérdések](relay-faq.md)
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)
