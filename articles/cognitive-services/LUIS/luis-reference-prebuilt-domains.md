---
title: Előre elkészített útmutatója - Azure |} Microsoft Docs
titleSuffix: Azure
description: Az előre elkészített tartományokhoz, amelyek gyűjteményei előre elkészített leképezések és entitásokat a nyelvi ismertetése intelligens szolgáltatások (LUIS) hivatkozás.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 14c53bd25913922a0cd7cc438ad0fbe8b4663dd1
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061964"
---
# <a name="prebuilt-domain-reference"></a>Előre összeállított tartományhivatkozás
Ez az útmutató információt nyújt a [előre elkészített tartományok](luis-how-to-use-prebuilt-domains.md), amely gyűjteményei előre elkészített leképezések és entitások LUIS kínál.

[Egyéni tartományok](create-new-app.md), ellentétben nem leképezések és modellek kezdődhet. Egy egyéni modell bármely előre elkészített tartomány leképezések és entitások adhat hozzá.

## <a name="list-of-prebuilt-domains"></a>Előre elkészített tartományok listáját
LUIS 20 előre elkészített tartományok kínál. 

| Előre elkészített tartomány | Leírás | Támogatott nyelvek |
| ---------------- |-----------------------|:------:|
| Naptár | A naptár tartomány leképezés és entitások hozzáadásával, törlésével, vagy egy időpontot egyeztessen szerkesztésére, résztvevők rendelkezésre állásának ellenőrzése és adatait, a naptár esemény biztosít.| hu-HU<br/> zh-CN |
| Kamera | A kamera tartomány leképezések és entitásokat biztosít képek, a rögzítés videók és a szórásos videó véve egy alkalmazáshoz.| hu-HU |
| Kommunikáció | Üzenetek küldése és telefonhívások.| hu-HU <br/> zh-CN |
| Szórakozás  | Zene, filmek és TV kapcsolódó lekérdezések kezelése.| hu-HU |
| Események | A jegyektől Koncertek, fesztiválok, sport játékok és comedy foglalás jeleníti meg.| hu-HU |
| Alkalmasságra | Kezelési alkalmasságra tevékenységek nyomon követéséhez kapcsolódó kérelem.| hu-HU |
| Játékok | Több résztvevős játék játék féllel kapcsolatos kérések kezelésével.| hu-HU |
| HomeAutomation | Intelligens otthoni eszközök, például fény és készülékek vezérlése.| hu-HU<br/> zh-CN |
| MovieTickets | Egy movie képernyős filmeket a jegyektől foglalás.| hu-HU |
| Zene | Zene játsszon tovább zenelejátszó.| hu-HU<br/> zh-CN |
| Megjegyzés | A Megjegyzés tartomány leképezések és a kapcsolódó létrehozására, szerkesztésére és megjegyzések keresése entitások biztosít.| hu-HU<br/> zh-CN |
| OnDevice | A OnDevice tartomány leképezések és az eszköz vezérlésére kapcsolódó entitások biztosít.| hu-HU<br/> zh-CN |
| Helyek  | Vagy hasonló vállalatok számára, intézmények, éttermekben, nyilvános szóközöket és címek kapcsolódó lekérdezések kezelése.| hu-HU<br/> zh-CN |
| Emlékeztető | Kezelési létrehozására, szerkesztésére és emlékeztetők keresése kapcsolódó kérelem.| hu-HU<br/> zh-CN |
| RestaurantReservation | Kezelési kérelem éttermi foglalások kezelése.| hu-HU<br/> zh-CN |
| Taxi | A taxiban foglalások kezelése.| hu-HU<br/> zh-CN |
| Fordítás | A megadott nyelv fordítása szöveg.| hu-HU<br/> zh-CN |
| TV | Televízió vezérlése.| hu-HU |
| Közművek  | Kezelési kérelem, az adott sok tartományokban, például a "Súgó", "ismételje meg a", "kezdje újra a folyamatot."| hu-HU |
| Időjárás | Időjárás-jelentések és előrejelzések beolvasásakor.| hu-HU<br/> zh-CN |
| Web | Navigálás a webhelyen.| hu-HU<br/> zh-CN |

Minden tartományban további részletekért tekintse meg a következő szakaszok.

## <a name="calendar"></a>Naptár 

A naptár tartomány leképezések és a kapcsolódó naptárbejegyzések entitások biztosít. A naptár leképezések hozzáadása, törlése vagy egy időpontot egyeztessen szerkesztése, rendelkezésre állásának ellenőrzése és a Naptári tétel vagy időpontot egyeztessen vonatkozó információk keresése tartalmaznak.

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Hozzáadás | Egy új egyszeri elem felvétele a naptárban.| Ellenőrizze egy időpontot egyeztessen az Lisa 2 du. vasárnap <br/><br/>Értekezlet ütemezése<br/><br/>Értekezlet kell|
| CheckAvailability | Egy időpontot egyeztessen vagy a felhasználó vagy egy másik személyre naptárat értekezlet rendelkezésre állási található.| Ha van elérhető teljesítéséhez Jim? <br/><br/>Carol elérhető esetén holnap megjelenítése<br/><br/>Az Chris szabad szombaton?|
| Törlés | Kérelem pedig törölheti a naptár bejegyzést.| Szakítsa meg a időpontot egyeztessen az Carol. <br/><br/>A reggel 9 értekezlet törlése<br/>|
| Szerkesztés | Módosíthatja egy meglévő értekezlet vagy naptár belépési kérelmet.| Helyezze át a reggel 9 értekezlet 10 óra.<br/><br/>Szeretném a ütemezésének frissítése.<br/><br/>Értekezlet a Reschdule Ryan együtt.|
| Keresés | Heti naptár megjelenítéséhez.| A Keresés a dentist tekintse át az időpontot egyeztessen. <br/><br/>a naptár megjelenítése<br/>|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Hely | Naptárelem, az értekezlet vagy az időpontot egyeztessen helyét. Címek városokat és régiók helyek jó példák.| 209 Nashville Fitnessklub <br/><br/>897 pancake house<br/><br/>Garázs|
| Tárgy | A cím értekezlet vagy időpontot egyeztessen.| Dentist tartozó időpontot egyeztessen <br/><br/>A Ágnes ebéd<br/><br/>Orvosi időpontot egyeztessen|

