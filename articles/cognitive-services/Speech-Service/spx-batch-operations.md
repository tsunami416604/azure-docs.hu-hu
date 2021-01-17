---
title: Speech CLI batch Operations-Speech Service
titleSuffix: Azure Cognitive Services
description: megtudhatja, hogyan végezheti el a Batch Speech (beszédfelismerés), a kötegelt szöveg és a beszédfelismerés (Speech szintézis) szövegét a Speech CLI használatával.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: 60cacafc89f2335b87885e4ea11dcf8992d68c3f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540056"
---
# <a name="speech-cli-batch-operations"></a>Beszéd CLI batch-műveletek

Az Azure Speech Services használatának gyakori feladatai a Batch-műveletek. Ebből a cikkből megtudhatja, hogyan végezheti el a Batch Speech (beszédfelismerés), a kötegelt szöveg és a beszédfelismerés (Speech szintézis) használatát a Speech CLI használatával. Konkrétan a következőket tanulhatja meg:

* A Batch Speech Recognition futtatása hangfájlok könyvtárán
* Batch Speech szintézis futtatása egy fájl ismételt megismétlésével `.tsv`

## <a name="batch-speech-to-text-speech-recognition"></a>Kötegelt beszéd szövege (beszédfelismerés)

A beszédfelismerési szolgáltatás gyakran használatos a hangfájlok hangjának felismerésére. Ebből a példából megtudhatja, hogyan lehet megismételni a könyvtárat a Speech CLI használatával, hogy rögzítse az egyes fájlok felismerési kimenetét `.wav` . A `--files` jelző a hangfájlok tárolására szolgáló könyvtárra mutat, a helyettesítő karakter pedig arra szolgál, hogy a `*.wav` Speech CLI a bővítmény minden fájlján futtassa a felismerést `.wav` . Az egyes felismerési fájlok kimenetét tabulátorral elválasztott értékként kell írni a alkalmazásban `speech_output.tsv` .

> [!NOTE]
> Az `--threads` argumentumot a következő szakaszban is használhatja a `spx synthesize` parancsokhoz, és az elérhető szálak a CPU és a jelenlegi terhelési aránytól függenek.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

A következő példa a kimeneti fájl szerkezetét szemlélteti.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Batch-szöveg beszédbe (beszéd szintézise)

A Batch szöveg-beszéd futtatásának legegyszerűbb módja egy új `.tsv` (tabulátorral tagolt) fájl létrehozása, és a parancs használata a `--foreach` Speech CLI-ben. Létrehozhat egy `.tsv` fájlt a kedvenc szövegszerkesztővel, ehhez a példához hívjuk a következőt `text_synthesis.tsv` :

>[!IMPORTANT]
> A szövegfájl tartalmának másolásakor győződjön meg arról, **hogy a fájl nem tér** el a fájl helye és a szöveg között. Időnként, ha a fenti példában a tartalom másolása történik, a rendszer a lapokat szóközökre konvertálja, így a `spx` parancs futtatása sikertelen lesz.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Ezután futtasson egy parancsot, hogy mutasson rá `text_synthesis.tsv` , végezze el a szintézist az egyes `text` mezőkön, és az eredményt fájlként írja a megfelelő `audio.output` elérési útra `.wav` .

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Ez a parancs megegyezik a `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` fájl **minden egyes** rekordjára vonatkozó futtatással `.tsv` .

Néhány Tudnivaló:

* Az oszlopfejlécek a `audio.output` és a `text` parancssori argumentumoknak felelnek meg `--audio output` `--text` . A több részből álló parancssori argumentumokat `--audio output` , például a szóközök nélküli fájlban kell formázni, a kezdő kötőjelek és a sztringeket elválasztó időszakok `audio.output` . A többi meglévő parancssori argumentum további oszlopként is hozzáadható a fájlhoz a minta használatával.
* Ha a fájl így formázva van, nem szükséges további argumentumokat átadni a következőhöz: `--foreach` .
* Ügyeljen arra, hogy az egyes értékeket a `.tsv` with a **lapon** válassza el.

Ha azonban a `.tsv` következő példához hasonló fájllal rendelkezik, olyan oszlopfejléceket, amelyek **nem egyeznek** a parancssori argumentumokkal:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Ezeket a mezőneveket a megfelelő argumentumokra írhatja felül a hívás következő szintaxisával `--foreach` . Ez a fenti hívás.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>További lépések

* [A Speech parancssori felület áttekintése](./spx-overview.md)
* [Beszédfelismerési parancssori felület](./spx-basics.md)
