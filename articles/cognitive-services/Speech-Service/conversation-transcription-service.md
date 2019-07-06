---
title: Beszélgetés Beszédátírási – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Beszélgetés Beszédátírási egy speciális funkció, amely valós idejű beszédfelismerés beszélő felismerése és diarization ötvözi a beszédszolgáltatások. Beszélgetés Beszédátírási tökéletes átírás személyesen értekezletek, lehetővé teszi az előadók megkülönböztetni, lehetővé teszi, hogy tudja, akik mondta, mit és mikor, ami lehetővé teszi résztvevők, hogy arra koncentrálhasson, az értekezlet, és gyorsan nyomon követheti a következő lépések. Ez a funkció javítja a kisegítő lehetőségek is. Az átírási Várjuk visszajelzését látási résztvevő aktívan léphet.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 18de3e52d61afd8e1ef20f80c020c8b7d96b9757
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603254"
---
# <a name="what-is-conversation-transcription"></a>Mit jelent a beszélgetés Beszédátírási?

Beszélgetés Beszédátírási egy speciális funkció, amely valós idejű beszédfelismerés beszélő felismerése és diarization ötvözi a beszédszolgáltatások. Beszélgetés Beszédátírási tökéletes átírás személyesen értekezletek, lehetővé teszi az előadók megkülönböztetni, lehetővé teszi, hogy tudja, akik mondta, mit és mikor, ami lehetővé teszi résztvevők, hogy arra koncentrálhasson, az értekezlet, és gyorsan nyomon követheti a következő lépések. Ez a funkció javítja a kisegítő lehetőségek is. Az átírási Várjuk visszajelzését látási résztvevő aktívan léphet.   

Beszélgetés Beszédátírási tesz lehetővé a pontos recognition, amely az iparági és a vállalatra jellemző szókincsből eredőket testre szabhatja testre szabható beszédfelismerés modellel. Emellett meg is párosítsa beszélgetés Beszédátírási a több mikrofon eszközök nyújtotta felhasználói élmény optimalizálása, a beszéd eszközök SDK-val.

>[!NOTE]
> Jelenleg a beszélgetés Beszédátírási kis értekezletek esetén ajánlott. Ha szeretné kiterjeszteni a nagy mennyiségű nagy méretű értekezletek esetén a beszélgetés Beszédátírási, lépjen kapcsolatba velünk a következő címen.

Az ábra azt mutatja, hardver-, szoftver és szolgáltatások, amelyek a beszélgetés Beszédátírási együttműködjenek.

![Az importálás beszélgetés Beszédátírási diagramja](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Egy körkörös hét mikrofon tömb a megadott geometriai konfigurációs szükség. Specifikáció és tervezési tudnivalókat tartalmaz [Microsoft Speech eszköz SDK mikrofon](https://aka.ms/cts/microphone). További információkért, vagy vásároljon egy fejlesztői készletet, lásd: [beolvasása a Microsoft beszédfelismerési eszközoldali SDK](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Beszélgetés Beszédátírási használatának első lépései

Nincsenek három lépést kell tennie a beszélgetés Beszédátírási használatának első lépései.

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
> Beszélgetés Beszédátírási jelenleg "en-US" és "zh-CN" parancsot az alábbi régiókban érhető el: `centralus` és `eastasia`.

* [REST-specifikáció](https://aka.ms/cts/signaturegenservice)
* [Beszélgetés Beszédátírási használata](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Alapuló átírás és azonosíthatja a beszélőket

Beszélgetés Beszédátírási többcsatornás audiostreamek lejátszásával, és a felhasználói profilok vár bemenetként beszédátírás létrehozni, és azonosíthatja a beszélőket. Audio- és felhasználói profil adatainak küldése a beszélgetés Beszédátírási szolgáltatás Speech eszköz SDK-val. Ahogy korábban említettük egy kör alakú hét mikrofon tömb és a Speech Devices SDK-val beszélgetés Beszédátírási használatára van szükség.

>[!NOTE]
> Specifikáció és tervezési tudnivalókat tartalmaz [Microsoft Speech eszköz SDK mikrofon](https://aka.ms/cts/microphone). További információkért, vagy vásároljon egy fejlesztői készletet, lásd: [beolvasása a Microsoft beszédfelismerési eszközoldali SDK](https://aka.ms/cts/getsdk).

Beszélgetés Beszédátírási használata a Speech Devices SDK-val kapcsolatban lásd: [beszélgetés beszédátírási használata](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>– Első lépések a mintaalkalmazás

Microsoft Speech eszközoldali SDK tartalmaz egy gyors üzembe helyezési mintaalkalmazást az összes eszköz kapcsolódó minták. Beszélgetés Beszédátírási az egyik közülük. Annak a [Speech eszközoldali SDK android rövid](https://aka.ms/sdsdk-quickstart) mintaalkalmazás és a forráskód referenciaként.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Speech Devices SDK-val](speech-devices-sdk.md)
