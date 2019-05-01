---
title: Arcfelismerés és attribútumok fogalmak
titleSuffix: Azure Cognitive Services
description: Ismerje meg az arcfelismerés és arctulajdonságok kapcsolatos alapvető fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572550"
---
# <a name="face-detection-and-attributes"></a>Arcfelismerés és attribútumok

Ez a cikk ismerteti az arcfelismerés és a face attribútumadatok visszaadásához. Arcfelismerés a emberi arcok megkeresése a képet, és igény szerint visszaadása az oldallal kapcsolatos adatokat számos művelet.

Használja a [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) arcfelismerés képen egy művelet. Minimális minden észlelt face felel meg egy **faceRectangle** mezőt a válaszban. Ez egy olyan képpontos koordináták (balra, felső, szélesség, magasság) jelölés található felületére. Használja ezeket a koordinátákat, helyét, a face, valamint annak méretét kérheti le. Az API-válasz az arcok mérete sorrend a legnagyobbtól a legkisebbig láthatók.

## <a name="face-id"></a>Arc azonosítója

A face ID egy egyszerűen csak egy egyedi azonosító karakterlánc minden észlelt lap a képen. Kérheti a face ID, az a [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-hívás.

## <a name="face-landmarks"></a>Arcrészek

Arcrészek egy könnyen megtalálható halmaza, például a tanulók a óraszámlapján vagy orrot csúcsa. Alapértelmezés szerint 27 előre meghatározott arcpont van. A következő ábrán látható összes 27 pont:

![A címkével ellátott összes 27 arcrész face diagram](../Images/landmarks.1.jpg)

A pontok koordinátáit adja vissza képpontban megadva.

## <a name="attributes"></a>Attribútumok

Attribútumok számos további arcfelismerési funkciót, amely igény szerint által észlelt a [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-t. Az attribútumokat, amelyek észlelik a következők:

* **Kor** becsült kora, az éves, egy adott arc.
* **Elmossa** a blurriness, az arcok a képen. Ez visszaad egy értéket, nulla és a egy, valamint egy nem hivatalos minősítése ("alacsony", "közepes", "nagy") között.
* **Kitettség** a az arcok a képen vannak kitéve. Ez visszaad egy értéket, nulla és a egy, valamint egy nem hivatalos minősítése ("alulexponál", "goodExposure", "túlexponálás beállításával") között.
* **Érzelemfelismerési** számára az adott személyt az észlelési magabiztosan érzelmek listáját. Megbízhatósági pontszámok van normalizálva: közötti összes érzelmek pontszámokat felveszi, akár egy. A visszaadott érzelmek boldogság, szomorúság, semleges, düh, megvetés, undor, meglepő és lehetőségének.
* **Arcfelismerési haj** becsült arcfelismerési haj jelenléte és az adott személyt hosszát.
* **A nemek** becsült tartalmazzák az adott személyt. Lehetséges értékek: "Férfi", "női" és "genderless".
* **Szemüveg** hogy rendelkezik-e az adott személyt szemüveg. Lehetséges értékek: "NoGlasses", "ReadingGlasses", "Napszemüvegeket" és "Úszóklub védõszemüveg".
* **Haj** a face haj stílusát. Ez jelzi a haj e látható, hogy baldness észlelt, és milyen haj színek észlelése.
* **A fő jelentenek** a 3D terület a face tájolását. Írja le a terv lényegét, visszaállítási és kitérése szögek fokban. A értéktartományú azok [-90, 90], [-180, 180] és [-90, 90] fok jelölik. Az alábbi ábrában szög leképezések:

    ![Egy fő, a térköz-visszaállítás, és yaw tengelyek címkével](../Images/headpose.1.jpg)
* **Makeup** hogy rendelkezik-e a face makeup. Ez a "eyeMakeup" és "lipMakeup" egy logikai értéket ad vissza.
* **Zaj** a vizuális zaj észlelt az arcfelismerés képen. Ez visszaad egy értéket, nulla és a egy, valamint egy nem hivatalos minősítése ("alacsony", "közepes", "nagy") között.
* **Hangelnyelés** hogy vannak-e objektumok blokkolja a face részeit. Ez a "eyeOccluded", "foreheadOccluded" és "mouthOccluded" egy logikai értéket ad vissza.
* **Mosolyogjon** az adott személyt mosoly definiáló kifejezés. Egy érték nulla (nincs mosoly) és a egy (egyértelmű mosoly) között.

> [!IMPORTANT]
> Arctulajdonságok vannak előre meghatározott statisztikai algoritmusokkal, és előfordulhat, hogy nem pontos. Körültekintően járjon el, ha az attribútum adatokon alapuló döntéseket.

## <a name="input-data"></a>Bemeneti adatok

Az alábbi tippek segítségével győződjön meg arról, a bemeneti képekhez biztosítanak a legpontosabb észlelésének eredménye:

* A bemeneti kép támogatott formátumok a következők: JPEG, PNG, (az első képkocka) GIF és BMP.
* Kép fájlmérete kell nem lehet nagyobb, mint 4 MB.
* Az észlelhető oldallal mérete tartománya 36 x 36, 4096 x 4096 képpont. Arcok a tartományon kívül nem észlelhető.
* Néhány arcok észlelése miatt kihívások helytelen lehet. Szélsőséges face szög (fő testtartás) vagy a face hangelnyelés (például napszemüvegeket vagy blokkolja a face részét kéz objektumok) hatással lehet az észlelés. Elülső és a közel-elülső arcok adja a legjobb eredmények elérése érdekében.

## <a name="next-steps"></a>További lépések

Most, hogy ismeri, a face észlelési fogalmak, ismerje meg, hogyan írhat egy egyszerű szkript, amely a megadott lemezkép arcokat észleli.

* [A kép arcok észlelése](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)