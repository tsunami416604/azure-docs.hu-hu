---
title: Hogyan rögzítése a voice-minták létrehozásához egy egyéni beszédfelismerési |} A Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Győződjön meg arról, a gyártási minőségű rendelésre hang robusztus parancsfájl előkészítése, mindegyik hangalapú jó szakembereket és professzionális rögzítése.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 1afab3b7c128abdf97c3a16fc493935bab5cfd4c
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072265"
---
# <a name="how-to-record-voice-samples-for-a-custom-voice"></a>Hogyan lehet egyéni beszédfelismerési rögzítése a voice-minták

A magas színvonalú éles egyéni beszédfelismerési létrehozása előzmények nem hangvételt, mintha kötelezettségvállalás. A központi összetevője egy egyéni beszédfelismerési hangmintát emberi beszéd nagy gyűjteménye. Rendkívül fontos, hogy ezek hangfelvételeket kiváló minőségű kell-e. Válassza ki a hangalapú szakembereket, így a felvételt az ilyen típusú tapasztalattal rendelkezik, és azokat egy professzionális berendezések használatával illetékes felvétel mérnök által rögzített.

Mielőtt elvégezné ezeket a felvételt, azonban szüksége parancsfájl: által a hangalapú szakembereket hozhat létre a hangmintát fog kell felolvasott szavakat. A legjobb eredmények érdekében a parancsfájl helyes fonetikus lefedettsége és az egyéni beszédfelismerési modell betanításához elegendő különböző kell rendelkeznie.

A professzionális hangrögzítés sok kis, de fontos részleteket fel. Ez az útmutató olyan egy ütemtervet a folyamatot, amely segít a megfelelő, következetes eredményeinek beolvasása.

> [!TIP]
> A legmagasabb minőségű eredmények között fontolja meg a Microsoft az egyéni beszédfelismerési fejlesztéséhez vonzó. A Microsoft magas színvonalú beszédhangot a saját termékek, köztük a Cortana és az Office előállító széles körű ismeretekkel rendelkezik.

## <a name="voice-recording-roles"></a>A hangrögzítés letltása szerepkörök

Egy egyéni beszédfelismerési felvétel projekt négy alapvető szerepkörök találhatók.

Szerepkör|Cél
-|-
Hangalapú szakembereket        |A személy hangalapú vevőrendelési fog az egyéni beszédfelismerési.
Felvétel a visszafejtés  |Hogyan felügyeli a felvétel technikai aspektusait és a rögzítés berendezések működtetésével foglalkozik.
Igazgató            |A parancsfájl előkészíti, és a hangalapú szakembereket teljesítmény coaches.
Szerkesztő              |Finalizes, a hangfájlokat, és felkészítheti őket a beállításhoz az egyéni beszédfelismerési portálon való feltöltéshez.

Egyéni előfordulhat, hogy töltse ki a több szerepkört. Ez az útmutató feltételezi, hogy meg fog kell elsősorban kitöltés a igazgató szerepkör és mindegyik hangtípus szakembereket és a egy felvételt mérnök. Van néhány információt a felvételt a visszafejtés szerepkör abban az esetben, ha azt szeretné, hogy a felvételt saját magának.

## <a name="choosing-voice-talent"></a>Hangalapú szakembereket kiválasztása

Actors-tapasztalattal voiceover vagy szóbeli karakter munkahelyi egyéni beszédfelismerési jó szakembereket ellenőrizze. Megfelelő szakembereket announcers és hírolvasó gyakran is megtalálhatja.

Válassza ki a hangalapú szakembereket, amelynek a természetes hangtípus, például. Hozhat létre egyedi "character" beszédhangot, lehetséges, de legyen, következetesen hajtsa végre a legtöbb szakembereket sokkal nehezebb, és részéről az erőfeszítés hangalapú törzs okozhat.

> [!TIP]
> Általában kerülje a felismerhető beszédhangot hozhat létre olyan egyéni beszédfelismerési – kivéve, ha természetesen célja az, hogy a hírességek hang előállításához. Alacsonyabb szintű ismert beszédhangot olyan általában kevésbé zavaró a felhasználók számára.

