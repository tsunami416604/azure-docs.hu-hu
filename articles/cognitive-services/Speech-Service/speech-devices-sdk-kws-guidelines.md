---
title: Kulcsszóelnevezési irányelvek – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Hatékony kulcsszó létrehozása elengedhetetlen annak biztosításához, hogy a készülék következetesen és pontosan reagáljon.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 1d363f9f6f9cb5b1ea28e6ae15a9cef857304cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370678"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>A hatékony kulcsszó létrehozásának irányelvei

Hatékony kulcsszó létrehozása elengedhetetlen annak biztosításához, hogy a készülék következetesen és pontosan reagáljon. A kulcsszó testreszabása hatékony módja annak, hogy megkülönböztesse az eszközt és erősítse a márkajelzést. Ebben a cikkben megtanulhat néhány vezérelvet a hatékony kulcsszó létrehozásához.

## <a name="choose-an-effective-keyword"></a>Hatékony kulcsszó kiválasztása

Kulcsszó kiválasztásakor vegye figyelembe az alábbi irányelveket:

> [!div class="checklist"]
> * A kulcsszónak angol szónak vagy kifejezésnek kell lennie.
> * Nem tarthat tovább két másodpercnél.
> * A 4-7 szótagjas szavak működnek a legjobban. Például a "Hey, Computer" egy jó kulcsszó. Csak a "Hé" egy szegény.
> * A kulcsszavaknak az angol nyelvre vonatkozó általános szabályokat kell követniük.
> * A gyakori angol kiejtési szabályokat követő egyedi vagy akár kitalált szó csökkentheti a hamis pozitív értékeket. Például a "computerama" jó kulcsszó lehet.
> * Ne válasszon egy közös szót. Például az "enni" és a "menj" olyan szavak, amelyeket az emberek gyakran mondanak a hétköznapi beszélgetésekben. Lehet, hogy hamis eseményindítók az eszköz.
> * Ne használjon olyan kulcsszót, amelynek esetleg van alternatív kiejtése. A felhasználóknak ismerniük kell a "helyes" kiejtést, hogy az eszközük reagáljon. Az "509" szövege lehet például "öt nulla kilenc", "öt ó kilenc" vagy "ötszázkilenc". "R.E.I." lehet ejteni "r-e-i" vagy "ray.". Az "élő" kiejthető "/līv/" vagy "/liv/".
> * Ne használjon speciális karaktereket, szimbólumokat vagy számjegyeket. Például a "Go#" és a "20 + cats" problémás kulcsszavak lehetnek. Bármennyire, " megy éles" vagy " húsz plusz macskák" erő dolgozik. Továbbra is használhatja a szimbólumokat a márkajelzésben, és marketing és dokumentáció segítségével megerősítheti a megfelelő kiejtést.

> [!NOTE]
> Ha egy védjeggyel ellátott szót választ kulcsszóként, győződjön meg arról, hogy ön a védjegy tulajdonosa, vagy hogy a védjegy tulajdonosának engedélye van a szó használatára. A Microsoft nem vállal felelősséget az Ön által választott kulcsszóválasztásból eredő jogi problémákért.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [hozhat létre egyéni kulcsszót a Speech Studio használatával.](speech-devices-sdk-create-kws.md)
