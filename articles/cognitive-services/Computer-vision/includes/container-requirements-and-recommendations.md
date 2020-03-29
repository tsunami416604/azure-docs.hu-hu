---
title: A tárolóra vonatkozó követelmények és ajánlások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481774"
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
