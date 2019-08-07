---
title: Azure VMware-megoldás létrehozása a CloudSimple-Service használatával
description: Útmutató a CloudSimple szolgáltatás létrehozásához a Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6986e0a7e6eee6dbbd43c72a415b01df7da7da51
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812447"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Azure VMware-megoldás létrehozása a CloudSimple-Service használatával

Az Azure VMware-megoldás CloudSimple való megkezdéséhez hozza létre az Azure VMware-megoldást a CloudSimple szolgáltatással a Azure Portal.

## <a name="before-you-begin"></a>Előkészületek

Egy/28 CIDR-blokk lefoglalása az átjáró-alhálózathoz.  Az átjáró-alhálózatok CloudSimple szolgáltatáshoz szükségesek, és egyediek ahhoz a régióhoz, amelyben létrehozták. Az átjáró-alhálózat az Edge hálózati szolgáltatások esetében használatos, és egy/28 CIDR blokkot igényel. Az átjáró alhálózatának címtartomány egyedinek kell lennie. Nem lehet átfedésben a CloudSimple-környezettel kommunikáló hálózattal.  A CloudSimple kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.

2. Keressen rá a **CloudSimple Services**kifejezésre.

    ![Search CloudSimple Service](media/create-cloudsimple-service-search.png)

3. Válassza a **CloudSimple Services**elemet.

4. Új szolgáltatás létrehozásához kattintson a **Hozzáadás** gombra.

    ![CloudSimple szolgáltatás hozzáadása](media/create-cloudsimple-service-add.png)

5. Válassza ki azt az előfizetést, amelyben létre szeretné hozni a CloudSimple szolgáltatást.

6. Válassza ki a szolgáltatáshoz tartozó erőforráscsoportot. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása**lehetőségre.

7. A szolgáltatás azonosításához adja meg a nevet.

8. Adja meg a CIDR a Service Gateway számára. Olyan/28 alhálózatot ad meg, amely nem fedi át a meglévő alhálózatok egyikét sem.  Ezek közé tartoznak a helyszíni alhálózatok, az Azure-alhálózatok vagy a tervezett CloudSimple alhálózatok. A CIDR nem módosítható a szolgáltatás létrehozása után.

    ![A CloudSimple szolgáltatás létrehozása](media/create-cloudsimple-service.png)

9. Kattintson az **OK** gombra.

Létrejön a szolgáltatás, és hozzá lesz adva a szolgáltatások listájához.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [hozhat létre saját felhőt](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Ismerje meg, hogyan [konfigurálhatja a saját felhőalapú környezetét](quickstart-create-private-cloud.md)
