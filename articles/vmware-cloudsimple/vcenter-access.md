---
title: Azure VMware-megoldás a CloudSimple által – Access vSphere-ügyfél
description: Ez a témakör azt ismerteti, hogyan érheti el a privát felhő vCenterét.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022664"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>A Privát felhőbeli vCenter-portál elérése

Elindíthatja a Private Cloud vCenter portál az Azure Portalon vagy a CloudSimple portálon.  A vCenter portál lehetővé teszi a VMware-infrastruktúra kezelését a privát felhőben.

## <a name="before-you-begin"></a>Előkészületek

A hálózati kapcsolatot létre kell hozni, és a DNS-névfeloldást engedélyezni kell a vCenter-portál eléréséhez.  Az alábbi lehetőségek bármelyikével hálózati kapcsolatot létesíthet a magánfelhővel.

* [Csatlakozás a helyszíni és a CloudSimple szolgáltatáshoz az ExpressRoute használatával](on-premises-connection.md)
* [VPN-kapcsolat konfigurálása a CloudSimple private cloudszolgáltatással](set-up-vpn.md)

A Private Cloud VMware infrastruktúra-összetevőinek DNS-névfeloldásának beállításához olvassa el a [DNS-hozzáférés konfigurálása a magánfelhő-vCenter-hozzáférés névfeloldása a helyszíni munkaállomásokról című témakört.](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="access-vcenter-from-azure-portal"></a>A vCenter elérése az Azure Portalról

Elindíthatja a private cloud vCenter-portálját az Azure Portalról.

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg a **CloudSimple szolgáltatásokat.**

3. Válassza ki a private cloud felhőszolgáltatását, amelyhez csatlakozni szeretne.

4. Az **Áttekintés** lapon kattintson a **VMware privát felhők megtekintése elemre.**

    ![CloudSimple szolgáltatás – áttekintés](media/cloudsimple-service-overview.png)

5. Válassza ki a magánfelhőt a privát felhők listájából, és kattintson a **VSphere-ügyfél indítása gombra**.

    ![VSphere kliens indítása](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>A vCenter elérése a CloudSimple portálról

Elindíthatja a private cloud vCenter portálját a CloudSimple portálról.

1. A [CloudSimple portál](access-cloudsimple-portal.md)elérése.

2. Az **Erőforrások** közül válassza ki a magánfelhőt, amelyet el szeretne érni, és kattintson a **VSphere-ügyfél indítása gombra.**

    ![VSphere client indítása - Erőforrások](media/cloudsimple-portal-resources-launch-vcenter.png)

3. A vCenter-portált a privát felhő összefoglaló képernyőjéről is elindíthatja.

    ![VSphere client indítása - Összefoglaló](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>További lépések

* [VLAN-ok/alhálózatok létrehozása és kezelése a privát felhők számára](create-vlan-subnet.md)
* [A VMware vCenter CloudSimple magánfelhő-engedélyezési modellje](learn-private-cloud-permissions.md)