## <a name="camera"></a>Kamera 
A kamera tartományi biztosítja a leképezések és entitásokat a fényképezőgép használatát. A leképezések fénykép, selfie, képernyőkép vagy videó rögzítése, és teszi közzé a videót, amely az alkalmazás tartozik.

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| CapturePhoto| Egy fénykép rögzíti.| Egy fénykép<br/><br/>rögzítése|
| CaptureScreenshot | A képernyőkép rögzítése.| Képernyőfelvétel igénybe vehet.<br/><br/>rögzítse a képernyő.|
| CaptureSelfie | Egy selfie rögzíti.| Egy selfie igénybe <br/><br/>készíthet egy képet arról nekem |
| CaptureVideo | A rögzítés videó elindítása.| Felvétel indítása <br/><br/>A rögzítés megkezdéséhez|
| StartBroadcasting| Indítsa el a szórásos videó.| Indítsa el a szórásos a Facebook-on|
| StopBroadcasting| Állítsa le a szórásos videó.| Állítsa le a szórásos|
| StopVideoRecording| Rögzítés a videó.| Ez elég<br/><br/>Rögzítés|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Alkalmazásnév | Szórási videót, amely az alkalmazás neve.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Kommunikáció 
A kommunikációs tartomány leképezések és e-mailek, az üzenetek és a telefonhívások kapcsolódó entitások biztosít.

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AddContact| Egy új partner felvétele a felhasználó listájához.|Új kapcsolat felvétele <br/><br/>Mentse ezt a számot, és helyezze a neve, ahogyan Carol|
| AddMore| Adja hozzá több, egy e-mailek vagy a szöveg, többlépcsős e-mailben vagy szöveges összeállítás részeként.|További szöveg hozzáadása <br/><br/>Több e-mail törzse is hozzáadása|
| Válasz| Bejövő telefonhívás fogadása.|a hívás <br/><br/>Emelje fel|
| AssignContactNickname| Becenév rendelni egy ügyfélhez.|Módosítsa Isaac dad <br/>Szerkesztés Jim becenév<br/>Patti Owens becenév hozzáadása|
| CallVoiceMail| A felhasználó hangposta csatlakozni.|Csatlakoztatás a hangposta mezőbe <br/>Hangposta<br/>hívás hangposta|
| CheckIMStatus| Egy partner Skype állapotának ellenőrzéséhez.|Jim tartozó online állapot értéke számítógépnél? <br/>Carol érhető el partnerünk?|
| Megerősítés| Erősítse meg a műveletet.|Igen<br/>Rendben<br/>rendben<br/>Megerősítem, hogy szeretnénk elküldeni az e-mailt.<br/>|
| telefonos kapcsolat| Telefonhívás.|Hívás Jim<br/>Adjon tárcsázza a 311<br/>|
| FindContact| Kapcsolattartási adatok Keresés név alapján.|Carol található<br/>Carol számának megjelenítése<br/>|
| FindSpeedDial| A telefonszám van beállítva, a, és ez fordítva is igaz speedial szám található.|Mi az a tárcsázandó 5?<br/>Tárcsázza a készlet sebesség van?<br/>Mi az a telefonszáma 941-5555-333?|
| GetForwardingsStatus| Átirányítás aktuális állapotának beolvasása.|Az átirányítás engedélyezve van?<br/>Figyelmeztessen, ha az állapot vagy be van kapcsolva<br/>|
| GoBack| Térjen vissza az előző lépésben.|Lépjen vissza a twitteren<br/>Lépjen vissza a lépés<br/>Vissza|
| Kihagyás| Egy bejövő hívás figyelmen kívül.|Nem fogadja a hívást<br/>Figyelmen kívül hagyja a hívást|
| IgnoreWithMessage| Egy bejövő hívás figyelmen kívül, és ehelyett válasz szóra.|A hívás nem válaszol, de ehelyett elküldeni az üzenetet.<br/>Figyelmen kívül hagyhatja, és küldje vissza egy szöveges.|
| PressKey| Nyomja le az ENTER egy gombra való kattintást vagy számát a billentyűzeten.|Telefonos kapcsolat csillag.<br/>Nyomja meg a 2-3 1.|
| ReadAloud| Olvassa el egy üzenetet vagy a felhasználó számára.|Olvassa el a szöveget.<br/>Mi volt ő fel az üzenet?|
| TurnForwardingOff| Telefonhívás.|<br/><br/>|
| Újratárcsázás| Újratárcsázás, vagy egy szám hívja meg ismét.|Újratárcsázás.<br/>Az utolsó hívás Újratárcsázás.|
| Elutasítás| Egy bejövő hívás elutasítása.|Hívás elutasítása<br/>Most már nem válaszol<br/>Pillanatnyilag nem érhető el, és a szolgáltatás fel fogja hívni vissza később.|
| Sendemail művelet| E-mailt küldeni. Ez a leképezés e-mailek, de nem a szöveges üzenetek vonatkozik.|E-mailek nagy vizek: Mike, hogy vacsora múlt héten splendid volt.<br/>E-mail küldése a Bob<br/>|
| SendMessage| Egy szöveges üzenetet vagy azonnali üzenet küldése.|Szöveg Chris és Carol küldése|
| SetSpeedDial| Telefonos kapcsolat sebessége parancsikon be a telefonszámát.|Carol egy gyors tárcsázás be.<br/>A mom gyors tárcsázás beállítása.|
| ShowNext| Jelenik meg a következő elem, például a szöveges üzenetek vagy e-maileket.|A következő egy megjelenítése.<br/>Nyissa meg a következő oldalra.|
| ShowPrevious| Lásd az előző elemnél, például a szöveges üzenetek vagy e-mailek listája.|Az előzőre megjelenítése.<br/>Előző<br/>Ugrás az előzőre.|
| StartOver| A rendszer kezdje újra a folyamatot, vagy indítson egy újat.|Újrakezdés<br/>Új munkamenet<br/>restart|
| TurnForwardingOff| Kapcsolja ki az átirányítás.|a hívások továbbítani<br/>Kapcsolja ki az átirányítás|
| TurnForwardingOn| Kapcsolja ki a hangalapú phone.|A hívások 3333 továbbítása<br/>Hívás-továbbítást a 3333 váltani|
| TurnSpeakerOff| Kapcsolja ki a hangalapú phone.|Ugrás a hangalapú ki.<br/>Kapcsolja ki a kihangosító.<br/>|
| TurnSpeakerOn| Kapcsolja be a hangalapú phone.|Kihangosító mód.<br/>Kihangosító helyezhető el.<br/>|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AudioDeviceType | Audioeszköz (előadói, Fejbeszélő, mikrofon, stb.) típusú.| Beszélő<br/>Beavatkozás nélküli<br/>Bluetooth-on|
| Kategória | A kategória üzenet vagy e-mailt.| Fontos<br/>Magas prioritású virtuális gép|
| ContactAttribute | Az ügyfél a felhasználói attribútum vonatkozó lekérdezésekben.| Születésnapjának<br/>Cím<br/>Telefonszám|
| Ügyintéző | Egy ügyfél vagy üzenet címzettje neve.| Carol<br/>Jim<br/>Chris|
| EmailSubject | Az e-mail tárgyát használt szöveg.| ISMÉTELT: érdekes szövegegység|
| Vonal | A sor a felhasználó a hívást, vagy egy szöveges/e-mail üzenetek küldéséhez a szeretne.| Munkahelyi sor<br/>Brit cella<br/>Skype|
| Üzenet | Az üzenet, szöveges vagy e-mailek küldése.| Nagyszerű ma értekezlet meg volt. További tudnivalók, nézzen!|
| MessageType | Egy ügyfél vagy üzenet címzettje neve.| Szöveg<br/>E-mail|
| OrderReference | A listán, ha egy elem beolvasása azonosító sorszám vagy relatív pozíciója. Például a "legutóbbi" vagy "legutóbbi" a "Mi volt az utolsó üzenet elküldött?"| Vezetéknév<br/>Legutóbbi|
| SenderName | A küldő neve.| Patti Owens|

