---
title: A Video Indexer webhely segítségével testre szabhatja egy személy modell – Azure
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a Video Indexer webhelyet egy személy modellt.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 29c32126a5bbe792dd6fe88ae189e09c511d2a22
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997057"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>A Video Indexer webhely személy modell testreszabása

A video Indexer videótartalmak támogatja az arcfelismerés és hírességek felismerése. A hírességek felismerése funkció körülbelül 1 millió arcok IMDB Wikipedia és felső LinkedIn véleményvezérek például gyakran lekérdezett adatforrás alapján ismerteti. Észlelt, amely nem ismeri fel a híresség-felismerés szolgáltatás; azonban van hátra névtelen. Miután a videó feltöltése a Video Indexer, és vissza eredményt, lépjen vissza, és nevezze el az arcok, amely nem ismerhetők fel. Miután Ön címke egy ARC nevére, a fiókhoz tartozó személy modell hozzáadja az arcfelismerés és nevét. Video Indexer majd fogja felismerni a face a jövőbeli videók és a múltbeli videókat.

A Video Indexer webhely használatával, amely az észlelt a videó szerkesztése ebben a témakörben leírtak szerint. Is használhatja az API-t, a leírtak szerint [testreszabás személy modell API-k használatával](customize-person-model-with-api.md).

## <a name="edit-a-face"></a>A face szerkesztése

> [!NOTE]
> Nevek egyediek-e egy személy modellek, így nevet két különböző arcokat ugyanazzal a névvel, ha megtekinti az arcok, ugyanazt az embert Video Indexer, és össze legyen vonva őket, ha Ön a videó újraindexelése. Ha látja, hogy a Video Indexer a azonos face több különböző előfordulása észleli, tegye lehetővé számukra ugyanazzal a névvel, és újraindexelni a videó.
> Frissítheti a egy oldallal, amely a Video Indexer a hírességek, más néven ismeri fel. Az új nevet, amelyet átadhat elsőbbséget élveznek a beépített hírességek felismerése.

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. Keresse meg a kívánt videót megtekintése és szerkesztése a fiókjában.
3. A face a videó szerkesztéséhez lépjen a **Insights** lapra, és kattintson a ceruza ikonra az ablak jobb felső sarkában.

    ![Személy modell testreszabása](./media/customize-face-model/customize-face-model.png)

4. Az egyes az észlelt arcok, és módosítsa a nevük "Ismeretlen #x2713" (vagy a neve, amely korábban már hozzá lett rendelve a face).
5. Után írja be az új nevet, kattintson a pipa ikonra az új neve mellett. Ezzel mentse az új nevét, és ismeri fel, és nevezze el a többi aktuális videók és a jövőben feltöltött videók a face összes előfordulását. Felismerés, a többi aktuális videóit a face érvénybe lépéséhez, mivel ez egy batch-folyamat hosszabb időt is igénybe vehet. 

    ![A frissítés mentése](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>A face törlése

A videó egy felismert arc törléséhez nyissa meg a **Insights** ablaktáblán, majd kattintson a ceruza ikonra a jobb felső sarokban található meg a. Kattintson a **törlése** lehetőséget a face neve alatt. Ezzel eltávolítja a videót a face észlelt. A face továbbra is észlelhető, amelyben megjelennek a más videókat, de a face ezeket videókkal után törölheti indexelve van. A face továbbra is a fiókhoz tartozó személy modell szerepel, ha azok törlése előtt neve.

## <a name="next-steps"></a>További lépések

[API-k használatával személy modell testreszabása](customize-person-model-with-api.md)
