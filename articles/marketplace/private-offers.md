---
title: Saját ajánlatok |} Az Azure
description: Saját ajánlatok az Azure piactéren elérhető alkalmazás és szolgáltatás-közzétevők esetében.
services: Azure, Marketplace, Compute
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/1/2018
ms.author: qianw211
ms.openlocfilehash: c3294aaa2b6c88e08028c8958d2ab3a7297aea74
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351813"
---
# <a name="private-offers"></a>Saját ajánlatok

Saját ajánlatok az [a Microsoft Azure Marketplace-en](https://azuremarketplace.microsoft.com/) engedélyezi a kiadók számára, amelyek csak megcélzott felhasználók számára látható SKU-k létrehozása.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Vállalati üzletek privát ajánlatokkal zárolásának feloldása

Nagyvállalati ügyfeleink egyre használatával online piacterein keresése, próbálhatnak ki és vásárolhatnak a felhőalapú megoldások. Most már az szóló ajánlatok közzétevők használatával marketplace testre szabott megoldásokat közvetlenül a Microsoftnak megoszthatja lehetőségeket a vállalatok igénylő célzott ügyfeleket:

- *Egyeztetett díjszabás* lehetővé teszi a közzétevők kiterjesztheti a kedvezményeket és a ki-listát a nyilvánosan elérhető ajánlatok díjszabása.
- *Privát feltételek és kikötések* közzétevők megfelelő használati feltételeit, egy adott ügyfél engedélyezése.
- *Speciális konfigurációk* lehetővé teszik a kiadók testre szabni az egyéni ügyféligények megfelelő virtuális gépek Azure-alkalmazások és SaaS-alkalmazások ajánlatukat. Ez a beállítás is lehetővé teszi a kiadók számára, hogy minden ügyfél számára szélesebb körben elindítása előtt új termékfunkciók, előzetes hozzáférést biztosítanak.

Saját ajánlatok lehetővé teszik a közzétevők a rugalmasságot és irányítást egyeztetésére és egyéni üzletek és konfigurációk előnyeit, a méretezési csoport és globális rendelkezésre állása egy nyilvános piactérről érvénybe. Ezek a funkciók együtt, nyissa meg a felhőalapú piacterein erős vállalati elfogadását ajtaja.  A vállalatok mostantól vásárolni, és értékesíteni szeretné termékét az általuk elvárt módon, és igény szerint.

Saját ajánlatok számára érhető el virtuális gép Azure-alkalmazás (megvalósítva, megoldássablonok vagy felügyelt alkalmazások), és SaaS-alkalmazásokat kínál. Nyilvános ajánlatokat, mint saját ajánlatok is létrehozhatóak és felügyelhetőek keresztül a [Cloud Partner Portalon](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  Az ügyfelek is megadható, vagy saját ajánlatok percek alatt hozzáférést visszavonták.

## <a name="creating-private-offers-using-skus-and-plans"></a>SKU-k és tervek létrehozása saját kínál

A *nyilvános SKU-k vagy a csomag új vagy meglévő ajánlatok*, közzétevők könnyedén hozhat létre új, privát költségszámítóban új termékváltozatok és tervek létrehozása, és privát megjelölése őket.  [Privát termékváltozatok](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) és a terveket az ajánlat összetevő, és csak látható és a megcélzott felhasználók által megvásárolható. Privát SKU-k és a tervek is újból felhasználhatja az alaprendszerképek lekéréshez és/vagy egy nyilvános Termékváltozat vagy csomag már közzétett metaadatok kínálnak. Ez a beállítás lehetővé teszi, hogy a kiadók számára hozzon létre egy nyilvános ajánlat több privát változata azonos alaplemezkép több verziójának közzététele és metaadatok nélkül. Virtuális gép és az Azure application, amelyek csak amikor egy privát Termékváltozat alapképet osztanak meg a nyilvános Termékváltozat módosítania kellene az ajánlat alaplemezkép továbbterjeszti alapszintű rendszerkép használata a nyilvános és privát termékváltozatok között.

A *új ajánlat csak a privát SKU-k vagy terveket tartalmazó*, közzétevők hozzon létre semmilyen egyéb ajánlattal ajánlatait, és jelöli az SKU-k vagy a csomagok saját. Az ajánlatok, amelyek csak privát SKU-k vagy tervek felderíthető vagy -en keresztül elérhető nem lesz [Azure Marketplace-en](https://azuremarketplace.microsoft.com) vagy a [az Azure portal](https://azure.microsoft.com/features/azure-portal/) ügyfelek, amelyek nem tartoznak az ajánlat keretében.

## <a name="targeting-customers-with-private-offers"></a>Saját ajánlatok célcsoport-kezelési ügyfeleit
Új és meglévő titkos ajánlatok esetében a kiadók előfizetés azonosítókat használó ügyfelek célba. Egy virtuális gép vagy Azure-alkalmazás ajánlat használatával a kiadók korlátozhatja az egyes Azure-előfizetés-azonosítója egy privát SKU rendelkezésre állását, vagy töltse fel a fürt megosztott kötetei szolgáltatás legfeljebb 20 000 Azure-előfizetés azonosítókat is. Egy SaaS-alkalmazás titkos ajánlat használatakor a kiadók társíthatja egy Azure-előfizetési Azonosítóját vagy a a bérlő Azonosítóját korlátozhatja az egy privát tervet, rendelkezésre állását a manuális vagy a fürt megosztott kötetei szolgáltatás a feltöltési módszer használatával.

Ajánlat hitelesített, és a közzétett, az ügyfelek frissítve vagy eltávolítva a szinkronizálási saját előfizetések szolgáltatással a Termékváltozat vagy a csomag percen belül. Ez a funkció lehetővé teszi, hogy a kiadók számára, hogy gyorsan és könnyedén ügyfeleink, amelyhez a privát Termékváltozat vagy csomag egyike a recertifying, és közzéteszi az ajánlatot itt nélkül listájának frissítése.

## <a name="deploying-private-offers"></a>Privát telepítése kínál

Saját ajánlatok csak akkor felfedezhetők a [az Azure portal](https://azure.microsoft.com/features/azure-portal/) és nem jelennek meg keresztül [Azure Marketplace-en](https://azuremarketplace.microsoft.com). Miután bejelentkezett az Azure Portalon, a felhasználók is választhatnak a Marketplace-en navigációs elem privát ajánlatait eléréséhez. Saját ajánlatok is megjelennek a keresési eredmények között, és a parancssor és az Azure Resource Manager-sablonokat, mint más ajánlatokkal keresztül telepíthető.

![[Szóló ajánlatok]](./media/marketplace-publishers-guide/private-offer.png)

Saját ajánlatok is megjelenik a keresési eredmények között. Keresse a "Privát" jelvény.

> [!Note]
> Saját ajánlatok a viszonteladó a Cloud Solution Provider (CSP) program keretében létrehozott előfizetések nem támogatottak.

## <a name="next-steps"></a>További lépések

Ha szeretné, hogy ezek az új képességek kihasználásához, megkezdheti az értékesítési a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/sell).