## <a name="entertainment"></a>Szórakozás  
A tartomány biztosít a leképezések és a kapcsolódó filmek, zene, játékok és TV keresése entitások Szórakozás jeleníti meg.

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Keresés| Keresse meg a filmek, zene, alkalmazások, játékok és TV jeleníti meg.|Keressen rá az áruházban Halo.<br/>Keresés a profilképet.|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| ContentRating | Multimédiás tartalom filmek például a G vagy R besorolását.|Gyermekek videó.<br/>A védelmi besorolása.|
| Genre | A genre movie, játék, alkalmazás vagy szám.|Comedies<br/>Dramas<br/>vicces|
| Kulcsszó| Adjon meg egy attribútumot általános keresési kulcsszó a nem létezik, a pontosabb media tárolóhelyre.|Számokat hozzanak<br/>Hold folyó<br/>Amelia Earhart|
| Nyelv | Multimédiás tartalom filmek például a G vagy R besorolását.|francia<br/>Angol<br/>koreai|
| MediaFormat | A további technikai futásakor, amelyben az adathordozó formázása.|HD filmeket<br/>3D filmek<br/>Letölthető|
| MediaSource | A tároló vagy piactér az beszerzése az adathordozót.|Netflix<br/>Prime|
| MediaSubTypes| Kisebb, mint filmek és játékok adathordozók típusairól.|Bemutatók<br/>DLC<br/>Pótkocsik|
| Állampolgárság| Az ország, amelyen létrehozták a movie, megjelenítése vagy szám.|francia<br/>német<br/>koreai|
| Személy| Az aktor, igazgató, készítő, zenésze vagy művésze movie, alkalmazás, játék vagy TV show.|Madonna<br/>Stanley Kubrick|
| Szerepkör| Szerepet, amelyet a személy adathordozó létrehozásához.|Sings<br/>Útmutatása alapján<br/>–|
| Beosztás| Egy movie, alkalmazás, játék, TV show vagy dal neve.|Barátok<br/>Minecraft|
| Típus| A típus vagy media formátuma egy movie, alkalmazás, játék, TV show vagy szám.|Zene<br/>MovieTV <br/>azt mutatja be|
| UserRating| Felhasználói csillag vagy minősítés OK.|5 csillagos<br/>3 csillag<br/>4 csillag|

## <a name="events"></a>Események 
Az események tartomány leképezések biztosít, és entitások kapcsolódó foglalási jegyek Koncertek, fesztiválok, sport játékok és comedy hasonló események megjelenítése.

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Könyv| Vásárolja meg a jegyektől eseményre.|Szeretnék a symphony a jegyet a hétvégi vásárolni.|


### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Cím | Események helye vagy a cím. |Palo Alto<br/>300 112th Ave használata <br/> Seattle |
| Name (Név) | Az esemény neve.|Az a Park Shakespeare|
| PlaceName| Az esemény hely nevét.|Louvre<br/>Opera ház<br/>Broadway|
| PlaceType | A hely típusa az esemény tárolandók.|Cafe<br/>Terület<br/>Részletes ismertetés|
| Típus | Az esemény típusa.|Energiaoptimalizálást egyszerre<br/>Sport játék|

## <a name="fitness"></a>Alkalmasságra 
A megkövetelt tartomány leképezések és a kapcsolódó alkalmasságra tevékenységek nyomon követése entitások biztosít. A leképezések tartalmaz megjegyzéseket, hátralévő idő vagy Distance lehet, vagy tevékenység eredmények mentése mentése.

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AddNote| Kiegészítő megjegyzések ad hozzá a nyomon követett tevékenységet.|Ehhez a futtató nehézsége 6/10 volt.<br/>A futó vagyok terület aszfalt<br/>3 sebesség kerékpárt használok|
|GetRemaining| Egy tevékenység lekéri a hátralévő idő vagy Distance lehet.|A következő lap: mennyi idő?<br/>Hány miles hátralévő saját futtatása? Mennyi időt a felosztás esetében?|
| LogActivity| Mentse, vagy jelentkezzen befejezett tevékenység eredménye.|A legutóbbi futtatás mentése<br/>A szombat reggel bejárása naplózása<br/>a korábbi szóra tárolásához|
| LogWeight| Mentse, vagy a felhasználó aktuális súly naplózása.|A jelenlegi súly mentése<br/>a súlyozással most naplózása<br/>a szervezet aktuális súly tárolásához|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Az ActivityType tulajdonság | A típusú tevékenység nyomon követéséhez. |Futtassa a következőt:<br/>Lépésről lépésre<br/>Szóra<br/>Ciklus |
| Élelmiszer | Nyomon követéséhez alkalmasságra alkalmazásban étele típusú. |Banánbehozatali<br/>Lazac<br/>Fehérje rázó|
| MealType| A rendszerállapot vagy alkalmasságra alkalmazásban nyomon étele típus.|Reggeli<br/>Vacsora<br/>Ebéd<br/>Supper|
| Mérés| Idő, távolság vagy weight alkalmasságra vagy állapotfigyelő alkalmazása mérések típusú.|Kilométerben<br/>Miles<br/>perc<br/>Kilogramm|
| Szám | Egy numerikus mennyiség alkalmasságra vagy állapotfigyelő alkalmazása.|19<br/>három<br/>200<br/>egy|
| StatType | Egy összesített adatai a alkalmasságra vagy állapotfigyelő alkalmazása statisztikai adat típusa.|Összeg<br/>Átlag<br/>Maximum<br/>Minimális|

