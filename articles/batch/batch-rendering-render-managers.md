---
title: Rendereléskezelő támogatása – Azure Batch
description: Az Azure Batch renderkezelő-integráció használata. További információ a népszerű renderelési kezelők beépített támogatásáról és bővítményeiről.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449686"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Az Azure Batch használata renderelési farmkezelőkkel

Ha egy meglévő helyszíni renderelő farmot használ, akkor nagyon valószínű, hogy egy rendereléskezelő szabályozza a renderelési farm kapacitását és a feladatok renderelését.

Az Azure beépített támogatást vagy bővítményeket biztosít a népszerű rendereléskezelők számára. Ezután hozzáadhatja és eltávolíthatja az Azure virtuális gépeket, beleértve a fizetős alkalmazáslicencelést és az alacsony prioritású virtuális gépeket.

A következő renderkezelők támogatottak:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Királyi render](https://www.royalrender.de/)
* [Thinkbox határidő](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure renderelő központ

Az Azure Render Hub leegyszerűsíti az Azure renderelési farmok létrehozását és kezelését.  A Render Hub natív támogatást nyújt a PipelineFx Qube és a Deadline 10 számára.  További információkért és részletes utasításokért tekintse meg [a GitHub-tárházat.](https://github.com/Azure/azure-render-hub)

## <a name="using-azure-with-pipelinefx-qube"></a>Az Azure használata a PipelineFX Qube-vel

Az Azure Render Hub támogatja a népszerű renderelési kezelőket, beleértve a Deadline-ot is.  A Render Hub telepítésével és használatával kapcsolatos tudnivalókért tekintse meg [a GitHub-tárházat.](https://github.com/Azure/azure-render-hub)

Parancsfájlok és utasítások, amelyek lehetővé teszik az Azure Batch-készlet virtuális gépei Qube-dolgozókként való használatára, szintén elérhetők [a GitHub-tárházban.](https://github.com/Azure/azure-qube)

## <a name="using-azure-with-royal-render"></a>Az Azure használata royal rendereléssel

A Royal Render beépített Azure- és Azure Batch-integrációval rendelkezik, amely lehetővé teszi a renderelési farm azure-alapú virtuális gépekhez való kiterjesztését. Összefoglalást a [súgófájlokban](https://www.royalrender.de/help8/index.html?Cloudrendering.html)talál.

Az Azure-integrációt használó Royal Render-ügyfél példáját lásd a [Medúzaképek ügyféltörténetében.](https://customers.microsoft.com/story/jellyfishpictures)

## <a name="using-azure-with-thinkbox-deadline"></a>Az Azure használata a Thinkbox határidővel

Az Azure Render Hub támogatja a népszerű renderelési kezelőket, beleértve a Deadline-ot is.  A Render Hub telepítésével és használatával kapcsolatos tudnivalókért tekintse meg [a GitHub-tárházat.](https://github.com/Azure/azure-render-hub)

## <a name="next-steps"></a>További lépések

Próbálja ki az Azure Batch-integrációt a rendereléskezelőhöz a megfelelő beépülő modul és adott esetben a GitHubon található utasítások használatával.
