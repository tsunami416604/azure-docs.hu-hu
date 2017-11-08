---
title: "Bemutatja, hogyan hozhat létre és telepíthet egy ajánlatot a piactér áttekintése |} Microsoft Docs"
description: "Ismerkedjen meg a lépésekkel kell egy engedélyezett Microsoft developer válnak és létrehozása és központi telepítése egy virtuális gép lemezképére, sablon, adatokat vagy fejlesztői szolgáltatást az Azure piactéren"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
ms.openlocfilehash: fa00f176ff56c13033907e134fc236c291308028
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Közzétehet és kezelhet egy ajánlatot az Azure piactéren
Ez a cikk segítségével a fejlesztők létrehozásához, telepítéséhez és az egyéb Azure-ügyfél és a partnerek beszerzési és használata az Azure piactéren felsorolt megoldások kezelése valósul meg.

## <a name="marketplace-publishing"></a>Piactér-közzététel
Egy Azure közzétevőként terjesztése és a innovatív megoldás vagy szolgáltatás értékesítés más fejlesztők számára, a független szoftverszállítók, és az informatikai szakemberek számára a piactéren. A piactéren keresztül érhető el az ügyfelek, akik gyorsan fejlesztése a felhőalapú alkalmazások és a mobil megoldások. Ha a megoldás az üzleti felhasználók célozza, érdemes figyelembe kell venni a [AppSource](http://appsource.microsoft.com) piactéren.


## <a name="supported-types-of-solutions"></a>Támogatott típusok megoldások
Először is meg szeretné tenni, mert a közzétevő megadhatja, milyen típusú megoldás vállalata kínál. A piactér a következő ajánlatok-típusokat támogatja:

|Megoldás típusa|Virtuális gép|Megoldás sablon|
|---|---|---|
|**Meghatározása**|Előre konfigurált képek a teljes telepített operációs rendszer és egy vagy több alkalmazást. A virtuálisgép-lemezkép létrehozása és telepítése a virtuális gépek az Azure Virtual Machines szolgáltatásban szükséges információkat tartalmazza.|Olyan adatszerkezet, amely egy vagy több különböző Azure-szolgáltatások hivatkozhat, más eladók többek között a szolgáltatások közzé. Az Azure-előfizetők segítségével egy vagy több ajánlatok egyetlen, koordinált módon telepítheti.|
|**Példa**|Egy Azure közzétevőként elkészítette és ellenőrizni a virtuális gép és egy új adatbázis-szolgáltatás. Egyéb Azure-előfizetők be kell szereznie, és a virtuális gép üzembe helyezés a cloud service-környezetek szeretné.|Egy Azure közzétevőként a szolgáltatások már kötegelt, amelyek miatt gyors felhőszolgáltatások terheléselosztás, a fokozott biztonságot és a magas rendelkezésre állású telepítése Azure között. Egyéb Azure-előfizetők időt takaríthat beszerzése a megoldás-sablont, amely megfelel a célja. Nem rendelkeznek manuálisan található, be kell szereznie, telepítésére és konfigurálására ugyanazon vagy hasonló Azure-szolgáltatás.|

> [!NOTE]
> Néhány lépést a különböző típusú megoldások között vannak megosztva, és mások különbözőek a megoldás megfelelő típusú. Ez a cikk rövid áttekintést nyújt az azon megoldás elvégzéséhez szükséges lépéseket.

## <a name="publish-a-solution"></a>A megoldás közzététele
![Jelöljön ki, regisztrálása, közzététele](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>A megoldás megadjuk előtti jóváhagyásra
Közzététele egy virtuális gép [megoldás](https://createopportunity.azurewebsites.net) a piactéren, végezze el a Microsoft Azure hitelesített **megoldás jelölési űrlap**.

>[!NOTE]
> Ha egy Partner-Ügyfélreferenséhez vagy a DX Partner Manager dolgozik, kérje meg őket, hogy jelöljön ki a megoldás az Azure hitelesített program. Ön is végrehajthatja a [hivatalos Microsoft Azure](http://createopportunity.azurewebsites.net) weblap és töltse ki az alkalmazás az űrlapot. Adja meg az e-mailt, a Partner Ügyfélfelelőshöz vagy a Partner Manager DX a **forduljon a Microsoft támogató** mezőbe.

Ha a jogosultsági feltételeknek a a [Azure piactér részvételét házirendek](http://go.microsoft.com/fwlink/?LinkID=526833) és az alkalmazás jóváhagyva, először a használata akkor érheti a megoldás a piactéren.

### <a name="register-your-account-as-a-microsoft-seller"></a>A fiók regisztrálásához a Microsoft értékesítő
Regisztrálja a Microsoft-fiókjával, mint egy [Microsoft Developer-fiók](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>A megoldás közzététele
A megoldást a piactérről való közzétételéhez kövesse az alábbi lépéseket:
1. A felhasználóknak követelmények teljesítéséhez.

    a. Teljesítéséhez a [felhasználóknak Előfeltételek](marketplace-publishing-pre-requisites.md).

    b. Teljesítéséhez a [VM műszaki Előfeltételek](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Teljesítéséhez a [megoldás sablon műszaki Előfeltételek](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Az ajánlat létrehozása.

    a. Hozzon létre egy [virtuális gép](marketplace-publishing-vm-image-creation.md) kínálnak.

    b. Hozzon létre egy [megoldássablonban](marketplace-publishing-solution-template-creation.md) kínálnak.

3. Hozzon létre az ajánlatot [tartalom marketing](marketplace-publishing-push-to-staging.md).

4. Tesztelje az ajánlatot a tesztelési.

    a. A virtuális gép ajánlatát tesztelése [átmeneti](marketplace-publishing-vm-image-test-in-staging.md).

    b. A sablon megoldás ajánlatát tesztelése [átmeneti](marketplace-publishing-solution-template-test-in-staging.md).

5. A lefokozásra szolgáló telepítése a [piactér](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Létrehozását és kezelését egy virtuálisgép-lemezkép
Hozzon létre, és Virtuálisgép-lemezkép kezelheti ezeket az erőforrásokat:
* Hozzon létre egy Virtuálisgép-lemezkép [helyszíni](marketplace-publishing-vm-image-creation-on-premise.md).
* Hozzon létre egy virtuális gépet futtató [Windows Azure-portálon](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Hozzon létre egy virtuális gépet futtató [az Azure portálon Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Hibaelhárítás során tapasztalt gyakori problémákkal [virtuális merevlemez létrehozása](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>A megoldás kezelése
A megoldás segítségével kezelheti a következő erőforrások:
* [Olvassa el a gyártás utáni útmutatót a virtuális gép ajánlatok](marketplace-publishing-vm-image-post-publishing.md)
* [Az ajánlat vagy egy SKU felhasználóknak részleteinek frissítése](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Az ajánlat vagy egy SKU műszaki részleteinek frissítése](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Adja hozzá a felsorolt ajánlat keretében egy új Termékváltozat](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [A felsorolt termékváltozat adatlemez módosítása](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [A felsorolt ajánlat törlése a piactérről](marketplace-publishing-vm-image-post-publishing.md)
* [A felsorolt SKU törlése a piactérről](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [A felsorolt SKU jelenlegi verziója törlése a piactérről](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Állítsa vissza az éles értékek listaelem ár](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [A számlázási modellt éles értékek visszaállításához](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Állítsa vissza a láthatósági a felsorolt metódust az éles értékre](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>További források
[Azure PowerShell beállítása](marketplace-publishing-powershell-setup.md)