## <a name="gaming"></a>Játékok 
A játékok tartomány leképezések és egy játék entitás több résztvevős játék felügyeletével kapcsolatos entitásokat biztosít.

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| InviteParty| Egy kapcsolat egy játék entitás csatlakozni meghívása.|A saját fél player meghívása<br/>Az entitás tudomást<br/>Csatlakozás a clan|
|LeaveParty| Egy tevékenység lekéri a hátralévő idő vagy Distance lehet.|Ki vagyok<br/>A másik fél I vagyok elhagyása<br/>I vagyok Kilépés|
| StartParty| Több résztvevős játék egy játék entitás elindítása.|Most volt egy entitás indítása<br/>egy entitás indítása<br/>kell először egy clan esti|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Kapcsolatfelvétel| Több résztvevős játék használandó kapcsolattartási nevet.|Carol<br/>Jim|


## <a name="homeautomation"></a>HomeAutomation 
A HomeAutomation tartomány leképezések és intelligens otthoni eszközök, például fény és készülékek szabályozása kapcsolódó entitások biztosít.

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Kikapcsolás| Kapcsolja ki, zárja be, vagy egy eszköz zárolásának feloldásához.|Kapcsolja ki a fény<br/>A kávét készítő leállítása<br/>Bezárás garázs ajtó|
|Bekapcsolása| Kapcsolja be az eszközön, vagy állítsa be az eszköz egy adott beállítást vagy mód.|a kávét készítő bekapcsolása<br/>kapcsolhatja be a kávét készítő?<br/>Állítsa a termosztát 72 fokban megadva.|


### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Eszköz | A típusú eszköz be és ki kell kapcsolni.|kávét készítő<br/>Termosztát<br/>fény|
| Művelet | Az állapot az eszköz beállításához.|zárolás<br/>megnyitás<br/>be<br/>kikapcsolva|
| Hely | A hely vagy az eszköz a helyiségben.|nappali<br/>szobája<br/>konyhai|

## <a name="movietickets"></a>MovieTickets 
A MovieTickets tartomány leképezések és egy movie képernyős filmeket foglalási jegyeket kapcsolódó entitások biztosít.

### <a name="examples"></a>Példák
```
Book me two tickets for Captain Omar and the two Musketeers
Cancel tickets
When is Captain Omar showing?
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Könyv | Film jegyek vásárolhat.|Lefoglalja me két jegyek kapitány Omar és a két musketeers<br/>A jegy jövő film megvásárlása kívánt<br/>2. rész Captian Omar következő szerda kívánt jegy|
|GetShowTime| Egy film showtime beolvasása.|Ha azt a kapitány Omar?|


### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Cím | Egy movie képernyős címe.|Palo Alto<br/>300 112th Ave használata<br/>Seattle|
| MovieTitle | Film címe.|A Pi élettartama<br/>Éhezés játékok<br/>Kezdete|
| PlaceName | Egy movie képernyős vagy film neve.|Film Amir<br/>Alexandria terület<br/>Győr képernyős|
| PlaceType | A hely film típusa jelenít meg.|film<br/>képernyős<br/>IMAX film|

## <a name="music"></a>Zene 
A zene tartomány leképezések és a kapcsolódó zene játsszon tovább zenelejátszó entitások biztosít.

### <a name="examples"></a>Példák
```
play Beethoven
Increase track volume
Skip to the next song
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| DecreaseVolume | Az eszköz kötet csökkentése.|track kötet csökkentése<br/>kötet le|
| IncreaseVolume | Az eszköz kötet növeléséhez.|Növelje a kötet nyomon követése<br/>kötet mentése|
| Csend |A lejátszott zene elnémíthatja.|Némító szám<br/>Helyezze a csend követése<br/>Némító zene |
| Szünet | A lejátszott zene felfüggesztése.|Szünet<br/>Felfüggesztés zene<br/>Felfüggesztés nyomon követése|
| PlayMusic | Zene lejátszása az eszközön.|Kevin Durant lejátszása<br/>Paradicsom által Coldplay lejátszása<br/>Hello által Adele lejátszása|
| Ismétlés |Ismételje meg a lejátszás zene.|Ismételje meg a dal<br/>A track nyereség lejátszása<br/>Ismételje meg a zene|
| Folytatás | Végezze el újra a lejátszott zene.|RESUME-dal<br/>Indítsa el újból a zene<br/>Folytatásához|
| SkipBack | Kihagyása visszamenőleges nyomon.|Ugorjon a következő dal<br/>A következő lejátszása|
| SkipForward |Ugrás előre nyomon.|Az előző lejátszása<br/>Lépjen vissza az előző szám |
| Leállítás | Zene lejátszás vonatkozó művelet leállítása. |A lemez megszakad.|
| Ki bekapcsolása | Ki-zene lejátszás eszköz bekapcsolása.| Ki bekapcsolása.|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| ArtistName | Az aktor, igazgató, készítő, író, zenésze vagy művésze társított adathordozó számára, hogy az eszközön.|Elvis Presley<br/>Taylor Swift<br/>Adele<br/>Mozart|
| Genre | A kért zene genre.|Ország zene<br/>Broadway classics<br/>A Baroque időszakba eső klasszikus zene lejátszása|

## <a name="note"></a>Megjegyzés 
A Megjegyzés tartomány leképezések és a kapcsolódó létrehozására, szerkesztésére és megjegyzések keresése entitások biztosít.

