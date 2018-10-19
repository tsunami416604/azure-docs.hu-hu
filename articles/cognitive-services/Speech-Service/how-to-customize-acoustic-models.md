---
title: Akusztikai modell létrehozása a Speech Service segítségével – Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre akusztikai modellt az Azure Cognitive Services Speech Service szolgáltatásával.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 39e591f6154573bb25fccc423ff63a82f282beaf
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017354"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Oktatóanyag: Egyéni akusztikai modell létrehozása

Egy egyéni akusztikai modell létrehozása akkor hasznos, ha az alkalmazása egy adott környezetben (például autóban), adott felvételi eszközökkel vagy feltételek mellett, illetve adott felhasználói körben történő használatra készült. A példák közé tartozik az akcentussal való beszéd, a meghatározott háttérzajok vagy egy adott mikrofon használata a felvételhez.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Az adatok előkészítése
> * Az akusztikai adatkészlet importálása
> * Egyéni akusztikai modell létrehozása

Ha még nincs Azure Cognitive Services-fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/try/cognitive-services), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

Nyissa meg a [Cognitive Services-előfizetések](https://cris.ai/Subscriptions) oldalt, és ellenőrizze, hogy Cognitive Services-fiókja egy előfizetés alá tartozik-e.

A **Connect existing subscription** (Meglévő előfizetés csatlakoztatása) lehetőséget választva csatlakozhat az Azure Portalon létrehozott Speech Service-előfizetésekhez.

A Speech Service-előfizetések az Azure Portalon való létrehozásával kapcsolatos útmutatásért lásd a [Speech Service ingyenes kipróbálásával](get-started.md) foglalkozó szakaszt.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Egy adott tartományhoz tartozó akusztikai modell testreszabásához beszédadatok gyűjteményére van szükség. A gyűjtemény néhány kimondott szövegtől több száz órányi beszédig terjedhet. A gyűjtemény beszédadatokat tartalmazó hangfájlokból, valamint az ezek mindegyikének szöveges átiratát tartalmazó szövegfájlból áll. A hangadatoknak reprezentatívnak kell lenniük arra a beszédhelyzetre nézve, amelyhez a beszédfelismerőt használni kívánja.

Például:

* Ha jobb beszédfelismerést szeretne elérni egy zajos gyári környezetben, a hangfájloknak zajos gyárban felvett beszédmintákat kell tartalmazniuk.
* Ha egy adott beszélőre szeretné optimalizálni a felismerés hatékonyságát – például ha Franklin D. Roosevelt elnök kandalló előtti beszélgetéseinek átiratát szeretné elkészíteni –, az audiofájloknak minél több, csak az adott beszélőtől származó példát kell tartalmazniuk.

Az akusztikai modell testreszabásához készült akusztikai adatkészlet két részből áll: (1) a beszédadatokat tartalmazó hangfájlok készletéből, valamint (2) a hangfájlok mindegyikének szöveges átiratát tartalmazó fájlból.

### <a name="audio-data-recommendations"></a>Hangadatokkal kapcsolatos javaslatok

* Az adatkészlet összes hangfájlját WAV (RIFF) hangformátumban kell tárolni.
* A hanganyag mintavételi frekvenciájának 8 vagy 16 kilohertz (kHz) értékűnek kell lennie, a mintaértékeket pedig tömörítetlen, 16 bites PCM (impulzuskód-moduláció) előjeles egész számokként (rövid jelek) kell tárolni.
* Csak az egycsatornás (mono) hangfájlok támogatottak.
* A hangfájlok hosszának 100 mikroszekundum és 1 perc között kell lennie. A hangfájloknak ideális esetben legalább 100 mikroszekundum csenddel kell kezdődniük és végződniük – az 500 mikroszekundum és 1 másodperc közötti érték az általános.
* Ha háttérzaj van az adatokban, javasoljuk, hogy hosszabb – például a beszédtartalom előtt és/vagy után néhány másodpercnyi – csendes szakaszokat tartalmazó minták is legyenek a példák között.
* Mindegyik hangfájlnak egyetlen kimondott szövegből kell állnia, például egyetlen mondatból vagy diktálásból, egy kérdésből, vagy egy párbeszéd egyetlen fordulójából.
* Az adatkészlet mindegyik hangfájljának egyedi fájlnévvel és a .wav kiterjesztéssel kell rendelkeznie.
* A hangfájlok készletét egyetlen, alkönyvtárak nélküli mappába kell helyezni, majd az egész készletet egyetlen .zip-fájlarchívumba kell tömöríteni.

> [!NOTE]
> A webes portálon keresztüli adatimportálás jelenleg 2 GB-ra korlátozott, ezért ez az akusztikai adatkészletek maximális mérete. Ez a mennyiség nagyjából 17 órányi, 16 kHz-es frekvencián felvett vagy 34 órányi, 8 kHz-es frekvencián felvett hanganyagnak felel meg. A hangadatokra vonatkozó főbb követelményeket az alábbi táblázat foglalja össze:
>

| Tulajdonság | Érték |
|---------- |----------|
| Fájlformátum | RIFF (WAV) |
| Mintavételezési frekvencia | 8000 vagy 16 000 Hz (hertz) |
| Csatornák | 1 (mono) |
| Minták formátuma | PCM, 16 bites egész számok |
| Fájlok időtartama | 0,1 másodperc és 60 másodperc között |
| Csend időtartama | >0,1 másodperc |
| Archívum formátuma | .zip |
| Archívum maximális mérete | 2 GB |

> [!NOTE]
> A fájlnevek csak latin karaktereket használhatnak, és a „fájlnév.kiterjesztés” formátumot kell követniük

## <a name="language-support"></a>Nyelvi támogatás

Az egyéni **diktálási** nyelvi modellekben támogatott összes nyelv listájáért lásd [a Speech Service támogatott nyelveit](language-support.md#speech-to-text) ismertető cikket.

### <a name="transcriptions-for-the-audio-dataset"></a>A hangadat-készlet átiratai

Az összes WAV fájl átiratát egyetlen, egyszerű szöveges fájlnak kell tartalmaznia. Az átiratfájl minden sorának egy hangfájl nevét és az annak megfelelő átiratot kell tartalmaznia. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani.

  Például:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Az átiratnak UTF-8 BOM (bájtsorrendjelzés) kódolásúnak kell lennie.

Az átiratokat a rendszer normalizálja, hogy fel tudja őket dolgozni. Bizonyos fontosabb normalizálási tevékenységeket azonban a felhasználónak kell végrehajtania az adatok a Custom Speech Service szolgáltatásba való feltöltése _előtt_. Az átiratok előkészítése során használandó megfelelő nyelv meghatározásához lásd [a Speech Service használatára vonatkozó átírási irányelvekkel](prepare-transcription.md) foglalkozó szakaszt.

Hajtsa végre a következő szakaszban leírt lépéseket [a Speech Service portál](https://cris.ai) használatával.

## <a name="import-the-acoustic-dataset"></a>Az akusztikai adatkészlet importálása

Miután előkészítette a hangfájlokat és az átiratokat, azok importálhatók a szolgáltatás webes portáljára.

Az importálásukhoz először mindenképp jelentkezzen be a [Speech Service portálra](https://cris.ai). Ezután kattintson a szalagsávon a **Custom Speech** legördülő menüre, és válassza az **Adaptation Data** (Adaptációs adatok) lehetőséget. Ha most tölt fel először adatokat a Custom Speech Service szolgáltatásba, egy üres tábla jelenik meg a **Datasets** (Adatkészletek) címkével. 

Az **Acoustic Datasets** (Akusztikai adatkészletek) sorban válassza az **Import** (Importálás) lehetőséget, és a webhely megjeleníti az új adatkészlet feltöltésére szolgáló oldalt.

![Az Import Acoustic Data (Akusztikai adatok importálása) lap](media/stt/speech-acoustic-datasets-import.png)

A **Name** (Név) és a **Description** (Leírás) mezőben adja meg a megfelelő információkat. A beszédes leírások segítenek a különböző feltöltött adatkészletek számon tartásában. 

A **Transcriptions file (.txt)** (Átiratfájl, .txt) és az **Audio files (.zip)** (Hangfájlok, .zip) mezőben a **Browse** (Böngészés) gombra kattintva válassza ki az egyszerű szöveges átiratfájlt, valamint a WAV-fájlokat tartalmazó tömörített archívumot. Ha elvégezte az előkészítést, válassza az **Import** (Importálás) lehetőséget az adatok feltöltéséhez. A rendszer feltölti az adatokat. Nagyobb adatkészlet esetén az importálási folyamat több percig is eltarthat.

Ha a feltöltés befejeződött, a rendszer visszalép az **Acoustic Datasets** (Akusztikai adatkészletek) adattáblára. Megjelenik egy, az akusztikai adatkészletre vonatkozó bejegyzés. Figyelje meg, hogy a rendszer egy egyedi azonosítót (GUID) rendelt az adatkészlethez. Az adatkészlet mellett megjelenik annak aktuális állapota: feldolgozásra várva *NotStarted* (Nincs elkezdve), az érvényesítés során *Running* (Fut), majd *Complete* (Kész), ha az adatok használatra készek.

Az adatok érvényesítéséhez a rendszer egy sor ellenőrzést végez – ellenőrzi a hangfájlok fájlformátumát, hosszát és mintavételezési frekvenciáját, valamint az átiratfájlok fájlformátumát, illetve némi szövegnormalizálást hajt végre.

Ha az állapot *Succeeded* (Sikeres), a **Details** (Részletek) lehetőséget választva megtekintheti a nyelvi adatok ellenőrzésére vonatkozó jelentést. Ebben látható az ellenőrzésen sikeresen és sikertelenül átment kimondott szövegek száma, valamint a sikertelen kimondott szövegek részletei. Az alábbi képen lévő példában két WAV fájl bizonyult sikertelennek az ellenőrzés során helytelen hangformátum miatt. Ebben az adatkészletben az egyik fájl helytelen mintavételezési frekvenciával, a másik pedig helytelen fájlformátummal rendelkezik.

![Az Adaptation of Data Details (Adatok adaptálásának részletei) lap](media/stt/speech-acoustic-datasets-report.png)

Ha módosítani szeretné az adatkészlet nevét vagy leírását, válassza az **Edit** (Szerkesztés) hivatkozást, és módosítsa a bejegyzéseket. Az átiratok és hangfájlok bejegyzései nem módosíthatók.

## <a name="create-a-custom-acoustic-model"></a>Egyéni akusztikai modell létrehozása

Ha a nyelvi adatkészlet *Complete* (Kész) állapotra vált, a használatával létrehozhat egy egyéni akusztikai modellt. Ehhez válassza az **Acoustic Models** (Akusztikai modellek) lehetőséget a **Custom Speech** legördülő listában. A **Your models** (Saját modellek) című táblázat megjeleníti az egyéni akusztikai modellek listáját. Ha először használja ezt a funkciót, a táblázat üres lesz. A táblázat címe az aktuális területi beállítást mutatja. Akusztikai modellek jelenleg csak az amerikai angol beállításhoz hozhatók létre.

Új modell létrehozásához válassza a **Create New** (Új létrehozása) lehetőséget a tábla címe alatt. Ahogy korábban is, adja meg a nevet és egy leírást, amelyek alapján azonosíthatja a modellt. Például a **Description** (Leírás) mezőben rögzíthető, hogy a modellt mely kiindulási modell és akusztikai adatok felhasználásával készítette. 

Ezután a **Base Acoustic Model** (Alap akusztikai modell) legördülő menüben válasszon ki egy alap modellt. Az alap modell szolgál a testreszabás kiindulópontjaként. Két alap akusztikai modell közül választhat:
* A **Microsoft Search and Dictation AM** (Microsoft keresési és diktálási akusztikai) modell az alkalmazásokhoz intézett beszéd, például parancsok, keresőlekérdezések vagy diktálás feldolgozásához alkalmas. 
* A **Microsoft Conversational Model** (Microsoft beszélgetési modell) a beszélgetési stílusban elhangzott beszélgetések feldolgozásához alkalmas. Az ilyen típusú beszéd általában másik személynek szól, és telefonos ügyfélszolgálatokon vagy értekezleteken hangzik el. 

A részleges eredmények késleltetése a Conversational-modellek esetében magasabb, mint a Search and Dictation-modellek esetében.

> [!NOTE]
> Jelenleg is zajlik az új **Universal** (Univerzális) modell bevezetése, amely az összes beszédhelyzet kezelésére szolgál. A fent említett modellek is nyilvánosan elérhetők maradnak.

Az **Acoustic Data** (Akusztikai adatok) legördülő listából válassza ki a testreszabáshoz használni kívánt akusztikai adatokat.

![A Create Acoustic Model (Akusztikai modell létrehozása) lap](media/stt/speech-acoustic-models-create2.png)

A feldolgozás befejezése után lehetősége van tesztelni az új modell pontosságát. Ez egy megadott akusztikai adatkészletnek az egyéni akusztikai modell használatával történő diktálásos értékelését, majd az arról való jelentéskészítést jelenti. A teszt elvégzéséhez jelölje be az **Accuracy Testing** (Pontossági tesztelés) jelölőnégyzetet. Ezután válassza ki a nyelvi modellt a legördülő menüből. Ha még nem hozott létre egyéni nyelvi modellt, a legördülő listában csak az alap nyelvi modellek jelennek meg. A megfelelő nyelvi modell kiválasztásával kapcsolatban lásd [az egyéni nyelvi modell létrehozását ismertető oktatóanyagot](how-to-customize-language-model.md).

Végül válassza ki az egyéni modell értékeléshez használni kívánt akusztikai adatkészletet. Ha pontossági tesztelést végez, annak érdekében, hogy valósághű képet kapjon a modell teljesítményéről, fontos olyan akusztikai adatkészletet kiválasztani, amelyik különbözik a modell elkészítéséhez felhasznált adatkészlettől. A betanítási adatokon végzett pontossági tesztelés nem teszi lehetővé az alkalmazott modell valós körülmények közötti működésének értékelését. Az eredmény túlságosan optimista lesz. Azt is vegye figyelembe, hogy a pontossági tesztelés 1000 kimondott szövegre van korlátozva. Ha a teszteléshez használt akusztikai adatkészlet ennél nagyobb, csak az első 1000 kimondott szöveg lesz értékelve.

Amikor készen áll a testreszabási folyamat indítására, válassza a **Create** (Létrehozás) lehetőséget.

Az akusztikai modellek táblázatában egy, az új modellhez tartozó bejegyzés jelenik meg. A táblázatban megjelenik a folyamat állapota is: *Waiting* (Várakozás), *Processing* (Feldolgozás) vagy *Complete* (Befejezve).

![Az Acoustic Models (Akusztikai modellek) lap](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>További lépések

- [Speech Service próba-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Beszéd felismerése C# nyelven](quickstart-csharp-dotnet-windows.md)
- [Git-mintaadatok](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
