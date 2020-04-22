---
title: Privát ajánlatok | Azure Piactér
description: Privát ajánlatok az Azure Piactéren az alkalmazás- és szolgáltatásközzétevők számára.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: efda9fb66a65f1f2cd40de8bf32a8a3fa99aea7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681434"
---
# <a name="private-offers"></a>Privát ajánlatok

A [Microsoft Azure Piactéren](https://azuremarketplace.microsoft.com/) a privát ajánlatok lehetővé teszik a kiadók számára, hogy olyan termékverziókat hozzanak létre, amelyek csak a megcélzott ügyfelek számára láthatók.

## <a name="unlock-enterprise-deals-with-private-offers"></a>A vállalati ajánlatok felszabadítása privát ajánlatokkal

A nagyvállalati ügyfelek egyre gyakrabban használják az online piactereket felhőalapú megoldások keresésére, kipróbálására és megvásárlására. A magánajánlatok segítségével a kiadók a piactér segítségével privát módon oszthatnak meg testreszabott megoldásokat a vállalatok által igényelt képességekkel rendelkező célzott ügyfelekkel:

- *Az egyeztetett árképzés* lehetővé teszi a megjelenítők számára, hogy a nyilvánosan elérhető ajánlatokból kibővítsék a kedvezményeket és a nem listázott díjakat.
- *A privát feltételek* lehetővé teszik a kiadók számára, hogy a feltételeket egy adott ügyfélhez igazítsák.
- *A speciális konfigurációk* lehetővé teszik a kiadók számára, hogy a virtuális gépeiket, az Azure-alkalmazásokat és a SaaS-alkalmazásokat az egyes ügyfelek igényeihez igazítsák. Ez a beállítás azt is lehetővé teszi a közzétevők számára, hogy előzetes hozzáférést biztosítsanak az új termékfunkciókhoz, mielőtt szélesebb körben elindulnának az összes ügyfél számára.

A privát ajánlatok lehetővé teszik a közzétevők számára, hogy kihasználják a nyilvános piactér nagyságrendjét és globális elérhetőségét, az egyéni ajánlatok és konfigurációk egyeztetéséhez és biztosításához szükséges rugalmassággal és ellenőrzéssel. Ezek a funkciók együttesen megnyitják az ajtót a felhőalapú piacterek erőteljes vállalati bevezetése előtt. A vállalkozások most már az elvárható és a keresletet megvásárolhatják és értékesíthetik.

Privát ajánlatok már elérhető a Virtuális gép, az Azure-alkalmazás (megoldássablonként vagy felügyelt alkalmazásokként megvalósítva) és a SaaS-alkalmazások ajánlataihoz. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>Privát ajánlatok létrehozása a csomagjainak és terveinek használatával

A *nyilvános és a nyilvános változatokkal vagy tervekkel rendelkező új vagy meglévő ajánlatok*esetében a megjelenítők egyszerűen létrehozhatnak új, privát változatokat új sk-ek vagy tervek létrehozásával és magánjellegűként való megjelölésükkel.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

A privát sk-ek és csomagok az ajánlat összetevői, és csak a megcélzott ügyfelek számára láthatók és megvásárolhatók. A privát termékváltozatok és a csomagok újra felhasználhatják az alaplemezképeket és/vagy felajánlhatnak egy nyilvános termékváltozathoz vagy csomaghoz már közzétett metaadatokat. Ez a beállítás lehetővé teszi a közzétevők számára, hogy egy nyilvános ajánlat több privát változatát hozzák létre anélkül, hogy ugyanazon alaplemezkép több verzióját kellene közzétenni, és metaadatokat kellene kínálniuk. A virtuális gép és az Azure-alkalmazás csak akkor, ha egy privát termékváltozat megosztja az alaprendszerképet egy nyilvános termékváltozattal, az ajánlat alaplemezképének módosításai az adott alaprendszerkép használatával az összes nyilvános és privát termékváltozatban terjednek.

Olyan *új ajánlatok esetén, amelyek csak privát sk-eket vagy csomagokat tartalmaznak,* a megjelenítők bármely más ajánlatként létrehozhatják ajánlataikat, majd megjelölhetik az SIT-eket vagy a csomagokat privátként. A csak saját termékkel vagy csomaggal rendelkező ajánlatok nem lesznek felderíthetők vagy elérhetők a Microsoft kereskedelmi piactéren vagy az [Azure Portalon](https://azure.microsoft.com/features/azure-portal/) keresztül az ajánlathoz nem társított ügyfelek számára.

## <a name="targeting-customers-with-private-offers"></a>Ügyfelek megcélzása privát ajánlatokkal

Mind az új, mind a meglévő privát ajánlatok esetében a közzétevők előfizetés-azonosítók használatával célozhatják meg az ügyfeleket. A virtuális gépet vagy Az Azure-alkalmazásajánlatot használó közzétevők korlátozhatják a privát termékváltozat rendelkezésre állását egy egyedi Azure-előfizetés-azonosítóra, vagy akár 20 000 Azure-előfizetési azonosítót feltölthetnek. A SaaS-alkalmazás privát ajánlatának használata közben a közzétevők társítani a bérlői azonosítót a privát csomag rendelkezésre állásának korlátozásához a manuális vagy a CSV-feltöltési megközelítés használatával társíthatják.

Az ajánlat minősítése és közzététele után az ügyfelek perceken belül frissíthetők vagy eltávolíthatók a termékváltozatból vagy a csomagból a Privát előfizetések szinkronizálása funkció használatával. Ez a funkció lehetővé teszi a közzétevők számára, hogy gyorsan és egyszerűen frissítsék azoknak az ügyfeleknek a listáját, amelyeknek a privát termékváltozat vagy csomag megjelenik az ajánlat hitelesítése vagy közzététele nélkül.

## <a name="deploying-private-offers"></a>Privát ajánlatok telepítése

A privát ajánlatok csak az [Azure Portalon](https://azure.microsoft.com/features/azure-portal/) keresztül fedezhetők fel, és nem jelennek meg a [Microsoft AppSource](https://appsource.microsoft.com/) vagy az [Azure Marketplace-en.](https://azuremarketplace.microsoft.com) Ha többet szeretne tudni a különböző kereskedelmi piactéri kirakatokban való közzétételről, [olvassa el a Közzétételi lehetőség meghatározása](./determine-your-listing-type.md)című témakört.

Miután bejelentkezett az Azure Portalra, az ügyfelek kiválaszthatják a Marketplace navigációs elemet a privát ajánlataik eléréséhez. A privát ajánlatok a keresési eredmények között is megjelennek, és parancssori és Azure Resource Manager-sablonokon keresztül telepíthetők, mint bármely más ajánlat.

![[Privát ajánlatok]](./media/marketplace-publishers-guide/private-offer.png)

A privát ajánlatok a keresési eredmények között is megjelennek. Csak keresd meg a "Privát" jelvényt.

>[!Note]
>A privát ajánlatok at nem támogatják a Felhőszolgáltató program (CSP) viszonteladóján keresztül létrehozott előfizetések.

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->