### <a name="examples"></a>Példák
```
Add to my groceries note lettuce tomato bread coffee
Check off bananas from my grocery list
Remove all items from my vacation list
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AddToNote | -Adatokat hozzáadni a megjegyzés.|A élelmiszerszükségletét Megjegyzés saláta paradicsom be kávé hozzáadása<br/>A teendőlista hozzáadása<br/>a Wunderlist cupcakes hozzáadása|
| CheckOffItem | Pipálja ki egy már meglévő megjegyzés szereplő elemeket.|Jelölje ki a bevásárlólista banánmennyiség<br/>Jelölje meg a szünnap vásárlás lista, mint a sajt torta|
| Törlés | Egy már meglévő megjegyzés összes elem törléséhez.|Az összes elem eltávolítása a szabadsága listából<br/>Törölje a jelet minden saját olvasási listából|
| Megerősítés | Erősítse meg az arra vonatkozó műveletet.|Az általam kapcsolatát<br/>igen<br/>Megőrzi az összes elemet a listák I vagyok megerősítése|
| Létrehozás | Új jegyzet létrehozása. | Hozzon létre egy listát<br/>Vegye figyelembe, hogy Jason Emlékeztessen a város első hetében, előfordulhat, hogy|
| Törlés | Egy teljes megjegyzés törlése. |A szabadsága lista törlése <br/>a élelmiszerszükségletét Megjegyzés törlése|
| DeleteNoteItem | Elemeket töröl egy már meglévő megjegyzés.| A bevásárlólista modulok törlése<br/>A lista vásárlás iskolai toll eltávolítása|
| ReadAloud | Hangos olvasás listáját.|Az információs fájl<br/>A részleteiből tájékozódjon|
| ShowNext | Tekintse meg a következő elem megjegyzések listáját.|A következő egy megjelenítése<br/>Következő oldal<br/>Következő lépés|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Alkalmazásnév | A Megjegyzés-és alkalmazás-neve.|Wunderlist<br/>OneNote|
| Ügyintéző | Megjegyzés a partner neve.|Carol<br/>Jim<br/>Chris|
| Adatforrás | Megjegyzések helye.|OneDrive<br/>Google docs<br/>a számítógép|
| Adattípus | A fájl vagy a dokumentumot, általában az adott szoftver társított típusát.|Diák<br/>Számolótábla<br/>Munkalap|
| Szöveg | Megjegyzés: vagy emlékeztető szöveg.|a Stretch előtt érdekében<br/>hosszú futtatásukkor holnap|
| Beosztás | Megjegyzés címe.|élelmiszerszükségletét<br/>felhasználó<br/>Tennivalók|

## <a name="ondevice"></a>OnDevice 
A OnDevice tartomány leképezések és az eszköz vezérlésére kapcsolódó entitások biztosít.

### <a name="examples"></a>Példák
```
Close video player
Cancel playback
Can you make the screen brighter?
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AreYouListening | Kérje meg, ha az eszközök figyel.|Ez a van?<br/>figyelnek?|
|CloseApplication|Zárja be az alkalmazást.|videólejátszó bezárása|
|FileBug|Hibajelentést az eszközön.|Adjon hibajelentést<br/>Tudja meg hibajelentést a számomra?<br/>Én ezen hiba jelentése|
|GoBack|Kérje meg, hogy lépjen vissza egy lépést vagy térjen vissza az előző lépésben.|Adja vissza<br/>Ugrás az előző képernyőre<br/>Lépjen vissza a stop-figyelő|
|Súgó| Segítséget kérni.|Adjon Súgó<br/>Üdvözöljük!<br/>Mire szolgál?<br/>Segítségre van szükségem| 
|LocateDevice|Keresse meg az eszközt.|Megtalálhassák a telefonomat<br/>Tom tartozó iphone keresése<br/>Telefon|
|Bejelentkezés|Jelentkezzen be a szolgáltatást, az eszköz használatával.|Bejelentkezési adjon<br/>A Facebook-napló<br/>Jelentkezzen be a LinkedIn|
|Kijelentkezés|Jelentkezzen ki a szolgáltatást, az eszköz használatával.|Jelentkezzen ki telefonomra<br/>Jelentkezzen be a twitteren<br/>Kijelentkezés|
|MainMenu|A főmenü eszközök megtekintése.|Nézet menü.|
|OpenApplication|Nyisson meg egy alkalmazást az eszközön.|Nyissa meg a riasztás adjon<br/>Kamera bekapcsolása<br/>Indítsa el a naptár|
|OpenSetting|Nyissa meg a beállítást, az eszközön.|Nyissa meg a hálózati beállításokat.|
|PairDevice|Az eszköz pár.|Akkor segítik kérek a Bluetooth jel telefonjára párosítás<br/>A bluetooth bekapcsolásának és párosítsa azt hordozható számítógép<br/>Pár Bluetooth jel hordozható számítógéphez|
|Kikapcsolás | Kapcsolja ki az eszközt.|Állíthatják le a számítógépemen<br/>Leállítás<br/>A mobile kikapcsolása|
|QueryBattery|Eszközakkumulátor élettartamának adatainak beolvasása.|Eszközakkumulátor élettartamának megjelenítése.<br/>Mi az az akkumulátor állapota<br/>Mennyi töltöttség most?<br/>Jelenjen meg töltöttség esetén|
|QueryWifi|Wi-Fi adatainak beolvasása.|Wi-Fi-adatok beolvasása.|
|Újraindítás|Indítsa újra az eszközt.|Indítsa újra.|
|RingDevice| Kérje meg az eszköz a telefonja, ahhoz, hogy megtalálja, ha rendelkezik elveszett.|Telefonszám gyűrű.| 
|SetBrightness|Az eszköz fényerő beállítása.|Set fényerő közepes<br/>Set fényerő magas<br/>Set fényerő alacsony értékre|
|SetupDevice|Indítsa el az eszközt.|Szeretnék telepíteni az operációs rendszer telepítése<br/>Adjon beállítása<br/>A telepítő tegye a számomra|
|ShowAppBar|Egy alkalmazás sávjának megjelenítése.|Az alkalmazás sávjának megjelenítése<br/>Alkalmazás sáv kérjük<br/>Az alkalmazás sáv én|
|ShowContextMenu|A helyi menü megjelenítése.|Én tekintse meg a helyi menü<br/>A helyi menü adjon<br/>Tudja meg jelenjen meg a helyi menü|
|Alvó állapot|Az eszköz alvó állapotba helyezni.|menj aludni<br/>Alvó állapot<br/>A számítógép alvó állapota|
|SwitchApplication|Váltás az alkalmazás használata az eszközön.|Váltás a saját media player.|
|TurnDownBrightness|Kapcsolja be az eszköz fényerő le.|A képernyő Dim.|
|TurnOffSetting|Kapcsolja ki a beállítást.|Bluetooth inaktiválása<br/>Tiltsa le az adatok<br/>Válassza le a Bluetooth-on|
|TurnOnSetting|Kapcsolja be a beállítást.|Bekapcsolva <br/> Ki|
|TurnUpBrightness|Eszköz fényerő bekapcsolásáról.|Győződhet meg a képernyő fényesebbre?|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Alkalmazásnév | Az eszközön az alkalmazás neve.|SoundCloud<br/>YouTube|
| BrightnessLevel | Értékével beállítása az eszközön.|Száz százaléka<br/>Ötven<br/>40%|
| Ügyintéző | Egy ügyfél az eszköz nevét.|Paul<br/>Marlen maximális száma|
| DeviceType | Milyen típusú eszköz. |Telefonszám<br/>Kindle<br/>Hordozható számítógép|
| Adathordozó típusa | A médiatípus kezeli az eszközt.|Zene<br/>Film<br/>Tévéműsorok|
| Kiszolgálónévhez | A beállítás vagy a beállítások panel, amely a felhasználó szeretne szerkesztése típus.|Wi-Fi<br/>Vezeték nélküli hálózat<br/>Színséma<br/>Értesítési központ|

