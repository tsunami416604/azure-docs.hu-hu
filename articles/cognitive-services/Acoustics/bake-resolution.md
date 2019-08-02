---
title: Project akusztikai sütni-feloldás
titlesuffix: Azure Cognitive Services
description: Ez a fogalmi áttekintés a durva és a kiváló megoldások közötti különbséget ismerteti az akusztikai sütések során.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: kylsto
ROBOTS: NOINDEX
ms.openlocfilehash: e237a010bfecc038816c1eedf94e2f94b1e12472
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704872"
---
# <a name="project-acoustics-bake-resolution"></a>Project akusztikai sütni-feloldás
Ez a fogalmi áttekintés a durva és a kiváló megoldások közötti különbséget ismerteti az akusztikai sütések során. Ezt a beállítást a sütési munkafolyamat mintavételek lépése alatt választhatja ki.

## <a name="Coarse-vs-Fine-Resolution"></a>Durva és részletes megoldás

Az egyetlen különbség a durva és a részletes feloldási beállítások között a szimuláció végrehajtásának gyakorisága. A finomabb a gyakoriságot kétszer is felhasználja, mint durva. Ennek számos hatása van az akusztikus szimulációra:

* A durva hullámhossza kétszer annyit, amíg a voxels, és így a két nagy méretű.
* A szimulációs idő közvetlenül kapcsolódik a Voxel-mérethez, így a durva sütni körülbelül 16-szor gyorsabb, mint a kiváló sütni.
* A Voxel kisebb méretű portálok (például ajtók vagy ablakok) nem szimulálható. A durva beállítás hatására előfordulhat, hogy a kisebb portálok némelyike nem szimulálható; ezért nem adják át a hangokat futásidőben. Láthatja, hogy ez a voxels megtekintésével történik.
* Az alsó szimuláció gyakorisága kevesebb diffrakciót eredményez a sarkok és élek körül.
* A hangforrások nem helyezhetők el "kitöltött" voxels belül (azaz a geometriát tartalmazó voxels). Ennek eredménye nem megfelelő. Nehezebb a hangforrásokat elhelyezni, hogy ne a nagy voxels belül legyenek, mint a megfelelő beállítás használatakor.
* A nagyobb voxels az alább látható módon több portálon is behatolnak. Az első kép durva használatával lett létrehozva, míg a második a kiváló megoldással megegyező ajtó. Ahogy azt a piros megjelölések is jelzik, a megfelelő beállítással sokkal kevésbé hatol be az ajtóba. A kék vonal a geometriában definiált ajtó, míg a piros vonal a Voxel-méret által definiált hatékony akusztikai portál. A behatolás egy adott helyzetben való kialakulásának módja attól függ, hogy a voxels hogyan épül fel a portál geometriájában, amelyet az objektumok mérete és helye határozza meg a jelenetben.

![Képernyőkép a durva voxels kitöltéséről az Unreal-ben](media/unreal-coarse-bake.png)

![Képernyőkép a részletes voxels az Unreal-beli ajtóban](media/unreal-fine-bake.png)

## <a name="next-steps"></a>További lépések

Az [Unreal](unreal-baking.md) vagy [Unity](unity-baking.md) beépülő modullal próbálja ki a durva és a kiváló megoldási beállításokat.
