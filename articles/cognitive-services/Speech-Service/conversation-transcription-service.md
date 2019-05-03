---
title: Beszélgetés Beszédátírási Service – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: A beszélgetés Beszédátírási szolgáltatás egy speciális funkció, amely valós idejű beszédfelismerés beszélő felismerése és diarization ötvözi a beszédszolgáltatások. Beszélgetés Beszédátírási szolgáltatás tökéletes átírás személyesen értekezletek, lehetővé teszi az előadók megkülönböztetni, lehetővé teszi, hogy tudja, akik mondta, mit és mikor, ami lehetővé teszi résztvevők, hogy arra koncentrálhasson, az értekezlet, és gyorsan nyomon követheti a következő lépések. Ez a funkció javítja a kisegítő lehetőségek is. Az átírási Várjuk visszajelzését látási résztvevő aktívan léphet.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2c3269a9e57c0392e152fedfb640b9fae91a4989
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025935"
---
# <a name="what-is-the-conversation-transcription-service"></a>Mi a beszélgetés Beszédátírási szolgáltatás?

A beszélgetés Beszédátírási szolgáltatás egy speciális funkció, amely valós idejű beszédfelismerés beszélő felismerése és diarization ötvözi a beszédszolgáltatások. Beszélgetés Beszédátírási szolgáltatás tökéletes átírás személyesen értekezletek, lehetővé teszi az előadók megkülönböztetni, lehetővé teszi, hogy tudja, akik mondta, mit és mikor, ami lehetővé teszi résztvevők, hogy arra koncentrálhasson, az értekezlet, és gyorsan nyomon követheti a következő lépések. Ez a funkció javítja a kisegítő lehetőségek is. Az átírási Várjuk visszajelzését látási résztvevő aktívan léphet.   

Beszélgetés Beszédátírási szolgáltatás, amely az iparági és a vállalatra jellemző szókincsből eredőket testre szabhatja testre szabható beszédfelismerés modellel pontos felismerése tesz lehetővé. Emellett meg is párosítsa beszélgetés Beszédátírási szolgáltatás a több mikrofon eszközök nyújtotta felhasználói élmény optimalizálása, a beszéd eszközök SDK-val.

>[!NOTE]
> Beszélgetés Beszédátírási szolgáltatás jelenleg kis értekezletek esetén ajánlott. Ha szeretné bővíteni a beszélgetés Beszédátírási szolgáltatás nagy mennyiségű nagy méretű értekezletek esetén, lépjen kapcsolatba velünk a következő címen.

Az ábra azt mutatja, hardver-, szoftver és szolgáltatások, amelyek működnek együtt beszélgetés Beszédátírási szolgáltatás.

![Az importálás beszélgetés Beszédátírási Szolgáltatásdiagram](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Egy körkörös hét mikrofon tömb a megadott geometriai konfigurációs szükség. Specifikáció és tervezési tudnivalókat tartalmaz [Microsoft Speech eszköz SDK mikrofon](https://aka.ms/cts/microphone). További információkért, vagy vásároljon egy fejlesztői készletet, lásd: [beolvasása a Microsoft beszédfelismerési eszközoldali SDK](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Beszélgetés Beszédátírási használatának első lépései

Nincsenek három lépést kell tennie a beszélgetés Beszédátírási szolgáltatás használatának első lépései.

1. Hangalapú minták gyűjteni a felhasználók.
2. Hozzon létre felhasználói profilok felhasználói voice-példák használata
3. A beszédfelismerés SDK segítségével azonosítsa azokat a felhasználókat (előadói), és átvezeti beszéd

## <a name="collect-user-voice-samples"></a>Felhasználói hangalapú mintákat gyűjtsön

Az első lépéseként a hanganyag gyűjtésére minden felhasználóhoz. Felhasználói speech háttérzaj nélkül csendes környezetben kell rögzíteni. Az ajánlott minden egyes hang minta hossza 30 másodperc és két perc között. Előadók hosszabb hangmintát eredményez pontosság jobb legyen. Hang 16 KHz minta arány monó csatornát kell lennie.

A fent említett útmutatást túli hang rögzíti, valamint tárolt van –, akár egy biztonságos adatbázisban ajánlott. A következő szakaszban először áttekintjük, hogyan a hang előadói felismerni a Speech SDK-val használható felhasználói profilok létrehozására szolgál.

## <a name="generate-user-profiles"></a>Felhasználói profilok létrehozása

Ezt követően kell küldeni a hangfelvételeket érvényesítéséhez a hang és a felhasználói profilok létrehozása a aláírási generációs szolgáltatás összegyűjtött. A [aláírását létrehozó szolgáltatások](https://aka.ms/cts/signaturegenservice) REST API-k, amelyek lehetővé teszik, és felhasználói profilok lekéréséről készlete.

Felhasználói profil létrehozása kell használni a `GenerateVoiceSignature` API-t. Specifikáció részleteit és a mintakódot érhetők el:

> [!NOTE]
> A beszélgetés Beszédátírási szolgáltatás érhető el jelenleg a következő régiókban: `centralus` és `eastasia`.

* [REST-specifikáció](https://aka.ms/cts/signaturegenservice)
* [Beszélgetés Beszédátírási szolgáltatás használata](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Alapuló átírás és azonosíthatja a beszélőket

A beszélgetés Beszédátírási szolgáltatás többcsatornás audiostreamek lejátszásával, és a felhasználói profilok vár bemenetként beszédátírás létrehozni, és azonosíthatja a beszélőket. Audio- és felhasználói profil adatainak érkeznek a beszélgetés Beszédátírási szolgáltatás Speech eszköz SDK-val. Ahogy korábban említettük egy kör alakú hét mikrofon tömb és a Speech Devices SDK-val a beszélgetés Beszédátírási szolgáltatás használatára van szükség.

>[!NOTE]
> Specifikáció és tervezési tudnivalókat tartalmaz [Microsoft Speech eszköz SDK mikrofon](https://aka.ms/cts/microphone). További információkért, vagy vásároljon egy fejlesztői készletet, lásd: [beolvasása a Microsoft beszédfelismerési eszközoldali SDK](https://aka.ms/cts/getsdk).

A beszélgetés Beszédátírási szolgáltatás használata a Speech Devices SDK-val kapcsolatban lásd: [beszélgetés beszédátírási szolgáltatás használata](https://aka.ms/cts/howto).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Speech Devices SDK-val](speech-devices-sdk.md)
