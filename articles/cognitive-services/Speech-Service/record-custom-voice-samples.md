---
title: Egyéni hangminták rögzítése – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Készíts egy termelési minőségű egyéni hang elkészítésével robusztus script, bérbeadása jó hang tehetség, és a felvétel szakmailag.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b076c642f72e45f58be61d67e887e11b6ccb0aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74975856"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Hangminták rögzítése egyéni hang létrehozásához

Létrehozása kiváló minőségű termelési egyéni hang a semmiből nem alkalmi vállalkozás. Az egyéni hang központi eleme az emberi beszéd hangmintáinak nagy gyűjteménye. Rendkívül fontos, hogy ezek a hangfelvételek kiváló minőségűek legyenek. Válasszon olyan hangalapú tehetséget, aki tapasztalattal rendelkezik az ilyen típusú felvételek készítésében, és egy hozzáértő hangmérnökkel rögzítheti őket professzionális berendezések használatával.

Mielőtt azonban elkészítetheti ezeket a felvételeket, szüksége van egy szkriptre: a szavakra, amelyeket a hangtehetsége fog kimondani a hangminták létrehozásához. A legjobb eredmény érdekében a parancsfájlnak jó fonetikus lefedettséggel és elegendő változatossággal kell rendelkeznie az egyéni hangmodell betanításához.

Sok apró, de fontos részleteket bemegy létre egy professzionális hangfelvétel. Ez az útmutató egy olyan folyamat ütemterve, amely segít a jó, konzisztens eredmények elkészítésében.

> [!TIP]
> A legjobb minőségű eredmények érdekében fontolja meg a Microsoft bevonását az egyéni hang fejlesztéséhez. A Microsoft széles körű tapasztalattal rendelkezik a kiváló minőségű hangok készítésében saját termékeihez, beleértve a Cortanát és az Office-t.

## <a name="voice-recording-roles"></a>Hangrögzítő szerepek

Egy egyéni hangrögzítő projektben négy alapvető szerepkör található:

Szerepkör|Cél
-|-
Hang tehetség        |Ennek a személynek a hangja képezi majd az egyéni hang alapját.
Hangmérnök  |Felügyeli a felvétel technikai szempontjait és működteti a menetíró készüléket.
Igazgató            |Előkészíti a forgatókönyvet, és edzaasza a hangtehetség teljesítményét.
Szerkesztő              |Véglegesíti a hangfájlokat, és előkészíti őket az egyéni hangportálra való feltöltésre.

Az egyén egynél több szerepet is betölthet. Ez az útmutató feltételezi, hogy ön lesz elsősorban kitöltésével a rendezőszerepét, és bérbeadása mind a hang tehetség és a felvétel mérnök. Ha saját kezűleg szeretné elkészíteni a felvételeket, ez a cikk tartalmaz néhány információt a hangmérnök szerepkörről. A szerkesztői szerepkörre csak a munkamenet után van szükség, így a rendező vagy a hangmérnök elvégezheti.

## <a name="choose-your-voice-talent"></a>Válaszd ki a hangtehetségedet

Színészek tapasztalattal voiceover vagy hang karakter munka, hogy jó egyéni hang tehetség. Gyakran talál megfelelő tehetséget a bemondók és a hírolvasók között is.

Válaszd ki a hangalapú tehetséget, akinek a természetes hangját szereted. Lehetőség van, hogy hozzon létre egyedi "karakter" hangok, de ez sokkal nehezebb a legtöbb tehetség, hogy végre őket következetesen, és az erőfeszítés okozhat hang törzs.

> [!TIP]
> Általában ne használjon felismerhető hangokat egyéni hang létrehozásához – kivéve persze, ha a cél az, hogy híresség hangját hozza létre. A kevésbé ismert hangok általában kevésbé zavaróak a felhasználók számára.

A hangalapú tehetség kiválasztásának legfontosabb tényezője a következetesség. A felvételeknek úgy kell hangzaniuk, mintha ugyanazon a napon készültek volna ugyanabban a szobában. Ezt az ideálisat a helyes rögzítési gyakorlatokkal és a mérnöki munkával közelítheti meg.

