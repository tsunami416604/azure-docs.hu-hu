---
title: Hogyan lehet létrehozni és üzembe helyezni egy ajánlatot a piactéren – áttekintés |} A Microsoft Docs
description: Egy engedélyezett Microsoft fejlesztői válnak, és létrehozása és üzembe helyezése egy virtuálisgép-lemezkép, a sablon, szolgáltatás vagy az Azure piactéren elérhető fejlesztői szolgáltatás szükséges lépések ismertetése
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
ms.openlocfilehash: 82580fbab68eab28a2027cd277213f1fb2a76e07
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716001"
---
> [!NOTE]
> Ez a dokumentáció már nem aktuális, és nem pontos. Ehelyett nyissa meg az Azure Marketplace-en [– eladói útmutató](https://docs.microsoft.com/azure/marketplace/seller-guide/cloud-partner-portal-seller-guide) útmutató az ajánlat közzététele az Azure Marketplace-en.

# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Közzétehet és kezelhet az Azure Marketplace-ajánlat
Ez a cikk segítségével a fejlesztők létrehozása, üzembe helyezése és kezelése a más Azure-ügyfelek és partnerek vásárlása és használata az Azure piactéren szereplő megoldásaikat biztosítunk.

## <a name="marketplace-publishing"></a>Marketplace – közzététel
Egy Azure közzétevőként terjesztése és értékesíthetik az innovatív megoldások vagy a szolgáltatás más fejlesztők, független szoftverszállítók, és informatikai szakemberek számára a Marketplace-en. A piactéren a felhasználóknak, akik gyorsan fejleszthetnek felhőalapú alkalmazásokat és a mobil megoldások elérheti. A megoldás célozza meg benne az üzleti felhasználók, előfordulhat, hogy szeretné-e, vegye figyelembe a [AppSource](http://appsource.microsoft.com) Marketplace-en.


## <a name="supported-types-of-solutions"></a>Támogatott típusú megoldások
A vállalati ajánlat szeretne tenni, közzétevő, hogy milyen típusú megoldás meghatározásához először is. A piactér támogatja a következő típusú ajánlatok:

|Megoldás típusa|Virtuális gép|Megoldássablon|
|---|---|---|
|**Definíció**|A egy teljes körűen telepített operációs rendszer és a egy vagy több előre konfigurált rendszerképeket. Virtuálisgép-lemezkép hozhat létre és helyezhet üzembe virtuális gépeket az Azure Virtual Machines szolgáltatás szükséges információkat tartalmazza.|Olyan adatszerkezet, amely egy vagy több különböző Azure-szolgáltatásokat is hivatkozhatnak, beleértve a szolgáltatások más eladók teszi közzé. Az Azure-előfizetők használhatják azt az egyetlen, koordinált módon egy vagy több ajánlatok üzembe helyezéséhez.|
|**Példa**|Egy Azure közzétevőként létrehozta és ellenőrizni a virtuális gép egy innovatív adatbázis-szolgáltatással. Más Azure-előfizetők be kell szereznie, és a cloud service-környezetek üzembe helyezése a virtuális gép szeretné.|Egy Azure közzétevőként a szolgáltatások már kötegelve, amelyek megkönnyítik a gyors üzembe helyezéséhez a cloud services és a terheléselosztás, a fokozott biztonságot és a magas rendelkezésre állású Azure-ban. Más Azure-előfizetők is időt takaríthat meg, amely megfelel a cél a megoldássablon beszerzése. Nem kell manuálisan keresse meg, beszerezzük, üzembe helyezését és az azonos vagy hasonló Azure-szolgáltatások konfigurálása.|

> [!NOTE]
> Néhány lépést a különböző típusú megoldások között vannak megosztva, és más, a megoldás megfelelő típusát különböző. Ez a cikk a bármilyen típusú megoldás végrehajtásához szükséges lépések rövid áttekintést nyújt.

## <a name="publish-a-solution"></a>A megoldás közzététele
![Jelöl ki, regisztrálása, közzététele](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>A megoldás jelölje a előzetes jóváhagyásra
A virtuális gép közzététele [megoldás](https://createopportunity.azurewebsites.net) a Marketplace-en, végezze el a Microsoft Azure Certified **megoldás jelölési űrlapot**.

>[!NOTE]
> Ha egy Partner Ügyfélmenedzserével vagy egy partneri DX Manager dolgozik, kérje meg, hogy jelöljön ki a megoldás az Azure Certified program. Azt is választhatja a [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) weblap és az alkalmazás űrlap kitöltésekor. Adja meg az e-mailt, a Partner Ügyfélfelelőshöz vagy a Partner Manager DX a **forduljon a Microsoft támogató** mezőbe.

Ha megfelel a jogosultsági feltételeket a [Azure piactér részvételi szabályzatának](http://go.microsoft.com/fwlink/?LinkID=526833) és az alkalmazás jóvá van hagyva, hogy való használatának megkezdéséhez, üzembe helyezni a megoldást a Marketplace-en.

### <a name="register-your-account-as-a-microsoft-seller"></a>A fiók regisztrálásához a Microsoft értékesítői
Microsoft-fiókja, regisztráljon egy [Microsoft Developer-fiók](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Tegye közzé a megoldását
A megoldás közzététele a piactéren, kövesse az alábbi lépéseket:
1. A felhasználóknak követelmények teljesítéséhez.

    a. Életciklusában képesek megfelelni a [felhasználóknak Előfeltételek](marketplace-publishing-pre-requisites.md).

    b. Életciklusában képesek megfelelni a [VM technikai Előfeltételek](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Életciklusában képesek megfelelni a [megoldás sablon technikai Előfeltételek](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Az ajánlat létrehozása.

    a. Hozzon létre egy [virtuális gép](marketplace-publishing-vm-image-creation.md) kínálnak.

    b. Hozzon létre egy [megoldássablon](marketplace-publishing-solution-template-creation.md) kínálnak.

3. Az ajánlat létrehozása [marketing-tartalom](marketplace-publishing-push-to-staging.md).

4. Az ajánlat tesztelése az átmeneti állapotában is.

    a. A Virtuálisgép-ajánlat a teszt [átmeneti](marketplace-publishing-vm-image-test-in-staging.md).

    b. A megoldás sablon ajánlatot tesztelése [átmeneti](marketplace-publishing-solution-template-test-in-staging.md).

5. Az ajánlat az üzembe helyezése a [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Hozzon létre és virtuálisgép-lemezképek kezelése
Hozzon létre, és egy Virtuálisgép-lemezkép kezelheti ezeket az erőforrásokat:
* Hozzon létre egy Virtuálisgép-lemezkép [helyszíni](marketplace-publishing-vm-image-creation-on-premise.md).
* Virtuális gép létrehozása [az Azure Portalon Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Virtuális gép létrehozása [Linux az Azure Portalon](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Során tapasztalt gyakori problémák megoldása [virtuális merevlemez létrehozása](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>A megoldás kezelése
A megoldás segítségével kezelheti az alábbi forrásanyagokat:
* [A virtuálisgép-ajánlat utáni üzemi útmutató elolvasása](marketplace-publishing-vm-image-post-publishing.md)
* [Az ajánlat vagy a Termékváltozat felhasználóknak részleteinek frissítése](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [A technikai részleteket ajánlat vagy a Termékváltozat frissítése](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Adjon hozzá egy új Termékváltozatot alatt felsorolt ajánlat](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Az adatlemezek száma a listán szereplő termékváltozat módosítása](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [A Marketplace-ről egy listán szereplő ajánlat törlése](marketplace-publishing-vm-image-post-publishing.md)
* [A listában szereplő SKU törlése a Marketplace-ről](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [A jelenlegi verziója a listában szereplő SKU törlése a Marketplace-ről](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Visszaállítás a termelési értékekkel a listaelem ár](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Visszaállítás a termelési értékekkel a számlázási modell](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Az éles értéket egy listán szereplő SKU láthatóságának beállítását visszaállítani](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>További források
[Állítsa be az Azure PowerShell-lel](marketplace-publishing-powershell-setup.md)
