---
title: Azure VMware-megoldás által CloudSimple rövid útmutató – a szolgáltatás létrehozása
description: Ismerje meg, hogyan hozhat létre a CloudSimple szolgáltatás csomópontok kiépítésére, és a csomópontok lefoglalása
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5732ea726bdecc10d0757224870ee5d8be83a2b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164220"
---
# <a name="quickstart---create-service"></a>Rövid útmutató – szolgáltatás létrehozása

Első lépésként hozzon létre az Azure VMware megoldás CloudSimple által az Azure Portalon.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware megoldást, CloudSimple - szolgáltatás áttekintése

A CloudSimple szolgáltatás lehetővé teszi, hogy Azure VMware megoldás által CloudSimple felhasználásához.  A szolgáltatás létrehozása lehetővé teszi, hogy a csomópontok üzembe helyezése, lefoglalni a csomópontokat, és a magánfelhők létrehozása.  A CloudSimple szolgáltatás minden egyes Azure-régióban, az CloudSimple szolgáltatás érhető el hozzá.  A szolgáltatás az Azure VMware megoldás által CloudSimple edge-hálózatot határoz meg.  Az edge-hálózatot a magánfelhők, VPN, ExpressRoute és az Internet kapcsolat-szolgáltatásokhoz használható.

A szolgáltatás hozzáadása a CloudSimple, létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat létrehozásához az edge-hálózatot, és a egy/28-as igényel CIDR-blokkja. Az átjáró-alhálózat címtere egyedinek kell lennie. Azt nem lehet átfedésben a helyszíni hálózat címterei vagy Azure virtuális hálózat címterét.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Erőforrás-szolgáltató Microsoft.VMwareCloudSimple engedélyezése

Az erőforrás-szolgáltató CloudSimple szolgáltatás engedélyezéséhez az alábbi lépésekkel.

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg és válassza **előfizetések**.

    ![Keresés az előfizetések között](media/cloudsimple-service-select-subscriptions.png)

3. Válassza ki az előfizetést, amelyen szeretné CloudSimple szolgáltatás engedélyezése
4. Kattintson a **erőforrás-szolgáltatók** az előfizetés
5. Használat **Microsoft.VMwareCloudSimple** az erőforrás-szolgáltató szűrése
6. Válassza ki a **Microsoft.VMwareCloudSimple** erőforrás-szolgáltató, majd kattintson a **regisztrálása**

    ![Erőforrás-szolgáltató regisztrálása](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg **CloudSimple szolgáltatás**.

    ![Search CloudSimple Service](media/create-cloudsimple-service-search.png)

3. Válassza ki **CloudSimple szolgáltatások**.
4. Kattintson a **Hozzáadás** új szolgáltatás létrehozása.

    ![Add CloudSimple Service](media/create-cloudsimple-service-add.png)

5. Válassza ki az előfizetést, ahol szeretné létrehozni a CloudSimple szolgáltatást.
6. Válassza ki az erőforráscsoportot, a szolgáltatás. Egy új erőforráscsoport hozzáadásához kattintson **hozzon létre új**.
7. Adja meg a szolgáltatás azonosítására szolgáló név.
8. Adja meg a CIDR a felhőszolgáltatási átjárót. Adjon meg egy/28-as alhálózat, amely nincs átfedésben a helyszíni alhálózatok, az Azure-alhálózatok és tervezett CloudSimple alhálózatok. A CIDR a szolgáltatás létrehozása után nem módosítható.

    ![A CloudSimple szolgáltatás létrehozása](media/create-cloudsimple-service.png)

9. Kattintson az **OK** gombra.

A szolgáltatás létrehozása és hozzáadni a szolgáltatások listájában.

## <a name="provision-nodes"></a>Csomópontok kiépítése

Használatalapú – mint-akkor lépjen kapacitás beállítására CloudSimple Magánfelhő-környezethez, először az Azure Portalon csomópontok kiépítésére.

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg **CloudSimple csomópontok**.

    ![CloudSimple csomópontok keresése](media/create-cloudsimple-node-search.png)

3. Válassza ki **CloudSimple csomópontok**.
4. Kattintson a **Hozzáadás** csomópontok létrehozásához.

    ![CloudSimple csomópontok hozzáadása](media/create-cloudsimple-node-add.png)

5. Válassza ki az előfizetést, ahol szeretné CloudSimple csomópontok kiépítésére.
6. Válassza ki az erőforráscsoportot, a csomópontok. Egy új erőforráscsoport hozzáadásához kattintson **hozzon létre új**.
7. Adja meg az előtagot a csomópontok azonosításához.
8. Válassza ki a csomópont erőforrások helyét.
9. Válassza ki a dedikált csomópont erőforrásokat üzemeltetni helyét.
10. Válassza ki a csomópontot. Választhat a [CS28 vagy CS36](cloudsimple-node.md). Az utóbbi lehetőséget tartalmaz a maximális számítási és memória-kapacitás.
11. Válassza ki a csomópontok kiépítéséhez.
12. Válassza ki **felülvizsgálat + létrehozás**.
13. Tekintse át a beállításokat. Valamelyik beállítás módosításához kattintson a **előző**.
14. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

* [Magánfelhő létrehozása és a környezet konfigurálása](quickstart-create-private-cloud.md)
* Tudjon meg többet [CloudSimple szolgáltatás](https://docs.azure.cloudsimple.com/cloudsimple-service)
