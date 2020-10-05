---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "70130088"
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
