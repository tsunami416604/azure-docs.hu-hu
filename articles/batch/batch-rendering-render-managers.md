---
title: Azure Batch renderelési manager-támogatás
description: Az Azure használatához megjelenítése az Azure Batch renderelési manager integrációjának használatával
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 066aab598628701bf7a60b0f4f20d996348fa5ce
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406721"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Azure Batch renderelési farm vezetők alá tartozó használatával

Használata egy meglévő helyi farm, renderelni, akkor nagyon valószínű, hogy egy renderelési manager a renderelési farm kapacitás szabályozza, és a renderelési feladatok.

Az Azure beépített támogatást vagy a bővítményeket kínál népszerű renderelési kezelők. Ezután adja hozzá, és távolítsa el az Azure virtuális gépekhez, beleértve a használatalapú esetében használható az alkalmazáslicenceket rendelkező virtuális gépek és az alacsony prioritású virtuális gépek.

Az alábbi renderelési kezelők támogatottak:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal renderelési](http://www.royalrender.de/)
* [Thinkbox határidő](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Az Azure-PipelineFX Qube

Parancsfájlok és útmutatást ahhoz, hogy az Azure Batch-készlet Qube munkavállalók a használni kívánt virtuális gépeket [a GitHub-adattár](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Az Azure-Királyi renderelési

Royal renderelési rendelkezik beépített, az Azure és az Azure Batch integrációja lehetővé teszi, hogy terjessze ki az Azure-alapú virtuális gépeket a renderelési farm. Egy összefoglaló, lásd: [a súgófájlok](http://www.royalrender.de/help8/index.html?Cloudrendering.html).

Az Azure-beli integrációs Royal jelennek meg Microsoft-példa: a [Jellyfish képek vásárlói beszámolónk](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Az Azure-Thinkbox határidő

Parancsfájlok és útmutatást ahhoz, hogy az Azure Batch-készlet határidő alkiszolgálók a használni kívánt virtuális gépeket [a GitHub-adattár](https://github.com/Azure/azure-deadline).

## <a name="next-steps"></a>További lépések

Próbálja ki az Azure Batch-integráció segítségével a megfelelő, a renderelési kezelője beépülő modul és útmutatás a Githubon, ahol lehetséges.