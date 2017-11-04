---
title: "Az Azure hibrid kapcsolatok protokoll útmutató |} Microsoft Docs"
description: "Az Azure hibrid kapcsolatok protokoll útmutató."
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: 
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 9d015678dbd99b8d978c2c8200b36bf51cac8893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# Az Azure hibrid kapcsolatok protokoll
Az Azure továbbítási az Azure Service Bus platform a fő funkció oszlopok egyike. Az új *hibrid kapcsolatok* továbbító egy biztonságos, nyílt-protokoll alakulása a HTTP és a websocket elemek alapján. Azt írja felül a volt, ugyanilyen nevű *BizTalk szolgáltatások* funkciója, amely a saját fejlesztésű protokollja alaprendszert lett létrehozva. Hibrid kapcsolatok integrálása Azure App Service szolgáltatások továbbra is működjön-van.

Hibrid kapcsolatok lehetővé teszi, hogy kétirányú, bináris adatfolyam közötti kommunikáció során, ami esetleg mindkét félnek elhelyezkedhetnek NAT vagy tűzfal mögé két hálózati alkalmazások. Ez a cikk ismerteti a hibrid kapcsolatok relay csatlakozó ügyfeleken figyelő küldő szerepkörök és hogyan figyelői új kapcsolatok fogadásához ügyféloldali interakció.

## Interakció modell
A hibrid kapcsolatok továbbítási azáltal, hogy az Azure felhőben, amelyek mind a felek felderíteni, és a saját hálózati szempontból csatlakozni a szinkronizálási pont csatlakozik a két fél. A szinkronizálási pont "Hibrid kapcsolat" Ebben és egyéb dokumentációt neve API-kban és az Azure portálon. A hibrid kapcsolatok szolgáltatási végpont a "szolgáltatás" Ez a cikk a többi nevezzük. A kommunikáció modell leans a sok más hálózati API-k által létrehozott.

Egy figyelő, amely először azt jelzi, hogy készültségi bejövő kapcsolat kezelésére, és ezt követően fogadja el őket, akkor van. A másik oldalon a figyelő vár, hogy kapcsolatot létrehozó kétirányú kommunikáció elérési elfogadja felé kapcsolódó ügyfél van.
"Csatlakozás", "Figyelés" és "Elfogadása" is az azonos feltételeket legtöbb szoftvercsatorna API-kat is megtalálhatók.

A továbbítón keresztüli kommunikáció modellnek vagy fél kimenő kapcsolatok felé egy végpontot, amely lehetővé teszi a "figyelő" is "ügyfél" köznyelvi használja, és más terminológiát túlterhelések okozhat. A pontos, ezért használjuk a hibrid kapcsolatok terminológia a következőképpen történik:

A kapcsolat mindkét oldalán programok "ügyfelek," nevezzük, mivel azok az ügyfelek számára a szolgáltatás. Megvárja, és a kapcsolatokat fogad ügyfél a "figyelő", vagy a "figyelő szerepkörben.", különállónak Az ügyfélnek, amelyik a szolgáltatáson keresztül figyelő felé új kapcsolatot kezdeményez a "feladó" nevezik, vagy a "feladó szerepkör."

### Figyelő interakciók
A figyelő rendelkezik a szolgáltatás; négy interakciók összes átviteli részletes ismertetett referencia című cikkben.

#### Figyelés
Jelzi a szolgáltatás, amely egy figyelő készen állnak készen áll kapcsolatok fogadására létrehoz egy kimenő WebSocket-kapcsolat. A kapcsolati kézfogás hordoz magában, ha a továbbítási névteret, és egy biztonsági jogkivonatot, amely a "figyelés" jogot a ruház neve konfigurálva hibrid kapcsolat neve.
A WebSocket a szolgáltatás által elfogadható, ha a regisztrálása sikeresen befejeződött, és a meglévő webes WebSocket megőrzi életben csatornaként"vezérlő" minden későbbi kapcsolatok engedélyezéséhez. A szolgáltatás lehetővé teszi, hogy legfeljebb 25 párhuzamos figyelők a hibrid kapcsolat. Ha két vagy több aktív figyelők, a bejövő kapcsolatok elosztását mindegyik véletlenszerű sorrendben; igazságos elosztási nem garantált.

#### Fogadja el
A küldő a szolgáltatás az új kapcsolat megnyitása után a szolgáltatás úgy dönt, és értesíti a hibrid kapcsolat aktív figyelőinek egyikét. A rendszer értesítést küld a figyelő keresztül nyissa meg a vezérlőcsatorna, amelyek a figyelő csatlakoztatni kell a kapcsolat elfogadása a WebSocket-végpont URL-CÍMÉT tartalmazó JSON üzenetben.

