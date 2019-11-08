---
title: Privát ajánlatok | Azure piactér
description: Privát ajánlatok az Azure Marketplace-en az alkalmazások és szolgáltatások közzétevői számára.
services: Azure, Marketplace, Compute
author: qianw211
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/1/2018
ms.author: pabutler
ms.openlocfilehash: 16903d4f9b1bad8a588b1a234ac84bc9c0150c4a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827840"
---
# <a name="private-offers"></a>Privát ajánlatok

A [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) privát ajánlatai lehetővé teszik a kiadók számára, hogy csak a megrendelt ügyfelek számára látható SKU-ket hozzanak létre.

## <a name="unlock-enterprise-deals-with-private-offers"></a>A vállalati ajánlatok feloldása privát ajánlatokkal

A nagyvállalati ügyfelek egyre gyakrabban használják online piactéreket a felhőalapú megoldások keresésére, kipróbálására és megvásárlására. A privát ajánlatokkal a kiadók a piactéren testre szabott megoldásokat használhatnak a megcélzott ügyfelekkel a vállalatok által igényelt képességekkel:

- Az *egyeztetett díjszabás* lehetővé teszi, hogy a kiadók a nyilvánosan elérhető ajánlatokból származó kedvezményeket és off-listázási díjszabást terjesszenek.
- A *privát feltételek és kikötések* lehetővé teszik a kiadók számára a használati feltételek egy adott ügyfélhez való személyre szabását.
- A *speciális konfigurációk* lehetővé teszik a kiadók számára a Virtual Machines, az Azure-alkalmazások és az SaaS-alkalmazások személyre szabását az ügyfelek igényei szerint. Ez a beállítás azt is lehetővé teszi, hogy a kiadók az új termékek funkcióinak előzetes hozzáférését is megadják, mielőtt szélesebb körben kellene elindítani az összes ügyfelet

A privát ajánlatok lehetővé teszik a kiadók számára, hogy kihasználják a nyilvános piactér skálázhatóságát és globális rendelkezésre állását, valamint az egyéni ajánlatok és konfigurációk egyeztetéséhez és megvalósításához szükséges rugalmasságot és irányítást. Ezek a funkciók együttesen nyitják meg az ajtót a Felhőbeli piactérek erős nagyvállalati bevezetéséhez.  A vállalatok mostantól megvásárolhatják és értékesíthetik a várható és a keresletet.

Mostantól elérhetők a virtuális gépek, az Azure-alkalmazások (megoldás-sablonok vagy felügyelt alkalmazások) és az SaaS-alkalmazások. A nyilvános ajánlatokhoz hasonlóan a [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)keresztül is létrehozhatók és kezelhetők a saját ajánlatok.  Az ügyfelek percek alatt megadhatók vagy visszavonhatók a privát ajánlatok eléréséhez.

## <a name="creating-private-offers-using-skus-and-plans"></a>Privát ajánlatok létrehozása SKU-val és csomagok használatával

A *nyilvános SKU-kal vagy csomagokkal rendelkező új vagy meglévő ajánlatok*esetében a kiadók egyszerűen létrehozhatnak új, privát változatokat új SKU-i vagy csomagok létrehozásával és magánjellegűként való megjelölésével.  A [privát SKU](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) -ket és a csomagokat egy ajánlat összetevői képezik, és csak a megadott ügyfelek számára láthatók és megvásárolhatóak. A privát SKU-i és csomagok felhasználhatják a nyilvános SKU-hoz vagy tervhez már közzétett alaplemezképeket és/vagy ajánlati metaadatokat. Ez a beállítás lehetővé teszi, hogy a kiadók a nyilvános ajánlatok több privát változatát is létrehozzák anélkül, hogy ugyanazon alaprendszerkép több verzióját kellene közzétenniük, és a metaadatokat is el kellene készíteni. A virtuális gépek és az Azure-alkalmazások esetében, ha egy privát SKU nyilvános SKU-val oszt meg egy alapszintű rendszerképet, az ajánlat alaprendszerképének minden módosítása az adott alaprendszerkép használatával lesz propagálva az összes nyilvános és privát SKU-ra.

Olyan *új ajánlatok esetén, amelyek csak privát SKU-ket vagy csomagokat tartalmaznak*, a kiadók az ajánlatokat bármely más ajánlatként hozhatják létre, majd megjelölik az SKU-t vagy a csomagokat magánjellegűként. Azok az ajánlatok, amelyek csak a privát SKU-val vagy a csomaggal rendelkeznek, nem vehetők igénybe, és nem érhetők el az [Azure Marketplace](https://azuremarketplace.microsoft.com) -en keresztül, vagy az ajánlathoz nem társított ügyfelek által [Azure Portal](https://azure.microsoft.com/features/azure-portal/) .

## <a name="targeting-customers-with-private-offers"></a>Ügyfelek célzása privát ajánlatokkal
A kiadók az új és a meglévő privát ajánlatok esetében is megcélozhatja az ügyfeleket az előfizetés-azonosítók használatával. A virtuális gépet vagy az Azure-alkalmazást használó közzétevők a privát SKU-t egy egyedi Azure-előfizetési AZONOSÍTÓra korlátozhatja, vagy akár 20 000 Azure-előfizetési azonosítóval rendelkező CSV-t is feltölthetnek. Egy SaaS-alkalmazás privát ajánlatának használatakor a kiadók az Azure-előfizetések vagy a bérlői AZONOSÍTÓk összekapcsolhatják a privát csomag rendelkezésre állását a manuális vagy CSV-feltöltési módszer használatával.

Az ajánlat minősítése és közzététele után az ügyfelek perceken belül frissíthetik és eltávolíthatják az SKU-t vagy a csomagot a privát előfizetések szinkronizálása funkció használatával. Ez a funkció lehetővé teszi a kiadók számára, hogy gyorsan és egyszerűen frissítse azon ügyfelek listáját, amelyekhez a privát SKU-t vagy csomagot az ajánlat ismételt hitelesítése vagy újbóli közzététele nélkül mutatjuk be.

## <a name="deploying-private-offers"></a>Privát ajánlatok üzembe helyezése

A privát ajánlatok csak a [Azure Portalon](https://azure.microsoft.com/features/azure-portal/) keresztül érhetők el, és nem az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en keresztül jelennek meg. Miután bejelentkezett a Azure Portalba, az ügyfelek kiválaszthatják a piactér navigációs elemét a privát ajánlatok eléréséhez. A privát ajánlatok is megjelennek a keresési eredmények között, és parancssorból és Azure Resource Manager-sablonokból is üzembe helyezhetők, mint bármely más ajánlat.

![[Privát ajánlatok]](./media/marketplace-publishers-guide/private-offer.png)

A privát ajánlatok is megjelennek a keresési eredmények között. Csak tekintse meg a "Private" jelvényt.

>[!Note]
>A nyilvános ajánlatok nem támogatottak a Cloud Solution Provider program (CSP) viszonteladóján keresztül létesített előfizetésekkel.

## <a name="next-steps"></a>További lépések

A privát ajánlatok használatának megkezdéséhez kövesse a [Private SKU-és csomagok](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) útmutatójának lépéseit.
