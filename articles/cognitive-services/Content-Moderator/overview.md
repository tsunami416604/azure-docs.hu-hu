---
title: Mi a Content Moderator?
titlesuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható a Content Moderator a felhasználók által létrehozott tartalmakban található nem megfelelő tartalmak nyomon követésére, megjelölésére, értékelésére és szűrésére.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: overview
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: e109376f47d921fb18d7bb9a6252e80315419ec0
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226041"
---
# <a name="what-is-content-moderator"></a>Mi a Content Moderator?

A tartalommoderálás az esetlegesen sértő, nem kívánt és kockázatos tartalmak monitorozási folyamata. A moderált tartalmak lehetnek képek, szövegek és videók.

## <a name="where-it-is-used"></a>Hol használjuk?

Az alábbi lista felsorol néhány példaforgatókönyvet a Content Moderator használatára:

- Online piacterek, amelyek moderálják a termékkatalógusokat és a felhasználók által készített tartalmakat
- Játékcégek, amelyek moderálják a felhasználók által létrehozott játékösszetevőket és a csevegőszobákat
- Közösségi üzenetkezelési platformok, amelyek moderálják a felhasználók által hozzáadott képeket, szövegeket és videókat
- Médianagyvállalatok, amelyek központosított tartalommoderálást alkalmaznak
- K-12 oktatási megoldások szolgáltatói, amelyek kiszűrik a diákok és oktatók számára elérhető helytelen és sértő tartalmakat

## <a name="what-it-includes"></a>Mit tartalmaz?

A Content Moderator számos webszolgáltatás API-t tartalmaz, valamint egy emberi beavatkozást igénylő beépített ellenőrzőeszközt, amely segít a képek, szövegek és videók moderálásában.

![A Content Moderator blokkdiagramja](images/content-moderator-block-diagram.png)

## <a name="apis"></a>API-k

A Content Moderator az alábbi API-kat tartalmazza:
  - [**Szövegmoderálási API**](text-moderation-api.md): Az API használatával észlelhetők a szövegekben az esetlegesen profán, explicit, kétértelmű, sértő és személyes azonosításra alkalmas adatokat tartalmazó (PII) részek.
  - [**Egyéni kifejezéslista API**](try-terms-list-api.md): Az API segítségével a szövegek a beépített kifejezések mellett egyéni kifejezéslistákkal is összevethetők. Ezek a listák segítik a tartalmak tiltását vagy engedélyezését a tartalomházirendeknek megfelelően.  
  - [**Képmoderálási API**](image-moderation-api.md): Az API használatával kiszűrhetők a felnőtteknek szóló és kényes tartalmakat ábrázoló képek, észlelhetők a képeken található szövegek az optikai karakterfelismerés (OCR) segítségével, valamint felismerhetők az arcok.
  - [**Egyéni képlista API**](try-image-list-api.md): Az API használatával a képek összevethetők egyéni képlistákkal, amelyek olyan, előre meghatározott tartalmakat tartalmaznak, melyeket nem szükséges újra besorolni.
  - [**Videomoderálási API**](video-moderation-api.md): Az API használatával kiszűrhetők a felnőtteknek szóló és kényes tartalmakat ábrázoló videók.
  - [**Ellenőrző API-k**](try-review-api-job.md): A [Feladatok](try-review-api-job.md), [Felülvizsgálatok](try-review-api-review.md) és [Munkafolyamat](try-review-api-workflow.md) műveletek segítségével automatizálhatók az emberi beavatkozást igénylő munkafolyamatok a felülvizsgálati eszközön belül.

## <a name="human-review-tool"></a>Emberi felülvizsgálati eszköz

A Content Moderator előfizetésnek része a beépített [emberi felülvizsgálati eszköz](Review-Tool-User-Guide/human-in-the-loop.md). A korábban említett felülvizsgálati API értékeléseket hoz létre a szövegekről, képekről és videókról, amelyek alapján az emberi moderátorok meghozhatják a végleges döntéseket.

![A Content Moderator videovizsgálati eszköze](images/video-review-default-view.png)

## <a name="next-steps"></a>További lépések

[Rövid útmutató](quick-start.md) a Content Moderator használatának első lépéseiről.