## <a name="places"></a>Helyek  
A helyek tartományi vagy hasonló vállalatok számára, intézmény, éttermekben, nyilvános szóközöket és címek kapcsolódó lekérdezések kezelése leképezések biztosítja.

### <a name="examples"></a>Példák
```
Save this location to my favorites
How far away is Holiday Inn?
At what time does Safeway close?
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AddFavoritePlace | A hely hozzáadása a felhasználói Kedvencek listájában.|Ezen a helyen mentése a Kedvencek közé<br/>Ez a cím felvétele a Kedvencek|
|CheckAccident|Kérje meg, hogy van-e egy véletlen megadott úton.|Van valamilyen nem várt esemény a 880?<br/>Baleset információk megjelenítése|
|CheckAreaTraffic|Ellenőrizze a forgalom egy általános terület vagy highway, nem a megadott útvonalon.|Budapesti forgalom<br/>Mi az a forgalmat, például a budapesti?|
|CheckIntoPlace|Ellenőrizze közösségi adathordozóval helyre.|Utánanézni me Foursquare<br/>Jelölje be|
|CheckRouteTraffic| Ellenőrizze a forgalmat egy adott felhasználó által megadott útvonal.|Hogyan van a forgalom Mashiko?<br/>A Kirkland traffice megjelenítése<br/>Hogyan van a forgalom Budapest?| 
|Megerősítés|Erősítse meg a helyre vonatkozó műveletet.|Erősítse meg a éttermi foglalás.|
|Kilépés|A művelet a kilépéshez a helyre vonatkozó feladat.|Lépjen ki a adjon<br/>Lépjen ki a jogosultságot utasításokat ad|
|FindPlace|Keresse meg a hely (üzleti, intézmény, éttermi, nyilvános helyen, a cím).|Hol található a legközelebbi könyvtár?<br/>Megtaláljanak egy jó olasz étterem hegyi nézetben|
|GetAddress| Kérje meg a helyet, a cím.|A Robson utca Guu címét megjelenítése<br/>Mi az a legközelebbi Starbucks címét?| 
|GetDistance|Kérdezze meg a megadott helyre távolság.|Milyen távolságra van szünnap Inn?<br/>milyen távolságban az, hogy itt négyzetes Bellevue<br/>Mi az a távolság Tahoe|
|GetHours|Kérdezze meg a hely üzemel.|Milyen időpontban nem Safeway bezárja?<br/>Mik az órák kezdőlap raktár számára?<br/>Folyamatban van a Starbucks?|
|GetMenu|A menü menüpontjai egy étterem kell kérni.|Nem Zucca kiszolgálására semmit vegan?<br/>Mi az a menü Sizzler:<br/>Applebee tartozó menü megjelenítése|
|GetPhoneNumber| Kérje meg a helyet, a telefonszám mellékét.|Mi az a legközelebbi Starbucks telefonszámát?<br/>Meg kell adni a szám kezdőlap raktár számára| 
|GetPriceRange| A hely ár tartomány kér.|Az Zucca olcsó?<br/>Az a Cineplex fele ár szerdánként?<br/>Mennyibe Sizzler, teljes homár vacsora költségekkel?|
|GetReviews|Kérje meg a hely ellenőrzésekhez.|A beépített Cheesecase értékelést megjelenítése<br/>Olvasási Cineplex ellenőrzi, hogy a Yelp|
|GetRoute|Kérje meg egy olyan helyre való.|Hogyan végigvezetheti a négyzetes Bellevue<br/>A lehető legrövidebb úgy a 8. és 59th innen megjelenítése<br/>Első me való hegyi nézet hitelesítésszolgáltató|
|GetStarRating|Kérje meg a hely értékelések születtek.|Hogyan értékelés szerint Yelp Zucca?<br/>Hány csillag rendelkezik a francia mosnivaló?<br/>A Monterrey akvárium jó van?|
|GetTransportationSchedule|A busz ütemezés lekérése egy helyen.|Milyen ideje a következő buszon New York?<br/>Az képeznie megye a buszok megjelenítése|
|GetTravelTime|Kérje meg a megadott helyre utazás alkalommal.|Mennyi ideig tart a innen San Francisco eléréséhez<br/>Mi ú való jelenti az vezetői idő|
|MakeCall eljárás|Telefonhívás helyre.|Mom hívása<br/>A Skype hívást Anna szeretnék<br/>Hívás Jim|
|MakeReservation|Egy étterem vagy egyéb üzleti foglalás kérelmet.|Foglaljon le Zucca: kettőt csak a tonight<br/>Egy tábla lefoglalja holnap<br/>Palo Alto 3 8: a tábla|
|MapQuestions|Információk utasításait, és hogy megadott közúti kerül a rendeltetési kérelmet.|13 New York továbbítása?<br/>I tarthat 880 Oakland?|
|Minősítés|Egy étterem vagy a hely leírása minősítés beolvasása.|Hány csillag rendelkezik a Contoso Inn?|
|ReadAloud|Hangos kiolvassa a helyek listáját.|Az információs fájl<br/>A részleteiből tájékozódjon|
|SelectItem|Válasszon ki egy elemet egy olyan helyre vagy helyek kapcsolatos lehetőségek közül.|Válassza ki a második érték<br/>Válassza ki az első|
|ShowMap|Olyan térképet terület megjelenítése.|A második egy térképre megjelenítése<br/>Térkép megjelenítése<br/>San Francisco keresse meg a térképen|
|ShowNext|A következő elem megjelenítése egy sorozatban.|A következő egy megjelenítése<br/>Nyissa meg a következő oldalra|
|ShowPrevious|Az előző elem megjelenítése azokat.|Előző megjelenítése<br/>vissza<br/>Ugrás az előzőre|
|StartOver|Indítsa újra az alkalmazást, vagy indítson egy újat.|Újrakezdés<br/>Új munkamenet<br/>
restart|
|TakesReservations|Kérje meg, hogy a hely fogad foglalások.|fogadja el foglalások a galéria<br/>Van lehetőség: a olívaolaj kerti lefoglalni úgy

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| AbsoluteLocation | A hely vagy egy helyének címét.|Palo Alto<br/>300 112th Ave használata<br/>Seattle|
| Eszközök | Egy hely objektív jellemzők/előnyeit.|gyermekek keleti-afrikai szabad<br/>partszakasz<br/>szabad várakozást|
| Légkör | A hely légkörbe.|KID mobilbarát<br/>alkalmi éttermi<br/>sporty|
| Cuisine | Egy hely cuisine. |Mediterrán<br/>olasz<br/>Indiai|
| DestinationAddress| A célhely vagy címe.|Palo Alto<br/>300 112th Ave használata<br/>Seattle|
| DestinationPlaceName| A cél, amely egy üzleti, éttermi, nyilvános vonzás vagy intézmény neve.|központi park<br/>safeway<br/>walmart|
| DestinationPlaceType | A cél, amely egy helyi üzleti, éttermi, nyilvános vonzás vagy intézmény típusa. |Étterem<br/>Opera<br/>film|
| Távolság | Egy olyan helyre, távolságot.|15 miles<br/>5 miles<br/>10 miles számítógépnél|
| MealType | Például reggeli vagy ebéd étele típusú. |Reggeli<br/>Vacsora<br/>Ebéd<br/>Supper|
| OpenStatus | Azt jelzi, hogy a hely megnyitott vagy lezárt.|Open program<br/>lezárt<br/>megnyitása|
| PlaceName | A hely neve.|Cheesecake gyári|
| PlaceType | Hely típusa.|Cafe<br/>Terület<br/>Részletes ismertetés|
| PreferredRoute | Az előnyben részesített útvonal, a felhasználó által megadott. | 101 <br/>202 <br/>401 útvonal|
| Product | A hely által kínált termék. | Munkaruházat<br/>Az ASR digitális fényképezőgépek<br/>A halszem friss | 
| PublicTransportationRoute | A nyilvános szállítására útvonalat, amely a felhasználó keres neve. | Északkelet-folyosó vonat<br/>BUS útvonal X 3 |
| Minősítés | Egy helyet minősítés. | 5 csillagos<br/>3 csillag<br/>4 csillag|
| RouteAvoidanceCriteria | Meghatározott útvonalakat például véletlenül, építmények vagy autópályadíjak elkerülhető elkerülése szempontjai | Autópályadíjak <br/>Építmények<br/>Útvonal 11|
| ServiceProvided | Ez az üzleti vagy feladat, például a hely által biztosított szolgáltatás hó plowing, környezetével. | feladat<br/>szerelő<br/>plumber|
| TransportationCompany | A szállítási szolgáltató neve.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | A szállítási típusa.|Busz<br/>Betanítás<br/>Vezetés|

## <a name="reminder"></a>Emlékeztető 
A felszólítás tartományi biztosítja a leképezések és identitások létrehozására, szerkesztésére és emlékeztetők keresése.

### <a name="examples"></a>Példák
```
Change my interview to 9 am tomorrow
Remind me to buy milk on my way back home
Can you check if I have a reminder about Christine's birthday?
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Módosítás| Emlékeztető módosítása.|Módosítsa a interjú holnap reggel 9<br/>Helyezze át a hozzárendelés emlékeztető holnap|
| Létrehozás| Hozzon létre egy új emlékeztető.|Hozzon létre egy emlékeztető<br/>Emlékeztessen tej megvásárlása<br/>Ne felejtse el Rebecca hívja, ha nem vagyok a otthoni szeretnék|
| Törlés | Emlékeztető törlése.|a kép emlékeztető törlése<br/>Ezt az emlékeztetőt törlése|
| Keresés | Emlékeztető található.|Van egy emlékeztető a évforduló kapcsolatban?<br/>Ellenőrizheti Ha Christine tartozó születési emlékeztető?|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Szöveg | Emlékeztető szöveg leírása.|vegytisztítás átvételéhez<br/>a car eldobása a szolgáltatás központban|

