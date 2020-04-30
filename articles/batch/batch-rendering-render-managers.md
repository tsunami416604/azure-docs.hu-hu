---
title: A render Manager támogatása
description: A Azure Batch Render Manager integrációjának használata. Ismerkedjen meg a népszerű Render managerek beépített támogatásával vagy bővítményeivel.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 9921c68bf6ebe44d6d2a6b7b74afbe3fa98e0803
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115737"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Azure Batch használata a render Farm Managerrel

Ha egy meglévő helyszíni Render farmot használ, akkor nagy valószínűséggel a render Manager a farmok renderelési kapacitását és a renderelési feladatokat vezérli.

Az Azure beépített támogatást vagy bővítményeket biztosít a népszerű Render managerekhez. Ezután hozzáadhat és eltávolíthat Azure-beli virtuális gépeket, beleértve a használaton kívüli alkalmazások licencelését és az alacsony prioritású virtuális gépeket is.

A következő Render managerek támogatottak:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox határideje](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render hub

Az Azure Render hub leegyszerűsíti az Azure Render-farmok létrehozását és felügyeletét.  A render hub natív módon támogatja a PipelineFx Qube és a 10. határidőt.  További információkért és részletes utasításokért lásd [a GitHub-tárházat](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Az Azure használata a PipelineFX Qube

Az Azure Render hub támogatja a népszerű Render managereket, beleértve a határidőt is.  A render hub üzembe helyezésével és használatával kapcsolatos utasításokért lásd [a GitHub-tárházat](https://github.com/Azure/azure-render-hub).

Azok a parancsfájlok és utasítások, amelyek lehetővé teszik, hogy Azure Batch készletben lévő virtuális gépek Qube feldolgozóként is használhatók legyenek [a GitHub-tárházban](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Az Azure és a Royal Render használata

A Royal Render rendelkezik az Azure-nal és Azure Batch integrációs szolgáltatással, amely lehetővé teszi a render farmok Azure-alapú virtuális gépekkel való bővítését. Az összegzést [a súgófájlok](https://www.royalrender.de/help8/index.html?Cloudrendering.html)részben tekintheti meg.

Az Azure-integrációt használó Royal Render-ügyfélre például a medúza- [képek vásárlói történetében](https://customers.microsoft.com/story/jellyfishpictures)olvashat.

## <a name="using-azure-with-thinkbox-deadline"></a>Az Azure használata a Thinkbox határidővel

Az Azure Render hub támogatja a népszerű Render managereket, beleértve a határidőt is.  A render hub üzembe helyezésével és használatával kapcsolatos utasításokért lásd [a GitHub-tárházat](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>További lépések

Próbálja ki a render Manager Azure Batch integrációját a megfelelő beépülő modullal és a GitHubon megjelenő utasításokkal, ahol lehetséges.
