---
title: A tárolóra vonatkozó követelmények és javaslatok
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877974"
---
> [!NOTE]
> A követelmények és javaslatok a másodpercenként egy egyszeri kéréssel rendelkező teljesítményteszteken alapulnak, és a beolvasott üzleti levél 8 MB-os képét, amely 29 sort és összesen 803 karaktert tartalmaz.

Az alábbi táblázat az egyes olvasási tárolók minimális és ajánlott erőforrás-elosztását ismerteti.

| Tároló | Minimális | Ajánlott |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Olvasás | 1 mag, 8 GB memória, 0,24 TPS | 8 mag, 16 GB memória, 1,17 TPS | 0,24, 1,17 |

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.
* TPS – tranzakció/másodperc.

Az alap és a memória a `--cpus` `docker run` parancs `--memory` részeként használt és beállításoknak felel meg.
