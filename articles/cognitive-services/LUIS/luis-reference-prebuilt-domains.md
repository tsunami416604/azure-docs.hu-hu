---
title: Előre összeállított tartományhivatkozás
titleSuffix: Azure
description: Az előre összeállított tartományok, amelyek előre elkészített gyűjteménye szándékokat és entitásokat a Language Understanding Intelligent Services (LUIS) referenciája.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: f03efe13cdc31381cd67317879b7bd936fc35e8f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868956"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>A LUIS-alkalmazás az előre összeállított útmutatója
Ez az útmutató információkkal szolgál a [előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md), amely gyűjteményei előre összeállított szándékok és entitások, amelyek a LUIS kínál.

[Egyéni tartományok](luis-how-to-start-new-app.md), ezzel szemben az elindításához nincs leképezések és a modellek. Egy egyéni modell bármilyen előre összeállított tartományban szándékok és entitások adhat hozzá.

## <a name="list-of-prebuilt-domains"></a>Előre összeállított tartományok listája
A LUIS 20 előre összeállított tartományok kínál. 

| Előre összeállított tartomány | Leírás | Támogatott nyelvek |
| ---------------- |-----------------------|:------:|
| Naptár | A naptár tartomány szándékot és entitások hozzáadása, törlése, vagy egy találkozót szerkesztésére, résztvevő rendelkezésre állásának ellenőrzése és adatait, egy naptáresemény biztosít.| en-US<br/> zh-CN |
| Kamera | A kamera tartományban szándékok és entitások biztosít képek, a rögzítés videók és a szórásos videók véve az alkalmazás.| en-US |
| Kommunikáció | Üzenetek küldése és telefonhívásokat.| en-US <br/> zh-CN |
| szórakoztatás  | Zene, filmek és TV kapcsolódó lekérdezések kezelése.| en-US |
| Események | Foglalás Koncertek, fesztiválok, sport játékokat és comedy jegyek jeleníti meg.| en-US |
| Alkalmasságát | Kezelési mentességre tevékenységek nyomon követéséhez kapcsolódó kérelem.| en-US |
| Játékok | Több résztvevős játék játék féllel kapcsolatos kérések kezelésével.| en-US |
| HomeAutomation | Az intelligens otthoni eszközök, például lámpa és berendezések szabályozása.| en-US<br/> zh-CN |
| MovieTickets | Foglalás egy filmet mozi filmeket jegyeket.| en-US |
| Zene | Zene játsszon egy zenelejátszókkal.| en-US<br/> zh-CN |
| Megjegyzés | A Megjegyzés tartományban szándékok és entitások létrehozására, szerkesztésére és megjegyzések keresése kapcsolatos biztosít.| en-US<br/> zh-CN |
| OnDevice | A OnDevice tartományban szándékok és entitások, az eszköz vezérlésére kapcsolatos biztosít.| en-US<br/> zh-CN |
| Helyek  | A kezelése, többek között a vállalkozások, intézmények, éttermek, nyilvános szóközöket és címek kapcsolódó lekérdezések.| en-US<br/> zh-CN |
| Emlékeztető | Kezelési létrehozására, szerkesztésére és emlékeztetők keresése kapcsolódó kérelem.| en-US<br/> zh-CN |
| RestaurantReservation | Kezelési kérelem éttermi foglalások kezelése.| en-US<br/> zh-CN |
| Taxi | -I taxik foglalások kezelése.| en-US<br/> zh-CN |
| Fordítás | A célként megadott nyelvre a szöveg fordítása.| en-US<br/> zh-CN |
| TV | Televízióra szabályozása.| en-US |
| Közművek  | Kezelési kérelem, amelyek közösek a számos tartományok, mint például a "Súgó", "ismételje meg a", "kezdje újra a folyamatot."| en-US |
| Időjárás | Időjárás-előrejelzéseket és előrejelzések beolvasásakor.| en-US<br/> zh-CN |
| Web | Navigálás egy webhelyre.| en-US<br/> zh-CN |

Minden tartományban a további részletekért tekintse meg a következő szakaszok.

## <a name="calendar"></a>Naptár 

A naptár tartományban szándékok és entitások naptárbejegyzések megőrzésének kapcsolatos biztosít. A naptár leképezések hozzáadása, törlése vagy találkozó szerkesztése, a rendelkezésre állásának ellenőrzése és a egy naptár bejegyzés vagy találkozó vonatkozó információk keresése tartalmazza.

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Hozzáadás | Új egyszeri elem felvétele a naptárba.| Győződjön meg arról, Lisa vasárnap 2-kor egy találkozót <br/><br/>Értekezlet ütemezése<br/><br/>Kell egy értekezlet beállítása|
| CheckAvailability | Rendelkezésre állási találkozó vagy a felhasználó vagy egy másik személy naptárat az értekezlet található.| Ha van elérhető teljesítéséhez Jim? <br/><br/>Ha Carol holnap elérhető megjelenítése<br/><br/>Chris szombat ingyenes van?|
| Törlés | Kérelem törlése egy naptár-bejegyzést.| Carol saját találkozót megszakítása <br/><br/>Értekezlet a 9: 00 törlése<br/>|
| Szerkesztés | Módosíthatja egy meglévő megbeszélésen vagy a naptár belépési kérelmet.| Helyezze át 9: 00-értekezlet a 10-kor.<br/><br/>Szeretnék saját ütemezés frissítése.<br/><br/>Értekezlet a Reschdule Ryan együtt.|
| Keresés | A heti naptár megjelenítése.| A fogorvosnak tekintse át a találkozó keresése. <br/><br/>Naptár megjelenítése<br/>|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Hely | Naptári elemet, az értekezlet vagy a találkozó helye. Cím, város és régiók helyek jó példák.| 209 Nashville Fitnessklub <br/><br/>897 pancake ház<br/><br/>Garázs|
| Tárgy | Egy megbeszélésen vagy találkozó címe.| Dentist a találkozó <br/><br/>A Julia ebéd<br/><br/>Orvos találkozó|

