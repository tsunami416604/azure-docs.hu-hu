---
title: Mi az egyéni beszédfelismerési? – Az azure Cognitive Services |} A Microsoft Docs
description: Ez a cikk áttekintése Microsoft szöveg-beszéd átalakítás hangalapú testreszabásával, amely lehetővé teszi, hogy hozzon létre egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló márka hangalapú.
services: cognitive-services
author: noellelacharite
manager: nolach
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: b9218600db8bd3e20528e61e411bb64c10dd9c84
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917635"
---
# <a name="creating-custom-voice-fonts"></a>Egyéni hangtípust létrehozása

Microsoft szövegfelolvasás (Szövegfelolvasás) hangalapú testreszabási lehetővé teszi, hogy hozzon létre egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló hang, a saját márkáját: egy *hangtípusú.* 

A hangtípusú létrehozásához, győződjön meg arról, a studio felvételt, és töltse fel a betanítási adatok, a kapcsolódó parancsprogramokat. A szolgáltatás ekkor létrehoz egy egyéni modell a rögzítése használatára vannak konfigurálva. Ezután használhatja e hangtípusú, beszéd szintetizálásához. 

Az egy kisebb mennyiségű adatot egy megvalósíthatósági megkezdheti. De minél több adatot ad meg, annál természetes és professional a hangját hangzik.

Hangalapú testreszabási amerikai angol (en-US) és kontinens kínai (zh-CN) érhető el.

## <a name="prerequisites"></a>Előfeltételek

A szöveg-beszéd átalakítás testreszabási funkciót jelenleg privát előzetes verzióban érhető el. [Az alkalmazás űrlap kitöltésekor](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) hozzáférés figyelembe kell venni.

Az Azure-fiók és a egy előfizetést a beszédfelismerési szolgáltatás is szükséges. [Hozzon létre egyet] (https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started) Ha még nem tette. A következő csatlakozzon az előfizetés az egyéni beszédfelismerési portálon.

