---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: d07a5da3b9013700694f6c20102ef2e8c5066087
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256548"
---
Legalább azt javasoljuk, hogy a kezdeti betanítási készletben címkével legalább 30 képet használjon. A modell kiképzése után néhány további képet is szeretne gyűjteni.

A modell hatékony betanításához használjon vizuálisan változó rendszerképeket. Válassza ki azokat a lemezképeket, amelyek az alábbiak szerint változnak:
* kamera szöge
* világítás
* háttér
* vizualizáció stílusa
* Egyéni/csoportosított tárgy (ok)
* size
* típus

Továbbá győződjön meg arról, hogy az összes betanítási rendszerkép megfelel a következő feltételeknek:
* . jpg,. png,. bmp vagy. gif formátum
* nem nagyobb, mint a 6MB mérete (4MB nál)
* nem kevesebb, mint 256 képpont a legrövidebb szegélynél. az ennél rövidebb képeket a Custom Vision Service automatikusan felskálázásra kerül

> [!NOTE]
> A Microsoft garázs-projekt, amely lehetővé teszi, hogy beszerezze a betanítási célokra szánt lemezképek készleteit. A képek összegyűjtése után letöltheti őket, majd a szokásos módon importálhatja őket a Custom Vision-projektbe. További információért látogasson el az [adattárház oldalára](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) .