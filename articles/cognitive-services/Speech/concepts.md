---
title: Bing Speech fogalmak | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Microsoft Speech Service-ben használt alapvető fogalmak.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fba1bbdeaf68bdd45524b336011627a27cd024da
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965716"
---
# <a name="basic-concepts"></a>Alapfogalmak

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Ez az oldal a Microsoft Speech Recognition szolgáltatás néhány alapvető fogalmát ismerteti. Javasoljuk, hogy olvassa el ezt a lapot, mielőtt a Microsoft Speech Recognition API-t használja az alkalmazásban.

## <a name="understanding-speech-recognition"></a>A beszédfelismerés ismertetése

Ha első alkalommal hoz létre beszédfelismerésre alkalmas alkalmazást, vagy ha első alkalommal ad hozzá beszédfelismerési képességeket egy meglévő alkalmazáshoz, ez a szakasz segítséget nyújt a kezdéshez. Ha már rendelkezik némi tapasztalattal a beszédfelismerést használó alkalmazásokkal kapcsolatban, dönthet úgy, hogy csak leválasztja ezt a szakaszt, vagy teljesen kihagyhatja, ha Ön egy régi beszéd, és a protokoll részleteit szeretné megkapni.

### <a name="audio-streams"></a>Audio streamek

A legfontosabb alapfogalmak közé tartozik az *audio stream*. A billentyűleütéstől eltérően, amely egy adott időpontban fordul elő, és egyetlen információt tartalmaz, a kimondott kérelmek több száz ezredmásodpercig terjednek, és sok kilobájtos információt tartalmaznak. A kimondott hosszúságú kimondott szöveg időtartama némi nehézségekbe ütközik a fejlesztők számára, hogy zökkenőmentes és elegáns beszédfelismerési élményt nyújtsanak az alkalmazáshoz. A mai számítógépek és algoritmusok a Kimondás időtartama körülbelül felében a beszéd átírását hajtják végre, így a 2 másodperces kiírás nagyjából 1 másodpercen belül elvégezhető, de minden olyan alkalmazás, amely a felhasználó 1 másodperces késleltetését tapasztalja. sem a áramvonalas, sem az elegáns.

Szerencsére a lemondási idő egy részének átírásával történik, ha a felhasználó egy másik részt beszél. Ha például egy 1 másodperces kiosztást oszt szét 10 darab 100 ezredmásodpercre, és az egyes adattömbökbe való átírást hajt végre, akkor az átíráshoz szükséges összes 500 ezredmásodpercnél több mint 450 lehet "rejtett", hogy a felhasználó ne legyen tisztában az átirattal folyamatban van. Ha erre a példára gondol, ne feledje, hogy a szolgáltatás az előző 100 ezredmásodpercben elvégezte az átírást, miközben a felhasználó a következő 100-as számú hangra mutat, így ha a felhasználó leállítja a beszédet, a szolgáltatásnak nagyjából 100-t kell átírnia. ezredmásodpercben, hogy eredményt hozzon létre.

Ennek a felhasználói élménynek a megvalósításához a rendszer a beszélt hangadatokat adattömbökben gyűjti össze, és a felhasználó beszél. Ezek a hangtömbök együttesen az *audio streamből*származnak, és a hangtömbök küldésének folyamatát *hangadatfolyamnak nevezzük.* A hangtovábbítás fontos részét képezi a beszédfelismerést használó alkalmazásoknak; az adathalmaz méretének finomhangolása és az adatfolyam-megvalósítás optimalizálása az alkalmazás felhasználói élményének leghatékonyabb módja.

### <a name="microphones"></a>Mikrofonok

A felhasználók a füleik használatával dolgozzák fel a hanganyagot, de az inanimálási hardver mikrofont használ. A beszédfelismerés bármely alkalmazásban való engedélyezéséhez integrálni kell az alkalmazáshoz tartozó hangadatfolyamot biztosító mikrofonnal.

