---
title: Mi az a Speaker Recognition API?
titleSuffix: Azure Cognitive Services
description: A hangszórók ellenőrzése és a hangszórók azonosítása a Cognitive Services Speaker Recognition API.
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73464915"
---
# <a name="speaker-recognition-api---preview"></a>Speaker Recognition API – előzetes verzió

A Speaker Recognition API-k olyan felhőalapú API-k, amelyek fejlett AI-algoritmusokat biztosítanak a hangszórók ellenőrzéséhez és a hangszórók azonosításához. Speaker Recognition két kategóriára oszlik: a hangszórók ellenőrzése és a beszélő azonosítása.

## <a name="speaker-verification"></a>Beszélő-ellenőrzés

A hang egyedi tulajdonságokkal rendelkezik, amelyek egy adott személyhez társíthatók.  Az alkalmazások a hang használatát további tényezőként használhatják az ellenőrzéshez, például a call centerek és a webszolgáltatások esetében.

Speaker Verification API-k intelligens eszközként szolgálnak a felhasználók hang-és beszédfelismerési hozzáférési jelszavuk használatával történő ellenőrzéséhez.

### <a name="enrollment"></a>Beléptetés

A hangszórók ellenőrzésének beléptetése szövegtől függ, ami azt jelenti, hogy a hangsugárzóknak ki kell választaniuk egy adott jelszót, amelyet a regisztrálási és ellenőrzési fázisokban kell használni.

A beszélő beléptetési fázisában a beszélő hangja egy adott kifejezéssel van rögzítve. A hangfunkciók kinyerése egyedi hangaláírást alkot, miközben a kiválasztott kifejezés felismerhető. Ezzel együtt a rendszer a hangszórók regisztrálására szolgáló adatkezelési adatként is ellenőrzi a hangszórót. A hangszórók beléptetési adatai biztonságos rendszeren vannak tárolva. Az ügyfél határozza meg, hogy mennyi ideig kell megőrizni. Az ügyfelek az API-hívásokon keresztül létrehozhatják, frissíthetik és eltávolíthatják az egyes hangszórók regisztrálási szolgáltatásait.  Az előfizetés törlésekor a rendszer az előfizetéshez társított összes hangszóró-regisztrációs adatmennyiséget is törli.

Az ügyfeleknek biztosítaniuk kell, hogy megkapják a megfelelő engedélyeket a felhasználóktól a hangszórók ellenőrzéséhez.

### <a name="verification"></a>Ellenőrzés

Az ellenőrzési fázisban az ügyfélnek meg kell hívnia a beszélő ellenőrző API-t az ellenőrizendő személyhez tartozó AZONOSÍTÓval.  A szolgáltatás kibontja a hangfunkciókat és a jelszót a bemeneti beszéd rögzítéséről. Ezt követően összehasonlítja a szolgáltatásokat a beszélő regisztrációjának megfelelő elemeivel, az ügyfél pedig ellenőrzi, hogy van-e egyezés.  A válasz "elfogadás" vagy "elutasítás" értéket ad vissza különböző megbízhatósági szintekkel.  Az ügyfél ezután meghatározza, hogy az eredmények hogyan használhatók annak eldöntésére, hogy ez a személy a regisztrált szónak.

A küszöbérték megbízhatósági szintjét a forgatókönyv és a használatban lévő egyéb ellenőrzési tényezők alapján kell beállítani. Javasoljuk, hogy kísérletezzen a megbízhatósági szinttel, és vegye figyelembe az egyes alkalmazások megfelelő beállítását. Az API-k nem határozzák meg, hogy a hang élő személyből vagy utánzatból származik-e.

A szolgáltatás nem őrzi meg a szolgáltatásnak az ellenőrzési fázis során eljuttatott beszédfelismerési és kinyert hangfunkciókat.

A beszélő-ellenőrzéssel kapcsolatos további információért tekintse meg a [beszélő-ellenőrzési](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652) API dokumentációját.

## <a name="speaker-identification"></a>Beszélőazonosítás

Az alkalmazások hang használatával azonosíthatják a "ki beszél" csoportot a regisztrált hangszórókból. Speaker Identification API-k olyan forgatókönyvekben használhatók, mint például a hatékonyság, a személyre szabás és a Call Center átírása.

### <a name="enrollment"></a>Beléptetés

A beszélőazonosításba történő regisztráció szövegfüggetlen, ami azt jelenti, hogy nincsenek korlátozások arra vonatkozóan, mit mond a beszélő a hanganyagban. Nincs szükség hozzáférési kód megírására.

A beléptetési fázisban a beszélő hangja rögzített, a hangfunkciók pedig egyedi hangaláírást alkotnak. A kinyert hanghangok és funkciók biztonságos rendszeren vannak tárolva. Az ügyfél ellenőrzi, hogy mennyi ideig tart. Az ügyfelek az API-hívásokon keresztül létrehozhatják, frissíthetik és eltávolíthatják az egyes hangszórókhoz tartozó beléptetési adatkészleteket. Az előfizetés törlésekor a rendszer az előfizetéshez társított összes hangszóró-regisztrációs adatmennyiséget is törli.

Az ügyfeleknek biztosítaniuk kell, hogy megkapják a megfelelő engedélyeket a felhasználóktól a beszélő azonosításához.

### <a name="identification"></a>Azonosítás

Az azonosítási fázisban a hangszóró-azonosító szolgáltatás kibontja a hangfunkciókat a bemeneti beszéd rögzítésével. Ezután összehasonlítja a funkciókat a megadott hangszórók beléptetési adatlistájával. Ha a rendszer megtalál egy egyezést egy regisztrált hangszóróval, a válasz a beszélő AZONOSÍTÓját adja vissza megbízhatósági szinttel.  Ellenkező esetben a válasz "elutasítás" értéket ad vissza, ha egyetlen beszélő sem felel meg egy regisztrált szónak.

A küszöbérték megbízhatósági szintjét a forgatókönyv alapján kell beállítani. Javasoljuk, hogy kísérletezzen a megbízhatósági szinttel, és vegye figyelembe az egyes alkalmazások megfelelő beállítását. Az API-k nem határozzák meg, hogy a hang élő személyből vagy utánzatból származik-e.

A szolgáltatás nem őrzi meg az azonosítási fázisban a szolgáltatásnak eljuttatott beszédfelismerési és kinyert hangfunkciókat.

A hangszórók azonosításával kapcsolatos további információkért tekintse meg az API [-hangszóró – azonosítás](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)című témakört.
