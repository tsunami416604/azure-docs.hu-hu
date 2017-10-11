---
title: "Kezelt alkalmazás Azure piactér felhasználása |} Microsoft Docs"
description: "Describeshow hozhat létre egy Azure felügyelt alkalmazást, amely elérhető a piactéren keresztül."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2017
---
# <a name="consume-azure-managed-applications-in-the-marketplace"></a>Azure felhasználásához felügyelt alkalmazások a piactéren

A bemutatott a [kezelt alkalmazás – áttekintés](managed-application-overview.md) cikk, van két olyan eset a teljes körű élményt nyújt. A közzétevő vagy a szállító, hozzon létre egy felügyelt alkalmazást, az ügyfelek által használható számára, akik egyik. A második pedig a végfelhasználók az ügyfél vagy a fogyasztó a kezelt alkalmazás. Ez a cikk ismerteti a második forgatókönyv szerint. Leírja, hogyan telepíthet egy kezelt alkalmazást, a Microsoft Azure piactérről.

## <a name="create-from-the-marketplace"></a>Hozzon létre a piactérről

A piactérről kezelt alkalmazás telepítése hasonlít bármilyen típusú erőforrások telepítése a piactéren. 

Válassza a portál **+ új** , és keresse meg a telepítendő megoldás típusával. Az elérhető lehetőségek közül jelölje ki a van szüksége.

![keresési megoldások](./media/managed-application-consume-marketplace/search-apps.png)

Tekintse át az összefoglalást, az alkalmazás, és válassza ki **létrehozása**.

![kezelt alkalmazás létrehozása](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

Más megoldás, például lehetősége lesz arra, hogy az értékek mezőkkel. Ezek a mezők változó által kezelt alkalmazás hoz létre. 

## <a name="view-support-information"></a>Támogatási információk megtekintése

Miután a kezelt alkalmazás telepítve van, az alkalmazás támogatási információk megtekintése. A kezelt alkalmazás paneljén jelenik meg a támogatási információkat.

![Támogatás](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>A publisher engedélyek megtekintése

A gyártó, amely felügyeli az alkalmazás az erőforrásokhoz való hozzáférést. Ezeket az engedélyeket, jelölje ki a **engedélyek**.

![Engedélyek](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>Következő lépések

* További információ a kezelt alkalmazás közzététele a piactéren: [által felügyelt alkalmazások Azure piactér](managed-application-author-marketplace.md).
* Felügyelt alkalmazások, amelyek csak a szervezet felhasználói számára elérhető közzétételét, lásd: [létrehozása és a szolgáltatási katalógus kezelt alkalmazás közzététele](managed-application-publishing.md).
* Felügyelt alkalmazások, amelyek csak a szervezet felhasználói számára elérhető felhasználását, lásd: [szolgáltatás katalógus kezelt alkalmazás felhasználása](managed-application-consumption.md).