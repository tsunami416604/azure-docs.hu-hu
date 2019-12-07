---
title: Azure Databricks munkaterület létrehozása saját Virtual Network rövid útmutatóban
description: Ez a cikk azt ismerteti, hogyan helyezhetők üzembe Azure Databricks a virtuális hálózaton.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 404aa1d345bb1e30a0fe55d033d49d7dd8ba5a1b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893427"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Rövid útmutató: Azure Databricks munkaterület létrehozása a saját Virtual Network

A Azure Databricks alapértelmezett telepítése egy új, a Databricks által felügyelt virtuális hálózatot hoz létre. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Databricks munkaterületet a saját virtuális hálózatában. Az adott munkaterületen belül Apache Spark-fürtöt is létrehozhat. 

További információ arról, hogy miért dönthet úgy, hogy létrehoz egy Azure Databricks munkaterületet a saját virtuális hálózatában: [Azure Databricks üzembe helyezése az Azure Virtual Networkban (VNet Injection)] (/databricks/Administration-Guide/Cloud-configurations/Azure/vnet-Inject).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

> [!Note]
> Ez az oktatóanyag nem hajtható végre az **Azure ingyenes próbaverziós előfizetésével**.
> Ha ingyenes fiókkal rendelkezik, lépjen a profilba, és változtassa meg az előfizetését **az utólagos**elszámolású verzióra. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket. Ezután [távolítsa el a](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)költségkeretet, és [igényeljen kvóta-növekedést](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) a régiójában lévő vCPU. A Azure Databricks munkaterületének létrehozásakor kiválaszthatja a **próbaverzió (prémium-14 napos ingyenes dBu)** díjszabását, hogy a munkaterület 14 napig elérhető legyen az ingyenes prémium Azure Databricks dBu.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A Azure Portal menüben válassza az **erőforrás létrehozása**lehetőséget. Ezután válassza a **hálózatkezelés > virtuális hálózat**lehetőséget.

    ![Virtuális hálózat létrehozása Azure Portalon](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. A **virtuális hálózat létrehozása**területen alkalmazza a következő beállításokat: 

    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Név|databricks – rövid útmutató|Válassza ki a virtuális hálózat nevét.|
    |Címtér|10.1.0.0/16|A virtuális hálózat CIDR jelölési tartománya. A CIDR tartományának/16 és/24 közé kell esnie|
    |Előfizetés|\<Az Ön előfizetése\>|Válassza ki a használni kívánt Azure-előfizetést.|
    |Erőforráscsoport|databricks – rövid útmutató|Válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet a fiókhoz.|
    |Földrajzi egység|\<Válassza ki a felhasználóihoz legközelebb eső régiót\>|Válassza ki azt a földrajzi helyet, ahol a virtuális hálózatot üzemeltetni szeretné. Használja a felhasználókhoz legközelebb eső helyet.|
    |Alhálózat neve|alapértelmezett|Válassza ki a virtuális hálózatban található alapértelmezett alhálózat nevét.|
    |Alhálózat címtartománya|10.1.0.0/24|Az alhálózat címtartománya a CIDR-jelölésrendszerben. Ennek a virtuális hálózat címterület részét kell tartalmaznia. A használatban lévő alhálózat címtartomány nem szerkeszthető.|

    ![Virtuális hálózat létrehozása Azure Portalon](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Az üzembe helyezés befejezését követően navigáljon a virtuális hálózathoz, és válassza a **címterület** lehetőséget a **Beállítások**területen. A *további címtartomány hozzáadása*mezőbe szúrja be `10.179.0.0/16`, majd válassza a **Mentés**lehetőséget.

    ![Azure-beli virtuális hálózati címtartomány](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

1. A Azure Portal menüben válassza az **erőforrás létrehozása**lehetőséget. Ezután válassza az **elemzési > Databricks**lehetőséget.

    ![Azure Databricks munkaterület létrehozása Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. A **Azure Databricks szolgáltatás**területen alkalmazza a következő beállításokat:

    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Munkaterület neve|databricks – rövid útmutató|Válassza ki a Azure Databricks munkaterület nevét.|
    |Előfizetés|\<Az Ön előfizetése\>|Válassza ki a használni kívánt Azure-előfizetést.|
    |Erőforráscsoport|databricks – rövid útmutató|Válassza ki ugyanazt az erőforráscsoportot, amelyet a virtuális hálózathoz használt.|
    |Földrajzi egység|\<Válassza ki a felhasználóihoz legközelebb eső régiót\>|Válassza ki a virtuális hálózattal megegyező helyet.|
    |Díjcsomag|Válassza a standard vagy a prémium lehetőséget.|A díjszabással kapcsolatos további információkért tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).|
    |Azure Databricks munkaterület üzembe helyezése a Virtual Networkban (VNet)|Igen|Ez a beállítás lehetővé teszi Azure Databricks munkaterület telepítését a virtuális hálózaton.|
    |Virtual Network (Virtuális hálózat)|databricks – rövid útmutató|Válassza ki az előző szakaszban létrehozott virtuális hálózatot.|
    |Nyilvános alhálózat neve|nyilvános alhálózat|Használja az alapértelmezett nyilvános alhálózat nevét.|
    |Nyilvános alhálózat CIDR tartománya|10.179.64.0/18|Használjon CIDR-tartományt, beleértve a/26-ot.|
    |Magánhálózati alhálózat neve|magánhálózati alhálózat|Használja az alapértelmezett magánhálózati alhálózat nevét.|
    |Magánhálózati alhálózat CIDR tartománya|10.179.0.0/18|Használjon CIDR-tartományt, beleértve a/26-ot.|

    ![Azure Databricks munkaterület létrehozása Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Az üzembe helyezés befejezése után navigáljon a Azure Databricks erőforráshoz. Figyelje meg, hogy a virtuális hálózat társítása le van tiltva. Figyelje meg az erőforráscsoportot és a felügyelt erőforráscsoportot is az Áttekintés oldalon. 

    ![Azure Databricks áttekintése Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    A felügyelt erőforráscsoport nem módosítható, és nem használatos virtuális gépek létrehozásához. A kezelt erőforráscsoporthoz csak virtuális gépeket hozhat létre.

    ![Felügyelt erőforráscsoport Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Fürt létrehozása

> [!NOTE]
> Ha egy ingyenes fiókot használna az Azure Databricks-fürt létrehozásához, a fürt létrehozása előtt nyissa meg a saját profilját, és módosítsa az előfizetését **használatalapú fizetésre**. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket.

1. Térjen vissza a Azure Databricks szolgáltatáshoz, és válassza a **munkaterület elindítása** lehetőséget az **Áttekintés** oldalon.

2. Válassza a **fürtök** >  **+ fürt létrehozása**lehetőséget. Ezután hozzon létre egy fürtöt, például *databricks-Gyorsindítás-cluster*, és fogadja el a többi alapértelmezett beállítást. Válassza a **Fürt létrehozása** lehetőséget.

    ![Azure Databricks-fürt létrehozása](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Miután a fürt fut, térjen vissza a felügyelt erőforráscsoporthoz a Azure Portalban. Figyelje meg az új virtuális gépeket, lemezeket, IP-címeket és hálózati adaptereket. A hálózati adapterek az IP-címekkel rendelkező nyilvános és magánhálózati alhálózatokban jönnek létre.  

    ![Felügyelt erőforráscsoport Azure Databricks a fürt létrehozása után](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Térjen vissza a Azure Databricks munkaterületre, és válassza ki a létrehozott fürtöt. Ezután a **Spark felhasználói felület** lapján navigáljon a **végrehajtók** lapra. Figyelje meg, hogy az illesztőprogram és a végrehajtók címei a magánhálózati alhálózat tartományában vannak. Ebben a példában az illesztőprogram a 10.179.0.6, a végrehajtók pedig a 10.179.0.4 és a 10.179.0.5. Az IP-címek eltérőek lehetnek.

    ![Azure Databricks Spark felhasználói felület végrehajtói](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a cikkel, leállíthatja a fürtöt. Ehhez az Azure Databricks-munkaterület bal oldali panelén kattintson a **Fürtök** elemre. A leállítani kívánt fürtnél vigye az egérmutatót a **Műveletek** oszlopban található három pont fölé, majd kattintson a **Leállítás** ikonra. Ezzel leállítja a fürtöt.

Ha nem állítja be manuálisan a fürtöt, a rendszer automatikusan leállítja, ha a fürt létrehozásakor bejelölte **\_\_ perc inaktivitás után** jelölőnégyzetet. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

Ha nem kívánja újra felhasználni a fürtöt, törölheti a Azure Portalban létrehozott erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy Spark-fürtöt hozott létre a virtuális hálózatra telepített Azure Databricksban. A következő cikkből megtudhatja, hogyan kérdezheti le a SQL Server Linux Docker-tárolót a virtuális hálózaton a JDBC használatával egy Azure Databricks jegyzetfüzetből.  

> [!div class="nextstepaction"]
>[SQL Server Linux Docker-tároló lekérdezése egy virtuális hálózaton egy Azure Databricks jegyzetfüzetből](vnet-injection-sql-server.md)
