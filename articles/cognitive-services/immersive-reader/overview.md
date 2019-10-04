---
title: Mi a magával ragadó olvasó API?
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg a magával ejtő olvasó API-val.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: f84804fbad07357706e786fc449fce9e42a5ec4a
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688335"
---
# <a name="what-is-immersive-reader"></a>Mi az a Modern olvasó?

A teljes [olvasó](https://www.onenote.com/learningtools) egy olyan, integráltan kialakított eszköz, amely bevált technikákat valósít meg a feltörekvő olvasók, a nyelvi tanulók és a tanulással kapcsolatos különbségek, például a diszlexia megismerése érdekében.

Webalkalmazásában a Modern olvasó SDK-jával használhatja a Modern olvasót.

## <a name="what-does-immersive-reader-do"></a>Mit tesz a magával ragadó olvasó?

A magával ragadó olvasó úgy lett kialakítva, hogy mindenki számára elérhetővé tegye az olvasót.

* A tartalom minimális olvasási nézetben való megjelenítése

  ![Modern olvasó](./media/immersive-reader.png)

* Megjeleníti a gyakran használt szavak képeit

  ![KÉPszóTÁR](./media/picture-dictionary.png)

* Kiemelt nevek, igék, melléknevek és határozószók

  ![A beszéd részei](./media/parts-of-speech.png)

* Hangosan beolvassa a tartalmat

  ![Hangosan olvasása](./media/read-aloud.png)

* Lefordítja a tartalmat egy másik nyelvre

  ![Fordítás](./media/translation.png)

* Megszakítja a szavakat szótagokra

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Hogyan működik a alámerülés olvasó?

A részletes olvasó egy önálló webalkalmazás, amely a saját webalkalmazásának a használatával történő meghívásakor jelenik meg a meglévő webalkalmazáson keresztül `iframe`. Amikor meghívja az API-t, hogy elindítsa a lebilincselő olvasót, megadhatja, hogy milyen tartalmat szeretne megjeleníteni a lebilincselő olvasóban. Az SDK kezeli a `iframe` és a kommunikáció létrehozását és stílusát az olvasói háttérrendszer-szolgáltatással, amely a beszéd részeire, szövegről beszédre, fordításra és egyebekre dolgozza fel a tartalmat.

## <a name="next-steps"></a>További lépések

A Modern olvasó használatának első lépései:

* Ugrás [a gyors](./quickstart.md) üzembe helyezésre
* Ismerje [meg](https://github.com/microsoft/immersive-reader-sdk) az olvasót a githubon
* Olvassa el a [magára olvasó SDK](./reference.md) -referenciát