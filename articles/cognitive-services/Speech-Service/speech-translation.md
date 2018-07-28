---
title: Beszédalapú fordítási kapcsolatban
description: Beszédalapú fordítási lehetőségeinek áttekintése
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 7d653a17212c727d65820382e22196d62af086e9
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324408"
---
# <a name="about-the-speech-translation-api"></a>Tudnivalók a beszédalapú fordítási API

A Microsoft Speech API lehetővé teszi az alkalmazások, eszközök és eszközök teljes körű, valós idejű, többnyelvű fordítás beszéd hozzáadását. Az azonos API-t a speech beszéd és a hang-szöveg transzformációs fordítás használható.

A Microsoft Translator Speech API ügyfélalkalmazások adatfolyam speech hangot a szolgáltatáshoz, és vissza adatfolyamban kaphatja kézhez az eredményeket. Ezeket az eredményeket a felismert szöveget tartalmazó az forrás és cél nyelven a fordítás. Ideiglenes fordítások használható befejezéséig az utterance (kifejezés), hogy mely végső fordítás biztosítja.

Szükség esetén a végső fordítás szintetizált hang verzióját is kell készíteni, igaz speech-az-beszédalapú fordítási engedélyezése.

A Speech Translation API egy WebSockets protokollt használ az ügyfél és a kiszolgáló közötti kétirányú kommunikációs csatorna. De nem kell foglalkozni a websockets protokoll; a beszédfelismerés SDK kezeli, amely az Ön számára.

A Speech Translation API, melyek különböző Microsoft-termékek és szolgáltatások technológiákat alkalmaz. Ez a szolgáltatás már használatban van számos vállalkozás világszerte, az alkalmazások és munkafolyamatok.

## <a name="about-the-technology"></a>A technológiával kapcsolatos

Az alapul szolgáló Microsoft fordítási motor két többféle módon is: statisztikai gépi fordítási (SMT) és a Neurális gépi fordítás (NMT). Az utóbbi, mesterséges intelligenciát használó megközelítést alkalmazó Neurális hálózatok, a gépi fordítás korszerűbb módja. NMT jobb fordítások biztosítja – nem csupán pontosabb, de is több fluent és természetes. A kulcs a rugalmasság oka, hogy használja-e NMT mondatok teljes kontextusában szó lefordítandó.

Jelenleg Microsoft lett migrálva, NMT a legnépszerűbb nyelvek SMT alkalmazó csak a kevésbé használt nyelvet. Az összes [speech tolmácsolás elérhető nyelvek](supported-languages.md#speech-translation) NMT működteti. Hang-szöveg transzformációs fordítási attól függően, a nyelv pár SMT vagy NMT használhatja. A Célnyelv NMT által támogatott, ha a teljes fordítás, NMT-alapú. A Célnyelv NMT által nem támogatott, ha a fordítás egy hibrid NMT és SMT, mint "kimutatást" angol között a két nyelv használatával.

A fordítási motor modell közti különbségekkel csomópontokba. A végfelhasználók csak a továbbfejlesztett fordítási minőség, különösen a kínai, japán és arab figyelje meg.

> [!NOTE]
> Szeretne többet megtudni a Microsoft fordítási motor mögöttes technológia? Lásd: [gépi fordítás](https://www.microsoft.com/en-us/translator/mt.aspx).

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lefordítja a beszéd, a C#-ban való használatáról](how-to-translate-speech-csharp.md)
