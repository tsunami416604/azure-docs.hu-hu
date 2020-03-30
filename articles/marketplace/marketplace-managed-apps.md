---
title: Az Azure Applications felügyelt alkalmazásajánlat-közzétételi útmutatója
description: Ez a cikk a felügyelt alkalmazások marketplace-en való közzétételének követelményeit ismerteti
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: dsindona
ms.openlocfilehash: 38323ecfee69460b16542a3e004513f6a7389c41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288410"
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
|Azure-kompatibilis virtuális merevlemez (VHD)    |   A virtuális gépeknek Windows vagy Linux rendszerre kell épülniük.<ul> <ul> <li>A Linux virtuális merevlemez ek létrehozásáról további információt az [Azure-ban jóváhagyott Linux-disztribúciók című](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)témakörben talál.</li> <li>A Windows virtuális merevlemez ek létrehozásáról további információt az [Azure-kompatibilis virtuális merevlemez létrehozása](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)című témakörben talál.</li> </ul> |

>[!NOTE]
> A felügyelt alkalmazásoknak a Marketplace-en keresztül telepíthetőnek kell lenniük. Ha az ügyfelek kommunikációja aggodalomra ad okot, akkor az érdeklődő ügyfelekkel kell kapcsolatba lépnie, miután engedélyezte az érdeklődők megosztását.  

>[!Note]
>A felhőszolgáltatók (CSP) partnercsatorna-opt-in már elérhető.  Az ajánlat microsoftos CSP-partnercsatornákon keresztül történő marketingről további információt a [felhőszolgáltatók](./cloud-solution-providers.md) ban talál.

## <a name="next-steps"></a>További lépések
Ha még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a piacon.

Ha regisztrált, és új ajánlatot hoz létre, vagy egy meglévőn dolgozik,

- [Jelentkezzen be a Cloud Partner Portal-ba az](https://cloudpartner.azure.com) ajánlat létrehozásához vagy befejezéséhez.
