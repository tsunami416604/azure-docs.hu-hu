---
title: Általános szerződés | Azure Piactér
description: Általános szerződés az Azure Piactérhez és az AppSource-hoz
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681447"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>A Microsoft kereskedelmi piacterének általános szerződése

Az ügyfelek beszerzési folyamatának egyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft a Microsoft kereskedelmi piacterére vonatkozó általános szerződést kínál a tranzakciók megkönnyítése érdekében a piacon. Ahelyett, hogy egyedi feltételeket gyártanának, a kereskedelmi piactér kiadói dönthetnek úgy, hogy az Általános Szerződés alapján kínálják szoftvereiket, amelyet az ügyfeleknek csak egyszer kell ellenőrizniük és elfogadniuk. Az alapszerződés itt található: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

Az ajánlat feltételeit a Partnerközpontban az ajánlat létrehozásakor határozzák meg. Választhat, hogy a Microsoft kereskedelmi piactér általános szerződését használja ahelyett, hogy saját egyéni feltételeket biztosítana.

>[!Note]
>Miután közzétett egy ajánlatot a Microsoft kereskedelmi piactér általános szerződésével, nem használhatja a saját egyéni szerződéseit. Ez egy "vagy" forgatókönyv. Vagy a Standard Szerződés, *vagy* a saját szerződési feltételek alapján kínálja a megoldást. Ha módosítani szeretné az Általános Szerződés feltételeit, ezt a Szerződés általános módosításai révén teheti meg.

## <a name="standard-contract-amendments"></a>Általános szerződésmódosítások

Az általános szerződésmódosítások lehetővé teszik a kiadók számára, hogy az egyszerűség kedvéért és a termékükre vagy vállalkozásukra szabott feltételekkel válasszák ki az általános szerződést. Az ügyfeleknek csak akkor kell áttekinteniük a szerződés módosításait, ha már felülvizsgálták és elfogadták a Microsoft általános szerződését.

A kereskedelmi piactér kiadói számára kétféle módosítás érhető el:

* Univerzális módosítások: Ezek a módosítások általánosan alkalmazzák az általános szerződés minden ügyfél számára. Az univerzális módosításokat az ajánlat minden ügyfele számára bemutatják a beszerzési folyamatban. Az ügyfeleknek el kell fogadniuk az Általános Szerződés feltételeit és a módosítást, mielőtt felhasználhatnák az ajánlatot.

* Egyéni módosítások: Ezek a módosítások a standard szerződés speciális módosításai, amelyek csak az Azure-beli bérlői azonosítókon keresztül irányulnak bizonyos ügyfelekre. A közzétevők kiválaszthatják a megcélozni kívánt bérlőt. Csak a bérlői ügyfelek kapnak egyéni módosítási feltételeket az ajánlat beszerzési folyamatában.  Az ügyfeleknek el kell fogadniuk az Általános Szerződés feltételeit és a módosítás(oka)t, mielőtt felhasználhatják az ajánlatot.

>[!Note]
>Ez a két típusú módosítások verem egymásra. Az egyéni módosításokkal megcélzott ügyfelek a vásárlás során az Általános Szerződés általános módosítását is megkapják.

A Microsoft kereskedelmi piactér általános szerződését a következő ajánlattípusokhoz használhatja ki: Azure-alkalmazások (megoldássablonok és felügyelt alkalmazások), virtuális gépek, tárolók, tárolóalkalmazások, IoT edge-modulok és SaaS.

## <a name="customer-experience"></a>Felhasználói élmény

Az Azure piactéren vagy az AppSource-ban a felderítési élmény során az ügyfelek láthatják az ajánlathoz társított feltételeket a Microsoft kereskedelmi piactér általános szerződésként és az általános módosításokat.

![Az Azure Portal ügyfélfelderítési élmény.](media/marketplace-publishers-guide/azure-discovery-process.png)

Az Azure Portalon a vásárlási folyamat során az ügyfelek láthatják az ajánlathoz társított feltételeket, mint a Microsoft kereskedelmi piactér általános szerződését, valamint az általános és/vagy bérlőspecifikus módosításokat.

![Az Azure Portal ügyfélvásárlási élménye.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Az ügyfelek a Get-AzureRmMarketplaceTerms használatával lekérhetik az ajánlat feltételeit, és elfogadhatják azt. Az általános szerződés és a kapcsolódó módosítások a parancsmag kimenetében kerülnek visszaadásra.