Az URL-címet is, és közvetlenül a figyelő extra munka nélkül kell használniuk.
A kódolt információt csak érvénytelen, a rövid idő alatt, lényegében, az addig, amíg a küldője hajlandó várnia kell lennie a meghatározott-végpontok közötti kapcsolat, de legfeljebb 30 másodperc. Az URL-cím csak egy sikeres kapcsolódási kísérlet portbesorolása. Amint a szinkronizálási URL-címet a WebSocket-kapcsolat létrejött, a WebSocket minden további tevékenység a kezdő és a feladó, a szolgáltatás értelmezése vagy beavatkozás nélkül a továbbítón keresztüli.

#### Frissítés
A biztonsági jogkivonatot kell használni a figyelő regisztrálása és karbantartása a vezérlőcsatorna előfordulhat, hogy lejár, amíg a figyelő aktív. A jogkivonat lejáratának nem befolyásolja a folyamatban lévő kapcsolatok, de ennek következtében a vezérlőcsatorna, illetve hamarosan lejáró időpontjában után a szolgáltatás megszakad. A "megújítása" művelet egy JSON-üzenetet, amely a figyelő küldhet, hogy a vezérlőcsatorna hosszabb ideig is megőrizhető, cserélje le a jogkivonatot a vezérlőcsatorna társított.

#### Ping
Ha hosszú ideje, a a módszerrel a közvetítők tétlen marad a vezérlőcsatorna terhelés például terheléselosztó vagy NAT dobhatja TCP-kapcsolatot. A "ping" művelet elkerülhető, hogy úgy, hogy a csatornán, is emlékezteti, hogy a kapcsolat az célja, hogy kell életben, és azt is szolgál egy "élő" tesztet a figyelő a hálózati útvonalhoz mindenki kisebb mennyiségű adatot küld. Ha a pingelés nem sikerül, a vezérlőcsatorna érdemes figyelembe venni használható, és a figyelő elméletileg újracsatlakozik.

### Küldő interakció
A küldő csak rendelkezik a szolgáltatás egyetlen interakcióba: csatlakozik.

#### Kapcsolódás
A "Csatlakozás" művelet megnyitja a WebSocket szolgáltatás, a nevét, a hibrid kapcsolat és (ha szükséges, de alapértelmezés szerint) a biztonsági token eredményező "Send" engedéllyel a lekérdezési karakterláncban. A szolgáltatás majd kommunikál a figyelő a korábban ismertetett módon, és a figyelő létrehoz egy szinkronizálási kapcsolat, amely a WebSocket kapcsolódik. A WebSocket elfogadását követően az adott WebSocket minden további interakciók vannak egy csatlakoztatott figyelő.

### Interakció összefoglaló
Ez a kommunikáció modell eredménye, hogy a küldő ügyfél származik-e a "tiszta" WebSocket, amelyek csatlakoztatva van egy figyelő, és további preambulumok vagy előkészítési nem igénylő kézfogást kívül. Ez a modell lehetővé teszi gyakorlatilag bármely létező WebSocket ügyfél megvalósítását, hogy könnyen előnyeit a hibrid kapcsolatok szolgáltatás úgy, hogy megadja a megfelelően összeállított URL-címet a WebSocket ügyfél rétegbe.

A szinkronizálási kapcsolat, amely a figyelő olvassa be a elfogadás interakció keresztül WebSocket is tiszta, és minden meglévő WebSocket server végrehajtása néhány minimális extra absztrakciós, amelyik megkülönbözteti az "elfogadás" műveleteket végez a keretrendszer helyi hálózati figyelők és a hibrid kapcsolatok távoli "elfogadás" műveletek között is kell adni.

## Protokoll referenciája

Ez a szakasz ismerteti a fentiekben ismertetett protokoll kapcsolati adatait.

Az összes WebSocket-kapcsolat készült a 443-as porton frissítéseként HTTPS 1.1, amelyeket gyakran néhány WebSocket keretrendszer vagy API-val. A leírását tartják megvalósítási semleges, anélkül, hogy egy adott keretrendszert javasolhat.

### Figyelő protokoll
A figyelő protokoll két kapcsolat kézmozdulatok és három üzenetművelet áll.

