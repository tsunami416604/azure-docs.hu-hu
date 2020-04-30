---
title: Azure-alkalmazások megoldási sablon ajánlatának közzétételi útmutatója | Azure piactér
description: Ez a cikk a megoldási sablon Azure Marketplace-en való közzétételének követelményeit ismerteti.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084855"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure-alkalmazások: a megoldás sablonja ajánlat közzétételi követelményei

Ez a cikk a megoldás-sablon ajánlat típusának követelményeit ismerteti, amely egy Azure-alkalmazás ajánlatának az Azure Marketplace-en való közzétételének egyik módja. A megoldási sablon típusa [Azure Resource Manager sablon (ARM-sablon)](../azure-resource-manager/templates/overview.md) szükséges a megoldás-infrastruktúra automatikus telepítéséhez.

Ha a következő feltételek szükségesek, használja az Azure-alkalmazás megoldás-sablonjának ajánlat típusát:

- A megoldás további üzembe helyezési és konfigurálási automatizálást igényel egy virtuális gépen kívül, például a virtuális gépek, a Hálózatkezelés és a tárolási erőforrások kombinációjával.
- Az ügyfél maga fogja felügyelni a megoldást.

A művelet meghívása, amelyet a felhasználó a jelen ajánlat típusához lát, "Letöltés most".

## <a name="requirements-for-solution-template-offers"></a>A megoldási sablonok ajánlatának követelményei

| **Követelmények** | **Részletek**  |
| ---------------  | -----------  |
|Számlázás és mérés    |  A megoldási sablon által kínált ajánlatok nem Transact-ajánlatok, de használhatók a fizetős virtuális gépek a Microsoft kereskedelmi piactéren keresztüli számlázására. A megoldás ARM-sablon által üzembe helyezett erőforrásai az ügyfél Azure-előfizetésében lesznek kiépítve. Az utólagos elszámolású (ÁTTÉRÉSHEZ) virtuális gépeket a Microsofton keresztül, az ügyfél Azure-előfizetésén keresztül számlázjuk.<br/> Ha saját licencet (BYOL) használ, míg a Microsoft az ügyfél-előfizetésben felmerülő infrastrukturális költségeket számlázza, akkor a szoftver licencelési díját közvetlenül az ügyfélnek kell átirányítani.   |
|Azure-kompatibilis virtuális merevlemez (VHD)  |   A virtuális gépeket Windows vagy Linux rendszerre kell építeni. További információkért lásd: <ul> <li>[Azure-beli alkalmazás-ajánlat létrehozása](./partner-center-portal/create-new-azure-apps-offer.md)(Windows rendszerű virtuális merevlemezekhez)</li><li>Az Azure-ban [támogatott Linux-disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (linuxos virtuális merevlemezek esetén).</li></ul> |
| Ügyfél-használati jóváírás | Az Azure Marketplace-en közzétett összes megoldási sablonban engedélyezni kell az ügyfél-használati jóváírást. Az ügyfél-használati jóváírással és annak engedélyezésével kapcsolatos további információkért tekintse meg az [Azure-partneri ügyfél használati](./azure-partner-customer-usage-attribution.md)feltételét ismertető témakört.  |
| Felügyelt lemezek használata | A [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) az alapértelmezett beállítás a IaaS virtuális gépek megőrzött lemezei számára az Azure-ban. A megoldási sablonokban Managed Diskst kell használnia. <br> <br> 1. kövesse az Azure ARM-sablonok Managed Disks használatának [útmutatását](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) és [mintáit](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) a megoldási sablonok frissítéséhez. <br> <br> 2. az alábbi útmutatást követve importálja a Managed Disks mögöttes VHD-jét egy Storage-fiókba, hogy a virtuális merevlemezt rendszerképként tegye közzé a piactéren: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [parancssori felület](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>További lépések

- Ha még nem tette meg, [Ismerkedjen meg](https://azuremarketplace.microsoft.com/sell) az Azure piactérrel.
- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
- További információért [hozzon létre egy Azure](./partner-center-portal/create-new-azure-apps-offer.md) -beli alkalmazás-ajánlatot.
