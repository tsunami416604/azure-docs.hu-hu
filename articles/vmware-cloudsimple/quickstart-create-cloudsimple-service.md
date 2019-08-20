---
title: Azure VMware-megoldás CloudSimple rövid útmutató – szolgáltatás létrehozása
description: Ismerje meg, hogyan hozhatja létre a CloudSimple szolgáltatást, a megvásárlási csomópontokat és a tartalék csomópontokat
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b20ff261939dd97a74d27f5ec7f21eae2665f474
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574543"
---
# <a name="quickstart---create-cloudsimple-service"></a>Rövid útmutató – CloudSimple szolgáltatás létrehozása

Első lépésként hozza létre az Azure VMware-megoldást a Azure Portal CloudSimple.

A CloudSimple szolgáltatás lehetővé teszi, hogy a CloudSimple az Azure VMware-megoldást használja.  A szolgáltatás létrehozása lehetővé teszi a csomópontok megvásárlását, a csomópontok fenntartását és a privát felhők létrehozását.  Adja hozzá a CloudSimple szolgáltatást minden olyan Azure-régióban, ahol a CloudSimple szolgáltatás elérhető.  A szolgáltatás a CloudSimple által definiált Azure VMware-megoldás peremhálózati hálózatát határozza meg.  Ez a peremhálózati hálózat olyan szolgáltatásokhoz használható, amelyek VPN-, ExpressRoute-és internetkapcsolattal rendelkeznek a privát Felhőkhöz.

A CloudSimple szolgáltatás hozzáadásához létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat a peremhálózati hálózat létrehozásakor használatos, és egy/28 CIDR blokkot igényel. Az átjáró alhálózatának címtartomány egyedinek kell lennie. A helyszíni hálózati címtartomány vagy az Azure-beli virtuális hálózati címtartomány egyikével sem lehet átfedésben.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>A Microsoft. VMwareCloudSimple erőforrás-szolgáltató engedélyezése

Az CloudSimple szolgáltatás erőforrás-szolgáltatójának engedélyezéséhez kövesse az alábbi lépéseket.

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg és válasszaki az előfizetéseket.

    ![Keresés az előfizetések között](media/cloudsimple-service-select-subscriptions.png)

3. Válassza ki azt az előfizetést, amelyen engedélyezni szeretné a CloudSimple szolgáltatást.
4. Az előfizetéshez tartozó **erőforrás-szolgáltatók** elemre.
5. A **Microsoft. VMwareCloudSimple** használatával szűrheti az erőforrás-szolgáltatót.
6. Válassza ki a **Microsoft. VMwareCloudSimple** erőforrás-szolgáltatót, és kattintson a **regisztráció**elemre.

    ![Erőforrás-szolgáltató regisztrálása](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple szolgáltatást**.

    ![Search CloudSimple Service](media/create-cloudsimple-service-search.png)

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

## <a name="purchase-nodes"></a>Csomópontok vásárlása

Ha a CloudSimple saját felhőalapú környezete számára szeretné beállítani az utólagos elszámolású kapacitást, először a Azure Portal csomópontjait kell kiépíteni.

1. Válassza az **Összes szolgáltatás** elemet.
2. CloudSimple- **csomópontok**keresése.

    ![CloudSimple-csomópontok keresése](media/create-cloudsimple-node-search.png)

3. Válassza a **CloudSimple**-csomópontok lehetőséget.
4. Csomópontok létrehozásához kattintson a **Hozzáadás** gombra.

    ![CloudSimple-csomópontok hozzáadása](media/create-cloudsimple-node-add.png)

5. Válassza ki azt az előfizetést, ahol a CloudSimple-csomópontokat szeretné megvásárolni.
6. Válassza ki a csomópontok erőforrás-csoportját. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása**lehetőségre.
7. Adja meg a csomópontok azonosításához szükséges előtagot.
8. Válassza ki a csomópont-erőforrások helyét.
9. Válassza ki a csomópont-erőforrások tárolására szolgáló dedikált helyet.
10. Válassza ki a csomópont típusát. Kiválaszthatja a [CS28 vagy a CS36 lehetőséget](cloudsimple-node.md). Az utóbbi lehetőség a maximális számítási és memória-kapacitást is tartalmazza.
11. Válassza ki a kiépíteni kívánt csomópontok számát.
12. Válassza a **felülvizsgálat + létrehozás**lehetőséget.
13. Tekintse át a beállításokat. A beállítások módosításához kattintson az **előző**gombra.
14. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

* [Saját felhő létrehozása és környezet konfigurálása](quickstart-create-private-cloud.md)
* További információ a [CloudSimple szolgáltatásról](cloudsimple-service.md)
