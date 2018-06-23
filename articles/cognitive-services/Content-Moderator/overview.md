---
title: Az Azure Content moderátor áttekintése |} Microsoft Docs
description: 'Útmutató: a tartalom moderátor segítségével nyomon követheti, ez a jelző, felmérési és szűrheti a felhasználók által létrehozott tartalom nem megfelelő tartalom.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: cd9e2e9c10e9dc5ba118c8319f76174bf6f0da9f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349379"
---
# <a name="what-is-content-moderator"></a>Mi az a tartalom moderátor?

Tartalom moderálás lehetséges sértő nemkívánatos és kockázatos tartalom figyelése során a rendszer. A moderált tartalom lemezképek, a szöveg és a videók lehet.

## <a name="where-it-is-used"></a>Felhasználási

Az alábbi lista tartalmazza az alábbiakban néhány példaforgatókönyvet hol használják a tartalom moderátor:

- Online piacterek moderálás a termék katalógusok és a felhasználók által létrehozott tartalom
- Felhasználói moderálás játék vállalatok jön létre, játék-összetevők és a Csevegés
- Képek, szöveg és a felhasználó által felvett videók moderálás közösségi üzenetkezelési platform
- A tartalom központi tartalom moderálás végrehajtási vállalati media vállalatok
- Szűrés rossz és durva tartalom a diákok és nevelők K-12 oktatási megoldásszállítók

## <a name="what-it-includes"></a>Ez magában foglalja

Tartalom moderátor áll több webszolgáltatás API-k és beépített emberi-az-a-hurkot tekintse át is mérsékelt képek, szöveg és videók.

![Tartalom moderátor blokk diagramja](images/content-moderator-block-diagram.png)

## <a name="apis"></a>API-k

Tartalom moderátor tartalmazza a következő API-kat:
  - [**Szöveg moderálás API**](text-moderation-api.md): Ellenőrizze a lehetséges Profanitás explicit, kétértelmű, sértő és személyes azonosításra alkalmas adatokat (PII) szövege az API segítségével.
  - [**Egyéni kifejezés lista API**](try-terms-list-api.md): Ez az API segítségével egyéni listájával mellett a beépített használati feltételek egyeznek. Ezek segítségével blokkolhatja vagy engedélyezheti a tartalom a tartalom házirendek szerint.  
  - [**Kép moderálás API**](image-moderation-api.md): Ez az API segítségével felnőtt és ellopható tartalom képek, szöveg felismeri a képek a optikai (OCR) képességgel rendelkező, és felületei észlelése.
  - [**Egyéni képlistában API**](try-image-list-api.md): Ez az API segítségével egyéni listájának lemezképeket, nem kell újra besorolása előre meghatározott tartalom egyeztetéshez.
  - [**Videó moderálás API**](video-moderation-api.md): Ez az API használatával végezze el a potenciális felnőtt és ellopható tartalom videók vizsgálatát.
  - [**Tekintse át az API-k**](try-review-api-job.md): használja a [feladatok](try-review-api-job.md), [értékelést](try-review-api-review.md), és [munkafolyamat](try-review-api-workflow.md) művelet létrehozása és belül emberi hurok munkafolyamatainak automatizálásához a felülvizsgálati eszköz.

## <a name="human-review-tool"></a>Emberi vizsgálóeszköz

A tartalom moderátor előfizetés tartalmazza a beépített [emberi felülvizsgálati eszköz](Review-Tool-User-Guide/human-in-the-loop.md). A korábban említett felülvizsgálati API segítségével hozzon létre a szöveg, képek és videók a végső döntéshozatali emberi moderátorok az értékelést.

![Tartalom moderátor videó eszköz áttekintése](images/video-review-default-view.png)

## <a name="next-steps"></a>További lépések

Használja a [gyors üzembe helyezés](quick-start.md) tartalom moderátor használatába.
