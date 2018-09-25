---
title: A Bing Speech fogalmak |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Microsoft Speech Service-ben használt alapvető fogalmait.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 08790d73dd4fd182d8129c755a291fd99b6136a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954109"
---
# <a name="basic-concepts"></a>Alapfogalmak

Ezen a lapon a Microsoft speech recognition szolgáltatás néhány alapvető fogalmait ismerteti. Azt javasoljuk, hogy az alkalmazás a Microsoft beszédfelismerési API használata előtt olvassa el a ezen a lapon.

## <a name="understanding-speech-recognition"></a>A beszédfelismerés ismertetése

Ha első alkalommal hoz létre egy beszéd-kompatibilis alkalmazás, vagy ha első alkalommal adja hozzá a beszédszolgáltatások egy meglévő alkalmazáshoz, ez a szakasz segítséget nyújt első lépései. Ha már rendelkezik némi tapasztalattal a beszédfeldolgozó alkalmazásokat, dönthet úgy, hogy ez a szakasz csak skim, vagy teljesen, ha egy régi aktuális, beszédfelismerési és protokoll adatait eredményes szeretné kihagyhatja.

### <a name="audio-streams"></a>Audio-adatfolyamok

Legelső az alapvető fogalmait, beszéd között van a *audio-adatfolyam*. Ellentétben az egy kattintással, egy ponton időpontban következik be, és a egy darab információkat tartalmaz, amely használja a beszélt kérelem száz között oszlik, és számos kilobájt információkat tartalmaz. Használja a beszélt utterances időtartama nehézségekbe megadja a fejlesztők számára szeretne biztosítani az alkalmazásokban egyszerű és elegáns speech élményt biztosítani. A mai számítógépek és algoritmusok hajtsa végre lejegyzés az utterance (kifejezés), időtartama körülbelül felét egy 2 másodperces utterance (kifejezés) is lehet megjelenített érzéseket, körülbelül 1 másodperc, azonban minden alkalmazás, amely során a feldolgozó felhasználó 1 másodperces késleltetés lép nem egyszerű és elegáns.

Szerencsére a módon a "elrejtése" beszédátírási idő szerint beszédátírási végez az utterance (kifejezés) egy részét, amíg a felhasználó egy másik elnököt. Például egy 1 másodperces utterance (kifejezés) adattömbökbe 10 ezredmásodperc 100 felosztásával és elvégzésével beszédátírási az egyes adattömbök viszont, több mint 450 beszédátírási szükséges teljes 500 idő ezredmásodpercben is "rejtett" úgy, hogy a felhasználó tudomása beszédátírási van miközben hallgatója elnököt végzi. Során szem előtt tartva ebben a példában ne feledje, hogy a szolgáltatás működik-e beszédátírási az előző 100 ezredmásodpercben hang adja meg, amíg a felhasználó a következő 100 elnököt, így ha a felhasználó leállítja a beszéd, a szolgáltatás csak van lefényképezze körülbelül 100 az eredmény hang ezredmásodperc.

A felhasználói élmény eléréséhez használja a beszélt hang adatokat tömbökben gyűjti, és megjelenített érzéseket, ahogy a felhasználó ad elő. Ezek együttesen a hang adattömböket a *audio-adatfolyam*, és ezek hang adattömbök küld a szolgáltatásnak folyamatán nevezzük *hang streamelési.* Hang streamelési bármely beszédfeldolgozó alkalmazás; fontos részét képezi az adatrészlet méretének finomhangolásához és optimalizáláshoz streamelési végrehajtása néhány a legnagyobb hatású módon az alkalmazás felhasználói élmény javítása érdekében.

### <a name="microphones"></a>Mikrofonok

Személyek feldolgozni a beszélt hangot képes használja a füleket, de inanimate hardver által használt mikrofon. Engedélyezi a beszédfelismerési bármely alkalmazásban, meg kell integrációt biztosít az audio-adatfolyamot az alkalmazás a mikrofon.

