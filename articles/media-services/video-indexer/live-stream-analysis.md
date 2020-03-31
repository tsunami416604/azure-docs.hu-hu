---
title: Élő közvetítés elemzése a Video Indexer használatával
titleSuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan végezhet élő közvetítés elemzést a Video Indexer használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185997"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Élő közvetítés elemzése a Video Indexerrel

Az Azure Media Services videoindexelő egy Azure-szolgáltatás, amelynek célja, hogy kinyerje a mély betekintést a video- és hangfájlok offline. Ez egy adott médiafájl előzetesen létrehozott elemzése. Egyes használati esetekben azonban fontos, hogy a médiaelemzéseket a lehető leggyorsabban beszerezzék egy élő hírfolyamból, hogy időben feloldhassák a működési és egyéb használati eseteket. Az ilyen gazdag metaadatokat például egy élő közvetítésen a tartalomgyártók felhasználhatják a televíziós gyártás automatizálására.

A jelen cikkben ismertetett megoldás lehetővé teszi az ügyfelek számára, hogy a Video Indexer-t közel valós idejű felbontásban használják élő hírcsatornákon. Az indexelés késleltetése akár négy perc is lehet ezzel a megoldással, az indexelt adatok tömbjeitől, a bemeneti felbontástól, a tartalom típusától és a folyamathoz használt számítási alapú teljesítménytől függően.

![A Video Indexer metaadatai az élő közvetítésen](./media/live-stream-analysis/live-stream-analysis01.png)

*1. ábra – Mintalejátszó, amely a Video Indexer metaadatait jeleníti meg az élő közvetítésen*

Az [aktuális adatfolyam-elemzési megoldás](https://aka.ms/livestreamanalysis) az Azure Functions és két logikai alkalmazás segítségével dolgozza fel az Azure Media Services videoindexerrel élő csatornájából származó élő programot, és megjeleníti az eredményt az Azure Media Player segítségével, amely a közel valós idejű adatfolyamot mutatja.

Magas szinten két fő lépésből áll. Az első lépés 60 másodpercenként fut, és az utolsó 60 másodperc alklipjét készíti el, létrehoz belőle egy eszközt, és indexeli azt a Video Indexer en keresztül. Ezután a második lépés neve, ha az indexelés befejeződött. A rögzített elemzéseket a rendszer feldolgozza, elküldi az Azure Cosmos DB-nek, és az indexelt részklip törlődik.

A mintalejátszó lejátssza az élő közvetítést, és egy dedikált Azure-függvény használatával leveszi az azure Cosmos DB-ből származó elemzéseket. Megjeleníti a metaadatokat és a miniatűröket szinkronban az élő videóval.

![Az élő közvetítést percenként feldolgozó két logikai alkalmazás a felhőben](./media/live-stream-analysis/live-stream-analysis02.png)

*2. ábra – A két logikai alkalmazás, amely az élő közvetítést dolgozza fel percenként a felhőben.*

## <a name="step-by-step-guide"></a>Részletes útmutató 

A teljes kód és az eredmények üzembe helyezéséhez használható részletes útmutató megtalálható a [GitHub-projektben a Video Indexer élő médiaelemzéséhez.](https://aka.ms/livestreamanalysis) 

## <a name="next-steps"></a>További lépések

[A Video Indexer áttekintése](video-indexer-overview.md)