Az egyik legfontosabb tényező hangalapú szakembereket kiválasztására vonatkozó konzisztencia. A felvételek kell az összes hang-, módosította őket ugyanarra a napra ugyanabban a helyiségben. Ez ideális jó felvétel eljárások és mérnöki keretében is készíthető elő. 

A szóbeli szakembereket a másik fele a egyenlet. Ő az egységes sebesség, a kötetek szintjén, a lényegét és a képviselő hangvételét mérnökeinkkel képesnek kell lennie. Egyértelmű diction kell. A szakembereket is képesnek kell lennie a szigorúan ellenőrzik a saját projektjeire variation érzelmi hatással és speech mannerisms.

Egyedi Hangüzenetek minták rögzítése több fatiguing, mint más hangalapú munka lehet. A legtöbb hangalapú szakembereket napi két vagy három órán vehet fel. Korlátozza három vagy négy egy hét naponta munkameneteket ki a kettő között, ha lehetséges.

Arról, hogy egy hang modell felvételek kötődsz semleges kell lennie. Azt jelenti egy távozik utterance (kifejezés) nem olvasható távozik módon. Hangulatának a szintetizált prosody vezérlők segítségével később is hozzáadhatók. A szóbeli szakembereket fejlesztéséhez egy "személy", amely meghatározza az egyéni beszédfelismerési teljes hang- és érzelmi képviselő hangvételét működnek. A folyamat során meg fog azonosíthatja a milyen "semleges" hangzik, mint az adott személy.

Egy személy lehet például egy természetes módon upbeat felhőszerepkör felel. Ezért "saját" hangjával is elvégezheti optimism jegyezze akkor is, ha neutrally beszélni. Az ilyen egy személy is leálljon azonban finom és egységes kell lennie. A kívánt elemére képet kapjon a meglévő beszédhangot által olvasmányok fog.

> [!TIP]
> Általában érdemes a hangalapú felvétel választja ki a saját. A beszédfelismerési szakembereket kell lennie a projekt egy munkahelyi – a felvételi külön kezelhető.

## <a name="creating-a-script"></a>Parancsfájl létrehozása

Minden olyan egyéni beszédfelismerési munkamenet rögzítése kiindulópontjaként a parancsfájlt, amely tartalmazza a hangalapú szakembereket által felolvasott lehet a kimondott szöveg. ("Beszédmódok" kifejezés magában foglalja a teljes mondatokat és rövidebb mondatok is.)

