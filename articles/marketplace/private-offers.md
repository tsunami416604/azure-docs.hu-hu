---
title: Privát ajánlatok a Microsoft kereskedelmi piactéren
description: Privát ajánlatok a Microsoft kereskedelmi piactéren az alkalmazások és szolgáltatások közzétevői számára.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 07/01/2020
ms.openlocfilehash: 5f8487126cd187ea66f9423a480c9a73151154c3
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168014"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Privát ajánlatok a Microsoft kereskedelmi piactéren

A [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) privát ajánlatai lehetővé teszik a kiadók számára, hogy olyan csomagokat hozzanak létre, amelyek csak a célcsoportok számára láthatók. Ez a cikk a privát ajánlatok lehetőségeit és előnyeit ismerteti.

## <a name="unlock-enterprise-deals-with-private-offers"></a>A vállalati ajánlatok feloldása privát ajánlatokkal

A nagyvállalati ügyfelek egyre gyakrabban használják online piactéreket a felhőalapú megoldások keresésére, kipróbálására és megvásárlására. A privát ajánlatokkal a kiadók a piactéren testre szabott megoldásokat használhatnak a megcélzott ügyfelekkel a vállalatok által igényelt képességekkel:

- Az *egyeztetett díjszabás* lehetővé teszi, hogy a kiadók a nyilvánosan elérhető ajánlatokból származó kedvezményeket és off-listázási díjszabást terjesszenek.
- A *privát feltételek és kikötések* lehetővé teszik a kiadók számára a használati feltételek egy adott ügyfélhez való személyre szabását.
- A *speciális konfigurációk* lehetővé teszik a kiadók számára a Virtual Machines, az Azure-alkalmazások és az SaaS-alkalmazások személyre szabását az ügyfelek igényei szerint. Ez a beállítás azt is lehetővé teszi, hogy a kiadók az új termékek funkcióinak előzetes hozzáférését is megadják, mielőtt szélesebb körben kellene elindítani az összes ügyfelet

A privát ajánlatok lehetővé teszik a kiadók számára, hogy kihasználják a nyilvános piactér skálázhatóságát és globális rendelkezésre állását, valamint az egyéni ajánlatok és konfigurációk egyeztetéséhez és megvalósításához szükséges rugalmasságot és irányítást. Ezek a funkciók együttesen nyitják meg az ajtót a Felhőbeli piactérek erős nagyvállalati bevezetéséhez. A vállalatok mostantól megvásárolhatják és értékesíthetik a várható és a keresletet.

Mostantól elérhetők a virtuális gépek, az Azure-alkalmazások (megoldás-sablonok vagy felügyelt alkalmazások) és az SaaS-alkalmazások.

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-plans"></a>Privát ajánlatok létrehozása csomagok használatával

A *csomagokkal rendelkező új vagy meglévő ajánlatok*esetében a kiadók egyszerűen létrehozhatnak új, privát változatokat új csomagok (korábbi nevén SKU) létrehozásával és magánjellegűként való megjelölésével. Minden ajánlat legfeljebb 45 privát csomaggal rendelkezhet.

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) --->

A Private-csomagok egy ajánlat összetevői, és csak a megtervezett ügyfelek számára láthatók és megvásárolhatóak. A privát csomagok felhasználhatják a nyilvános csomagokhoz már közzétett alaplemezképeket és/vagy ajánlati metaadatokat. Ez a beállítás lehetővé teszi, hogy a kiadók a nyilvános ajánlatok több privát változatát is létrehozzák anélkül, hogy ugyanazon alaprendszerkép több verzióját kellene közzétenniük, és a metaadatokat is el kellene készíteni. A virtuális gépek és az Azure-alkalmazások esetében csak akkor érhető el, ha egy privát csomag nyilvános csomaggal rendelkező alaprendszerképet oszt meg, az ajánlat alaprendszerképének minden módosítása az adott alaprendszerkép használatával lesz propagálva az összes nyilvános és privát csomagra.

Az olyan *új ajánlatok esetében, amelyek csak privát csomagokat foglalnak magukban*, a kiadók az ajánlatokat bármely más ajánlatként hozhatják létre, majd megjelölik magánjellegűként a terveket. Azok az ajánlatok, amelyek csak privát csomagokkal rendelkeznek, nem lesznek felderíthetők vagy elérhetők a Microsoft kereskedelmi piactéren, illetve az ajánlathoz nem társított ügyfelek által [Azure Portal](https://azure.microsoft.com/features/azure-portal/) .

>[!NOTE]
>A csak privát csomagokat tartalmazó ajánlat nem jelenik meg a nyilvános Azure piactéren vagy AppSource.

## <a name="targeting-customers-with-private-offers"></a>Ügyfelek célzása privát ajánlatokkal

A kiadók az új és a meglévő privát ajánlatok esetében is megcélozhatja az ügyfeleket az előfizetés-azonosítók használatával. A virtuális gépet vagy Azure-alkalmazási ajánlatot használó közzétevők egy egyéni Azure-előfizetés-AZONOSÍTÓra korlátozhatja a privát csomag rendelkezésre állását, vagy akár 20 000 Azure-előfizetési azonosítóval rendelkező CSV-t is feltölthet. A SaaS-alkalmazások privát ajánlatának használata során a kiadók hozzárendelhet egy bérlői azonosítót, hogy korlátozzák a privát csomag rendelkezésre állását a manuális vagy CSV-feltöltési módszer használatával.

Az ajánlat minősítése és közzététele után az ügyfelek perceken belül frissíthetik vagy eltávolíthatják a csomagot a privát előfizetések szinkronizálása funkció használatával. Ez a funkció lehetővé teszi a kiadók számára, hogy gyorsan és egyszerűen frissítse azon ügyfelek listáját, amelyekhez a privát csomag az ajánlat hitelesítése vagy közzététele nélkül jelenik meg.

## <a name="deploying-private-offers"></a>Privát ajánlatok üzembe helyezése

A privát ajánlatok csak a [Azure Portalon](https://azure.microsoft.com/features/azure-portal/) keresztül érhetők el, és nem jelennek meg [Microsoft AppSource](https://appsource.microsoft.com/) vagy az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en. Ha többet szeretne megtudni a különböző kereskedelmi Piactéri kirakatok közzétételéről, olvassa el [a közzétételi lehetőség meghatározása](./determine-your-listing-type.md)című témakört.

Miután bejelentkezett a Azure Portalba, az ügyfelek kiválaszthatják a piactér navigációs elemét a privát ajánlatokhoz való hozzáféréshez. A privát ajánlatok is megjelennek a keresési eredmények között, és parancssorból és Azure Resource Manager-sablonokból is üzembe helyezhetők, mint bármely más ajánlat.

![[Privát ajánlatok]](./media/marketplace-publishers-guide/private-offer.png)

A privát ajánlatok is megjelennek a keresési eredmények között. Csak keresse meg a **privát** jelvényt.

>[!Note]
>A nyilvános ajánlatok nem támogatottak a Cloud Solution Provider (CSP) program viszonteladóján keresztül létesített előfizetésekkel.


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.
--->