## <a name="camera"></a>Kamera 
A kamera tartományban szándékok és entitások egy kamera használatához kapcsolódó biztosít. A leképezések foglalkozik, selfie, képernyőkép videófelvétel vagy fénykép rögzítése, és teszi közzé a videót, amely egy alkalmazást.

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| CapturePhoto| Egy fénykép rögzítése.| Fényképezés<br/><br/>rögzítése|
| CaptureScreenshot | Képernyőkép rögzítése.| A képernyőképet.<br/><br/>rögzítse a képernyőn.|
| CaptureSelfie | Egy selfie rögzítése.| Egy selfie igénybe <br/><br/>Kép készítése Me |
| CaptureVideo | Indítsa el a felvételt a videó.| Felvétel indítása <br/><br/>Rögzítés megkezdéséhez|
| StartBroadcasting| Indítsa el a szórásos videót.| Indítsa el a szórásos a Facebookon|
| StopBroadcasting| Állítsa le a szórásos videót.| Közvetítés leállítása|
| StopVideoRecording| Videó Rögzítés leállítása.| Végeztünk<br/><br/>Rögzítés leállítása|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Alkalmazásnév | Szórási videót, amely az alkalmazás neve.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Kommunikáció 
A kommunikációs tartományban szándékok és entitások kapcsolatos e-mailek, az üzenetek és a telefonhívások biztosít.

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AddContact| Új ügyfél hozzá a felhasználó ügyfelek listáját.|Új kapcsolat felvétele <br/><br/>Mentse ezt a számot, és helyezze a nevet a következőként Carol|
| AddMore| Adja hozzá több, egy e-mailben vagy szöveges, többlépcsős e-mail vagy szöveges juthat szerephez.|Szöveg bővítése <br/><br/>E-mail törzse bővítése|
| Válasz| A Bejövő telefonhívás fogadása.|A hívás <br/><br/>Emelje fel|
| AssignContactNickname| Rendelje hozzá egy névjegyet egy becenév.|Imrének apa módosítása <br/>Szerkesztés Jim becenév<br/>Patti Owens becenév hozzáadása|
| CallVoiceMail| A felhasználó hangposta csatlakozni.|Csatlakozzon számomra a saját hangposta <br/>Hangposta<br/>Hívás hangposta|
| CheckIMStatus| A Skype-ügyfél állapotának ellenőrzéséhez.|Jim az online állapot értéke azonnal? <br/>Carol érhető el a Csevegés?|
| Megerősítés| Erősítse meg a műveletet.|Igen<br/>Rendben<br/>rendben<br/>Megerősítem, hogy szeretnék-e e-mailt.<br/>|
| Telefonos kapcsolat létrehozása| Telefonhívás.|Jim hívása<br/>Adjon tárcsázza a 311<br/>|
| FindContact| Kapcsolattartási adatok keresése név alapján.|Carol szám keresése<br/>Carol szám megjelenítése<br/>|
| FindSpeedDial| Található a speedial telefonszám van beállítva, és ez fordítva is igaz.|Mi az a telefonos számot 5?<br/>Tárcsázza a set sebesség van?<br/>Mi a telefonszáma 941-5555-333?|
| GetForwardingsStatus| Átirányítás aktuális állapotának lekéréséhez.|A hívás továbbítás engedélyezve van?<br/>Figyelmeztessen, ha hívás Állapotom-e a be- vagy kikapcsolása<br/>|
| GoBack| Lépjen vissza az előző lépésben.|Lépjen vissza a twitteren<br/>Lépjen vissza az adott lépés<br/>Vissza|
| Kihagyás| Bejövő hívás figyelmen kívül.|Nem válaszol<br/>Hagyja figyelmen kívül a hívás|
| IgnoreWithMessage| Bejövő hívás figyelmen kívül, és inkább szöveget tartalmazó válasz.|Ez a meghívás nem válaszol, de küldjön inkább üzenetet.<br/>Figyelmen kívül hagyhatja, és vissza egy szöveges üzenet elküldéséhez.|
| PressKey| Egy gomb vagy a szám nyomja le a billentyűzeten.|Tárcsázás csillagra.<br/>Nyomja meg az 1-2 3.|
| ReadAloud| Olvassa el az üzenet vagy e-mailt a felhasználónak.|Olvassa el a szöveget.<br/>Mi volt ő tegyük fel, hogy az üzenet?|
| TurnForwardingOff| Telefonhívás.|<br/><br/>|
| Újratárcsázás| Újratárcsázás, vagy egy szám hívja meg újra.|Újratárcsázás.<br/>A legutóbbi hívásának Újratárcsázás.|
| Elutasítás| Bejövő hívás elutasítása.|Hívás elutasítása<br/>Most már nem válaszol<br/>Jelenleg nem érhető el, és meghívja vissza később.|
| Sendemail művelet| E-mail küldése. Ez a leképezés e-mailt, de nem a szöveges üzenetek vonatkozik.|E-mailek víz Mike: Mike, hogy az elmúlt hét vacsora splendid volt-e.<br/>Bob e-mail küldése<br/>|
| SendMessage| Szöveges üzenet vagy azonnali üzenet küldése.|Chris és Carol szöveg küldése|
| SetSpeedDial| Sebesség tárcsázás parancsikon be a telefonszámát.|Carol egy gyors tárcsázás be.<br/>Állítsa be a mom sebesség bármekkora munkát képes elvégezni.|
| ShowNext| Lásd a következő elem, például a szöveges üzenetben vagy e-mailek listáját.|Megjelenítés a következő parancsot.<br/>A következő oldalra lépéshez.|
| ShowPrevious| Lásd az előző elemmel, például a szöveges üzenetben vagy e-mailek listáját.|Az előző feladat megjelenítése.<br/>Előző<br/>Nyissa meg az előző.|
| StartOver| A rendszer kezdje újra a folyamatot, vagy új munkamenet indításához.|Újrakezdés<br/>Új munkamenet<br/>restart|
| TurnForwardingOff| Kapcsolja ki az átirányítás.|Hívásaim hibajelentéseket<br/>Kikapcsolhatja az átirányítás|
| TurnForwardingOn| Kapcsolja ki a kihangosítás.|A hívások 3333 továbbítása<br/>Hívás-továbbítást a 3333 váltani|
| TurnSpeakerOff| Kapcsolja ki a kihangosítás.|Ugrás a beszélő ki.<br/>Kapcsolja ki a kihangosító.<br/>|
| TurnSpeakerOn| Kapcsolja be a kihangosítás.|Kihangosító mód.<br/>Kihangosító helyezze.<br/>|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AudioDeviceType | Hangeszköz (előadói, mikrofonos, mikrofon, stb.) típusú.| Beszélő<br/>Kihangosítás<br/>Bluetooth|
| Kategória | A kategória üzenetben vagy e-mailt.| Fontos<br/>Magas prioritású|
| ContactAttribute | Az ügyfél a felhasználói attribútum vonatkozó lekérdezésekben.| Születésnapok<br/>Cím<br/>Telefonszám|
| ContactName | Egy ügyfél vagy üzenet címzettje neve.| Carol<br/>Jim<br/>Chris|
| EmailSubject | A szöveg tárgy szolgáló e-mailt.| RE: érdekes történetet|
| Vonal | A sor a felhasználó szeretne egy hívást, vagy a szöveg/e-mail küldése.| Munkahelyi sor<br/>Brit cella<br/>Skype|
| Üzenet | Küldendő e-mail vagy szöveges üzenet.| Nagyszerű, ma értekezlet-volt. Találkozunk kis idő múlva!|
| MessageType | Egy ügyfél vagy üzenet címzettje neve.| Szöveg<br/>E-mail|
| OrderReference | Sorszámnál vagy relatív azonosító lekéréséhez elem lista pozíciója. Például "last" vagy "legutóbbi" a "Mi volt az utolsó üzenet elküldött?"| Vezetéknév<br/>Legutóbbi|
| SenderName | A feladó neve.| Patti Owens|

