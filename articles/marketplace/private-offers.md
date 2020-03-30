---
title: Privát ajánlatok | Azure Piactér
description: Privát ajánlatok az Azure Piactéren az alkalmazás- és szolgáltatásközzétevők számára.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/1/2018
ms.author: dsindona
ms.openlocfilehash: 67aba077304117ad357d4e004ce7bdb25ac58352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285044"
---
# <a name="private-offers"></a>Privát ajánlatok

A [Microsoft Azure Piactéren](https://azuremarketplace.microsoft.com/) a privát ajánlatok lehetővé teszik a kiadók számára, hogy olyan termékverziókat hozzanak létre, amelyek csak a megcélzott ügyfelek számára láthatók.

## <a name="unlock-enterprise-deals-with-private-offers"></a>A vállalati ajánlatok felszabadítása privát ajánlatokkal

A nagyvállalati ügyfelek egyre gyakrabban használják az online piactereket felhőalapú megoldások keresésére, kipróbálására és megvásárlására. A magánajánlatok segítségével a kiadók a piactér segítségével privát módon oszthatnak meg testreszabott megoldásokat a vállalatok által igényelt képességekkel rendelkező célzott ügyfelekkel:

- *Az egyeztetett árképzés* lehetővé teszi a megjelenítők számára, hogy a nyilvánosan elérhető ajánlatokból kibővítsék a kedvezményeket és a nem listázott díjakat.
- *A privát feltételek* lehetővé teszik a kiadók számára, hogy a feltételeket egy adott ügyfélhez igazítsák.
- *A speciális konfigurációk* lehetővé teszik a kiadók számára, hogy a virtuális gépeiket, az Azure-alkalmazásokat és a SaaS-alkalmazásokat az egyes ügyfelek igényeihez igazítsák. Ez a beállítás azt is lehetővé teszi a közzétevők számára, hogy előzetes hozzáférést biztosítsanak az új termékfunkciókhoz, mielőtt szélesebb körben elindulnának az összes ügyfél számára.

A privát ajánlatok lehetővé teszik a közzétevők számára, hogy kihasználják a nyilvános piactér nagyságrendjét és globális elérhetőségét, az egyéni ajánlatok és konfigurációk egyeztetéséhez és biztosításához szükséges rugalmassággal és ellenőrzéssel. Ezek a funkciók együttesen megnyitják az ajtót a felhőalapú piacterek erőteljes vállalati bevezetése előtt.  A vállalkozások most már az elvárható és a keresletet megvásárolhatják és értékesíthetik.

Privát ajánlatok már elérhető a Virtuális gép, az Azure-alkalmazás (megoldássablonként vagy felügyelt alkalmazásokként megvalósítva) és a SaaS-alkalmazások ajánlataihoz. A nyilvános ajánlatokhoz hasonlóan privát ajánlatok is létrehozhatók és kezelhetők a [Cloud Partner Portal portálon](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)keresztül.  Az ügyfelek percek alatt megkaphatják vagy visszavonhatják a privát ajánlatokhoz való hozzáférést.

## <a name="creating-private-offers-using-skus-and-plans"></a>Privát ajánlatok létrehozása sk-ek és csomagok használatával

A *nyilvános és a nyilvános változatokkal vagy tervekkel rendelkező új vagy meglévő ajánlatok*esetében a megjelenítők egyszerűen létrehozhatnak új, privát változatokat új sk-ek vagy tervek létrehozásával és magánjellegűként való megjelölésükkel.  [A privát sk-ek](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) és csomagok az ajánlat összetevői, és csak a megcélzott ügyfelek számára láthatók és megvásárolhatók. A privát termékváltozatok és a csomagok újra felhasználhatják az alaplemezképeket és/vagy felajánlhatnak egy nyilvános termékváltozathoz vagy csomaghoz már közzétett metaadatokat. Ez a beállítás lehetővé teszi a közzétevők számára, hogy egy nyilvános ajánlat több privát változatát hozzák létre anélkül, hogy ugyanazon alaplemezkép több verzióját kellene közzétenni, és metaadatokat kellene kínálniuk. A virtuális gép és az Azure-alkalmazás csak akkor, ha egy privát termékváltozat megosztja az alaprendszerképet egy nyilvános termékváltozattal, az ajánlat alaplemezképének módosításai az adott alaprendszerkép használatával az összes nyilvános és privát termékváltozatban terjednek.

Olyan *új ajánlatok esetén, amelyek csak privát sk-eket vagy csomagokat tartalmaznak,* a megjelenítők bármely más ajánlatként létrehozhatják ajánlataikat, majd megjelölhetik az SIT-eket vagy a csomagokat privátként. Azok az ajánlatok, amelyek csak saját sk-ekkel vagy csomagokkal rendelkeznek, nem lesznek felderíthetők vagy elérhetők az [Azure Marketplace-en](https://azuremarketplace.microsoft.com) vagy az [Azure Portalon](https://azure.microsoft.com/features/azure-portal/) keresztül az ajánlathoz nem társított ügyfelek számára.

## <a name="targeting-customers-with-private-offers"></a>Ügyfelek célzása privát ajánlatokkal
Mind az új, mind a meglévő privát ajánlatok esetében a közzétevők előfizetés-azonosítók használatával célozhatják meg az ügyfeleket. A virtuális gépet vagy Az Azure-alkalmazásajánlatot használó közzétevők korlátozhatják a privát termékváltozat rendelkezésre állását egy egyedi Azure-előfizetés-azonosítóra, vagy akár 20 000 Azure-előfizetési azonosítót feltölthetnek. SaaS-alkalmazás privát ajánlat használata közben a közzétevők társíthatnak egy Azure-előfizetés-azonosítót vagy egy bérlői azonosítót, hogy korlátozzák a privát csomag rendelkezésre állását a manuális vagy a CSV-feltöltési megközelítés használatával.

Az ajánlat minősítése és közzététele után az ügyfelek perceken belül frissíthetők vagy eltávolíthatók a termékváltozatból vagy a csomagból a Privát előfizetések szinkronizálása funkció használatával. Ez a funkció lehetővé teszi a közzétevők számára, hogy gyorsan és egyszerűen frissítsék azoknak az ügyfeleknek a listáját, amelyeknek a privát termékváltozat vagy csomag megjelenik az ajánlat újbóli hitelesítése vagy újbóli közzététele nélkül.

## <a name="deploying-private-offers"></a>Privát ajánlatok telepítése

A privát ajánlatok csak az [Azure Portalon](https://azure.microsoft.com/features/azure-portal/) keresztül fedezhetők fel, és nem jelennek meg az [Azure Marketplace-en](https://azuremarketplace.microsoft.com)keresztül. Miután bejelentkezett az Azure Portalra, az ügyfelek kiválaszthatják a Marketplace navigációs elemet a privát ajánlataik eléréséhez. A privát ajánlatok a keresési eredmények között is megjelennek, és parancssori és Azure Resource Manager-sablonokon keresztül telepíthetők, mint bármely más ajánlat.

![[Privát ajánlatok]](./media/marketplace-publishers-guide/private-offer.png)

A privát ajánlatok a keresési eredmények között is megjelennek. Csak nézd meg a "Privát" jelvényt.

>[!Note]
>A privát ajánlatok at nem támogatják a Felhőszolgáltató program (CSP) viszonteladóján keresztül létrehozott előfizetések.

## <a name="next-steps"></a>További lépések

A privát ajánlatok használatának megkezdéséhez kövesse a [Privát ska-k és csomagok útmutató lépéseit.](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)
