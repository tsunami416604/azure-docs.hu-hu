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
ms.openlocfilehash: bffdb7b33fc7da38b6985edc3948b848f417a497
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982083"
---
> [!NOTE]
> A követelmények és javaslatok a másodpercenként egy egyszeri kéréssel rendelkező teljesítményteszteken alapulnak, és a beolvasott üzleti levél 8 MB-os képét, amely 29 sort és összesen 803 karaktert tartalmaz.

Az alábbi táblázat az egyes olvasási tárolók minimális és ajánlott erőforrás-elosztását ismerteti.

| Tároló | Minimum | Ajánlott |
|-----------|---------|-------------|
| Olvasás 3,0 – előzetes verzió | 8 mag, 16 GB memória | 8 mag, 24 GB memória |
| Olvasás 3,1 – előzetes verzió | 8 mag, 16 GB memória | 8 mag, 24 GB memória |

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.

Az alap és a memória a `--cpus` `--memory` parancs részeként használt és beállításoknak felel meg `docker run` .
