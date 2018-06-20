---
title: Kvóták és korlátozások az Azure Media Services v3 |} Microsoft Docs
description: Ez a témakör ismerteti a kvóták és korlátozások az Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: juliako
ms.openlocfilehash: 75bfb0d5d29f0b8e038e68af08130564b72a05bf
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266753"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóták és korlátozások az Azure Media Services v3

Ez a cikk ismerteti a kvóták és korlátozások az Azure Media Services v3.

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Egy Azure Media Services-fiók eszközök | 1,000,000|
| Egy feladat JobInputs | 50 |
| Egy feladat JobOutputs | 20 (rögzített) |
| Fájlméret| Bizonyos esetekben korlátozva van a Media Services feldolgozás támogatott maximális fájlméretet. <sup>(1)</sup> |
| Egy Media Services-fiók feladatok | 500 000 értéket <sup>(2)</sup>|
| Átalakítások listázása|A válasz laponként 1000 átalakító megjelenítheti|
| Feladatok listázása|Megjelenítheti a válasz laponként 500 feladatok|
| Egy Media Services-fiók LiveEvents |5|
| Egy-egy előfizetéshez a Media Services-fiókok | 25 (rögzített) |
| StreamingPolicies | 1 000 000<sup>(3)</sup> |
| LiveOutputs / LiveEvent fut. |3|
| LiveOutputs / LiveEvent leállított állapotban |50|
| A fájlok JobInput másodpercenkénti száma|10|
| Tárfiókok | 100<sup>(4)</sup> (rögzített) |
| Streamvégpontok / Media Services-fiókkal fut.|2|
| Átalakítja az / Media Services-fiók | 100 |
| Az átalakító TransformOutputs| 20|
| Egy eszköz egyszerre társított egyedi StreamingLocators | 20<sup>(5)</sup> |

<sup>1</sup> egyetlen blob: jelenleg az Azure Blob Storage legfeljebb 5 TB támogatott maximális méretnél. Azonban további korlátok alapján a Virtuálisgép-méretek a szolgáltatás által használt Azure Media Services alkalmazni. Ha a forrásfájl 260-GB-nál nagyobb, a feladat valószínűleg sikertelen lesz. Ha 4 KB-os tartalom, amely nagyobb, mint 260 GB-os korlát, lépjen velünk kapcsolatba amshelp@microsoft.com a lehetséges megoldást a támogatásához.

<sup>2</sup> a telefonszám aszinkron, befejezett, aktív és megszakított feladatok tartalmaz. Nem tartalmazza a Törölt feladatok. 

Minden feladat rekord 90 napnál régebbi fiókja automatikusan törlődik, még akkor is, ha a rekordok száma nem éri el a kvóta felső határát. 

<sup>3</sup> egyéni használatakor [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), korlátozott számú ezeket a házirendeket tervezzen a Media Services-fiókhoz, és újra használhatja őket a StreamingLocators Ha ugyanazt a titkosítási beállítások, illetve protokollok van szükség. Nem érdemes új streamelési szabályzatot létrehozni minden egyes StreamingLocatorhöz.

<sup>4</sup> tárfiókok az azonos Azure-előfizetés között kell lennie.

<sup>5</sup> StreamingLocators nem készültek, felhasználókon hozzáférés-vezérlés kezelése. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat.

## <a name="support-ticket"></a>Támogatási jegy

A nem javított erőforrások, megkérheti a kvóták csak generál, nyissa meg a [támogatja a jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Részletes információkat tartalmazzák a kívánt kvóta változik, a használati eset forgatókönyveket, és a szükséges régiók a kérelemben. <br/>**Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.

## <a name="next-steps"></a>További lépések

[Áttekintés](media-services-overview.md)
