---
title: Azure-előfizetés leképezése Azure VMware-Megoldásbeli erőforráskészlet-CloudSimple
description: Leírja, hogyan képezhető le egy erőforráskészlet az Azure VMware-megoldásban az Azure-előfizetéshez CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bf721f35500d2ff1344996e7750c5e574f40f31
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816263"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Erőforráskészlet hozzárendelése saját felhőből az Azure-előfizetésbe

Az Azure-előfizetések leképezése lehetővé teszi az erőforráskészletek leképezését a saját felhőalapú vCenter az Azure-előfizetésre. Csak azt az előfizetést tudja leképezni, amelybe létrehozta a CloudSimple szolgáltatást.  Ha a Azure Portal VMware virtuális gépet hoz létre, a leképezett erőforráskészletben telepíti a virtuális gépet.  A CloudSimple-portálon megtekintheti és kezelheti a privát Felhőkhöz tartozó Azure-előfizetést.

Egy előfizetés több vCenter erőforrás-készlethez is hozzárendelhető egy privát felhőben.  Minden egyes privát felhőhöz le kell képeznie az erőforrás-készleteket.  Csak a leképezett erőforráskészlet lesz elérhető egy VMware virtuális gép létrehozásához a Azure Portalból.

> [!IMPORTANT]
> Az erőforráskészlet leképezése szintén leképezi az összes alárendelt erőforrás-készletet. A szülő erőforráskészlet nem képezhető le, ha a gyermek-erőforrások készlete már le van képezve.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy CloudSimple szolgáltatással és saját felhővel az előfizetésében.  CloudSimple-szolgáltatás létrehozásához tekintse meg a rövid útmutató [– szolgáltatás létrehozása](quickstart-create-cloudsimple-service.md)című témakört.  Ha létre kell hoznia egy privát felhőt, lásd: rövid útmutató [– privát felhőalapú környezet konfigurálása](quickstart-create-private-cloud.md).

Az előfizetéshez hozzárendelheti a vCenter-fürtöt (gyökérszintű erőforráskészlet).  Ha új erőforráskészletet szeretne létrehozni, olvassa el a következő cikket: [erőforráskészlet létrehozása](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) a VMware dokumentációs webhelyen.

## <a name="default-resource-group"></a>Alapértelmezett erőforráscsoport

Új CloudSimple virtuális gép Azure Portalból való létrehozása lehetővé teszi az erőforráscsoport kiválasztását.  A leképezett erőforráskészlet saját felhőalapú vCenter létrehozott virtuális gépek Azure Portal láthatók lesznek.  A felderített virtuális gép az alapértelmezett Azure-erőforráscsoporthoz kerül.  Módosíthatja az alapértelmezett erőforráscsoport nevét.

## <a name="map-azure-subscription"></a>Azure-előfizetés leképezése

1. Nyissa meg a [CloudSimple portált](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot, és válassza ki a leképezni kívánt privát felhőt.

3. Válassza az **Azure**-előfizetések leképezése elemet.

4. Kattintson az Azure-előfizetések **leképezésének szerkesztése**elemre.

5. Az elérhető erőforráskészlet hozzárendeléséhez jelölje ki őket a bal oldalon, majd kattintson a jobbra mutató nyílra.

6. A leképezések eltávolításához jelölje ki őket a jobb oldalon, majd kattintson a balra mutató nyílra.

    ![Azure-előfizetések](media/resources-azure-mapping.png)

7. Kattintson az **OK** gombra.

## <a name="change-default-resource-group-name"></a>Alapértelmezett erőforráscsoport nevének módosítása

1. Nyissa meg a [CloudSimple portált](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot, és válassza ki a leképezni kívánt privát felhőt.

3. Válassza az **Azure**-előfizetések leképezése elemet.

4. Kattintson az Azure-erőforráscsoport neve alatt az **Edit (Szerkesztés** ) elemre.

    ![Erőforráscsoport nevének szerkesztése](media/resources-edit-resource-group-name.png)

5. Adja meg az erőforráscsoport új nevét, majd kattintson a **Submit (Küldés**) gombra.

    ![Adja meg az új erőforráscsoport nevét](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>További lépések

* [VMware virtuális gépek használata az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ a [CloudSimple Virtual Machines](cloudsimple-virtual-machines.md) szolgáltatásról