A parancsfájlban megcímkézzen származhatnak bárhol: fantasztikus, nem fantasztikus, beszédek, news jelentések és bármi más elérhető átiratok nyomtatott űrlap. Ha azt szeretné, hogy a hangját does is az adott típusú szavakat (például terminológiát vagy programozási szakkifejezéseivel), érdemes mondatokat egyetemi tanulmányok vagy műszaki dokumentáció tartalmazza. (Azonban [Legalities](#legalities) alatt.) A saját szöveget is írhat.

A kimondott szöveg azonos forrásból, vagy a forrás ugyanolyan típusú nem kell származnia. Nem is szükséges, ami ehhez egymással. Azonban akkor, ha fogja használata állítsa mondatokat (például "sikeresen bejelentkezett") az speech-alkalmazás ügyeljen arra, hogy a szkriptet fogja össze őket. Ez lehetőséget biztosít az egyéni beszédfelismerési jobb esélye a ezeket a kifejezéseket is felel. És kell való használatra megad egy felvételt szintetizált helyett, ha már rendelkezik, a mint a szintetizált azonos használatának.

Kulcsot hangalapú szakembereket kiválasztása a konzisztencia pedig különböző a hallmark jó parancsfájlra. A szkript számos más szavakat és a mondat hosszúságú, és a hangulatokra különböző mondatokat tartalmaznia kell. Minden nyelven hang mindig és számos olyan környezeteket kezeli őket több kell lennie (nevű *fonetikus lefedettség).* 

Továbbá a szöveget kell tartalmazniuk, összes lehetséges, hogy az adott hang írásban jelölhető, és helyezze el az egyes hang különböző helyeken a mondatokban. Deklaratív mondatokat és kérdéseket is érdemes lehet tartalmazza, és olvassa el a megfelelő intonation.

Nehéz írni egy parancsfájlt, amely biztosít *éppen elegendő* ahhoz, hogy a Custom Speech-portál hozhat létre egy jó hangalapú adatokat. A gyakorlatban a legegyszerűbb módja, hogy egy parancsfájl, amely hatékony fonetikus lefedettséget biztosít az minták nagy számú. A Microsoft standard beszédhangot utterances tízezer lett létrehozva. Jegyezze fel a több ezer utterances hozhat létre egy gyártási minőségű egyéni beszédfelismerési néhány előkészített kell lennie.

Ellenőrizze a parancsfájl alaposan a hibákat. Ha lehetséges valaki más próbálja túl van. Amikor a szakembereket futtatja a parancsfájl segítségével, valószínűleg az néhány további hibák tényleges lesz.

### <a name="script-format"></a>Parancsfájl-formátum

A szkript írhat a Microsoft Word programban. A parancsfájl a rögzítési munkamenet közben van, így beállíthatja úgy találja egyszerűen dolgozhat bármilyen módon. Hozzon létre a szükséges egyéni beszédfelismerési portál által külön-külön szövegfájl.

Alapvető parancsprogram formátum három oszlopot tartalmazza:

* Az utterance (kifejezés), 1-gyel kezdődik száma. Számozása megkönnyíti a Studio hivatkozni egy adott utterance (kifejezés) mindenki számára ("próbálkozzon szám 356 újra"). A Word bekezdés számozása funkció segítségével automatikusan száma a tábla sorait.
* Üres oszlop hol fogja írja be a lekérési szám, vagy minden kimondásakor segítséget nyújtanak a befejezett felvétel az található kód idő.)
* Az utterance (kifejezés) maga szövege.

![Példaszkript](media/custom-voice/script.png)

> [!NOTE]
> A legtöbb studios rögzítése rövid néven szegmensek *vesz igénybe.* Minden egyes végezze el a tíz – 24 utterances általában tartalmazza. A lekérési szám csak megállapítva az utterance (kifejezés) később található megfelelő lesz. A studióban, amely arra törekszik, hosszabb felvételek rögzítését, ha szeretné inkább vegye figyelembe az idő kódjában. A studio egy neves idő megjelenítése fog rendelkezni.

Után minden sort írhat jegyzeteket, hagyja elég hely. Győződjön meg arról, hogy nincs utterance (kifejezés) oldalai között van felosztva. A lapok számát, és nyomtassa ki a parancsfájl a papír oldalán.

Nyomtassa ki a parancsfájl három másolata: egyet a szakembereket, egyet a visszafejtés és egyet a igazgató, (,). A tanulmány klip staples helyett használjon: egy tapasztalt hangalapú előadó elkülönített az oldalakat a lapok vannak kapcsolva, így a zaj elkerülése érdekében.

### <a name="legalities"></a>Legalities

Szerzői jogi szerzői jogvédelem alatt álló szöveg egy szereplő olvasási lehet a teljesítmény, amelynek a szerző a munka kell bude kompenzována. A teljesítmény nem ismerhető fel a végtermékben, az egyéni beszédfelismerési. Még ha így nem jól bevált a jogszerűségét, szerzői jogvédelem alatt álló munkahelyi használatának erre a célra. A Microsoft nem biztosít jogi tanácsadásként ennél a hibánál; Tekintse meg a saját tanácsadóként.

Szerencsére a lehetőség a problémák teljes egészében elkerülése érdekében. Nincsenek számos forrásból szöveg engedéllyel vagy licenc nélkül is használhatja.

|Szöveg forrás|Leírás|
|-|-|
|[CMU tavi forrásgyűjteményébe](http://festvox.org/cmu_arctic/)|Kifejezetten a speech összefoglaló projektekben használható out-az-szerzői jogok a kiválasztott készül 1100 mondatokat. Egy remek kiindulási pont.|
|Már nem működik.<br>a szerzői jogi|Általában works 1923 előtt közzé. Az angol nyelvű tájékoztatáshoz [projekt Gutenberg](https://www.gutenberg.org/) kínál több ezer, az ilyen működik. Előfordulhat, hogy szeretné, a nyelv modern angol közelebb lesz újabb works összpontosíthat.|
|Kormányzati&nbsp;működik|Az Egyesült Államok kormánya által létrehozott működése nem szerzői jogvédelem alatt állnak az Egyesült Államokban, ha a kormányzat igényelhet a szerzői jog más országokban.|
|Nyilvános tartományt|Melyik copyright explicit módon biztosítják, illetve, hogy működik a nyilvános tartomány rendelkezik lett kijelölve. (Ez nem lehetséges elengedni copyright teljes mértékben az egyes jogi szabályozásainak.)|
|Permissively licenccel rendelkező működése|Például a Creative Commons licencet, vagy a GNU ingyenes dokumentáció licenc forgalmazva működik. Wikipedia a GFDL használ. Néhány licenccel, azonban előfordulhat, hogy korlátozásokat alkalmazzanak a teljesítmény, a licencelt tartalom, amely hatással lehet egy egyéni beszédfelismerési modell létrehozását, olvassa el a licencet.|

## <a name="recording-your-script"></a>A szkript rögzítése

Lehetőleg kell rögzíteni található szakmai felvétel stúdióban, amely a szkriptet hangvétel munkahelyi. A rögzítés érzékelőadatainak, a megfelelő eszközök és működjön, a megfelelő személyeknek rendelkeznek. Nem, a rögzítés mulassza fizet.

A projekt tárgyalják a studio felvétel szakértővel, és figyeli a saját tanácsokat. A felvétel alig vagy egyáltalán nem dinamikus tartományának tömörítés (legfeljebb 4:1) kell rendelkeznie. Rendkívül fontos, hogy a hanganyag konzisztens kötet és a egy magas jel zaj arány rendelkezik ugyanakkor nemkívánatos hangok ingyenes.

### <a name="doing-it-yourself"></a>Saját

Ha azt szeretné, hogy a felvétel saját maga is ahelyett, a rögzítés studióba, itt látható egy rövid ismertetését. Otthoni felvétel és kínálja a hallgatóknak márkáik, köszönhetően egyszerűbb, mint valaha használatával megtalálhatja a helyes felvétel tanácsokat és erőforrások online.

A "rögzítés érzékelőadatainak" kell lennie egy kis szoba nincs észrevehető echo vagy a "hely képviselő hangvételét." Azt is csendes és a lehető soundproof kell lennie. A falak drapériát segítségével csökkentheti az echo és semlegesítsük vagy "deaden" a hangot a helyet.

Használjon szánt rögzítése a voice studio magas színvonalú hűtő mikrofont (röviden "mic" jelöli). Sennheiser AKG és még újabb nagyítás megkérhetném jó eredmények eredményezhet. A mic vásárolni, vagy bérelhet egy helyi audiovizuális bérleti cég közül. Keressen egy olyan USB-kapcsolat. Az ilyen típusú mic kényelmesen egyesíti a mikrofon elem preamp és analóg digitális converter egy csomagot, leegyszerűsíti a összekapcsolása.

Egy analóg mikrofon is használhatja. Számos bérleti házak "szüreti" mikrofonok elismert a hangalapú karakter kínálnak. Vegye figyelembe, hogy szakmai analóg fogaskerék elosztott terhelésű XLR összekötőket használ, nem pedig az 1/4" csatlakoztassa a fogyasztói berendezések használt. Ha analóg, is szüksége lesz egy preamp és a egy számítógép hang felületen ezeket az összekötőket.

A mikrofon egy önálló vagy bumm telepít és elé "plosive" hangzás "p" és "b". a háttérzaj kiküszöbölése érdekében a mikrofon pop szűrő Néhány mikrofonok, amely elkülöníti azokat a rezgés bővíteni, ami hasznos lehet a felfüggesztés csatlakoztatási kapható.

A szóbeli szakembereket a mikrofon konzisztens távolságra kell maradnia. Az emelet a szalag használatával, üzembe kell megjelölni. Szeretném a szakembereket részesíti előnyben, különös gondot mic távolság figyelése, és elkerülheti az elnöki zaj igénybe vehet.

Használjon a parancsfájl tárolásához. Kerülje a horgászat bővíteni, hogy tükrözzék, hang, mikrofon felé.

A személy, aki a rögzítés készülék – a mérnök – a szakembereket valamilyen módon a szakembereket a rögzítés érzékelőadatainak az kommunikáljon az egy helyiségben kell lennie (egy *talkback kapcsolatcsoport).*

A felvételt, kis zaj, amennyire csak lehetséges, amelynek az a célja egy 80 db jel zaj aránynak vagy jobb tartalmaznia kell.

A "érzékelőadatainak," zaj ahol származik, és az OK kiküszöbölése vizsgálhatja a csend felvétel szorosan fog. Közös zaj forrásai szellőzőnyílásokkal, fluoreszcens világos fénycsőelőtétek, forgalom a közeli utak és berendezések ventilátor (még akkor is, a jegyzetfüzet számítógépek rendelkezhet ventilátorok). Mikrofon és kábelek folytattuk a munkát a közeli AC-háttérszolgáltatást, általában egy hum elektromos zaj vagy lelkesítő.

> [!TIP]
> Bizonyos esetekben előfordulhat, hogy kell segítségével egy hangszínszabályzó vagy egy zaj csökkentésére szoftverfrissítési beépülő modul zaj eltávolítása felvételeit, bár minden esetben célszerű állítsa le a forrásban.

A legtöbb digitális felvétel elérhető dinamikus tartományának overdriving nélkül történik, így szintek kell beállítani. Ez azt jelenti, hangos lejátszásra, de nem így hangos, hogy a hanganyag eltorzítják. Alább egy példát a helyes felvétel hullám van.

![jó felvétel hullám](media/custom-voice/good-recording.png)

Itt a tartomány (magasság) a legtöbb használatos, de a jel a legmagasabb csúcsok nem érte el a top vagy az ablak alsó részén. Is láthatja, hogy a csend elszámolása megközelíti a dinamikus kiosztás vízszintes vonal, egy alacsony zaj emelet jelzi. Ez a felvétel elfogadható dinamikus tartomány és a jel zaj arány rendelkezik.

Közvetlenül a számítógéphez egy kiváló minőségű hang felület vagy egy USB-porttal rekordot, attól függően, a mic használ. Analóg, tartsa meg a hang lánc egyszerű: mic, preamp, hang felület, a számítógép. Mindkét [Avid Pro eszközök](http://www.avid.com/en/pro-tools) és [Adobe Audition](https://www.adobe.com/products/audition.html) havonta licencelni elfogadható áron. Ha a költségvetés rendkívül szoros, próbálja ki az ingyenes [Audacity](https://www.audacityteam.org/).

Jegyezze fel minőségben 44,1 KHz 16 bites monophonic (CD), vagy még jobb. Aktuális állapot,-legújabb 48 KHz 24 bites, akkor, ha az a készülék támogatja ezt a funkciót. Lehetővé teszi a felbontásának 16 KHz 16 bites, a hangot az egyéni beszédfelismerési portálra való elküldése előtt. Továbbra is hogy megéri rendelkezik egy kiváló minőségű eredeti rögzítése abban az esetben, ha módosítások szükségesek.

A szerepkörök igazgató, a visszafejtés és szakembereket szolgálja ki a különböző emberek ideális esetben van. Ne kísérelje meg ezt az összes saját maga! A csippentés a igazgató és a visszafejtés lehet több személy.

### <a name="before-the-session"></a>Mielőtt a munkamenet

Elkerülése érdekében, ami pazarolja a studio idő, futtassa a parancsfájlt a hangalapú szakembereket keresztül a rögzítési munkamenet előtt. A beszédfelismerési szakembereket válik a szöveg ismeri, miközben ő a kiejtés bármely ismeretlen szavak is tisztázása.

> [!NOTE]
> A legtöbb felvétel studios elektronikus megjelenítését a rögzítés érzékelőadatainak parancsfájlok kínálnak. Ebben az esetben a run-through megjegyzések közvetlenül írja be a parancsfájl-dokumentumot. Érdemes a papír másolatot feljegyzéseket a munkamenet során azonban továbbra is. A legtöbb mérnökök túl érdemes egy vezetékhelyettesítési. És továbbra is gondoskodni a harmadik biztonsági mentéséhez a szakembereket a nyomtatott példány, abban az esetben, ha a számítógép nem működik.

A beszédfelismerési szakembereket kérheti, mely azt szeretné, word megcélzó az utterance (kifejezés). Aktorok hívja, ez az "operatív szót." Mondja el neki, nincs különös tekintettel a természetes olvasó szeretne. Kiemelés speech synthesized van; Ha adhatók hozzá nem lehet az eredeti felvétel részét.

Közvetlenül a szakembereket a jelszórészek mondjuk ki szavakat. A parancsfájl minden szó kell lennie ejtsd formában. Hangok nem szabad nincs megadva vagy slurred, gyakori az alkalmi-beszéd átalakítás, együttesen *, kivéve, ha azok alkalmazáskonfigurációjának ezzel a módszerrel a szkriptben.*

|Írt szöveg|A nem kívánt hangvételt, mintha kiejtése|
|-|-|
|soha nem szeretné, hogy Ön|soha nem szeretné, hogy Ön|
|négy jelzőfény|ott van a négy lámpa|
|hogyan alakul mai időjárás|Hogyan történik az adatok "mai időjárás|
|a kis barátjának búcsút|Tegyük fel, saját lil hello "typu friend|

Szakembereket kell *nem* adja hozzá a szavak közötti különböző szünetel. A mondat továbbra is áramlásának magától értetődő, kevés formális riasztások hang közben is. Ezt a gondot megkülönböztetést kellhet eljárás is igénybe vehet.

### <a name="the-recording-session"></a>A rögzítési munkamenet

Hozzon létre egy felvételt, referencia vagy *egyezés fájlt,* egy tipikus utterance (kifejezés), a munkamenet elején annak. Kérje meg a szakembereket, ismételje meg ezt a sort, vagy minden lapon és a egy fél. Minden alkalommal, amikor, hasonlítsa össze az új rögzítés-referenciáját. Ez az eljárás segítségével a szakembereket a kötetet, tempo, lényegét és intonation konzisztens marad. Eközben a mérnök használhatja az egyezés fájl hivatkozásként van listázva szinteket és az e hang.

A match fájl különösen fontos, amikor folytatása a felvételt, egy szünet után, vagy egy másik napon. Érdemes a szakembereket a néhányszor, lejátszásához, és ismételje meg minden egyes alkalommal, amíg nem jól megfelelő őket.

A részletes például és a egy kis ideig, mielőtt minden kimondásakor szüneteltetése a szakembereket coach. Jegyezze fel a néhány másodperc csend között a kimondott szöveg. Szavak kell lennie ejtsd azonos módon minden alkalommal, amikor jelennek meg, figyelembe véve a környezetben: "rögzítése" szerint egy művelet van ejtsd eltérően a "rekord" főnévként.

Jegyezze fel a helyes öt másodpercenként az első rögzítés előtt csend rögzítheti a "hely képviselő hangvételét." Ez segít az egyéni beszédfelismerési portálon minden fennmaradó lényeget a felvételt a meghiúsult lépések kompenzációjához.

> [!TIP]
> Valóban szüksége a hangalapú szakembereket vonalak érdekében, hogy csak azok a sorok monophonic (single-csatorna) felvétel. Azonban ha sztereó rögzíti, a második csatorna segítségével rögzítse az adott sorokat tárgyalása rögzítése a vezérlő helyiségben a chatter, vagy vesz igénybe. Távolítsa el a nyomon követése a egyéni beszédfelismerési portálra történő feltöltés verzió.

Szorosan, figyelési, fejhallgatót a hangalapú szakembereket teljesítménye. Helyes, de természetes diction írásmódja megfelelő és nem kívánt hangok hiánya keres. Ne habozzon, kérje meg a szakembereket újra jegyezze fel az utterance (kifejezés), amely nem felel meg ezeknek a szabványoknak. 

> [!TIP] 
> Beszédmódok nagy mennyiségű rögzítésekor egy egyetlen utterance (kifejezés) nem lehet az így kapott, egyéni hangalapú észrevehető hatása. Ezért érdemes lehet egyszerűen problémákkal rendelkező megszólalásokat jegyezze fel, zárja ki őket az adatkészlet, és tekintse meg, hogyan az egyéni beszédfelismerési elemről kiderül, hogy több bármelyikére. Mindig lépjen vissza a studióban, és jegyezze fel a kihagyott minták később.

Jegyezze fel az a lekérési szám, vagy minden kimondásakor, amikor a parancsfájlt a kódot. Kérje meg a mérnök, ha minden kimondásakor, a rögzítés metaadatok vagy köteg táblázatban is jelölheti.

Rendszeres oldaltörések igénybe vehet, és adjon meg olyan, amelyet a hangalapú szakembereket fenntartható jó minősége hangjának segítségével.

### <a name="after-the-session"></a>A munkamenet után

A modern felvétel studios futtasson azokon a számítógépeken. A munkamenet végén egy vagy több audio-fájlokat kap, nem szalagon. Ezek a fájlok valószínűleg a CD-minőségi (44,1 KHz 16-bit) WAV vagy AIFF formátumot kell használni, vagy jobb. 48 kHz 24 bites a közös és kívánatos. Nagyobb mintavételi arányt, 96 KHz, például általában nincs szükség.

Az egyéni beszédfelismerési portálon minden egyes megadott utterance (kifejezés) kell lennie a saját fájl szükséges. Az egyes studio által kínált hangfájlok tartalmazhat több kimondott szöveg. Ezért az elsődleges utáni üzemi feladata felosztani a máshogy a felvételt, és készítse elő azokat küldésének. A rögzítés mérnök esetleg a fájlban elhelyezni jelölők (vagy egy külön köteg listában megadott) annak jelzésére, ahol minden kimondásakor kezdődik.

Használja a jegyzetek található, a pontos továbblép szeretné, majd a segédprogram például szerkesztési hang [Avid Pro eszközök](http://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html), vagy az ingyenes [Audacity](https://www.audacityteam.org/) minden kimondásakor másolása egy új fájlba.

Hagyja üresen a csend elején és végén az első kivételével minden klip mindössze 0,2 másodperc. Ezt a fájlt kell kezdődnie teljes csend öt másodperc. Ne használja a hang-szerkesztő "nulla out" csendes a fájl része. Többek között a "hely"hangfrekvenciás az egyéni beszédfelismerési bármely maradék háttérzaj meghiúsult lépések kompenzációjához algoritmusok segítségével.

Alaposan az egyes fájlok figyelésére. Ezen a ponton ki, amelyek kimaradtak a felvételt, például egy sort, mielőtt egy enyhe nyelvi csomag smack kis nemkívánatos hangok szerkesztheti, de ügyeljen arra, hogy bármilyen tényleges speech eltávolítása. Ha egy fájl nem oldja meg, távolítsa el az adatkészlethez, így egy vegye figyelembe, hogy még meg az.

Minden egyes fájl átalakítása 16 bites és a egy mintavételi gyakoriság 16 kHz mentés előtt, és ha a studio chatter rögzített, távolítsa el a második csatornát. Mentse a fájlokat a WAV-formátumban, a fájlok elnevezési a parancsfájl az utterance (kifejezés) számát.

Végül hozza létre a *átirat* egyes WAV-fájl, amely társítja a megfelelő utterance (kifejezés) megjelenő szöveges változata. [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md) olyan, megfelelő formátumú adatokat tartalmaz. Közvetlenül a szkriptet a másolhatja a szöveget. Ezután hozzon létre egy ZIP-fájlban a WAV-fájlok és a szöveg a szövegben.

Az eredeti felvételek biztonságos helyen, archiválhatja, abban az esetben szüksége lesz rájuk később. A szkript és a megjegyzések, túl megőrzése.

## <a name="next-steps"></a>További lépések

Készen áll a felvételek feltöltése és létrehozása a saját egyéni hangját!

> [!div class="nextstepaction"]
> [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md)
