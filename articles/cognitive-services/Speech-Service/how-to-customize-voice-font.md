---
title: Egyéni hangtípust létrehozása
titlesuffix: Azure Cognitive Services
description: Ez a cikk az szöveg-beszéd átalakítás hangalapú testreszabásával, amely lehetővé teszi, hogy hozzon létre egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló márka hangalapú áttekintése.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: panosper
ms.openlocfilehash: 181797cd8ba193d7200e5ece8b599ffa40f7185b
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413702"
---
# <a name="creating-custom-voice-fonts"></a>Egyéni hangtípust létrehozása

Szövegfelolvasás hangalapú testreszabási lehetővé teszi, hogy hozzon létre egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló hang, a saját márkáját: egy *hangtípusú.*

A hangtípusú létrehozásához, győződjön meg arról, a studio felvételt, és töltse fel a betanítási adatok, a kapcsolódó parancsprogramokat. A szolgáltatás ekkor létrehoz egy egyéni modell a rögzítése használatára vannak konfigurálva. E hangtípusú használatával beszédfelismerési szintetizálásához.

Az egy kisebb mennyiségű adatot egy megvalósíthatósági megkezdheti. De minél több adatot ad meg, annál természetes és professional a hangját hangzik.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-fiók és a Speech service-előfizetés szükséges. [Hozzon létre egyet](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) Ha még nem tette. Az előfizetés csatlakozhat az egyéni beszédfelismerési portal itt látható módon.

