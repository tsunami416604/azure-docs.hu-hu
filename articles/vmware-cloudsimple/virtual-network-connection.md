---
title: Az Azure Virtual Network összekötése az AVS-lel a ExpressRoute használatával
description: Útmutató az Azure-beli virtuális hálózat és az AVS-környezet közötti kapcsolathoz kapcsolódó információk megszerzéséhez
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ff11bbafe6f9057ce70c799e0437691d89ccb40
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014402"
---
# <a name="connect-azure-virtual-network-to-avs-using-expressroute"></a>Az Azure Virtual Network összekötése az AVS-lel a ExpressRoute használatával

Az AVS Private Cloud Network az Azure-beli virtuális hálózatra és az Azure-erőforrásokra is kiterjeszthető. Az ExpressRoute-kapcsolat lehetővé teszi az Azure-előfizetésében futó erőforrások elérését az AVS Private Cloud-ból.

## <a name="request-authorization-key"></a>Kérelem engedélyezési kulcsa

Az AVS Private Cloud és az Azure Virtual Network közötti ExpressRoute-kapcsolathoz engedélyezési kulcs szükséges. A kulcs beszerzéséhez egy <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatási</a>jegyet kell beszereznie. A kérelemben használja a következő információkat:

* Probléma típusa: **technikai**
* Előfizetés: **válassza ki azt az előfizetést, amelyen az AVS szolgáltatás telepítve van**
* Szolgáltatás: **VMware-megoldások (AVS)**
* Probléma típusa: **szolgáltatás kérése**
* Probléma altípusa: **Az Azure VNET-kapcsolatok engedélyezési kulcsa**
* Subject: **engedélyezési kulcs kérése az Azure VNET-kapcsolatban**

## <a name="get-peering-information-from-avs-portal"></a>Egyenrangú információk beolvasása az AVS-portálról

A kapcsolat beállításához létre kell hoznia egy kapcsolatot az Azure Virtual Network és az AVS-környezet között. Az eljárás részeként meg kell adnia a társ-áramköri URI-t és az engedélyezési kulcsot. Szerezze be az URI-és engedélyezési kulcsot az [AVS-portálról](access-cloudsimple-portal.md). Válassza a **hálózat** lehetőséget az oldalsó menüben, majd válassza az **Azure hálózati kapcsolatok**lehetőséget. Vagy válassza a **fiók** lehetőséget az oldalsó menüben, majd válassza az **Azure hálózati kapcsolatok**lehetőséget.

Másolja a társ-áramköri URI-t és az egyes régiókhoz tartozó engedélyezési kulcsot a *Másolás* ikon használatával. Minden olyan AVS-régióhoz, amelyhez csatlakozni szeretne:

1. Az URI másolásához kattintson a **Másolás** gombra. Illessze be egy fájlba, ahol elérhető lehet a Azure Portalhoz való hozzáadáshoz. 
2. A **Másolás** gombra kattintva másolja az engedélyezési kulcsot, és illessze be a fájlba.

Másolja ki az **elérhető** állapotban lévő engedélyezési kulcsot és a társ-kör URI azonosítóját. A **használt** állapot azt jelzi, hogy a kulcs már használatban van egy virtuális hálózati kapcsolatok létrehozásához.

![Virtual Network a kapcsolatok lapja](media/virtual-network-connection.png)

Az Azure Virtual Network és az AVS közötti kapcsolat beállításával kapcsolatos további információkért lásd: az [AVS Private Cloud Environment csatlakoztatása az Azure Virtual networkhez a ExpressRoute használatával](azure-expressroute-connection.md).

## <a name="next-steps"></a>Következő lépések

* [Azure-beli virtuális hálózati kapcsolatok az AVS Private Cloud-hoz](azure-expressroute-connection.md)
* [Kapcsolódás helyszíni hálózathoz az Azure ExpressRoute](on-premises-connection.md)
