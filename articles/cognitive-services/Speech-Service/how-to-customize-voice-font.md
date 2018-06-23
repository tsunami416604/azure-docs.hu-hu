---
title: Mi az az egyéni hang? -Azure kognitív szolgáltatások |} Microsoft Docs
description: A cikk áttekintése Microsoft szöveg-beszéd átalakítás hang testreszabásával, amely lehetővé teszi egy felismerhető, egy-az-a-jellegű márka hang létrehozását.
services: cognitive-services
author: noellelacharite
manager: nolach
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: ad5af799fd46dc51b85432999f986de8cdb056ec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349347"
---
# <a name="creating-custom-voice-fonts"></a>Egyedi Hangüzenetek betűtípusok létrehozása

Microsoft szöveg-beszéd átalakítás (TTS) hang testreszabási lehetővé teszi a márka számára felismerhető, egy-az-a-jellegű hangot létrehozását: egy *hang betűtípusát.* 

A hang betűtípus létrehozása, ellenőrizze a studio felvétel, majd töltse fel a betanítási adatok, a kapcsolódó parancsprogramokat. A szolgáltatás ekkor létrehoz egy egyedi hang modell a rögzítése terveztek. A beszédfelismerési betűtípusára majd beszéd szintetizálásához. 

Ismerkedés a koncepció igazolása adatainak kevés. De a további adatokat ad meg, annál természetes és professional hanggal hangvételére.

Hang testreszabási angol (en-US) és szárazföldi kínai (zh-CN) érhető el.

## <a name="prerequisites"></a>Előfeltételek

A szöveg-beszéd átalakítás hang testreszabási szolgáltatás jelenleg magán előnézetben. [Töltse ki az alkalmazás űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) hozzáférés figyelembe kell venni.

Emellett a következőkre van szükség:

