---
title: Szöveg-beszéd átalakítás kapcsolatban
description: Szöveg-beszéd átalakítás lehetőségeinek áttekintése.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: eebe41ea879ad34defadd08ce3095f7f6edca4d8
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719800"
---
# <a name="about-the-text-to-speech-api"></a>Tudnivalók a Text to Speech API

A **szöveg-beszéd átalakítás** (Szövegfelolvasás) API-ját a Speech service természetes hangzó beszéddé átalakítja a bemeneti szöveg (más néven *beszédszintézishez*).

Beszéd létrehozásához, az alkalmazás HTTP POST-kérelmet küld a Speech service. Itt szöveg van beszéddé emberi hangzó és a egy hangfájlt értéket küldi vissza. Hangok és nyelvek széles támogatottak.

Melyik beszéd összefoglaló kik forgatókönyvek a következők:

* *Kisegítő lehetőségek javítása:* **szöveg-beszéd átalakítás** technológia lehetővé teszi a tartalmak tulajdonosai, és különböző módokon személyek válaszolni a kiadók használhatja a saját tartalmakat. Készült kisegítő funkciók vagy olvasási nehézségekkel emberek értékeljük képes arra, hogy a tartalmak felhasználása aurally. Hangalapú kimeneti is megkönnyíti a szöveges tartalmakat, például újságok és blogok, miközben utazás, illetve az ilyen mobileszközök csak a felhasználók számára.

* *Többfeladatos feldolgozáshoz forgatókönyvekben válaszol:* **szöveg-beszéd átalakítás** lehetővé teszi személyek számára fontos információk gyorsan és kényelmesen vezetési vagy más módon kívül egy kényelmes környezet olvasása közben. Navigáció: egy közös alkalmazás ezen a területen. 

* *Több módok learning növelése:* különböző emberek különböző módokon Ismerkedjen meg a legjobb. Internetes tanulás szakértőktől kimutatták, hogy együtt nyújtó hanghívási és SMS is könnyebben információk ismerje meg, és azokat megőrizheti.

* *Intuitív robotokat vagy asszisztensek továbbítása:* kapcsolódniuk egy intelligens csevegőrobot, vagy egy virtuális asszisztensek szerves része lehet. Egyre több vállalat fejleszt Csevegés robotokat vonzó ügyfél szolgáltatás élményt biztosít az ügyfelek számára. Hang hozzáadása egy további dimenziót azáltal, hogy a robot válaszok (például telefonon) aurally fogadását.

## <a name="voice-support"></a>Beszédfelismerési támogatása

A Microsoft **szöveg-hang transzformációs** szolgáltatás kínál a több mint 75 beszédhangot legfeljebb 45 nyelvet és területi beállításokat. Ezek szabványos "hangtípust" használatához meg kell adja meg a hangalapú nevét néhány más paraméterekkel, amikor a szolgáltatás REST API-t hívja. A támogatott beszédhangot részletekért lásd: [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#text-to-speech). 

Ha az alkalmazás egyéni szeretne, létrehozhat [egyéni hangtípust](how-to-customize-voice-font.md) saját speech mintákból.

## <a name="api-capabilities"></a>API-funkciók

Nagy mennyiségű képességeit a **szöveg-beszéd átalakítás** API - különös tekintettel testreszabása – REST-en keresztül érhetők el. Az alábbi táblázat foglalja össze az egyes módszerek az API eléréséhez képességeit. A lehetőségeket és az API teljes listáját meg részletesen consult [Swagger](https://swagger/service/11ed9226-335e-4d08-a623-4547014ba2cc#/)

| Használati eset | REST | SDK-k |
|-----|-----|-----|----|
| Töltse fel az adatkészletek a voice-betanítás | Igen | Nem |
| Hozzon létre & hangvezérelt betűtípus modellek kezelése | Igen | Nem |
| & Hangvezérelt betűtípus központi telepítések felügyeletéhez szükséges létrehozása | Igen | Nem |
| Létrehozása és kezelése a hangalapú betűtípus-tesztek| Igen | Nem |
| Előfizetések kezelése | Igen | Nem |

> [!NOTE]
> Az API-t valósít meg, amely korlátozza az API-kérelmek 25, 5 másodpercenként szabályozás. Üzenet hearders tájékoztatni fogja korlátját.

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A REST API használatával beszédfelismerési szintetizálásához való használatáról](how-to-text-to-speech.md)
