---
title: Kulcsszavas elnevezési irányelvek – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Hatékony kulcsszó létrehozása elengedhetetlen ahhoz, hogy az eszköz következetesen és pontosan válaszoljon.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 3c3943f7269fa2d0ed25acf2215549635b5f16ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456387"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Útmutató egy hatékony kulcsszó létrehozásához

Hatékony kulcsszó létrehozása elengedhetetlen ahhoz, hogy az eszköz következetesen és pontosan válaszoljon. A kulcsszó személyre szabása hatékony módszert tesz lehetővé az eszköz megkülönböztetésére és a saját arculatának megerősítésére. Ebből a cikkből megtudhatja, hogyan hozhat létre egy hatékony kulcsszót.

## <a name="choose-an-effective-keyword"></a>Érvényes kulcsszó kiválasztása

A kulcsszó kiválasztásakor vegye figyelembe a következő irányelveket:

* A kulcsszónak angol szónak vagy kifejezésnek kell lennie.

* Nem lehet hosszabb két másodpercnél.

* A 4 – 7 szótag szavai a legjobban működnek. Például a "Hé, számítógép" egy jó kulcsszó. Csak a "Hey" egy rossz.

* A kulcsszavaknak a gyakori angol kiejtési szabályokat kell követniük.

* Egy egyedi vagy akár egy, a közös angol Kiejtési szabályt követő szó is csökkentheti a téves pozitív értéket. Előfordulhat például, hogy a "computerama" kulcsszó jó.

* Ne válasszon közös szót. Például az "Eat" és a "Go" kifejezés olyan szavakat mutat be, amelyekben gyakran előfordul a hétköznapi beszélgetés. Lehet, hogy az eszköz hamis eseményindítókat tartalmaz.

* Ne használjon olyan kulcsszót, amely alternatív kiejtésekkel rendelkezhet. A felhasználóknak ismerniük kell a "jobb" kiejtést, hogy az eszközük válaszoljon. Például: "509" lehet "5 0 9", "5 0 9" vagy "509". "R.E.I." "r-e-i" vagy "Ray" lehet. Az "élő" kifejezés "/līv/" vagy "/Liv/" lehet.

* Ne használjon speciális karaktereket, szimbólumokat vagy számokat. Például a "Go #" és a "20 + Cats" lehet problematikus kulcsszó. Azonban a "Go Sharp" vagy a "húsz Plus Cats" is működhet. Továbbra is használhatja a branding szimbólumokat, és a marketing és a dokumentáció használatával megerősítheti a megfelelő kiejtést.

> [!NOTE]
> Ha kulcsszóként választ egy védjeggyel ellátott szót, akkor győződjön meg arról, hogy a védjegy tulajdonosa, vagy hogy a védjegy tulajdonosának van-e engedélye a szó használatára. A Microsoft nem vállal felelősséget az Ön által választott kulcsszótól esetlegesen felmerülő jogi problémákért.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [hozhat létre egyéni kulcsszavakat a Speech Studióval](speech-devices-sdk-create-kws.md).
