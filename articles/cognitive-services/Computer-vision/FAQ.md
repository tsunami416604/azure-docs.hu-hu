---
title: Gyakori kérdések – Computer Vision
titleSuffix: Azure Cognitive Services
description: Választ kaphat az Azure Cognitive Services Computer Vision APIával kapcsolatos gyakori kérdésekre.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564592"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API gyakori kérdések

> [!TIP]
> Ha nem talál választ a kérdéseire ebben a GYIK-ban, próbálja meg megkérdezni a Computer Vision API Közösséget a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) -on, vagy forduljon [segítségért és támogatásért a UserVoice](https://cognitive.uservoice.com/)

---

**Kérdés**: *Használhatok Computer Vision API egyéni címkék használatára?  Szeretném például, hogy a Cat-ból származó képeket a mesterséges intelligenciára ("Train"), majd egy AI-kérelemre kapják.*

**Válasz**: Ez a függvény jelenleg nem érhető el. Mérnökeink azonban arra dolgozunk, hogy ezt a funkciót Computer Vision.

---

**Kérdés**: *Helyileg, internetkapcsolat nélkül is használható Computer Vision?*

**Válasz**: Jelenleg nem biztosítunk helyszíni vagy helyi megoldást.

---

**Kérdés**: *A Computer Vision használhatók a rendszámtábla olvasásához?*

**Válasz**: A víziós API jó szöveges észlelést biztosít az OCR használatával, de jelenleg nincs optimalizálva a licencek lemezekre. Folyamatosan igyekszünk javítani a szolgáltatásainkat, és az Automatikus rendszámtábla-felismeréshez hozzáadott OCR-t is készítettünk a szolgáltatásra vonatkozó kérések listájára.

---

**Kérdés**: *Milyen típusú írási felületek támogatottak a kézírás-felismeréshez?*

**Válasz**: A technológia különböző típusú felületekkel működik, beleértve a táblákat, a tanulmányokat és a sárga ragacsos megjegyzéseket.

---

**Kérdés**: *Mennyi ideig tart a kézírás-felismerési művelet?*

**Válasz**: Az igénybe vevő időtartam a szöveg hosszától függ. Hosszabb szövegek esetében akár több másodpercig is eltarthat. Ezért, ha a kézzel írt szöveg felismerése művelet befejeződik, előfordulhat, hogy meg kell várnia az eredmények lekérését a kézzel írt szöveg műveleti eredményének beolvasása művelettel.

---

**Kérdés**: *Hogyan kezeli a kézírás-felismerési technológia a sorok közepén lévő kalap használatával beszúrt szöveget?*

**Válasz**: Ezt a szöveget a kézírás-felismerési művelet külön sorban adja vissza.

---

**Kérdés**: *Hogyan kezeli a kézírás-felismerési technológia a határokon átnyúló szavakat vagy sorokat?*

**Válasz**: Ha a szavakat több sorral keresztezik, hogy felismerhetetlenek legyenek, a kézírás-felismerési művelet nem használja fel őket. Ha azonban a szavakat egyetlen vonallal keresztezik, az átkelést a rendszer zajként kezeli, és a szavakat a kézírás-felismerési művelet továbbra is felveszi.

---

**Kérdés**: *Milyen szöveges tájolást támogat a kézírás-felismerési technológia?*

**Válasz**: A kézírás-felismerési művelettel akár körülbelül 30 fokos, 40 fokos szögbe szedett szöveg is megadható.

---
