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
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 1d363f9f6f9cb5b1ea28e6ae15a9cef857304cab
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370678"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Útmutató egy hatékony kulcsszó létrehozásához

Hatékony kulcsszó létrehozása elengedhetetlen ahhoz, hogy az eszköz következetesen és pontosan válaszoljon. A kulcsszó személyre szabása hatékony módszert tesz lehetővé az eszköz megkülönböztetésére és a saját arculatának megerősítésére. Ebből a cikkből megtudhatja, hogyan hozhat létre egy hatékony kulcsszót.

## <a name="choose-an-effective-keyword"></a>Érvényes kulcsszó kiválasztása

A kulcsszó kiválasztásakor vegye figyelembe a következő irányelveket:

> [!div class="checklist"]
> * A kulcsszónak angol szónak vagy kifejezésnek kell lennie.
> * Érdemes igénybe legfeljebb két másodpercen övezi.
> * A 4-7 szótagokat határoznak szavak működnek a legjobban. Például a "Hé, számítógép" egy jó kulcsszó. Csak a "Hey", adott gyenge.
> * A kulcsszavaknak a gyakori angol kiejtési szabályokat kell követniük.
> * Egy egyedi vagy akár egy közös angol nyelvű írásmódja szabályok a következő feldolgozott szó előfordulhat, hogy csökkenti a vakriasztások. Előfordulhat például, hogy a "computerama" kulcsszó jó.
> * Ne adja meg egy gyakori szót. Például "étkezési" és "go" olyan szavak, amelyek az emberek gyakran tegyük fel, a szokásos beszélgetésekben. Az eszköz hamis eseményindítók lehetnek.
> * Ne használjon olyan kulcsszót, amely alternatív kiejtésekkel rendelkezhet. Felhasználók a saját eszköz válaszol "megfelelő" kiejtés ismernie kell. Ha például olyan "509" is ejtsd, "öt nulla nine," "öt hoppá kilenc," vagy "öt száz és kilenc." "R.E.I." "az r-e-i" vagy "ray." ejtsd is Is lehet ejtsd a "Live", "/līv/" vagy "/liv/".
> * Ne használja a speciális karaktereket, szimbólumok és számjegy. Például a "Go #" és a "20 + Cats" lehet problematikus kulcsszó. Azonban "Ugrás éles" vagy "húsz macskák plusz" működhetnek. Továbbra is a szimbólumok használata a márkajelzési beállításokat, és marketing- és dokumentáció segítségével a megfelelő kiejtés megerősítése.

> [!NOTE]
> Ha kulcsszóként választ egy védjeggyel ellátott szót, akkor győződjön meg arról, hogy a védjegy tulajdonosa, vagy hogy a védjegy tulajdonosának van-e engedélye a szó használatára. A Microsoft nem vállal felelősséget az Ön által választott kulcsszótól esetlegesen felmerülő jogi problémákért.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [hozhat létre egyéni kulcsszavakat a Speech Studióval](speech-devices-sdk-create-kws.md).
