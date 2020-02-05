---
title: Azure VMware Solutions (AVS) – hozzáférés vSphere-ügyfélhez
description: Leírja, hogyan érheti el a vCenter az AVS Private Cloud-ból.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022664"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>Hozzáférés az AVS Private Cloud vCenter-portálhoz

Az AVS Private Cloud vCenter-portált Azure Portal vagy AVS portálról indíthatja el. a vCenter-portál lehetővé teszi a VMware-infrastruktúra kezelését az AVS Private-felhőben.

## <a name="before-you-begin"></a>Előzetes teendők

Meg kell teremteni a hálózati kapcsolatokat, és engedélyezni kell a DNS-névfeloldást a vCenter-portálhoz való hozzáféréshez. Az alábbi lehetőségek bármelyikével létrehozhat hálózati kapcsolatot az AVS Private-felhőhöz.

* [Kapcsolódás a helyszínről az AVS-hez a ExpressRoute használatával](on-premises-connection.md)
* [VPN-kapcsolat konfigurálása az AVS Private Cloud-hoz](set-up-vpn.md)

Az AVS Private Cloud VMware Infrastructure Components DNS-névfeloldás beállításával kapcsolatban lásd: [DNS beállítása névfeloldáshoz az AVS Private Cloud vCenter hozzáférés a helyszíni munkaállomásokról](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-vcenter-from-azure-portal"></a>VCenter elérése a Azure Portalból

Az AVS Private Cloud vCenter-portált elindíthatja Azure Portalról.

1. Válassza az **Összes szolgáltatás** elemet.

2. Keressen rá az **AVS-szolgáltatások**kifejezésre.

3. Válassza ki annak az AVS-felhőnek az AVS-szolgáltatását, amelyhez csatlakozni szeretne.

4. Az **Áttekintés** lapon kattintson a **VMware AVS privát felhők megtekintése** elemre.

    ![Az AVS szolgáltatás áttekintése](media/cloudsimple-service-overview.png)

5. Válassza ki az AVS Private Cloud-t az AVS privát felhők listájából, és kattintson az **vSphere-ügyfél indítása**lehetőségre.

    ![VSphere-ügyfél elindítása](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>VCenter elérése az AVS-portálról

Az AVS Private Cloud vCenter-portált az AVS Portalról indíthatja el.

1. Hozzáférés az [AVS-portálhoz](access-cloudsimple-portal.md).

2. Az **erőforrások** közül válassza ki azt az AVS Private-felhőt, amelyet szeretne elérni, és kattintson a **vSphere-ügyfél indítása**lehetőségre.

    ![VSphere-ügyfél elindítása – erőforrások](media/cloudsimple-portal-resources-launch-vcenter.png)

3. A vCenter-portált az AVS Private Cloud összefoglaló képernyőjéről is elindíthatja.

    ![VSphere-ügyfél indítása – összefoglalás](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Következő lépések

* [VLAN-ok/alhálózatok létrehozása és kezelése az AVS privát Felhőkhöz](create-vlan-subnet.md)
* [Az AVS Private Cloud engedélyezési modellje VMware vCenter](learn-private-cloud-permissions.md)