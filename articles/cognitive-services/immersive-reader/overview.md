---
title: Kapcsolatos ragadó olvasó
titlesuffix: Azure Cognitive Services
description: További információ a ragadó olvasó
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 38056f701b76f3fc2cf1f617a6977321445f78ff
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296790"
---
# <a name="what-is-immersive-reader"></a>Mit jelent a ragadó olvasó?

A [ragadó olvasó](https://www.onenote.com/learningtools) szélsőértékeket is beleértve megtervezett eszköz, amely megvalósítja az olvasó szövegértést az újonnan felbukkanó olvasók, nyelvet tanuló, és a tanulási például dyslexia különbségek rendelkező személyek bevált módszereket.

A webalkalmazás ragadó olvasó használhatja a ragadó olvasó SDK-val.

## <a name="what-does-immersive-reader-do"></a>Mire ragadó olvasó?

A ragadó olvasó felhasználóbaráttá olvasási mindenki számára tervezték.

* A minimális olvasó nézetben látható tartalom

  ![Immersive Reader](./media/immersive-reader.png)

* Megjeleníti a gyakran használt szó képek

  ![Kép szótár](./media/picture-dictionary.png)

* Kiemeli azokat a parancsokat, műveletek, melléknevek és módosítószavak

  ![A beszédfelismerés részei](./media/parts-of-speech.png)

* A tartalom hangos is olvassa be

  ![Felolvasása](./media/read-aloud.png)

* A tartalom fordítja le egy másik nyelvet

  ![Fordítás](./media/translation.png)

* Felszámolja szótagokat határoznak szavakat

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Hogyan működik a ragadó olvasó?

A ragadó olvasó egy önálló webes alkalmazás, amely meghívásakor olvasó rendezvényein megismerkedhet a JavaScript SDK-val, a már meglévő webalkalmazás felett jelenik meg egy `iframe`. Az API-t, indítsa el a ragadó olvasó hívásakor adja meg a tartalom megjelenítése a ragadó olvasó a kívánt. Az SDK kezeli, létrehozását és stílusának a `iframe` és a háttérrendszer dolgozza fel a tartalom a részek a beszéd, szöveg-beszéd átalakítás, fordítási és így tovább ragadó olvasó szolgáltatással folytatott kommunikáció.

## <a name="next-steps"></a>További lépések

Első lépések a ragadó olvasó:

* Nyissa meg a [a rövid útmutató](./quickstart.md)
* Fedezze fel a [ragadó olvasó SDK a Githubon](https://github.com/Microsoft/immersive-reader-sdk)
* Olvassa el a [ragadó olvasó SDK-referencia](./reference.md)