A hangtehetséged az egyenlet másik fele. Képesnek kell lenniük arra, hogy beszéljen ekkonzisztens sebességgel, hangerő, hangmagasság, és a hang. A világos beszéd mód. A tehetség is kell tudni, hogy szigorúan ellenőrizzék a pályán variáció, érzelmi hatással, és a beszéd modora.

Az egyéni hangminták rögzítése fárasztóbb lehet, mint más típusú hangmunka. A legtöbb hangtehetség napi két-három órát tud felvenni. Korlátozza a munkameneteket heti három-négy re, ha lehetséges, egy szabadnapot a kettő között.

A hangmodellhez készített felvételeknek érzelmileg semlegesnek kell lenniük. Ez azt jelenti, hogy a szomorú kijelentést nem szabad szomorú módon olvasni. Hangulat lehet adni a szintetizált beszéd később keresztül prosody ellenőrzéseket. A hangalapú tehetséggel együttműködve olyan "személyiséget" alakítson ki, amely meghatározza az egyéni hang általános hangját és érzelmi tónusát. A folyamat során, akkor pontosan milyen "semleges" hangzik, hogy a persona.

Egy személyiségnek lehet például egy természetesen derűs személyisége. Tehát "a" hang is hordoz egy megjegyzés az optimizmus akkor is, ha beszélnek semlegesen. Azonban egy ilyen személyiségvonáslegyen finom és következetes. Hallgassa meg a meglévő hangok olvasását, hogy képet kapjaaarról, hogy mire törekszik.

> [!TIP]
> Általában, akkor szeretné, hogy a saját hangfelvételeket csinál. A hangadott tehetségednek alkalmasnak kell lennie a projekt rekedésére.

## <a name="create-a-script"></a>Parancsfájl létrehozása

Minden egyéni hangfelvétel-munkamenet kiindulópontja a parancsfájl, amely tartalmazza a hangtehetség által kimondott szövegeket. (A "kimondott szöveg" kifejezés magában foglalja a teljes mondatokat és a rövidebb kifejezéseket is.)

