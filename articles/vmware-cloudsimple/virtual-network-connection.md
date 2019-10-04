---
title: Azure-beli virtuális hálózat összekötése a CloudSimple a ExpressRoute használatával
description: Útmutató az Azure-beli virtuális hálózat és a CloudSimple-környezet közötti kapcsolathoz kapcsolódó információk megszerzéséhez
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00d49d763dedc5d86557dadd10f5d727e7893dbe
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563057"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Azure-beli virtuális hálózat összekötése a CloudSimple a ExpressRoute használatával

Saját felhőalapú hálózatát kiterjesztheti az Azure-beli virtuális hálózatra és az Azure-erőforrásokra. Az ExpressRoute-kapcsolat lehetővé teszi, hogy hozzáférjen az Azure-előfizetésében futó erőforrásokhoz a privát felhőből.

## <a name="request-authorization-key"></a>Kérelem engedélyezési kulcsa

A privát felhő és az Azure virtuális hálózat közötti ExpressRoute-kapcsolathoz engedélyezési kulcs szükséges. A kulcs beszerzéséhez egy <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatási</a>jegyet kell beszereznie.  A kérelemben használja a következő információkat:

* Probléma típusa: **Technikai**
* Előfizetés: **Válassza ki azt az előfizetést, ahol a CloudSimple szolgáltatás telepítve van**
* Szolgáltatás: **VMware-megoldás CloudSimple szerint**
* Probléma típusa: **Szolgáltatási kérelem**
* Probléma altípusa: **Az Azure VNET-kapcsolatok engedélyezési kulcsa**
* Tárgy: **Az Azure VNET-kapcsolatok engedélyezési kulcsának kérése**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>Az Azure Virtual Network CloudSimple-hez való kapcsolódási információinak beszerzése

A kapcsolat beállításához létre kell hoznia egy kapcsolatot az Azure Virtual Network és a CloudSimple-környezet között.  Az eljárás részeként meg kell adnia a társ-áramköri URI-t és az engedélyezési kulcsot. Szerezze be az URI és az engedélyezési kulcsot a [CloudSimple portálról](access-cloudsimple-portal.md).  Válassza a **hálózat** lehetőséget az oldalsó menüben, majd válassza az **Azure hálózati kapcsolatok**lehetőséget. Vagy válassza a **fiók** lehetőséget az oldalsó menüben, majd válassza az **Azure hálózati kapcsolatok**lehetőséget.

Figyelje meg a partneri kör URI azonosítójának és az egyes régiók engedélyezési kulcsának másolási ikonjait. Minden olyan privát felhőhöz, amelyhez csatlakozni szeretne:

* Az URI másolásához kattintson a **Másolás** gombra. Illessze be egy fájlba, ahol elérhető lehet a Azure Portalhoz való hozzáadáshoz.  
* A **Másolás** gombra kattintva másolja az engedélyezési kulcsot, és illessze be a fájlba.

![Virtual Network a kapcsolatok lapja](media/network-virt-conn-page.png)

Az Azure-beli virtuális hálózat CloudSimple való beállításával kapcsolatos további információkért lásd: a [CloudSimple saját felhőalapú környezetének csatlakoztatása az Azure Virtual networkhez a ExpressRoute használatával](azure-expressroute-connection.md).
