---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70130088"
---
Legalább azt javasoljuk, hogy legalább 30 képet használjon címkénként az alapképzési készletben. Azt is szeretné, hogy gyűjtsön néhány extra képeket, hogy tesztelje a modell, ha már bevan tanítva.

Annak érdekében, hogy a modell betanítása hatékonyan, használja a képeket vizuális változatosság. Válassza ki a következő től függő képeket:
* kamera szöge
* Világítás
* háttér
* vizuális stílus
* egyéni/csoportosított téma(ok)
* size
* type

Ezenkívül győződjön meg arról, hogy az összes betanítási kép megfelel az alábbi feltételeknek:
* .jpg, .png, .bmp vagy .gif formátumban
* nem nagyobb, mint 6MB méretű (4MB előrejelzési képek)
* a legrövidebb élen nem kevesebb, mint 256 pixel; az ennél rövidebb képeket a Custom Vision Service automatikusan felskálázja