A kimondott szöveg a forgatókönyvben bárhonnan érkezhet: fikció, nem-fikció, beszédek átiratai, híradások és bármi más, amely nyomtatott formában elérhető. Ha azt szeretnénk, hogy megbizonyosodjon arról, a hangja nem jól bizonyos típusú szavak (mint például az orvosi terminológia vagy programozási zsargon), érdemes lehet felvenni mondatokat tudományos papírok vagy műszaki dokumentumok. A lehetséges jogi kérdések rövid ismertetése a ["Jogszerűség"](#legalities) című részben található. Saját szöveget is írhat.

A kimondott szöveg nem kell származnia ugyanabból a forrásból, vagy az azonos típusú forrásból. Nem is kell, hogy bármi közük legyen egymáshoz. Ha azonban beállított kifejezéseket (például "Sikeresen bejelentkezett") használ a beszédfelismerési alkalmazásban, győződjön meg arról, hogy azokat beilleszti a parancsfájlba. Ez ad az egyéni hang nagyobb eséllyel kiejteni ezeket a kifejezéseket is. És ha úgy dönt, hogy egy felvételt használ a szintetizált beszéd helyett, akkor már ugyanazt a hangot fogja használni.

Míg a következetesség kulcsfontosságú kiválasztásában hang tehetség, a fajta fémjelzi a jó forgatókönyvet. A szkriptnek sok különböző szót és mondatot kell tartalmaznia, különböző mondathosszakkal, struktúrákkal és hangulatokkal. Minden hangot a nyelven kell képviselni többször és számos összefüggésben (úgynevezett *fonetikus lefedettség*).

Továbbá a szövegnek tartalmaznia kell minden olyan módot, amelylehetővé teszi egy adott hang írásban történő ábrázolásának módját, és minden hangot különböző helyeken kell elhelyeznie a mondatokban. Mind a deklaratív mondatokat, mind a kérdéseket bele kell foglalni, és megfelelő hanglejtéssel kell felolvasni.

Nehéz olyan parancsfájlt írni, amely *éppen elegendő* adatot biztosít ahhoz, hogy az egyéni beszédportál jó hangot hozzon létre. A gyakorlatban a legegyszerűbb módja annak, hogy egy script, amely eléri a robusztus fonetikus lefedettség, hogy tartalmazza a nagy számú mintát. A Microsoft által biztosított szabványos hangok több tízezer kimondott szövegből készültek. Legalább néhány vagy több ezer utterances rögzítésére kell felkészülnie egy éles minőségű egyéni hang létrehozásához.

Ellenőrizze a parancsfájl gondosan hibákat. Ha lehetséges, valaki más ellenőrizze azt is. Amikor végigfutasz a forgatókönyvön a tehetségeddel, valószínűleg elkapsz még néhány hibát.

### <a name="script-format"></a>Parancsfájl formátuma

A parancsfájlt a Microsoft Word ben is megírhatjuk. A szkript a felvétel során használható, így bármilyen módon beállíthatja, hogy könnyen használható legyen. Az egyéni hangportál által megkövetelt szövegfájl külön-külön létrehozása.

Az alapparancsfájl-formátum három oszlopot tartalmaz:

* Az utterance (kifejezés) száma, 1-től kezdődően. A számozás megkönnyíti a stúdió minden tagjának, hogy egy adott kijelentésre hivatkozzon ("próbáljuk meg újra a 356-os számot"). A Word bekezdésszámozási funkciójával automatikusan számozhatja a táblázat sorait.
* Egy üres oszlop, ahol az egyes kimondott szöveg ek felvételi számát vagy időkódját fogja írni, hogy segítsen megtalálni azt a kész felvételben.
* A szöveg a kimondott szöveg maga.

![Példaszkript](media/custom-voice/script.png)

> [!NOTE]
> A legtöbb stúdió rekord rövid szegmensben ismert *veszi*. Minden egyes take általában tartalmaz 10-24 utterances. Csak a take szám tudomásul veszi elegendő egy utterance (kifejezés később). Ha olyan stúdióban készít felvételt, amely inkább hosszabb felvételeket készít, akkor inkább az időkódot kell feljegyeznie. A stúdió nak lesz egy kiemelkedő idő kijelző.

Hagyjon elég helyet minden sor után a jegyzetek írásához. Győződjön meg arról, hogy nincs kimondott szöveg oldalak között oszlik meg. Számozhatja az oldalakat, és nyomtassa ki a forgatókönyvet a papír egyik oldalára.

Nyomtasson ki három példányt a forgatókönyvből: egyet a tehetségnek, egyet a mérnöknek, egyet pedig a rendezőnek (te). Használjon gemkapcsot kapcsok helyett: egy tapasztalt hangművész szétválasztja az oldalakat, hogy elkerülje a zajzatot az oldalak elfordulásakor.

### <a name="legalities"></a>Legalities

A szerzői jogi törvények értelmében a szerzői joggal védett szöveg színészi felolvasása olyan előadás lehet, amelyért a mű szerzőjét kompenzálni kell. Ez a teljesítmény nem lesz felismerhető a végtermék, az egyéni hang. Még így is, a jogszerűségét egy szerzői joggal védett mű erre a célra nem jól megalapozott. A Microsoft nem tud jogi tanácsot adni ebben a kérdésben; konzultáljon saját ügyvédjével.

Szerencsére, lehetséges, hogy elkerüljék ezeket a kérdéseket teljesen. Számos szövegforrást használhat engedély vagy licenc nélkül.

|Szöveg forrása|Leírás|
|-|-|
|[CMU Sarkvidéki corpus](http://festvox.org/cmu_arctic/)|Körülbelül 1100 mondatot választottak ki szerzői jogi védelem alatt álló művekből, amelyek kifejezetten beszédszintézisi projektekben használhatók. Kiváló kiindulási pont.|
|Már nem működik<br>szerzői jog alatt|Jellemzően 1923 előtt publikált művek. Az angol, [Project Gutenberg](https://www.gutenberg.org/) kínál több tízezer ilyen művek. Érdemes lehet összpontosítani újabb művek, mint a nyelv közelebb lesz a modern angol.|
|Kormányzati&nbsp;munkák|Az Egyesült Államok kormánya által létrehozott művek nem szerzői jogvédelem alatt állnak az Egyesült Államokban, bár a kormány más országokban/régiókban is hivatkozhat szerzői jogra.|
|Nyilvános tulajdon|Olyan művek, amelyek szerzői jogát kifejezetten elutasították, vagy amelyeket a nyilvánosság számára szenteltek. Előfordulhat, hogy egyes jogrendszerekben nem lehet teljes mértékben lemondani a szerzői jogról.|
|Engedékenyen engedélyezett munkák|Művek forgalmazott licenc alatt, mint a Creative Commons vagy a GNU Free Documentation License (GFDL). A Wikipédia a GFDL-t használja. Egyes licencek azonban korlátozhatják a licencelt tartalom teljesítményét, ami hatással lehet egy egyéni hangmodell létrehozására, ezért figyelmesen olvassa el a licencet.|

## <a name="recording-your-script"></a>A parancsfájl rögzítése

Rögzítse a forgatókönyvet egy professzionális stúdióban, amely a hangmunkára specializálódott. Lesz egy hangstúdiójuk, a megfelelő felszerelésük, és a megfelelő emberek, hogy működtessék. Megéri, hogy ne skimp a felvétel.

Beszélje meg a projektet a stúdió hangmérnökével, és hallgassa meg a tanácsaikat. A felvételnek kevés dinamikus tartományú tömörítéssel kell rendelkeznie (legfeljebb 4:1). Rendkívül fontos, hogy a hang hanganyaga egyenletes legyen, és magas jel-zaj aránnyal legyen, miközben nem kívánatos hangoktól mentes.

### <a name="do-it-yourself"></a>Csináld magad

Ha azt szeretnénk, hogy a felvétel magad, ahelyett, hogy egy stúdióban, itt egy rövid alapozó. Köszönhetően a nő az otthoni felvétel és podcasting, ez könnyebb, mint valaha, hogy megtalálja a jó felvételi tanácsadás és források online.

A "felvételi fülkének" egy kis szobának kell lennie, észrevehető visszhang vagy "szobahang" nélkül. A lehető legcsendesebbnek és hangszigeteltnek kell lennie. Drapériák a falakon lehet használni, hogy csökkentsék a visszhang és semlegesíti vagy "deaden" a hang a szobában.

Használjon kiváló minőségű stúdió kondenzátor mikrofont (röviden"mikrofon" ) hangfelvételre. A Sennheiser, az AKG és még az újabb Zoom mikrofonok is jó eredményeket érhetnek el. Lehet kapni egy mikrofon, vagy bérelni egy helyi audio-vizuális kölcsönző cég. Keressen egy USB-csatlakozóval. Ez a fajta mikrofon kényelmesen egyesíti a mikrofon elem, előerősítő, és analóg-digitális átalakító egy csomagban, egyszerűsítve hookup.

Analóg mikrofont is használhat. Sok bérházak kínálnak "vintage" mikrofonok híres hangkarakter. Ne feledje, hogy a professzionális analóg fogaskerék kiegyensúlyozott XLR csatlakozókat használ, nem pedig a fogyasztói berendezésekben használt 1/4 hüvelykes dugót. Ha megy analóg, akkor is szükség van egy előerősítő és a számítógép audio interfész ezekkel a csatlakozók.

Telepítse a mikrofont egy állványra vagy gémre, és helyezzen be egy pop szűrőt a mikrofon elé, hogy megszüntesse a "plosive" mássalhangzók, például a "p" és a "b" zaját. Egyes mikrofonok felfüggesztési tartóval vannak elcsatolva, amely elszigeteli őket az állvány rezgéseitől, ami hasznos.

A hangtehetségnek a mikrofontól egyenletes távolságban kell maradnia. Használja szalagot a padlón, hogy jelölje meg, ahol meg kell állni. Ha a tehetség inkább ülni, különös gonddal, hogy figyelemmel kíséri a mikrofon távolság és ne szék zaj.

Állvány használatával tartsa a parancsfájlt. Kerülje az állvány horgászatát, hogy az visszaverje a hangot a mikrofon felé.

A menetíró készüléket üzemeltető személynek – a mérnöknek – a tehetségtől külön szobában kell lennie, és valamilyen módon beszélnie kell a felvételi fülkében lévő tehetségekkel *(talkback áramkör).*

A felvételnek a lehető legkevesebb zajt kell tartalmaznia, 80 db-os jel-zaj arány vagy annál jobb céllal.

Hallgassa meg figyelmesen a felvétel a csend a "fülkében", kitalálni, ahol a zaj jön, és megszünteti az oka. A gyakori zajforrások a szellőzőnyílások, a fluoreszkáló fényelőtasztatok, a közeli utakon a forgalom és a berendezések ventilátorai (még a notebook PC-knek is lehetnek ventilátorai). Mikrofonok és kábelek is vegye fel az elektromos zaj a közeli AC vezetékek, általában a zümmögés vagy zümmögés. A zümmögést okozhatja a *földhurok*is, amelyet az okoz, hogy a berendezés több elektromos áramkörhöz van csatlakoztatva.

> [!TIP]
> Bizonyos esetekben előfordulhat, hogy használhat egy hangszínszabályzót vagy egy zajcsökkentő szoftver beépülő modult, hogy segítsen eltávolítani a zajt a felvételekből, bár mindig a legjobb, ha a forrásánál állítja le.

Állítsa be a szinteket úgy, hogy a digitális felvétel rendelkezésre álló dinamikus tartományának nagy része túlvezetés nélkül használható. Ez azt jelenti, hogy állítsa be a hangot hangosan, de nem olyan hangosan, hogy eltorzul. Egy jó felvétel hullámformájának egy példája a következő képen látható:

![Egy jó felvételi hullámforma](media/custom-voice/good-recording.png)

Itt a tartomány (magasság) nagy részét használják, de a jel legmagasabb csúcsai nem érik el az ablak tetejét vagy alját. Azt is láthatja, hogy a felvétel csendje megközelíti a vékony vízszintes vonalat, amely alacsony zajszintű padlót jelez. Ez a felvétel elfogadható dinamikatartományt és jel-zaj arányt eredményez.

Rögzítsen közvetlenül a számítógépbe kiváló minőségű hanginterfészen vagy USB-porton keresztül, a használt mikrofontól függően. Az analóg, tartsa az audio lánc egyszerű: mikrofon, előerősítő, audio interfész, számítógép. Az [Avid Pro Tools](https://www.avid.com/en/pro-tools) és az [Adobe Audition](https://www.adobe.com/products/audition.html) havonta is licencelhető elfogadható áron. Ha a költségvetés rendkívül szoros, próbálja ki az ingyenes [Audacity](https://www.audacityteam.org/).

44,1 kHz-es, 16 bites monofónikus (CD minőség) vagy jobb felvétel. A jelenlegi korszerű 48 kHz-es 24 bites, ha a berendezés támogatja azt. A 16 kHz-es 16 bites hangmintát, mielőtt elküldenék az egyéni hangportálra. Mégis, megéri, hogy egy jó minőségű eredeti felvétel esetén szerkesztések van szükség.

Ideális esetben, hogy a különböző emberek szolgálnak a szerepek rendező, mérnök, és a tehetség. Ne próbáld meg egyedül csinálni. Egy csipetnyi, egy személy lehet mind a rendező és a mérnök.

### <a name="before-the-session"></a>A munkamenet előtt

Annak elkerülése érdekében, hogy a stúdió idő, fuss át a forgatókönyvet a hang tehetségét, mielőtt a felvétel ülésén. Bár a hangtehetség megismerkedik a szöveggel, tisztázhatja az ismeretlen szavak kiejtését.

> [!NOTE]
> A legtöbb stúdió elektronikus annektált forgatókönyveket kínál a felvételi fülkében. Ebben az esetben írja be a végigfutó jegyzeteket közvetlenül a parancsfájl dokumentumába. A munkamenet során azonban továbbra is szüksége lesz egy papíralapú másolatra. A legtöbb mérnök is szeretne egy nyomtatott példányt. És akkor is szüksége van egy harmadik nyomtatott példányt, mint egy biztonsági másolatot a tehetség, ha a számítógép nem.

A hangod tehetsége megkérdezheti, hogy melyik szót szeretnéd hangsúlyozni egy kijelentésben (a "kulcsszó). Mondd meg nekik, hogy természetes olvasmányt akarsz, különösebb hangsúly nélkül. A beszéd szintetidása esetén a hangsúly kiadható; nem lehet része az eredeti felvételnek.

Irányítsd a tehetséget, hogy határozottan ejtse ki a szavakat. A forgatókönyv minden szavát úgy kell kiejteni, ahogy meg van írva. Hangok nem szabad kihagyni, vagy elmosódott együtt, mint a közös alkalmi beszéd, *kivéve, ha már írt, hogy így a forgatókönyvet*.

|Írott szöveg|Nem kívánt alkalmi kiejtés|
|-|-|
|soha nem fog feladni|soha nem fog feladni|
|négy lámpa van|Négy lámpa van.|
|Milyen az időjárás ma|Milyen az időjárás ma|
|köszönj a kis barátomnak|köszönj a lil' barátomnak|

A tehetség *nem* adhat hozzá különböző szünetek szavak között. A mondat nak természetesen kell folynia, még akkor is, ha egy kicsit formálisnak hangzik. Ez a finom különbséget lehet, hogy a gyakorlat, hogy jobb.

### <a name="the-recording-session"></a>A felvételi munkamenet

Hozzon létre egy referencia-felvételt, vagy *egyezési fájlt,* egy tipikus utterance (kifejezés) a munkamenet elején. Kérd meg a tehetséget, hogy ismételje meg ezt a sort minden oldalon, vagy úgy. Minden alkalommal hasonlítsa össze az új felvételt a hivatkozással. Ez a gyakorlat segít a tehetség továbbra is következetes a hangerő, tempó, pitch, és intonáció. Eközben a mérnök használhatja a match fájlt referenciaként a szintekhez és a hang általános konzisztenciájához.

Az egyezési fájl különösen akkor fontos, ha szünet után vagy egy másik napon folytatja a felvételt. Akkor eldönthetjük, hogy játszani, hogy egy párszor a tehetség, és azokat ismételje meg minden alkalommal, amíg azok megfelelő is.

Edző a tehetségét, hogy egy mély lélegzetet, és szünet egy pillanatra, mielőtt minden kijelentés. Rögzítsen néhány másodpercnyi csendet a kimondott szövegek között. A szavakat ugyanúgy kell kiejteni minden alkalommal, amikor megjelennek, figyelembe véve a kontextust. Például a "rekord" igeként a főnévi "rekord" szavaktól eltérően ejtik ki.

Rögzítsen egy jó öt másodperces csendet az első felvétel előtt, hogy rögzítse a "szoba hangot". Ez a gyakorlat segít az egyéni hangportál kompenzálja a felvételek ben fennmaradó zajt.

> [!TIP]
> Minden, amit igazán kell elfog a hang tehetség, így lehet, hogy egy monofonikus (egycsatornás) felvétel csak a sorokat. Ha azonban sztereó ban rögzít, a második csatornával rögzítheti a csevegést a vezérlőteremben, hogy rögzítse az adott vonalak vagy felvételekek megvitatását. Távolítsa el ezt a számot az egyéni hangportálra feltöltött verzióból.

Hallgassa meg figyelmesen, fejhallgató segítségével, hogy a hang tehetség teljesítményét. Jó, de természetes beszédmódra, helyes kiejtésre és a nem kívánt hangok hiányára vágyik. Ne habozzon, kérje meg a tehetségét, hogy újra rögzítsen egy olyan kijelentést, amely nem felel meg ezeknek a szabványoknak.

> [!TIP]
> Ha nagy számú kimondott szöveget használ, előfordulhat, hogy egyetlen utterance (kifejezés) nem rendelkezik észrevehető hatással az eredő egyéni hang. Célszerűbb lehet egyszerűen megjegyezni a problémákat, kizárni őket az adatkészletből, és látni, hogy az egyéni hang kiderül. Bármikor visszamehet a stúdióba, és később rögzítheti a kihagyott mintákat.

Vegye figyelembe a take number vagy time code a parancsfájlon minden utterance (kifejezés). Kérje meg a mérnököt, hogy jelölje meg az egyes kimondott szövegeket a felvétel metaadat- vagy végadatlapján is.

Tartson rendszeres szüneteket, és adjon egy italt, hogy a hangtehetsége jó formában tartsa hangját.

### <a name="after-the-session"></a>A munkamenet után

A modern stúdiók számítógépeken futnak. A munkamenet végén egy vagy több hangfájlt kap, nem szalagot. Ezek a fájlok valószínűleg WAV vagy AIFF formátumban CD minőségű (44,1 kHz-es 16 bites) vagy jobb. 48 kHz-es 24 bites gyakori és kívánatos. Általában nincs szükség magasabb mintavételi sebességre, például 96 kHz-re.

Az egyéni hangportál minden megadott utterance (kifejezés) a saját fájlban kell lennie. A stúdió által szállított minden egyes hangfájl több kimondott szöveget tartalmaz. Tehát az elsődleges utómunka a felvételek felosztása és a benyújtásra való felkészülés. Előfordulhat, hogy a hangmérnök jelölőket helyezett el a fájlban (vagy külön dákólapot biztosított), jelezve, hogy hol kezdődik az egyes kimondott szöveg.

A jegyzetek segítségével keresse meg a kívánt pontos adatokat, majd használjon hangszerkesztő segédprogramot, például [az Avid Pro Tools,](https://www.avid.com/en/pro-tools)az [Adobe Audition](https://www.adobe.com/products/audition.html)vagy az ingyenes [Audacity](https://www.audacityteam.org/)eszközt, hogy minden kimondott szöveget új fájlba másoljon.

Az első kivételével csak 0,2 másodpercnyi csendet hagyhat az egyes klipek elején és végén. Az akta öt másodpercnyi hallgatással kezdődik. Ne használjon hangszerkesztőt a fájl "nulla ki" néma részeinek "nullázására". Beleértve a "szoba hang" segít a Custom Voice algoritmusok kompenzálni a maradék háttérzaj.

Minden fájlt figyelmesen hallgasson meg. Ebben a szakaszban kiszerkesztheti a felvétel során kihagyott kis nem kívánt hangokat, például egy kis ajakcsapot a sor előtt, de legyen óvatos, hogy ne távolítson el egyetlen tényleges beszédet sem. Ha nem tudja kijavítani a fájlt, távolítsa el az adatkészletből, és jegyezze fel, hogy ezt megtette.

A mentés előtt konvertálja az egyes fájlokat 16 bitre és 16 kHz-es mintavételi sebességre, és ha rögzítette a stúdió csevegést, távolítsa el a második csatornát. Mentse az egyes fájlokat WAV formátumban, elnevezése a fájlokat a kimondott szám a parancsfájlból.

Végül hozza létre az *átiratot,* amely az egyes WAV-fájlokat a megfelelő utterance (kifejezés) szöveges verziójához társítja. [Az egyéni hangbetűtípusok létrehozása](how-to-customize-voice-font.md) tartalmazza a szükséges formátum részleteit. A szöveget közvetlenül a parancsfájlból másolhatja. Ezután hozzon létre egy Zip fájlt a WAV fájlokés a szöveg átiratát.

Archiválja az eredeti felvételeket biztonságos helyen, ha később szüksége lenne rájuk. Őrizze meg a forgatókönyvet és a jegyzeteket is.

## <a name="next-steps"></a>További lépések

Készen áll a felvételek feltöltésére és az egyéni hang létrehozására.

> [!div class="nextstepaction"]
> [Egyéni hangbetűtípusok létrehozása](how-to-customize-voice-font.md)
