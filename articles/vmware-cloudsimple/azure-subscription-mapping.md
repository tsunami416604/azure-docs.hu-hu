---
title: Erőforráskészletek CloudSimple Azure VMware-megoldások az Azure-előfizetés leképezése
description: Ismerteti, hogyan lehet az Azure VMware megoldás által CloudSimple erőforráskészlet leképezése az Azure-előfizetés
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332986"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Térkép erőforráskészletek a privát felhőben az Azure-előfizetéshez

Azure-előfizetés leképezés leképezése az Azure-előfizetéshez a Magánfelhő vcenter erőforráskészletek teszi lehetővé. Csak az előfizetés, amelyben létrehozta a CloudSimple szolgáltatás leképezheti.  VMware virtuális gép létrehozása az Azure Portalról helyez üzembe a virtuális gép erőforrás-készletben.  A CloudSimple portálon megtekintheti és kezelheti a Magánfelhők létrehozása Azure-előfizetésével.

Több vCenter erőforráskészletek magánfelhő előfizetést is le lehet képezni.  Képezze le az egyes magánfelhőkhöz erőforráskészletek kell.  Csak az erőforrás-készleteket a VMware virtuális gép létrehozása az Azure Portalon elérhető lesz.

> [!IMPORTANT]
> Erőforráskészlet leképezési is gyermek erőforráskészletekhez képezi le. Szülő erőforráskészlet nem rendelhető hozzá, ha bármely gyermek erőforráskészletek van már hozzá van rendelve.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy az egy CloudSimple szolgáltatás és a magánfelhő az előfizetésében.  Egy CloudSimple szolgáltatás létrehozásához lásd: [rövid útmutató – szolgáltatás létrehozása](quickstart-create-cloudsimple-service.md).  Ha a magánfelhő létrehozása van szüksége, tekintse meg [rövid útmutató – konfigurálja a magánfelhő-környezethez](quickstart-create-private-cloud.md).

A vCenter-fürt (legfelső szintű erőforráskészlet) leképezheti az előfizetéshez.  Ha szeretne létrehozni egy új erőforrás-készletet, lásd: [erőforráskészlet létrehozása](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) a cikk a VMware dokumentációjában helyen.

## <a name="default-resource-group"></a>Alapértelmezett erőforráscsoportot

CloudSimple új virtuális gép létrehozása az Azure Portalról lehetővé teszi, hogy válassza ki az erőforráscsoportot.  Egy csatlakoztatott erőforráskészlet magánfelhő vcenter létrehozott virtuális gép az Azure Portalon látható lesz.  A felderített virtuális gépen az Azure alapértelmezett erőforráscsoportot kerülnek.  Módosíthatja az alapértelmezett erőforráscsoport nevét.

## <a name="map-azure-subscription"></a>Képezze le az Azure-előfizetés

1. Hozzáférés a [CloudSimple portál](access-cloudsimple-portal.md).

2. Nyissa meg a **erőforrások** lapon, és válassza ki azt a magánfelhőt, amely hozzá kívánja rendelni.

3. Válassza ki **leképezése az Azure-előfizetések**.

4. Kattintson a **szerkesztése az Azure-előfizetés leképezés**.

5. Elérhető erőforráskészletek leképezni, válassza ki azokat a bal oldalon, és kattintson a jobbra mutató nyílra.

6. Hozzárendelések eltávolításához jelölje ki azokat a jobb oldalon, és kattintson a balra mutató nyílra.

    ![Azure-előfizetések](media/resources-azure-mapping.png)

7. Kattintson az **OK** gombra.

## <a name="change-default-resource-group-name"></a>Változás alapértelmezett erőforráscsoport-név

1. Hozzáférés a [CloudSimple portál](access-cloudsimple-portal.md).

2. Nyissa meg a **erőforrások** lapon, és válassza ki azt a magánfelhőt, amely hozzá kívánja rendelni.

3. Válassza ki **leképezése az Azure-előfizetések**.

4. Kattintson a **szerkesztése** alatt az Azure erőforráscsoport-nevet.

    ![Erőforráscsoport-név szerkesztése](media/resources-edit-resource-group-name.png)

5. Adjon meg egy új nevet az erőforráscsoport, és kattintson a **küldés**.

    ![Adja meg az új erőforráscsoport neve](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>További lépések

* [Az Azure-ban a VMware virtuális gépeket felhasználására](quickstart-create-vmware-virtual-machine.md)
* Tudjon meg többet [CloudSimple virtuális gépek](cloudsimple-virtual-machines.md)