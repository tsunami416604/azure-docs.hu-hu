---
title: Privát ajánlatok | Azure piactér
description: Privát ajánlatok az Azure Marketplace-en az alkalmazások és szolgáltatások közzétevői számára.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: efda9fb66a65f1f2cd40de8bf32a8a3fa99aea7f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681434"
---
# <a name="private-offers"></a>Privát ajánlatok

A [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) privát ajánlatai lehetővé teszik a kiadók számára, hogy csak a megrendelt ügyfelek számára látható SKU-ket hozzanak létre.

## <a name="unlock-enterprise-deals-with-private-offers"></a>A vállalati ajánlatok feloldása privát ajánlatokkal

A nagyvállalati ügyfelek egyre gyakrabban használják online piactéreket a felhőalapú megoldások keresésére, kipróbálására és megvásárlására. A privát ajánlatokkal a kiadók a piactéren testre szabott megoldásokat használhatnak a megcélzott ügyfelekkel a vállalatok által igényelt képességekkel:

- Az *egyeztetett díjszabás* lehetővé teszi, hogy a kiadók a nyilvánosan elérhető ajánlatokból származó kedvezményeket és off-listázási díjszabást terjesszenek.
- A *privát feltételek és kikötések* lehetővé teszik a kiadók számára a használati feltételek egy adott ügyfélhez való személyre szabását.
- A *speciális konfigurációk* lehetővé teszik a kiadók számára a Virtual Machines, az Azure-alkalmazások és az SaaS-alkalmazások személyre szabását az ügyfelek igényei szerint. Ez a beállítás azt is lehetővé teszi, hogy a kiadók az új termékek funkcióinak előzetes hozzáférését is megadják, mielőtt szélesebb körben kellene elindítani az összes ügyfelet

A privát ajánlatok lehetővé teszik a kiadók számára, hogy kihasználják a nyilvános piactér skálázhatóságát és globális rendelkezésre állását, valamint az egyéni ajánlatok és konfigurációk egyeztetéséhez és megvalósításához szükséges rugalmasságot és irányítást. Ezek a funkciók együttesen nyitják meg az ajtót a Felhőbeli piactérek erős nagyvállalati bevezetéséhez. A vállalatok mostantól megvásárolhatják és értékesíthetik a várható és a keresletet.

Mostantól elérhetők a virtuális gépek, az Azure-alkalmazások (megoldás-sablonok vagy felügyelt alkalmazások) és az SaaS-alkalmazások. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>Privát ajánlatok létrehozása SKU-val és csomagok használatával

A *nyilvános SKU-kal vagy csomagokkal rendelkező új vagy meglévő ajánlatok*esetében a kiadók egyszerűen létrehozhatnak új, privát változatokat új SKU-i vagy csomagok létrehozásával és magánjellegűként való megjelölésével.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

A privát SKU-ket és a csomagokat egy ajánlat összetevői képezik, és csak a megadott ügyfelek számára láthatók és megvásárolhatóak. A privát SKU-i és csomagok felhasználhatják a nyilvános SKU-hoz vagy tervhez már közzétett alaplemezképeket és/vagy ajánlati metaadatokat. Ez a beállítás lehetővé teszi, hogy a kiadók a nyilvános ajánlatok több privát változatát is létrehozzák anélkül, hogy ugyanazon alaprendszerkép több verzióját kellene közzétenniük, és a metaadatokat is el kellene készíteni. A virtuális gépek és az Azure-alkalmazások esetében, ha egy privát SKU nyilvános SKU-val oszt meg egy alapszintű rendszerképet, az ajánlat alaprendszerképének minden módosítása az adott alaprendszerkép használatával lesz propagálva az összes nyilvános és privát SKU-ra.

Olyan *új ajánlatok esetén, amelyek csak privát SKU-ket vagy csomagokat tartalmaznak*, a kiadók az ajánlatokat bármely más ajánlatként hozhatják létre, majd megjelölik az SKU-t vagy a csomagokat magánjellegűként. Azok az ajánlatok, amelyek csak a saját SKU-kat vagy csomagokat foglalják magukban, nem lesznek felderíthetők vagy elérhetők a Microsoft kereskedelmi piactéren, illetve az ajánlathoz nem társított ügyfelek által [Azure Portal](https://azure.microsoft.com/features/azure-portal/) .

## <a name="targeting-customers-with-private-offers"></a>Ügyfelek célzása privát ajánlatokkal

A kiadók az új és a meglévő privát ajánlatok esetében is megcélozhatja az ügyfeleket az előfizetés-azonosítók használatával. A virtuális gépet vagy az Azure-alkalmazást használó közzétevők a privát SKU-t egy egyedi Azure-előfizetési AZONOSÍTÓra korlátozhatja, vagy akár 20 000 Azure-előfizetési azonosítóval rendelkező CSV-t is feltölthetnek. A SaaS-alkalmazások privát ajánlatának használata során a kiadók hozzárendelhet egy bérlői azonosítót, hogy korlátozzák a privát csomag rendelkezésre állását a manuális vagy CSV-feltöltési módszer használatával.

Az ajánlat minősítése és közzététele után az ügyfelek perceken belül frissíthetik és eltávolíthatják az SKU-t vagy a csomagot a privát előfizetések szinkronizálása funkció használatával. Ez a funkció lehetővé teszi a kiadók számára, hogy gyorsan és egyszerűen frissítse azon ügyfelek listáját, amelyekhez a privát SKU-t vagy csomagot az ajánlat újbóli hitelesítése vagy közzététele nélkül mutatjuk be.

## <a name="deploying-private-offers"></a>Privát ajánlatok üzembe helyezése

A privát ajánlatok csak a [Azure Portalon](https://azure.microsoft.com/features/azure-portal/) keresztül érhetők el, és nem jelennek meg [Microsoft AppSource](https://appsource.microsoft.com/) vagy az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en. Ha többet szeretne megtudni a különböző kereskedelmi Piactéri kirakatok közzétételéről, olvassa el [a közzétételi lehetőség meghatározása](./determine-your-listing-type.md)című témakört.

Miután bejelentkezett a Azure Portalba, az ügyfelek kiválaszthatják a piactér navigációs elemét a privát ajánlatok eléréséhez. A privát ajánlatok is megjelennek a keresési eredmények között, és parancssorból és Azure Resource Manager-sablonokból is üzembe helyezhetők, mint bármely más ajánlat.

![[Privát ajánlatok]](./media/marketplace-publishers-guide/private-offer.png)

A privát ajánlatok is megjelennek a keresési eredmények között. Csak keresse meg a "Private" jelvényt.

>[!Note]
>A nyilvános ajánlatok nem támogatottak a Cloud Solution Provider program (CSP) viszonteladóján keresztül létesített előfizetésekkel.

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->