#### Figyelő vezérlő adatcsatorna-kapcsolatot
A vezérlőcsatorna, ahol a WebSocket-kapcsolat létrehozása:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...
```

A `namespace-address` teljesen minősített tartományneve, amelyen a hibrid kapcsolatot, általában az űrlap Azure továbbítási névtér `{myname}.servicebus.windows.net`.

A lekérdezési karakterlánc paraméter lehetőségek a következők:

| Paraméter | Szükséges | Leírás |
| --- | --- | --- |
| `sb-hc-action` |Igen |A figyelő szerepkörhöz a paramétert kell **sb-hc-művelet figyelési =** |
| `{path}` |Igen |Az előre konfigurált hibrid kapcsolat regisztrálni. Ez a figyelő URL-kódolású névtér elérési útját. A kifejezés a rendszer hozzáfűzi a rögzített `$hc/` elérési út részével. |
| `sb-hc-token` |igen\* |A figyelő adjon meg egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési jogkivonat a névtér vagy a hibrid kapcsolat, amely ruház a **figyelésére** jobb. |
| `sb-hc-id` |Nem |Az ügyfél által megadott választható azonosítója lehetővé teszi, hogy a végpont a diagnosztikai nyomkövetés. |

A WebSocket-kapcsolat sikertelen a hibrid kapcsolat elérési út nem regisztrált, vagy egy érvénytelen vagy hiányzó jogkivonatot, vagy valamilyen egyéb hiba miatt, ha a hiba visszajelzés érkezett a HTTP 1.1 állapot rendszeres visszajelzés modell használatával. Az állapot leírása hiba követési-azonosítót tartalmaz, amely az Azure támogatási személyzet tájékoztatni lehet:

| Kód | Hiba | Leírás |
| --- | --- | --- |
| 404 |Nem található |A hibrid kapcsolat elérési út érvénytelen, vagy az alap URL-cím formátuma hibás. |
| 401 |Nem engedélyezett |A biztonsági token hiányzik vagy helytelen formátumú vagy érvénytelen. |
| 403 |Tiltott |A biztonsági token érvénytelen ehhez az elérési úthoz ehhez a művelethez. |
| 500 |Belső hiba |Valami hiba történt a szolgáltatásban. |

Ha a WebSocket-kapcsolat szándékosan állítja le a szolgáltatás után kezdetben hozták létre, így a információcserére kerül sor, használja a megfelelő WebSocket protokoll hibakódot együtt egy is magában foglalja a nyomkövetési azonosítót. részletes hibaüzenet az az oka A szolgáltatás nem állítja le a vezérlőcsatorna hibaállapotot elolvasása nélkül. A tiszta Leállítás utáni a felügyelt ügyfél.

| WS-állapot | Leírás |
| --- | --- |
| 1001 |A hibrid kapcsolati útvonal törölve lett vagy le van tiltva. |
| 1008 |A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirendben sérül. |
| 1011 |Valami hiba történt a szolgáltatásban. |

### Fogadja el a kézfogás
A "elfogadása" rendszer értesítést küld a szolgáltatás által a figyelő a korábban létrehozott vezérlő csatornán WebSocket szöveg keretben JSON üzenetben. Nincs válasz ezzel az üzenettel.

Az üzenet JSON objektumot tartalmaz "elfogadás" nevű meghatározó jelenleg a következő tulajdonságokkal:

* **cím** – az URL karakterlánc használandó a szolgáltatásnak a WebSocket létrehozása egy bejövő kapcsolatok fogadására.
* **azonosító** – az ehhez a kapcsolathoz tartozó egyedi azonosító. Az azonosító a küldő ügyfél által biztosított, ha a feladó megadott érték, különben a rendszer által létrehozott értéket.
* **connectHeaders** – meg van adva a továbbítási végpontra a feladó, amely magában foglalja az mp-WebSocket-protokoll és az mp-WebSocket-bővítmények fejlécek HTTP-fejlécek.

#### Üzenet elfogadása

```json
{                                                           
    "accept" : {
        "address" : "wss://168.61.148.205:443/$hc/{path}?..."    
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

#### A szoftvercsatorna elfogadásakor
Fogadja el, hogy a figyelő a megadott cím WebSocket kapcsolat jön létre.

Ha a "elfogadása" üzenetének egy `Sec-WebSocket-Protocol` fejléc, várható, hogy a figyelő csak elfogadja a WebSocket, ha az támogatja-e a protokoll. Emellett a fejléc állítja a, a WebSocket jön létre.

Ugyanez érvényes a `Sec-WebSocket-Extensions` fejléc. Ha a keretrendszer egy bővítmény támogatja, a fejléc azt úgy beállítani, hogy a szükséges a kiszolgálóoldali válasz `Sec-WebSocket-Extensions` kézfogás a bővítmény.

Az URL-címet kell használni, mint-létrehozásához az elfogadás szoftvercsatorna, de tartalmazza a következő paramétereket:

| Paraméter | Szükséges | Leírás |
| --- | --- | --- |
| `sb-hc-action` |Igen |A szoftvercsatorna elfogadásakor, a paraméternek kell lennie`sb-hc-action=accept` |
| `{path}` |Igen |(lásd a következő) |
| `sb-hc-id` |Nem |Tekintse meg az előző leírása **azonosító**. |

`{path}`az URL-kódolású névterek elérési útjai, amelyen ez a figyelő regisztrálása az előre konfigurált hibrid kapcsolat van. A kifejezés a rendszer hozzáfűzi a rögzített `$hc/` elérési út részével. 

A `path` kifejezés ki, amelynek utótag és a regisztrált név elválasztó perjellel után a következő lekérdezési karakterlánc-kifejezés. Ez lehetővé teszi, hogy a küldő ügyfél feladó argumentumok átadása a átvevő figyelő, amikor nincs lehetőség a HTTP-fejlécek. Az elvárás, hogy a figyelő keretrendszer elemzi a rögzített elérési út részével és a regisztrált név elérési úton található, és valószínűleg lehetővé teszi a fennmaradó által meghatározott lekérdezési karakterlánc argumentum nélkül `sb-`, eldönti, hogy fogadja el a kapcsolatot az alkalmazás számára elérhető.

További információkért lásd a következő "Küldő protokoll".

Ha nem sikerül, a szolgáltatás válaszolhatnak az alábbiak szerint:

| Kód | Hiba | Leírás |
| --- | --- | --- |
| 403 |Tiltott |Az URL-cím érvénytelen. |
| 500 |Belső hiba |Hiba történt a szolgáltatásban |

A kapcsolat létrejötte után a kiszolgáló leáll a WebSocket, amikor a küldő WebSocket kikapcsolják, vagy a következő állapottal:

| WS-állapot | Leírás |
| --- | --- |
| 1001 |A küldő ügyfél leállítja a kapcsolatot. |
| 1001 |A hibrid kapcsolati útvonal törölve lett vagy le van tiltva. |
| 1008 |A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirendben sérül. |
| 1011 |Valami hiba történt a szolgáltatásban. |

#### A szoftvercsatorna elutasítása
Egy hasonló kézfogás az "elfogadás" üzenet ellenőrzése után a szoftvercsatorna elutasítása igényel, így az állapotkódot és a kommunikáció során az elutasítás oka állapot leírása áramolhasson a feladónak.

A protokoll tervezési szempontokat itt, hogy a WebSocket-kézfogás (, amely egy meghatározott hibaállapot végződésű) használja, hogy a figyelő ügyfélmegvalósítások továbbra is a WebSocket ügyfél alapulnak, és nem kell egy plusz alkalmaz, a HTTP-alapú operációs rendszer.

A szoftvercsatorna, hogy az ügyfél időt vesz igénybe az URI-cím az "elfogadás" üzenet, és hozzáfűzi a lekérdezési karakterlánc-paraméterrel, az alábbiak szerint:

| Param | Szükséges | Leírás |
| --- | --- | --- |
| statusCode |Igen |Numerikus HTTP-állapotkódot. |
| StatusDescription |Igen |Az elutasítás emberi olvasható oka. |

Az eredményül kapott URI majd WebSocket-kapcsolat létrehozásához használt.

Megfelelően befejezése a kézfogás szándékosan sikertelen lesz, hibakódú HTTP 410, mivel nem WebSocket létrehozása. Ha valamilyen hiba, a következő kódokkal mutatják be, a hiba:

| Kód | Hiba | Leírás |
| --- | --- | --- |
| 403 |Tiltott |Az URL-cím érvénytelen. |
| 500 |Belső hiba |Valami hiba történt a szolgáltatásban. |

### Figyelő jogkivonat megújítási
Ha a figyelő-token érvényessége lejár, akkor lecserélheti keret szöveges üzenetet küld a szolgáltatás a kijelölt ellenőrzési csatornán keresztül. Az üzenet tartalmaz egy JSON-objektum neve `renewToken`, amely megadja, hogy a következő tulajdonság ekkor:

* **token** – egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési jogkivonat a névtér vagy a hibrid kapcsolat, amely ruház a **figyelésére** jobb.

#### renewToken üzenet

```json
{                                                                                                                                                                        
    "renewToken" : {                                                                                                                                                      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"  
    }                                                                                                                                                                     
}
```

Ha a jogkivonatok érvényesség-ellenőrzése sikertelen, nincs hozzáférése, és a felhőalapú szolgáltatás bezárja a vezérlőcsatorna WebSocket hiba történt. Ellenkező esetben nincs válasz.

| WS-állapot | Leírás |
| --- | --- |
| 1008 |A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirendben sérül. |

## Küldő protokoll
A küldő protokoll hatékonyan megegyezik a módot, létrejön egy figyelő.
A végpont WebSocket maximális átlátszóságának célja. Való csatlakozás címét ugyanúgy történik, mint a figyelő, de az "action" eltér, és a tokent egy másik engedélyre van szüksége:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

A *névtér-cím* teljesen minősített tartományneve, amelyen a hibrid kapcsolatot, általában az űrlap Azure továbbítási névtér `{myname}.servicebus.windows.net`.

A kérelem tartalmazhat tetszőleges extra HTTP-fejlécek található, beleértve az alkalmazás által meghatározott néhányat a meglévők közül. Minden megadott fejléc a figyelő a áramló, és megtalálható a `connectHeader` objektum a **fogadja el** felügyeletének üzenetében.

A lekérdezési karakterlánc paraméter lehetőségek a következők:

| Param | Kötelező? | Leírás |
| --- | --- | --- |
| `sb-hc-action` |Igen |A küldő szerepkörhöz a paramétert kell `action=connect`. |
| `{path}` |Igen |(lásd a következő) |
| `sb-hc-token` |igen\* |A figyelő adjon meg egy érvényes, az URL-kódolású Service Bus megosztott hozzáférési jogkivonat a névtér vagy a hibrid kapcsolat, amely ruház a **küldése** jobb. |
| `sb-hc-id` |Nem |Egy nem kötelező Azonosítót, amely lehetővé teszi, hogy a végpont a diagnosztikai nyomkövetés és teszi elérhetővé a figyelő az elfogadás kézfogás során. |

A `{path}` URL-kódolású névtér elérési útja az előre konfigurált hibrid kapcsolat, amelyen ez a figyelő regisztrálása. A `path` kifejezés ki lehet terjeszteni utótag és a további kommunikációt a lekérdezési karakterlánc-kifejezés. Ha a hibrid kapcsolat regisztrálva van az elérési úthoz tartozó `hyco`, a `path` kifejezés lehet `hyco/suffix?param=value&...` az itt megadott lekérdezési karakterlánc paraméterekhez követ. Egy teljes kifejezést majd a következő lehet:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

A `path` az átadott kifejezés keresztül a figyelő a címben URI vezérlő "elfogadása" üzenet.

A WebSocket-kapcsolat sikertelen a hibrid kapcsolat elérési út nem regisztrált, egy érvénytelen vagy hiányzó tokent vagy valamilyen egyéb hiba miatt, ha a hiba visszajelzés érkezett a HTTP 1.1 állapot rendszeres visszajelzés modell használatával. Az állapot leírása tartalmazza nyomon követését a továbbíthatók azonosító az Azure támogatási személyzet hiba:

| Kód | Hiba | Leírás |
| --- | --- | --- |
| 404 |Nem található |A hibrid kapcsolat elérési út érvénytelen, vagy az alap URL-cím formátuma hibás. |
| 401 |Nem engedélyezett |A biztonsági token hiányzik vagy helytelen formátumú vagy érvénytelen. |
| 403 |Tiltott |A biztonsági token érvénytelen ehhez az elérési úthoz, és ehhez a művelethez. |
| 500 |Belső hiba |Valami hiba történt a szolgáltatásban. |

Ha a WebSocket-kapcsolat szándékosan állítja le a szolgáltatás után azt kezdetben be van állítva, így a információcserére kerül sor, használja a megfelelő WebSocket protokoll hibakódot együtt egy is magában foglalja a nyomkövetési azonosítót. részletes hibaüzenet az az oka

| WS-állapot | Leírás |
| --- | --- |
| 1000 |A figyelő a szoftvercsatorna leállítása. |
| 1001 |A hibrid kapcsolati útvonal törölve lett vagy le van tiltva. |
| 1008 |A biztonsági jogkivonat érvényessége lejárt, ezért az engedélyezési házirendben sérül. |
| 1011 |Valami hiba történt a szolgáltatásban. |

## Következő lépések
* [Relay – gyakori kérdések](relay-faq.md)
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)