A mikrofonhoz tartozó API-k lehetővé teszik, hogy a mikrofonból elindítsa és leállítsa az audió bájtok fogadását. El kell döntenie, hogy mely felhasználói műveletek aktiválja a mikrofont a beszéd figyelésének megkezdéséhez. Dönthet úgy, hogy a gomb megnyomásával megnyomja a figyelés megkezdése gombot, vagy dönthet úgy is, *hogy a Word vagy a* *Wake Word* -figyelő mindig figyeli a mikrofont, és használja a modul kimenetét, hogy elindítsa a hangot a Microsoft Speech szolgáltatásnak Szolgáltatás.

### <a name="end-of-speech"></a>A beszéd vége

Észlelés, *Ha* egy beszélő *abbahagyta* a beszédet, elég egyszerűnek tűnik az emberek számára, de a laboratóriumi feltételeken kívüli, meglehetősen nehéz probléma. Nem elég, ha egyszerűen csak a tiszta csendet szeretnék kipróbálni, hiszen a probléma sokszor sok környezeti zajt okoz. A Microsoft Speech szolgáltatás kiváló feladatokkal szolgál a gyors észleléshez, amikor egy felhasználó abbahagyta a beszédet, és a szolgáltatás értesítheti az alkalmazást, de ez a megoldás azt jelenti, hogy az Ön alkalmazása a legutóbb tudnia kell, ha a felhasználó leállítja a beszédet. Ez egyáltalán nem más, mint a bevitel más formái, ahol az alkalmazás az *első* , aki tudni szeretné, hogy mikor kezdődik *és* végződik a felhasználó bemenete.

### <a name="asynchronous-service-responses"></a>Aszinkron szolgáltatásokra adott válaszok

Az a tény, hogy az alkalmazásnak tájékoztatnia kell arról, ha a felhasználói bevitel befejeződött, nem számít fel semmilyen teljesítménnyel kapcsolatos szankciót vagy programozási nehézséget az alkalmazásában, de megköveteli, hogy a bemeneti kérelemtől eltérően gondoljon a beszédfelismerési kérésekre/ az ismerős válaszokat. Mivel az alkalmazás nem fogja tudni, hogy mikor szűnik meg a felhasználó, az alkalmazásnak továbbra is továbbítania kell a hangot a szolgáltatásnak, miközben egyidejűleg és aszinkron módon kell várnia a szolgáltatás válaszára. Ez a minta a más kérés/válasz webes protokollok, például a HTTP protokolltól eltérően van. Ezekben a protokollokban minden válasz fogadása előtt végre kell hajtania egy kérelmet; a Microsoft Speech Service protokollban választ kaphat, *Amikor továbbra is a hangvételt kéri a kéréshez*.

> [!NOTE]
> Ez a funkció nem támogatott a Speech HTTP-REST API használata esetén.

### <a name="turns"></a>Kiderül

A Speech az információk hordozója. Ha beszél, olyan információt próbál továbbítani, amely az Ön birtokában van, aki az adott információt figyeli. Az információk továbbításakor általában a beszéd és a figyelés válik elérhetővé. Hasonlóképpen, a beszédfelismerést támogató alkalmazás a felhasználókkal folytatja a figyelést és a válaszadást, bár az alkalmazás általában a figyelést végzi. A felhasználó szóbeli bemenetét és az erre a bemenetre adott válaszának neve *turn*. Ha az alkalmazás befejezte a beszédfelismerési szolgáltatás válaszának kezelését, *akkor a felhasználó* beszél és ér véget.

### <a name="telemetry"></a>Telemetria

A beszédfelismerésre képes eszköz vagy alkalmazás létrehozása kihívást jelenthet a tapasztalt fejlesztők számára is. A stream-alapú protokollok gyakran ijesztőnek tűnnek első pillantásra, és fontos részletek, például a csend észlelése teljesen új lehet. Ha sok üzenetre van szüksége ahhoz, hogy sikeresen lehessen elküldeni és fogadni egy kérés/válasz pár teljesítését, *nagyon* fontos, hogy az üzenetek teljes és pontos adatait összegyűjtse. A Microsoft Speech Service protokoll ezen adatgyűjtést biztosítja. Minden erőfeszítést meg kell tennie, hogy a lehető legpontosabban adja meg a szükséges adatmennyiséget; a teljes és pontos adatok megadásával segítheti magát – ha segítségre van szüksége a Microsoft Speech Service-csapattól az ügyfél-implementáció hibaelhárítása során, a gyűjtött telemetria-adatok minősége kritikus fontosságú lesz a probléma megoldásában. elemzés.

