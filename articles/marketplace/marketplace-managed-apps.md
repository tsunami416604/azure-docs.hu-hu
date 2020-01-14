---
title: Azure-alkalmazások által felügyelt alkalmazás-ajánlat – közzétételi útmutató
description: Ez a cikk a felügyelt alkalmazások piactéren való közzétételének követelményeit ismerteti
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: MaggiePucciEvans
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/14/2018
ms.author: evansma
ms.openlocfilehash: 764212ac148b336b07d29c29a72314c5d889d47c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934653"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure-alkalmazások: felügyelt alkalmazás ajánlatának közzétételi útmutatója

A felügyelt alkalmazások egyike a megoldásnak a piactéren való közzétételének egyik fő módja. Az útmutató segítségével megismerheti az ajánlat követelményeit. 

Ezek olyan tranzakciós ajánlatok, amelyek üzembe helyezése és számlázása a piactéren történik. A felhasználó által megjelenő művelet hívása "Letöltés most".

Használja az Azure-alkalmazást: felügyelt alkalmazás-ajánlat típusa, ha a következő feltételek szükségesek:
- Az ügyfél előfizetésen alapuló megoldását egy virtuális gép vagy egy teljes IaaS-alapú megoldás használatával helyezheti üzembe.
- Ön vagy az ügyfele megköveteli, hogy a megoldást egy partner felügyelje.

>[!NOTE]
>Előfordulhat például, hogy egy partner egy SI vagy egy felügyelt szolgáltató (MSP).  

## <a name="managed-application-offer"></a>Felügyelt alkalmazás ajánlata

|Követelmények |Részletek  |
|---------|---------|
|Az ügyfél Azure-előfizetéséhez van telepítve | A felügyelt alkalmazásokat az ügyfél előfizetésében kell telepíteni, és egy harmadik fél is felügyelheti. | 
|Számlázás és mérés    |  Az erőforrások az ügyfél Azure-előfizetésében lesznek kiépítve. Az utólagos elszámolású (ÁTTÉRÉSHEZ) virtuális gépeket a Microsofton keresztül, az ügyfél Azure-előfizetésével (ÁTTÉRÉSHEZ) számoljuk el. <br> A saját licenc használata esetén, míg a Microsoft az ügyfél-előfizetésben felmerülő infrastrukturális költségeket számlázza, a szoftveres licencelési díjakat közvetlenül az ügyfélnek kell átirányítani.        |
|Azure-kompatibilis virtuális merevlemez (VHD)    |   A virtuális gépeket Windows vagy Linux rendszerre kell építeni.<ul> <ul> <li>A linuxos virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Az Azure-ban támogatott Linux-disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>A Windows virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Azure-kompatibilis virtuális merevlemez létrehozása](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> A felügyelt alkalmazásokat a piactéren keresztül kell üzembe helyezni. Ha az ügyfél kommunikációja aggodalomra ad okot, az érdeklődők megosztásának engedélyezése után érdemes megszólítani az érdeklődőket.  

>[!Note]
>A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető.  Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](./cloud-solution-providers.md) című témakört.

## <a name="next-steps"></a>Következő lépések
Ha még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a piactéren.

Ha regisztrálva van, és új ajánlatot hoz létre, vagy dolgozik egy meglévőn,

- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a Cloud Partner Portalba](https://cloudpartner.azure.com) .
