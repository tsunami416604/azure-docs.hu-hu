---
title: Mi az a Magával ragadó Reader API?
titleSuffix: Azure Cognitive Services
description: A Magával ragadó Olvasó API egy olyan eszköz, amely lehet használni elhelyezésére emberek tanulási különbségek, vagy segít az új olvasók és nyelvtanulók.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 2eab7ab62f6a7a105dab33aa889af9f2be3a3fa3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052386"
---
# <a name="what-is-immersive-reader"></a>Mi az a Modern olvasó?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A [Magával ragadó Olvasó](https://www.onenote.com/learningtools) egy inkluzívan megtervezett eszköz, amely bevált technikákat valósít meg az olvasás megértésének javítására a feltörekvő olvasók, a nyelvtanulók és a tanulási nehézségekkel küzdő emberek, például a diszlexia számára.

Webalkalmazásában a Modern olvasó SDK-jával használhatja a Modern olvasót.

## <a name="what-does-immersive-reader-do"></a>Mit csinál a Magával ragadó olvasó?

A Magával ragadó olvasó célja, hogy az olvasás mindenki számára hozzáférhetőbb legyen.

* Tartalom megjelenítése minimális olvasási nézetben

  ![Modern olvasó](./media/immersive-reader.png)

* Gyakran használt szavak képeit jeleníti meg

  ![Képszótár](./media/picture-dictionary.png)

* Főnevek, igék, melléknevek és határozószavak kiemelése

  ![A beszéd részei](./media/parts-of-speech.png)

* Hangosan felolvassa a tartalmat

  ![Hangos felolvasás](./media/read-aloud.png)

* Lefordítja a tartalmat egy másik nyelvre

  ![Fordítás](./media/translation.png)

* Szókra bontja a szavakat

  ![Tanmenet](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Hogyan működik a Magával ragadó olvasó?

A Magával ragadó olvasó egy önálló webes alkalmazás, amely a Magával ragadó olvasó JavaScript SDK használatával `iframe`történő meghívásesetén a meglévő webalkalmazás tetején jelenik meg egy . Amikor felhívja az API-t, hogy indítsa el a Magával ragadó olvasó, meg kell adnia a kívánt tartalmat megjeleníteni a magával ragadó olvasó. SDK-nk kezeli a Immersive `iframe` Reader háttérszolgáltatás létrehozását és stílusát, amely feldolgozza a tartalom tartalmát a beszéd, a szöveg-beszéd, a fordítás és így tovább.

## <a name="next-steps"></a>További lépések

A Modern olvasó használatának első lépései:

* Ugrás a [rövid útmutatóba](./quickstart.md)
* Fedezze fel a [magával ragadó Reader SDK-t a GitHubon](https://github.com/microsoft/immersive-reader-sdk)
* Olvassa el a [magával ragadó Reader SDK-útmutatót](./reference.md)
