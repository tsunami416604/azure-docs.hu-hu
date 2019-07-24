---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ee2c1fd1fc1cad07b14a2c99318be20be30db9c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423576"
---
Legalább azt javasoljuk, hogy a kezdeti betanítási készletben címkével legalább 30 képet használjon. A modell kiképzése után néhány további képet is szeretne gyűjteni.

A modell hatékony betanításához használjon vizuálisan változó rendszerképeket. Válassza ki azokat a lemezképeket, amelyek az alábbiak szerint változnak:
* kamera szöge
* világítás
* háttér
* vizualizáció stílusa
* Egyéni/csoportosított tárgy (ok)
* size
* type

Továbbá győződjön meg arról, hogy az összes betanítási rendszerkép megfelel a következő feltételeknek:
* . jpg,. png vagy. BMP formátum
* nem nagyobb, mint a 6MB mérete (4MB nál)
* nem kevesebb, mint 256 képpont a legrövidebb szegélynél. az ennél rövidebb képeket a Custom Vision Service automatikusan felskálázásra kerül