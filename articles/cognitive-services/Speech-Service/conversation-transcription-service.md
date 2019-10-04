---
title: Beszélgetés átirata – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszélgetés átírása a beszédfelismerési szolgáltatások olyan speciális funkciója, amely ötvözi a valós idejű beszédfelismerést, a hangszórók azonosítását és a diarization. A beszélgetés átirata tökéletes megoldás a személyes értekezletek átírására, és lehetővé teszi a beszélők megkülönböztetését, és megtudhatja, hogy ki mit és mikor, így a résztvevők az értekezletre összpontosítanak, és gyorsan követhetik a következő lépéseket. Ez a funkció a kisegítő lehetőségeket is javítja. Az átirattal aktívan részt vehet a résztvevőkkel a tárgyaláson.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acbf82335523ee226f4b899180f0f22cb94cca5e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562877"
---
# <a name="what-is-conversation-transcription"></a>Mi a beszélgetés átirata?

A beszélgetés átírása a beszédfelismerési szolgáltatások olyan speciális funkciója, amely ötvözi a valós idejű beszédfelismerést, a hangszórók azonosítását és a diarization. A beszélgetés átirata tökéletes megoldás a személyes értekezletek átírására, és lehetővé teszi a beszélők megkülönböztetését, és megtudhatja, hogy ki mit és mikor, így a résztvevők az értekezletre összpontosítanak, és gyorsan követhetik a következő lépéseket. Ez a funkció a kisegítő lehetőségeket is javítja. Az átirattal aktívan részt vehet a résztvevőkkel a tárgyaláson.   

A társalgási átiratok pontos felismerést biztosítanak a testreszabható beszédfelismerési modellekkel, amelyek az iparág és a vállalatra jellemző szókincs megismerésére alkalmasak. Emellett a beszédfelismerési eszközök SDK-val is párosíthatja a beszélgetéseket, így optimalizálhatja a több mikrofonos eszközökön tapasztalható élményt.

>[!NOTE]
> A beszélgetés átírása jelenleg kis értekezletek esetében ajánlott. Ha szeretné kiterjeszteni a beszélgetés átírását nagy méretű értekezletek esetén, vegye fel velünk a kapcsolatot.

Ez az ábra azokat a hardvereket, szoftvereket és szolgáltatásokat mutatja be, amelyek együttműködnek a beszélgetés átírásával.

![Az importálási beszélgetés átiratának diagramja](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> A megadott geometriai konfigurációval rendelkező kör alakú, hét mikrofonos tömböt kell megadni. A specifikációk és a kialakítás részleteiért lásd: [Microsoft Speech Device SDK mikrofon](https://aka.ms/cts/microphone). További információért vagy a fejlesztői csomag megvásárlásához lásd: [Microsoft Speech Device SDK](https://aka.ms/cts/getsdk)beszerzése.

## <a name="get-started-with-conversation-transcription"></a>Ismerkedés a társalgási átirattal

Három lépésben kell megkezdenie a beszélgetés átírását.

1. Hangmintákat gyűjthet a felhasználóktól.
2. Felhasználói profilok előállítása a felhasználói hangminták használatával
3. A beszédfelismerési SDK használata a felhasználók (hangszórók) azonosítására és a beszéd átírására

## <a name="collect-user-voice-samples"></a>Felhasználói hangminták gyűjtése

Az első lépés a hangfelvételek összegyűjtése minden felhasználótól. A felhasználói beszédet csendes környezetben, háttérzaj nélkül kell rögzíteni. Az egyes hangmintákhoz javasolt hossz 30 másodperc és két perc között lehet. A több hangminta nagyobb pontosságot eredményez a hangszórók azonosítása során. A hangnak monó csatornának kell lennie, 16 KHz-es mintavételi sebességgel.

A fent említett útmutatáson túl a hangrögzítés és a tárolt adatok tárolása is megtörténik – egy biztonságos adatbázis ajánlott. A következő szakaszban áttekintjük, hogyan használható a hang a Speech SDK-val a beszélők felismeréséhez használt felhasználói profilok létrehozásához.

## <a name="generate-user-profiles"></a>Felhasználói profilok előállítása

Ezután el kell küldenie az összegyűjtött hangfelvételeket az aláírás-létrehozási szolgáltatásba, hogy érvényesítse a hanganyagot, és létrehozza a felhasználói profilokat. Az [aláírás-létrehozási szolgáltatás](https://aka.ms/cts/signaturegenservice) olyan REST API-k összessége, amelyek lehetővé teszik felhasználói profilok létrehozását és lekérését.

Felhasználói profil létrehozásához az `GenerateVoiceSignature` API-t kell használnia. A specifikáció részletei és a mintakód elérhető:

> [!NOTE]
> A beszélgetés átírása jelenleg az "en-us" és a "zh-CN" nyelven érhető el `centralus` a `eastasia`következő régiókban: és.

* [REST-specifikáció](https://aka.ms/cts/signaturegenservice)
* [A beszélgetés átiratának használata](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Hangszórók átírása és azonosítása

A társalgási átirat a többcsatornás hangadatfolyamokat és a felhasználói profilokat bemenetként adja át átiratok létrehozásához és a hangszórók azonosításához. A hang-és felhasználói profilok adatai a beszélgetési átirat szolgáltatásba kerülnek a Speech Devices SDK használatával. Ahogy korábban említettük, egy kör alakú Seven mikrofon-tömb és a Speech Devices SDK szükséges a beszélgetési átirat használatához.

>[!NOTE]
> A specifikációk és a kialakítás részleteiért lásd: [Microsoft Speech Device SDK mikrofon](https://aka.ms/cts/microphone). További információért vagy a fejlesztői csomag megvásárlásához lásd: [Microsoft Speech Device SDK](https://aka.ms/cts/getsdk)beszerzése.

Ha szeretné megtudni, hogyan használhatja a beszédfelismerést a Speech Devices SDK-val, olvassa el a következő témakört: a [beszélgetés átírásának használata](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>gyorskonfigurálás egy minta alkalmazással

A Microsoft Speech Device SDK egy gyors üzembe helyezési minta alkalmazást tartalmaz az összes eszközhöz kapcsolódó mintához. A beszélgetés átírása az egyik. A dokumentációban megtalálja a [beszédfelismerési eszköz SDK Android](https://aka.ms/sdsdk-quickstart) -útmutatójában a minta alkalmazást és annak forráskódját.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Speech Devices SDK-ról](speech-devices-sdk.md)
