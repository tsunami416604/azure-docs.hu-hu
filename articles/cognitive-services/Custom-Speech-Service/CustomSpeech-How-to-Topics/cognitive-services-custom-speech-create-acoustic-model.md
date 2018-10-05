---
title: 'Oktatóanyag: Akusztikai modell létrehozása – Custom Speech Service'
titlesuffix: Azure Cognitive Services
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre akusztikai modellt a Custom Speech Service-szel.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 72c5a0dfb8f33f273ba850378c1fefeef82b4d7a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220230"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Oktatóanyag: Egyéni akusztikai modell létrehozása

Ebben az oktatóanyagban létrehoz egy egyéni akusztikai modellt, amellyel az alkalmazása képes felismerni a várt beszédadatokat. Egy egyéni akusztikai modell létrehozása akkor hasznos, ha az alkalmazása egy adott környezetben (például zajos gyárban) vagy adott felhasználói körben történő használatra készült.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az adatok előkészítése
> * Az akusztikai adatkészlet importálása
> * Egyéni akusztikai modell létrehozása

Ha még nincs Cognitive Services-fiókja, hozzon létre egy [ingyenes fiókot](https://cris.ai), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

Nyissa meg a [Cognitive Services-előfizetések](https://cris.ai/Subscriptions) oldalt, és ellenőrizze, hogy Cognitive Services-fiókja egy előfizetés alá tartozik-e.

Ha nincsenek előfizetések listázva, a **Get free subscription** (Ingyenes előfizetés igénylése) gombra kattintva létrehozhat egy fiókot a Cognitive Services szolgáltatásban. Vagy a **Connect existing subscription** (Meglévő előfizetés csatlakoztatása) gombra kattintva csatlakozhat egy, az Azure Portalon létrehozott Custom Search Service-előfizetéshez.

A Custom Search Service-előfizetések az Azure Portalon való létrehozásával kapcsolatos útmutatásért lásd a [Cognitive Services API-fiókok az Azure Portalon való létrehozását](../../cognitive-services-apis-create-account.md) ismertető cikket.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Egy adott tartományhoz tartozó akusztikai modell testreszabásához beszédadatok gyűjteményére van szükség. A gyűjtemény beszédadatokat tartalmazó hangfájlokból, valamint az ezek mindegyikének szöveges átiratát tartalmazó szövegfájlból áll. A hangadatoknak reprezentatívnak kell lenniük arra a beszédhelyzetre nézve, amelyhez a beszédfelismerőt használni kívánja.

Például:

*   Ha jobb beszédfelismerést szeretne elérni egy zajos gyári környezetben, a hangfájloknak zajos gyárban felvett beszédmintákat kell tartalmazniuk.
<a name="Preparing data to customize the acoustic model"></a>
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

### <a name="transcriptions"></a>Átiratok

Az összes WAV fájl átiratát egyetlen, egyszerű szöveges fájlnak kell tartalmaznia. Az átiratfájl minden sorában egy hangfájl nevének és az annak megfelelő átiratnak kell szerepelnie. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani.

  Például:
```
  speech01.wav  speech recognition is awesome

  speech02.wav  the quick brown fox jumped all over the place

  speech03.wav  the lazy dog was not amused
```

Az átiratokat a rendszer normalizálja, hogy fel tudja őket dolgozni. Bizonyos fontosabb normalizálási tevékenységeket azonban a felhasználónak kell végrehajtania az adatok a Custom Speech Service szolgáltatásba való feltöltése _előtt_. Az átiratok előkészítése során használandó megfelelő nyelv meghatározásához lásd az [átírási irányelvekkel](cognitive-services-custom-speech-transcription-guidelines.md) foglalkozó szakaszt.

Az alábbi lépések a [Custom Speech Service Portal](https://cris.ai) használatával hajthatók végre. 

## <a name="import-the-acoustic-data-set"></a>Az akusztikai adatkészlet importálása

Ha a hangfájlok és az átiratok előkészítése megtörtént, importálhatók a szolgáltatás webes portáljára.

Ehhez először is győződjön meg róla, hogy be van jelentkezve a [Custom Speech Service Portalra](https://cris.ai). Ezután kattintson a felső sávon a „Custom Speech” legördülő menüre, és válassza az „Adaptation Data” (Adaptációs adatok) lehetőséget. Ha most tölt fel először adatokat a Custom Speech Service szolgáltatásba, egy üres tábla jelenik meg „Datasets” (Adatkészletek) néven. 

Kattintson az „Acoustic Datasets” (Akusztikai adatkészletek) sorban az „Import” (Importálás) gombra, és a webhely megjeleníti az új adatkészlet feltöltésére szolgáló oldalt.

![kipróbálás](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-datasets-import.png)

Adjon meg egy _nevet_ és egy _leírást_ a megfelelő szövegmezőkben. Ezek segítenek a különböző feltöltött adatkészletek számon tartásában. Ezután kattintson a „Choose File” (Fájl kiválasztása) gombra a „Transcription file” (Átiratfájl) és a „WAV files” (WAV fájlok) esetében, majd válassza ki ezeknek megfelelően az egyszerű szöveges átiratfájlt és a WAV fájlok zip-archívumát. Ha ezt elvégezte, kattintson az „Import” (Importálás) gombra az adatok feltöltéséhez. A rendszer ekkor feltölti az adatokat. A nagyobb adatkészletek esetében ez eltarthat néhány percig.

Ha a feltöltés befejeződött, a rendszer visszalép az „Acoustic Datasets” (Akusztikai adatkészletek) adattáblára, és megjelenik egy, az akusztikai adatkészletre vonatkozó bejegyzés. A rendszer ehhez egy egyedi azonosítót (GUID) rendelt hozzá. Az adatok aktuális állapota is látható a táblában. A feldolgozásra várva az állapot „Waiting” (Várakozik), az érvényesítés során „Processing” (Feldolgozás), majd „Complete” (Kész), ha az adatok használatra készek.

Az adatok érvényesítéséhez a rendszer egy sor ellenőrzést végez – ellenőrzi a hangfájlok fájlformátumát, hosszát és mintavételezési frekvenciáját, valamint az átiratfájlok fájlformátumát, illetve némi szövegnormalizálást hajt végre.

Ha az állapot „Complete” (Befejezve), a „Details” (Részletek) gombra kattintva megtekintheti a nyelvi adatok ellenőrzésére vonatkozó jelentést. Ebben látható az ellenőrzésen sikeresen és sikertelenül átment kimondott szövegek száma, valamint a sikertelen kimondott szövegek részletei. Az alábbi példában két WAV fájl bizonyult sikertelennek az ellenőrzés során helytelen hangformátum miatt (ebben az adatkészletben az egyik fájl helytelen mintavételezési frekvenciával, a másik pedig helytelen fájlformátummal rendelkezett).

![kipróbálás](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-datasets-report.png)

Ha az adatkészlet „Name” (Név) vagy „Description” (Leírás) értékét bármikor módosítani szeretné, kattintson az „Edit” (Szerkesztés) hivatkozásra, és módosítsa a megfelelő bejegyzéseket. Vegye figyelembe, hogy a hangfájlok és az átiratok nem módosíthatók.

## <a name="create-a-custom-acoustic-model"></a>Egyéni akusztikai modell létrehozása

Ha a nyelvi adatkészlet „Complete” (Kész) állapotra vált, a használatával létrehozható egy egyéni akusztikai modell. Ehhez kattintson az „Acoustic Models” (Akusztikai modellek) elemre a „Custom Speech” legördülő menüben. Itt egy „Your models” (Saját modellek) nevű táblázatban láthatja egyéni akusztikai modelljeinek listáját. Ha először használja ezt a funkciót, a táblázat üres lesz. A tábla címe az aktuális területi beállítást mutatja. Az akusztikai modellek jelenleg csak az amerikai angol beállításhoz hozhatók létre.

Új modell létrehozásához kattintson a „Create New” (Új létrehozása) gombra a tábla címe alatt. Ahogy korábban is, adja meg a nevet és egy leírást, amelyek alapján azonosíthatja a modellt. Például a „Description” (Leírás) mezőben rögzíthető, hogy a modell mely kiindulási modell és akusztikai adatok felhasználásával készült. Ezután válassza a „Base Acoustic Model” (Alap akusztikai modell) lehetőséget a legördülő menüből. Az alapmodell szolgál a testreszabás kiindulópontjaként. Két alap akusztikai modell közül választhat. A _Microsoft Search and Dictation AM_ (Microsoft keresési és diktálási akusztikai modell) az alkalmazásoknak szóló beszéd, például parancsok, keresőlekérdezések vagy diktálás feldolgozásához alkalmas. A _Microsoft Conversational model_ (Microsoft beszélgetési modell) a beszélgetési stílusban elhangzott beszélgetések feldolgozásához alkalmas. Az ilyen típusú beszéd általában másik személynek szól, és telefonos ügyfélszolgálatokon vagy értekezleteken hangzik el. Vegye figyelembe, hogy a részleges eredmények késleltetése a Conversational-modellek esetében magasabb, mint a Search and Dictation-modellek esetében.

Következő lépésként válassza ki a személyre szabáshoz használni kívánt akusztikai adatkészletet a legördülő menüből.

![kipróbálás](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-models-create2.png)

Lehetősége van offline tesztelni az új modellt a feldolgozás befejezése után. Ez egy megadott akusztikai adatkészletnek az egyéni akusztikai modell használatával történő diktálásos értékelését, majd az arról való jelentéskészítést jelenti. A teszt elvégzéséhez jelölje be az „Accuracy Testing” (Pontossági tesztelés) jelölőnégyzetet. Ezután válassza ki a nyelvi modellt a legördülő menüből. Ha még nem hozott létre egyéni nyelvi modellt, a legördülő listában csak az alap nyelvi modellek jelennek meg. Tekintse meg az alap nyelvi modellek [leírását](cognitive-services-custom-speech-create-language-model.md) az útmutatóban, és válassza ki a leginkább megfelelőt.

Végül válassza ki az egyéni modell értékeléshez használni kívánt akusztikai adatkészletet. Ha pontossági tesztelést végez, fontos olyan akusztikai adatkészletet kiválasztani, amelyik különbözik a modell elkészítéséhez felhasznált adatkészlettől, hogy valós képet kaphasson a modell teljesítményéről. Azt is vegye figyelembe, hogy az offline tesztelés 1000 kimondott szövegre korlátozott. Ha a teszteléshez használt akusztikai adatkészlet ennél nagyobb, csak az első 1000 kimondott szöveg lesz értékelve.

Amikor készen áll a személyreszabási folyamat indítására, kattintson a „Create” (Létrehozás) gombra.

Az akusztikai modellek táblázatában az új modellhez tartozó bejegyzés jelenik meg. A táblázatban látható a folyamat állapota. Az állapot a következő értékeket veheti fel: „Waiting” (Várakozik), „Processing” (Feldolgozás) és „Complete” (Kész).

![kipróbálás](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-models-creating.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy hangfájlokhoz és átirataikhoz használható egyéni akusztikai modellt hoztunk létre. Szövegfájlokkal használható egyéni nyelvi modell létrehozásához lépjen tovább az egyéni nyelvi modellek létrehozásával foglalkozó oktatóanyagra.

> [!div class="nextstepaction"]
> [Egyéni akusztikai modell létrehozása](cognitive-services-custom-speech-create-language-model.md)
