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
ms.openlocfilehash: 5acd6e4be178c7e645eb6855a79cd0f58303ba9a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846420"
---
# <a name="what-is-immersive-reader"></a>Mi az a Modern olvasó?

A teljes [olvasó](https://www.onenote.com/learningtools) egy olyan, integráltan kialakított eszköz, amely bevált technikákat valósít meg a feltörekvő olvasók, a nyelvi tanulók és a tanulással kapcsolatos különbségek, például a diszlexia megismerése érdekében.

Webalkalmazásában a Modern olvasó SDK-jával használhatja a Modern olvasót.

## <a name="what-does-immersive-reader-do"></a>Mit tesz a magával ragadó olvasó?

A magával ragadó olvasó úgy lett kialakítva, hogy mindenki számára elérhetővé tegye az olvasót.

* A tartalom minimális olvasási nézetben való megjelenítése

  ![Immersive Reader](./media/immersive-reader.png)

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

A lebilincselő olvasó egy önálló webalkalmazás, amely a saját webalkalmazások egy `iframe`használatával történő meghívásakor jelenik meg. Amikor meghívja az API-t, hogy elindítsa a lebilincselő olvasót, megadhatja, hogy milyen tartalmat szeretne megjeleníteni a lebilincselő olvasóban. Az SDK kezeli a `iframe` létrehozását és stílusát, valamint a kommunikációt a magával ragadó olvasói háttér-szolgáltatással, amely a beszéd részeire, szövegről beszédre, fordításra és egyebekre dolgozza fel a tartalmat.

## <a name="next-steps"></a>Következő lépések

A Modern olvasó használatának első lépései:

* Ugrás [a gyors](./quickstart.md) üzembe helyezésre
* Ismerje [meg az olvasót a githubon](https://github.com/microsoft/immersive-reader-sdk)
* Olvassa el a [magára olvasó SDK-referenciát](./reference.md)