## <a name="entertainment"></a>szórakoztatás  
A Szórakozás tartományban szándékok és entitások filmek, zene, játékokat és TV megkeresésével kapcsolatos biztosít jeleníti meg.

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Keresés| Keresse meg a filmek, zene, alkalmazásokat, játékokat és TV jeleníti meg.|Keressen rá az áruházban a Halo.<br/>Az Avatar keresése.|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| ContentRating | Media tartalom filmekhez például G vagy R minősítése.|Gyerek videó.<br/>PG besorolása.|
| Műfaj | A műfajt filmet, játékot, alkalmazást vagy dal.|Comedies<br/>Dramas<br/>vicces|
| Kulcsszó| Adjon meg egy attribútum általános keresési kulcsszó a pontosabb media tárolóhelyben nem létezik.|Zeneszámokat<br/>Hold folyam<br/>Amelia Earhart|
| Nyelv | Az adathordozó, például használja a beszélt nyelv filmet vagy a dal használt nyelv.|francia<br/>Angol<br/>koreai|
| MediaFormat | A további technikai különleges, amelyben az adathordozó van formázva.|HD filmek<br/>3D filmek<br/>Letölthető|
| MediaSource | A tároló vagy a Marketplace-en az adathordozó beszerzése a.|Netflix<br/>Prime|
| MediaSubTypes| Adathordozó-típusok kisebb, mint a mozi- és játékokat.|Bemutatók<br/>DLC<br/>Pótkocsik|
| Állampolgárság| Az ország, ahol egy filmet, megjelenítése vagy egy zeneszám lett létrehozva.|francia<br/>német<br/>koreai|
| Személy| Az aktor, igazgató, előállítói, zenésze vagy egy filmet, alkalmazás, játék vagy TV-műsor előadója.|Madonna<br/>Stanley Kubrick|
| Szerepkör| Szerepet, amelyet a személy, a média létrehozása.|Sings<br/>Útmutatása alapján<br/>–|
| Beosztás| Egy filmet, alkalmazás, játékot, TV-műsor vagy dal neve.|Ismerősök felvételének engedélyezése<br/>Minecraft|
| Típus| Egy filmet, alkalmazás, játékot, TV show vagy szám típus vagy adathordozó formátumát.|Zene<br/>MovieTV <br/>jeleníti meg|
| UserRating| Felhasználói csillag vagy minősítés miniatűrök.|5 csillag<br/>3 csillag<br/>4 csillag|

## <a name="events"></a>Események 
Az események tartományban szándékok biztosít, és lefoglalási jegyeket, Koncertek, fesztiválok, sport játékokat és comedy hasonló események mutat be kapcsolatos entitásokat.

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Könyv| Jegyek az esemény.|Szeretnék vásárolni egy jegyet a symphony a hétvégi.|


### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Cím | Az esemény helyszíne vagy címe. |Rendszert futtató Palo Alto<br/>300 112th Ave képlet használata <br/> Seattle |
| Name (Név) | Egy esemény neve.|Az a Park Shakespeare|
| PlaceName| Az esemény hely nevét.|Louvre<br/>Opera ház<br/>Broadway|
| PlaceType | A hely típusa az esemény tárolandók.|Kávéház<br/>Terület<br/>Erőforrástár|
| Típus | Az esemény típusa.|Concert<br/>Sport-játék|

## <a name="fitness"></a>Alkalmasságát 
A mentességre tartományban szándékok és entitások mentességre tevékenységek nyomon követéséhez kapcsolódó biztosít. A leképezések közé tartozik a megjegyzések, hátralévő idő vagy a távolságot vagy tevékenység eredmények mentése mentése.

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AddNote| A nyomon követett tevékenységet ad hozzá kiegészítő megjegyzések.|A Futtatás nehéz volt 6/10<br/>A futó vagyok a terep aszfaltot<br/>Egy 3 sebesség kerékpárt használok|
|GetRemaining| Egy tevékenység lekéri a hátralévő idő vagy távolság.|Mennyi ideig, amíg a következő rövid ismertetése?<br/>Hány mérföld hátralévő a Futtatás? Mennyi időt a felosztás?|
| LogActivity| Mentse, vagy jelentkezzen befejezett tevékenység eredménye.|Mentse a legutóbbi futtatás<br/>Jelentkezzen a saját szombat reggel útmutató<br/>az előző úszó tárolásához|
| LogWeight| Mentse, vagy a felhasználó aktuális súly naplózása.|A jelenlegi súly mentése<br/>saját súly Jelentkezzen most<br/>a jelenlegi törzs súly tárolásához|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Tevékenységtípus | Nyomon követő tevékenység típusa. |Futtassa a következőt:<br/>Gyaloglás<br/>Úszni<br/>Ciklus |
| Élelmiszer | Élelmiszer mentességre alkalmazásban nyomon követéséhez egy típusa. |Banán<br/>Lazacvörös<br/>Fehérjék rázó|
| MealType| Állapot és a valamilyen alkalmazásban nyomon dara típusa.|A reggeli<br/>Vacsora<br/>Ebéd<br/>Supper|
| Mérés| Idő, a távolságot vagy a súly alkalmasságra vagy egészségügyi alkalmazásban használható méréseket típusát.|Adja meg kilométerben<br/>Mérföld<br/>perc<br/>Kg|
| Szám | Numerikus mennyiség alkalmasságra vagy egészségügyi alkalmazásban használható.|19<br/>három<br/>200<br/>egy|
| StatType | Egy összesített adat, egy alkalmasságra vagy egészségügyi alkalmazásban használható statisztika típusa.|Összeg<br/>Átlag<br/>Maximum<br/>Minimális|

