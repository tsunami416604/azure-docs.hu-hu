---
title: Standard szintű szerződés | Azure
description: Standard szintű szerződés az Azure Marketplace-en és a AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819497"
---
# <a name="standard-contract"></a>Standard szerződés

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft egy standard szintű szerződést biztosít, amely megkönnyíti a tranzakciót a piactéren. Az egyéni használati feltételek és kikötések elvégzése helyett az Azure Marketplace-közzétevők dönthetnek úgy, hogy a standard szintű szerződés keretében kínálják a szoftvereket, amelyeket csak egyszer kell bemutatni és elfogadni. A standard szintű szerződés itt található: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178). 

Az ajánlatok használati feltételeinek meghatározása a piactér lapon történik, amikor ajánlatot hoz létre a Cloud Partner Portal. A standard szintű szerződés beállítás engedélyezéséhez módosítsa a beállítást Igen értékre.

![A standard szintű szerződés engedélyezése lehetőség](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Ha úgy dönt, hogy a normál szerződést használja, külön feltételek és kikötések szükségesek a [Cloud Solution Provider](./cloud-solution-providers.md) -csatornához.

## <a name="standard-contract-amendments"></a>Standard szintű szerződés módosításai

A standard szintű szerződés módosításai lehetővé teszik a kiadók számára, hogy az egyszerűség kedvéért standard szerződést válasszanak, a termékük vagy a vállalatuk testreszabott feltételeivel.  Az ügyfeleknek csak akkor kell áttekinteniük a szerződés módosításait, ha már áttekintették és elfogadták a Microsoft standard szerződést.

Az Azure Marketplace-közzétevők kétféle módosítást biztosítanak:

* Univerzális módosítások: ezeket a módosításokat a rendszer univerzálisan alkalmazza az összes ügyfélre vonatkozó standard szerződésre. Az univerzális módosítások a termék minden ügyfelénél megjelennek a vásárlás folyamatában.

![Univerzális módosítások](media/marketplace-publishers-guide/universal-amendaments.png)

* Egyéni módosítások: az Azure Marketplace-en a bérlőknek szánt egyéni módosítások is rendelkezésére állnak. A standard szerződés speciális módosításai, amelyek csak bizonyos ügyfelekre irányulnak. A kiadók kiválaszthatják, hogy melyik bérlőt szeretnék megcélozni. A bérlő ügyfelei a standard szintű szerződés és a megcélzó módosítások keretében vásárolják meg a terméket.

![Egyéni módosítások](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Az egyéni módosításokkal rendelkező ügyfelek a vásárlás során a standard feltételek egyetemes módosítását is megkapják.

>[!Note]
>A következő ajánlati típusok támogatják a szabványos szerződések módosításait: Azure-alkalmazások (megoldás-sablonok és felügyelt alkalmazások), Virtual Machines, tárolók, tároló-alkalmazások.

### <a name="customer-experience"></a>Felhasználói élmény

A Azure Portal vásárlási folyamata során az ügyfelek megtekinthetik a termékhez tartozó feltételeket a Microsoft szabványos szerződésének és a módosításoknak megfelelően.

![A Azure Portal felhasználói élmény.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

Az ügyfelek a `Get-AzureRmMarketplaceTerms` használatával kérhetik le az ajánlatok feltételeit, és elfogadják azt. A standard szerződést és a kapcsolódó módosításokat a rendszer a parancsmag kimenetében adja vissza.

---
