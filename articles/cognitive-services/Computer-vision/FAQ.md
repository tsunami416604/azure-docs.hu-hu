---
title: Gyakori kérdések - Computer Vision
titleSuffix: Azure Cognitive Services
description: Válaszok at kaphat az Azure Cognitive Services Computer Vision API-jával kapcsolatos gyakori kérdésekre.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68564592"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API – gyakori kérdések

> [!TIP]
> Ha ebben a gyIKben nem talál választ a kérdéseire, próbálja meg megkérdezni a Computer Vision API-közösségét a [StackOverflow-n,](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) vagy lépjen kapcsolatba [a UserVoice súgójával és támogatásával.](https://cognitive.uservoice.com/)

---

**Kérdés:** *Betaníthatom a Computer Vision API-t egyéni címkék használatára?  Például, Szeretnék -ni táplál -ban mozi -ból macska tenyészt -hoz ' vonat' a AI, akkor kap a tenyészt érték -ra egy AI kereslet.*

**Válasz**: Ez a funkció jelenleg nem érhető el. Mérnökeink azonban azon dolgoznak, hogy ezt a funkciót a Computer Vision-be vigyék.

---

**Kérdés**: *Lehet Computer Vision használható helyben internetkapcsolat nélkül?*

**Válasz:** Jelenleg nem kínálunk helyszíni vagy helyi megoldást.

---

**Kérdés**: *Használható-e a Computer Vision a rendszámtáblák olvasására?*

**Válasz:** A Vision API jó szövegfelismerést kínál az OCR-rel, de jelenleg nincs rendszámtáblákra optimalizálva. Folyamatosan igyekszünk javítani szolgáltatásainkat, és az automatikus rendszámfelismeréshez szükséges OCR-t hozzáadtuk a funkciókérések listájához.

---

**Kérdés**: *Milyen típusú írási felületek támogatottak a kézírás-felismeréshez?*

**Válasz:** A technológia különböző felületekkel működik, beleértve a táblákat, a fehér papírt és a sárga öntapadó jegyzeteket.

---

**Kérdés**: *Mennyi ideig tart a kézírás-felismerési művelet?*

**Válasz**: A szöveg hosszától függ, hogy mennyi időt vesz igénybe. Hosszabb szövegek esetén akár néhány másodpercig is eltarthat. Ezért a Kézzel írt szöveg felismerése művelet befejezése után előfordulhat, hogy várnia kell, mielőtt a Kézzel írt szöveg művelet eredményének beolvasása művelettel lekérheti az eredményeket.

---

**Kérdés:** *Hogyan kezeli a kézírás-felismerő technológia a sor közepén lévő beszúrással beszúrt szöveget?*

**Válasz**: Az ilyen szöveget a kézírás-felismerési művelet külön sorként adja vissza.

---

**Kérdés**: *Hogyan kezeli a kézírás-felismerő technológia az áthúzott szavakat vagy sorokat?*

**Válasz:** Ha a szavakat több vonallal áthúzzák, hogy felismerhetetlenné tegyék őket, a kézírás-felismerési művelet nem veszi fel őket. Ha azonban a szavakat egyetlen vonal használatával húzzák át, az átkelészajként lesz kezelve, és a kézírás-felismerési művelet továbbra is felveszi a szavakat.

---

**Kérdés**: *Milyen szövegorientáltságok támogatottak a kézírás-felismerő technológiához?*

**Válasz**: A kézírás-felismerési művelet akár 30-40 fokos szögben is eligazodó szöveget kaphat.

---