## <a name="gaming"></a>Játékok 
A játék tartományban szándékok és entitások egy több résztvevős játék a játék fél felügyeletével kapcsolatos biztosít.

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| InviteParty| Meghívhat egy névjegyet egy játékokkal foglalkozó fél csatlakozni.|A player saját fél meghívása<br/>Saját fél jár<br/>Csatlakozzon a saját clan|
|LeaveParty| Egy tevékenység lekéri a hátralévő idő vagy távolság.|Ki vagyok<br/>I vagyok és a egy másik entitás<br/>I/am Kilépés|
| StartParty| Indítsa el a játék fél több résztvevős játék.|Nézzük dude egy entitás indítása<br/>egy entitás indítása<br/>érdemes kezdődik meg, egy clan vacsora|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Kapcsolattartó| Több résztvevős játék használata a kapcsolattartó nevét.|Carol<br/>Jim|


## <a name="homeautomation"></a>HomeAutomation 
A HomeAutomation tartományban szándékok és entitások intelligens otthoni eszközök, például lámpa és berendezések szabályozásával kapcsolatos biztosít.

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Kikapcsolás| Kapcsolja ki, zárja be, vagy egy adott eszköz feloldásához.|Kapcsolja ki a jelzőfény<br/>A kávét készítő leállítása<br/>Ajtó bezárása garázs|
|Bekapcsolása| Kapcsolja be az eszközön, vagy állítsa be az eszköz egy adott beállítást vagy mód.|a kávét készítő bekapcsolása<br/>kapcsolhatja be a kávét készítő?<br/>Állítsa az okosórák 72 fok.|


### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Eszköz | Eszköz, amely be vagy ki kell kapcsolni egy típusa.|kávét készítő<br/>Thermostat<br/>lámpa|
| Művelet | Az eszköz beállítása állapota.|zárolás<br/>nyitás<br/>be<br/>kikapcsolva|
| Hely | A hely vagy az eszköz a helyiségben.|nappali<br/>szobája<br/>megjelenítő kijelzőket a konyhai|

## <a name="movietickets"></a>MovieTickets 
A MovieTickets tartományi biztosítja, leképezések és a egy filmet mozi filmeket foglalási jegyeket kapcsolódó entitások.

### <a name="examples"></a>Példák

|Példák|
|--|
|Könyv me kapitány Omar és a két Musketeers két jegyek|
|Jegyek megszakítása|
|Ha látható a kapitány Omar?|

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Könyv | Film jegyek.|Könyv me kapitány Omar és a két musketeers két jegyek<br/>Holnapi movie egy jegyet vásárolni kívánt<br/>2. rész Captian Omar tovább szerda kívánt jegy|
|GetShowTime| Az egy filmet showtime beolvasása.|Ha látható a kapitány Omar?|


### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Cím | Egy filmet mozi címe.|Rendszert futtató Palo Alto<br/>300 112th Ave képlet használata<br/>Seattle|
| MovieTitle | Film címe.|A Pi élettartama<br/>Éhezés játékok<br/>Kezdete|
| PlaceName | Mozi webhelyét vagy mozibemutatók neve.|Mozi Amir<br/>Alexandria terület<br/>New York-i mozi|
| PlaceType | Film jelenik-e meg a hely típusa.|mozi<br/>mozi<br/>IMAX mozibemutatók|

## <a name="music"></a>Zene 
A zene tartományban szándékok és entitások zene játsszon egy zenelejátszókkal kapcsolatos biztosít.

### <a name="examples"></a>Példák

|Példák|
|--|
|Beethoven lejátszása|
|Növelje a kötet nyomon követése|
|Folytassa a következő zeneszám|

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| DecreaseVolume | Az eszköz kötet csökkentése.|zeslabení hlasitosti nyomon követése<br/>kötet le|
| IncreaseVolume | Az eszköz kötet növelése.|Növelje a kötet nyomon követése<br/>kötet mentése|
| Elnémítás |A lejátszás zene vypnutí.|A dal vypnutí<br/>Helyezze a csend a nyomon követése<br/>Vypnutí zene |
| Szünet | A lejátszás zene felfüggesztése.|Szünet<br/>Felfüggesztés zene<br/>Felfüggesztés nyomon követése|
| PlayMusic | Zene játsszon egy eszközön.|Kevin Durant lejátszása<br/>által Coldplay paradicsom lejátszása<br/>Adele által Hello lejátszása|
| Ismétlés |Ismételje meg a lejátszás zene.|Ismételje meg a dal<br/>A track nyereség lejátszása<br/>Ismételje meg a zene|
| Folytatás | A lejátszás zene folytatódik.|RESUME-dal<br/>Indítsa újra a zene<br/>Folytatásához|
| SkipBack | Folytassa a visszamenőleges nyomon.|Folytassa a következő zeneszám<br/>A következő lejátszása|
| SkipForward |Ugrás előre nyomon.|Az előző lejátszása<br/>Lépjen vissza az előző szám |
| Leállítás | Zene lejátszási vonatkozó művelet leállításához. |Állítsa le a lemez játszott.|
| Némítás feloldása | Zene a lejátszás eszköz némításának feloldása.| Ki-bekapcsolása.|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| ArtistName | Az aktor, igazgató, termelő, író, zenésze vagy egy eszközön lejátszása a media előadója.|Elvis Presley<br/>Taylor Swift<br/>Adele<br/>Mozart|
| Műfaj | A kért zene műfaj.|Ország zene<br/>Broadway classics<br/>A klasszikus zene Baroque időszak lejátszása|

## <a name="note"></a>Megjegyzés 
A Megjegyzés tartományban szándékok és entitások létrehozására, szerkesztésére és megjegyzések keresése kapcsolatos biztosít.

### <a name="examples"></a>Példák