## <a name="restaurantreservation"></a>RestaurantReservation 
A RestaurantReservation tartomány leképezések és a kapcsolódó éttermi foglalások kezelése entitások biztosít.

### <a name="examples"></a>Példák
```
Reserve at Zucca for two for tonight
Book a table at BJ's for tomorrow
Table for 3 in Palo Alto at 7
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Tartalék | Egy étterem foglalás kérelmet. |Foglaljon le Zucca: kettőt csak a tonight<br/>Egy tábla lefoglalja holnap<br/>A 3. a Palo Alto 7 tábla|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Cím| Egy esemény hely vagy a Foglalás címét.|Palo Alto<br/>300 112th Ave használata<br/>Seattle|
| Eszközök | A hely eszközök leíró attribútum.|óceáni megtekintése<br/>nem fogyasztási|
| Alkalmazásnév | Adja meg a megfelelő foglalások alkalmazás neve.|TáblaMegnyitása<br/>Yelp<br/>TripAdvisor|
| Légkör | Egy étterem vagy más hely légkör leírását.|romantic<br/>alkalmi<br/>a csoportok jó|
| Cuisine | Étele, cuisine vagy cuisine állampolgárság típusú. |kínai<br/>olasz<br/>Mexikói|
| MealType | A Foglalás rendelt étele típusát.|Reggeli<br/>Vacsora<br/>Ebéd<br/>Supper|
| PlaceName | Egy helyi üzleti, éttermi, nyilvános vonzás vagy intézmény neve.|IHOP<br/>Cheesecake gyári<br/>Louvre|
| PlaceType | A helyi vállalati, a éttermi, a nyilvános vonzás vagy az intézmény típusa.|étterem<br/>Opera<br/>film|
| Minősítés | Egy hely vagy egy éttermi minősítése.|5 csillagos<br/>3 csillag<br/>4 csillag|

## <a name="taxi"></a>Taxi 
 
A Taxi tartomány leképezések és entitások létrehozására és kezelésére taxi lefoglalások biztosít.

### <a name="examples"></a>Példák
```
Get me a cab at 3 pm
How much longer do I have to wait for my taxi?
Cancel my Uber
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Könyv | Egy taxi hívja. |Cab-fájl lekérése me egy<br/>egy taxi keresése<br/>Egy uber ablakban me x|
| Mégse | Szakítsa meg a Foglalás taxiban kapcsolatos műveletet.|Szakítsa meg a taxi<br/>Szakítsa meg a Uber|
| Követés | Nyomon követi a taxi útvonalat.|Milyen mértékű már rendelkeznek a taxi várni?<br/>Hol található a Uber?|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Cím| A cím foglalási taxiban. |Palo Alto<br/>300 112th Ave használata<br/>Seattle|
| DestinationAddress| A célhely vagy címe. |Palo Alto<br/>300 112th Ave használata<br/>Seattle|
| DestinationPlaceName | A cél, amely egy helyi üzleti, éttermi, nyilvános vonzás vagy intézmény neve. |Központi Park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | A cél, amely egy helyi üzleti, éttermi, nyilvános vonzás vagy intézmény típusa. |Étterem<br/>Opera<br/>film|
| PlaceName | Helyi üzleti, éttermi, nyilvános vonzás vagy intézmény neve. |Központi Park<br/>Safeway<br/>Walmart|
| PlaceType| A hely taxiban foglalható le a kérelem típusa.|Étterem<br/>Opera<br/>film|
| TransportationCompany | A szállítási szolgáltató neve.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | A szállítási típusa.|Busz<br/>Betanítás<br/>Vezetés|

