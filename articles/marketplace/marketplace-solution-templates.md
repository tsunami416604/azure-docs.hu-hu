---
title: Azure Applications Solution Template Offer Publishing Guide | Azure Piactér
description: Ez a cikk ismerteti a megoldássablon közzétételének követelményeit az Azure Marketplace-en.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: faff12adbf6c0f88f3161e1e4f2760da3b7966ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687516"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure-alkalmazások: Megoldássablon-közzétételi útmutató

A megoldássablonok a megoldások marketplace-en való közzétételének egyik fő módja. Ebben az útmutatóban megismerheti az ajánlat követelményeit. 

Használja az Azure-alkalmazást: megoldássablon-ajánlat típusa, ha a megoldás egyetlen virtuális gépen túl további üzembe helyezést és konfigurációs automatizálást igényel. Automatizálhatja egy vagy több virtuális gép üzembe létesítését az Azure-alkalmazások használatával: megoldássablonok. Hálózati és tárolási erőforrásokat is kiépíthet. Azure-alkalmazások: a megoldássablonok ajánlattípus automatizálási előnyöket biztosít egyetlen virtuális géphez és teljes IaaS-alapú megoldásokhoz.

Ezek a megoldássablonok nem tranzakciós ajánlatok, de a Microsoft kereskedelmi piacterén keresztül számlázott fizetős virtuálisgép-ajánlatok üzembe helyezésére használhatók. A felhasználó által látott cselekvésre való felhívás a "Get It Now" (Get It Now) (Get It Now) (Get It Now) (Get It Now) (Get It Now) (Get It Now)


## <a name="requirements-for-solution-templates"></a>A megoldássablonokra vonatkozó követelmények

| **Követelmények** | **Részletek**  |
| ---------------  | -----------  |
|Számlázás és mérés    |  Az erőforrások az ügyfél Azure-előfizetésében lesznek kiépítve. A felosztó-ki(PAYGO) virtuális gépeket a Microsofton keresztül, az ügyfél Azure-előfizetésén (PAYGO) keresztül számlázunk.  <br/> A bring-your-own-license (BYOL) esetében, míg a Microsoft kiszámlázja az ügyfél-előfizetés során felmerülő infrastrukturális költségeket, ön a szoftverlicencelési díjakat közvetlenül az ügyfélnek adja át.   |
|Azure-kompatibilis virtuális merevlemez (VHD)  |   A virtuális gépeknek Windows vagy Linux rendszerre kell épülniük.  További információt az [Azure-alkalmazásajánlat létrehozása](./partner-center-portal/create-new-azure-apps-offer.md)című témakörben talál. |
| Vevői használati hozzárendelés | Az Azure Piactéren közzétett összes megoldássablonon engedélyezni kell az ügyfélhasználat-hozzárendelést. Az ügyfélhasználat-hozzárendeléssel és annak engedélyezésével kapcsolatos további információkért lásd: [Azure partner-ügyfélhasználati hozzárendelés.](./azure-partner-customer-usage-attribution.md)  |
| Felügyelt lemezek használata | [Felügyelt lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) az alapértelmezett beállítás az Azure-beli IaaS virtuális gépek megőrzött lemezei számára. A megoldássablonokban felügyelt lemezeket kell használnia. <br> <br> 1. Kövesse a felügyelt lemezek azure ARM-sablonokban a felügyelt lemezek használatával [kapcsolatos útmutatást](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) és [mintákat](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) a megoldássablonok frissítéséhez. <br> <br> 2. Kövesse az alábbi utasításokat a felügyelt lemezek alapjául szolgáló virtuális merevlemezek storage-fiókba történő importálásához a virtuális merevlemez képként való közzétételéhez a Marketplace-en: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [parancssori felület](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, 

- [További információ](https://azuremarketplace.microsoft.com/sell) a piactérről.

A Partnerközpontban való regisztrációhoz kezdjen új ajánlatot létrehozni, vagy dolgozzon egy meglévőn:

- [Jelentkezzen be a Partnerközpontba](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) az ajánlat létrehozásához vagy teljesítéséhez.
- További információkért tekintse [meg az Azure-alkalmazásajánlat létrehozása](./partner-center-portal/create-new-azure-apps-offer.md) című témakört.