|Példák|
|--|
|Saját élelmiszerszükségletét Megjegyzés saláta tomato kenyér kávé hozzáadása|
|Jelölje ki a saját bevásárlólistáról banánok|
|Szabadságolási lista összes elemének eltávolításához|

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AddToNote | Információ hozzáadása egy megjegyzés.|Saját élelmiszerszükségletét Megjegyzés saláta tomato kenyér kávé hozzáadása<br/>Saját teendőlista hozzáadása<br/>a Wunderlist cupcakes hozzáadása|
| CheckOffItem | Pipálja ki egy már meglévő megjegyzés szereplő elemeket.|Jelölje ki a saját bevásárlólistáról banánok<br/>A saját vásárlási listája, ahogyan az ünnepi sajtok iránti szenvedélyének torta megjelölése|
| Törlés | Egy már meglévő megjegyzés összes elemének törlése.|Szabadságolási lista összes elemének eltávolításához<br/>Törölje az összes saját olvasási listából|
| Megerősítés | Erősítse meg a vonatkozó megjegyzés művelet.|Az általam rendben<br/>igen<br/>Minden elem tartja a listák I vagyok megerősítése|
| Létrehozás | Hozzon létre egy új megjegyzés. | Hozzon létre egy listát<br/>Figyelje meg, a Jason Emlékeztessen város első hete – május|
| Törlés | Egy teljes megjegyzés törlése. |Saját szabadság lista törlése <br/>saját élelmiszerszükségletét jegyzet törlése|
| DeleteNoteItem | Elemeket töröl egy már meglévő megjegyzés.| A megosztott bevásárlólistáról lapkák törlése<br/>Saját iskolai lista vásárlás tollak eltávolítása|
| ReadAloud | Olvassa el hangos listáját.|Az első egy információs fájl<br/>Információs fájl részletei|
| ShowNext | Tekintse meg a következő elem egy listában megjegyzések.|Következő megjelenítése<br/>Következő oldal<br/>Következő lépés|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Alkalmazásnév | Jegyzetelő alkalmazást neve.|Wunderlist<br/>OneNote|
| ContactName | Egy partnert a jegyzet neve.|Carol<br/>Jim<br/>Chris|
| Adatforrás | Megjegyzések helye.|OneDrive<br/>Google docs<br/>a számítógép|
| Adattípus | A fájl vagy dokumentum, általában az adott programok társított típusát.|Diák<br/>Számolótábla<br/>Munkalap|
| Szöveg | Egy megjegyzés vagy emlékeztető szöveg.|a Stretch walking előtt<br/>hosszú futtatású holnap|
| Beosztás | Megjegyzés címe.|élelmiszerszükségletét<br/>Személyek meghívásához<br/>TEENDŐ|

## <a name="ondevice"></a>OnDevice 
A OnDevice tartományban szándékok és entitások, az eszköz vezérlésére kapcsolatos biztosít.

### <a name="examples"></a>Példák

|Példák|
|--|
|Zárja be a videólejátszó|
|A lejátszás megszakítása|
|Módosíthatja a képernyőt világosabb?|


### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AreYouListening | Kérje meg, ha az eszköz figyeli.|Ez az a?<br/>figyelik?|
|CloseApplication|Zárja be az alkalmazást.|Zárja be a videólejátszó|
|FileBug|Hiba jelentése az eszközön.|Hiba jelentése.<br/>Tudja meg jelentése a számomra?<br/>Én választom jelentse a hibát|
|GoBack|Kérje meg, hogy egy lépéssel újra, vagy térjen vissza az előző lépésben.|Lépjen vissza.<br/>Ugrás az előző képernyőre<br/>Lépjen vissza stop-figyelő|
|Súgó| Segítséget kérni.|Súgó.<br/>Üdvözöljük!<br/>Mire használható?<br/>Segítségre van szükségem| 
|LocateDevice|Keresse meg az eszközt.|Megtalálhassák telefonomat<br/>A tom iphone keresése<br/>Telefon nyomon követése|
|Bejelentkezés|Jelentkezzen be az eszköz használatával.|Bejelentkezés.<br/>Facebook-bejelentkezés<br/>Jelentkezzen be a LinkedIn|
|Kijelentkezés|Jelentkezzen ki az eszköz használatával.|Kijelentkezés a telefonomra<br/>Twitter-bejelentkezés<br/>Kijelentkezés|
|MainMenu|Tekintse meg az eszköz fő menüjéből.|Nézet menü.|
|OpenApplication|Nyisson meg egy alkalmazást az eszközön.|Nyissa meg a riasztás.<br/>Kapcsolja be a kamerához<br/>Indítsa el a naptárban|
|OpenSetting|Nyissa meg a beállítást, az eszközön.|Nyissa meg a hálózati beállításokat.|
|PairDevice|Párosítsa az eszközt.|Lehet, segít a párosítást Bluetooth jel telefonjára<br/>A bluetooth aktiválását, és párosítsa azt hordozható számítógép<br/>Párosítsa a hordozható Bluetooth jelzés|
|Kikapcsolás | Kapcsolja ki az eszközt.|Állíthatják le a számítógépemen<br/>Leállítás<br/>A mobile kikapcsolása|
|QueryBattery|Akkumulátor-élettartamát adatainak beolvasása.|Megjelenítés élettartamának.<br/>Mi az akkumulátor Állapotom<br/>Mennyi töltöttség most?<br/>Akkumulátor-megjelenítés|
|QueryWifi|Wi-Fi adatainak beolvasása.|Wi-Fi-adatainak beolvasása.|
|Újraindítás|Indítsa újra az eszközt.|Indítsa újra.|
|RingDevice| Kérje meg az eszköz telefonja, annak érdekében, hogy ha megszakadt, keresse meg.|Gyűrűpuffer telefonomra.| 
|SetBrightness|Állítsa be az eszköz fényereje.|Közepes set fényereje<br/>A magas set fényereje<br/>Set fényerő prioritásának alacsonyra történő átállításához|
|SetupDevice|Kezdeményezheti az eszköz beállítását.|Szeretnék telepíteni az operációs rendszer telepítése<br/>A telepítő.<br/>Hajtsa végre a telepítő nekem|
|ShowAppBar|Egy alkalmazás sávjának megjelenítése.|Az alkalmazás sávjának megjelenítése<br/>Alkalmazás sáv meg<br/>Én választom az alkalmazássáv lásd:|
|ShowContextMenu|A helyi menü megjelenítése|Tekintse meg a helyi menü velem<br/>Helyi menü.<br/>Megmutatnád számomra a helyi menüben|
|Alvó állapot|Az eszköz alvó állapotba helyezi.|menj aludni<br/>Alvó állapot<br/>A számítógép alvó állapota|
|SwitchApplication|Váltson az alkalmazás számára az eszköz használatát.|Váltson a saját media player.|
|TurnDownBrightness|Kapcsolja be az eszköz fényerő le.|A képernyő Dim.|
|TurnOffSetting|Kapcsolja ki a beállítást.|Bluetooth inaktiválása<br/>Tiltsa le az adatokat<br/>Bluetooth leválasztása|
|TurnOnSetting|Kapcsolja be a beállítást.|Bekapcsolva <br/> Ki|
|TurnUpBrightness|Kapcsolja be eszköz fényerő.|Módosíthatja a képernyőt világosabb?|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Alkalmazásnév | Egy alkalmazás az eszköz nevét.|SoundCloud<br/>YouTube|
| BrightnessLevel | Brightness szintjének beállítása az eszközön.|100 %-os<br/>Ötven<br/>40%|
| ContactName | Egy ügyfél az eszköz nevét.|Paul<br/>Marlen maximális száma|
| DeviceType | Eszköz típusa. |Telefonszám<br/>Kindle<br/>Hordozható számítógép|
| mediaType | Az adathordozó típusát, kezeli az eszközt.|Zene<br/>Film<br/>Tévéműsorok|
| Példányokat | Beállítás vagy a beállítások panel, amely a felhasználó szeretne szerkeszteni egy típusa.|Wi-Fi<br/>Vezeték nélküli hálózat<br/>Színsémát<br/>Értesítési központ|