> [!NOTE]
> Ez a funkció nem támogatott a Speech Recognition REST API használata esetén.

### <a name="speech-application-states"></a>Speech Application-állapotok

Az alkalmazásban a beszédfelismerési bevitel engedélyezéséhez szükséges lépések némileg eltérnek a más típusú bemenetek, például egérkattintások vagy ujjal való Koppintások lépéseitől. Nyomon kell követnie, hogy az alkalmazás Mikor figyeli a mikrofont, és adatokat küld a beszédfelismerési szolgáltatásnak, amikor a szolgáltatás válaszára vár, és ha inaktív állapotban van. Ezen állapotok közötti kapcsolat az alábbi ábrán látható.

![Beszédfelismerő alkalmazás állapotának diagramja](Images/speech-application-state-diagram.png)

Mivel a Microsoft Speech Service néhány államban részt vesz, a szolgáltatási protokoll olyan üzeneteket határoz meg, amelyek segítenek az alkalmazásnak az állapotok közötti átállásában. Az alkalmazásnak a beszédfelismerési alkalmazás állapotának nyomon követéséhez és kezeléséhez a protokoll üzeneteinek értelmezését és kezelését kell eljárnia.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>A beszédfelismerési szolgáltatás használata az alkalmazásokból

A Microsoft Speech Recognition Service kétféle módszert biztosít a fejlesztők számára az alkalmazásokhoz való beszéd hozzáadására.

- [REST API](GetStarted/GetStartedREST.md)-k: A fejlesztők a beszédfelismeréshez használhatnak HTTP-hívásokat az alkalmazásaikat a szolgáltatáshoz.
- [Ügyféloldali kódtárak](GetStarted/GetStartedClientLibraries.md): A fejlett funkciókhoz a fejlesztők letöltik a Microsoft Speech Client kódtárait, és az alkalmazásaikat is használhatják.  Az ügyféloldali kódtárak különböző platformokon (Windows, Android, iOS) érhetők el különböző nyelvekenC#(, Java, JavaScript, ObjectiveC).

