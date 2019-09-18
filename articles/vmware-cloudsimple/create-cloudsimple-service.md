---
title: Azure VMware-megoldás a CloudSimple által – CloudSimple szolgáltatás létrehozása
description: Útmutató a CloudSimple szolgáltatás létrehozásához a Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d6d4af28d79b2375e774da98d4fdb1ad9dc22063
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035719"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Az Azure VMware-megoldás létrehozása a CloudSimple szolgáltatással

Az Azure VMware-megoldás CloudSimple való megkezdéséhez hozza létre az Azure VMware-megoldást a CloudSimple szolgáltatással a Azure Portal.

> [!IMPORTANT]
> A CloudSimple szolgáltatás létrehozása előtt regisztrálnia kell a Microsoft. VMwareCloudSimple erőforrás-szolgáltatót az Azure-előfizetésében. Kövesse az Azure- [előfizetéshez tartozó Microsoft. VMwareCloudSimple erőforrás-szolgáltató engedélyezése](enable-cloudsimple-service.md)című témakör lépéseit.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá a **CloudSimple Services**kifejezésre.
    ![CloudSimple szolgáltatás keresése](media/create-cloudsimple-service-search.png)
3. Válassza a **CloudSimple Services**elemet.
4. Új szolgáltatás létrehozásához kattintson a **Hozzáadás** gombra.
    ![CloudSimple szolgáltatás hozzáadása](media/create-cloudsimple-service-add.png)
5. Válassza ki azt az előfizetést, amelyben létre szeretné hozni a CloudSimple szolgáltatást.
6. Válassza ki a szolgáltatáshoz tartozó erőforráscsoportot. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása**lehetőségre.
7. A szolgáltatás azonosításához adja meg a nevet.
8. Adja meg a CIDR a Service Gateway számára. Olyan/28 alhálózatot ad meg, amely nem fedi át a helyszíni alhálózatok, az Azure-alhálózatok vagy a tervezett CloudSimple alhálózatok egyikét sem. A CIDR nem módosítható a szolgáltatás létrehozása után.

    ![A CloudSimple szolgáltatás létrehozása](media/create-cloudsimple-service.png)
9. Kattintson az **OK** gombra.

Létrejön a szolgáltatás, és hozzá lesz adva a szolgáltatások listájához.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [hozhat létre saját felhőt](create-private-cloud.md)
* Ismerje meg, hogyan [konfigurálhatja a saját felhőalapú környezetét](quickstart-create-private-cloud.md)
