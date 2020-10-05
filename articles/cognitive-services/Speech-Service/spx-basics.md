---
title: Beszédfelismerési parancssori felület alapjai
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használható a beszédfelismerési szolgáltatás a Speech parancssori felülettel, és ne legyen minimális beállítású a Speech CLI használatával.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e859ac13c72ed07d3f57da6e61fd6d9f827f0fca
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88854903"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>A Speech CLI alapjai

Ebből a cikkből megtudhatja, hogyan használhatja a beszédfelismerési parancssori felületet a beszédfelismerési szolgáltatás alapszintű felhasználási mintái, kód írása nélkül. A beszédfelismerési szolgáltatás fő funkcióit gyorsan kipróbálhatja fejlesztési környezetek létrehozása vagy kód írása nélkül, így ellenőrizheti, hogy a használati esetek megfelelően teljesíthetők-e. Emellett a Speech CLI éles környezetben is készen áll, és az egyszerű munkafolyamatok automatizálására használható a Speech szolgáltatásban, a `.bat` vagy a rendszerhéj parancsfájljainak használatával.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Alapvető használat

Ez a szakasz néhány alapszintű SPX-parancsot mutat be, amelyek gyakran hasznosak az első alkalommal történő teszteléshez és kísérletezéshez. Először az alábbi parancs futtatásával tekintse meg az eszközre épített súgót.

```shell
spx
```

Figyelje meg a **következőt:** súgótémakörök a parancssori paraméterektől jobbra. Ezeket a parancsokat megadhatja az alparancsok részletes súgójának megjelenítéséhez.

A súgótémakörök kulcsszava alapján is megkereshetők. Adja meg például a következő parancsot a Speech CLI használati példák listájának megtekintéséhez:

```shell
spx help find --topics "examples"
```

A következő parancs megadásával tekintheti meg a felismerési parancs beállításait:

```shell
spx help recognize
```

A beszédfelismerési szolgáltatással a következő parancs futtatásával elvégezheti a beszédfelismerést az alapértelmezett mikrofon használatával.

```shell
spx recognize --microphone
```

A parancs beírása után az SPX megkezdi a hang figyelését az aktuális aktív bemeneti eszközön, és a megnyomása után leáll `ENTER` . A rendszer ezután felismeri és átalakítja a rögzített beszédet a konzol kimenetében lévő szövegre. A beszédfelismerési parancssori felület használatával egyszerűen elvégezhető a szöveg és a beszéd szintézise is. 

A következő parancs futtatásakor a beírt szöveg bemenetként fog megjelenni, és a szintetizált beszédet a jelenlegi aktív kimeneti eszközre írja.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

A beszédfelismerésen és a szintézisen kívül a beszédfelismerési parancssori felülettel is elvégezheti a fordítást. A fenti beszédfelismerési parancshoz hasonlóan a következő parancs futtatásával rögzítheti a hangot az alapértelmezett mikrofonból, és elvégezheti a fordítást a célnyelv szövegére.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

Ebben a parancsban meg kell adnia a forrást (a fordítás nyelvét) és a célként megadott (nyelvről **lefordítható** **)** nyelveket. Az `--microphone` argumentum használatával a rendszer meghallgatja az aktuális aktív bemeneti eszköz hangját, és a lenyomás után leáll `ENTER` . A kimenet egy szöveges fájlba írt szöveg fordítása a célként megadott nyelvre.

> [!NOTE]
> Tekintse meg az összes támogatott nyelv listáját a [nyelvi és területi beállításban](language-support.md) a megfelelő területi beállításokkal.

## <a name="batch-operations"></a>Batch-műveletek

Az előző szakaszban található parancsok nagyszerűen ismertetik a beszédfelismerési szolgáltatás működésének gyors megismerését. Ha azonban azt állapítja meg, hogy a használati esetek teljesíthetők-e, valószínű, hogy egy már meglévő bemeneti tartományon kell végrehajtania a kötegelt műveleteket, hogy megtudja, hogyan kezelje a szolgáltatás a különböző forgatókönyveket. Ez a szakasz a következőket mutatja be:

* A Batch Speech Recognition futtatása hangfájlok könyvtárán
* Iteráció egy `.tsv` fájlon keresztül és a köteg szöveg-beszéd szintézisének futtatása

## <a name="batch-speech-recognition"></a>Batch-beszédfelismerés

Ha a hangfájlok könyvtára egyszerűen fut, a beszédfelismerési parancssori felület segítségével gyorsan futtathatja a Batch-Speech felismerést. Egyszerűen futtassa a következő parancsot, amely a könyvtárára mutat a `--files` paranccsal. Ebben a példában hozzáfűzi `\*.wav` a címtárhoz, hogy felismerje `.wav` a dir mappában található összes fájlt. Emellett adja meg `--threads` azt az argumentumot, amely 10 párhuzamos szálon futtatja az elismerést.

> [!NOTE]
> Az `--threads` argumentumot a következő szakaszban is használhatja a `spx synthesize` parancsokhoz, és az elérhető szálak a CPU és a jelenlegi terhelési aránytól függenek.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

A felismert beszédfelismerési kimenet az `speech_output.tsv` argumentum használatával íródik `--output file` . A következő példa a kimeneti fájl szerkezetét szemlélteti.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>Köteg szövege – beszéd szintézis

A Batch szöveg-beszéd futtatásának legegyszerűbb módja egy új `.tsv` (tabulátorral tagolt) fájl létrehozása, és a parancs kihasználása a `--foreach` beszédfelismerési CLI-ben. Vegye figyelembe a következő fájlt `text_synthesis.tsv` :

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 Ezután futtasson egy parancsot, hogy mutasson rá `text_synthesis.tsv` , végezze el a szintézist az egyes `text` mezőkön, és az eredményt fájlként írja a megfelelő `audio.output` elérési útra `.wav` . 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Ez a parancs megegyezik a `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` fájl **minden egyes** rekordjára vonatkozó futtatással `.tsv` . Néhány Tudnivaló:

* Az oszlopfejlécek és a `audio.output` a `text` parancssori argumentumok, illetve a `--audio output` `--text` . Több részből álló parancssori argumentumok `--audio output` , mint például a szóközök nélküli fájlban, a kezdő kötőjelek és a sztringeket elválasztó időszakok, például: `audio.output` . A többi meglévő parancssori argumentum további oszlopként is hozzáadható a fájlhoz a minta használatával.
* Ha a fájl így formázva van, nem szükséges további argumentumokat átadni a következőhöz: `--foreach` .
* Ügyeljen arra, hogy az egyes értékeket a `.tsv` with a **lapon**válassza el.

Ha azonban a `.tsv` következő példához hasonló fájllal rendelkezik, olyan oszlopfejléceket, amelyek **nem egyeznek** a parancssori argumentumokkal:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

Ezeket a mezőneveket a megfelelő argumentumokra írhatja felül a hívás következő szintaxisával `--foreach` . Ez a fenti hívás.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>További lépések

* Fejezze be a [beszédfelismerési](./quickstarts/speech-to-text-from-microphone.md) vagy [beszédfelismerési](./quickstarts/text-to-speech.md) útmutatókat az SDK használatával.
