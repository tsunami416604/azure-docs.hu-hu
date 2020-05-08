---
title: Standard szintű szerződés a Microsoft kereskedelmi piactérről
description: Standard szintű szerződés az Azure Marketplace-hez és a AppSource a partner Centerben
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 3886b29a47edcfb36114cedf8b8edb3799e0c345
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871431"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Standard szintű szerződés a Microsoft kereskedelmi piactérről

A Microsoft szabványos szerződést kínál a Microsoft kereskedelmi Piactérről. Ezzel egyszerűbbé válik a beszerzési folyamat leegyszerűsítése az ügyfelek számára, a szoftvergyártók jogi összetettségének csökkentése és a tranzakciók megkönnyítése a piactéren. Az egyéni használati feltételek és kikötések kereskedelmi Piactéri közzétevőként való használata helyett dönthet úgy, hogy a [standard szintű szerződés](https://go.microsoft.com/fwlink/?linkid=2041178)keretében kínálja a szoftverét, amelyeknek az ügyfeleknek csak a szakképzést és a szükséges időt kell elfogadniuk.

Az ajánlat feltételei és kikötései az ajánlat a partner Centerben való létrehozásakor vannak meghatározva. Kiválaszthatja, hogy a szokásos szerződést használja a Microsoft kereskedelmi piactérre, és nem adja meg saját használati feltételeit.

>[!Note]
>Miután közzétette az ajánlatot a Microsoft kereskedelmi piactér standard szerződésével, nem használhatja a saját használati feltételeit. Ez egy "vagy" forgatókönyv. A megoldást a standard szerződés *vagy* a saját használati feltételei alapján ajánljuk fel. Ha módosítani szeretné a standard szintű szerződés feltételeit, ezt a standard szintű szerződés módosításain keresztül teheti meg.

## <a name="standard-contract-amendments"></a>Standard szintű szerződés módosításai

A standard szintű szerződés módosításai lehetővé teszik a kiadók számára, hogy az egyszerűség kedvéért standard szerződést válasszanak, a termékük vagy a vállalatuk testreszabott feltételeivel. Az ügyfeleknek csak akkor kell áttekinteniük a szerződés módosításait, ha már áttekintették és elfogadták a Microsoft standard szerződést.

A kereskedelmi piactér-közzétevők számára két fajta módosítás érhető el:

* Univerzális módosítások: ezeket a módosításokat a rendszer univerzálisan alkalmazza az összes ügyfélre vonatkozó standard szerződésre. Az univerzális módosítások az ajánlat minden ügyfelének a vásárlás folyamatában jelennek meg. Az ügyfeleknek el kell fogadniuk a standard szerződés és a módosítás feltételeit, mielőtt felhasználhatják az ajánlatot.

* Egyéni módosítások: ezek a módosítások a standard szerződés speciális módosításai, amelyek csak az Azure-bérlői azonosítók használatával vannak megcélozva az egyes ügyfelekre. A kiadók kiválaszthatják, hogy melyik bérlőt szeretnék megcélozni. Az ajánlat vásárlási folyamatában csak a bérlő ügyfelei jelennek meg az egyéni módosítási feltételekkel.  Az ügyfeleknek el kell fogadniuk a standard szerződés feltételeit és a módosítás (oka) t, mielőtt felhasználhatják az ajánlatot.

>[!Note]
>Ez a két típusú módosítás egymásra épül. Az egyéni módosításokkal rendelkező ügyfelek a vásárlás során általános módosítást is kapnak a standard szerződéshez.

A Microsoft kereskedelmi piactér standard szerződését a következő típusú ajánlatokra használhatja: Azure-alkalmazások (megoldás-sablonok és felügyelt alkalmazások), Virtual Machines, tárolók, tároló-alkalmazások, IoT Edge modulok és SaaS.

## <a name="customer-experience"></a>Felhasználói élmény

Az Azure Marketplace-en vagy AppSource észlelt felderítési élményben az ügyfelek az ajánlathoz tartozó feltételeket a Microsoft kereskedelmi piactér standard szerződésének és bármely egyetemes módosításnak megfelelően láthatják.

![A Azure Portal ügyfél-felderítési élmény.](media/marketplace-publishers-guide/azure-discovery-process.png)

A Azure Portal vásárlási folyamata során az ügyfelek megtekinthetik az ajánlathoz tartozó feltételeket a Microsoft kereskedelmi piactér standard szerződése, valamint az univerzális és/vagy bérlők által meghatározott módosítások esetében.

![A Azure Portal ügyfél-vásárlási élmény.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Az ügyfelek a Get-AzureRmMarketplaceTerms használatával lekérhetik az ajánlatok feltételeit, és elfogadják azt. A standard szerződést és a kapcsolódó módosításokat a rendszer a parancsmag kimenetében adja vissza.
