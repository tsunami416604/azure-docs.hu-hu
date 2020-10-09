---
title: Azure-beli virtuális hálózat összekötése a CloudSimple ExpressRoute-Azure VMware-megoldással a CloudSimple használatával
description: Útmutató az Azure-beli virtuális hálózat és a CloudSimple-környezet közötti kapcsolathoz kapcsolódó információk megszerzéséhez
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77563990"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Azure-beli virtuális hálózat összekötése a CloudSimple a ExpressRoute használatával

Saját felhőalapú hálózatát kiterjesztheti az Azure-beli virtuális hálózatra és az Azure-erőforrásokra. Az ExpressRoute-kapcsolat lehetővé teszi, hogy hozzáférjen az Azure-előfizetésében futó erőforrásokhoz a privát felhőből.

## <a name="request-authorization-key"></a>Kérelem engedélyezési kulcsa

A privát felhő és az Azure virtuális hálózat közötti ExpressRoute-kapcsolathoz engedélyezési kulcs szükséges. A kulcs beszerzéséhez egy <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatási</a>jegyet kell beszereznie.  A kérelemben használja a következő információkat:

* Probléma típusa: **technikai**
* Előfizetés: **válassza ki azt az előfizetést, ahol a CloudSimple szolgáltatás telepítve van**
* Szolgáltatás: **VMware-megoldás CloudSimple szerint**
* Probléma típusa: **szolgáltatás kérése**
* Probléma altípusa: **Az Azure VNET-kapcsolatok engedélyezési kulcsa**
* Subject: **engedélyezési kulcs kérése az Azure VNET-kapcsolatban**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Egyenrangú információk beolvasása a CloudSimple-portálról

A kapcsolat beállításához létre kell hoznia egy kapcsolatot az Azure Virtual Network és a CloudSimple-környezet között.  Az eljárás részeként meg kell adnia a társ-áramköri URI-t és az engedélyezési kulcsot. Szerezze be az URI és az engedélyezési kulcsot a [CloudSimple portálról](access-cloudsimple-portal.md).  Válassza a **hálózat** lehetőséget az oldalsó menüben, majd válassza az **Azure hálózati kapcsolatok**lehetőséget. Vagy válassza a **fiók** lehetőséget az oldalsó menüben, majd válassza az **Azure hálózati kapcsolatok**lehetőséget.

Másolja a társ-áramköri URI-t és az egyes régiókhoz tartozó engedélyezési kulcsot a *Másolás* ikon használatával. Minden olyan CloudSimple-régióhoz, amelyhez csatlakozni szeretne:

1. Az URI másolásához kattintson a **Másolás** gombra. Illessze be egy fájlba, ahol elérhető lehet a Azure Portalhoz való hozzáadáshoz.  
2. A **Másolás** gombra kattintva másolja az engedélyezési kulcsot, és illessze be a fájlba.

Másolja ki az **elérhető** állapotban lévő engedélyezési kulcsot és a társ-kör URI azonosítóját.  A **használt** állapot azt jelzi, hogy a kulcs már használatban van egy virtuális hálózati kapcsolatok létrehozásához.

![Virtual Network a kapcsolatok lapja](media/virtual-network-connection.png)

Az Azure-beli virtuális hálózat CloudSimple való beállításával kapcsolatos további információkért lásd: a [CloudSimple saját felhőalapú környezetének csatlakoztatása az Azure Virtual networkhez a ExpressRoute használatával](azure-expressroute-connection.md).

## <a name="next-steps"></a>További lépések

* [Azure-beli virtuális hálózati kapcsolatok a privát felhőhöz](azure-expressroute-connection.md)
* [Kapcsolódás helyszíni hálózathoz az Azure ExpressRoute](on-premises-connection.md)
