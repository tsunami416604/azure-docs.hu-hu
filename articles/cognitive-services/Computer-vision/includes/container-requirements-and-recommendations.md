---
title: A tárolóra vonatkozó követelmények és javaslatok.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034475"
---
A következő táblázat az egyes szövegfelismerés tárolók számára lefoglalható minimális és ajánlott CPU-magokat és memóriát ismerteti.

| Tároló | Minimális | Ajánlott |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|szövegfelismerés|1 mag, 8 GB memória, 0,5 TPS|2 mag, 8 GB memória, 1 TPS|0,5, 1|

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.
* TPS – tranzakció/másodperc

Az alap és a memória a `--cpus` `docker run` parancs `--memory` részeként használt és beállításoknak felel meg.