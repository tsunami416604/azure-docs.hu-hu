---
title: Azure-alkalmazások megoldási sablon ajánlatának közzétételi útmutatója | Azure piactér
description: Ez a cikk a megoldási sablon Azure Marketplace-en való közzétételének követelményeit ismerteti.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: 8d02d942fce7bd51a116cc4c19eac9faca0060ef
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561483"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure-alkalmazások: Megoldási sablon ajánlatának közzétételi útmutatója

A megoldási sablonok az egyik fő módszer a megoldás közzétételére a piactéren. Az útmutató segítségével megismerheti az ajánlat követelményeit. 

Az Azure-alkalmazás: megoldás-sablon típusú ajánlat típusa akkor használható, ha a megoldás további üzembe helyezést és konfigurációs automatizálást igényel egy virtuális gépen kívül. Egy vagy több virtuális gép üzembe helyezését az Azure apps: megoldás-sablonok használatával automatizálhatja. A hálózatkezelési és tárolási erőforrásokat is kiépítheti. Azure-alkalmazások: a megoldási sablonok ajánlat típusa automatizálási előnyöket biztosít az egyes virtuális gépek és a teljes IaaS-alapú megoldások számára.

Ezek a megoldási sablonok a piactéren üzembe helyezett és számlázható tranzakciós ajánlatok. A felhasználó által megjelenő művelet hívása "Letöltés most".


## <a name="requirements-for-solution-templates"></a>A megoldási sablonokra vonatkozó követelmények

| **Követelmények** | **Részletek**  |
| ---------------  | -----------  |
|Számlázás és mérés    |  Az erőforrások az ügyfél Azure-előfizetésében lesznek kiépítve. Az utólagos elszámolású (ÁTTÉRÉSHEZ) virtuális gépeket a Microsofton keresztül, az ügyfél Azure-előfizetésével (ÁTTÉRÉSHEZ) számoljuk el.  <br/> Ha saját licencet (BYOL) használ, míg a Microsoft az ügyfél-előfizetésben felmerülő infrastrukturális költségeket számlázza, akkor a szoftver licencelési díját közvetlenül az ügyfélnek kell átirányítani.   |
|Azure-kompatibilis virtuális merevlemez (VHD)  |   A virtuális gépeket Windows vagy Linux rendszerre kell építeni.  További információ [: Azure-kompatibilis virtuális merevlemez létrehozása](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Ügyfél-használati jóváírás | Az Azure Marketplace-en közzétett összes megoldási sablonban engedélyezni kell az ügyfél-használati jóváírást. Az ügyfél-használati jóváírással és annak engedélyezésével kapcsolatos további információkért tekintse meg az [Azure-partneri ügyfél használati](./azure-partner-customer-usage-attribution.md)feltételét ismertető témakört.  |
| Managed Disks használata | A [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) az alapértelmezett beállítás a IaaS virtuális gépek megőrzött lemezei számára az Azure-ban. A megoldási sablonokban Managed Diskst kell használnia. <br> <br> 1. A megoldási sablonok frissítéséhez kövesse az Azure ARM-sablonok Managed Disks használatának [útmutatását](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) és [mintáit](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) . <br> <br> 2. Az alábbi útmutatást követve importálja a Managed Disks mögöttes VHD-jét egy Storage-fiókba, hogy a virtuális merevlemezt rendszerképként tegye közzé a piactéren: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Parancssori felület](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>További lépések
Ha még nem tette meg, [regisztráljon](https://azuremarketplace.microsoft.com/sell) a piactéren.

Ha regisztrálva van, és új ajánlatot hoz létre, vagy egy meglévőt használ, jelentkezzen be [](https://cloudpartner.azure.com) a Cloud Partner Portalba az ajánlat létrehozásához vagy befejezéséhez.
