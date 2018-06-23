---
title: Fogalmak |} Microsoft Docs
description: A Microsoft beszéd szolgáltatáshoz használt alapvető fogalmait.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: bc23f4fb7dfc045a0f8cc87155c31875c4de8450
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347490"
---
# <a name="basic-concepts"></a>Alapfogalmak

Ezen a lapon Microsoft beszéd felismerési szolgáltatás néhány alapvető fogalmait ismerteti. Azt javasoljuk, hogy az alkalmazás Microsoft beszédfelismerés API használata előtt olvassa el a ezen a lapon.

## <a name="understanding-speech-recognition"></a>A beszédfelismerés ismertetése

Ha első alkalommal hoz létre, a beszéd-kompatibilis alkalmazások, vagy ha egy meglévő alkalmazás beszéd képességeket adja hozzá az első alkalommal, ez a szakasz segítséget nyújt első lépései. Ha már rendelkezik némi tapasztalattal a alkalmazások engedélyezését, csak az ebben a szakaszban skim választhatja, vagy teljesen, ha egy régi aktuális beszéd átalakítás, és le szeretné kérdezni a protokoll részletek jobb kihagyhatja.

### <a name="audio-streams"></a>Hang adatfolyamok

Legelső beszéd főbb fogalmait és kifejezéseit között van a *hangadatfolyam*. Ellentétben a billentyűlenyomást, pont időpontban következik be, és egyetlen információkat tartalmaz, amely egy szóbeli kérelem van elosztva, több száz ideje (MS), és sok kilobájt információkat tartalmazza. A szóbeli utterances időtartama néhány nehezen fejlesztők egy egyszerű, elegáns beszéd élményt biztosít az alkalmazás keresése mutatja be. A mai számítógépek és algoritmusok hajtsa végre beszéd írjanak elő körülbelül fele a utterance időtartama, a 2-második utterance is kért, körülbelül 1 másodperc múlva, de minden olyan alkalmazás, amely feldolgozó felhasználó 1 másodperc késés észlel zökkenőmentes sem elegáns.

Szerencsére módon "elrejtése" írjanak elő idő egy része a utterance érdekében végezzen írjanak elő a felhasználó egy másik van beszéd közben. Például egy 1 másodperc utterance 100 milliomod másodperc 10 több felosztásával és elvégzésével írjanak elő az egyes adattömbök pedig több mint a teljes 500 ideje (MS) vonatkozó szükséges is "rejtett" 450 úgy, hogy a felhasználó tudomása írjanak elő van végrehajtott többé van beszéd közben. Ha ebben a példában továbbléphetnek ne feledje, hogy a szolgáltatás működik írjanak elő a hang előző 100 milliomod másodperc, amíg a felhasználó a következő 100 van beszéd, ezért amikor a felhasználó abbahagyja beszéd, a szolgáltatás csak lesz átírni körülbelül 100 az eredmény hang ezredmásodperc.

A felhasználói élmény eléréséhez szóbeli hang információt adattömbök gyűjtött és a felhasználó beszél, kért. Ezek együttesen a hang adattömböket a *hangadatfolyam*, és elküldi a hang adattömböket a szolgáltatás folyamathoz *hang adatfolyam.* Hang streaming fontos része a beszéd-kompatibilis alkalmazások; az adatrészlet méretének beállítása és optimalizálása adatfolyam végrehajtása a legtöbb impactful módon az alkalmazás felhasználói élmény javítása közé tartoznak.

### <a name="microphones"></a>Mikrofonok

Személyek feldolgozni szóbeli hang használja a füleket, de inanimate hardver mikrofonok használ. Ahhoz, hogy a beszédfelismerés bármely alkalmazásban, az alkalmazás a hangadatfolyam biztosít a mikrofon integrálni kell.