Az API-k a mikrofon lehetővé kell tennie, hogy elindíthatja és leállíthatja a mikrofon hang bájtok fogadása. Döntse el, milyen felhasználói műveletek indítja a mikrofon megkezdeni a figyelést a beszédfelismerés kell. Dönthet úgy, hogy rendelkezik egy gomb megnyomása figyelés kezdete aktiválhat, vagy dönthet úgy, hogy rendelkezik egy *égbe tör* vagy *word ébresztési* spotter mindig figyeli a mikrofon és használatára, hogy a modul kimenete a Microsoft Speech Service küldés hang aktiválása.

### <a name="end-of-speech"></a>Beszéd vége

Észlelés *amikor* beszélő rendelkezik *leállt* , és beszéljen az emberek elég egyszerű úgy tűnik, de kívül laboratóriumi feltételek meglehetősen nehéz probléma. Nem áll elég egyszerűen a keresett tiszta csend után az utterance (kifejezés), mert gyakran sok dolog nehezíti a zajszint. A Microsoft beszédfelismerési szolgáltatás hajtja végre, amikor a felhasználó már le van állítva, és beszéljen, és a szolgáltatás tájékoztathatja a tényt, az alkalmazás, de ezzel az elrendezéssel fokozott jelenti azt, hogy az alkalmazás az utolsó tudni, hogy amikor a felhasználó állítsa le, és beszéljen gyors észlelése egy kiváló feladat. Ez nem minden más bemeneti formája például az alkalmazás esetén a *első* tudni, hogy ha a felhasználói bevitel kezdődik *és* ér véget.

### <a name="asynchronous-service-responses"></a>A szolgáltatás aszinkron válaszok

A tény, hogy az alkalmazása szükséges, hogy értesüljön az, ha befejeződött a felhasználói bevitel nem ír elő, bármely teljesítmény ennek elmulasztása vagy az alkalmazás programozási nehézségeket, de igényel, és a megadott kérés a beszédalapú kéréseket gondolja / válasz minták, amellyel ismeri. Az alkalmazás nem tudja, ha a felhasználó leállítja a beszéd, mivel az alkalmazás továbbra is a szolgáltatás az audio adatfolyam párhuzamosan és aszinkron módon történik a szolgáltatás válaszára történő várakozás során. Ez a minta akkor ellentétben más HTTP kérés/válasz webes protokollokkal. Ezeket a protokollokat, a kérelem előtt teljesítendő; válasz fogadása a Microsoft Speech Service protokoll válaszokat kaphatnak *közben továbbra is a kéréshez hang folyamatos átviteli*.

> [!NOTE]
> Ez a funkció nem támogatott a Speech HTTP REST API használata esetén.

### <a name="turns"></a>Ennek a

Beszéd egy szolgáltató információk. Amikor beszél, kívánt információ, amely az Öné, aki figyel, ez az információ átadásához. Küldött értesítésben információkat, általában ennek a beszéd, a figyelő igénybe vehet. Hasonlóképpen a beszéd-kompatibilis alkalmazás kommunikál felhasználók azt is megteheti figyeli és reagál, bár az alkalmazás általában teszi a figyelő a legtöbb. A felhasználó használja a beszélt bemeneti és a szolgáltatás válasza a bemeneti nevezzük egy *kapcsolja*. A *kapcsolja* elindul, amikor a felhasználó ad elő és ér véget, amikor az alkalmazás a beszédfelismerési szolgáltatás válasza kezelése befejeződött.

### <a name="telemetry"></a>Telemetria

Beszédfelismerő eszköz vagy alkalmazás létrehozása kihívást jelenthet, akár a tapasztalt fejlesztők számára. Stream-alapú protokollokat, gyakran úgy tűnik, még ha ez ijesztően első ránézésre, és fontos részleteket, a csend észlelési lehet teljesen új. A sok üzenetek sikeresen küldött és fogadott kellene egy egyetlen kérés-válasz párosai végrehajtásához, *nagyon* fontos, hogy ezeket az üzeneteket teljes és pontos adatok gyűjtése. A Microsoft Speech Service protokoll biztosítja az adatok gyűjtését. Meg kell minden erőfeszítést; lehető legpontosabban adja meg a szükséges adatok teljes és pontos adatok megadásával, akkor lesz kell útmutatás nyújtása a saját maga – minden eddiginél szüksége van segítségre a Microsoft Speech Service csapatának ügyfél megvalósítása a hibaelhárítás, a telemetriai adatokat összegyűjtötte minőségét kritikus probléma lesz elemzés.

