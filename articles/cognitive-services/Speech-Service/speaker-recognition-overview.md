---
title: Azure Speaker Recognition szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Speaker Recognition olyan algoritmusokat biztosít, amelyek egyedi hangtulajdonságokkal rendelkeznek a hangszórók ellenőrzéséhez és azonosításához. Speaker Recognition a "ki beszél?" kérdésre válaszol.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
ms.openlocfilehash: 2d4ce6f274efbd4d8afe2ac48856b0fc312f0a09
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261785"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>Mi az Azure Speaker Recognition szolgáltatás?

A Speaker Recognition szolgáltatás olyan algoritmusokat biztosít, amelyek egyedi hangtulajdonságokkal rendelkeznek a hangszórók ellenőrzéséhez és azonosításához. Speaker Recognition a "ki beszél?" kérdésre válaszol. A hangképzési adatok egyetlen beszélő számára is megadhatók, amely a beszélő hangja egyedi jellemzői alapján létrehoz egy beléptetési profilt. A profilhoz tartozó hangmintákat úgy ellenőrizheti, hogy a beszélő ugyanaz a személy (előadói ellenőrzés), vagy a hangvételi mintákat a regisztrált hangsugárzó-profilok egy *csoportján* keresztül ellenőrzi, hogy az megfelel-e a csoport összes profiljának (a beszélő azonosítása). Ezzel szemben a [beszélő Diarization](batch-transcription.md#speaker-separation-diarization) csoportosítja a hangfelvételek szegmensét egy batch-műveletben.

## <a name="speaker-verification"></a>Beszélő-ellenőrzés

Speaker Verification leegyszerűsíti a regisztrált hangszórók azonosításának folyamatát a jelszóval vagy a szabad formátumú hangbevitelsel. Felhasználható arra, hogy a felhasználók a biztonságos, a súrlódás nélküli ügyfelek és a megoldások széles körének ellenőrzéséhez, az ügyfelek személyazonosságának ellenőrzése a Calling Centers szolgáltatásban, hogy kapcsolatba lépjenek a szolgáltatással.

### <a name="how-does-speaker-verification-work"></a>Hogyan működik Speaker Verification?

![Hogyan működik a beszélő ellenőrzése?](media/speaker-recognition/speaker-rec.png)

A beszélő ellenőrzése szövegtől függő vagy szövegtől független lehet. A **szövegtől függő** ellenőrzés azt jelenti, hogy a hangszóróknak ugyanazt a jelszót kell kiválasztaniuk, amelyet a regisztrálási és ellenőrzési fázisokban is használni kell. A **szövegtől független** ellenőrzés azt jelenti, hogy a hangszórók a beléptetési és ellenőrző kifejezésekben a mindennapos nyelven szólalnak fel.

A **szövegtől függő** ellenőrzéshez a beszélő hangja regisztrálva van az előre definiált kifejezésekből származó hozzáférési kód megadásával. A hangfunkciók a hangrögzítésből származnak, és egyedi hangaláírást alkotnak, míg a kiválasztott hozzáférési kód is felismerhető. A hang aláírása és a jelszó együttes használata a beszélő ellenőrzéséhez használható. 

A **szöveges független** ellenőrzés nem korlátozza azt, amit a beszélő a regisztráció során, vagy a hangmintán ellenőrizni kell, mivel csak a hangfunkciókat fogja kinyerni a hasonlósághoz. 

Az API-k nem határozzák meg, hogy a hang élő személyből, vagy egy beléptetett hangszóró utánzatával vagy felvételével van-e. 

## <a name="speaker-identification"></a>Beszélőazonosítás

Speaker Identification a rendszer egy ismeretlen beszélő identitásának meghatározására szolgál a regisztrált hangszórók egy csoportján belül. A Speaker Identification lehetővé teszi a beszédek egyéni hangszórókhoz való megadását, valamint a több hangszóróval rendelkező forgatókönyvek értékének feloldását, például:

* A távoli értekezletek hatékonyságát támogató megoldások 
* Többfelhasználós eszközök személyre szabásának létrehozása

### <a name="how-does-speaker-identification-work"></a>Hogyan működik Speaker Identification?

A beszélő azonosításának beléptetése **szövegtől független**, ami azt jelenti, hogy nincs korlátozás arra vonatkozóan, hogy a beszélő milyen hangon van. A Speaker Verificationhoz hasonlóan a beléptetési fázisban a beszélő hangja rögzített, a hangfunkciók pedig egyedi hangaláírást alkotnak. Az azonosítási fázisban a bemeneti hangminta összehasonlítható a regisztrált hangok egy adott listájával (az egyes kérésekben legfeljebb 50-re).

## <a name="data-security-and-privacy"></a>Adatbiztonság és adatvédelem

A hangszórók beléptetési adatai biztonságos rendszeren tárolódnak, beleértve a beléptetés hangfelvételét és a hangaláírás funkcióit. A beléptetéshez használt hangfelvételi hang csak akkor használatos, ha az algoritmus frissül, és a szolgáltatásokat újra kell kibontani. A szolgáltatás nem tartja meg a beszédfelismerési fázisban a szolgáltatásnak eljuttatott hangfelvételt vagy kinyert hangfunkciókat. 

Szabályozhatja, hogy mennyi ideig kell megőrizni az adatmegőrzési időtartamot. Az API-hívásokkal létrehozhat, frissíthet és törölhet beléptetési adatkészleteket az egyes hangszórókhoz. Az előfizetés törlésekor a rendszer az előfizetéshez társított összes hangszóró-regisztrációs adatmennyiséget is törli. 

Akárcsak az összes Cognitive Services erőforrás esetében, a Speaker Recognition szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. Győződjön meg arról, hogy megkapta a megfelelő engedélyeket a felhasználóktól Speaker Recognition számára. További információkért tekintse meg a [Cognitive Services page](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   Microsoft adatvédelmi központjának Cognitive Services lapját. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> * Tekintse meg a szöveg-független hangszórók ellenőrzésének [videós oktatóanyagát](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) .