## <a name="translate"></a>Fordítás 
A fordítás tartomány leképezések és a kapcsolódó szöveges cél nyelvre fordítása entitások biztosít.

### <a name="examples"></a>Példák
```
Translate to French
Translate hello to German
Translate this sentence to English
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Fordítás| A szöveg más nyelven történő fordításához.|Francia fordítás<br/>Német hello fordítása|


### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| TargetLanguage | A cél a fordítás nyelvét.|francia<br/>német<br/>koreai|
| Szöveg | A fordítandó szöveg.|Hello World<br/>jó reggelt<br/>jó estét|

## <a name="tv"></a>TV 
 
A TV tartomány leképezések és entitásokat biztosít televízió vezérlése.

### <a name="examples"></a>Példák
```
Switch channel to BBC
Show TV guide
Watch National Geographic
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| ChangeChannel| Módosítsa a egy TV-csatornát.|CNN csatorna módosítása<br/>BBC kapcsoló csatorna<br/>Ugrás a csatorna 4|
| ShowGuide| A TV útmutató megjelenítése.|TV útmutató megjelenítése<br/>Mi az az movie csatornán most?<br/>a program lista megjelenítése|
| WatchTV| Kérje meg, hogy figyelje a TV-csatorna.|Tekintse meg a Disney csatorna szeretnék<br/>Ugrás a TV adjon<br/>Tekintse meg a földrajzi National|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| CsatornaNeve | A TV-csatorna nevét.|CNN<br/>BBC<br/>Film csatorna|

## <a name="utilities"></a>Közművek  
A segédprogramok tartomány leképezések biztosít sok feladatokat, mint például a hónap, törlését, megerősítő, Súgó, megismétlését, navigációs indítása és leállítása a feladatokat.

### <a name="examples"></a>Példák
```
Go back to Twitter
Please help
Repeat last question please
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Mégse | Szakítsa meg a műveletet.|Az üzenet törlése<br/>Nem szeretnék többé az e-mailek küldése|
| Megerősítés | Erősítse meg a műveletet.|Igen bizony megerősítem<br/>Jó I vagyok megerősítése<br/>Rendben I vagyok megerősítése|
| FinishTask | Fejezze be a feladat a felhasználó elindult.|Elkészült<br/>Végeztem<br/>Elkészült|
| GoBack | Lépjen vissza egy lépést, vagy visszatérhet az előző lépésben.|Vissza a Twitteren<br/>Lépjen vissza a lépés<br/>Vissza|
| Súgó | Kérelem segítségét.|Kérjük, segítsen<br/>Súgó megnyitása<br/>help|
| Ismétlés | Ismételje meg a műveletet.|Ismételje meg az utolsó kérdés adjon<br/>Ismételje meg az utolsó szám|
| ShowNext | A következő elem megjelenítése egy sorozatban. |A következő egy megjelenítése<br/>Nyissa meg a következő oldalra|
| ShowPrevious | Az előző elem megjelenítése azokat.|Előző megjelenítése|
| StartOver | Indítsa újra az alkalmazást, vagy indítson egy újat.|Újrakezdés<br/>Új munkamenet<br/>restart|
| Leállítás | Állítsa le a műveletet.| Állítsa le, ezzel arról<br/>fogd be<br/>Adjon leállítása|

## <a name="weather"></a>Időjárás 
A időjárási tartományi leképezések és entitások biztosítja az első időjárás-jelentések és előrejelzések.

### <a name="examples"></a>Példák
```
weather in London in september
What?s the 10 day forecast?
What's the average temperature in India in september?
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| GetCondition | Időjárás kapcsolódó történelmi tények beolvasása. |szeptembertől Londonban időjárási<br/>Mi az az átlaghőmérséklet Indiában szeptemberben?|
| GetForecast | Az aktuális időjárása és a előrejelzésének a következő néhány nap múlva. |Hogyan van a időjárási ma?<br/>Mi az a 10 nap előrejelzési?<br/>Hogyan fogja a időjárási a hétvégi?|

### <a name="entities"></a>Entitások
| Entitás neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Hely| A időjárási kérelmek abszolút helye.|Seattle<br/>Párizs<br/>Palo Alto|

## <a name="web"></a>Web 
A webes tartomány megjelölésű biztosít egy weboldal-elérési útvonalon.

### <a name="examples"></a>Példák
```
Navigate to facebook.com
Go to www.twitter.com
Navigate to www.bing.com
```

### <a name="intents"></a>Leképezések
| Leképezési neve | Leírás | Példák |
| ---------------- |-----------------------|----|
| Navigálás | Nyisson meg egy adott webhelyet kérést. |Navigáljon a Facebook.com weboldalt<br/>Ugrás a www.twitter.com|