> [!NOTE]
> Ez a funkció nem támogatott a beszédfelismerés REST API használata esetén.

### <a name="speech-application-states"></a>Beszéd alkalmazás állapotok

A lépéseket, ahhoz, hogy az alkalmazás bemeneti kicsit más, mint a többi formáját bemeneti lépései, például az egérrel rákattint vagy rákoppint ujját. Meg kell nyomon követheti, ha az alkalmazás a mikrofonhoz figyeli, és adatokat küldeni a speech service, amikor vár választ a szolgáltatástól, és a egy inaktív állapotban van. Ezeket az állapotokat az közötti kapcsolatot az alábbi ábrán látható.

![Beszéd alkalmazásállapot-Diagram](Images/speech-application-state-diagram.png)

A Microsoft Speech Service részt vesz az egyes állapotai, mivel a szolgáltatás protokoll, amely segít az alkalmazás átmeneti állapotok közötti üzenetek határozza meg. Az alkalmazásnak kell értelmezni, és az ezek protokoll üzenetek nyomon követése, és kezelheti a speech alkalmazás állapotok.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>A speech recognition service az alkalmazások használata

Microsoft speech recognition szolgáltatás a fejlesztők a beszédfelismerési hozzá alkalmazásaikat kétféle módszert biztosít.

