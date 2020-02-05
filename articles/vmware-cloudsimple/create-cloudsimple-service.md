---
title: Azure VMware Solutions (AVS) – AVS-szolgáltatás létrehozása
description: Útmutató az AVS szolgáltatás létrehozásához a Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024823"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>Az Azure VMware Solutions (AVS) szolgáltatás létrehozása

Az Azure VMware Solutions (AVS) szolgáltatás megkezdéséhez hozza létre az Azure VMware Solutions (AVS) szolgáltatást a Azure Portal.

## <a name="before-you-begin"></a>Előzetes teendők

Foglaljon le egy/28 CIDR-blokkot az átjáró-alhálózathoz. Az átjáró-alhálózatot az AVS szolgáltatásnak kell megadnia, és egyedinek kell lennie abban a régióban, amelyben létrehozták. Az átjáró-alhálózat az Edge hálózati szolgáltatások esetében használatos, és egy/28 CIDR blokkot igényel. Az átjáró alhálózatának címtartomány egyedinek kell lennie. Nem lehet átfedésben az AVS-környezettel kommunikáló hálózattal. Az AVS-vel kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá az **AVS-szolgáltatások**kifejezésre.
    ![keresési AVS-szolgáltatás](media/create-cloudsimple-service-search.png)
3. Válassza az **AVS-szolgáltatások**lehetőséget.
4. Új szolgáltatás létrehozásához kattintson a **Hozzáadás** gombra.
    ![AVS-szolgáltatás hozzáadása](media/create-cloudsimple-service-add.png)
5. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az AVS szolgáltatást.
6. Válassza ki a szolgáltatáshoz tartozó erőforráscsoportot. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása**lehetőségre.
7. A szolgáltatás azonosításához adja meg a nevet.
8. Adja meg a CIDR a Service Gateway számára. Olyan/28 alhálózatot ad meg, amely nem fedi át a helyszíni alhálózatok, az Azure-alhálózatok vagy a tervezett AVS-alhálózatok egyikét sem. A CIDR nem módosítható a szolgáltatás létrehozása után.

    ![Az AVS szolgáltatás létrehozása](media/create-cloudsimple-service.png)
9. Kattintson az **OK** gombra.

Létrejön a szolgáltatás, és hozzá lesz adva a szolgáltatások listájához.

## <a name="next-steps"></a>Következő lépések

* További információ a [csomópontok](create-nodes.md) kiépítéséről
* Megtudhatja, hogyan [hozhat létre egy AVS Private-felhőt](create-private-cloud.md)
* Megtudhatja, hogyan [konfigurálhat egy AVS Private Cloud Environment-környezetet](quickstart-create-private-cloud.md)
