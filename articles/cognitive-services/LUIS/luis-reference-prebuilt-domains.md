---
title: Előre elkészített tartományi hivatkozás – LUIS
titleSuffix: Azure Cognitive Services
description: Az előre összeállított tartományok, amelyek előre elkészített gyűjteménye szándékokat és entitásokat a Language Understanding Intelligent Services (LUIS) referenciája.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 7e2b5c5c5d2ca4c0d6ab820866341c5f30082c5f
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672777"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Előre elkészített tartományi referenciák a LUIS-alkalmazáshoz
Ez az útmutató információkkal szolgál a [előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md), amely gyűjteményei előre összeállított szándékok és entitások, amelyek a LUIS kínál.

[Egyéni tartományok](luis-how-to-start-new-app.md), ezzel szemben az elindításához nincs leképezések és a modellek. Egy egyéni modell bármilyen előre összeállított tartományban szándékok és entitások adhat hozzá.

## <a name="supported-domains-across-cultures"></a>Támogatott tartományok kultúrák között

Az alábbi táblázat összefoglalja a jelenleg támogatott tartományokat. Az angol nyelvű támogatás általában teljesebb, mint mások. 

| Entitás típusa       | EN-US      | ZH-CN   | DE    | JK     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| [Naptár](#calendar)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Kommunikáció](#communication)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [E-mail](#email)           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [HomeAutomation](#homeautomation)           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Megjegyzések](#notes)      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Helyek](#places)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [RestaurantReservation](#restaurantreservation)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [ToDo](#todo)     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Segédprogramok](#utilities)          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Weather](#weather)        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Web](#web)    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Az előre elkészített tartományok **nem támogatottak** a ben:

* Francia kanadai
* hindi
* Spanyol mexikói

## <a name="description-for-luis-prebuilt-domains"></a>A LUIS előre összeépített tartományok leírása
## <a name="calendar"></a>**Naptár**
A naptár a személyes találkozókról és a találkozókról szól, nem nyilvános eseményről (például a VB-ütemtervekről, a Seattle-i eseménynaptárról) vagy az általános naptárról (például a mai napig, hogy mi a kezdete, ha a Labor Day).
### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------------
 AcceptEventEntry | Fogadjon el egy (n) találkozót/értekezletet/eseményt a naptárban. | Fogadja el a találkozót. <br> Az esemény elfogadása <br> fogadja el a mai találkozót.
 Mégse | Szakítsa meg a folyamatban lévő műveletet a virtuális asszisztens által, például szakítsa meg az értekezlet létrehozásának folyamatát. <br> _**Figyelmeztetés**: Ez a szándék elsősorban a "Mégse" műveletet tartalmazza a naptári forgatókönyvekben. Ha a "Mégse" kifejezésre általános kifejezésre van szüksége, használja a "Mégse" szándékot a **Utilities** tartományban._ | Rendben van, csak szakítsa meg az eseményt. <br> Nem, csak megszakítom a találkozót.
 ChangeCalendarEntry | Módosítsa vagy ütemezze át a naptári bejegyzést. | 6 óra újraütemezett találkozó holnaptól 2 óráig <br> Az orvos kinevezésének átütemezett időpontja 5 óráig <br> Az ebédet a Jenny olsonval péntekig újraütemezhetjük. <br> Esemény időpontjának módosítása
 CheckAvailability | Rendelkezésre állási találkozó vagy a felhasználó vagy egy másik személy naptárat az értekezlet található. | Ha van elérhető teljesítéséhez Jim? <br> Azt mutatja, hogy a Carol elérhető holnap. <br> Chris szombat ingyenes van?
 Megerősítés | Erősítse meg, hogy az előző szándékon alapuló műveletet/műveletet hajt végre. <br> _**Figyelmeztetés**: Ez a szándék elsősorban a "Confirm" műveletet tartalmazza a naptári forgatókönyv esetében. Ha több általános kifejezésre van szüksége a "Confirm" (megerősítés) mezőben, használja a "Confirm" szándékot a **Utilities** tartományban._| Ez helyes, hozzon létre egy értekezletet <br> Igen, Köszönjük, hogy csatlakozik a találkozóhoz.
 ConnectToMeeting | Kapcsolódjon egy értekezlethez. | Kapcsolódjon a 11:00-es konferenciahíváshoz Andy használatával. <br> Fogadja el a költségvetési értekezlet hívását.
 ContactMeetingAttendees | Lépjen kapcsolatba az értekezlet résztvevőinek. | Mondja el, hogy az értekezlet a 3:00-es értekezlet végén fut. <br> Értesítse a munkatársakat a 8. találkozóról, hogy el kell kezdenie a 8:30-at.
 CreateCalendarEntry | Új egyszeri elem felvétele a naptárba. | Hozzon létre egy értekezletet a problémák megvitatásához. <br> Értekezlet létrehozása aabc@microsoft.com
 DeleteCalendarEntry | Kérelem törlése egy naptár-bejegyzést.| Carol saját találkozót megszakítása <br> 9 óra törlése értekezlet. <br> Az esemény törlése.
  FindCalendarEntry | Nyissa meg a naptári alkalmazást, vagy keresse meg a naptári bejegyzést. | A fogorvosnak tekintse át a találkozó keresése. <br> Saját naptár megjelenítése. <br> Mi a naptárom csütörtökön?
 FindCalendarWhen | Tekintse át az ütemterv időpontját. | Mikor kell találkozni az Amber és a Susan szolgáltatással? <br> Mikor van ütemezve a Brunch? 
 FindCalendarWhere | Győződjön meg arról, hogy az ütemezett hely hol zajlik. | Hol vannak a találkozók holnap? <br>Hol tartok találkozót a Stacy és a Michael holnap vacsorára?
  FindCalendarWho | Keresse meg azokat a résztvevő (ka) t, akik részt vesznek a cél-ütemtervben. | Azt szeretném, hogy a holnapi találkozón megerősített kísérők legyenek a 2. <br> Jim lesz a következő ápolók Értekezlete?
 FindCalendarDetail | Tekintse át és jelenítse meg az ütemterv részleteit. | Meg kell adnia nekem a kollégám Paul használatával ütemezett értekezlet részleteit.
 FindDuration | Vizsgálja meg az időtartamot. | Mennyi ideig kell vennem a élelmiszereket? <br> Meddig kell ebédelni?
 FindMeetingRoom | Keresse meg a rendelkezésre álló konferenciatermeket. | Mi felel meg a szobáknak? <br> Egy új értekezlet helye, keresse meg az egyiket.
 GoBack | Lépjen vissza az utolsó lépéshez vagy az elemmel.  <br> _**Figyelmeztetés**: További GoBack általános hosszúságú kimondott szöveg a **segédprogramok** tartománya nyújt további információt._ | Előző egy <br> Vissza az utolsó e-mailhez.
 Elutasítás | A felhasználó elutasítja a javasolt virtuális Segédet. <br> _**Figyelmeztetés**: Az általános hosszúságú kimondott szöveg a tartalomjegyzék **segédprogramok** tartományában tekintheti meg._ | Nem kell beállítani az eseményt. <br> Ebben az időben más dolgom van.
ShowNext | Keresse meg a következő eseményt. <br> _**Figyelmeztetés**: További ShowNext általános hosszúságú kimondott szöveg a **segédprogramok** tartománya nyújt további információt._ | Adja meg a következő eseményt. <br> Mi a következő a naptárban?
 ShowPrevious | Keresse meg az előző eseményt. <br> _**Figyelmeztetés**: További ShowPrevious általános hosszúságú kimondott szöveg a **segédprogramok** tartománya nyújt további információt._ | Mi az az ütemterv?
 TimeRemaining | A következő esemény előtt keresse meg a hátralévő időt. | Megjeleníti, hogy mennyi idő van az értekezletek előtt. <br> A következő értekezlet megkezdése előtti időtartam megjelenítése.
 
### <a name="entities"></a>**Szervezetek**
Entitás neve | Entitás típusa | Leírás | Példák | Tárhelyek
-------|-----------------------|-------------|---------|--------
ContactName | personName | A partner vagy az értekezlet résztvevője neve. | Találkozzon a **Betsy**-vel. <br>  Ismerkedjen **meg a** következővel: július 3., 19:00. | Betsy <br> Aubrey <br> Amy 
DestinationCalendar | egyszerű | A cél naptár neve. | ebédelés MOM kedd 12 **személyes** <br> Saját **Google** naptár használata alapértelmezett naptárként. <br> A jóga osztály frissítése a Mon Wed-ra 3 órakor a **személyes** naptár listázása. | Google <br> Személyes <br> vállalata számára <br> fő
Duration | datetime | Értekezlet, találkozó vagy hátralévő idő időtartama. | Vegyen fel a Work Calendar Meeting-be Gary segítségével, hogy beszéljen az ösztöndíj részleteiről holnap 11 órakor **20 percre**. <br> Felvétel a naptárba egy esemény a metroban **pénteken, este este 9** órakor | egy óra <br> 2 nap <br> 20 perc 
EndDate | datetime | Értekezlet vagy találkozó befejezési dátuma. | A koncert hozzáadása a Bass hallban Mary 3 – **Mary 5th** | Mary 5  
EndTime | datetime | Értekezlet vagy találkozó befejezési időpontja. | teheti 2 30- **három** | három 
Location | egyszerű | Naptári elemet, az értekezlet vagy a találkozó helye.  Cím, város és régiók helyek jó példák. | értekezlet betartása a **Fremont** -ben a tábla Burmában való üzembe helyezéséhez <br> Pro Bono-találkozó az **Edina** -ben | 209 Nashville Fitnessklub <br> 897 pancake ház <br> Garázs 
MeetingRoom | egyszerű | Értekezlet vagy találkozó helye. | Hozzáadás a Work Calendar-értekezlethez Jake-kor 2 órakor az **irodájában** ez a péntek | az irodája <br> konferenciaterem <br> 2\. hely
MoveEarlierTimeSpan | datetime | Az az időpont, amikor a felhasználó korábban szeretne áthelyezni egy értekezletet vagy találkozót. | Az ebéd időpontja **30 percen át elérhető**. | 30 perc <br> két óra 
MoveLaterTimeSpan |  datetime | Az az időpont, amikor a felhasználó később szeretne áthelyezni egy értekezletet vagy találkozót. | megbeszélések leküldése az orchidea-Box **4 órával**vissza. | 4 óra <br> 15 perc 
OrderReference | egyszerű | Sorszámnál vagy relatív azonosító lekéréséhez elem lista pozíciója. | Mi a jövő időpontja holnap? | tovább
OriginalEndDate | datetime | Értekezlet vagy találkozó eredeti befejezési dátuma. | A nyaralásom megváltozása **péntektől** hétfőig | Péntek 
OriginalEndTime | datetime | Értekezlet vagy találkozó eredeti befejezési időpontja. | Végezze el az egyik végét **3** – 4. | 3
OriginalStartDate | datetime | Értekezlet vagy találkozó eredeti kezdési dátuma. | **Holnapi**találkozó módosítása 10 órától Szerda: 9 órakor  | holnap 
OriginalStartTime | datetime | Értekezlet vagy találkozó eredeti kezdési időpontja. | Holnapi találkozó módosítása **10 órától** Szerda: 9 órakor | 10:00
PositionReference | ordinal | Egy lista abszolút pozíciója, amely a lekérdezni kívánt elem azonosítására szolgál. | A **második** | másodperc <br> Nem. 3 <br> 5\. szám
RelationshipName | list | Egy névjegy kapcsolatának neve. | ebéd hozzáadása 1:00 órakor **feleségemmel** | feleségemmel <br> férjem <br> húga 
SlotAttribute | egyszerű | A felhasználó szeretné lekérdezni vagy szerkeszteni az attribútumot. | esemény **helyének** módosítása <br> módosítsa az **időpontot** hét óráig | location <br> time 
StartDate | datetime | Értekezlet vagy találkozó kezdési dátuma. | Találkozó létrehozása **szerdánként** 4 órakor | Szerda 
StartTime | datetime | Értekezlet vagy találkozó kezdési időpontja. | találkozó létrehozása szerdánként **4 órakor** | 4 óra
Subject | egyszerű, minta. Bármely | Tárgy, például értekezlet vagy találkozó címe. | Mennyi idő van a **felek felkészülési** ülésén? | Fogorvos <br> A Julia ebéd 
Message | egyszerű, minta. Bármely | A résztvevőknek küldendő üzenet. | A vacsora résztvevői megtartották, hogy **későn**tartanak. | Későn fogok

## <a name="communication"></a>**Kommunikáció**
Hívások kezdeményezésére, szövegek/IMs küldésére, névjegyek keresésére/hozzáadására, valamint más kommunikációval kapcsolatos kérelmek (általában kimenő). A _Kapcsolattartó neve csak_ a hosszúságú kimondott szöveg nem tartozik kommunikációs tartományhoz.

### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------
AddContact | Új ügyfél hozzá a felhasználó ügyfelek listáját. | Új névjegy hozzáadása.  <br>   Mentse ezt a számot, és tegye a nevet Jane-ként.
AddFlag | Jelölő hozzáadása egy e-mailhez | E-mail-cím megjelölése <br> Adjon hozzá egy jelölőt az e-mailhez.
AddMore | Adja hozzá több, egy e-mailben vagy szöveges, többlépcsős e-mail vagy szöveges juthat szerephez. | További szöveg hozzáadása.  <br>   Továbbiak hozzáadása az e-mail törzséhez.
Válasz | A Bejövő telefonhívás fogadása. | Válaszolja meg a hívást.  <br>   Válassza ki.
AssignContactNickname | Rendelje hozzá egy névjegyet egy becenév. | Izsák módosítása apa-ra.  <br>   Jim beceneve szerkesztése. <br>   Adja hozzá a becenevet a Patti Owens-hoz.
Visszahívási | Telefonhívás visszaküldése. | Hívjon vissza.
CallVoiceMail | A felhasználó hangposta csatlakozni. | Kapcsolódás a hangposta mezőhöz. <br>Hangposta. <br>   A hangposta hívása.
Mégse | Művelet megszakítása. | Mégse. <br>   Fejezze be.
CheckIMStatus | Egy partner állapotának megkeresése az IM-ben. | Jim az online állapot értéke azonnal? 
CheckMessages | Keresse meg az új üzeneteket vagy e-maileket. | Saját beérkezett fájlok keresése <br>  Van új e-mailem?
Megerősítés | Erősítse meg a műveletet. | Igen, küldje el. <br> Jobb, megerősítem, hogy szeretném elküldeni ezt az e-mailt.
EndCall | Telefonhívás befejezése. | Szakítsa meg a hívást. <br> Befejezés.
FindContact | Kapcsolattartási adatok keresése név alapján. | A MUM számának megkeresése <br>   Carol számának megjelenítése
FinishTask | Aktuális feladat befejezése. | Kész vagyok <br> Ez az összes.
TurnForwardingOff | Kapcsolja ki az átirányítás. | A hívások továbbításának leállítása. <br> Kapcsolja ki a hívásátirányítás funkciót.
TurnForwardingOn | A hívásátirányítás bekapcsolása. | A hívások 3333 továbbítása <br>  Váltás a hívásátirányítás 3333-ra.
GetForwardingsStatus | Átirányítás aktuális állapotának lekéréséhez. | A hívás továbbítás engedélyezve van? <br>   Mondja el, ha a hívás állapota be van kapcsolva vagy ki van kapcsolva.
GetNotifications | Szerezze be az értesítéseket. | saját értesítések megnyitása <br>   megtekintheti a telefon Facebook-értesítéseit
GoBack | Lépjen vissza az előző lépésben. | Lépjen vissza a twitteren <br>   Lépjen vissza az adott lépés <br>   Vissza
Kihagyás | Bejövő hívás figyelmen kívül. | Nem válaszol <br>   Hagyja figyelmen kívül a hívás
IgnoreWithMessage | Bejövő hívás figyelmen kívül, és inkább szöveget tartalmazó válasz. | Ez a meghívás nem válaszol, de küldjön inkább üzenetet. <br>   Figyelmen kívül hagyhatja, és vissza egy szöveges üzenet elküldéséhez.
Telefonos kapcsolat létrehozása | Telefonhívás. | Jim hívása <br>   Hívja meg a 311-es telefonszámot.
FindSpeedDial | Található a speedial telefonszám van beállítva, és ez fordítva is igaz. | Mi az a telefonos számot 5? <br>   Tárcsázza a set sebesség van? <br>   Mi a telefonszáma 941-5555-333?
Továbbítás | E-mail továbbítása | Továbbítsa ezt az e-mailt Gregnek.
ReadAloud | Olvassa el az üzenet vagy e-mailt a felhasználónak. | Olvassa el a szöveget. <br>   Mi volt ő tegyük fel, hogy az üzenet?
PressKey | Egy gomb vagy a szám nyomja le a billentyűzeten. | Tárcsázás csillagra. <br>   Nyomja meg az 1-2 3.
QueryLastText | Az utolsó szöveg vagy üzenet lekérdezése. | Ki SMS-ben? <br>   Ki nemrég üzenetet küldött?
Újratárcsázás | Újratárcsázás, vagy egy szám hívja meg újra. | Újratárcsázás. <br>   A legutóbbi hívásának Újratárcsázás.
Elutasítás | Bejövő hívás elutasítása. | Hívás elutasítása <br>   Most már nem válaszol <br>   Jelenleg nem érhető el, és meghívja vissza később.
Válasz | Válaszüzenet küldése. | Válasz a Lore houndre <br>   Válasz beírásával én vagyok az utamba
SearchMessages | Az üzenetekben bizonyos feltételek alapján kereshet. | A Jane által küldött e-mailekre kereshet?
Sendemail művelet | E-mail küldése. Ez a leképezés e-mailt, de nem a szöveges üzenetek vonatkozik. | E-mailek a Mike Watersba: Mike, a múlt héten tartott vacsora csodálatos volt. <br>   Küldjön egy e-mailt a Bob-nek.
SendMessage | Szöveges üzenet vagy azonnali üzenet küldése. | Chris és Carol szöveg küldése <br>   Facebook-üzenet, Greg <br>   
SetSpeedDial | Sebesség tárcsázás parancsikon be a telefonszámát. | Carol egy gyors tárcsázás be. <br>   Állítsa be a mom sebesség bármekkora munkát képes elvégezni.
ShowCurrentNotification | Aktuális értesítések megjelenítése. | Vannak új értesítések? <br> Értesítés megjelenítése.
ShowNext | Lásd a következő elem, például a szöveges üzenetben vagy e-mailek listáját. | Megjelenítés a következő parancsot. <br>   A következő oldalra lépéshez.
ShowPrevious | Lásd az előző elemmel, például a szöveges üzenetben vagy e-mailek listáját. | Az előző feladat megjelenítése. <br>   Korábbi. <br>   Nyissa meg az előző.
TurnSpeakerOff | Kapcsolja ki a kihangosítás. | Ugrás a beszélő ki. <br>   Kapcsolja ki a kihangosító.
TurnSpeakerOn | Kapcsolja be a kihangosítás. | Kihangosító mód. <br>   Kihangosító helyezze.

### <a name="entities"></a>**Szervezetek**
Entitás neve | Entitás típusa | Leírás | Példák | Tárhelyek
------|-------|----------|--------------|---------------
Attachment | egyszerű | Az a melléklet, amelyet a felhasználó SMS-ben vagy e-mailben szeretne elküldeni. | **Fájl** küldése a OneNote-ből. <br> A kitakarítási **doc** küldése a Katie-nek. | file <br> doc
AudioDeviceType | egyszerű | Az audioeszköz típusa (hangszóró, fejhallgató, mikrofon stb.). | Válasz a **kihangosító**használatával. <br> Tárcsázzon újra a **hangszórón**. | Speaker <br> kihangosító <br> Bluetooth
Category | egyszerű | Az üzenetek vagy e-mailek kategóriájának egyértelmű definícióval kell rendelkeznie az e-mail-rendszeren, például "olvasatlan", "Flag". A Description Description Clear Definition (például "New" és "legutóbbi") nem kategóriák. | Az összes e-mail megjelölése **olvasottként**  <br> Új, **magas prioritású** e-mailek Paul-hoz | fontos <br> magas prioritás <br> olvasás
ContactAttribute | egyszerű | A felhasználó által megkérdezett kapcsolathoz tartozó attribútum.| Minden, a jövő hónapban megjelenő **születésnapom** ? | születésnapok <br> cím <br> Telefonszám
ContactName | personName  | Egy ügyfél vagy üzenet címzettje neve. | Küldje el az e-mailt a **Stevens** -nek | Stevens
Date/Time | datetime | A fogadott e-mailek datetime-értéke. | A **mai**e-mail olvasása <br> A **mai napon**e-mailt küldött? <br> ki, **7 órakor**telefonált? | ma <br> holnap
DestinationPhone | egyszerű | A célként megadott felhasználó szeretne hívni vagy elküldeni egy szöveget a következőnek:. | Hívás kezdeményezése a **házba** <br> szöveges üzenet küldése a **kezdőlapnak** | házon <br> kezdőlap
EmailAddress | email | A felhasználó e-mail-címe szeretné elküldeni vagy lekérdezni. | e-mail küldése a következőnekMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | egyszerű, minta. Bármely | A szöveg tárgy szolgáló e-mailt. | E-mail-cím írása a tárgy **Hé** -mel  | RE: érdekes történetet
Kulcs | egyszerű | A legfontosabb felhasználó szeretné megnyomni a gombot. | Nyomja le a **szóköz** billentyűt. <br> a **9-es** gomb megnyomása | font <br> csillagos <br> 8
Vonal | egyszerű | A sor felhasználója szeretne e-mailt vagy szöveget küldeni a alkalmazásból. | Az utolsó **hotmail** -e-mail-cím olvasása. <br> A Peter Call by **Mobile**. <br> Az apa meghívása a saját **munkaterület** használatával.| hotmail <br> Skype <br> Brit cella
SenderName | personName | A feladó neve. | Az e-mail olvasása **David** -ból <br> A Chana által küldött e-mailek | David <br> Chanda
FromRelationshipName | egyszerű | A küldő kapcsolatának neve. | Üzenet olvasása **apa**-ból. <br> El tudja olvasni az **anya**összes szöveges üzenetét? | Apának <br> MOM 
Message | egyszerű, minta. Bármely |  Küldendő e-mail vagy szöveges üzenet.  | E-mail küldése – "**elfoglalt vagyok**". | Foglalt
OrderReference | egyszerű | Sorszámnál vagy relatív azonosító lekéréséhez elem lista pozíciója. | Mi volt az **utolsó** üzenet, amit küldtem? <br> Olvassa el a **legújabb** Nokia e-mailt. <br> **Új** szöveges üzenetek olvasása. | utolsó <br> legújabb <br> Legutóbbi <br> legújabb
PositionReference | egyszerű, sorszám | Sorszámnál vagy relatív azonosító lekéréséhez elem lista pozíciója.| Mi volt az **első** üzenet, amit küldtem? <br> A **harmadik** egy.| Első <br> külső
Telefonszám | Telefonszám | A telefonszám-felhasználó szeretne meghívni vagy elküldeni egy szöveget. | szöveg küldése **4 1 5 6 8 4 5 2 8 4** -re | 3525214446
RelationshipName | egyszerű | Egy partner vagy üzenet címzettjének kapcsolati neve. | E-mailek a **feleségemnek** | feleségemmel
SearchTexts | egyszerű, minta. bármilyen | Az e-mailek vagy üzenetek szűréséhez használt szövegek | A "**Surface Pro**" kifejezést tartalmazó e-mailek keresése | Surface Pro
Gyorstárcsázás | egyszerű | A gyorstárcsázás. | Call **3 4 5**. <br> Állítsa be a gyorstárcsázás **egyikét**. | 345 <br> 5

## <a name="email"></a>**E-mail**
Az e-mail a *kommunikációs* tartomány altartománya. Elsősorban az e-mailek küldésére és fogadására vonatkozó kérelmeket tartalmaz.
### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------
AddMore | Adja hozzá több, egy e-mailben vagy szöveges, többlépcsős e-mail vagy szöveges juthat szerephez. | Továbbiak hozzáadása az e-mail törzséhez.
Mégse | Művelet megszakítása. <br> ***Figyelmeztetés**: A General hosszúságú kimondott szöveg további megszakításához tekintse meg a **segédprogramok** tartományát. * | Mégse. Ne küldje el. <br> Fejezze be.
CheckMessages | Az új üzenetek/e-mailek feltételes lekérdezés nélküli keresése. Ha van ilyen feltétel, a hosszúságú kimondott szöveg a *SearchMessage* szándékhoz tartozik. | A beérkezett fájlok mappájának bejelölése. <br> Van új levelezésem? 
Megerősítés | Erősítse meg a feldolgozási e-mailekkel kapcsolatos folyamatban lévő műveletet. <br> ***Figyelmeztetés**: Az általános hosszúságú kimondott szöveg a további tudnivalókat lásd: a **segédprogramok** tartománya. * | Igen, küldje el. <br> Megerősítem, hogy szeretnék-e e-mailt.
Törlés | Törölheti az e-maileket vagy az egyes feltételek alapján szűrt leveleket. | Az e-mail a Lomtárba való helyezése <br> Üres a saját beérkezett fájlok mappája. <br> A Mary e-mail-címének eltávolítása.
ReadAloud | Olvassa el az üzenet vagy e-mailt a felhasználónak. <br> ***Figyelmeztetés**: További Dobrai Lajos általános hosszúságú kimondott szöveg tekintse meg a **segédprogramok** tartományát. *  | Olvassa el a Mary által küldött e-mailt.
Válasz | Válaszüzenet küldése a jelenlegi e-mail-címre. | Hozzon létre egy választ az e-mail-címre. <br> Válasz beírásával: "Köszönöm szépen, Üdvözlettel, John".
SearchMessages | Az üzenetekben bizonyos feltételek alapján kereshet, beleértve a küldő nevét, az időpontot és a tárgyat. | Üzenetek megjelenítése a Nisheen. <br> Kereshet az "ABC" címet viselő e-mailekben?
Sendemail művelet | E-mail küldése. | E-mail küldése Mikenak: Mike, a múlt héten tartott vacsora csodálatos volt. <br> Küldjön egy e-mailt a Bob-nek.
ShowNext | Tekintse meg a következő elem (ek) et a szöveges üzenetek vagy e-mailek listájában. <br> ***Figyelmeztetés**: További ShowNext általános hosszúságú kimondott szöveg tekintse meg a **segédprogramok** tartományát. * | Megjelenítés a következő parancsot. <br> A következő oldalra lépéshez.
ShowPrevious | Az előző elemek megjelenítése a szöveges üzenetek vagy e-mailek listájában. <br> ***Figyelmeztetés**: További ShowPrevious általános hosszúságú kimondott szöveg tekintse meg a **segédprogramok** tartományát. * | Az előző feladat megjelenítése. <br> Korábbi. <br> Nyissa meg az előző.
Továbbítás | Továbbítson egy e-mailt. | Továbbítsa ezt az e-mailt Gregnek.
AddFlag | Jelölő hozzáadása egy e-mailhez. | E-mail-cím megjelölése <br> Adjon hozzá egy jelölőt az e-mailhez.
QueryLastText | Az utolsó e-mail lekérdezése. | Ki küldött e-mailt? <br> Ki nemrég e-mailben nekem?


### <a name="entities"></a>**Szervezetek**
Entitás neve | Entitás típusa | Leírás | Példák | Tárhelyek
------|-------|----------|--------------|---------------
Attachment | egyszerű | Az a melléklet, amelyet a felhasználó SMS-ben vagy e-mailben szeretne elküldeni. | **Fájl** küldése a OneNote-ből. <br> A kitakarítási **doc** küldése a Katie-nek. | file <br> doc
ContactName | personName  | Egy ügyfél vagy üzenet címzettje neve. | Küldje el az e-mailt a **Stevens** -nek | Stevens
Date | datetime | Az e-mailek fogadásának dátuma. | A **mai**e-mail olvasása <br> A **mai napon**e-mailt küldött? | ma
EmailAddress | email | A felhasználó e-mail-címe szeretné elküldeni vagy lekérdezni. | e-mail küldése a következőnekMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | egyszerű, minta. Bármely | A szöveg tárgy szolgáló e-mailt. | E-mail-cím írása a tárgy **Hé** -mel  | RE: érdekes történetet
SenderName | personName | A feladó neve. | Az e-mail olvasása **David** -ból <br> A Chana által küldött e-mailek | David <br> Chanda
FromRelationshipName | egyszerű | A küldő kapcsolatának neve. | Üzenet olvasása **apa**-ból. <br> El tudja olvasni az **anya**összes szöveges üzenetét? | Apának <br> MOM 
Message | egyszerű, minta. Bármely |  Küldendő e-mail vagy szöveges üzenet.  | E-mail küldése – "**elfoglalt vagyok**". | Foglalt
Category | egyszerű | Az üzenetek vagy e-mailek kategóriájának egyértelmű definícióval kell rendelkeznie az e-mail-rendszeren, például "olvasatlan", "Flag". A Description Description Clear Definition (például "New" és "legutóbbi") nem kategóriák. | Az összes e-mail megjelölése **olvasottként**  <br> Új, **magas prioritású** e-mailek Paul-hoz | fontos <br> magas prioritás <br> olvasás
OrderReference | egyszerű | Sorszámnál vagy relatív azonosító lekéréséhez elem lista pozíciója. | Mi volt az **utolsó** üzenet, amit küldtem? <br> Olvassa el a **legújabb** Nokia e-mailt. <br> **Új** szöveges üzenetek olvasása. | utolsó <br> legújabb <br> Legutóbbi <br> legújabb
PositionReference | egyszerű, sorszám | Sorszámnál vagy relatív azonosító lekéréséhez elem lista pozíciója.| Mi volt az **első** üzenet, amit küldtem? <br> A **harmadik** egy.| Első <br> külső
RelationshipName | egyszerű | Egy partner vagy üzenet címzettjének kapcsolati neve. | E-mailek a **feleségemnek** | feleségemmel
Time | datetime | Time | e-mail küldése **ma este**. | Tonight
SearchTexts | egyszerű, minta. bármilyen | Az e-mailek vagy üzenetek szűréséhez használt szövegek | A "**Surface Pro**" kifejezést tartalmazó e-mailek keresése | Surface Pro 
Vonal | egyszerű | A sor felhasználója szeretne e-mailt küldeni a alkalmazásból. | Az utolsó **hotmail** -e-mail-cím olvasása. <br> E-mail küldése a **munkahelyi fiókból**.| hotmail <br> munkahelyi fiók 

## <a name="homeautomation"></a>**HomeAutomation**
A HomeAutomation-tartomány a Smart Home-eszközök vezérléséhez kapcsolódó leképezéseket és entitásokat biztosít. Elsősorban a fények és a légkondicionáló szolgáltatáshoz kapcsolódó vezérlési parancsot támogatjuk. Vannak azonban általánosítási képességei más elektromos készülékeken.
### <a name="supported-devices-and-properties"></a>**Támogatott eszközök és tulajdonságok**
Eszköz | properties
-------|---------
Hőmérséklet-érzékelő | Hőmérséklet
Fény, lámpa | Kikapcsolt, fényerő, szín
TV, rádió | Bekapcsolt, kötet
Légkondicionáló (A/C), termosztát | Kikapcsolva, hőmérséklet, teljesítmény
Ventilátor | Kikapcsolva, teljesítmény
Kivezetések, DVD-lejátszó, Ice Maker stb. | Bekapcsolva

### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------
 Kikapcsolás | A felhasználó ki szeretné kapcsolni az eszközt vagy a beállításokat.  | A fények kikapcsolása. <br> Kapcsolja ki a dolgot. <br> Valami kikapcsolva.
 Bekapcsolása | Kapcsolja be az eszközt, vagy kapcsolja be, és állítsa be az eszközt egy adott beállításra vagy üzemmódra. | Kapcsolja be a fényt 50%-ra. <br> A kávéfőző bekapcsolása <br> Be lehet kapcsolni a kávéfőzőt?
 SetDevice | A felhasználó az eszközt egy adott beállításhoz vagy típushoz szeretné beállítani.  | Állítsa a légkondicionálót 26 fokos értékre. <br> A fények kék színének bekapcsolása <br> Hívja meg ezt a fényt a lámpa.
 QueryState | A felhasználó az eszköz vagy a beállítás állapotát kéri.  | Mit kell beállítani a termosztát? <br> Be van kapcsolva az a/C? <br> Mi a hálószoba hőmérséklete?
 TurnUp | Az eszközök beállításainak vagy fényességének kikapcsolása. | A fények 75 százalékkal felvilágosítva. <br> A fényerő itt 10 százalékkal növelhető.  <br> Legyen melegebb a nappaliban.
 TurnDown | Kikapcsolhatja az eszközt, de nem lehet kikapcsolva az eszközön, a hőmérsékletet vagy a fények fényességét. | Állítsa le a könyvtárat 44%-kal. <br> A fények homályosak. <br> A helyiség hűvösebbvé tétele.
### <a name="entities"></a>**Szervezetek**
Entitás neve | Entitás típusa | Leírás | Példák
-------|----------|--------------|---------------
Eszköznév | List | A felhasználó által definiált szöveg az eszközökhöz. | Kék<br> Karácsony <br> Recepció
DeviceType | List | Támogatott eszközök. | Fények <br> Légkondicionáló <br> lámpa
Location | egyszerű | Az eszköz helye vagy helye. | Konyhai<br> Földszinten <br> Hálószoba
NumericalChange | egyszerű | A beállítás növelésének vagy csökkenésének mértéke. <br> <br> _A tárolóhely csak a turn_up és a turn_down leképezésekkel jelenik meg._ | 3<br> 50%<br>
OrderReference | ordinal | Ennek a tárolóhelynek a célja az elemek kiválasztásának rögzítése. Jelzi az elem pozícióját egy listán. | Első<br> második
Számlálónak | List | A mennyiségi azonosító azt jelzi, hogy egy adott entitás hány példánya van hivatkozva. Például: "all", "every" stb. | Összes<br> Minden<br> Mindenben
Beállítás | Egyszerű | Annak beállítása, hogy a felhasználó a saját eszközét szeretné beállítani, amely magában foglalja a következőket: jelenet, szint, intenzitás, szín, üzemmód, hőmérséklet, az eszköz állapota. | Kék<br> 72 <br> Intenzitástérkép 
Példányokat | List | Az az eszköz, amely a felhasználót érdekli. | Hőmérséklet<br> 
Idő/dátum |  datetime | Eszköz működtetésének ideje és időtartama| 5 perc <br> 3 órakor
Unit (Egység) | List | Ha olyan szavakat szeretne címkézni, mint például a "fok", a "százalék", a "Fahrenheit", a "Celsius", az egyes egységekhez tartozó kifejezéseket külön kell címkézni | Fok<br> Percent


## <a name="notes"></a>**Megjegyzések**
Megjegyzés: a tartomány megkönnyíti a jegyzetek létrehozását és a felhasználókra vonatkozó elemek leírását.
### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------
AddToNote | Információ hozzáadása egy megnyitott megjegyzéshez. | Vegyen fel a saját tervezési megjegyzésbe, hogy felvegye a kapcsolatot a saját főnökével a projekttel kapcsolatban.
Törlés | Egy már meglévő megjegyzés összes elemének törlése. | Az összes elem eltávolítása a saját nyaralási megjegyzésből. <br>Összes törlése az olvasói megjegyzésből
Megerősítés | Erősítse meg a vonatkozó megjegyzés művelet. <br> ***Figyelmeztetés**: Ez a szándék elsősorban a "Confirm" műveletet tartalmazza a Megjegyzés forgatókönyvéhez. Ha több általános kifejezésre van szüksége a "Confirm" (megerősítés) mezőben, használja a "Confirm" szándékot a **segédprogramok** tartományában. * | Ez a Megjegyzés rendben van. <br>Megerősítem a listán szereplő összes elem megtartását.
Hozzon létre | Hozzon létre egy új megjegyzés. | Hozzon létre egy megjegyzést. <br>Figyelje meg, hogy Jason a május első hetében van a városban. 
Törlés | Egy teljes megjegyzés törlése. | Saját nyaralási Megjegyzés törlése <br>A My élelmiszerbolt Megjegyzés törlése.
ReadAloud | Hangos megjegyzés olvasása. | Olvassa el az első megjegyzést. <br>Olvassa el a részleteket.
Bezárás | Az aktuális Megjegyzés lezárása. | A Megjegyzés lezárása. <br>Az aktuális Megjegyzés lezárása.
Open program | Egy már létező Megjegyzés megnyitása. | Nyissa meg a saját Meghívási megjegyzését. <br>Nyissa meg a "tervezés" megjegyzést.
RemoveSentence | Egy Megjegyzés mondatának eltávolítása. | Távolítsa el az utolsó mondatot. <br>Az élelmiszerbolt megjegyzésből származó zsetonok törlése. <br>A tollak eltávolítása az iskolai vásárlási megjegyzésből
ChangeTitle | Módosítsa a jegyzet címét. | A Megjegyzés neve "tervezés".

### <a name="entities"></a>**Szervezetek**
Entitás neve | Entitás típusa | Leírás | Példák 
------- | ------- | ------- | -------
Text | egyszerű, minta. Bármely | Egy megjegyzés vagy emlékeztető szöveg. | a Stretch walking előtt <br> hosszú futtatású holnap
Beosztás | egyszerű, minta. Bármely | Megjegyzés címe. | élelmiszerszükségletét <br> Személyek meghívásához <br> TEENDŐ
CreationDate | datetimeV2 | Ez a tárolóhely akkor használható, ha a felhasználó egy adott dátum/idő ablakon belül létrehozott megjegyzéseket kér. | 
Számlálónak | List | Amikor egy felhasználó az "all", az "all" vagy az "any" vagy a "bármelyik" elemre vagy egy Megjegyzés összes szövegére vonatkozó műveletet kér. | összes <br> Bármely <br> minden
OrderReference | ordinal | A felhasználó az "első", az "utolsó", a "Next" és az "etc." elemek használatával szeretne műveleteket végezni. | első <br> utolsó


## <a name="places"></a>**Helyek**
A helyek közé tartoznak a vállalatok, intézmények, éttermek, nyilvános területek és címek. A tartomány támogatja a hely megkeresését és az információk lekérdezését olyan nyilvános helyen, mint például a hely, az üzemóra és a távolság. 
### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------
MakeCall eljárás | Telefonhívás helyre. | applebees on 1000/200 Rojas St és Call.
FindPlace | Keresse meg a hely (üzleti, intézmény, vendéglátás, nyilvános címtér, cím). De nem: <li> Csak a vállalat neve: Starbucks <li> Csak a hely neve: Seattle/Norvégia <li> Csak konyha, élelmiszer vagy termék: Pizza/guacamole/olasz | Hol található a legközelebbi tár? <br> Starbucks a Seattle-ben. <br> Hol található a legközelebbi tár? <br> 
GetAddress | Kérje meg a cím, egy helyen. | Megmutatom a Guu címeit az "a" platformon. <br> Mi az a legközelebbi Kávézik címét?
GetDistance | A jelenlegi helytől adott helyre vonatkozó távolságot kér. | Milyen távol az ünnepi adóazonosító?<br> Meddig van a Bellevue Square? <br> Mi a távolság a Tahoe-tól?
GetPhoneNumber | Kérdezze meg a telefonszámát helyen. | Mi az a legközelebbi Kávézik telefonszáma?<br> Adja meg a Home Depot számát. <br> A Disneyland telefonszáma.
GetPriceRange | Kérje az egy főre jutó felhasználás tartományát egy helyen. | A J. sushi átlagos díja Seattle-ben. <br> Az a Cineplex féláron szerdánként? <br> Mennyibe kerül, Sizzler teljes lobster vacsora költsége?
GetStarRating | Kérje meg a minősítést, egy helyen. | Hogyan történik a Zucca besorolása?<br> Hány csillagot rendelkezik a francia ruhák?<br> A aquarium Monterrey a jó van?
GetHours | Kérdezze meg a hely üzemel. | Mely nem Safeway bezárja?<br> Mik azok a kezdőlap Depot az órák?<br> Még mindig nyitva van a Kávézik?
GetReviews | Kérje meg a hely felülvizsgálatai. | Megmutatom a sajttorta Factory értékeléseit. <br> Cineplex-felülvizsgálatok olvasása. <br> Van-e a Humperdinks legújabb felülvizsgálata?
GetMenu | Kérjen egy étterem menü elemei. | Nem Zucca kiszolgálása bármit kis? <br> Mi a tipp? <br> A Applebee menüjének megjelenítése.
RatePlace | Egy hely kiértékelése. | 4 csillagos minősítés a Kimberly-beli pizza-hoz. <br> Adja meg a 4 csillagos Bonefish a TripAdvisor. <br> Hozzon létre egy 4 csillagos, La Hacienda-felülvizsgálatot.
AddFavoritePlace | A felhasználó egy helyet szeretne hozzáadni a kedvencekhez vagy az MVP-listához. | Mentse ezt a helyet a kedvenceim közé. <br> Vegye fel a legjobb vásárlást a kedvenceim közé.

### <a name="entities"></a>**Szervezetek**
LUIS entitások | Entitás típusa | Leírás | Példák | Példák a teljes kifejezésekre
--------------|-------------|-------------|----------|-------------------
AbsoluteLocation | egyszerű | A hely vagy a hely címe. | Rendszert futtató Palo Alto <br> 300 112th Ave képlet használata <br> Seattle | **1020 Middlefield Rd.** a **Palo Alto** -ben <br> Bird Seed Stores in **Seattle** <br> Innen elérheti a távolságot innen: **300 112Th Ave se**.
Eszközök | egyszerű | A nyilvános hely objektív jellemzői és előnyei. | i partszakasz <br> ingyenes várakozást | Kirkland **Waterfront** Seafood-éttermek. <br> Van a közeljövőben **ingyenes parkoló** ?
Cuisine | egyszerű | Élelmiszer, cuisine vagy cuisine állampolgárság típusát. | kínai <br> olasz <br> Sushi <br> Tészta <br> | Segítség a **kínai** étterem megtalálásához. <br> Mik a **sushi** étterem nyitvatartási ideje? <br> Hol található a legközelebbi **steak** -ház?
Date | datetime | a célhely dátumának dátuma vagy időtartama. | holnap <br> ma <br> 6 órakor | Milyen időpontban zárul az akvárium **holnap**? <br> a legközelebbi Bike Shop, amely **6 óra** elteltével nyitva van
Távolság | dimenzió | A felhasználó currenct-pozíciójában lévő nyilvános hely távolsága. | 15 mérföld <br> 10 mérföld | egy ruházati áruház **15 mérfölden** belül <br> Egy gyermek-étterem, amely mindössze **10 mérföld** távolságra van
MealType | List | Például a reggeli vagy ebédszünetet beiktatni dara típusa. | A reggeli <br> Vacsora | Keresési **reggeli** Greenwood Seattle <br> Keressen egy helyet, ahol **ebédelem**.
Közeli | List | Írjon be egy közeli helyet abszolút hely vagy címe nélkül. | legközelebbi <br> ezen a téren <br> körülöttem | A **legközelebbi** indiai étterem megkeresése. <br> Hol található a **helyi** Wetherspoon? <br> Minden **jó étterem?**
OpenStatus | List | Azt jelzi, hogy egy helyen nyílt vagy lezárt. | nyitás <br> Lezárt | Milyen időt jelent a joghurt **földje ma** ? <br> Milyen **nyitvatartási** idő van a Costco?
PlaceName | egyszerű | A cél, amely egy üzleti, vendéglátás, nyilvános vonzás vagy intézmény neve. A hely neve tartalmazhatja a placetype, ha az általános használatban van. | Közép-India Park <br> Safeway <br> Walmart| Milyen időt nyit a **Safeway** gyógyszertár? <br> Nyitva van a **Walmart** ?
PlaceType | egyszerű | A cél, amely egy helyi üzleti, vendéglátás, nyilvános vonzás vagy intézmény típusa. | Éttermek <br> Opera <br> mozi | Cambridge-i **mozik** <br> Van egy közeli **étterem** ?
PriceRange | egyszerű | Az adott helyhez tartozó termékek vagy szolgáltatások árának tartománya. | Bármennyire <br> költséghatékony <br> költséges | **Megfizethető** berendezések javításának megkeresése <br> Milyen **olcsó** pizza-helyet nyit most?
Product | egyszerű | A termék által egy helyen érhető el. | ruhák <br> televíziók | Hol van a legjobb hely az **élelmiszer**beszerzéséhez? <br> Keresse meg a **televíziókat**kereső Kelet-Kilbride.
Minősítés | egyszerű | A helyszín minősítése. | 5 csillag <br> Top <br> jó | Van-e **jó** hely, hogy kilépjem, és holnap enni <br> **legjobb** Amszterdami éttermek <br> Sorolja fel az **első** három pizza-áruházat.


## <a name="restaurantreservation"></a>**RestaurantReservation**
Az étterem foglalási tartománya támogatja az éttermek foglalásának kezelésére szolgáló leképezéseket.
### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------
Foglalás | Egy étterem foglalás kérelmet. | Foglaljon le a Zucca-on két estére. <br> Táblázat lefoglalása holnapra. <br> 3\. táblázat a Palo Alto 7-es verziójában. <br> Készítse el a 3. és a Vörös Homár foglalását.
DeleteReservation | Egy étterem foglalásának megszakítása vagy törlése. | A foglalás megszakítása a Zucca holnap este. <br> Felejtsd el a Red homár-foglalást 7:00 órakor következő pénteken. <br> Nincs szükségem a Zucca-re vonatkozó foglalásra, majd a Mégse gombra.
ChangeReservation | Megváltoztathatja egy meglévő éttermi foglalás időpontját, helyét vagy számát. | A foglalás módosítása 21:00 óráig <br> Módosítsa a Zucca és a Vörös Homár közötti foglalást. <br> a foglaláshoz a Zucca címen 6 személy nem használható.
Megerősítés | Erősítse meg a foglalással kapcsolatos műveletet. <br> ***Figyelmeztetés**: Ez a szándék elsősorban a "Confirm" műveletet foglalja magában az éttermi foglalási forgatókönyvben. Ha több általános kifejezésre van szüksége a "Confirm" (megerősítés) mezőben, használja a "Confirm" szándékot a **segédprogramok** tartományában. * | Igen, elvégeztem a foglalást este 8 órakor a tészta Makerben. <br> Igen, csak foglalja le. <br> Erősítse meg a foglalást a sushi bárban.
FindReservationDetail | Jelenítse meg a meglévő foglalás részletes információit. | Saját foglalás keresése a Renaissance San Diegoban <br> A foglalás holnapi megjelenítése. <br> A Zucca vonatkozó foglalás részleteinek megjelenítése.
FindReservationWhere | Keresse meg a foglalás abszolút helyét. | Hol található a Zucca helye a saját foglalásban? <br> A foglalásom helyi beállításainak megjelenítése holnap.
FindReservationWhen | A foglalás pontos időpontjának ellenõrzése | Mikor kerül a holnap Zucca? <br> A foglalás ideje a vörös homáron.
Elutasítás | A felhasználó elutasítja a foglalás kezeléséhez javasolt virtuális Segédet. <br> ***Figyelje**meg,:P bérlet további általános hosszúságú kimondott szöveg elutasításához tekintse meg a **segédprogramok** tartományát. * | Nem kell beállítani az eseményt. | Nem, nem szeretném módosítani a foglalást. <br> Nem, ne foglaljon le hibát.

### <a name="entities"></a>**Szervezetek**
LUIS entitás | Entitás típusa | Leírás | Példák
-------|------|---------|-------------------
Cím | egyszerű | Egy esemény helyre vagy egy foglalás címe. | Rendszert futtató Palo Alto<br>300 112th Ave képlet használata<br>Seattle
Levegőben | List | Egy étterem hangulatának leírása. | romantic<br>az alkalmi<br>a csoportok jó<br>szép
Cuisine | egyszerű | Élelmiszer, cuisine vagy cuisine állampolgárság típusát. | kínai<br>olasz<br>Mexikói<br>Sushi<br>Tészta<br>steak
MealType | List | A Foglalás társított dara típus. | A reggeli<br>Vacsora<br>Ebéd<br>Supper
PlaceName | egyszerű | Egy étterem neve | Zucca<br>Cheesecake gyári<br>Vörös Homár
Minősítés | egyszerű | Egy hely vagy egy éttermi minősítése. | 5 csillag<br>3 csillag<br>4 csillagos
NumberPeople | egyszerű | A foglaláshoz személyek száma | 3<br>hat
Time | datetime| Az étterem foglalásának ideje | holnap<br>Tonight<br>7:00 óra<br>Szenteste


## <a name="todo"></a>**ToDo**
A _Todo_ -tartomány feladatlistákat biztosít a felhasználók számára a teendők hozzáadásához, megjelöléséhez és törléséhez.
### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------
AddToDo | A felhasználó a felsorolt típusok bármelyikéhez hozzá kívánja adni a feladatokat. |  Emlékeztessen a tej megvásárlására. <br> "Vásárlás tej" nevű elem felvétele a feladatlistára
Megerősítés | A felhasználó szeretne megerősíteni egy folyamatban lévő műveletet. A teljes kifejezés explicit jeleket tartalmaz, például az "igen" jelet a művelet megerősítéséhez. <br> <br> ***Figyelmeztetés**: Ez a szándék elsősorban a "Confirm" műveletet tartalmazza a ToDo-forgatókönyv esetében. Ha több általános kifejezésre van szüksége a "Confirm" (megerősítés) mezőben, használja a "Confirm" szándékot a **segédprogramok** tartományában. * | Törölje a feladatot. <br> Biztos, hogy hozzá szeretném adni ezt a feladatot. <br> Igen, erre van szükségem.
Mégse | A felhasználó szeretné megszakítani a folyamatban lévő műveletet.  <br> <br> ***Figyelmeztetés**: Ez a szándék elsősorban a "Confirm" műveletet foglalja magában az éttermi foglalási forgatókönyvben. Ha több általános kifejezésre van szüksége a "Confirm" (megerősítés) mezőben, használja a "Confirm" szándékot a **segédprogramok** tartományában. * | Nem, ne felejtse el. <br> csak szakítsa meg a feladatot. <br> Nem, ne adja hozzá.
DeleteToDo | Töröljön egy elemet a feladatlistán a sorrendre való hivatkozással, vagy törölje az összes tennivaló elemet. | Távolítsa el az összes feladatot. <br> Segítség a második törléséhez.
MarkToDo | Feladat megjelölése befejezettként vagy elvégezve a megrendelés vagy a feladat tartalmára való hivatkozással. | A "tej vásárlása" feladat befejezettként való megjelölése. <br> A feladat olvasásának befejezése. <br> Fejezze be az összeset.
ShowNextPage | A lista több oldalra is fel lesz osztva. Listaelemek megjelenítése a következő oldalon a felhasználó számára. | Meg tudja jeleníteni a következő lapot a bevásárlási listán? <br> Mi a következő lépés? <br> A következő lépések
ShowPreviousPage | Listaelemek megjelenítése az előző oldalon a felhasználó számára. | előző megjelenítése. <br> Ellenőriznie kell az előző feladatokat.
ShowToDo | A Feladatlista összes elemének megjelenítése. | Saját vásárlások listájának megjelenítése <br> Saját élelmiszerboltok listájának megjelenítése

### <a name="entities"></a>**Szervezetek**
LUIS entitás | Entitás típusa | Leírás | Példák
-------|------|---------|-------------------
ContainsAll | egyszerű | A Feladatlista összes vagy bármely elemének jelölése | segítséget nyújt az **összes** feladat eltávolításához. <br> Fejezze be a **mindent**.
ordinal | ordinal | Egy tételhez tartozó sorszám vagy numerikus hivatkozás. | Megjelölheti a **harmadikat** befejezettként. <br> Törölje az **első** feladatot.
ListType | egyszerű | Feladatlista típusa  | Cipő hozzáadása a **bevásárlási** listához.
FoodOfGrocery | List | Az élelmiszeripari elemek listájának észlelése | Emlékeztessen a **tej**megvásárlására. <br> Adja hozzá a **marhahúst** a saját élelmiszerbolt listához.
TaskContent | egyszerű, minta. bármilyen | Egy feladat tartalmának észlelése. | Emlékeztessen a **saját anyám meghívására** . <br> Az **ünnepelt John születésnapjának** hozzáadása a tennivalók listájához


## <a name="utilities"></a>**Segédprogramok**
A _segédprogramok_ tartománya az összes _Luis_ előre összeépített modell egyik általános tartománya, amely közös szándék-és hosszúságú kimondott szöveg tartalmaz a különbségi helyzetekben.
### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------
 Mégse | Művelet/kérelem/feladat vagy szolgáltatás megszakítása. | Megszakítása. <br> Ne feledje, hogy elfelejti.
 Megerősítés | Erősítse meg a műveletet. | Persze <br> Igen, kérjük, <br> Ellenőrizze.
 Elutasítás | A felhasználó elutasítja a javasolt virtuális Segédet. | Nem
 FinishTask | Fejezze be a felhasználó egy feladatot. | Kész vagyok. <br> Ennyi az egész. <br> Befejezés gombra.
 GoBack | Lépjen vissza egy lépést, vagy térjen vissza az előző lépésben. | Lépjen vissza egy lépésre. <br> menj vissza.
 Súgó | Kérelem segítséget. | Kérjük, segítsen. <br> Nyissa meg a súgót.
 Ismétlés | Ismételje meg a műveletet. | Mondja újra.
 ShowNext | A következő elem (ek) megjelenítése vagy megmondása. | Megjelenítés a következő parancsot.
 ShowPrevious | Sorozat megjelenítése az előző elemmel. | Előző egy megjelenítése.
 StartOver | Indítsa újra az alkalmazást, vagy új munkamenet indításához. | Indítsa újra.
 Leállítás | Tájékoztassa a virtuális Segédet a beszélgetés megszakításáról.  | Kérjük, mondja el ezt.
 SelectAny | A felhasználó a képernyőn megjelenő bármely elemet vagy eredményt kijelöli. | Ezek bármelyike. <br> Válasszon ki egyet.
 SelectNone | A felhasználó kiválasztja a meglévő elemek egyikét sem, és további beállításokat kér. | Más javaslatok is megadhatók. <br> Egyik sem.
  SelectItem | A felhasználó a képernyőn megjelenő elem vagy eredmény kiválasztását kéri. | Válassza ki a harmadikat. <br> Válassza a jobb felső sarokban.
 Eszkalálás | Kérje meg az ügyfélszolgálatot, hogy kezelje a problémákat. | Tudok valakivel kommunikálni?
 ReadAloud | Olvasson el valami hangosan a felhasználónak. | Olvassa el ezt a lapot. <br> Olvassa el hangosan.

### <a name="entities"></a>**Szervezetek**
LUIS entitás | Entitás típusa | Leírás | Példák
------------|-------------|-------------|---------
ordinal | ordinal | Egy tételhez tartozó sorszám vagy numerikus hivatkozás. | A **második** . <br> **Ezután** .
number | number | A felhasználó által kiakart elemek mennyisége | A következő **3** elem
DirectionalReference | egyszerű | Egy hivatkozási pont, ahol a képernyőn egy elem található. | A jobb oldal <br> Felső

## <a name="weather"></a>**Weather**
Az időjárási tartomány az időjárási feltételek és az olyan tanácsadók ellenőrzésére összpontosít, amelyekben a hely és az idő, illetve az idő időjárási körülmények között van.
### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------
 CheckWeatherValue | Az előrejelzési vagy a múltbeli információk elérési helyétől függően időjárási vagy időjárási tényezőt is kérhet. <br> Az időjárással kapcsolatos tényezők többek között a következők: <li> hőmérséklet </li> <li> eső/hó/csapadék </li> <li> száraz/nedves/páratartalom </li> <li> köd </li> <li> UV-index </li> <li> eső/hó centiméter </li> | Mi a levegőminőségi index Pekingben? <br> Mennyi Rain várható a Seattle-ben márciusban? <br> Jegyezze fel a Hawaii legmagasabb hőmérsékletét.
 CheckWeatherTime | Kérje meg az előre jelzett vagy a múltbeli időjárási viszonyok egy adott helyre vonatkozó tényezőit. | Mikor várható az eső? <br> Jó idő a Kanadába való ugrásra <br> Mikor van a legforróbb hónap a Minnesotaban?
 QueryWeather | Kérdezzen az időjárási feltételekről vagy az időjárási tényezőkről egy adott helyhez, ahol az "igen, nem vagy talán" válasz várható, vagy kérjen olyan tevékenységgel kapcsolatos tanácsot, amely az időjárási feltételektől függ. | Holnap eső lesz? <br> Ma Sunny? <br> Túl korán fordulhat az alaszkai-hoz?
 ChangeTemperatureUnit | Az időjárási egység módosítása, beleértve a Celsius, a Kelvin és a Fahrenheit mértékegységet. | A konverzió Celsius-fok. <br> Megkaphatom, hogy Kelvinben?
 GetWeatherAdvisory | Adott hely időjárási tanácsadóinak vagy riasztásának beolvasása. | Vannak időjárási tanácsadók a Memphisben? <br> Van-e időjárási riasztás a saját részhez?

### <a name="entities"></a>**Szervezetek**
LUIS entitás | Entitás típusa | Leírás | Példák
------------|-------------|-------------|---------
Location | Földrajz | Az időjárási kérések abszolút vagy implicit helye. | Palo Alto<br>Sanghaj<br>Seattle<br>Delvina<br>
Date/Time   | datetime | Az időjárás lekérdezésének dátuma vagy időtartama. | November<br>óránkénti<br>reggel<br>Ezen a hétvégén<br>10 nap<br>
AdditionalWeatherCondition | list | Az időjárás további leírása, például a szél sebessége vagy iránya. | direction<br>Gyors<br>Sebességű
Történelmi | egyszerű | A múltbeli időjárási feltételek Leírás szavai, beleértve az átlagot, az elmúlt időszakban megjelenő eseteket. | elmúlt<br>korábbi/korábbi előzmények<br>szezonális<br>legjobb idő<br>valaha rögzített
PrecipitationUnit | dimenzió | A hó vagy az eső csapadéka. | 5 hüvelyk<br>6 cm
SuitableFor | egyszerű | Egy időjárási feltétel alá tartozó emberi tevékenység leírása, amely gyakori, amikor a felhasználók az időjárási feltételektől függő tevékenységekre vonatkozó tanácsokat kérdeznek le. | kabát<br>esernyő<br>úszni
TemperatureUnit |hőmérséklet | hőmérséklet | 18 Celsius<br>7 Kelvin fok
WeatherRange | egyszerű | A hőmérséklet, a szél és az egyéb időjárási feltételek konkrét feltétele egy adott időtartam alatt | maximum<br>Magas<br>alacsony<br>átlagos magas<br>Legmagasabb
WeatherCondition | egyszerű | Időjárási feltétel leírása | Sunny<br>eső<br>eső<br>hőmérséklet<br>vízisízni<br>gyakori elérésű
WindDirectionUnit | list | A szél irányának szavai | Észak<br>Dél<br>Kelet<br>Nyugat<br>Északkelet Németország


## <a name="web"></a>**Web**
A webes tartomány biztosítja a szándékot és az entitásokat a webhelyek kereséséhez.
### <a name="intents"></a>**Leképezések**
Cél neve | Leírás | Példák
---------|----------|---------
 WebSearch | Egy adott webhelyre vagy keresésre irányuló kérelem egy keresőmotorban. | Keresési felület a google.com-ben. <br> Boldog születésnap-zeneszám keresése a weben <br> Nyissa meg a www.twitter.com.

### <a name="entities"></a>**Szervezetek**
LUIS entitás | Entitás típusa | Leírás | Példák
------------|-------------|-------------|---------
Keresőmarketing | List | A keresőmotor felhasználója szeretné használni. | Bing <br> Google
Keresettszöveg | egyszerű, minta. Bármely | A szöveges felhasználó szeretne keresni. <br> _A "barátok a Facebookon" címkével Keresettszöveg, ha a "in" után a webhely nem keresőmotor. Az URL-címnek a Keresettszöveg is címkével kell rendelkeznie._ | Film <br> Deep learning <br> Tom Cruise
Összekapcsolás | url | A webhely hivatkozása. | www.twitter.com