- [REST API-k](GetStarted/GetStartedREST.md): a fejlesztők a beszédfelismerési a szolgáltatáshoz az alkalmazások HTTP-hívások.
- [Ügyfélkódtárak](GetStarted/GetStartedClientLibraries.md): speciális funkciók fejlesztők is töltse le a Microsoft Speech-ügyfélkódtárak és alkalmazásokba hivatkozásra.  A klienskódtárak érhetők el a különböző platformokon (Windows, Android, iOS) különböző nyelvekhez (C#, Java, JavaScript, ObjectiveC) használatával.

| Használati esetek | [REST API-k](GetStarted/GetStartedREST.md) | [Ügyfélkódtárak](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konvertálás egy rövid beszélt hangot képes, például parancsokat (hang hossza < 15 mp) ideiglenes eredmények nélkül | Igen | Igen |
| Konvertálja a hosszú hang (> 15 % s) | Nem | Igen |
| A köztes eredményeket kívánt Stream hang | Nem | Igen |
| A LUIS használatával hang tartalomtárolási szöveg ismertetése | Nem | Igen |

 Ha Ön nyelvéhez vagy platformjához még nem rendelkezik egy SDK-t, a saját implementációjához alapján hozhat létre a [protokoll dokumentációja](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Elismerés módok

Elismerés három módot: `interactive`, `conversation`, és `dictation`. A beszédfelismerést mód beszédfelismerés hogyan a felhasználók valószínűleg beszéd alapján állítja be. Válassza ki a megfelelő mód az alkalmazáshoz.

> [!NOTE]
> Elismerési módok esetleg tartalmaz másként a [REST-protokollon](#rest-speech-recognition-api) , mint az a [WebSocket protokoll](#webSocket-speech-recognition-api). Például a REST API nem támogatja folyamatos felismerés beszélgetés vagy Diktálás módban is.
> [!NOTE]
> Módokban alkalmazandók, ha közvetlenül a REST vagy a WebSocket protokoll használ. A [klienskódtárak](GetStarted/GetStartedClientLibraries.md) különböző paraméterek használatával adja meg a mód. További információkért tekintse meg az ügyféloldali kódtár a választott.

A Microsoft beszédfelismerési szolgáltatás minden felismerés mód csak egy felismerés kifejezés eredményét adja vissza. Minden olyan egyetlen utterance (kifejezés) a 15 másodperces korlátozva van.

### <a name="interactive-mode"></a>Interaktív mód

A `interactive` mód, a felhasználó rövid kérelmek segítségével, és vár választ egy műveletet az alkalmazás.

Interaktív mód kérelmek jellemzően a a következő jellemzőkkel:

- Felhasználók, hogy a gép és a egy másik emberi beszéd.
- Alkalmazás felhasználói számára, hogy időben szeretnének tegyük fel, alapján, amit szeretnének tenni az alkalmazást.
- Beszédmódok általában kapcsolatos legutóbbi 2-3 másodpercet.

### <a name="conversation-mode"></a>Beszélgetés mód

A `conversation` mód, felhasználók bízott emberi beszélgetésbe.

A következők jellemzően a beszélgetés módhoz készült alkalmazások a következő jellemzőkkel:

- Felhasználók tudják, hogy meghatalmazottjával áll egy másik személynek.
- A beszédfelismerés növeli az emberi beszélgetések azáltal, hogy legalább az egyik résztvevők a kimondott szöveg.
- Felhasználók nem mindig tervezi tegyük fel, hogy szeretnének.
- A felhasználók gyakran a szleng és az egyéb informális speech használhatják.

### <a name="dictation-mode"></a>Diktálás

A `dictation` mód, felhasználók álmából hosszabb kimondott szöveg az alkalmazás további feldolgozás céljából.

A következők jellemzően a Diktálás módhoz készült alkalmazások a következő jellemzőkkel:

- Felhasználók tudják, hogy meghatalmazottjával áll egy géphez.
- Felhasználók jelennek meg a speech recognition szöveges eredményt.
- Felhasználók gyakran megtervezése, amit szeretnének tegyük fel, és a formális nyelvet használja.
- Felhasználók alkalmaz teljes sentences, amely elmúlt 5 – 8 másodpercben.

> [!NOTE]
> Diktálás és beszélgetés módban a Microsoft beszédfelismerési szolgáltatás nem ad vissza részleges eredményeket. Ehelyett a szolgáltatás az audio-adatfolyamot csend határokon után állandó kifejezés eredményeket ad vissza. A Microsoft fokozott előfordulhat, hogy ezek folyamatos felismerés módban a felhasználói élmény javítása érdekében a speech protokollt.

## <a name="recognition-languages"></a>Nyelv felismerése

A *beszédfelismerési nyelv* meghatározza, hogy az alkalmazás felhasználói beszél nyelvét. Adja meg a *beszédfelismerési nyelv* együtt a *nyelvi* URL-cím lekérdezési paraméter a kapcsolat. Értékét a *nyelvi* lekérdezési paramétert használja az IETF nyelvcímkét [BCP-47](https://en.wikipedia.org/wiki/IETF_language_tag), és **kell** beszédfelismerő API által támogatott nyelvek valamelyikével kell. A beszédfelismerési szolgáltatás által támogatott nyelvek teljes listáját a lapon található [támogatott nyelvek](API-Reference-REST/supportedlanguages.md).

A Microsoft beszédfelismerési szolgáltatás visszautasítja az érvénytelen kapcsolati kérelem megjelenítésével egy `HTTP 400 Bad Request` választ. Érvénytelen kérést egyike, amelyek:

- Nem tartalmazza a *nyelvi* lekérdezési paraméter értéke.
- Tartalmaz egy *nyelvi* lekérdezési paraméter, amely nem megfelelően van formázva.
- Tartalmaz egy *nyelvi* lekérdezési paraméter, amely nem egy olyan támogatása.

Dönthet úgy hozhat létre egy alkalmazást, amely egy, vagy a szolgáltatás által támogatott nyelveket támogatja.

### <a name="example"></a>Példa

Az alkalmazás használja a következő példában *beszélgetés* beszédfelismerési mód az Amerikai angol nyelvű beszélő.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Beszédátírási válaszok

Az átírási válaszok a felismert szöveget visszaadása az ügyfeleknek a hang. Beszédátírási választ a következő mezőket tartalmazzák:

- `RecognitionStatus` a beszédfelismerést állapotát határozza meg. A lehetséges értékeket az alábbi táblázat a.

| status | Leírás |
| ------------- | ---------------- |
| Sikeres | A beszédfelismerést sikeres volt, és a megjelenő mezőben szerepel. |
| NoMatch | Beszéd az audio-adatfolyam észlelt, de nincs szó azoktól a Célnyelv sem felel meg is. [NoMatch felismerés Status(#nomatch-recognition-status) további részletekért lásd:  |
| InitialSilenceTimeout | A hang stream elején szereplő csak csend, és a szolgáltatás, beszédfelismerés várakozás túllépte az időkorlátot |
| BabbleTimeout | A hang stream elején szereplő csak zaj, és a szolgáltatás, beszédfelismerés várakozás túllépte az időkorlátot |
| Hiba | A beszédfelismerést szolgáltatás belső hibába ütközött, és nem lehetett folytatni |

- `DisplayText` a felismert kifejezés jelöli, kis-és nagybetűk, írásjelek és Inverz – szöveg-normalizálási alkalmazása és cenzúrázása maszkolva csillagok után. A megjelenő mezőben szerepel *csak* Ha a `RecognitionStatus` mező értéke `Success`.

- `Offset` eltolás (a 100 nanoszekundumos egységek), amellyel kifejezés ismerhető, a hang stream elején viszonyítva.

- `Duration`Megadja a időtartama (100 nanoszekundumos egységek), a beszéd kifejezés helyett szerepel.

Beszédátírási választ további információkat ad vissza, ha szükséges. Lásd: [kimeneti formátum](#output-format) , hogy hogyan kimenetek részletesebb visszaadása.

Microsoft Speech Service további beszédátírási folyamat, amely tartalmazza a kis-és nagybetűk és írásjelek, támogatja a maszkolási cenzúrázása és normalizálása közös képernyők szöveg. Például ha egy felhasználó által képviselt kifejezés beszél a szavak "Emlékeztessen megvásárlása hat iPhone-OK", a Microsoft beszédszolgáltatások visszatér az átírt szöveg "Emlékeztessen megvásárlása 6 iPhone-okon." A folyamat, amely a "hat" szót alakítja át a számot, "6" néven ismert *Inverz szöveg normalizálási* (*fel* röviden).

### <a name="nomatch-recognition-status"></a>NoMatch felismerés állapota

Az átírási választ adja vissza `NoMatch` a `RecognitionStatus` Ha a Microsoft Speech Service speech észleli az audio-adatfolyamot, de nem felel meg, hogy a kéréshez használt nyelv nyelvtani beszédfelismerés. Ha például egy *NoMatch* az állapot akkor fordulhat elő, ha egy felhasználó valami német felirat, amikor a felismerő amerikai angolra használja a beszélt nyelv vár. Hullám mintát követik az utterance (kifejezés) lenne az emberi speech meglétének jelzéséhez, de egyike a kimondott szavakat akkor sem felel meg az Egyesült Államok angol nyelvű lexikális a felismerő használja.

Egy másik *NoMatch* állapot akkor fordul elő, amikor a felismerés algoritmus nem tudja a hangok az audio-adatfolyamot szereplő pontos egyezést. Ez az állapot akkor fordul elő, amikor a Microsoft Speech Service eredményezhet *speech.hypothesis* tartalmazó üzeneteket *feltételezett átlagos szöveg* eredményez, de egy *speech.phrase*üzenet, amelyben a *RecognitionStatus* van *NoMatch*. Ez az állapot akkor normál; nem győződjön meg a pontosság vagy a minőség szövegének feltételezésekre a *speech.hypothesis* üzenet. Továbbá, hogy kell nem feltételezze, hogy a Microsoft Speech Service eredményez *speech.hypothesis* , amelyet a szolgáltatás képes előállítani egy *speech.phrase* üzenet  *RecognitionStatus* *sikeres*.

## <a name="output-format"></a>Kimeneti formátum

Microsoft Speech Service is adattartalom-formátumok különböző beszédátírási válaszok visszaadása. Az összes is észleltünk adattartalmakat. olyan struktúrák, JSON.

A kifejezés eredményformátum megadásával szabályozhatja a `format` URL-cím lekérdezési paraméter. Alapértelmezés szerint a szolgáltatás visszaadja `simple` eredményeket.

| Formátum | Leírás |
|-----|-----|
| `simple` | A beszédfelismerést állapota és a megjelenítési űrlap a felismert szöveget tartalmazó egyszerűsített kifejezés eredményt. |
| `detailed` | Egy felismerése állapotát, és ahol minden egyes kifejezés eredménye tartalmaz minden négy felismerés űrlap és a egy megbízhatósági pontszám kifejezés eredményeinek legjobb N listázhatja. |

A `detailed` tartalmaz [legjobb N értéket](#n-best-values), mellett `RecognitionStatus`, `Offset`, és `duration`, a válaszban.

### <a name="n-best-values"></a>Legjobb N értéket

Figyelők, akár emberi, akár gépi, soha nem lehet abban, hogy azok meghallgatni *pontosan* mi volt a beszélt. Egy figyelő is hozzárendelhet egy *valószínűségi* csak az egy adott értelmezése az utterance (kifejezés). 

Normál körülmények között, amikor mások számára, akikkel gyakran használják, és beszéljen a felhasználók nagy valószínűséggel ismeri fel lettek kimondott szavakat is. Számítógép-alapú beszédfelismerési figyelői arra törekszik, hogy hasonló pontossági szint elérése és a megfelelő feltételek mellett [emberek való elérésének](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

A beszédfelismerés használt algoritmusok Fedezze fel az alternatív értelmezések, az utterance (kifejezés) normál feldolgozása során. Általában ezek az alternatívák értéke egy egyetlen értelmezése bizonyítékok elsöprő válik, elveti. Nem optimális körülmények között azonban a beszédfelismerő befejezi az alternatív lehetséges értelmezések listáját. A felső *N* alternatíva a listában szereplő nevezzük a *legjobb N lista*. Minden egyes alternatív hozzá van rendelve egy [megbízhatósági pontszám](#confidence). Megbízhatósági 0 és 1 közé eső pontszámmodell. Egy 1 pontszám a legmagasabb szintű megbízhatóság jelöli. A pontszám a 0 a legalacsonyabb megbízhatósági szintjét jelöli.

> [!NOTE]
> A legjobb N listában bejegyzések száma több utterances eltérőek. A bejegyzések száma változhat a között, több felismerés a *ugyanazon* utterance (kifejezés). Ezt a különbséget a speech recognition algoritmus valószínűségi jellege természetes és várt eredményét.

A legjobb N listát adja vissza mindegyik bejegyzés tartalmazza

- `Confidence`, mely jelöli a [megbízhatósági pontszámok](#confidence) ez.
- `Lexical`, amely a [lexikai űrlap](#lexical-form) felismert szöveget.
- `ITN`, amely a [fel űrlap](#itn-form) felismert szöveget.
- `MaskedITN`, amely a [fel űrlap maszkolva](#masked-itn-form) felismert szöveget.
- `Display`, amely a [megjelenítési űrlap](#display-form) felismert szöveget.

### Megbízhatósági pontszámok <a id="confidence"></a>

Megbízhatósági pontszámok szerves speech recognition rendszerekhez. A Microsoft Speech Service szerzi be a megbízhatósági pontszámok egy *megbízhatósági osztályozó*. A Microsoft bizalom osztályozó betanítja olyan szolgáltatások készlete, amelyek célja, hogy a megfelelő és nem megfelelő felismerése standardként megkülönböztetést keresztül. Megbízhatósági pontszámok értékeli az egyes szavak és a teljes kimondott szöveg.

Ha a szolgáltatás által visszaadott megbízhatósági pontszámokat használatát választja, vegye figyelembe a következő viselkedés:

- Megbízhatósági pontszámok összehasonlíthatók csak az azonos mód és a nyelvi belül. Más nyelvű vagy különböző felismerő módok között pontszámok hasonlítja össze. Például egy megbízhatósági pontszám, az interaktív mód rendelkezik *nincs* végezni, a Diktálás megbízhatósági pontszámot.
- Megbízhatósági pontszámok legjobb szolgálnak korlátozott konfigurálásában a kimondott szöveg. Természetesen van a vonatkozó eredmények rengeteg utterances változékonyságát nagyszerű fokú.

Ha úgy dönt, hogy egy megbízhatósági pontszám értékként használni egy *küszöbérték* az alkalmazás működik, használja a beszédfelismerés létesíteni a küszöbértékeket.

- Hajtsa végre a beszédfelismerés egy reprezentatív mintát, az alkalmazás kimondott szöveg.
- A minta-készletben lévő minden egyes elismerési megbízhatósági pontszámokat összegyűjtése.
- A küszöbérték alapja magabiztosan a minta néhány PERCENTILIS.

Nincs egyetlen küszöbérték nem megfelelő összes alkalmazáshoz. Előfordulhat, hogy egy megfelelő megbízhatósági pontszám egy alkalmazáshoz egy másik alkalmazás fogadható el.

### <a name="lexical-form"></a>lexikális űrlap

A lexikális képernyő az a felismert szöveget, és pontosan hogyan történt az utterance (kifejezés) és nem absztrakt vagy a kis-és nagybetűk. Például a cím "1020 vállalati módon" lexikális formájában lenne *10 20 vállalati módon*, feltéve, hogy ezzel a módszerrel azt volt beszélt. A mondat formájában lexikai "Emlékeztessen megvásárlása 5 ceruza" van *Emlékeztessen öt ceruza megvásárlása*.

A lexikális képernyő az leginkább megfelelő, nem a szokásos szöveges normalizálási végrehajtásához igénylő alkalmazások esetében. A lexikális képernyő az szintén feldolgozatlan felismerés szavak igénylő alkalmazásokhoz megfelelő.

Káromkodás soha nem lexikai formájában van maszkolva.

### <a name="itn-form"></a>FEL űrlap

Szöveg normalizálási az a folyamat egy űrlap szöveg konvertálása egy másik "kanonikus" formátumú. Ha például a telefonszám "555-1212" Előfordulhat, hogy alakítható át a kanonikus formájában *öt öt öt egy két egy két*. *Más néven inverz* szöveg normalizálási (fel) visszavonja ezt a folyamatot, a szavakat konvertálása "öt öt öt egy két egy két" fordított kanonikus formájában *555-1212*. A felismerés eredményét fel formájában nem tartalmazza a kis-és nagybetűk vagy absztrakt.

A fel képernyő az leginkább megfelelő alkalmazásokat, amelyek a felismert szöveget cselekedhet. Például egy alkalmazás, amely lehetővé teszi a felhasználó mérnökeinkkel keresési kifejezéseket, majd ezeket a feltételeket a webes lekérdezésben használna fel formájában. Káromkodás soha nem fel formájában van maszkolva. Maszkolandó vulgáris, használja a *maszkolt fel űrlap*.

### <a name="masked-itn-form"></a>Maszkolt fel űrlap

Mivel cenzúrázása természetes módon használja a beszélt nyelv része, a Microsoft beszédfelismerési szolgáltatás érzékeli az ilyen szavak és kifejezések, amelyek beszélt. Káromkodás, azonban nem minden alkalmazás, különösen egy korlátozott, nem felnőtt felhasználói közönség számára az alkalmazások megfelelő.

A maszkolt fel képernyő adatmaszkolás segít Önnek az inverz szöveg normalizálási űrlap cenzúrázása vonatkozik. Maszkolandó vulgáris, állítsa az értékét a vulgáris paraméter értékének `masked`. Káromkodás maszkolva van, amikor a nyelv cenzúrázása lexikális részeként felismerhető szavak helyén csillagok. Például: *Emlékeztessen megvásárlása 5 x ceruza*. A maszkolt fel formájában a felismerés eredményét nem tartalmazza a kis-és nagybetűk vagy absztrakt.

> [!NOTE]
> Ha a vulgáris lekérdezési paraméter értéke értéke `raw`, a maszkolt fel űrlap pedig ugyanaz, mint az fel űrlap. Káromkodás van *nem* maszkolva.

### <a name="display-form"></a>Megjelenítési űrlap

Absztrakt, és a nagybetűk jelezze, ha a felfüggesztés, és így tovább, ami lehetővé teszi szöveg megérteni a hangsúlyt, hová kerüljön. A megjelenítési űrlap absztrakt, és a nagybetűk hozzá felismerési eredményeket, így a legmegfelelőbb módja az alkalmazásokat, amelyek a kimondott szöveg megjelenítéséhez.

A megjelenítési űrlap a maszkolt fel űrlapot terjeszti ki, mert a vulgáris paraméter értéke és megadható `masked` vagy `raw`. Ha az értéke `raw`, a felismerési eredményeket bármilyen közönséges kifejezést a felhasználó által felolvasott tartalmazza. Ha az értéke `masked`, csillagok ismeri fel a nyelvi cenzúrázása lexikális részeként szavak helyén.

### <a name="sample-responses"></a>Minta válaszok

Az összes is észleltünk adattartalmakat. olyan struktúrák, JSON.

Az adattartalom formátuma a `simple` kifejezés eredménye:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Az adattartalom formátuma a `detailed` kifejezés eredménye:

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

## <a name="profanity-handling-in-speech-recognition"></a>A beszédfelismerés cenzúrázása-kezelése

A Microsoft Speech Service felismeri az emberi beszéd átalakítás, beleértve a szavak és kifejezések, amelyek sok ember szeretné besorolni "cenzúrázása.", az összes formáját Hogyan a szolgáltatás elvégzi a vulgáris segítségével szabályozhatja a *cenzúrázása* lekérdezési paraméter. Alapértelmezés szerint a szolgáltatás maszkolja a vulgáris *speech.phrase* eredményeket, és nem ad vissza *speech.hypothesis* cenzúrázása tartalmazó üzeneteket.

| *Káromkodás* érték | Leírás |
| - | - |
| `masked` | Maszkok cenzúrázása szószűrő. Ez a viselkedés az alapértelmezett érték. | 
| `removed` | Káromkodás eltávolít minden eredmény. |
| `raw` | Felismeri és cenzúrázása összes eredményt adja vissza. |

### <a name="profanity-value-masked"></a>Káromkodás érték `Masked`

Maszkolandó vulgáris, állítsa be a *cenzúrázása* lekérdezési paraméter értéke a *maszkolva*. Ha a *cenzúrázása* lekérdezési paraméter értéke ezzel, vagy nincs megadva a kérelem, a szolgáltatás *maszkok* cenzúrázása. A szolgáltatás maszkolási cenzúrázása a felismerési eredményeket a szószűrő lecserélésével hajtja végre. Káromkodás-maszkolási kezelési megadása esetén a szolgáltatás nem ad vissza *speech.hypothesis* cenzúrázása tartalmazó üzeneteket.

### <a name="profanity-value-removed"></a>Káromkodás érték `Removed`

Ha a *cenzúrázása* lekérdezési paraméter értéke *eltávolított*, a szolgáltatás eltávolítja a vulgáris is *speech.phrase* és *speech.hypothesis* üzeneteket. Az eredmények ugyanazok *, mintha a TRÁGÁR szavakat nem voltak beszélt*.

#### <a name="profanity-only-utterances"></a>Csak cenzúrázása kimondott szöveg

A felhasználó előfordulhat, hogy beszéd *csak* cenzúrázása egy alkalmazást a szolgáltatás eltávolítja a vulgáris konfigurálásakor. Ebben a forgatókönyvben, ha a felismerés mód *Diktálás* vagy *beszélgetés*, a szolgáltatás nem ad vissza egy *speech.result*. Ha a felismerés mód *interaktív*, a szolgáltatás által visszaadott egy *speech.result* állapotkódú *NoMatch*. 

### <a name="profanity-value-raw"></a>Káromkodás érték `Raw`

Ha a *cenzúrázása* lekérdezési paraméter értéke *nyers*, a szolgáltatás nem távolítsa el, vagy akár cenzúrázása maszkolja a *speech.phrase* vagy  *Speech.hypothesis* üzeneteket.
