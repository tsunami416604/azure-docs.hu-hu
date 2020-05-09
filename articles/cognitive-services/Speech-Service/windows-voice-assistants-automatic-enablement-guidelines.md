---
title: Adatvédelmi irányelvek a hangasszisztensekhez Windows rendszeren
titleSuffix: Azure Cognitive Services
description: A hangutasítások aktiválásának engedélyezése a hangügynök számára alapértelmezés szerint.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997512"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Adatvédelmi irányelvek a hangasszisztensekhez Windows rendszeren

Fontos, hogy a felhasználók egyértelmű információkat kapjanak a hangadatok gyűjtéséről és használatáról, valamint arról, hogy mikor és hogyan történik a gyűjtemény ellenőrzése. Az adatvédelem alapvető aspektusait – a *nyilvánosságra hozatalt* és a betartást *– különösen* fontos a Windowsba integrált hangsegédek számára a használatuk folyamatos figyelése.

A Windowsban a hangasszisztenseket létrehozó fejlesztőknek tartalmazniuk kell a felhasználói felület azon elemeit, amelyek a Segéd figyelési képességeit tükrözik.

> [!NOTE]
> A Segéd-alkalmazás megfelelő nyilvánosságra hozatalának és beleegyezésének hiánya, beleértve az alkalmazások frissítéseinek megadását, az asszisztens nem lesz elérhető a hangalapú aktiváláshoz, amíg az adatvédelmi problémák megoldódik. 

## <a name="minimum-requirements-for-feature-inclusion"></a>A funkciók felvételének minimális követelményei

A Windows-felhasználók láthatják és szabályozhatják a Segéd-alkalmazásaik **`Settings > Privacy > Voice activation`** rendelkezésre állását a alkalmazásban.

 > [!div class="mx-imgBorder"]
 > [![Adatvédelem – alkalmazás – Listázás](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Egy segéd-alkalmazás Windows Voice Activate adatvédelmi beállítási bejegyzése")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Ahhoz, hogy jogosult legyen a listára való felvételre, egy alkalmazásnak a következőket kell tennie:

1. Kiemelten tájékoztathatja a felhasználókat arról, hogy egy kulcsszót figyelnek, még akkor is, ha az alkalmazás nem fut, és mi a kulcsszó
1. Adja meg a felhasználó hangalapú adatainak felhasználásának leírását, beleértve a hivatkozást vagy a kapcsolódó adatvédelmi szabályzatokra mutató hivatkozást is.
1. Tájékoztassa a felhasználókat arról, hogy az alkalmazáson belüli beállításokon kívül a felhasználók megtekinthetik és módosíthatják **`Settings > Privacy > Voice activation`** az adatvédelmi beállításokat a alkalmazásban, opcionálisan megadhatják a közvetlen hozzáférésre `ms-settings:privacy-voiceactivation` szolgáló protokollt is

A követelmények teljesítése és a Microsoft jóváhagyásának megkezdése után a hangaktiválási alkalmazások listájában megjelenik egy segéd-alkalmazás, miután `Windows.ApplicationModel.ConversationalAgent` regisztrált az API-kkal, és a felhasználók engedélyt kaphatnak az alkalmazásnak a kulcsszó aktiválására. Alapértelmezés szerint mindkét beállítás, `Off` és a felhasználónak manuálisan kell megkeresnie a beállítások lapot az engedélyezéshez.

> [!NOTE]
> A hangaktiválási engedélyhez minden esetben mikrofon-engedély szükséges. Ha egy segéd-alkalmazáshoz nem tartozik mikrofon-hozzáférés, nem jogosult a hangaktiválásra, és a rendszer letiltott állapotban fogja megjeleníteni a Hangaktiválás adatvédelmi beállításait.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>A mikrofon belefoglalásával kapcsolatos további követelmények

Az asszisztens szerzők, akik könnyebben és simábban szeretnék megtenni a felhasználók számára a hangaktiválást, ehhez a fentiekhez néhány további követelményt kell benyújtani. A fentieknek való megfelelést követően a Segéd alkalmazás standard, eszköz által zárolt hangaktiválási `On` beállítása alapértelmezés szerint egyszer (és csak egyszer) a mikrofonhoz való hozzáférést kapja meg az alkalmazás számára. Ezzel a művelettel a Segéd aktiválása előtt nem kell további utat megadnia a beállításokhoz.

A további követelmények az, hogy a Segéd-alkalmazásnak a következőket kell tennie:

1. A mikrofon jóváhagyásának **megkezdése előtt** (például az `AppCapability.RequestAccessAsync` API használatával) adja meg azt a felhasználót, akit a Segéd-alkalmazás szeretne hallgatni egy kulcsszót felhasználó hangján, még akkor is, ha az alkalmazás nem fut, és szeretné, ha a felhasználó beleegyezik
2. A mikrofon elérésének vagy az API-k használatának **megkezdése előtt** adja meg az adatfelhasználással és az `Windows.ApplicationModel.ConversationalAgent` adatvédelmi szabályzatokkal kapcsolatos összes releváns információt
3. Kerülje az irányelvek vagy a vezető megfogalmazás elkerülését (például "kattintson az Igen gombra a következő üzenetnél") az élmény folyamatában a hangrögzítési viselkedés kihagyása és az engedély kérelmezése érdekében

Ha ezek a követelmények teljesülnek, egy jogosult segéd-alkalmazás jelenik meg a hangaktiválásra `enabled` jogosult alkalmazások listájában a mikrofonos hozzáférés engedélyezése után.

> [!NOTE]
> A zárolás feletti Hangaktiválás nem jogosult a mikrofonos hozzáférés automatikus engedélyezésére, és továbbra is szükség van a felhasználóra, hogy meglátogassa a Hangaktiválás adatvédelmi lapját, amely lehetővé teszi a fenti zárolási hozzáférés engedélyezését egy asszisztens számára.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók a Windows hangalapú asszisztensekkel kapcsolatos ajánlott eljárásokról](windows-voice-assistants-best-practices.md)