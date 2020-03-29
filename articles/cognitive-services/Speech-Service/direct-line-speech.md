---
title: Direct Line Speech - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A Közvetlen vonalbeszéddel és a Beszédszoftver-fejlesztő készlettel (SDK) használó hangalapú beszédfelismerést használó hangasszisztensek funkcióinak, képességeinek és korlátozásainak áttekintése.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 714cdefa7100160a510d1922a2547ae53692d76c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367805"
---
# <a name="what-is-direct-line-speech"></a>Mi az a Direct Line Speech?

**A Direct Line Speech** egy robusztus, végpontok között működő megoldás a rugalmas, bővíthető hangasszisztens létrehozásához. Ez hajtja a Bot Framework és a Direct Line Speech csatorna, hogy van optimalizálva voice-in, voice-out interakció botok.

[A hangasszisztensek figyelik](voice-assistants.md) a felhasználókat, és válaszul cselekszenek, gyakran visszabeszélve. [Beszédről szövegre](speech-to-text.md) történő írást használnak a felhasználó beszédének átírásához, majd lépéseket tesznek a szöveg természetes nyelvi megértésére. Ez a művelet gyakran tartalmazza a [szövegfelolvasással](text-to-speech.md)létrehozott asszisztens szóbeli kimenetét.

A Direct Line Speech a legmagasabb szintű testreszabást és kifinomultságot kínálja a hangasszisztensek számára. Olyan társalgási forgatókönyvekhez készült, amelyek nyílt végűek, természetesek vagy hibridek a kettő között, feladatteljesítéssel vagy parancs-vezérlési használattal. Ez a nagyfokú rugalmasság nagyobb összetettséggel jár, és a jól definiált feladatokra természetes nyelvi bevitelt használó forgatókönyvek érdemes lehet figyelembe venni [az egyéni parancsokat (előzetes verziót)](custom-commands.md) az egyszerűsített megoldásélmény érdekében.

## <a name="getting-started-with-direct-line-speech"></a>Első lépések a direct line beszédfelismeréssel

A közvetlen vonalbeszéddel létrehozott hangsegéd első lépései [a beszéd-előfizetési kulcs leolvasása,](get-started.md)az előfizetéshez társított új robot létrehozása, és a robot csatlakoztatása a Közvetlen vonal beszédcsatornához.

   ![A Direct Line Speech vezénylési szolgáltatásfolyamatának fogalmi diagramja](media/voice-assistants/overview-directlinespeech.png "A beszédcsatorna áramlása")

A Direct Line Speech használatával egy egyszerű hangasszisztens létrehozásához készült teljes, lépésről lépésre szóló útmutatót a [beszédskak és a Közvetlen vonal beszédcsatornájával történő beszédlehetővé tévő üzenetbemutatójában](tutorial-voice-enable-your-bot-speech-sdk.md)talál.

Rövid útmutatókat is kínálunk, amelyek célja a kód futtatása és az API-k gyors tanulása. Ez a táblázat a hangasszisztens-rövid útmutatók listáját tartalmazza, nyelv és platform szerint rendezve.

| Első lépések | Platform | API-referencia |
|------------|----------|---------------|
| C#, UWP | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Tallózás](https://aka.ms/csspeech/javaref) |
| Java | Android | [Tallózás](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Mintakód

A hangsegéd létrehozásához a GitHubon érhető el mintakód. Ezek a minták az ügyfélalkalmazást fedik le, amely több népszerű programozási nyelven csatlakozik az asszisztenshez.

* [Hangasszisztens-minták (SDK)](https://aka.ms/csspeech/samples)
* [Oktatóanyag: A hang funkcióval engedélyezheti asszisztensét a Beszéd SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Testreszabás

A Beszédszolgáltatás sal készült hangasszisztensek a [beszédfelismerési](speech-to-text.md), a [szövegfelolvasó](text-to-speech.md)és [az egyéni kulcsszóválasztásra](speech-devices-sdk-create-kws.md)rendelkezésre álló testreszabási lehetőségek teljes skáláját használhatják .

> [!NOTE]
> A testreszabási lehetőségek nyelvtől/területi beállítástól függően változnak [(lásd: Támogatott nyelvek](supported-languages.md)).

A Direct Line Speech és a hangasszisztensek kapcsolódó funkciói ideális kiegészítői a virtuális asszisztens megoldásnak és a [vállalati sablonnak.](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) Bár a közvetlen vonalbeszéd bármely kompatibilis robottal használható, ezek az erőforrások újrafelhasználható alapkonfigurációt biztosítanak a kiváló minőségű társalgási élményekhez, valamint közös támogató készségeket és modelleket a gyors kezdéshez.

## <a name="reference-docs"></a>Referenciadokumentumok

* [Beszéd SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>További lépések

* [Ingyenes beszédfelismerési szolgáltatás-előfizetési kulcs beszerezni](get-started.md)
* [A beszédfelismerési SDK beolvasása](speech-sdk.md)
* [Alapszintű robot létrehozása és üzembe helyezése](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [A virtuális asszisztens megoldásának és vállalati sablonjának beszereznie](https://github.com/Microsoft/AI)