## <a name="places"></a>Helyek  
A helyek tartományban szándékok kezeléséhez, többek között a vállalkozások, intézmény, éttermek, nyilvános szóközöket és címek kapcsolódó lekérdezések biztosít.

### <a name="examples"></a>Példák

|Példák|
|--|
|A hely mentése a Kedvencek|
|Milyen távol az ünnepi adóazonosító?|
|Mely nem Safeway bezárja?|


### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Adjon hozzá egy olyan helyre a felhasználó Kedvencek listájához.|A hely mentése a Kedvencek<br/>Ez a cím felvétele a Kedvencek|
|CheckAccident|Kérje meg, hogy van-e valamilyen egy megadott útközben.|Van valamilyen a 880?<br/>Objektuma információk megjelenítése|
|CheckAreaTraffic|Ellenőrizze a forgalmat egy általános területen vagy highway, nem a megadott útvonalon.|Seattle forgalma<br/>Mi az a forgalmat, például a Seattle?|
|CheckIntoPlace|Ellenőrizze, hogy a közösségi média használatával történik.|A Foursquare me beadás<br/>Ellenőrizze Itt|
|CheckRouteTraffic| Ellenőrizze a forgalmat egy adott, a felhasználó által megadott útvonal.|Hogyan alakul a forgalom Mashiko?<br/>A forgalom Kirkland megjelenítése<br/>Hogyan alakul a forgalom Seattle?| 
|Megerősítés|Erősítse meg a helyre vonatkozó műveletet.|Győződjön meg róla saját éttermi foglalás.|
|Kilépés|Lépjen ki a feladat arra a helyre vonatkozó művelet.|Lépjen ki.<br/>Lépjen ki a jogosultságot utasításokat ad|
|FindPlace|Keresse meg a hely (üzleti, intézmény, vendéglátás, nyilvános címtér, cím).|Hol található a legközelebbi tár?<br/>Megtaláljanak egy jó olasz éttermi hegyi nézetben|
|GetAddress| Kérje meg a cím, egy helyen.|Az utca Robson Guu címét megjelenítése<br/>Mi az a legközelebbi Kávézik címét?| 
|GetDistance|Kérdezzen a távolság egy adott helyen.|Milyen távol az ünnepi adóazonosító?<br/>milyen távolságban az, hogy itt négyzetes Bellevue<br/>Mi az a távolság Tahoe|
|GetHours|Kérdezze meg a hely üzemel.|Mely nem Safeway bezárja?<br/>Mik azok a kezdőlap Depot az órák?<br/>Még mindig nyitva van a Kávézik?|
|GetMenu|Kérjen egy étterem menü elemei.|Nem Zucca kiszolgálása bármit kis?<br/>Mi az a Sizzler menü<br/>A Applebee menü megjelenítése|
|GetPhoneNumber| Kérdezze meg a telefonszámát helyen.|Mi az a legközelebbi Kávézik telefonszáma?<br/>A szám Depot kezdőlap meg kell adni| 
|GetPriceRange| Egy hely ár tartományán kér.|Az olcsó Zucca?<br/>Az a Cineplex féláron szerdánként?<br/>Mennyibe kerül, Sizzler teljes lobster vacsora költsége?|
|GetReviews|Kérje meg a hely felülvizsgálatai.|Cheesecase Factory felülvizsgálatok megjelenítése<br/>Olvassa el a Yelppel Cineplex értékelések|
|GetRoute|Kérje meg egy helyen irányban.|Hogyan végigvezetheti a Bellevue négyzetes<br/>Megjelenítés a legrövidebb 8. között, valamint elvégezhető 59th innen<br/>Hegyi nézet hitelesítésszolgáltatóhoz irányban első kérek|
|GetStarRating|Kérje meg a minősítést, egy helyen.|Hogyan besorolása alapján Yelppel Zucca?<br/>Hány csillagot rendelkezik a francia ruhák?<br/>A aquarium Monterrey a jó van?|
|GetTransportationSchedule|Szerezze be a busz ütemezés egy helyen.|Milyen ideje a következő busz, New York?<br/>A King County megye a buszok megjelenítése|
|GetTravelTime|Kérje az utazási időt egy meghatározott célhelyre.|Mennyi ideig tart a San Franciscóban innen érheti<br/>Mi az az utazási időn, Denver az SF|
|MakeCall eljárás|Telefonhívás helyre.|A mom hívása<br/>Szeretném, ha a Skype hívást Anna<br/>Jim hívása|
|MakeReservation|Egy étterem vagy egyéb üzleti foglalás kérelmet.|Két tonight, Zucca fenntarthat<br/>Könyv a másnapi tábla<br/>3. a Palo Alto 8: tábla|
|MapQuestions|Információkérés kapcsolatos utasításokat, vagy egy megadott út e cél kerül.|13 Seattle belvárosában továbbítása?<br/>Használhatom-e 880 Oakland?|
|Minősítés|Egy étterem vagy a hely leírása minősítés beszerzése.|Hány csillagot rendelkezik a Contoso adóazonosító?|
|ReadAloud|Olvassa el hangos helyek listáját.|Az első egy információs fájl<br/>Információs fájl részletei|
|SelectItem|Válasszon egy elemet egy helyre vagy helyek kapcsolatos választási listájáról.|A második érték kiválasztása<br/>Válassza ki az első|
|ShowMap|Egy terület térkép megjelenítése.|A második egy térképen megjelenítése<br/>Térkép megjelenítése<br/>Keresse meg a San Franciscóban a térképen|
|ShowNext|A következő elem megjelenítése egy sorozatban.|Következő megjelenítése<br/>a következő oldalra lépéshez|
|ShowPrevious|Sorozat megjelenítése az előző elemmel.|egy korábbi megjelenítése<br/>vissza<br/>Nyissa meg az előző|
|StartOver|Indítsa újra az alkalmazást, vagy új munkamenet indításához.|Újrakezdés<br/>Új munkamenet<br/>
restart|
|TakesReservations|Kérje meg, hogy helyet fogad-e a foglalások.|Nem a tárban foglalások elfogadása<br/>Az lehetséges, ha szeretné tenni egy foglalást a olívaolaj kertben

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AbsoluteLocation | A hely vagy a hely címe.|Rendszert futtató Palo Alto<br/>300 112th Ave képlet használata<br/>Seattle|
| Eszközök | A cél jellemzők/előnyei egy helyen.|gyerek étkezési ingyenes<br/>i partszakasz<br/>ingyenes várakozást|
| Levegőben | A levegőben hely.|gyerek mobilbarát<br/>az alkalmi éttermi<br/>sporty|
| Cuisine | Egy hely cuisine. |Mediterrán<br/>olasz<br/>Indiai|
| DestinationAddress| A célhely vagy címe.|Rendszert futtató Palo Alto<br/>300 112th Ave képlet használata<br/>Seattle|
| DestinationPlaceName| A cél, amely egy üzleti, vendéglátás, nyilvános vonzás vagy intézmény neve.|Közép-India park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | A cél, amely egy helyi üzleti, vendéglátás, nyilvános vonzás vagy intézmény típusa. |Étterem<br/>Opera<br/>mozi|
| Távolság | A távolság egy helyen.|15 mérföld<br/>5 mérföld<br/>10 mérföld távolságban|
| MealType | Például a reggeli vagy ebédszünetet beiktatni dara típusa. |A reggeli<br/>Vacsora<br/>Ebéd<br/>Supper|
| OpenStatus | Azt jelzi, hogy egy helyen nyílt vagy lezárt.|Open program<br/>Lezárt<br/>megnyitása|
| PlaceName | A hely neve.|Cheesecake gyári|
| PlaceType | Hely típusa.|Kávéház<br/>Terület<br/>Erőforrástár|
| PreferredRoute | Az előnyben részesített a felhasználó által megadott útvonal. | 101 <br/>202 <br/>401-es útvonal|
| Product | A termék által egy helyen érhető el. | Ruhák<br/>Az ASR digitális fényképezőgépek<br/>Friss hal | 
| PublicTransportationRoute | A nyilvános szállítás útvonal, amely a felhasználó keres neve. | Északkelet folyosó train<br/>3-szor Bus útvonal |
| Minősítés | A helyszín minősítése. | 5 csillag<br/>3 csillag<br/>4 csillag|
| RouteAvoidanceCriteria | Kerülje a meghatározott útvonalakat feltételeit, például balesetek, építmények vagy útdíjak elkerülése | Útdíjak <br/>Építmények<br/>Útvonal 11|
| ServiceProvided | Ez a szolgáltatás egy üzleti vagy a feladat, például a hely által a hó plowing, környezetével. | feladat<br/>szerelő<br/>plumber|
| TransportationCompany | A szállítási szolgáltató neve.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | A szállítási típusa.|Busz<br/>Betanítás<br/>Vezetés|

