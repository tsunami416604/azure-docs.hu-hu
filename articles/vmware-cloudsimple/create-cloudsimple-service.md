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
ms.openlocfilehash: 99194c42dbc6ef07301be517021bf0fb4b4e7c23
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173507"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Az Azure VMware-megoldás létrehozása a CloudSimple szolgáltatással

Az Azure VMware-megoldás CloudSimple való megkezdéséhez hozza létre az Azure VMware-megoldást a CloudSimple szolgáltatással a Azure Portal.

> [!IMPORTANT]
> A CloudSimple szolgáltatás létrehozása előtt regisztrálnia kell a Microsoft. VMwareCloudSimple erőforrás-szolgáltatót az Azure-előfizetésében. Kövesse az Azure- [előfizetéshez tartozó Microsoft. VMwareCloudSimple erőforrás-szolgáltató engedélyezése](enable-cloudsimple-service.md)című témakör lépéseit.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá a **CloudSimple Services**kifejezésre.
    ![Search CloudSimple szolgáltatás @ no__t-1
3. Válassza a **CloudSimple Services**elemet.
4. Új szolgáltatás létrehozásához kattintson a **Hozzáadás** gombra.
    ![Add CloudSimple szolgáltatás @ no__t-1
5. Válassza ki azt az előfizetést, amelyben létre szeretné hozni a CloudSimple szolgáltatást.
6. Válassza ki a szolgáltatáshoz tartozó erőforráscsoportot. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása**lehetőségre.
7. A szolgáltatás azonosításához adja meg a nevet.
8. Adja meg a CIDR a Service Gateway számára. Olyan/28 alhálózatot ad meg, amely nem fedi át a helyszíni alhálózatok, az Azure-alhálózatok vagy a tervezett CloudSimple alhálózatok egyikét sem. A CIDR nem módosítható a szolgáltatás létrehozása után.

    ![A CloudSimple szolgáltatás létrehozása](media/create-cloudsimple-service.png)
9. Kattintson az **OK** gombra.

Létrejön a szolgáltatás, és hozzá lesz adva a szolgáltatások listájához.

## <a name="next-steps"></a>Következő lépések

* További információ a [csomópontok](create-nodes.md) kiépítéséről
* Megtudhatja, hogyan [hozhat létre saját felhőt](create-private-cloud.md)
* Ismerje meg, hogyan [konfigurálhatja a saját felhőalapú környezetét](quickstart-create-private-cloud.md)
