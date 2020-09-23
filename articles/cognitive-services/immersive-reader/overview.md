---
title: Mi a magával ragadó olvasó?
titleSuffix: Azure Cognitive Services
description: A lebilincselő olvasó egy olyan eszköz, amelynek célja, hogy segítse a tanulási különbségeket, vagy új olvasókat és nyelvi tanulókat felismerni.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.custom: cog-serv-seo-aug-2020
keywords: olvasók, nyelvi tanulók, képek megjelenítése, az olvasás javítása, tartalom olvasása, fordítás
ms.openlocfilehash: a537845c0256316f9c47317a8805fa989a624f60
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985736"
---
# <a name="what-is-immersive-reader"></a>Mi az a Modern olvasó?

A teljes körűen kialakított [olvasó](https://www.onenote.com/learningtools) egy kipróbált eljárás, amely az új olvasók, a nyelvi tanulók és a tanulással kapcsolatos különbségeket, például a diszlexiat is javítja. Az olvasó ügyféloldali kódtár használatával a Microsoft Wordben és a Microsoft egy megjegyzésében is használhatja a webalkalmazások fejlesztéséhez használt technológiát. 

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>A kisegítő lehetőségek kihasználása az olvasóval 

A lebilincselő olvasó úgy lett kialakítva, hogy mindenki számára megkönnyítse és könnyebben elérhetővé tegye az olvasót. Vessünk egy pillantást néhány lebilincselő olvasó alapszolgáltatására.

### <a name="isolate-content-for-improved-readability"></a>Tartalom elkülönítése a jobb olvashatóság érdekében

A lebilincselő olvasó elszigeteli a tartalmat az olvashatóság javítása érdekében. 

  ![Tartalom elkülönítése a jobb olvashatóság érdekében a magával ragadó olvasóval](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>Képek megjelenítése a gyakori szavakhoz

A gyakran használt kifejezések esetében a lebilincselő olvasó egy képet jelenít meg.

  ![A képet tartalmazó szótár magával ragadó olvasóval](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>A beszédfelismerés részeinek kiemelése

A lebilincselő olvasó segítségével a tanulók megismerhetik a beszéd és a nyelvtan egyes részeit, és kiemelheti a műveleteket, a névmásokat, a névmásokat és egyebeket.

  ![A beszéd egyes részeinek megjelenítése magával ragadó olvasóval](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>Tartalom olvasása hangosan

A beszédfelismerés (vagy szövegről beszédre) bekerül a lebilincselő olvasó szolgáltatásba, amely lehetővé teszi, hogy az olvasók hangosan felolvassák a szöveget. 

  ![Szöveg felolvasása a magára olvasóval](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>Tartalom valós idejű fordítása

A lebilincselő olvasó számos nyelvre képes valós időben lefordítani a szöveget. Ez hasznos lehet az új nyelvet tanuló olvasóinak megértéséhez.

  ![Szöveg lefordítása magába ejtő olvasóval](./media/translation.png)

### <a name="split-words-into-syllables"></a>Szavak felosztása szótagokra

A magával ragadó olvasóval a szavakat szótagokra lehet bontani az olvashatóság javítása vagy az új szavak kijavítása érdekében.

  ![Szavak kibontása a szótagokra magával ragadó olvasóval](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Hogyan működik a alámerülés olvasó?

A lebilincselő olvasó egy önálló webalkalmazás. Ha a meghívja az olvasó ügyféloldali függvénytárát, a meglévő webalkalmazás tetején jelenik meg `iframe` . Amikor a WEP-alkalmazás meghívja az olvasó szolgáltatást, megadja a tartalmat az olvasó megjelenítéséhez. A lebilincselő olvasó ügyféloldali kódtár kezeli a `iframe` és a kommunikációt az olvasói háttérrendszer-szolgáltatással. A magával ragadó olvasó szolgáltatás feldolgozza a tartalmat a beszédfelismerés, a szöveg és a fordítás között, és így tovább.

## <a name="get-started-with-immersive-reader"></a>Ismerkedés a lebilincselő olvasóval

A gördülékeny olvasó ügyféloldali kódtára C#, JavaScript, Java (Android), Kotlin (Android) és Swift (iOS) rendszeren érhető el. Első lépések:

* [Rövid útmutató: az olvasó ügyféloldali függvénytárának használata](quickstarts/client-libraries.md)

## <a name="next-steps"></a>Következő lépések

A Modern olvasó használatának első lépései:

* Az [olvasói ügyféloldali függvénytár-referenciájának](./reference.md) elolvasása
* Ismerje [meg az olvasói ügyféloldali kódtárat a githubon](https://github.com/microsoft/immersive-reader-sdk)
