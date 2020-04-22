---
title: Az Azure Applications felügyelt alkalmazásajánlat-közzétételi útmutatója
description: Ez a cikk a felügyelt alkalmazások marketplace-en való közzétételének követelményeit ismerteti
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 56281ff8faba6b4b950334e2b0018d48c8e7aeb3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687566"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure-alkalmazások: Felügyelt alkalmazásajánlat közzétételi útmutatója

A felügyelt alkalmazások a megoldások marketplace-en való közzétételének egyik fő módja. Ebben az útmutatóban megismerheti az ajánlat követelményeit. 

Ezek olyan tranzakciós ajánlatok, amelyek a Marketplace-en keresztül vannak telepítve és számlázva. A felhasználó által látott cselekvésre való felhívás a "Get It Now" (Get It Now) (Get It Now) (Get It Now) (Get It Now) (Get It Now) (Get It Now)

Használja az Azure-alkalmazást: felügyelt alkalmazásajánlat típusa, ha a következő feltételek szükségesek:
- Vagy egy előfizetés-alapú megoldást telepít az ügyfél számára egy virtuális gép vagy egy teljes IaaS-alapú megoldás használatával.
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
>A felhőszolgáltatók (CSP) partnercsatorna-opt-in már elérhető.  Az ajánlat microsoftos CSP-partnercsatornákon keresztül történő marketingről további információt a [felhőszolgáltatók](./cloud-solution-providers.md) ban talál.

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, 

- [További információ](https://azuremarketplace.microsoft.com/sell) a piactérről.

A Partnerközpontban való regisztrációhoz kezdjen új ajánlatot létrehozni, vagy dolgozzon egy meglévőn:

- [Jelentkezzen be a Partnerközpontba](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) az ajánlat létrehozásához vagy teljesítéséhez.
- További információkért tekintse [meg az Azure-alkalmazásajánlat létrehozása](./partner-center-portal/create-new-azure-apps-offer.md) című témakört.
