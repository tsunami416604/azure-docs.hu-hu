---
title: Standard szintű szerződés |} Az Azure
description: Standard szintű szerződés az Azure Marketplace és appsource-ban
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620395"
---
# <a name="standard-contract"></a>Standard szerződés

Ügyfelei a Beszerzéselemzési megkönnyítik és szoftvergyártók jogi csökkenthető, a Microsoft kínál a szabványos szerződés sablon annak érdekében, hogy egy tranzakciónak a Marketplace-en megkönnyítése érdekében. Ahelyett, hogy elvégezte az egyéni használati feltételeket, Azure Marketplace-en a kiadók választhat a standard szintű szerződést, amelyet csak a vet, és fogadja el a egyszer kell ügyfelek alatt Szoftverüket. A standard szintű szerződés itt található: [ https://go.microsoft.com/fwlink/?linkid=2041178 ](https://go.microsoft.com/fwlink/?linkid=2041178). 

A használati feltételeket az ajánlatot a piactéren lapon megadott a Cloud Partner portálra az ajánlat létrehozásakor. A standard szintű szerződés engedélyezve van a beállítás az Igen módosításával.

![Általános szerződési beállítás engedélyezése](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>A standard szintű szerződés használatát választja, külön feltételek és kikötések-e továbbra is szükséges a [Cloud Solution Provider](./cloud-solution-providers.md) csatorna.

## <a name="standard-contract-amendments"></a>Általános szerződési módosításai

Szabványos szerződéses módosítások lehetővé teszik a kiadók számára, válassza ki a standard szintű szerződést az egyszerűség és termék-vagy business testre szabott adatokkal.  Ügyfelek tekintse át a szerződés, a módosításokat, ha rendelkezik már ellenőrizte és elfogadta a Microsoft szabványos szerződéses csak kell.

Nincsenek elérhető az Azure piactér kiadói kétféle módosításokat:

* Univerzális módosításokat: Ezek a módosítások minden ügyfelünk esetében a standard szintű szerződés általánosan érvényesek. Univerzális módosítások jelennek meg a termék a vásárlási folyamat minden ügyfél számára.

![Univerzális módosításai](media/marketplace-publishers-guide/universal-amendaments.png)

* Egyéni módosításokat: Az Azure Marketplace-en is rendelkezik egy egyéni módosítására irányuló bérlők kiépítése. A standard szintű szerződést, amely csak bizonyos ügyfeleket célozza speciális módosításai. A kiadók a bérlői szeretnének, amelyekre választhat. A bérlőt az ügyfelek lenne beszerzési a termék általános szerződési és a célként megadott módosításait.

![Egyéni módosításait](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Egyéni módosításait a megcélzott felhasználók is fog kapni a vásárlás során, a standard szintű használati univerzális módosítás.

>[!Note]
>A következő típusú támogatja a standard szintű szerződés módosításokat: Az Azure-alkalmazások (Megoldássablonok és a felügyelt alkalmazások), virtuális gépek, tárolók, Tárolóalkalmazások.

### <a name="customer-experience"></a>Felhasználói élmény

A vásárlás során az Azure Portalon ügyfelek tudják a feltételek mellett a termék a Microsoft szabványos szerződéses és a módosítások megtekintéséhez.

![Az Azure portal felhasználói élmény.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

Vállalt `Get-AzureRmMarketplaceTerms` beolvasásához ajánlat feltételeit, és fogadja el. A standard szintű szerződés és a kapcsolódó módosítások a parancsmag kimenetében visszaad.

---
