---
title: Video Indexer jelenetek helyességének és kulcsképek – Azure
titlesuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt a Video Indexer jelenetek, helyességének és kulcsképek.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: dfa41dc695cf6ab357a9cd4cdbd32454b6dd107d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896409"
---
# <a name="scenes-shots-and-keyframes"></a>Jelenetek, felvételek és kulcsképkockák

A video Indexer szegmentálásával videók szerkezeti és Szemantikus tulajdonságok alapján historikus egységekbe támogatja. Ez a funkció használatát teszi lehetővé egyszerűen, kezelése, és szerkessze a videó tartalom alapján különböző granularitással. Például alapján jelenetek helyességének és kulcsképek, a jelen témakörben. A **jelenet észlelési** a szolgáltatás jelenleg előzetes verzióban érhető el.   

![Jelenetek, felvételek és kulcsképkockák](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>Jelenet észlelése (előzetes verzió)

A video Indexer határozza meg, ha álló jelenet renderelése; a videóban vizuális jelek alapján változik. Jelenet ábrázolja egy adott eseményhez, és egymást követő helyességének, amely szemantikailag kapcsolódó sorozatát állnak. Jelenet miniatűr az alapul szolgáló képernyőkép-készítés, az első kulcsképkocka. Video indexer – videó szegmensek több egymást követő helyességének szín koherencia alapján jelenetek be, és beolvassa a elején és minden egyes helyszín befejezési időpontja. Jelenet észlelési minősül egy feladat magában foglalja mennyiségi meghatározására videók szemantikai aspektusait.

> [!NOTE]
> Legalább 3 jelenetek tartalmazó videókra alkalmazható.

## <a name="shot-detection"></a>Felvételészlelés

Video Indexer – azt határozza meg, amikor egy képernyőkép változások nyomon követése a szomszédos keretek színsémát hirtelen és a fokozatos átmenetek vizuális jelek alapján a videóban. A célirányítópultot metaadatokat egy kezdési és befejezési időpontja, valamint tartalmazza a képernyőkép-készítés kulcsképek listáját tartalmazza. A helyességének olyan egymást követő keretek ugyanazt a kamerához egyszerre származnak.

## <a name="keyframe-detection"></a>Kulcsképkocka-felismerés

A kocka a képernyőkép-készítés legjobb képviselő választja ki. Kulcsképek a reprezentatív keretek szerezte be a teljes videó esztétikai tulajdonságok (például kontraszt és stableness) alapján. A video Indexer kulcsképkocka azonosítók a célirányítópultot metaadatok, mely ügyfelek kibonthatja a kulcsképkocka miniatűr részeként listájának beolvasása. 

A kimenet JSON helyességének kulcsképek tartoznak. 

## <a name="next-steps"></a>További lépések

[Az API által előállított Videóindexelő kimenetének vizsgálata](video-indexer-output-json-v2.md#scenes)