1. Jelentkezzen be a [egyéni beszédfelismerési portál](https://customvoice.ai) hozzáférést alkalmazásához használt ugyanazon Microsoft-fiók használatával.

2. Lépjen a fiók neve alatt az "előfizetések" jobb felső sarokban.

    ![Előfizetések](media/custom-voice/subscriptions.png)

3. Az "Előfizetések" oldalán válassza a "Csatlakozás meglévő előfizetés".

     ![Csatlakozás a meglévő előfizetés](media/custom-voice/connect-existing-sub.png)

4. Az előfizetési kulcs illessze be a táblázat alább látható módon. Minden előfizetési csomaghoz tartozik két kulcsot, és bármelyiket használhatja.

     ![Előfizetés hozzáadása](media/custom-voice/add-subscription.png)

Készen áll a go!

## <a name="prepare-recordings-and-transcripts"></a>És előkészítése

Audio-fájlokat, és a egy szövegfájlt, amely tartalmazza az összes alábbi hangfájlok az átiratok hangalapú betanítási adatkészletet tartalmaz.

Ezek a fájlok bármelyik irányba előkészítheti: vagy írjon egy parancsfájlt, és annak hangalapú szakembereket, olvassa vagy használja a nyilvánosan elérhető hang és lefényképezze azt a szöveget. Az utóbbi esetben az audio fájlokból, például a "um" és egyéb kitöltő hangok, akadozik, mumbled szavakat vagy mispronunciations disfluencies szerkesztése.

Egy jó hangtípusú előállításához, fontos, hogy a felvételt a magas színvonalú mikrofon csendes szoba történik. Egységes kötet, és beszéljen sebességét, beszéd térközt és kifejező mannerisms beszéd elengedhetetlenek, amellyel a nagyszerű digitális hangot. Hozzon létre egy hang éles környezetben, azt javasoljuk egy professzionális felvétel studio és a hangalapú szakembereket használja. További részletekért lásd: [hogyan rögzítése a voice-minták az egyéni beszédfelismerési](record-custom-voice-samples.md).

### <a name="audio-files"></a>Hangfájlok

Mindegyik hangfájlt tartalmaznia kell egy egyetlen utterance (például egyetlen mondatban vagy párbeszédpanel rendszer egyetlen menetben). Minden fájl (többnyelvű egyéni beszédhangot nejsou podporována) ugyanazt a nyelvet kell lennie. Hangfájlt is minden egyes rendelkeznie kell egy egyedi, a fájlnév-kiterjesztésű végzett numerikus filename `.wav`.

Hangfájlok a következőképpen kell készíteni. Más formátum nem támogatott, és a rendszer elutasítja.

| **Tulajdonság** | **Érték** |
| ------------ | --------- |
| Fájlformátum  | RIFF (WAV)|
| Mintavételi ráta| legalább 16 000 Hz |
| Minta formátumával| A PCM, 16-bit |
| Fájlnév    | A numerikus `.wav` bővítmény |
| Archív formátum| Zip      |
| Maximális archívum mérete|200 MB|

Hangfájlok készletét helyezzen alkönyvtárak nélkül egyetlen mappát, és egyetlen ZIP-archívumot, állítsa be a teljes csomag.

> [!NOTE]
> Mintavételi sebessége alacsonyabb, mint 16000 Hz Wave fájlokat a rendszer elutasítja. Azokban az esetekben, ahol egy zip-fájl tartalmazza a regisztrálást megelőző szakaszban tanácsos különböző mintavételi aránnyal csak ezek egyenlő vagy nagyobb, mint 16000 Hz lesz importálva.
> A portálon jelenleg importálja a TÖMÖRÍTETT archívum akár 200 MB. Azonban több archívumot lesz feltöltve. Adatkészletek engedélyezett maximális számát az 10 ZIP-előfizetés felhasználóinak és 50 a standard szintű előfizetés felhasználóinak ingyenes fájlok.

### <a name="transcripts"></a>Szövegekben

Az átírási fájlja (ANSI/UTF-8/UTF-8-BOM/UTF-16-LE/UTF-16-BE) egyszerű szöveges fájlt. Az átírási fájl minden sorának rendelkeznie kell egy hangfájlt tabulátor (kódpont 9), végül pedig Videólejátszás követ nevét. Nem üres sorok engedélyezettek.

Példa:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Az egyéni beszédfelismerési rendszer átiratok Normalizálja a szöveg konvertálása kisbetűket, és eltávolítja a felesleges beolvasások absztrakt. Fontos, hogy az átiratok-e a megfelelő hangfelvételeket pontosságú 100 %-os.

> [!TIP]
> Amikor létrehozását a szöveg-hang transzformációs beszédhangot, figyelembe véve a fonetikus lefedettség és a hatékonyság válassza utterances (vagy parancsfájlokat).

## <a name="upload-your-datasets"></a>Az adathalmazok feltöltése

A hangfájl archív és szövegekben előkészítése, miután feltölti őket keresztül a [egyéni beszédfelismerési portál](https://customvoice.ai).

> [!NOTE]
> Adatkészletek nem szerkeszthető, miután azok fel lett töltve. Ha elfelejtette szövegekben az egyes zenei fájlok tartalmazzák, például vagy véletlenül válassza ki a megfelelő nemek, újra fel kell tölteni a teljes adatkészletet. Ellenőrizze az adatkészlet és a beállításokat alaposan feltöltésének megkezdése előtt.

1. Jelentkezzen be az Portalra.

2. Válasszon **adatok** meg egyéni beszédfelismerési alatt. 

    ![Projektek](media/custom-voice/my-projects.png)

    A saját Beszédadatait tábla jelenik meg. Emellett akkor üres, ha még nem feltölteni hangalapú adatkészletekkel.

3. Kattintson a **adatimportálás** egy új adatkészlet feltöltése lap megnyitásához.

    ![Voice-adatok importálása](media/custom-voice/import-voice-data.png)

4. Adjon meg egy nevet és leírást a megadott mezőket. 

5. Válassza ki a területi beállításait a hangtípusokkal. Ellenőrizze, hogy a területi információval kapcsolatos adatok a felvétel és a parancsfájlok nyelvének megfelelő. 

6. Válassza ki a beszélő, amelynek hangjával tartalmazzák.

7. Válassza ki a parancsfájl és hangfájlokat feltölteni. 

8. Kattintson a **importálás** feltölteni az adatokat. A nagyobb adatkészletek esetében importálása eltarthat néhány percig.

> [!NOTE]
> Ingyenes előfizetés felhasználóinak egyszerre két adatkészletet tölthet fel. Standard szintű előfizetés felhasználóinak is feltölthet egyszerre öt adatkészletek. Ha eléri a korlátot, kis türelmet, amíg legalább egy, az adatkészletek importálásának befejezése, és próbálkozzon újra.

A feltöltés befejeződése után újra megjelenik a saját Beszédadatait tábla. Megjelenik egy bejegyzést, amely megfelel a feltöltött közvetlenül az adatkészlethez. 

Az adatkészletek automatikusan érvényesíti feltöltés után. Adatok érvényesítése tartalmaz egy sor ellenőrzést a hang fájlokat, és ellenőrizze a fájlformátum, a méretet, és a mintavételi ráta. Ellenőrzések beszédátírási fájlokról ellenőrizze a fájlformátum, és hajtsa végre az egyes szöveg normalizálási. A kimondott szöveg beszédfelismerés megjelenített érzéseket, és így a szöveg a megadott szöveges összeveti.

![Saját Beszédadatait](media/custom-voice/my-voice-data.png)

Az alábbi táblázat a feldolgozási állapot importált adatkészletek esetében. 

| Állapot | Jelentés
| ----- | -------
| `NotStarted` | Az adatkészlet megkaptuk, és a feldolgozáshoz sorba állított
| `Running` | Folyamatban van az adatkészlet érvényesítése
| `Succeeded` | Az adatkészlet ellenőrzése megtörtént, és előfordulhat, hogy most már használható hozhat létre egy hangtípusú

Érvényesítés befejezése után megjelenik az adatkészletek az utterance (kifejezés) oszlopban minden egyező utterances teljes száma.

-Jelentés írásmódja pontszámokat és a zajszint ellenőrzéssel az egyes felvételeit töltheti le. A írásmódja pontszám értéke 0 – 100. 70 alatti általában azt jelzi, hogy beszéd hiba vagy a parancsfájl nem egyeznek. Nehéz hangsúlyos csökkenthető a írásmódja pontszám, és hatással van a generált digitális hangot.

Nagyobb jel zaj memóriaarányt (SNR) azt jelzi, hogy alacsonyabb lényeget a hangot a. Felvétel keresztül professzionális studios által általában egy több mint 50 SNR is elérheti. Az alábbi 20-SNR hang nyilvánvaló lényeget a generált hangminta eredményezhet.

Vegye figyelembe, hogy az alacsony írásmódja pontszámok vagy gyenge jel zaj arányok megszólalásokat a felvétel. A felvétel ez nem lehetséges, ha az adatkészlet előfordulhat, hogy kizárni azokat kimondott szöveg.

## <a name="build-your-voice-font"></a>A hangtípusú létrehozása

Ha az adatkészlet ellenőrzése megtörtént, hozhat létre az egyéni hangtípusú használhatja. 

1. Válasszon **modellek** "Egyéni beszédfelismerési" legördülő menüben. 
 
    A saját Hangtípust tábla megjelenő, bármely egyéni hangtípust már létrehozott.

1. Kattintson a **beszédhangot létrehozása** a táblázatban cím alatt. 

    Az egy hangtípusú létrehozására szolgáló oldal jelenik meg. A tábla első sorát mutatja a jelenlegi területi beállításhoz. A területi beállítások egy hang létrehozása egy másik nyelven. A területi beállításai szerint ugyanaz, mint az adatkészletek a hangalapú létrehozásához használt kell lennie.

1. Az adatkészlet feltöltésekor ugyanúgy adjon meg egy nevet és leírást, hogy azonosíthatók legyenek az ebben a modellben. 

    Itt adja meg a név lesz SSML bemeneti részeként adja meg a hangalapú beszédszintézishez vonatkozó kérelmét, ezért válasszon gondosan használt név. Csak betűket, számokat és néhány elválasztó karaktereket, például a '-', '_' '(',')' engedélyezettek.

    A Leírás mezőben egyik gyakori felhasználási, hogy a modell létrehozása során használt adatkészletekhez rekordnevekre.

1. Válassza ki a hangtípusú tartalmazzák. Meg kell egyeznie az adatkészlet tartalmazzák.

1. Válassza ki a képzés a hangtípusú használni kívánt adatkészlet(ek). Az azonos speaker használt összes adatkészlet kell lennie.

1. Kattintson a **létrehozás** a hangtípusú létrehozásának megkezdéséhez.

    ![Modell létrehozása](media/custom-voice/create-model.png)

Az új modell jelenik meg a saját Hangtípust tábla. 

![Saját Hangtípust](media/custom-voice/my-voice-fonts.png)

A megjelenített állapot jeleníti meg az adatkészlet egy hangtípusú átalakítása a folyamat itt látható módon.

| Állapot | Jelentés
| ----- | -------
| `NotStarted` | A kérelem a hangalapú betűtípus létrehozásához a feldolgozáshoz sorba állított
| `Running` | A hangtípusú létrehozása folyamatban van
| `Succeeded` | A hangtípusú létrejött, és is üzembe helyezhetők

Idő képzési hang feldolgozott adatok mennyiségétől függően változik. Tipikus alkalommal között tájékozódhat utterances több száz 30 perc a 20 000 utterances 40 óra.

> [!NOTE]
> Ingyenes előfizetés felhasználóinak egy hangtípusú betaníthatja egyszerre. Standard szintű előfizetés felhasználóinak betaníthatja három beszédhangot egyszerre. Ha eléri a korlátot, várjon, amíg legalább egyike a hangtípust képzési befejeződik, majd próbálkozzon újra.

## <a name="test-your-voice-font"></a>A hangtípusú tesztelése

A hangtípusú sikeres létrehozása után tesztelheti, mielőtt központilag telepítené azt használja. Kattintson a **teszt** a műveletek oszlopban. A teszt lap megjelenik a kiválasztott hangtípusú. A táblázat üres, ha még nem még küldte el a hang teszt irányuló.

![Saját Hangtípust, 2. rész](media/custom-voice/my-voice-fonts2.png)

Kattintson a **szöveggel teszt** , megjelenik egy legördülő menü szöveg kérések elküldésére szolgáló tábla címe alatt megjelenő gomb. A tesztelési kérelem egyszerű szöveges vagy SSML küldhet. A maximális bemeneti mérete 1024 karakter hosszúságú, beleértve az összes címke SSML kérelem. A szöveg nyelvét a hangtípusú nyelvi azonosnak kell lennie.

![Hangposta betűtípus tesztelése](media/custom-voice/voice-font-testing.png)

Miután a beviteli mód állapíthatja meg a beviteli mező kitöltése, kattintson a **Igen** a tesztelési kérelem elküldéséhez, és a teszt lapra való visszatéréshez. A táblázat most már tartalmaz egy bejegyzést, amely megfelel az új kérelmet, és a már ismerős Állapot oszlopban. Beszéd szintetizálásához néhány percet is igénybe vehet. Az Állapot oszlopban sikeres olvasását, letöltheti a szövegbevitel (egy `.txt` fájl) és a hangkimeneti (egy `.wav` fájl) és az utóbbi a minőségi audition.

![Hangalapú betűtípus tesztelés, 2. rész](media/custom-voice/voice-font-testing2.png)

## <a name="create-and-use-a-custom-endpoint"></a>Létrehozhat és használhat egyéni végpontok

Miután sikeresen létrehozta és tesztelte a hangalapú modell, üzembe helyezésekor egy egyéni szövegfelolvasás végpontot. Ezt a végpontot a szokásos végpont helyett majd a REST API-n keresztül szöveg-beszéd átalakítás kérések küldésekor használja. Egyéni végpontra az előfizetést, amellyel a betűtípus telepítése csak a nem hívható meg.

Hozzon létre egy új egyéni végpontot, válassza a **végpontok** az oldal tetején lévő egyéni beszédfelismerési menüből. Az üzembe helyezés lap jelenik meg, az a tábla az aktuális egyéni beszédfelismerési végpontokat, ha van ilyen.

Kattintson a **üzembe helyezése beszédhangot** gombra egy új végpont létrehozásához. A Create Endpoint"lapon megjelenik a táblázat első sorában a jelenlegi területi beállításhoz. Hozzon létre egy üzemelő példánya egy másik nyelvet, módosítsa a megjelenített területi beállítás. (Azt meg kell egyeznie a hangalapú végzi.) Adja meg a nevét és leírását, egyéni végpontra.

Az előfizetés menüből válassza a használni kívánt előfizetést. Ingyenes előfizetés felhasználóinak üzembe helyezett egyszerre csak egy modell rendelkezhet. Standard szintű előfizetés felhasználóinak legfeljebb 20 a saját egyéni beszédfelismerési végpontokat hozhat létre.

![Végpont létrehozása](media/custom-voice/create-endpoint.png)

A modell üzembe helyezni kiválasztása után kattintson az **létrehozás**. Az üzembe helyezés lap ismét megjelenik, most már az új végpont egy bejegyzés. Eltarthat néhány percig, hozza létre az új végpont. A központi telepítés állapotát a rendszer sikeres, a végpont készen áll a használatra.

![Az üzembe helyezett beszédhangot](media/custom-voice/my-deployed-voices.png)

A sikeres üzembe helyezés állapota, az üzembe helyezett hangtípusú végpontja megjelenik az üzembe helyezett beszédhangot táblázat. Ez az URI használhatja közvetlenül a HTTP-kérést.

A végpont tesztelését online érhető el az egyéni beszédfelismerési portálon keresztül. A végpont teszteléséhez válassza **tesztelés végpontok** az egyéni beszédfelismerési legördülő menüből. A végpont tesztelése oldalon jelenik meg. Válassza ki a központilag telepített hang, és adjon meg a szöveget, (az egyszerű szöveges vagy SSML formátumban) a szövegmezőbe.

> [!NOTE] 
> SSML, használatakor a `<voice>` címke a neve, mint az egyéni beszédfelismerési létrehozásakor adott meg kell adnia.

Kattintson a **lejátszása** felvesszük Önnel a szöveget az egyéni hangtípusú beszélt.

![Végpont tesztelése](media/custom-voice/endpoint-testing.png)

Az egyéni végpont funkcionálisan megegyezik a standard szintű, szöveg-hang transzformációs kérelmek használt végponthoz. Lásd: [REST API-val](rest-apis.md) további információt.

## <a name="next-steps"></a>További lépések

- [A beszédfelismerés próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A beszédfelismerést a C#-ban](quickstart-csharp-windows.md)
