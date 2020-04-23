---
title: Azure-alkalmazások felügyelt alkalmazásajánlat-közzétételi útmutatója | Azure Piactér
description: Ez a cikk ismerteti a felügyelt alkalmazások közzétételének követelményeit az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084872"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Azure Applications: Felügyelt alkalmazásajánlat közzétételi követelmények

Ez a cikk ismerteti a felügyelt alkalmazásajánlat-típus követelményeit, amely az Egyik módja egy Azure-alkalmazásajánlat közzétételének az Azure Piactéren. A felügyelt alkalmazások tranzakciós ajánlatok, amelyek az Azure Marketplace-en keresztül vannak telepítve és számlázva. A felhasználó által látott cselekvésre való felhívás a "Get It Now" (Get It Now) (Get It Now) (Get It Now) (Get It Now) (Get It Now) (Get It Now)

Akkor használja a felügyelt alkalmazásajánlat típusát, ha a következő feltételek szükségesek:

- Egy előfizetés-alapú megoldást üzembe helyez az ügyfél számára egy virtuális gép vagy egy teljes IaaS-alapú megoldás használatával.
- Önnek vagy az ügyfélnek szüksége van arra, hogy a megoldást egy partner irányítsa.

>[!NOTE]
>Lehet például egy partner si vagy felügyelt szolgáltató (MSP).  

## <a name="managed-application-offer"></a>Felügyelt alkalmazásajánlat

|Követelmények |Részletek  |
|---------|---------|
|Üzembe helyezve egy ügyfél Azure-előfizetésében | A felügyelt alkalmazásokat az ügyfél előfizetésében kell telepíteni, és azokat egy harmadik fél is kezelheti. |
|Számlázás és mérés    |  Az erőforrások az ügyfél Azure-előfizetésében lesznek kiépítve. A felosztó-ki(PAYGO) virtuális gépeket a Microsofton keresztül, az ügyfél Azure-előfizetésén (PAYGO) keresztül számlázunk. <br> A saját licenced et elhozva, míg a Microsoft kiszámlázja az ügyfél-előfizetés során felmerülő infrastrukturális költségeket, ön a szoftverlicencelési díjakat közvetlenül az ügyfélnek kell átfizetnie.        |
|Azure-kompatibilis virtuális merevlemez (VHD)    |   A virtuális gépeknek Windows vagy Linux rendszerre kell épülniük.<ul> <ul> <li>A Linux virtuális merevlemez ek létrehozásáról további információt az [Azure-ban jóváhagyott Linux-disztribúciók című](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)témakörben talál.</li> <li>A Windows vHD-s merevlemezek létrehozásáról további információt az [Azure-alkalmazásajánlat létrehozása](./partner-center-portal/create-new-azure-apps-offer.md)című témakörben talál.</li> </ul> |

>[!NOTE]
> A felügyelt alkalmazásoknak a Marketplace-en keresztül telepíthetőnek kell lenniük. Ha az ügyfelek kommunikációja aggodalomra ad okot, akkor az érdeklődő ügyfelekkel kell kapcsolatba lépnie, miután engedélyezte az érdeklődők megosztását.  

>[!Note]
>A felhőszolgáltatók (CSP) partnercsatorna-opt-in már elérhető. Az ajánlat Microsoft CSP partnercsatornákon keresztül történő marketingjéről a [Felhőszolgáltatók](./cloud-solution-providers.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

- Ha még nem tette meg, [ismerje meg](https://azuremarketplace.microsoft.com/sell) az Azure Piactér.
- [Jelentkezzen be a Partnerközpontba](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) az ajánlat létrehozásához vagy teljesítéséhez.
- [Hozzon létre egy Azure-alkalmazás ajánlatot](./partner-center-portal/create-new-azure-apps-offer.md) további információkért.
