---
title: Mi a magával ragadó olvasó API?
titleSuffix: Azure Cognitive Services
description: A lebilincselő olvasó API egy olyan eszköz, amellyel a tanulási különbségekkel rendelkező személyeket, illetve az új olvasókat és nyelvi tanulókat is felhasználhatják.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 2eab7ab62f6a7a105dab33aa889af9f2be3a3fa3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80052386"
---
# <a name="what-is-immersive-reader"></a>Mi az a Modern olvasó?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

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
* Ismerje [meg az olvasót a githubon](https://github.com/microsoft/immersive-reader-sdk)
* Olvassa el a [magára olvasó SDK-referenciát](./reference.md)
