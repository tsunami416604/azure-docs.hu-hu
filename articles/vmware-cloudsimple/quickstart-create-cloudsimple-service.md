---
title: Azure VMware Solutions (AVS) rövid útmutató – szolgáltatás létrehozása
description: Ismerje meg, hogyan hozhatja létre az AVS szolgáltatást, a megvásárlási csomópontokat és a tartalék csomópontokat
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024432"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>Rövid útmutató – Azure VMware Solutions (AVS) szolgáltatás létrehozása

Első lépésként hozza létre az Azure VMware-megoldásokat (AVS) a Azure Portal.

## <a name="vmware-solutions-avs---service-overview"></a>VMware Solutions (AVS) – szolgáltatás áttekintése

Az AVS szolgáltatás lehetővé teszi, hogy az AVS használatával Azure VMware-megoldást fogyasszon. A szolgáltatás létrehozása lehetővé teszi a csomópontok kiépítését, a csomópontok fenntartását és az AVS privát felhők létrehozását. Adja hozzá az AVS szolgáltatást minden olyan Azure-régióban, ahol az AVS szolgáltatás elérhető. A szolgáltatás az Azure VMware-megoldás peremhálózati hálózatát az AVS használatával határozza meg. Ez a peremhálózati hálózat olyan szolgáltatások esetében használatos, amelyek VPN-t, ExpressRoute és internetkapcsolatot tartalmaznak az AVS privát Felhőkhöz.

Az AVS szolgáltatás hozzáadásához létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat a peremhálózati hálózat létrehozásakor használatos, és egy/28 CIDR blokkot igényel. Az átjáró alhálózatának címtartomány egyedinek kell lennie. A helyszíni hálózati címtartomány vagy az Azure-beli virtuális hálózati címtartomány egyikével sem lehet átfedésben.

## <a name="before-you-begin"></a>Előzetes teendők

Egy/28 CIDR-blokk lefoglalása az átjáró-alhálózathoz. Az átjáró-alhálózatot az AVS szolgáltatásnak kell megadnia, és egyedinek kell lennie abban a régióban, amelyben létrehozták. Az átjáró-alhálózatot az AVS Edge hálózati szolgáltatások használják az Azure VMware-megoldáshoz, és egy/28 CIDR blokkra van szükség. Az átjáró alhálózatának címtartomány egyedinek kell lennie. Nem lehet átfedésben az AVS-környezettel kommunikáló hálózattal. Az AVS-vel kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák.

Tekintse át a [hálózati előfeltételeket](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresés az **AVS szolgáltatásban**.

    ![Az AVS szolgáltatás keresése](media/create-cloudsimple-service-search.png)

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

## <a name="provision-nodes"></a>Csomópontok kiépítése

Az AVS Private Cloud Environment esetében az utólagos elszámolású kapacitás beállításához először a Azure Portal csomópontjait kell kiépíteni.

1. Válassza az **Összes szolgáltatás** elemet.
2. Az **AVS-csomópontok**keresése.

    ![AVS-csomópontok keresése](media/create-cloudsimple-node-search.png)

3. Válassza az **AVS-csomópontok**lehetőséget.
4. Csomópontok létrehozásához kattintson a **Hozzáadás** gombra.

    ![AVS-csomópontok hozzáadása](media/create-cloudsimple-node-add.png)

5. Válassza ki azt az előfizetést, ahol az AVS-csomópontokat szeretné kiépíteni.
6. Válassza ki a csomópontok erőforrás-csoportját. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása**lehetőségre.
7. Adja meg a csomópontok azonosításához szükséges előtagot.
8. Válassza ki a csomópont-erőforrások helyét.
9. Válassza ki a csomópont-erőforrások tárolására szolgáló dedikált helyet.
10. Válassza ki a [csomópont típusát](cloudsimple-node.md).
11. Válassza ki a kiépíteni kívánt csomópontok számát.
12. Válassza a **felülvizsgálat + létrehozás**lehetőséget.
13. Tekintse át a beállításokat. A beállítások módosításához kattintson az **előző**gombra.
14. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>Következő lépések

* [AVS Private Cloud létrehozása és környezet konfigurálása](quickstart-create-private-cloud.md)
* További információ az [AVS szolgáltatásról](https://docs.azure.cloudsimple.com/cloudsimple-service)