Az API-kat a mikrofon kell teszik indítása és leállítása a mikrofon hang bájtok fogadása. Döntse el, hogy milyen felhasználói műveletek akkor indul el, a mikrofon megkezdeni a figyelést a beszédfelismerés kell. Választhatja a figyelés elindítása Indítás gomb megnyomása rendelkezik, vagy dönthet, hogy egy *kulcsszavakat* vagy *word ébresztés* spotter mindig figyeli a mikrofon és a kimeneti adott modul használatával a Microsoft beszéd szolgáltatás hang küldése eseményindító.

### <a name="end-of-speech"></a>Beszéd vége

Észlelése *amikor* egy hangalapú rendelkezik *leállt* beszéd az emberek elég egyszerű úgy tűnik, de kívül laboratóriumi feltételek ahelyett, hogy nehéz probléma. Nincs elég egyszerűen keres tiszta csend után egy utterance, hiszen gyakran dolgot nehezíti a zajszint számos. A Microsoft beszéd szolgáltatásnak nincs gyorsan észlelni, amikor a felhasználó leállította a beszéd, és a szolgáltatás megfelelően tájékoztatják a az alkalmazás a tényt, de ezzel az elrendezéssel jelenti azt, hogy az alkalmazás tudni, hogy amikor a felhasználó Beszéd leállítása-e az elmúlt egy kiváló feladat. Ez nem minden más bemeneti formában például az alkalmazás esetén a *első* tudni, hogy amikor a felhasználó elindítja bemeneti *és* karakterlánccal végződik-e.

### <a name="asynchronous-service-responses"></a>Aszinkron szolgáltatás válaszok

Az, hogy az alkalmazás kell tájékoztatni felhasználói bevitel befejezésekor nem ugyanazok bármely teljesítményveszteség vagy az alkalmazás programozási nehézségek merülnek fel, de legyen szükség, hogy úgy gondolja, hogy a bemeneti kérelemből másképp beszéd kérelmekkel kapcsolatos / válasz mintázatok, amellyel tisztában. Az alkalmazás nem fognak ismerni, amikor a felhasználó abbahagyja beszéd, mert az alkalmazás továbbra is a szolgáltatás hang adatfolyam egyidejűleg és aszinkron módon történik a szolgáltatás válaszára várakozás során. Ez a minta nem más HTTP kérelem-válasz webes protokollokkal. Ezeket a protokollokat el kell végeznie egy kérelem; válasz fogadása előtt a Microsoft beszéd szolgáltatás protokoll választ kap *közben továbbra is a kérelem hang folyamatos átviteli*.

> [!NOTE]
> Ez a funkció beszéd HTTP REST API használata esetén nem támogatott.

### <a name="turns"></a>Bekapcsolása

Beszéd egy információk szolgáltatója. Amikor beszél, kívánt információáramlás, aki ezt az információt a figyeli a birtokában van. Küldött értesítésben információkat, akkor általában felváltva beszéd és figyel. Hasonlóképpen a beszéd-kompatibilis alkalmazás kommunikációja felhasználók felváltva figyel és válaszol, bár az alkalmazás általában teszi a figyelő a legtöbb. A felhasználó szóbeli bemeneti és a szolgáltatás válasza a bemeneti nevezik egy *kapcsolja*. A *kapcsolja* akkor kezdődik, amikor a felhasználó beszél, és ezzel véget ér, az alkalmazás a beszédfelismerés szolgáltatás válasza kezelésének befejezése.

### <a name="telemetry"></a>Telemetria

A beszéd-kompatibilis eszköz vagy alkalmazás létrehozása kihívást jelenthet, még akkor is tapasztalt fejlesztők számára. Adatfolyam-alapú protokollt gyakran úgy tűnik, első ránézésre ijesztőnek és például lehet, hogy a csend észlelési teljesen új fontos részleteket. A sok üzenetek sikeresen küldött és fogadott kell végrehajtani egy egyetlen kérelem/válasz párhoz, *nagyon* fontos, hogy az üzenetek teljes és pontos adatokat gyűjteni. A Microsoft beszéd szolgáltatás protokoll biztosítja ezen adatok gyűjtését. Meg kell győződnie mindent, hogy a lehető; pontosan adja meg a szükséges adatokat. úgy, hogy megadja a teljes és pontos adatokat, akkor lesz kell útmutatás nyújtása a saját kezűleg--valaha is szüksége van segítségre a Microsoft beszéd szolgáltatás csapat rendelkezésére a ügyfélmegvalósítással kaphatók, a telemetriai adatokat összegyűjtötte minőségének kritikus probléma lesz elemzés.

