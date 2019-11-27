---
title: Standard szintű szerződés | Azure
description: Standard szintű szerződés az Azure Marketplace és a AppSource számára
services: Azure, Marketplace, Compute, Storage, Networking
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/19/2019
ms.author: v-chjen
ms.openlocfilehash: dc8edf2b6e4a1204e7edd515da9323896049eb13
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228214"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standard szintű szerződés a Microsoft kereskedelmi piactérről

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést biztosít a Microsoft kereskedelmi piactérről, hogy segítse a tranzakciók megkönnyítése a piactéren. Az egyéni használati feltételek és kikötések elvégzése helyett a kereskedelmi piactér-közzétevők dönthetnek úgy, hogy a standard szintű szerződés keretében kínálják a szoftvereket, amelyeket csak egyszer kell bemutatni és elfogadni. A standard szintű szerződés itt található: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

Az ajánlat feltételei és kikötései az ajánlat a partner Centerben vagy a Cloud Partner Portal való létrehozásakor vannak meghatározva. Kiválaszthatja, hogy a szokásos szerződést használja a Microsoft kereskedelmi piactérre, és nem adja meg saját használati feltételeit.

>[!Note]
>Miután közzétette az ajánlatot a Microsoft kereskedelmi piactér standard szerződésével, nem használhatja a saját használati feltételeit. Ez egy "vagy" forgatókönyv. A megoldást a standard szerződés *vagy* a saját használati feltételei alapján ajánljuk fel. Ha módosítani szeretné a standard szintű szerződés feltételeit, ezt a standard szintű szerződés módosításain keresztül teheti meg.

## <a name="standard-contract-amendments"></a>Standard szintű szerződés módosításai

A standard szintű szerződés módosításai lehetővé teszik a kiadók számára, hogy az egyszerűség kedvéért standard szerződést válasszanak, a termékük vagy a vállalatuk testreszabott feltételeivel. Az ügyfeleknek csak akkor kell áttekinteniük a szerződés módosításait, ha már áttekintették és elfogadták a Microsoft standard szerződést.

A kereskedelmi piactér-közzétevők számára két fajta módosítás érhető el:

* Univerzális módosítások: ezeket a módosításokat a rendszer univerzálisan alkalmazza az összes ügyfélre vonatkozó standard szerződésre. Az univerzális módosítások az ajánlat minden ügyfelének a vásárlás folyamatában jelennek meg. Az ügyfeleknek el kell fogadniuk a standard szerződés és a módosítás feltételeit, mielőtt felhasználhatják az ajánlatot.

* Egyéni módosítások: ezek a módosítások a standard szerződés speciális módosításai, amelyek csak az Azure-bérlői azonosítók használatával vannak megcélozva az egyes ügyfelekre. A kiadók kiválaszthatják, hogy melyik bérlőt szeretnék megcélozni. Az ajánlat vásárlási folyamatában csak a bérlő ügyfelei jelennek meg az egyéni módosítási feltételekkel.  Az ügyfeleknek el kell fogadniuk a standard szerződés feltételeit és a módosítás (oka) t, mielőtt felhasználhatják az ajánlatot.

>[!Note]
>Ez a két típusú módosítás egymásra épül. Az egyéni módosításokkal rendelkező ügyfelek a vásárlás során általános módosítást is kapnak a standard szerződéshez.

A Microsoft kereskedelmi piactér standard szerződését a következő típusú ajánlatokra használhatja: Azure-alkalmazások (megoldás-sablonok és felügyelt alkalmazások), Virtual Machines, tárolók, tároló alkalmazások, IoT Edge modulok és SaaS .

## <a name="customer-experience"></a>Felhasználói élmény

Az Azure Marketplace-en vagy AppSource észlelt felderítési élményben az ügyfelek az ajánlathoz tartozó feltételeket a Microsoft kereskedelmi piactér standard szerződésének és bármely egyetemes módosításnak megfelelően láthatják.

![A Azure Portal ügyfél-felderítési élmény.](media/marketplace-publishers-guide/azure-discovery-process.png)

A Azure Portal vásárlási folyamata során az ügyfelek megtekinthetik az ajánlathoz tartozó feltételeket a Microsoft kereskedelmi piactér standard szerződése, valamint az univerzális és/vagy bérlők által meghatározott módosítások esetében.

![A Azure Portal ügyfél-vásárlási élmény.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Az ügyfelek a Get-AzureRmMarketplaceTerms használatával lekérhetik az ajánlatok feltételeit, és elfogadják azt. A standard szerződést és a kapcsolódó módosításokat a rendszer a parancsmag kimenetében adja vissza.
