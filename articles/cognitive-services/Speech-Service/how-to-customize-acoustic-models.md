---
title: Akusztikai modell létrehozása a Speech Service segítségével – Microsoft Cognitive Services | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre akusztikai modellt a Microsoft Cognitive Services Speech Service szolgáltatásával.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 3e247b6f81a9306cbad630d42c3a41d8ce3045a5
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345124"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Oktatóanyag: Egyéni akusztikai modell létrehozása

Egy egyéni akusztikai modell létrehozása akkor hasznos, ha az alkalmazása egy adott környezetben (például autóban), adott felvételi eszközökkel vagy feltételek mellett, illetve adott felhasználói körben történő használatra készült. A példák közé tartozik az akcentussal való beszéd, a meghatározott háttérzajok vagy egy adott mikrofon használata a felvételhez.

Ezen az oldalon megtanulhatja a következőket:
> [!div class="checklist"]
> * Az adatok előkészítése
> * Az akusztikai adatkészlet importálása
> * Egyéni akusztikai modell létrehozása

Ha még nincs Cognitive Services-fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/try/cognitive-services), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

Nyissa meg a [Cognitive Services-előfizetések](https://customspeech.ai/Subscriptions) oldalt, és ellenőrizze, hogy Cognitive Services-fiókja egy előfizetés alá tartozik-e.

A **Connect existing subscription** (Meglévő előfizetés csatlakoztatása) gombra kattintva csatlakozhat egy, az Azure Portalon létrehozott Speech Service-előfizetéshez.

A Speech Service-előfizetések az Azure Portalon való létrehozásával kapcsolatos útmutatásért lásd az [első lépések](get-started.md) oldalt.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Egy adott tartományhoz tartozó akusztikai modell testreszabásához beszédadatok gyűjteményére van szükség. Ez néhány kimondott szövegtől a több száz órányi beszédig terjedhet. A gyűjtemény beszédadatokat tartalmazó hangfájlokból, valamint az ezek mindegyikének szöveges átiratát tartalmazó szövegfájlból áll. A hangadatoknak reprezentatívnak kell lenniük arra a beszédhelyzetre nézve, amelyhez a beszédfelismerőt használni kívánja.

Például:

*   Ha jobb beszédfelismerést szeretne elérni egy zajos gyári környezetben, a hangfájloknak zajos gyárban felvett beszédmintákat kell tartalmazniuk.
*   Ha egy adott beszélőre szeretné optimalizálni a felismerés hatékonyságát – például ha Franklin D. Roosevelt elnök kandalló előtti beszélgetéseinek átiratát szeretné elkészíteni –, akkor az audiofájloknak minél több, csak az adott beszélőtől származó példát kell tartalmazniuk.

Az akusztikai modell testreszabásához készült akusztikai adatkészlet két részből áll: (1) a beszédadatokat tartalmazó hangfájlok készletéből, valamint (2) a hangfájlok mindegyikének szöveges átiratát tartalmazó fájlból.

### <a name="audio-data-recommendations"></a>Hangadatokkal kapcsolatos javaslatok

*   Az adatkészlet összes hangfájlját WAV (RIFF) hangformátumban kell tárolni.
*   A hanganyag mintavételi frekvenciájának 8 kHz-nek vagy 16 kHz-nek kell lennie, a mintaértékeket pedig tömörítetlen, 16 bites PCM előjeles egész számokként (rövid jelek) kell tárolni.
*   Csak az egycsatornás (mono) hangfájlok támogatottak.
*   A hangfájlok hosszának 100 ms és 1 perc között kell lennie. A hangfájloknak ideális esetben legalább 100 ms csenddel kell kezdődniük és végződniük – az 500 ms és 1 másodperc közötti érték az általános.
*   Ha háttérzaj van az adatokban, javasoljuk, hogy a beszédtartalom előtt és/vagy után hosszabb, például néhány másodpercnyi csendes szakaszokat tartalmazó minták is legyenek a példák között.
*   Mindegyik hangfájlnak egyetlen kimondott szövegből kell állnia, például egyetlen mondatból vagy diktálásból, egy kérdésből, vagy egy párbeszéd egyetlen fordulójából.
*   Az adatkészlet mindegyik hangfájljának egyedi fájlnévvel és „wav” kiterjesztéssel kell rendelkeznie.
*   A hangfájlok készletét egyetlen, alkönyvtárak nélküli mappába kell helyezni, majd az egész készletet egyetlen ZIP fájlarchívumba kell tömöríteni.

> [!NOTE]
> A webes portálon keresztüli adatimportálás jelenleg 2 GB-ra korlátozott, ezért az akusztikai adatkészletek maximális mérete is 2 GB. Ez nagyjából 17 órányi, 16 kHz-es frekvencián felvett, vagy 34 órányi, 8 kHz-es frekvencián felvett hanganyagnak felel meg. A hangadatokra vonatkozó főbb követelményeket az alábbi táblázat foglalja össze.
>

| Tulajdonság | Érték |
|---------- |----------|
| Fájlformátum | RIFF (WAV) |
| Mintavételezési frekvencia | 8000 Hz vagy 16 000 Hz |
| Csatornák | 1 (mono) |
| Minták formátuma | PCM, 16 bites egész számok |
| Fájlok időtartama | 0,1 másodperc és 60 másodperc között |
| Csend időtartama | >0,1 másodperc |
| Archívum formátuma | Irányítószám |
| Archívum maximális mérete | 2 GB |

## <a name="language-support"></a>Nyelvi támogatás

Az alábbi nyelvek támogatottak az egyéni **diktálási** nyelvi modellek esetében.

A [támogatott nyelvek](supported-languages.md) teljes listájának megtekintéséhez kattintson ide.

### <a name="transcriptions-for-the-audio-dataset"></a>A hangadat-készlet átiratai

Az összes WAV fájl átiratát egyetlen, egyszerű szöveges fájlnak kell tartalmaznia. Az átiratfájl minden sorában egy hangfájl nevének és az annak megfelelő átiratnak kell szerepelnie. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani.

  Például:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Az átiratnak UTF-8 Bom kódolásúnak kell lennie

Az átiratokat a rendszer normalizálja, hogy fel tudja őket dolgozni. Bizonyos fontosabb normalizálási tevékenységeket azonban a felhasználónak kell végrehajtania az adatok a Custom Speech Service szolgáltatásba való feltöltése _előtt_. Az átiratok előkészítése során használandó megfelelő nyelv meghatározásához lásd az [átírási irányelvekkel](prepare-transcription.md) foglalkozó szakaszt.

Az alábbi lépések a [Speech Service Portal](https://customspeech.ai) használatával hajthatók végre.

## <a name="import-the-acoustic-data-set"></a>Az akusztikai adatkészlet importálása

Ha a hangfájlok és az átiratok előkészítése megtörtént, importálhatók a szolgáltatás webes portáljára.

Ehhez először is győződjön meg róla, hogy be van jelentkezve a [Speech Service Portalra](https://customspeech.ai). Ezután kattintson a felső sávon a „Custom Speech” legördülő menüre, és válassza az „Adaptation Data” (Adaptációs adatok) lehetőséget. Ha most tölt fel először adatokat a Custom Speech Service szolgáltatásba, egy üres tábla jelenik meg „Datasets” (Adatkészletek) néven. 

Kattintson az „Acoustic Datasets” (Akusztikai adatkészletek) sorban az „Import” (Importálás) gombra, és a webhely megjeleníti az új adatkészlet feltöltésére szolgáló oldalt.

![kipróbálás](media/stt/speech-acoustic-datasets-import.png)

Adjon meg egy _nevet_ és egy _leírást_ a megfelelő szövegmezőkben. A beszédes leírások segítenek a különböző feltöltött adatkészletek számon tartásában. Ezután kattintson a „Choose File” (Fájl kiválasztása) gombra a „Transcription file” (Átiratfájl) és a „WAV files” (WAV fájlok) esetében, majd válassza ki ezeknek megfelelően az egyszerű szöveges átiratfájlt és a WAV fájlok zip-archívumát. Ha elvégezte az előkészítést, kattintson az „Import” (Importálás) gombra az adatok feltöltéséhez. A rendszer ekkor feltölti az adatokat. A nagyobb adatkészletek esetében ez eltarthat néhány percig.

Ha a feltöltés befejeződött, a rendszer visszalép az „Acoustic Datasets” (Akusztikai adatkészletek) adattáblára, és megjelenik egy, az akusztikai adatkészletre vonatkozó bejegyzés. A rendszer ehhez egy egyedi azonosítót (GUID) rendelt hozzá. Az adatok aktuális állapota is látható a táblában. A feldolgozásra várva az állapot „NotStarted” (Nincs elkezdve), az érvényesítés során „Running” (Fut), majd „Complete” (Kész), ha az adatok használatra készek.

Az adatok érvényesítéséhez a rendszer egy sor ellenőrzést végez – ellenőrzi a hangfájlok fájlformátumát, hosszát és mintavételezési frekvenciáját, valamint az átiratfájlok fájlformátumát, illetve némi szövegnormalizálást hajt végre.

Ha az állapot „Succeeded” (Sikeres), a „Details” (Részletek) gombra kattintva megtekintheti a nyelvi adatok ellenőrzésére vonatkozó jelentést. Ebben látható az ellenőrzésen sikeresen és sikertelenül átment kimondott szövegek száma, valamint a sikertelen kimondott szövegek részletei. Az alábbi példában két WAV fájl bizonyult sikertelennek az ellenőrzés során helytelen hangformátum miatt (ebben az adatkészletben az egyik fájl helytelen mintavételezési frekvenciával, a másik pedig helytelen fájlformátummal rendelkezett).

![kipróbálás](media/stt/speech-acoustic-datasets-report.png)

Ha az adatkészlet „Name” (Név) vagy „Description” (Leírás) értékét bármikor módosítani szeretné, kattintson az „Edit” (Szerkesztés) hivatkozásra, és módosítsa a megfelelő bejegyzéseket. A hangfájlok és az átiratok nem módosíthatók.

## <a name="create-a-custom-acoustic-model"></a>Egyéni akusztikai modell létrehozása

Ha a nyelvi adatkészlet „Complete” (Kész) állapotra vált, a használatával létrehozható egy egyéni akusztikai modell. Ehhez kattintson az „Acoustic Models” (Akusztikai modellek) elemre a „Custom Speech” legördülő menüben. Itt egy „Your models” (Saját modellek) nevű táblázatban láthatja egyéni akusztikai modelljeinek listáját. Ha először használja ezt a funkciót, a táblázat üres lesz. A tábla címe az aktuális területi beállítást mutatja. Az akusztikai modellek jelenleg csak az amerikai angol beállításhoz hozhatók létre.

Új modell létrehozásához kattintson a „Create New” (Új létrehozása) gombra a tábla címe alatt. Ahogy korábban is, adja meg a nevet és egy leírást, amelyek alapján azonosíthatja a modellt. Például a „Description” (Leírás) mezőben rögzíthető, hogy a modell mely kiindulási modell és akusztikai adatok felhasználásával készült. Ezután válassza a „Base Acoustic Model” (Alap akusztikai modell) lehetőséget a legördülő menüből. Az alapmodell szolgál a testreszabás kiindulópontjaként. Két alap akusztikai modell közül választhat. A _Microsoft Search and Dictation AM_ (Microsoft keresési és diktálási akusztikai modellek) az alkalmazásoknak szóló beszéd, például parancsok, keresőlekérdezések vagy diktálás feldolgozásához alkalmas. A _Microsoft Conversational model_ (Microsoft beszélgetési modell) a beszélgetési stílusban elhangzott beszélgetések feldolgozásához alkalmas. Az ilyen típusú beszéd általában másik személynek szól, és telefonos ügyfélszolgálatokon vagy értekezleteken hangzik el. A részleges eredmények késleltetése a Conversational-modellek esetében magasabb, mint a Search and Dictation-modellek esetében.

> [!NOTE]
> Jelenleg is zajlik az új Universal modell bevezetése, amelynek célja az összes beszédhelyzet kezelése. A fent említett modellek is nyilvánosan elérhetők maradnak.

Következő lépésként válassza ki a személyre szabáshoz használni kívánt akusztikai adatkészletet a legördülő menüből.

![kipróbálás](media/stt/speech-acoustic-models-create2.png)

Lehetősége van tesztelni az új modell pontosságát a feldolgozás befejezése után. Ez egy megadott akusztikai adatkészletnek az egyéni akusztikai modell használatával történő diktálásos értékelését, majd az arról való jelentéskészítést jelenti. A teszt elvégzéséhez jelölje be az „Accuracy Testing” (Pontossági tesztelés) jelölőnégyzetet. Ezután válassza ki a nyelvi modellt a legördülő menüből. Ha még nem hozott létre egyéni nyelvi modellt, a legördülő listában csak az alap nyelvi modellek jelennek meg. Tekintse meg az alap nyelvi modellek [leírását](how-to-customize-language-model.md) az útmutatóban, és válassza ki a leginkább megfelelőt.

Végül válassza ki az egyéni modell értékeléshez használni kívánt akusztikai adatkészletet. Ha pontossági tesztelést végez, fontos olyan akusztikai adatkészletet kiválasztani, amelyik különbözik a modell elkészítéséhez felhasznált adatkészlettől, hogy valós képet kaphasson a modell teljesítményéről. A betanítási adatokon végzett pontossági tesztelés nem teszi lehetővé az alkalmazott modell valós körülmények közötti működésének értékelését. Az eredmény túlságosan optimista lesz. Azt is vegye figyelembe, hogy a pontossági tesztelés 1000 kimondott szövegre korlátozott. Ha a teszteléshez használt akusztikai adatkészlet ennél nagyobb, csak az első 1000 kimondott szöveg lesz értékelve.

Amikor készen áll a személyreszabási folyamat indítására, kattintson a „Create” (Létrehozás) gombra.

Az akusztikai modellek táblázatában az új modellhez tartozó bejegyzés jelenik meg. A táblázatban látható a folyamat állapota. Az állapot a következő értékeket veheti fel: „Waiting” (Várakozik), „Processing” (Feldolgozás) és „Complete” (Kész).

![kipróbálás](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Beszéd felismerése C# nyelven](quickstart-csharp-windows.md)
- [Git-mintaadatok](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
