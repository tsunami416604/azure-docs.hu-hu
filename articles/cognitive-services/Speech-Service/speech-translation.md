---
title: Tolmácsolás – beszédszolgáltatások kapcsolatban
titlesuffix: Azure Cognitive Services
description: A Speech Service API lehetővé teszi az alkalmazások, eszközök és eszközök teljes körű, valós idejű, többnyelvű fordítás beszéd hozzáadását. Az azonos API-t a speech beszéd és a hang-szöveg transzformációs fordítás használható.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 8519e05062ad1b8b8b20dcf5caafddaa07550049
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228729"
---
# <a name="about-the-speech-translation-api"></a>Tudnivalók a beszédalapú fordítási API

A Speech Service API lehetővé teszi az alkalmazások, eszközök és eszközök teljes körű, valós idejű, többnyelvű fordítás beszéd hozzáadását. Az azonos API-t a speech beszéd és a hang-szöveg transzformációs fordítás használható.

A Translator Speech API-val ügyfélalkalmazások adatfolyam speech hangot a szolgáltatáshoz, és vissza adatfolyamban kaphatja kézhez az eredményeket. Ezeket az eredményeket a felismert szöveget tartalmazó az forrás és cél nyelven a fordítás. Ideiglenes fordítások használható befejezéséig az utterance (kifejezés), hogy mely végső fordítás biztosítja.

Szükség esetén a végső fordítás szintetizált hang verzióját is kell készíteni, igaz speech-az-beszédalapú fordítási engedélyezése.

A Speech Translation API egy WebSockets protokollt használ az ügyfél és a kiszolgáló közötti kétirányú kommunikációs csatorna. De nem kell foglalkozni a websockets protokoll; a beszédfelismerés SDK kezeli, amely az Ön számára.

A Speech Translation API, melyek különböző Microsoft-termékek és szolgáltatások technológiákat alkalmaz. Ez a szolgáltatás már használatban van számos vállalkozás világszerte, az alkalmazások és munkafolyamatok.

## <a name="about-the-technology"></a>A technológiával kapcsolatos

Az alapul szolgáló Microsoft fordítási motor két többféle módon is: statisztikai gépi fordítási (SMT) és a Neurális gépi fordítás (NMT). Az utóbbi, mesterséges intelligenciát használó megközelítést alkalmazó Neurális hálózatok, a gépi fordítás korszerűbb módja. NMT jobb fordítások biztosítja – nem csupán pontosabb, de is több fluent és természetes. Ez a gördülékenység elsősorban annak köszönhető, hogy az NMT a mondat teljes kontextusát használja a szavak lefordításához.

Jelenleg Microsoft lett migrálva, NMT a legnépszerűbb nyelvek SMT alkalmazó csak a kevésbé használt nyelvet. Az összes [speech tolmácsolás elérhető nyelvek](language-support.md#speech-translation) NMT működteti. Hang-szöveg transzformációs fordítási attól függően, a nyelv pár SMT vagy NMT használhatja. Abban az esetben, ha a célnyelvet az NMT támogatja, a teljes fordítás NMT-alapú. A Célnyelv NMT által nem támogatott, ha a fordítás egy hibrid NMT és SMT, mint "kimutatást" angol között a két nyelv használatával.

A fordítási motor modell közti különbségekkel csomópontokba. A végfelhasználók csak a továbbfejlesztett fordítási minőség, különösen a kínai, japán és arab figyelje meg.

> [!NOTE]
> Szeretne többet megtudni a Microsoft fordítási motor mögöttes technológia? Lásd: [gépi fordítás](https://www.microsoft.com/en-us/translator/mt.aspx).

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lefordítja a beszéd, a C#-ban való használatáról](how-to-translate-speech-csharp.md)
* [Lásd: how to speech, a C++ fordítása](how-to-translate-speech-cpp.md)
* [Lásd: how to speech javában fordítása](how-to-translate-speech-java.md)
