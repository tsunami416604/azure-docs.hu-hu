---
title: A tárolóra vonatkozó követelmények és ajánlások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877974"
---
> [!NOTE]
> A követelmények és ajánlások másodpercenként egyetlen kéréssel rendelkező referenciaértékeken alapulnak, amelyek egy beolvasott üzleti levél 8 MB-os képét használják, amely 29 sort és összesen 803 karaktert tartalmaz.

Az alábbi táblázat az egyes olvasási tárolók erőforrásainak minimális és ajánlott elosztását ismerteti.

| Tároló | Minimális | Ajánlott |Tps<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
| Olvasás | 1 mag, 8 GB memória, 0,24 TPS | 8 mag, 16 GB memória, 1,17 TPS | 0.24, 1.17 |

* Minden magnak legalább 2,6 gigahertzesnek (GHz) vagy gyorsabbnak kell lennie.
* TPS - tranzakciók másodpercenként.

A mag és `--cpus` a `--memory` memória megfelel nek a `docker run` és a beállításoknak, amelyek a parancs részeként használatosak.