> [!NOTE]
> Ez a funkció nem támogatott a beszédfelismerés REST API használata esetén.

### <a name="speech-application-states"></a>Beszéd alkalmazás állapota

Ahhoz, hogy az alkalmazás bemeneti lépései olyan más bemeneti formában lépései kissé eltérő, mint például kattintanak az egérrel vagy ujját gombra koppint. Meg kell nyomon követheti, ha az alkalmazás a mikrofon figyel, és adatokat küldeni a beszédfelismerés szolgáltatást, ha a szolgáltatás válaszára várakozik, illetve ha üresjárati állapotban van. Ezeket az állapotokat kapcsolatát az alábbi ábrán látható.

![Beszéd alkalmazásállapot diagramja](Images/speech-application-state-diagram.png)

A Microsoft beszéd szolgáltatás részt vesz az egyes állapotai, mert a szolgáltatás protokoll üzeneteket, amelyek segítenek az alkalmazás átmenet állapotok közötti határozza meg. Az alkalmazás kell értelmezni, és ezek protokoll üzenetek nyomon követheti és figyelheti a beszédfelismerés alkalmazás állapotok rájuk.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>A beszédfelismerés felismerési szolgáltatás használatát a alkalmazásai

A Microsoft beszéd felismerési szolgáltatás a fejlesztők számára a beszédfelismerés hozzáadása alkalmazások két lehetőséget biztosít.

