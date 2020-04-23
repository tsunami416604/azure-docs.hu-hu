---
title: Azure-alkalmazások megoldássablonja közzétételi útmutatót kínál | Azure Piactér
description: Ez a cikk ismerteti a megoldássablon közzétételének követelményeit az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084855"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure Applications: A megoldássablon közzétételi követelményeket kínál

Ez a cikk ismerteti a megoldássablon ajánlattípusának követelményeit, amely az Egyik módja egy Azure-alkalmazásajánlat közzétételének az Azure Marketplace-en. A megoldássablon-ajánlat típusához egy [Azure Resource Manager-sablon (ARM-sablon)](../azure-resource-manager/templates/overview.md) szükséges a megoldási infrastruktúra automatikus üzembe helyezéséhez.

Használja az Azure alkalmazásmegoldás-sablon ajánlattípusát, ha a következő feltételek szükségesek:

- A megoldás további üzembe helyezési és konfigurációs automatizálást igényel egyetlen virtuális számítógépen kívül, például virtuális gépek, hálózati és tárolási erőforrások kombinációját.
- Az ügyfél fogja kezelni a megoldást magukat.

A felhasználó által az ajánlattípushoz látott cselekvésre való felhívás a "Get It Now" (Lehívás most) a következő: "Get It Now".

## <a name="requirements-for-solution-template-offers"></a>A megoldássablon-ajánlatokra vonatkozó követelmények

| **Követelmények** | **Részletek**  |
| ---------------  | -----------  |
|Számlázás és mérés    |  A megoldássablon-ajánlatok nem tranzakciós ajánlatok, de a Microsoft kereskedelmi piactéren keresztül számlázott fizetős virtuálisgép-ajánlatok üzembe helyezésére használhatók. A megoldás ARM-sablonja által üzembe helyezett erőforrások az ügyfél Azure-előfizetésében lesznek kiépítve. A felosztó-ki(PAYGO) virtuális gépeket a Microsofton keresztül, az ügyfél Azure-előfizetésén keresztül számlázunk.<br/> A bring-your-own-license (BYOL) esetében, míg a Microsoft kiszámlázja az ügyfél-előfizetés során felmerülő infrastrukturális költségeket, ön a szoftverlicencelési díjakat közvetlenül az ügyfélnek adja át.   |
|Azure-kompatibilis virtuális merevlemez (VHD)  |   A virtuális gépeknek Windows vagy Linux rendszerre kell épülniük. További információkért lásd: <ul> <li>[Azure-alkalmazásajánlat létrehozása](./partner-center-portal/create-new-azure-apps-offer.md)(Windows VD-khez)</li><li>[Linux disztribúciók az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (Linux VHD-k).</li></ul> |
| Vevői használati hozzárendelés | Az Azure Piactéren közzétett összes megoldássablonon engedélyezni kell az ügyfélhasználat-hozzárendelést. Az ügyfélhasználat-hozzárendeléssel és annak engedélyezésével kapcsolatos további információkért lásd: [Azure partner-ügyfélhasználati hozzárendelés.](./azure-partner-customer-usage-attribution.md)  |
| Felügyelt lemezek használata | [Felügyelt lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) az alapértelmezett beállítás az Azure-beli IaaS virtuális gépek megőrzött lemezei számára. A felügyelt lemezeket megoldássablonokban kell használnia. <br> <br> 1. Kövesse a felügyelt lemezek azure ARM-sablonokban a felügyelt lemezek használatával [kapcsolatos útmutatást](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) és [mintákat](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) a megoldássablonok frissítéséhez. <br> <br> 2. Kövesse az alábbi utasításokat a felügyelt lemezek alapjául szolgáló virtuális merevlemez ek egy tárfiókba történő importálásához a virtuális merevlemez képként való közzétételéhez a Marketplace-en: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [parancssori felület](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>További lépések

- Ha még nem tette meg, [ismerje meg](https://azuremarketplace.microsoft.com/sell) az Azure Piactér.
- [Jelentkezzen be a Partnerközpontba](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) az ajánlat létrehozásához vagy teljesítéséhez.
- [Hozzon létre egy Azure-alkalmazás ajánlatot](./partner-center-portal/create-new-azure-apps-offer.md) további információkért.
