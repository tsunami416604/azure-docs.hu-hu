---
title: Azure Applications – felügyelt alkalmazás-ajánlat – közzétételi útmutató | Azure piactér
description: Ez a cikk ismerteti a felügyelt alkalmazások Azure Marketplace-en való közzétételének követelményeit.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084872"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Azure-alkalmazások: felügyelt alkalmazások ajánlatának közzétételi követelményei

Ez a cikk ismerteti a felügyelt alkalmazás-ajánlat típusának követelményeit, amely az Azure-beli alkalmazások ajánlatának az Azure Marketplace-en való közzétételének egyik módja. A felügyelt alkalmazások az Azure Marketplace-en üzembe helyezett és számlázható Transact-ajánlatok. A felhasználó által megjelenő művelet hívása "Letöltés most".

Ha a következő feltételek szükségesek, használja a felügyelt alkalmazás ajánlatának típusát:

- Egy előfizetésen alapuló megoldást helyez üzembe az ügyfél számára vagy egy virtuális gépet vagy egy teljes IaaS-alapú megoldást.
- Ön vagy az ügyfele megköveteli, hogy a megoldást egy partner felügyelje.

>[!NOTE]
>Előfordulhat például, hogy egy partner egy SI vagy egy felügyelt szolgáltató (MSP).  

## <a name="managed-application-offer"></a>Felügyelt alkalmazás ajánlata

|Követelmények |Részletek  |
|---------|---------|
|Az ügyfél Azure-előfizetéséhez van telepítve | A felügyelt alkalmazásokat az ügyfél előfizetésében kell telepíteni, és egy harmadik fél is felügyelheti. |
|Számlázás és mérés    |  Az erőforrások az ügyfél Azure-előfizetésében lesznek kiépítve. Az utólagos elszámolású (ÁTTÉRÉSHEZ) virtuális gépeket a Microsofton keresztül, az ügyfél Azure-előfizetésével (ÁTTÉRÉSHEZ) számoljuk el. <br> A saját licenc használata esetén, míg a Microsoft az ügyfél-előfizetésben felmerülő infrastrukturális költségeket számlázza, a szoftveres licencelési díjakat közvetlenül az ügyfélnek kell átirányítani.        |
|Azure-kompatibilis virtuális merevlemez (VHD)    |   A virtuális gépeket Windows vagy Linux rendszerre kell építeni.<ul> <ul> <li>A linuxos virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Az Azure-ban támogatott Linux-disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>A Windows virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Azure-alkalmazási ajánlat létrehozása](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> A felügyelt alkalmazásokat a piactéren keresztül kell üzembe helyezni. Ha az ügyfél kommunikációja aggodalomra ad okot, az érdeklődők megosztásának engedélyezése után érdemes megszólítani az érdeklődőket.  

>[!Note]
>A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető. Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért lásd: [felhőalapú megoldások szolgáltatói](./cloud-solution-providers.md).

## <a name="next-steps"></a>További lépések

- Ha még nem tette meg, [Ismerkedjen meg](https://azuremarketplace.microsoft.com/sell) az Azure piactérrel.
- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
- További információért [hozzon létre egy Azure](./partner-center-portal/create-new-azure-apps-offer.md) -beli alkalmazás-ajánlatot.