## <a name="reminder"></a>Emlékeztető 
Az emlékeztető tartományban szándékok és entitások létrehozására, szerkesztésére és emlékeztetők keresése a biztosít.

### <a name="examples"></a>Példák

|Példák|
|--|
|Módosítsa a Kérdezési holnap 9: 00|
|Emlékeztessen tej a kezdőlap vissza saját módon megvásárlása|
|Ha az emlékeztető Christine a születésnap is ellenőrizheti?|


### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Módosítás| Emlékeztető módosítása|Módosítsa a Kérdezési holnap 9: 00<br/>A hozzárendelés emlékeztető holnap áthelyezése|
| Létrehozás| Hozzon létre egy új emlékeztető.|Hozzon létre egy emlékeztető<br/>Emlékeztessen tej megvásárlása<br/>Szeretném Rebecca hívásokat otthonról dolgozom a vágólapra|
| Törlés | Emlékeztető törlése.|Saját kép emlékeztető törlése<br/>Ezt az emlékeztetőt törlése|
| Keresés | Emlékeztető található.|Van egy emlékeztető az évfordulós kapcsolatban?<br/>Ha az emlékeztető Christine a születésnap is ellenőrizheti?|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Szöveg | Emlékeztető szöveges leírása.|vegytisztítás csomópontmetrikák<br/>autómon elvetését a szolgáltatás középen|

## <a name="restaurantreservation"></a>RestaurantReservation 
A RestaurantReservation tartományban szándékok és entitások éttermi foglalások felügyeletével kapcsolatos biztosít.

### <a name="examples"></a>Példák

|Példák|
|--|
|Két tonight, Zucca fenntarthat|
|Könyv BJ a másnapi egy táblázat|
|3. a Palo Alto 7 tábla|

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Foglalás | Egy étterem foglalás kérelmet. |Két tonight, Zucca fenntarthat<br/>Könyv a másnapi tábla<br/>3. a Palo Alto 7 tábla|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Cím| Egy esemény helyre vagy egy foglalás címe.|Rendszert futtató Palo Alto<br/>300 112th Ave képlet használata<br/>Seattle|
| Eszközök | Az eszközök egy helyszín leíró attribútum.|óceán megtekintése<br/>nem fogyasztási|
| Alkalmazásnév | Egy alkalmazás végzett foglalásokat neve.|TáblaMegnyitása<br/>Yelppel<br/>TripAdvisor|
| Levegőben | Egy étterem vagy más helyen a levegőben leírása.|romantic<br/>az alkalmi<br/>a csoportok jó|
| Cuisine | Élelmiszer, cuisine vagy cuisine állampolgárság típusát. |kínai<br/>olasz<br/>Mexikói|
| MealType | A Foglalás társított dara típus.|A reggeli<br/>Vacsora<br/>Ebéd<br/>Supper|
| PlaceName | Egy helyi üzleti, vendéglátás, nyilvános vonzás vagy intézmény neve.|IHOP<br/>Cheesecake gyári<br/>Louvre|
| PlaceType | Egy helyi üzleti, vendéglátás, nyilvános vonzás vagy intézmény típusa.|Éttermek<br/>Opera<br/>mozi|
| Minősítés | Egy hely vagy egy éttermi minősítése.|5 csillag<br/>3 csillag<br/>4 csillag|

## <a name="taxi"></a>Taxi 
 
A Taxi tartományban szándékok és entitások létrehozására és kezelésére taxi foglalások biztosít.

### <a name="examples"></a>Példák