- [REST API-k](GetStarted/GetStartedREST.md): a fejlesztők a beszédfelismerési a szolgáltatásnak HTTP alkalmazások hívást.
- [Ügyféloldali kódtáraknál](GetStarted/GetStartedClientLibraries.md): olyan speciális funkciókhoz, a fejlesztők letöltheti Microsoft Speech klienskódtárak segítségével, és az alkalmazásokba való hivatkozás.  A klienskódtárak segítségével a különböző platformokon (Android, iOS Windows,) használatával (C#, Java, JavaScript, ObjectiveC) különböző nyelveken érhetők el.

| Használati esetek | [REST API-k](GetStarted/GetStartedREST.md) | [Ügyfél-függvénytárak](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Egy rövid szóbeli hang-, a konvertálás például parancsok köztes eredmények nélkül (hang hossza < 15 s) | Igen | Igen |
| Alakítsa át a hosszú hang (> 15 s) | Nem | Igen |
| Az adatfolyam hang szükséges ideiglenes eredményekkel | Nem | Igen |
| A szöveg LUIS használatával hang konvertálásához ismertetése | Nem | Igen |

 Ha a nyelv vagy a platform még nem rendelkezik az SDK, a saját megvalósítási alapján hozhat létre a [protokoll dokumentációja](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Felismerés módok

A felismerési három módot: `interactive`, `conversation`, és `dictation`. A mód beállítása alapján, hogy a felhasználók valószínűleg beszéd beszédfelismerés. Válassza ki a megfelelő mód az alkalmazáshoz.

> [!NOTE]
> Felismerés módok esetleg tartalmaz különböző beállításokat a [REST protokoll](#rest-speech-recognition-api) működnek a [WebSocket protokoll](#webSocket-speech-recognition-api). Például a REST API nem támogatja beszélgetés vagy Diktálás módban is folyamatos használatát.
> [!NOTE]
> Az üzemmódokról közvetlenül a REST- vagy WebSocket protokoll használata esetén alkalmazható. A [klienskódtárak](GetStarted/GetStartedClientLibraries.md) mód adja meg a különböző paraméterek használatával. További információkért tekintse meg az ügyféloldali kódtár az Ön által választott.

A Microsoft beszéd szolgáltatás minden felismerés mód csak egy felismerés kifejezés eredménye adja vissza. Maximális száma bármely egyetlen utterance 15 másodpercre van.

### <a name="interactive-mode"></a>Interaktív mód

A `interactive` módban, a felhasználó rövid kérelmek teszi és az alkalmazás válaszul egy műveletet vár.

A következő jellemzőkkel tipikusan interaktív módban alkalmazások:

- Felhasználók tudják, a gép nem pedig egy másik emberi beszéd.
- Az alkalmazás felhasználóinak tudja előre szeretnék mondja ki, mi a teendő az alkalmazás szeretnék alapján.
- Utterances általában kapcsolatos utolsó 2-3 másodpercet.

### <a name="conversation-mode"></a>Beszélgetés mód

A `conversation` módban, a HR-részleg-emberi beszélgetés részt vevő felhasználók.

A következő jellemzőkkel tipikusan beszélgetés mód alkalmazások:

- Felhasználók tudják, hogy azok egy másik személyre beszélünk.
- Beszédfelismerés a emberi témák egyik vagy mindkét résztvevők szóbeli szöveg azáltal növeli.
- Felhasználók nem mindig tervezze meg szeretnék mondja ki.
- Felhasználók gyakran használt zsargon és egyéb kötetlen beszéd.

### <a name="dictation-mode"></a>Diktálás

A `dictation` mód, felhasználók recite hosszabb utterances az alkalmazás további feldolgozásra.

A következő jellemzőkkel tipikusan Diktálás mód alkalmazások:

- Felhasználók tudják, hogy a gép beszélünk-e.
- A felhasználóknak megjelenik a beszédfelismerés felismerés szöveges eredményt.
- Felhasználók gyakran tervezze meg, mit szeretne tegyük fel például, és a formális nyelv használatával.
- Felhasználók alkalmaz teljes sentences, amelyek utolsó 5 – 8 másodperc.

> [!NOTE]
> A Microsoft beszéd szolgáltatás Diktálás és beszélgetés módban, nem ad vissza részleges eredményt. Ehelyett a szolgáltatás a hangadatfolyam csend határokhoz után állandó kifejezés eredményeket ad vissza. A Microsoft a beszédfelismerés protokoll folyamatos felismerés módokban a felhasználói élmény javítása érdekében előfordulhat, hogy javítása.

## <a name="recognition-languages"></a>Nyelveket

A *beszédfelismerési nyelv* meghatározza, hogy az alkalmazás felhasználói beszél nyelvét. Adja meg a *beszédfelismerési nyelv* rendelkező a *nyelvi* URL-cím lekérdezésparaméter a kapcsolaton. Értékét a *nyelvi* lekérdezési paraméter használja az IETF nyelvcímkének [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag), és **kell** beszédfelismerés API által támogatott nyelvek valamelyikével kell. A beszédfelismerés szolgáltatás által támogatott nyelvek teljes listája megtalálható az oldal [támogatott nyelvek](API-Reference-REST/supportedlanguages.md).

A Microsoft beszéd szolgáltatás érvénytelen kapcsolódási kérelmeket visszautasítja megjelenítésével jelzi ezt az `HTTP 400 Bad Request` választ. Érvénytelen kérést egyike, amelyek:

- Nem tartalmaz egy *nyelvi* lekérdezési paraméter értéke.
- Tartalmaz egy *nyelvi* lekérdezésparaméter, amelyek formátuma nem megfelelő.
- Tartalmaz egy *nyelvi* paraméter, amely nem támogatja nyelveinek lekérdezése.

Választhatja azt, vagy a szolgáltatás által támogatott nyelvek támogató alkalmazás létrehozásához.

### <a name="example"></a>Példa

A következő példában egy alkalmazás használja *beszélgetés* az Amerikai angol hangalapú beszédfelismerési mód.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Írjanak elő válaszok

A írjanak elő válaszok a konvertált visszaadásának az ügyfelek számára a hang. Írjanak elő választ a következő mezőket tartalmazza:

- `RecognitionStatus` a felismerési állapotát határozza meg. A lehetséges értékeket az alábbi táblázat a.

| status | Leírás |
| ------------- | ---------------- |
| Sikeres | A felismerési sikeres volt, és a szöveg mező szerepel |
| NoMatch | Beszéd észlelt a hangadatfolyam, de nem a megadott nyelv szavak egyező volt. [NoMatch felismerés Status(#nomatch-recognition-status) további részletekért lásd:  |
| InitialSilenceTimeout | A hangadatfolyam kezdetét csak csend, és az időhatár-túllépés beszéd szolgáltatás található |
| BabbleTimeout | A hangadatfolyam kezdetét csak zaj, és az időhatár-túllépés beszéd szolgáltatás található |
| Hiba | A felismerési szolgáltatás belső hibát észlelt, ezért leállt |

- `DisplayText` a felismert kifejezés jelöli a kis-és nagybetűk, absztrakt és Inverz-szöveg-normalizálási telepítve lettek, és Profanitás rendelkezik van maszkolva csillag után. A megjelenő mező szerepel *csak* Ha a `RecognitionStatus` mező értéke `Success`.

- `Offset` eltolás (100 nanoszekundumos egységekben), amellyel a kifejezés ismerhető, a hangadatfolyam kezdetét viszonyítva.

- `Duration`a beszédfelismerés kifejezés (100 nanoszekundumos egységekben) időtartamát határozza meg.

Írjanak elő választ további információkat ad vissza, ha szükséges. Lásd: [kimeneti formátum](#output-format) arról, hogyan részletes kimenet visszaadása.

Microsoft beszéd szolgáltatás további írjanak elő folyamat, amely tartalmazza az hozzáadását írásjelek, és a nagybetűk Profanitás maszkolás és közös képernyők szöveg normalizálása támogatja. Például ha egy felhasználó által képviselt kifejezés beszél szavak "Emlékeztessen megvásárlása hat iPhone-OK", a Microsoft beszéd szolgáltatást a transcribed szöveg "Emlékeztessen megvásárlása 6 iPhone-okra." A folyamat, amely a "hat" szó alakítja át a számot, "6" nevezik *Inverz szöveg normalizálási* (*ITN* röviden).

### <a name="nomatch-recognition-status"></a>NoMatch felismerés állapota

A írjanak elő választ ad vissza `NoMatch` a `RecognitionStatus` amikor a Microsoft beszéd szolgáltatás beszéd észleli a hangadatfolyam, de nem felel meg, hogy a kérelemhez használt nyelv nyelvtani beszédfelismerés. Például egy *NoMatch* az állapot akkor fordulhat elő, ha a felhasználó valami német nyelven szerint, amikor a felismerő angol nyelvi szóbeli vár. A hanghullám mintáját a utterance volna emberi beszéd meglétének jelzéséhez, de a szóbeli szavak egyike sem használja a felismerő amerikai angol lexikonban megfelelő.

Egy másik *NoMatch* állapot akkor fordul elő, ha a felismerési algoritmus nem tudja a hangok a hangadatfolyam szereplő pontos egyezést. Ez az állapot akkor fordul elő, amikor a Microsoft beszéd szolgáltatás készíthet *speech.hypothesis* tartalmazó üzenetek *szöveg feltételezett* , de a művelet létrehoz egy *speech.phrase*üzenetet, amelyben a *RecognitionStatus* van *NoMatch*. Ez a feltétel nem jelent problémát; nem szabad végrehajtania bármilyen pontosságát vagy rögzített szöveg feltételezéseket a *speech.hypothesis* üzenet. Ezenkívül meg kell nem feltételezze, hogy a Microsoft beszéd szolgáltatás előállító *speech.hypothesis* üzeneteket, amelyek a szolgáltatás nem képes előállítani egy *speech.phrase* message,  *RecognitionStatus* *sikeres*.

## <a name="output-format"></a>Kimeneti formátum

Microsoft beszéd szolgáltatás is hasznos formátumok számos írjanak elő válaszok visszaadása. Összes hasznos adatot JSON struktúrák.

A kifejezés eredményformátum megadásával szabályozhatja a `format` URL-cím lekérdezési paraméter. Alapértelmezés szerint a szolgáltatás visszaadja `simple` eredmények.

| Formátum | Leírás |
|-----|-----|
| `simple` | A felismerési állapota és az űrlap megjelenítése a felismert szöveget tartalmazó egyszerűsített kifejezés eredményt. |
| `detailed` | A felismerési állapotát, és ahol minden kifejezés eredménye összes négy felismerés űrlap és egy megbízhatósági pontszám tartalmazza-e a kifejezés eredményeinek N-legjobb listázhatja. |

A `detailed` formátum tartalmaz [N-legjobb értékek](#n-best-values), kívül `RecognitionStatus`, `Offset`, és `duration`, a válaszban.

### <a name="n-best-values"></a>N-legjobb értékek

Figyelők, akár emberi, akár gépi, soha nem lehet abban, hogy azok kérték egyes *pontosan* Mi történt szóbeli. Egy figyelő rendelhet egy *valószínűség* csak az adott értelmezésének egy utterance. 

Normál körülmények között, ha mások számára, akikkel gyakran léphetnek kapcsolatba, és beszéljen a felhasználók nagy valószínűséggel ismeri fel a szöveget, volt szóbeli is. A virtuálisgép-alapú beszéd figyelői hasonló pontossága szintek eléréséhez szükség és a megfelelő feltételek mellett [emberek paritás elérésének](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

A beszédfelismerés használt algoritmusok megismerkedhet a normál folyamat részeként egy utterance alternatív értelmezéseket. Másik módszert általában, a rendszer elveti a egyetlen értelmezése helyett a tanúsítás túlságosan kerül. Kevésbé optimális körülmények között azonban a beszédfelismerés nyelvével befejezze a másik lehetséges értelmezéseket listáját. A felső *N* a listában szereplő alternatívák nevezzük a *lista N-legjobb*. Minden egyes alternatív hozzá van rendelve egy [abban, hogy pontszám](#confidence). Megbízhatósági pontszámok 0 és 1 közé. 1 pontszámot abban, hogy a legmagasabb szintű jelöli. A 0 pontszámot abban, hogy a legalacsonyabb szintű jelöli.

> [!NOTE]
> A bejegyzések száma a az N-legjobb lista több utterances eltérőek. A bejegyzések száma több bizonyítványoknak változhatnak a *azonos* utterance. Ezt a különbséget a beszédfelismerés felismerés algoritmus probabilisztikus jellege természetes és várt eredményét.

Mindegyik bejegyzés az N-legjobb listában visszaadott tartalmazza

- `Confidence`, jelölő a [abban, hogy pontszámok](#confidence) a bejegyzés.
- `Lexical`, amely a [lexikális űrlap](#lexical-form) a felismert szöveg.
- `ITN`, amely a [ITN űrlap](#itn-form) a felismert szöveg.
- `MaskedITN`, amely a [ITN űrlap maszkolva](#masked-itn-form) a felismert szöveg.
- `Display`, amely a [űrlap megjelenítése](#display-form) a felismert szöveg.

### Megbízhatósági pontszámok <a id="confidence"></a>

Megbízhatósági pontszámok szerves Beszéd felismerés rendszereken. A Microsoft beszéd szolgáltatás beolvassa az vetett bizalmat pontszámok egy *abban, hogy osztályozó*. A Microsoft az vetett bizalmat osztályozó betanítja olyan szolgáltatások készlete, amelyek úgy tervezték, hogy a helyes és helytelen felismerés standardként megkülönböztetést keresztül. Pontszámok abban, hogy egyes szavak és teljes utterances állapított meg.

Ha a megbízhatósági pontokat, a szolgáltatás által visszaadott használatát választja, a következő viselkedés figyelembe:

- Csak belül az azonos mód és a nyelvi abban, hogy pontszámok hasonlíthatók össze. Más nyelvű vagy különböző felismerő módok között pontszámok hasonlítja össze. Például az interaktív mód abban, hogy pontszámot rendelkezik *nem* abban, hogy pontszámot Diktálás módban való korreláció.
- Megbízhatósági pontszámok utterances köre korlátozott a legérdemesebb használni. Természetesen nincs vonatkozó eredmények utterances számos eltérési kiváló fokát.

Ha úgy dönt, hogy az abban, hogy pontszám érték, egy *küszöbérték* meg, amely az alkalmazás működik, használja a Beszédfelismerés a küszöbértékek meghatározásához.

- Hajtsa végre a beszédfelismerés alkalmazás utterances reprezentatív mintáját.
- Az egyes felismerés minta készletében a abban, hogy pontszámait gyűjtése.
- A küszöbérték alapjául a minta az vetett bizalmat közül néhány PERCENTILIS visszaadása.

Nincs egyetlen küszöbértéket értéke megfelelő-e az összes alkalmazáshoz. Előfordulhat, hogy egy alkalmazás egy elfogadható abban, hogy pontszám nem fogadható el, hogy egy másik alkalmazás.

### <a name="lexical-form"></a>lexikális képernyő

A lexikális formátuma a felismert szöveg pontosan hogyan azt történt a utterance, absztrakt vagy a kis-és nagybetűk nélkül. Például a címet "1020 vállalati módon" lexikális formájában lenne *10 20 vállalati módon*, feltéve, hogy azt lett szóbeli ily módon. A mondatok lexikális formájában "Emlékeztessen 5 ceruza megvásárlása" van *Emlékeztessen öt ceruza megvásárlása*.

A lexikális formátuma nem szabványos szöveg normalizálási végrehajtásához igénylő alkalmazásokhoz legmegfelelőbb. A lexikális formátuma is megfelelő feldolgozatlan felismerés szavak igénylő alkalmazásokhoz.

Profanitás soha nem maszkolt lexikális formájában.

### <a name="itn-form"></a>ITN képernyő

Szöveg normalizálási szöveg egy űrlap egy másik "kanonikus" formában történő átalakítása során a rendszer. Például a telefonszám "555-1212" lehet, hogy alakítható át a kanonikus alakja *öt öt öt egy két egy két*. *Más néven inverz* szöveg normalizálási (ITN) megfordítja ezt a folyamatot, a szavakat konvertálása "öt öt öt egy két egy két" fordított kanonikus formájában *555-1212*. A felismerési eredmények ITN formájában nem tartalmaz kis-és nagybetűk, illetve absztrakt.

A ITN a felismert szöveg működő alkalmazásokhoz legmegfelelőbb érték. Például egy alkalmazás, amely lehetővé teszi, hogy a felhasználó képes legyen alkalmazni a keresési feltételeket, és használja ezeket a feltételeket a webes lekérdezésben használna a ITN űrlap. Profanitás soha nem maszkolt ITN formájában. A maszk Profanitás használja a *maszkolt ITN űrlap*.

### <a name="masked-itn-form"></a>Maszkolt ITN képernyő

Profanitás része a természetes nyelvi szóbeli, mert a Microsoft beszéd szolgáltatás érzékeli az ilyen szavak és kifejezések éppen kiejtett. Profanitás előfordulhat, hogy azonban nem minden olyan alkalmazásnál, különösen akkor ezeknek az alkalmazásoknak a korlátozott, nem felnőtt felhasználói célközönség megfelelő.

A maszkolt ITN űrlap számára a más néven Inverz szöveg normalizálási forma maszkolás Profanitás vonatkozik. A Profanitás maszkolja a Profanitás paraméterérték értékének meg `masked`. Amikor Profanitás van maszkolva, a nyelvi Profanitás lexicon részeként szavakat csillag helyett. Például: *Emlékeztessen megvásárlása 5 x ceruza*. Maszkolt ITN formájában felismerés eredményeként nem tartalmaz kis-és nagybetűk, illetve absztrakt.

> [!NOTE]
> Ha a Profanitás lekérdezési paraméter értéke `raw`, a maszkolt ITN űrlap megegyezik a ITN űrlap. Profanitás van *nem* maszkolva.

### <a name="display-form"></a>Űrlap megjelenítése

Absztrakt és a nagybetűk azt hangsúlyt, ahol a szüneteltetési, és így tovább, így könnyebben érthetőek legyenek szöveg helyét. Az űrlap megjelenítése absztrakt és a nagybetűk hozzáadása felismerési eredmények, így az alkalmazásokat, amelyek a szóbeli szöveg megjelenítése a legmegfelelőbb formában.

Az űrlap megjelenítése a maszkolt ITN űrlapot terjeszti ki, mert állíthat be a Profanitás paraméter értéke `masked` vagy `raw`. Ha a változó értéke `raw`, a felismerési eredmények tartalmazzák a felhasználó által felolvasott bármely Profanitás. Ha a változó értéke `masked`, a nyelvi Profanitás lexicon részeként felismerhető szavakat csillag helyett.

### <a name="sample-responses"></a>A minta válaszok

Összes hasznos adatot JSON struktúrák.

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

## <a name="profanity-handling-in-speech-recognition"></a>A beszédfelismerés Profanitás-kezelése

A Microsoft beszéd szolgáltatás észleli az összes űrlap emberi beszéd, beleértve a szavak és kifejezések, hogy sokan szeretné besorolni, "Profanitás." A szolgáltatás kezelésének Profanitás segítségével szabályozhatja a *Profanitás* lekérdezési paraméter. Alapértelmezés szerint a szolgáltatás elfedi a Profanitás *speech.phrase* annak az eredménye, és nem ad vissza *speech.hypothesis* Profanitás tartalmazó üzenetek.

| *Profanitás* érték | Leírás |
| - | - |
| `masked` | Maszkok Profanitás csillag. Ez a viselkedés az alapértelmezett beállítás. | 
| `removed` | Minden eredmény Profanitás eltávolítja. |
| `raw` | Felismeri és Profanitás összes eredményt adja vissza. |

### <a name="profanity-value-masked"></a>Profanitás érték `Masked`

A maszk Profanitás meg a *Profanitás* lekérdezési paraméter értéke a *maszkolva*. Ha a *Profanitás* lekérdezési paraméter értéke ezzel, vagy nincs megadva a kérelmet, a szolgáltatás *maszkok* Profanitás. A szolgáltatás maszkolási azáltal, hogy a felismerési eredmények Profanitás csillag hajtja végre. Profanitás-maszkolási kezelésére ad meg, a szolgáltatás nem ad vissza *speech.hypothesis* Profanitás tartalmazó üzenetek.

### <a name="profanity-value-removed"></a>Profanitás érték `Removed`

Ha a *Profanitás* lekérdezési paraméter értéke *eltávolított*, a szolgáltatás eltávolítja a Profanitás is *speech.phrase* és *speech.hypothesis* üzeneteket. Az eredmények megegyeznek *, mintha a Profanitás szavak nem volt szóbeli*.

#### <a name="profanity-only-utterances"></a>Csak Profanitás utterances

A felhasználó előfordulhat, hogy beszéd *csak* Profanitás alkalmazás Profanitás eltávolítása a szolgáltatás konfigurálásakor. Az ebben a forgatókönyvben, ha a mód *Diktálás* vagy *beszélgetés*, a szolgáltatás nem ad vissza egy *speech.result*. Ha a mód *interaktív*, a szolgáltatás értéket ad vissza egy *speech.result* -as állapotkóddal *NoMatch*. 

### <a name="profanity-value-raw"></a>Profanitás érték `Raw`

Ha a *Profanitás* lekérdezési paraméter értéke *nyers*, a szolgáltatás nem távolítsa el, vagy akár Profanitás maszkolja a *speech.phrase* vagy  *Speech.hypothesis* üzeneteket.
