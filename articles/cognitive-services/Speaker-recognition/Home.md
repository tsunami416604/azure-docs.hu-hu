---
title: Mi az a Speaker Recognition API?
titleSuffix: Azure Cognitive Services
description: Hangszóró-ellenőrzés és hangszóró-azonosítás a Kognitív szolgáltatások hangszóró-felismerési API-jával.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73464915"
---
# <a name="speaker-recognition-api---preview"></a>Hangszórófelismerési API – előzetes verzió

A hangszórófelismerési API-k olyan felhőalapú API-k, amelyek fejlett AI-algoritmusokat biztosítanak a hangszóró-ellenőrzéshez és a hangszóró-azonosításhoz. Az előadófelismerés két kategóriába sorolható: a hangszóró ellenőrzése és a hangszóró azonosítása.

## <a name="speaker-verification"></a>Beszélő-ellenőrzés

A hang nak egyedi jellemzői vannak, amelyek az egyénhez társíthatók.  Az alkalmazások további tényezőként használhatják a hangot az ellenőrzéshez olyan esetekben, mint a call centerek és a webszolgáltatások.

A hangszóró-ellenőrzési API-k intelligens eszközként szolgálnak a felhasználók hang- és beszédjelmondataik használatával történő ellenőrzéséhez.

### <a name="enrollment"></a>Regisztráció

A hangszóró-ellenőrzésre való regisztráció szövegfüggő, ami azt jelenti, hogy a felszólalóknak ki kell választaniuk egy adott jelszót, amelyet a beléptetési és ellenőrzési fázisokban is használni kell.

A hangszóró beiratkozási fázisában a beszélő hangját rögzítik, mondván, hogy egy adott mondat. A hangfunkciók kibontása egyedi hangaláírást alkot, miközben a kiválasztott kifejezés felismerhető. Ez a hangszóró-regisztrációs adatok együttesen a hangszóró ellenőrzésére szolgálnak. A hangszóró regisztrációs adatai biztonságos rendszerben tárolódnak. Az Ügyfél határozza meg, hogy mennyi ideig őrizze meg. Az ügyfelek API-hívásokon keresztül hozhatnak létre, frissíthetnek és távolíthatnak el regisztrációs adatokat az egyes hangszórókhoz.  Az előfizetés törlésekor az előfizetéshez társított összes hangszóróregisztrációs adat is törlődik.

Az ügyfeleknek gondoskodniuk kell arról, hogy a felhasználóktól megfelelő engedélyeket kaptak a hangszóró ellenőrzéséhez.

### <a name="verification"></a>Ellenőrzés

Az ellenőrzési fázisban az Ügyfélnek fel kell hívnia a hangszóró-ellenőrzési API-t az ellenőrizendő személyhez társított azonosítóval.  A szolgáltatás kinyeri a hangfunkciókat és a bemeneti beszédfelvétel jelmondatát. Ezután összehasonlítja a funkciókat a hangszóró regisztrációs adatainak megfelelő elemeivel a hangszóró számára, amelyet az Ügyfél ellenőrizni és meghatározni kíván.  A válasz különböző megbízhatósági szintekkel rendelkező "elfogadás" vagy "elutasítás" értéket ad vissza.  Az Ügyfél ezután meghatározza, hogyan használja fel az eredményeket annak eldöntésére, hogy ez a személy-e a regisztrált előadó.

A küszöbérték megbízhatósági szintjét a forgatókönyv és az egyéb használt ellenőrzési tényezők alapján kell meghatározni. Azt javasoljuk, hogy kísérletezzen a megbízhatósági szint, és fontolja meg a megfelelő beállítást az egyes alkalmazások. Az API-k nem annak meghatározására szolgálnak, hogy a hang élő személytől származik-e, vagy egy beiratkozott hangszóró utánzata vagy felvétele.

A szolgáltatás nem őrzi meg a beszédrögzítést vagy a kinyert hangfunkciókat, amelyeket az ellenőrzési fázisban a szolgáltatásnak küldenek.

A beszélő-ellenőrzéssel kapcsolatos további információért tekintse meg a [beszélő-ellenőrzési](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652) API dokumentációját.

## <a name="speaker-identification"></a>Beszélőazonosítás

Az alkalmazások hanggal azonosíthatják a "ki beszél" nyelvet a beiratkozott hangszórók egy csoportja alapján. A hangszóró-azonosító API-k olyan esetekben használhatók, mint a munkaértekezletek hatékonysága, személyre szabása és a hívásközpont-átírás.

### <a name="enrollment"></a>Regisztráció

A beszélőazonosításba történő regisztráció szövegfüggetlen, ami azt jelenti, hogy nincsenek korlátozások arra vonatkozóan, mit mond a beszélő a hanganyagban. Nincs szükség jelszóra.

A beléptetési fázisban a hangszóró hangja rögzítésre kerül, és a hangfunkciók kinyerése egyedi hangaláírást hoz létre. A beszédhang és a kinyert funkciók biztonságos rendszerben tárolódnak. A Vevő határozza meg, hogy mennyi ideig őrzi meg. Az ügyfelek api-hívásokon keresztül hozhatják létre, frissíthetik és távolíthatják el ezeket a hangszóró-regisztrációs adatokat az egyes hangszórók számára. Az előfizetés törlésekor az előfizetéshez társított összes hangszóróregisztrációs adat is törlődik.

Az ügyfeleknek gondoskodniuk kell arról, hogy a felhasználóktól megfelelő engedélyeket kaptak a hangszóró-azonosításhoz.

### <a name="identification"></a>Azonosítás

Az azonosítási fázisban a hangszóró-azonosító szolgáltatás kinyeri a hangfunkciókat a bemeneti beszédfelvételből. Ezután összehasonlítja a funkciókat a megadott hangszórók regisztrációs adataival. Ha egyezést talál egy regisztrált hangszóróval, a válasz a beszélő azonosítóját adja vissza megbízhatósági szinttel.  Ellenkező esetben a válasz "elutasítást" ad vissza, ha egyetlen előadó sem egyezik meg egy regisztrált előadóval.

A küszöbérték megbízhatósági szintjét a forgatókönyv alapján kell meghatározni. Azt javasoljuk, hogy kísérletezzen a megbízhatósági szint, és fontolja meg a megfelelő beállítást az egyes alkalmazások. Az API-k nem annak meghatározására szolgálnak, hogy a hang élő személytől származik-e, vagy egy beiratkozott hangszóró utánzata vagy felvétele.

A szolgáltatás nem őrzi meg a beszédrögzítést vagy a kinyert hangfunkciókat, amelyeket a rendszer az azonosítási fázishoz küld a szolgáltatásnak.

A hangszóró azonosításával kapcsolatos további részletekért olvassa el az API [hangszóró - azonosítás című dokumentumát.](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)
