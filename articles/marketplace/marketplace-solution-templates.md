---
title: Közzétételi útmutató az Azure-alkalmazások megoldási sablonjaihoz – Azure Marketplace
description: Ez a cikk ismerteti az Azure Marketplace-en elérhető megoldási sablonok közzétételének követelményeit.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 4d395aa68c579b8e1271f4225ba57fa5f9932246
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963784"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Közzétételi útmutató az Azure Applications megoldás sablonjának ajánlatai

Ez a cikk ismerteti a megoldási sablonokkal kapcsolatos ajánlatok közzétételének követelményeit, amely az Azure-beli alkalmazások Azure piactéren való közzétételének egyik módja. A megoldási sablon típusa [Azure Resource Manager sablon (ARM-sablon)](../azure-resource-manager/templates/overview.md) szükséges a megoldás-infrastruktúra automatikus telepítéséhez.

Használja az Azure Application *Solution-sablon* ajánlat típusát a következő feltételek teljesülése esetén:

- A megoldás további üzembe helyezést és konfigurációs automatizálást igényel egy virtuális gépen (VM) túl, például virtuális gépek, hálózati és tárolási erőforrások kombinációja mellett.
- Ügyfelei saját maguk fogja kezelni a megoldást.

A művelet meghívása, amelyet az ügyfél az ajánlat típusának *tekint.*

## <a name="requirements-for-solution-template-offers"></a>A megoldási sablonok ajánlatának követelményei

| **Követelmények** | **Részletek**  |
| ---------------  | -----------  |
|Számlázás és mérés    |  A megoldási sablon által kínált ajánlatok nem tranzakciós ajánlatok, de használhatók a Microsoft kereskedelmi Piactéren keresztül számlázott fizetős virtuális gépek üzembe helyezésére is. A megoldás ARM-sablonja által üzembe helyezett erőforrások az ügyfél Azure-előfizetésében vannak beállítva. Az utólagos elszámolású virtuális gépeket a Microsoft és az ügyfél Azure-előfizetése alapján számlázzák az ügyfélen keresztül.<br/> A saját licencek (BYOL) számlázásához, bár a Microsoft az ügyfél-előfizetésben felmerülő infrastrukturális költségeket számláz, a szoftveres licencelési díjakat közvetlenül a vásárlónak kell átirányítani.   |
|Azure-kompatibilis virtuális merevlemez (VHD)  |   A virtuális gépeket Windows vagy Linux rendszerre kell építeni. További információkért lásd: <ul> <li>[Azure-beli alkalmazás-ajánlat létrehozása](./partner-center-portal/create-new-azure-apps-offer.md) (Windows rendszerű virtuális merevlemezekhez).</li><li>Az Azure-ban [támogatott Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md) (linuxos virtuális merevlemezek esetén).</li></ul> |
| Ügyfelek általi használat nyomon követése | Az Azure Marketplace-en közzétett összes megoldási sablonban engedélyezni kell az ügyfél-használati jóváírást. További információ az ügyfél-használati feladatokról és annak engedélyezéséről: az [Azure-partneri ügyfél használati](./azure-partner-customer-usage-attribution.md)feladatának megállapítása.  |
| Felügyelt lemezek használata | A [Managed Disks](../virtual-machines/windows/managed-disks-overview.md) az alapértelmezett beállítás az Azure-beli infrastruktúra-(IaaS-) virtuális gépek megőrzött lemezei számára. A megoldási sablonokban felügyelt lemezeket kell használnia. <ul><li>A megoldási sablonok frissítéséhez kövesse a [felügyelt lemezek használata Azure Resource Manager-sablonokban](../virtual-machines/windows/using-managed-disks-template-deployments.md)című témakör útmutatását, és használja a megadott [mintákat](https://github.com/Azure/azure-quickstart-templates).<br><br> </li><li>Ha a virtuális merevlemezt képként szeretné közzétenni az Azure Marketplace-en, a következő módszerek egyikével importálja a felügyelt lemezek mögöttes VHD-jét egy Storage-fiókba:<ul><li>[Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md) </li> <li> [Az Azure CLI](../virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md) </li> </ul></ul> |

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, Ismerje meg, hogyan [növelheti Felhőbeli üzletét az Azure Marketplace-szel](https://azuremarketplace.microsoft.com/sell).

A partner Centerben való regisztráció és a használat megkezdése:

- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
- További információért lásd [Az Azure-alkalmazások létrehozásáról szóló ajánlatot](./partner-center-portal/create-new-azure-apps-offer.md) .