1. Jelentkezzen be a [egyéni beszédfelismerési portál](https://customvoice.ai) a alkalmazni a hozzáféréshez használt Microsoft-fiók használatával.

2. Lépjen a fiók neve, a jobb felső sarokban, alatt **előfizetések**.

    ![Előfizetések](media/custom-voice/subscriptions.png)

3. Az előfizetések oldalán válassza ki a **csatlakozás meglévő előfizetés**. Vegye figyelembe, hogy beszédszolgáltatások támogatja-e a különböző régiókban. Ellenőrizze a régió, ahol az előfizetési kulcs lett létrehozva, és győződjön meg arról, hogy kapcsolódni a kulcsot a megfelelő alárendelt portálon.  

4. Az előfizetési kulcs illessze be a táblába az alábbi példában látható módon. Minden előfizetési csomaghoz tartozik két kulcsot, és bármelyiket használhatja.

     ![Előfizetés hozzáadása](media/custom-voice/add-subscription.png)

Készen áll a go!

## <a name="prepare-recordings-and-transcripts"></a>És előkészítése

Audio-fájlokat, és a egy szövegfájlt, amely tartalmazza az átiratok a hangfájlok hangalapú betanítási adatkészletet áll.

Ezeket a fájlokat két módon készítheti elő. Írjon egy parancsfájlt, és a hangalapú szakembereket olvassa vagy használja a nyilvánosan elérhető hang és lefényképezze, szöveges vagy. Az utóbbi esetben az audio fájlokból, például a "um" és egyéb kitöltő hangok, akadozik, mumbled szavakat vagy mispronunciations disfluencies szerkesztése

Egy jó hangtípusú előállításához, győződjön meg arról, a felvételt csendes szoba magas színvonalú mikrofonnal. Egységes kötet, és beszéljen sebességét, beszéd térközt és kifejező mannerisms beszéd elengedhetetlenek, amellyel a nagyszerű digitális hangot.

Hozzon létre egy hang éles környezetben, azt javasoljuk egy professzionális felvétel studio és a hangalapú szakembereket használja. További információkért lásd: [hogyan rögzítése a voice-minták az egyéni beszédfelismerési](record-custom-voice-samples.md).

### <a name="audio-files"></a>Hangfájlok

Mindegyik hangfájlt tartalmaznia kell egy egyetlen utterance (például egyetlen mondatban vagy párbeszédpanel rendszer egyetlen menetben). Minden fájl nyelvével azonos nyelven kell lennie. (Többnyelvű egyéni beszédhangot nem támogatottak.) Hangfájlt is minden egyes rendelkeznie kell egy egyedi, a fájlnév-kiterjesztésű numerikus filename `.wav`.

Hangfájlok a következőképpen kell készíteni. Más formátum nem támogatott, és a rendszer elutasítja.

| **Tulajdonság** | **Érték** |
| ------------ | --------- |
| Fájlformátum  | RIFF (.wav)|
| Mintavételi ráta| legalább 16 000 Hz |
| Minta formátumával| A PCM, 16-bit |
| Fájlnév    | A numerikus `.wav` bővítmény |
| Archív formátum| .zip      |
| Maximális archívum mérete|200 MB|

> [!NOTE]
> mintavételi sebessége alacsonyabb, mint 16000 Hz .wav fájlokat a rendszer elutasítja. Egy .zip-fájlban található a regisztrálást megelőző szakaszban tanácsos a különböző mintavételi díjszabás, ha csak adott egyenlő vagy nagyobb, mint 16000 Hz fog kiterjedni.
> A portál jelenleg import .zip archívumok akár 200 MB. Azonban több archívumot is tölthető fel. Adatkészletek engedélyezett maximális számát az 10 .zip fájlokat ingyenes előfizetés felhasználóinak és 50 a standard szintű előfizetés felhasználóinak.

### <a name="transcripts"></a>Szövegekben

Az átírási fájl egy egyszerű szöveges fájlt (ANSI, UTF-8, az UTF-8-AJ, UTF-16-LE vagy UTF-16-BE). Az átírási fájl minden sorának rendelkeznie kell egy hangfájlt tabulátor (kódpont 9), végül pedig Videólejátszás követ nevét. Nem üres sorok engedélyezettek.

Példa:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Az egyéni beszédfelismerési rendszer átiratok normalizálja való visszaváltás a szöveg kis- és eltávolítani felesleges beolvasások absztrakt. Fontos, hogy az átiratok 100 %-os pontos beszédátírás, a megfelelő hangfelvételeket-e.

> [!TIP]
> Ha a készít éles szöveg-hang transzformációs beszédhangot, válassza ki a kimondott szöveg (vagy parancsfájlokat), amelyeknek veszi figyelembe a fiók fonetikus lefedettség és a hatékonyságot. Az eredmények problémákat okoz a következőket szeretne? [Az egyéni beszédfelismerési csapattól](mailto:speechsupport@microsoft.com) található ki kellene USA kapcsolatos további részletekért.

## <a name="upload-your-datasets"></a>Az adathalmazok feltöltése

A hangfájl archív és szövegekben előkészítése után töltse fel őket keresztül a [egyéni beszédfelismerési portál](https://customvoice.ai).

> [!NOTE]
> Adatkészletek nem szerkeszthető, miután azok fel lett töltve. Ha elfelejti szövegekben az egyes zenei fájlok tartalmazzák, például vagy véletlenül válassza ki a megfelelő nemek, újra fel kell tölteni a teljes adatkészletet. Ellenőrizze az adatkészlet és a beállításokat alaposan feltöltésének megkezdése előtt.

1. Jelentkezzen be az Portalra.

2. A főoldalon alatt **egyéni beszédfelismerési**válassza **adatok**.   

    A **saját hangalapú** tábla jelenik meg. Emellett akkor üres, ha még még nem töltött hangalapú adatkészletekkel.

3. Nyissa meg a feltöltése egy új adatkészlet, válassza ki a **adatimportálás**.

    ![Voice-adatok importálása](media/custom-voice/import-voice-data.png)

4. Adja meg egy nevet és leírást a mezőkben biztosított.

5. Válassza ki a területi beállításait a hangtípusokkal. Ellenőrizze, hogy a területi információval kapcsolatos adatok a felvétel és a parancsfájlok nyelvének megfelelő.

6. Válassza ki a beszélő, amelynek használata hangalapú tartalmazzák.

7. Válassza ki a parancsfájl és hangfájlok feltölteni.

8. Válassza ki **importálás** feltölteni az adatokat. A nagyobb adatkészletek esetében importálása eltarthat néhány percig.

> [!NOTE]
> Ingyenes előfizetés felhasználóinak egyszerre két adatkészletet tölthet fel. Standard szintű előfizetés felhasználóinak is feltölthet egyszerre öt adatkészletek. Ha eléri a korlátot, várjon, amíg legalább egy, az adatkészletek importálásának befejezése. Próbálkozzon újra.

Ha a feltöltés befejeződött, a **saját Beszédadatait** ekkor újra megjelenik a táblázat. Egy bejegyzést, amely megfelel az imént feltöltött adatkészlet kell megjelennie.

Az adatkészletek automatikusan érvényesíti feltöltés után. Adatok érvényesítése tartalmaz egy sor ellenőrzést a hang fájlokat, és ellenőrizze a fájlformátum, a méretet, és a mintavételi ráta. Ellenőrzések beszédátírási fájlokról ellenőrizze a fájlformátum, és hajtsa végre az egyes szöveg normalizálási. A kimondott szöveg vannak megjelenített érzéseket beszédfelismerés. Majd az eredményül kapott szöveg rendszer összehasonlítja a megadott szövegben.

![Saját Beszédadatait](media/custom-voice/my-voice-data.png)

Az alábbi táblázat az importált adatkészletek feldolgozása állapotai:

| Állapot | Jelentés
| ----- | -------
| `NotStarted` | Az adatkészlet megkaptuk, és a feldolgozáshoz sorba állított.
| `Running` | Az adatkészlet érvényesítése.
| `Succeeded` | Az adatkészlet ellenőrzése megtörtént, és előfordulhat, hogy most már használható egy hangtípusú hozhat létre.

Érvényesítés befejezése után láthatja az egyes az adatkészleteket az egyező kimondott szöveg teljes száma a **utterance (kifejezés)** oszlop.

-Jelentés írásmódja pontszámokat és a zajszint ellenőrzéssel az egyes felvételeit töltheti le. A írásmódja pontszám értéke 0 és 100. 70 alatti általában azt jelzi, hogy beszéd hiba vagy a parancsfájl nem egyeznek. Nehéz hangsúlyos csökkenthető a írásmódja pontszám, és hatással van a generált digitális hangot.

Nagyobb jel zaj memóriaarányt (SNR) azt jelzi, hogy alacsonyabb lényeget a hangot a. Felvétel a professzionális studios által általában egy több mint 50 SNR is elérheti. Az alábbi 20-SNR hang nyilvánvaló lényeget a generált hangminta eredményezhet.

Vegye figyelembe, hogy az alacsony írásmódja pontszámok vagy gyenge jel zaj arányok megszólalásokat a felvétel. Ha nem tud újra rögzíteni, ezeket a kimondott szöveg, előfordulhat, hogy zárja ki a az adatkészlet.

## <a name="build-your-voice-font"></a>A hangtípusú létrehozása

Az adatkészlet érvényesítése után használhatja az egyéni hangtípusú hozhat létre.

1.  Az a **egyéni beszédfelismerési** legördülő menüben válassza a **modellek**.

    A **saját Hangtípust** tábla megjelenik, bármilyen egyéni hangtípust ajánlati már létrehozott.

1. A tábla címe területen válassza ki a **beszédhangot létrehozása**.

    Az egy hangtípusú létrehozására szolgáló oldal jelenik meg. A tábla első sorát mutatja a jelenlegi területi beállításhoz. A területi beállítások egy hang létrehozása egy másik nyelven. A területi beállítás azonosnak kell lennie, az adatkészletek a hangalapú létrehozásához használt.

1. Az adatkészlet feltöltésekor ugyanúgy adjon meg egy nevet és leírást, hogy azonosíthatók legyenek az ebben a modellben.

    Gondosan válasszon egy nevet. A név, az Itt adhatja meg a hangalapú beszédszintézishez a kérelemből a bemeneti SSML részeként neve lesz. Csak betűket, számokat és néhány írásjeleket például `-`, `_`, és `(', ')` engedélyezettek.

    Egyik gyakori felhasználási a **leírás** mező jegyezze fel a modell létrehozásához használt az adatkészletek nevei.

1. Válassza ki a hangtípusú tartalmazzák. Meg kell egyeznie az adatkészlet tartalmazzák.

1. Válassza ki a képzés a hangtípusú használni kívánt adatkészlet(ek). Összes adatkészlet, amelyet használhat az azonos speaker kell lennie.

1. Kattintson a **létrehozás** a hangtípusú létrehozásának megkezdéséhez.

    ![Create Model](media/custom-voice/create-model.png)

Az új modell megjelenik a **saját Hangtípust** tábla.

![Saját Hangtípust](media/custom-voice/my-voice-fonts.png)

Az állapot látható a folyamat az adatkészlet egy hangtípusú átalakítása tükrözi, itt látható módon.

| Állapot | Jelentés
| ----- | -------
| `NotStarted` | A feldolgozáshoz sorba állított hangalapú betűtípus létrehozási vonatkozó kérelmét.
| `Running` | A hangtípusú létrehozása folyamatban van.
| `Succeeded` | A hangtípusú létrejött, és is üzembe helyezhetők.

Idő képzési hang feldolgozott adatok mennyiségétől függően változik. Tipikus alkalommal között tájékozódhat utterances több száz 30 perc a 20 000 utterances 40 óra.

> [!NOTE]
> Ingyenes előfizetés felhasználóinak egy hangtípusú betaníthatja egyszerre. Standard szintű előfizetés felhasználóinak betaníthatja három beszédhangot egyszerre. Ha eléri a korlátot, várjon, amíg legalább egyike a hangtípust képzési befejeződik, és próbálkozzon újra.

## <a name="test-your-voice-font"></a>A hangtípusú tesztelése

Miután a hangtípusú sikeresen létrejött, mielőtt központilag telepítené azt használja tesztelheti. Az a **műveletek** oszlopában válassza **teszt**. A teszt lap megjelenik a kiválasztott hangtípusú. A táblázat üres, ha még nem még küldte el a hang teszt irányuló.

A szöveg a kérelmet egy előugró menü megjelenítéséhez válassza a **szöveggel teszt** tábla címe alatt megjelenő gomb. A tesztelési kérelem egyszerű szöveges vagy SSML küldhet. A maximális bemeneti mérete 1024 karakter hosszúságú, beleértve az összes címke a SSML kéréshez. A szöveg nyelvét a hangtípusú nyelvi azonosnak kell lennie.

A beviteli mód állapíthatja meg a beviteli mező kitöltésével, után válassza ki a **Igen** a tesztelési kérelem elküldéséhez, és a teszt lapra való visszatéréshez. A táblázat most már tartalmaz egy bejegyzést, amely megfelel az új kérés és az Állapot oszlopban. Beszéd szintetizálásához néhány percet is igénybe vehet. Amikor az Állapot oszlop szerint **sikeres**, letöltheti a szövegbevitel (egy `.txt` fájl) és a hangkimeneti (egy `.wav` fájl), és az utóbbi a minőségi audition.

## <a name="create-and-use-a-custom-endpoint"></a>Létrehozhat és használhat egyéni végpontok

Miután sikeresen létrehozta és tesztelte a hangalapú modell, üzembe helyezésekor egy egyéni szöveg-hang transzformációs végpontot. Ezt a végpontot a szokásos végpont helyett majd a REST API-n keresztül szöveg-hang transzformációs kérések küldésekor használja. Egyéni végpontra az előfizetést, amellyel a betűtípus telepítése csak a nem hívható meg.

Hozzon létre egy új egyéni végpontot, válassza a **végpontok** származó a **egyéni beszédfelismerési** az oldal tetején lévő menüben. A **saját telepített beszédhangot** lap, a tábla az aktuális egyéni beszédfelismerési végpontokat, ha van ilyen. A jelenlegi területi beállításhoz tükrözi a tábla első sorát. Hozzon létre egy üzemelő példánya egy másik nyelvet, módosítsa a megjelenített területi beállítás. (Azt meg kell egyeznie a hangalapú telepíti.)

Új végpont létrehozásához válassza a **üzembe helyezése beszédhangot** gombra. Adja meg a nevét és leírását, egyéni végpontra.

Az a **előfizetés** menüben válassza ki a használni kívánt előfizetést. Ingyenes előfizetés felhasználóinak üzembe helyezett egyszerre csak egy modell rendelkezhet. Standard szintű előfizetés felhasználóinak legfeljebb 20 a saját egyéni beszédfelismerési végpontokat hozhat létre.

![Végpont létrehozása](media/custom-voice/create-endpoint.png)

Miután kiválasztotta a modell üzembe helyezni, válassza ki a **létrehozás**. A **saját telepített beszédhangot** lap ismét megjelenik, most már az új végpont egy bejegyzés. Eltarthat néhány percig, hozza létre az új végpont. Ha a központi telepítés állapota **sikeres**, a végpont készen áll a használatra.

![Az üzembe helyezett beszédhangot](media/custom-voice/my-deployed-voices.png)

Ha a központi telepítési állapota **sikeres**, megjelenik az üzembe helyezett hangtípusú végpontja a **saját telepített beszédhangot** tábla. Ez az URI használhatja közvetlenül a HTTP-kérést.

A végpont tesztelését online érhető el az egyéni beszédfelismerési portálon keresztül. A végpont teszteléséhez válassza **tesztelés végpontok** származó a **egyéni beszédfelismerési** legördülő menüből. A végpont tesztelése oldalon jelenik meg. Válasszon egy üzembe helyezett egyéni beszédfelismerési, és írja be a szöveget, (az egyszerű szöveges vagy SSML formátumban) a szövegmezőbe.

> [!NOTE]
> SSML, használatakor a `<voice>` címkével kell adnia a nevét, hogy egyezzenek a egyéni beszédfelismerési létrehozásakor adott meg. Ha küld egyszerű szöveges formátumban, az egyéni beszédfelismerési mindig használja.

Hallgassa meg az egyéni hangtípusú beszélt szövegét, jelölje be **lejátszása**.

![Végpont tesztelése](media/custom-voice/endpoint-testing.png)

Az egyéni végpont funkcionálisan megegyezik a standard szintű végpontot, amely a szöveg-hang transzformációs kéréseket szolgál. Lásd: [REST API-val](rest-apis.md) további információt.

## <a name="language-support"></a>Nyelvi támogatás

Hangalapú testreszabása az alábbi nyelveken érhető el:

| Nyelv | Területi beállítás |
|----------|--------|
| Kínai (Spanyolország) | zh-CN |
| Angol (Egyesült Államok) | en-US |
| francia | FR-FR |
| német | de-DE |
| olasz | it-IT |

> [!NOTE]
> Francia, német és olasz hangalapú képzés, mint 2000 utterances adatkészlet kezdődik. Angol nyelvű kínai kétnyelvű modelleket is, mint 2000 utterances adatkészlet használata támogatott.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Rögzítése a voice-minták](record-custom-voice-samples.md)
