---
title: Tároló-követelményeket és javaslatokat
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129819"
---
> [!NOTE]
> A követelmények és javaslatok a másodpercenként egy egyszeri kéréssel rendelkező teljesítményteszteken alapulnak, és a beolvasott üzleti levél 8 MB-os képét, amely 29 sort és összesen 803 karaktert tartalmaz.

#### <a name="readtabread"></a>[Olvasás](#tab/read)

Az alábbi táblázat az egyes olvasási tárolók minimális és ajánlott erőforrás-elosztását ismerteti.

| Tároló | Minimális | Ajánlott |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Olvasás | 1 mag, 8 GB memória, 0,24 TPS | 8 mag, 16 GB memória, 1,17 TPS | 0,24, 1,17 |

#### <a name="recognize-texttabrecognize-text"></a>[Szövegének felismerése](#tab/recognize-text)

Az alábbi táblázat az egyes szövegfelismerés-tárolók minimális és ajánlott erőforrás-elosztását ismerteti.

| Tároló | Minimális | Ajánlott |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| szövegfelismerés | 1 mag, 8 GB memória, 0,12 TPS | 8 mag, 16 GB memória, 0,60 TPS | 0,12, 0,60 |

***

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.
* TPS – tranzakció/másodperc.

Az alap és a memória a `--cpus` `docker run` parancs `--memory` részeként használt és beállításoknak felel meg.