* Az Azure-fiók ([regisztráljon az ingyenes](https://azure.microsoft.com/free/ai/) Ha még nincs ilyen).

* A beszéd-előfizetés. [Hozzon létre egyet](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) Ha még nem tette meg.

    ![Hozzon létre panel](media/custom-voice/create-panel.png)

Miután létrehozta az előfizetést, a gyors üzembe helyezés vagy a áttekintése panelen az új előfizetés két előfizetés kulcsok találja. Vagy a kulcs segítségével.

Végül csatlakoztassa az előfizetés az egyéni hang portálon az alábbiak szerint.

1. Jelentkezzen be a [egyéni hang portal](https://customvoice.ai) hozzáféréshez használt alkalmazandó ugyanazon Microsoft-fiókjával.

2. Nyissa meg a jobb felső "Előfizetések" a fiók alatt.

    ![Előfizetések](media/custom-voice/subscriptions.png)

3. Az "Előfizetés" lapon válassza a "Csatlakozás meglévő előfizetés".

     ![Csatlakozás meglévő előfizetés](media/custom-voice/connect-existing-sub.png)

4. Az Előfizetés kulcs illessze be a táblázat alább látható módon.

     ![Előfizetés hozzáadása](media/custom-voice/add-subscription.png)

Most készen áll!

## <a name="prepare-recordings-and-transcripts"></a>Felvétel és ki előkészítése

Egy hang képzési dataset hangfájlok, az alábbi zenei fájlok átiratai tartalmazó szövegfájl együtt áll.

Ezek a fájlok egyik irányban előkészítheti: vagy parancsfájlokat, és hang tehetség, olvassa vagy használja a nyilvánosan elérhető hang és szöveggé átvezeti őket. Az utóbbi esetben a hang fájlok, például a "százalékos" és más kitöltő hangok, akadozik, mumbled szavak vagy mispronunciations disfluencies szerkesztése.

Egy jó hang betűtípus eredményezett, fontos, hogy a felvétel kiváló minőségű mikrofon csendes helyiségben történik. Egységes kötet sebessége, és beszéljen térközt és kifejező mannerisms beszéd beszéd nélkülözhetetlenek a kiváló digitális hangot felépítése. Egy hang üzemi használatra létrehozásához javasoljuk, használjon egy professzionális rögzítése studio és hangra vonatkozó tehetség.

### <a name="audio-files"></a>Hangfájlok

Mindegyik hangfájlt egy egyetlen utterance (például egyetlen mondatban vagy párbeszédpanel rendszer egyetlen menetben) kell tartalmaznia. Minden fájl (többnyelvű egyéni hangok nem támogatottak) nyelvével azonos nyelven kell lennie. A hangfájlok is egyes egy egyedi numerikus fájlnevet kell a fájlnév-kiterjesztésű végrehajtott `.wav`.

Hangfájlok a következőképpen kell készíteni. Más formátum nem támogatott, és vissza lesznek utasítva.

| **Tulajdonság** | **Érték** |
| ------------ | --------- |
| Fájlformátum  | RIFF (WAV)|
| Mintavételi ráta| 16 000 Hz |
| Csatornák     | 1 (monophonic)  |
| A minta formátumban| PCM, 16 bites |
| Fájlnév    | A numerikus `.wav` bővítmény |
| Archiválási formátum| zip      |
| Archívum maximális mérete|200 MB|

Helyezze el az hang fájlokat alkönyvtárak nélkül egyetlen mappába, majd egy ZIP-archívumból állítja be a teljes csomag.

> [!NOTE]
> A portál jelenleg importálja ZIP legfeljebb 200 MB archiválja. Azonban több archívumot is fel kell tölteni. Az adatkészletek engedélyezett maximális számát az 10 ZIP-előfizetés felhasználóinak és a szabványos előfizetés felhasználók 50 szabad fájlokat.

### <a name="transcripts"></a>Ki

A írjanak elő fájl nem egy egyszerű Unicode-szövegfájlt (UTF-16 kevés-endian). A írjanak elő fájl minden egyes sorának hangfájl, tabulátor (kódpontot 9), és végül a Beszélgetés szövegének követ nevét kell rendelkeznie. Nem üres sorok engedélyezettek.

Példa:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Az egyedi Hangüzenetek rendszer normalizálja ki konvertálásakor a kisbetűs, és oda nem tartozó írásjelek eltávolításával. Fontos, hogy a ki-e a megfelelő hangfelvételei pontosságú 100 %.

> [!TIP]
> Ha éles szöveg-beszéd átalakítás felépítése hangok, figyelembe véve a fonetikus érvényességének és a hatékonyság válassza utterances (vagy parancsfájlokat).

## <a name="upload-your-datasets"></a>Töltse fel az adatkészletek

A hangfájl archiválási és ki előkészítése, után feltöltheti ezeket keresztül a [egyéni hang portálon](https://customvoice.ai).

> [!NOTE]
> Adatkészletek nem szerkeszthető, miután fel lett töltve. Ha elfelejtette a átiratai zenei fájlok közé tartoznak például, vagy véletlenül válasszon ki a megfelelő nemét, újra kell tölteni a teljes adatkészletet. Ellenőrizze a DataSet adatkészlet és a beállítások alaposan feltöltésének megkezdése előtt.

1. Jelentkezzen be az Portalra.

2. Válasszon **adatok** alatt egyéni hang fő lapján. 

    ![Saját projektek](media/custom-voice/my-projects.png)

    A saját hang adattábla jelenik meg. Üres, ha még nem feltöltött bármely hang adatkészletek.

3. Kattintson a **adatimportálás** új adatkészlet feltöltése lapjának megnyitásához.

    ![Hang-adatok beolvasása](media/custom-voice/import-voice-data.png)

4. Adjon meg nevet és leírást a megadott mezőket. 

5. Válassza ki a hang betűtípusok területi beállításokat. Győződjön meg arról, hogy a területibeállítás-információk rögzítése adatok és a parancsfájlok nyelvének megfelelő. 

6. Válassza ki a nemének a hangalapú, amelynek hang használ.

7. Válassza ki a parancsfájl és hangfájlok feltölteni. 

8. Kattintson a **importálási** feltölteni az adatokat. Nagyobb adatkészletekhez importálása több percig is eltarthat.

> [!NOTE]
> Ingyenes előfizetés felhasználók két adatkészletet feltöltheti egyszerre. Standard előfizetés felhasználóinak egyszerre öt adatkészletek töltse fel. Ha eléri a határértéket, várja meg, amíg a adatkészletek közül legalább egy befejezését importálása, és próbálkozzon újra.

Ha befejeződött a feltöltés, a saját hang adattábla ismét megjelenik. Meg kell jelennie egy bejegyzést, amely megfelel a most feltöltött DataSet adatkészlethez. 

Adatkészletek feltöltése után automatikusan érvényesíti. Adatok érvényesítése több ellenőrzést a ellenőrizni a fájl formátumát, a mérete és a mintavételi ráta hangfájlok tartalmazza. A írjanak elő fájlok ellenőrzésére ellenőrizze a fájl formátumát, és hajtsa végre az egyes szöveg normalizálási. A utterances vannak kért beszédfelismerés, és így a szöveg a rendszer összehasonlítja a megadott szövegének.

![A beszédfelismerési adataimhoz](media/custom-voice/my-voice-data.png)

Az alábbi táblázat a feldolgozási állapotok importált adatkészletek esetében. 

| Állapot | Jelentés
| ----- | -------
| `NotStarted` | A dataset érkezett, és a feldolgozáshoz sorba állított
| `Running` | A dataset érvényesítésre kerül
| `Succeeded` | A dataset ellenőrzése megtörtént, és előfordulhat, hogy most összeállításához kell használni egy hang betűtípus

Érvényesítési befejeződése után megtekintheti az egyes Utterance oszlopában az adatkészletek egyező utterances teljes száma.

A jelentés a kiejtés pontokat és a zajszint ellenőrizze az egyes a felvételek töltheti le. A kiejtés pontszám tartományokon, 0 – 100. a pontszám alatt 70 általában azt jelzi, beszéd hiba vagy a parancsfájl nem egyeznek. Nehéz hangsúlyos csökkentheti a kiejtés pontszám és hatással lehet a létrehozott digitális hang.

Magasabb jel-zaj arány (SNR) azt jelzi, hogy a hang az alsó zaj. Professional studios keresztül által általában egy 50 + SNR képes elérni. Az alábbi 20 egy SNR hang a generált hanggal nyilvánvaló zaj eredményezhet.

Vegye figyelembe a felvétel bármely utterances alacsony kiejtés pontszámok vagy gyenge jel zaj arányt tesz lehetővé. A felvétel nincs lehetőség, ha ezek utterances előfordulhat, hogy kizárja a dataset adatkészletből.

## <a name="build-your-voice-font"></a>A beszédfelismerési betűtípus összeállítása

Miután a dataset ellenőrzése megtörtént, használhatja a egyedi Hangüzenetek betűtípus létrehozásához. 

1. Válasszon **modellek** az "Egyéni Voice" legördülő menüjében. 
 
    A saját hang betűtípusok tábla megjelenik, a már létrehozott egyedi Hangüzenetek betűtípusok tartalmazza.

1. Kattintson a **hangok létrehozása** a tábla cím alatt. 

    A lap egy hang betűtípus létrehozásához jelenik meg. A területi beállítás látható a tábla első sorát. Módosítsa a hangot létrehozásához egy másik nyelvű területi beállításokat. A területi beállítás ugyanaz, mint a hang létrehozásához használt adathalmaz kell lennie.

1. Hasonló a dataset feltöltött, módon adja meg a nevet és leírást, hogy könnyebben azonosíthassa az ebben a modellben. 

    Itt neve lesz a nevet adja meg a hang a beszédfelismerés összefoglaló vonatkozó kérést az SSML-bemenet részeként, tehát a körültekintően használja. Csak betűket, számokat és néhány írásjelek karaktereket, mint például "-", "_" "(",")" engedélyezettek.

    A közös a Leírás mezőben rendeltetése jegyezze fel a modell létrehozása során használt adathalmaz nevét.

1. Válassza ki a nemét, a hang betűtípus. Meg kell egyeznie a nemét, a DataSet adatkészlet.

1. Válassza ki az adathalmaz a hang betűtípus betanítása használni kívánt. Minden adatkészletekből a azonos hangalapú kell lennie.

1. Kattintson a **létrehozása** a hang betűtípus létrehozásának megkezdéséhez.

    ![Modell létrehozása](media/custom-voice/create-model.png)

Az új modell jelenik meg a saját hang betűtípusok tábla. 

![A beszédfelismerési betűkészletek](media/custom-voice/my-voice-fonts.png)

A megjelenített állapot az adatkészlethez konvertálása egy hang betűtípus folyamata tükrözi, ahogy az itt látható.

| Állapot | Jelentés
| ----- | -------
| `NotStarted` | A kérelem a hang betűtípus létrehozásához a a feldolgozáshoz sorba állított
| `Running` | A beszédfelismerési betűtípus létrehozása folyamatban van
| `Succeeded` | A beszédfelismerési betűtípus létrejött, és telepítésére

Idő betanítása attól függ, hogy a feldolgozott hang adatok mennyiségét. Tipikus időpontok között tájékozódhat utterances több száz 30 perc a 20 000 utterances 40 óra.

> [!NOTE]
> Ingyenes előfizetés felhasználók is betanítása két hang betűtípusok egyszerre. Standard előfizetés felhasználóinak egyszerre három hangok még betanítani. Ha eléri a határértéket, várjon, amíg a hang betűtípusok közül legalább egy képzési befejeződik, majd próbálkozzon újra.

## <a name="test-your-voice-font"></a>A beszédfelismerési betűtípus tesztelése

Miután a hang betűtípus sikeresen épül, mielőtt központilag telepítené azt a tesztelheti. Kattintson a **teszt** műveletek oszlopában. A teszt lap jelenik meg a kijelölt hangot betűkészlet. A táblázat üres, ha még nem még a vizsgálat kéréseit a hang.

![A beszédfelismerési betűkészletek, 2. rész](media/custom-voice/my-voice-fonts2.png)

Kattintson a **szövegre teszt** a tábla címben megjelenítendő szöveg a kérelmet a felbukkanó gombra. A tesztelési kérelem egyszerű szöveges vagy SSML elküldheti. A maximális bemeneti mérete 1024 karaktert, köztük az SSML-kérés található összes kódcímkének. A szöveg nyelvének a nyelve, a hang betűtípus azonosnak kell lennie.

![Hangos betűtípus tesztelése](media/custom-voice/voice-font-testing.png)

A szövegmező kitöltése, és erősítse meg a beviteli módot, után kattintson a **Igen** küldje el a vizsgálati kérést, és a vizsgálati laphoz való visszatéréshez. A tábla most már tartalmaz egy bejegyzést, amely megfelel az új kérelmet, és most már ismerős állapot oszlopában. Beszéd szintetizálásához néhány percet is igénybe vehet. Az Állapot oszlopban sikeres olvasását, letöltheti a szöveges bevitel (egy `.txt` fájl) és a hangkimeneti (egy `.wav` fájl) és az utóbbi szolgáltatásminőségi audition.

![Hangalámondás betűtípus tesztelése, 2. rész](media/custom-voice/voice-font-testing2.png)

## <a name="create-and-use-a-custom-endpoint"></a>Létrehozhat és használhat egy egyéni végpont

Miután sikeresen létrehozta és tesztelte a hang-modell, telepítheti azt egy egyéni szöveg-beszéd átalakítás végpont. Ehhez a végponthoz, a szokásos végpont helyett majd ha a szöveg-beszéd átalakítás kérést a REST API-n keresztül használja. Az egyéni végpont csak az előfizetést, a betűtípus központi telepítéséhez használt meghívható.

Hozzon létre egy új egyéni végpontot, válassza a **végpontok** a lap tetején lévő egyedi Hangüzenetek menüből. A központi telepítés lap jelenik meg, és a tábla jelenlegi egyedi Hangüzenetek végpontok, ha van ilyen.

Kattintson a **hangok telepítése** gombra kattintva új végpont létrehozásához. A Create Endpoint"lapon, az aktuális területi beállítása is megjelenik a tábla első sorát. Telepítést hozhat létre más nyelven, módosítsa a megjelenített nyelvterületi beállításokat. (Akkor meg kell egyeznie a hang telepít.) Adja meg a név és leírás egyéni végpontot.

Az előfizetés menüből válassza ki a használni kívánt előfizetést. Ingyenes előfizetés felhasználók rendelkezhetnek telepített egyszerre csak egy modell. Standard előfizetés felhasználóinak legfeljebb 20 végpontok, mindegyiket a saját egyedi Hangüzenetek hozhat létre.

![-Végpont létrehozása](media/custom-voice/create-endpoint.png)

A telepítendő modell kiválasztása után kattintson az **létrehozása**. A telepítési oldal ismét megjelenik, most már az új végpont olyan bejegyzés. Új végpont példányosítani néhány percig is eltarthat. A telepítés sikeres van, a végpont esetén használatra kész.

![A telepített hangok](media/custom-voice/my-deployed-voices.png)

Ha a központi telepítési állapot van sikeres volt, a végpont a telepített hang betűtípus megjelenik a telepített hangok táblázatban. Ezt az URI közvetlenül egy HTTP-kérelem használható.

A végpont online tesztelését is rendelkezésre áll, az egyedi Hangüzenetek portálján keresztül. A végpont teszteléséhez válassza **tesztelés végpontok** a egyéni hang legördülő menüből. A végpont tesztelés lap jelenik meg. Válassza ki a központilag telepített hang, és adjon meg a szövegmezőbe írja be a szöveget, (az egyszerű szöveges vagy SSML formátumban).

> [!NOTE] 
> SSML, használatakor a `<voice>` címke a egyedi Hangüzenetek létrehozásakor megadott nevét kell megadnia.

Kattintson a **lejátszása** a szöveget a egyedi Hangüzenetek betűmérettel szóbeli meghallgatásához.

![Végpont tesztelése](media/custom-voice/endpoint-testing.png)

Az egyéni végpont funkcionálisan megegyezik a szabványos végponthoz használt szöveg-beszéd átalakítás kérelmeket. Lásd: [REST API](rest-apis.md) további információt.

## <a name="next-steps"></a>További lépések

- [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A C# beszéd felismerésére](quickstart-csharp-windows.md)