|Példák|
|--|
|Cab-fájl lekérése me egy du. 3:|
|Mennyi már rendelkezik saját taxi várni?|
|Megszakítja az Uber|

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Könyv | Hívja meg a taxi. |A cab-fájl első kérek<br/>Keresse meg az-i taxik<br/>Az uber repülőjáratra me x|
| Mégse | Foglalás-i taxik vonatkozó művelet törlése.|Saját taxi megszakítása<br/>Megszakítja az Uber|
| Követés | Nyomon követheti a taxi útvonalat.|Mennyi már rendelkezik saját taxi várni?<br/>Hol található az Uber?|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Cím| Foglalás-i taxik társított cím. |Rendszert futtató Palo Alto<br/>300 112th Ave képlet használata<br/>Seattle|
| DestinationAddress| A célhely vagy címe. |Rendszert futtató Palo Alto<br/>300 112th Ave képlet használata<br/>Seattle|
| DestinationPlaceName | A cél, amely egy helyi üzleti, vendéglátás, nyilvános vonzás vagy intézmény neve. |Közép-India Park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | A cél, amely egy helyi üzleti, vendéglátás, nyilvános vonzás vagy intézmény típusa. |Étterem<br/>Opera<br/>mozi|
| PlaceName | Helyi üzleti, vendéglátás, nyilvános vonzás vagy intézmény neve. |Közép-India Park<br/>Safeway<br/>Walmart|
| PlaceType| A kérelem-i taxik foglalható le a hely típusa.|Étterem<br/>Opera<br/>mozi|
| TransportationCompany | A szállítási szolgáltató neve.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | A szállítási típusa.|Busz<br/>Betanítás<br/>Vezetés|

## <a name="translate"></a>Fordítás 
A fordítás tartományban szándékok és entitások kapcsolatos szöveget a cél nyelvre fordításával biztosít.

### <a name="examples"></a>Példák

|Példák|
|--|
|Francia fordítás|
|Német hello fordítása|
|Ezt a mondatot angol fordítása|


### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Fordítás| Egy másik nyelven használható szövegfordításra.|Francia fordítás<br/>Német hello fordítása|


### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| TargetLanguage | Egy fordítási Célnyelv.|francia<br/>német<br/>koreai|
| Szöveg | A fordítandó szöveg.|Hello World<br/>jó reggelt<br/>jó estét|

## <a name="tv"></a>TV 
 
A TV tartományban szándékok és entitások biztosít televízióra szabályozása.

### <a name="examples"></a>Példák

|Példák|
|--|
|BBC kapcsoló csatorna|
|Útmutató TV show|
|Tekintse meg a földrajzi National|

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| ChangeChannel| A TV-csatorna módosítása|CNN csatorna módosítása<br/>BBC kapcsoló csatorna<br/>Megnyithatja a csatornát 4|
| ShowGuide| Útmutató a TV megjelenítése.|Útmutató TV show<br/>Mi az a film csatornán most?<br/>a program lista megjelenítése|
| WatchTV| Kérje meg, hogy egy TV-csatorna megtekintése.|Szeretném a Disney-csatorna megtekintése<br/>Ugrás a TV.<br/>Tekintse meg a földrajzi National|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| ChannelName | TV-csatorna neve.|CNN<br/>BBC<br/>Film-csatorna|

## <a name="utilities"></a>Közművek  
A segédprogramok tartományban szándékok számos feladat, például üdvözletek, törlését, megerősítő, Súgó, ismétlődés, navigációs, elindítása és leállítása a feladatokat biztosít.

### <a name="examples"></a>Példák

|Példák|
|--|
|Lépjen vissza a Twitteren|
|Kérjük, segítsen|
|Ismételje meg az utolsó kérdés.|


### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Mégse | Művelet törlése.|Az üzenet törlése<br/>Nem szeretnék többé elküldeni az e-mailben|
| Megerősítés | Erősítse meg a műveletet.|Igen hoppá megerősítem, hogy<br/>Jól tudom vagyok megerősítése<br/>Rendben, szeretnék vagyok megerősítése|
| FinishTask | Fejezze be a felhasználó egy feladatot.|Már nem használom<br/>Végeztem<br/>Művelet befejeződött|
| GoBack | Lépjen vissza egy lépést, vagy térjen vissza az előző lépésben.|Lépjen vissza a Twitteren<br/>Lépjen vissza az adott lépés<br/>Vissza|
| Súgó | Kérelem segítséget.|Kérjük, segítsen<br/>Súgó megnyitása<br/>help|
| Ismétlés | Ismételje meg a műveletet.|Ismételje meg az utolsó kérdés.<br/>Ismételje meg a legutóbbi dal|
| ShowNext | A következő elem megjelenítése egy sorozatban. |Következő megjelenítése<br/>a következő oldalra lépéshez|
| ShowPrevious | Sorozat megjelenítése az előző elemmel.|egy korábbi megjelenítése|
| StartOver | Indítsa újra az alkalmazást, vagy új munkamenet indításához.|Újrakezdés<br/>Új munkamenet<br/>restart|
| Leállítás | Állítsa le a műveletet.| Arról tájékoztatja a kérjük leállítása<br/>fogd be<br/>Állítsa le.|

## <a name="weather"></a>Időjárás 
Az időjárási tartományban szándékok és entitások biztosít időjárás-előrejelzéseket és előrejelzések.

### <a name="examples"></a>Példák

|Példák|
|--|
|szeptembertől londoni időjárás|
|Milyen? a 10 nap-előrejelzési s?|
|Mi az az átlaghőmérséklet Indiában szeptembertől?|


### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| GetCondition | Időjárási kapcsolatos korábbi tények beolvasása. |szeptembertől londoni időjárás<br/>Mi az az átlaghőmérséklet Indiában szeptembertől?|
| GetForecast | Az aktuális időjárás beolvasása, és jelezze előre a következő néhány napra. |Hogyan alakul mai időjárás?<br/>Mi az a 10 nap-előrejelzési?<br/>Hogyan fogja a hétvégi az időjárás?|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Hely| Az időjárási kérelem abszolút helye.|Seattle<br/>Párizs<br/>Rendszert futtató Palo Alto|

## <a name="web"></a>Web 
A webes tartomány megjelölésű biztosít ellenőrizheti, hogy egy webhely.

### <a name="examples"></a>Példák

|Példák|
|--|
|Navigáljon a Facebook.com weboldalt|
|Ugrás a www.twitter.com|
|Navigáljon a www.bing.com|

### <a name="intents"></a>Leképezések
| Leképezés neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Navigálás | Egy kérelem egy adott webhelyre navigálhat. |Navigáljon a Facebook.com weboldalt<br/>Ugrás a www.twitter.com|

