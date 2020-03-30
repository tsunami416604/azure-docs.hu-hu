---
title: Az Azure virtuális hálózatának csatlakoztatása a CloudSimple szolgáltatáshoz az ExpressRoute használatával – Azure VMware Solution by CloudSimple
description: Ez a témakör azt ismerteti, hogy miként szerezhet be társviszony-létesítési adatokat az Azure virtuális hálózat és a CloudSimple környezet közötti kapcsolathoz
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563990"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Az Azure virtuális hálózatának csatlakoztatása a CloudSimple szolgáltatáshoz az ExpressRoute használatával

Kiterjesztheti a magánfelhő-hálózatot az Azure virtuális hálózatára és az Azure-erőforrásokra. Az ExpressRoute-kapcsolat lehetővé teszi az Azure-előfizetésében futó erőforrások elérését a privát felhőből.

## <a name="request-authorization-key"></a>Engedélyezési kulcs kérése

A magánfelhő és az Azure virtuális hálózat közötti ExpressRoute-kapcsolathoz engedélyezési kulcs szükséges. Kulcs beszerzéséhez nyújtson be egy jegyet <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">a Support programhoz.</a>  Használja a következő információkat a kérelemben:

* Probléma típusa: **Műszaki**
* Előfizetés: **Válassza ki azt az előfizetést, amelyben a CloudSimple szolgáltatás telepítve van**
* Szolgáltatás: **VMware megoldás a CloudSimple-től**
* Probléma típusa: **Szolgáltatáskérés**
* Probléma altípusa: **Engedélyezési kulcs az Azure VNET-kapcsolathoz**
* Tárgy: **Engedélyezési kulcs kérése az Azure VNET-kapcsolathoz**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Társviszony-létesítési információk beszereznie a CloudSimple portálról

A kapcsolat beállításához létre kell hoznia egy kapcsolatot az Azure virtuális hálózat és a CloudSimple környezet között.  Az eljárás részeként meg kell adnia a társáramkör URI-ját és az engedélyezési kulcsot. Szerezze be az URI-t és az engedélyezési kulcsot a [CloudSimple portálról.](access-cloudsimple-portal.md)  Válassza az oldalsó menü **Hálózat** parancsát, majd az **Azure Hálózati kapcsolat**lehetőséget. Vagy válassza az **oldalmenüFiók** parancsát, majd az **Azure hálózati kapcsolat lehetőséget.**

Másolja a társáramkör URI-ját és az egyes régiók engedélyezési kulcsát a *másolásikon* használatával. Minden csatlakoztatni kívánt CloudSimple-régió esetében:

1. Az URI másolásához kattintson a **Másolás** gombra. Illessze be egy fájlba, ahol az Azure Portalon hozzáadhatható.  
2. Kattintson a **Másolás** gombra az engedélyezési kulcs másolásához és beillesztéséhez a fájlba is.

Másolja az engedélyezési kulcsot és a **társáramkör URI-ját,** amely Elérhető állapotban van.  **A használt** állapot azt jelzi, hogy a kulcsot már használták virtuális hálózati kapcsolat létrehozásához.

![Virtuális hálózati kapcsolat lap](media/virtual-network-connection.png)

Az Azure virtuális hálózat CloudSimple kapcsolatra való beállításával kapcsolatos további tudnivalókért olvassa el [a CloudSimple magánfelhő-környezet csatlakoztatása az Azure virtuális hálózathoz az ExpressRoute használatával](azure-expressroute-connection.md)című témakört.

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális hálózati kapcsolata a magánfelhővel](azure-expressroute-connection.md)
* [Csatlakozás helyszíni hálózathoz az Azure ExpressRoute használatával](on-premises-connection.md)