| Használati esetek | [REST API-k](GetStarted/GetStartedREST.md) | [Ügyféloldali kódtárak](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Rövid hangalapú hang konvertálása, például parancsok (hanghossz < 15 s) ideiglenes eredmények nélkül | Igen | Igen |
| Hosszú hang konvertálása (> 15 s) | Nem | Igen |
| Stream hang a kívánt ideiglenes eredményekkel | Nem | Igen |
| A hangról a LUIS használatával átalakított szöveg ismertetése | Nem | Igen |

 Ha a nyelv vagy a platform még nem rendelkezik SDK-val, a [protokoll dokumentációja](API-Reference-REST/websocketprotocol.md)alapján hozhatja létre saját implementációját.

## <a name="recognition-modes"></a>Felismerési módok

Háromféle felismerési mód létezik: `interactive`, `conversation`és `dictation`. A felismerési mód úgy állítja be a beszédfelismerést, hogy a felhasználók milyen valószínűséggel fognak beszélni. Válassza ki az alkalmazás megfelelő felismerési módját.

> [!NOTE]
> Az elismerési módok eltérő viselkedéssel rendelkezhetnek a REST protokollban, mint a WebSocket protokollban. A REST API például nem támogatja a folyamatos felismerést, még beszélgetési vagy diktálási módban is.
> [!NOTE]
> Ezek a módok akkor alkalmazhatók, ha közvetlenül a REST vagy a WebSocket protokollt használja. Az [ügyféloldali kódtárak](GetStarted/GetStartedClientLibraries.md) eltérő paramétereket használnak az elismerési mód megadásához. További információkért tekintse meg az Ön által választott ügyféloldali könyvtárat.

A Microsoft Speech Service csak egy felismerési kifejezés eredményét adja vissza az összes felismerési módhoz. Egyetlen Kimondás esetén a korlát 15 másodperc.

### <a name="interactive-mode"></a>Interaktív mód

A `interactive` módban a felhasználók rövid kérelmeket tesznek elérhetővé, és elvárják, hogy az alkalmazás egy adott műveletet hajtson végre.

Az alábbi jellemzők jellemzőek az interaktív üzemmódú alkalmazásokra:

- A felhasználók tudják, hogy egy géppel beszélnek, nem pedig egy másik embernek.
- Az alkalmazás felhasználói megtudják, mire szeretnének mondani, hogy mit szeretnének tenni az alkalmazásban.
- A hosszúságú kimondott szöveg jellemzően utolsó körülbelül 2-3 másodperc.

### <a name="conversation-mode"></a>Beszélgetési mód

A `conversation` módban a felhasználók emberi – emberi beszélgetést folytatnak.

A következő jellemzők jellemzőek a beszélgetési mód alkalmazásaira:

- A felhasználók tudják, hogy egy másik személlyel beszélgetnek.
- A beszédfelismerés azáltal javítja az emberi beszélgetéseket, hogy az egyik vagy mindkét résztvevő láthatja a beszélt szöveget.
- A felhasználók nem mindig tervezik meg, hogy mit szeretnének mondani.
- A felhasználók gyakran használják a szlenget és más informális beszédet.

### <a name="dictation-mode"></a>Diktálási mód

A `dictation` módban a felhasználók tovább hosszúságú kimondott szöveg az alkalmazásba további feldolgozás céljából.

A következő jellemzők jellemzőek a diktálási üzemmódú alkalmazásokra:

- A felhasználók tudják, hogy egy géppel beszélgetnek.
- A felhasználók a beszédfelismerés szövegének eredményét jelenítik meg.
- A felhasználók gyakran tervezik meg, hogy mit szeretnének mondani, és hogyan használják a formális nyelvet.
- A felhasználók az utolsó 5-8 másodpercben teljes mondatokat alkalmaznak.

> [!NOTE]
> A diktálási és a beszélgetési módokon a Microsoft Speech szolgáltatás nem ad vissza részleges eredményeket. A szolgáltatás Ehelyett a stabil kifejezés eredményét adja vissza a hangadatfolyamban található csend határai után. A Microsoft növelheti a beszédfelismerési protokollt, hogy javítsa a felhasználói élményt ezekben a folyamatos felismerési módokban.

## <a name="recognition-languages"></a>Felismerési nyelvek

Az *elismerés nyelve* határozza meg, hogy az alkalmazás felhasználója milyen nyelven beszél. Az *elismerés nyelvének* megadása a *nyelv* URL-lekérdezési paramétereként a kapcsolatban. A *Language* Query paraméter értéke a [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag)IETF nyelvi címkét használja **, és a** Speech Recognition API által támogatott nyelvek egyikének kell lennie. A beszédfelismerési szolgáltatás által támogatott nyelvek teljes listája megtalálható az oldal [támogatott nyelvein](API-Reference-REST/supportedlanguages.md).

A Microsoft Speech szolgáltatás visszautasítja az érvénytelen kapcsolatkérelmeket a `HTTP 400 Bad Request` válasz megjelenítésével. Egy érvénytelen kérelem:

- A nem tartalmaz *nyelvi* lekérdezési paraméter értékét.
- A nem megfelelően formázott *nyelvi* lekérdezési paramétert tartalmaz.
- A *nyelvi* lekérdezési paramétert tartalmaz, amely nem a támogatási nyelvek egyike.

Dönthet úgy, hogy olyan alkalmazást hoz létre, amely támogatja a szolgáltatás által támogatott nyelvek egyikét vagy mindegyikét.

### <a name="example"></a>Példa

Az alábbi példában egy alkalmazás a beszédfelismerési *módot használja az* Egyesült államokbeli angol nyelvű hangszórókhoz.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Átírási válaszok

Az átírási válaszok visszaküldik a konvertált szöveget a hangról az ügyfeleknek. Az átírási válasz a következő mezőket tartalmazza:

- `RecognitionStatus`meghatározza az felismerés állapotát. A lehetséges értékeket az alábbi táblázatban találja.

| State | Leírás |
| ------------- | ---------------- |
| Siker | Az felismerés sikeres volt, és a szöveg mező jelen van |
| Nem egyező | Beszéd az audio-adatfolyam észlelt, de nincs szó azoktól a Célnyelv sem felel meg is. További részletek: [eltérő felismerési állapot (#nomatch-felismerés-állapot).  |
| InitialSilenceTimeout | A hangadatfolyam kezdete csak csendet foglalt, és a szolgáltatás a beszédfelismerésre való várakozás során időtúllépést várt |
| BabbleTimeout | A hangadatfolyam elején csak a zaj szerepel, és a szolgáltatás a beszédfelismerésre való várakozás során időtúllépést okozott. |
| Hiba | A felismerési szolgáltatás belső hibát észlelt, és nem folytatható |

- `DisplayText`a felismert mondatot jelöli a nagybetűssé tétel, a központozás és az inverz szöveg-normalizálás után, és a káromkodás maszkolása csillagokkal van ellátva. A szöveg mező *csak* akkor jelenik meg, `RecognitionStatus` ha a mező értéke `Success`.

- `Offset`meghatározza azt az eltolást (100-ns egységben), amelynél a kifejezés felismerhető, a hangadatfolyam elejéhez képest.

- `Duration`Megadja a jelen Speech kifejezés időtartamát (100 ns egységben).

Az átírási válasz további információkat ad vissza, ha szükséges. A részletes kimenetek visszaküldéséhez tekintse meg a [kimeneti formátumot](#output-format) .

A Microsoft Speech Service támogatja a további átírási folyamatot, amely magában foglalja a nagybetűk és a központozás hozzáadását, a káromkodás maszkolását és a szövegek gyakori űrlapokra való normalizálása. Ha például egy felhasználó a "emlékeztessen hat iPhone-ra" kifejezéssel jelölt kifejezést jelöl, a Microsoft Speech Services visszaküldi az átmásolt szöveget "emlékeztessen a 6 iPhone megvásárlására". A "hat" szót a "6" értékre konvertáló folyamat *inverz szöveg-normalizálás* (röviden*ITN* ).

### <a name="nomatch-recognition-status"></a>Nem egyező felismerési állapot

Az átírási `NoMatch` válasz `RecognitionStatus` abban az esetekben tér vissza, amikor a Microsoft Speech szolgáltatás észleli a hangadatfolyamban található beszédet, de nem tudja egyeztetni a kéréshez használt nyelvi nyelvtant. Előfordulhat például, hogy egy nem *megfelelő* feltétel akkor fordulhat elő, ha a felhasználó német nyelven mond valamit, ha a felismerő a beszélt nyelvként az angol nyelvet keresi. A Kimondás hullámforma-mintázata az emberi beszéd jelenlétét jelzi, de a felismert szavak egyike sem felel meg a felismerő által használt amerikai angol nyelvű lexikonnak.

Egy másik nem *egyező* állapot akkor fordul elő, ha az felismerő algoritmus nem talál pontos egyezést a hangadatfolyamban található hangok számára. Ha ez a feltétel történik, a Microsoft Speech szolgáltatás olyan *beszédet okozhat. hipotézisek* , amelyekben a rendszer *feltételezi a szöveget* , de létrehoz egy *beszédet. a kifejezés* olyan üzenetet tartalmaz, amelyben a *RecognitionStatus* nem *egyezik* . Ez az állapot normális; a *Speech. hipotézis* üzenetben nem lehet feltételezni a szöveg pontosságát vagy hűségét. Emellett nem feltételezheti azt, hogy a Microsoft Speech szolgáltatás a *Speech. hipotézisek* üzeneteit állítja elő, hogy a szolgáltatás képes legyen *beszédet létrehozni.* a *RecognitionStatus* *sikerességét*jelző üzenet jelenik meg.

## <a name="output-format"></a>Kimeneti formátum

A Microsoft Speech Service számos adattartalom-formátumot képes visszaadni az átírási válaszokban. Minden hasznos adat JSON-struktúra.

Az `format` URL-cím lekérdezési paraméterének megadásával szabályozhatja a kifejezés eredményének formátumát. Alapértelmezés szerint a szolgáltatás az eredményeket `simple` adja vissza.

| Formátum | Leírás |
|-----|-----|
| `simple` | Egy egyszerűsített kifejezés eredménye, amely tartalmazza az felismerési állapotot és a megjelenített szöveget a megjelenítési űrlapon. |
| `detailed` | A kifejezés eredményének felismerési állapota és N-legjobb listája, ahol minden kifejezés eredménye mind a négy felismerési űrlapot és egy megbízhatósági pontszámot tartalmaz. |

A `detailed` formátum [N-legjobb értékeket](#n-best-values)tartalmaz `RecognitionStatus`, `Offset`a és a, valamint `duration`a válaszában.

### <a name="n-best-values"></a>N – a legjobb értékek

A figyelők, akár emberi, akár a gép, soha nem biztos benne, hogy *pontosan* mit hallottak. Egy figyelő csak a teljes érték egy adott értelmezéséhez rendelhet *valószínűséget* .

Normális körülmények között, amikor másokkal beszélgetnek, akikkel gyakran kommunikálnak, a személyek nagy valószínűséggel azonosítják a beszélt szavakat. A gépi alapú beszédek figyelői a hasonló pontossági szintek elérésére törekednek, és a megfelelő feltételek mellett az [emberek paritást érnek el](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

A beszédfelismerés során használt algoritmusok a normál feldolgozás részeként megvizsgálják a teljes kiértékelés alternatív értelmezését. Ezeket az alternatívákat általában elveti a rendszer, mivel az egyetlen értelmezés előnyben részesített bizonyítéka egyre fontosabbá válik. Az optimális körülmények között azonban a beszédfelismerés a lehetséges alternatív értelmezések listájával fejeződik be. A lista legfontosabb *n* alternatíváit az *n-Best listának*nevezzük. Minden alternatíva [megbízhatósági pontszámot](#confidence)kap. A megbízhatósági pontszámok 0 és 1 közé esnek. Az 1 pontszám a legmagasabb szintű megbízhatóságot jelenti. A 0 pontszám a legalacsonyabb megbízhatósági szintet jelenti.

> [!NOTE]
> Az N-legjobb lista bejegyzéseinek száma több hosszúságú kimondott szöveg is változhat. A bejegyzések száma az *azonos* Kimondás több felismerésében is változhat. Ez a variáció a beszédfelismerési algoritmus valószínűségi természetének természetes és várt eredménye.

Minden, az N-legjobb listán visszaadott bejegyzés tartalmazza a következőt:

- `Confidence`, amely a bejegyzés [megbízhatósági pontszámait](#confidence) jelöli.
- `Lexical`, amely a felismert szöveg [lexikális formája](#lexical-form) .
- `ITN`, amely a felismert szöveg [ITN formája](#itn-form) .
- `MaskedITN`, amely a felismert szöveg [MASZKOLT ITN formája](#masked-itn-form) .
- `Display`, amely a felismert szöveg [megjelenítési formája](#display-form) .

### Megbízhatósági pontszámok<a id="confidence"></a>

A megbízhatósági pontszámok a beszédfelismerési rendszerek szerves részét képezik. A Microsoft Speech szolgáltatás megbízhatósági mutatókat szerez be a *megbízhatósági besorolásból*. A Microsoft a megbízhatósági besorolást olyan funkciók összessége szerint állítja be, amelyek a helyes és a helytelen felismerés közötti maximális megkülönböztetéshez lettek kialakítva. A megbízhatósági pontszámok kiértékelése az egyes szavak és a teljes hosszúságú kimondott szöveg esetében történik.

Ha úgy dönt, hogy a szolgáltatás által visszaadott megbízhatósági pontszámokat használja, vegye figyelembe a következő viselkedést:

- A megbízhatósági pontszámok csak ugyanazon felismerési módban és nyelven hasonlíthatók össze. Ne hasonlítsa össze a pontszámokat a különböző nyelvek vagy a különböző felismerési módok között. Az interaktív felismerési mód megbízhatósági pontszáma például *nem* tartalmaz korrelációt a diktálási módban lévő megbízhatósági pontszámhoz.
- A megbízhatósági pontszámokat a rendszer a hosszúságú kimondott szöveg korlátozott készletében használja legjobban. A hosszúságú kimondott szöveg nagy készlete esetében a pontszámok természetesen nagy mértékben változékonyságot eredményeznek.

Ha úgy dönt, hogy a megbízhatósági pontszám értékét az alkalmazás által használt *küszöbértékként* használja, a beszédfelismerés használatával állapítsa meg a küszöbértékeket.

- Beszédfelismerés végrehajtása az alkalmazáshoz tartozó hosszúságú kimondott szöveg reprezentatív mintáján.
- Gyűjtsön minden egyes felismerés megbízhatósági pontszámát a mintavételi készletben.
- Adja meg a küszöbértéket az adott minta megbízhatóságának bizonyos százalékában.

Egyetlen küszöbérték sem felel meg az összes alkalmazásnak. Előfordulhat, hogy egy alkalmazás elfogadható megbízhatósági pontszáma nem fogadható el egy másik alkalmazás számára.

### <a name="lexical-form"></a>lexikális űrlap

A lexikális elem a felismert szöveg, amely pontosan azt mutatja be, hogy milyen módon történt, és nincs írásjel vagy kapitalizáció. Például a "1020 Enterprise Way" címen található lexikális formában a következő lenne: *10 20 Enterprise Way*, feltéve, hogy ez így volt. A mondat lexikális formája: "emlékeztessen 5 ceruzát vásárolni", *emlékeztessen öt ceruza megvásárlására*.

A lexikális formátum a legmegfelelőbb olyan alkalmazások esetében, amelyek nem szabványos szöveg-normalizálás végrehajtásához szükségesek. A lexikális űrlap olyan alkalmazások esetében is megfelelő, amelyeknek feldolgozatlan felismerési szavakat kell megadniuk.

A profán soha nem fedi le a lexikális alakot.

### <a name="itn-form"></a>ITN űrlap

A szöveg normalizálása az a folyamat, amelynek során a szöveg konvertálása az egyik űrlapról egy másik "kanonikus" űrlapra történik. Előfordulhat például, hogy a "555-1212" telefonszám a *5 5 5 1 2 1 2*kanonikus formára lett konvertálva. Az *inverz* szöveg normalizálása (ITN) megfordítja ezt a folyamatot, és átalakítja a "5 5 5 1 2 1 2" szöveget a fordított kanonikus formátumba *555-1212*. A felismerési eredmény ITN formája nem tartalmazza a nagybetűket vagy a központozást.

A ITN űrlap a felismert szövegben szereplő alkalmazások esetében a legmegfelelőbb. Például egy olyan alkalmazás, amely lehetővé teszi a felhasználók számára, hogy a keresési kifejezéseket használják, majd ezeket a kifejezéseket egy webes lekérdezésben használja, használja a ITN űrlapot. A káromkodás soha nem szerepel a ITN űrlapon. A káromkodás maszkolásához használja a *MASZKOLT ITN űrlapot*.

### <a name="masked-itn-form"></a>Maszkolt ITN űrlap

Mivel a káromkodás természetesen a beszélt nyelv részét képezi, a Microsoft Speech szolgáltatás a szóbeli szavakat és kifejezéseket ismeri fel. A káromkodás azonban nem feltétlenül megfelelő minden alkalmazáshoz, különösen azokhoz az alkalmazásokhoz, amelyek korlátozott, nem felnőtt felhasználói közönséggel rendelkeznek.

A maszkolt ITN űrlap a káromkodás maszkolását alkalmazza az inverz szöveg normalizálása űrlapra. A káromkodás maszkolásához állítsa a profán paraméter értékének `masked`értékét a következőre:. A káromkodás maszkolásakor a rendszer a nyelv káromkodási lexikonának részeként felismert szavakat csillagokkal helyettesíti. Például: *emlékeztessen 5 * * * * * ceruza megvásárlására*. A felismerési eredmények maszkolt ITN formája nem tartalmazza a nagybetűket vagy a központozást.

> [!NOTE]
> Ha a káromkodás lekérdezési paramétere értékre van állítva `raw`, a maszkolt ITN űrlap ugyanaz, mint a ITN űrlap. A káromkodás *nem* maszkolt.

### <a name="display-form"></a>Megjelenítési űrlap

A központozás és a nagybetűk kihasználása a hangsúlyt, a szüneteltetést és így tovább, ami megkönnyíti a szöveg megértését. A megjelenítési űrlap írásjeleket és nagybetűket jelenít meg az eredmények felismeréséhez, így a legmegfelelőbb formában jeleníti meg a szóbeli szöveget megjelenítő alkalmazásokat.

Mivel a megjelenítési űrlap kiterjeszti a maszkolt ITN űrlapot, beállíthatja a káromkodás paraméter értékét `masked` vagy `raw`értéket. Ha a érték van beállítva `raw`, az elismerés eredményei tartalmazzák a felhasználó által beszélt összes trágár értéket. Ha a érték értéke `masked`, a rendszer a nyelv káromkodási lexikonának részeként felismert szavakat csillagokkal helyettesíti.

### <a name="sample-responses"></a>Minta válaszok

Minden hasznos adat JSON-struktúra.

A `simple` kifejezés eredményének hasznos formátuma:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

A `detailed` kifejezés eredményének hasznos formátuma:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Káromkodás – a beszédfelismerés feldolgozása

A Microsoft Speech Service felismeri az emberi beszéd összes formáját, beleértve azokat a szavakat és kifejezéseket, amelyeket sok ember "káromkodásnak" minősít. Megadhatja, hogy a szolgáltatás hogyan kezelje a káromkodást a *káromkodás* lekérdezési paraméterének használatával. Alapértelmezés szerint a szolgáltatás elrejti a káromkodást a *beszédben. a kifejezés* eredményét, és nem ad vissza *beszédet.* a káromkodást tartalmazó hipotézisek üzeneteit.

| *Káromkodás* értéke | Leírás |
| - | - |
| `masked` | Maszkok káromkodása csillagokkal. Ez az alapértelmezett viselkedés. |
| `removed` | Eltávolítja a káromkodást az összes eredményből. |
| `raw` | Felismeri és visszaadja a káromkodást az összes eredményben. |

### <a name="profanity-value-masked"></a>Káromkodás értéke`Masked`

A káromkodás maszkolásához állítsa a *káromkodás* lekérdezési paraméterét a *maszkolt*értékre. Ha a *trágár* lekérdezési paraméter értéke vagy nincs megadva egy kérelemhez, a szolgáltatás *elrejti* a káromkodást. A szolgáltatás maszkolást végez azáltal, hogy a felismerési eredményekben csillagokkal helyettesíti a káromkodást. Ha a káromkodás-maszkolási kezelést megadja, a szolgáltatás nem ad vissza *beszédet.* a rendszer a káromkodást tartalmazó hipotézisi üzeneteket tartalmazza.

### <a name="profanity-value-removed"></a>Káromkodás értéke`Removed`

Ha a *káromkodás* lekérdezési paramétere *el lett távolítva*, a szolgáltatás eltávolítja a káromkodást mind a *Speech. kifejezésből* , mind a *Speech. hipotézis* üzenetből. Az eredmények ugyanazok *, mint ha a trágár szavakat nem beszélték*.

#### <a name="profanity-only-utterances"></a>Csak trágár hosszúságú kimondott szöveg

A felhasználók *csak* a káromkodást jelezhetik, ha egy alkalmazás úgy konfigurálta a szolgáltatást, hogy eltávolítsa a káromkodást. Ebben a forgatókönyvben, ha a felismerési mód *diktálás* vagy *beszélgetés*, a szolgáltatás nem ad vissza *beszédet. eredmény*. Ha az felismerési mód *interaktív*, a szolgáltatás egy *beszédet* ad vissza. az eredmény az állapotkód szerint *eltér.*

### <a name="profanity-value-raw"></a>Káromkodás értéke`Raw`

Ha a *trágár* lekérdezési paraméter értéke *RAW*, a szolgáltatás nem távolítja el a káromkodást a *Speech. mondat* vagy a *Speech. hipotézis* üzeneteiben.
