---
title: Hozzon létre egy Azure Databricks-munkaterületet saját virtuális hálózatának rövid útmutatójában
description: Ez a cikk ismerteti, hogyan telepítheti az Azure Databricks a virtuális hálózatra.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132675"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Rövid útmutató: Hozzon létre egy Azure Databricks-munkaterületet a saját virtuális hálózatán

Az Azure Databricks alapértelmezett üzembe helyezése egy új virtuális hálózatot hoz létre, amelyet a Databricks kezel. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Databricks munkaterületet a saját virtuális hálózatában. Apache Spark-fürtöt is létrehozhat a munkaterületen belül. 

Ha többet szeretne tudni arról, hogy miért érdemes Azure Databricks-munkaterületet létrehozni a saját virtuális hálózatában, olvassa [el az Azure Databricks üzembe helyezése az Azure virtuális hálózatban (VNet injection) című témakört.](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/databricks/)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

> [!Note]
> Ez az oktatóanyag nem hajtható végre **az Azure ingyenes próba-előfizetésével.**
> Ha van ingyenes fiókja, nyissa meg a profilját, és módosítsa az előfizetését **a felosztó-kirovó szolgáltatásra.** További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket. Ezután [távolítsa el a költségkeretet,](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)és kérjen [kvótanövelést](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) a régióban a vCPU-khoz. Az Azure Databricks-munkaterület létrehozásakor kiválaszthatja a **próbaverziós (prémium – 14 napos ingyenes dbári)** díjszabási szintet, hogy a munkaterület 14 napig hozzáférést biztosítson az ingyenes prémium szintű Azure Databricks KBB-khoz.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Ezután válassza **a Hálózat > virtuális hálózat lehetőséget.**

    ![Virtuális hálózat létrehozása az Azure Portalon](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. A **Virtuális hálózat létrehozása**csoportban alkalmazza a következő beállításokat: 

    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Előfizetés|\<Az Ön előfizetése\>|Válassza ki a használni kívánt Azure-előfizetést.|
    |Erőforráscsoport|databricks-rövidútmutató|Válassza **az Új létrehozása lehetőséget,** és adjon meg egy új erőforráscsoport nevet a fiókjához.|
    |Név|databricks-rövidútmutató|Válassza ki a virtuális hálózat nevét.|
    |Régió|\<Válassza ki a felhasználóihoz legközelebb eső régiót\>|Válassza ki azt a földrajzi helyet, ahol a virtuális hálózatot üzemeltetheti. Használja a felhasználókhoz legközelebb eső helyet.|

    ![Virtuális hálózat alapjai az Azure Portalon](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Válassza a **Tovább: IP-címek >,** és alkalmazza a következő beállításokat. Ezután válassza **a Véleményezés + create lehetőséget.**
    
    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |IPv4-címtér|10.2.0.0/16|A virtuális hálózat címtartománya CIDR jelölésben. A CIDR-tartománynak /16 és /24 között kell lennie|
    |Alhálózat neve|alapértelmezett|Válassza ki a virtuális hálózat alapértelmezett alhálózatának nevét.|
    |Alhálózat címtartománya|10.2.0.0/24|Az alhálózat címtartománya a CIDR-jelölésrendszerben. A virtuális hálózat címterének kell tartalmaznia. A használatban lévő alhálózat címtartománya nem szerkeszthető.|

    ![Virtuális hálózat IP-konfigurációinak beállítása az Azure Portalon](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. A **Véleményezés + létrehozás** lapon válassza a **Létrehozás** lehetőséget a virtuális hálózat központi telepítéséhez. A telepítés befejezése után keresse meg a virtuális hálózatot, és válassza **a Címterület lehetőséget** a **Beállítások csoportban.** A *További címtartomány hozzáadása*párbeszédpanelen `10.179.0.0/16` a Beszúrás és a **Mentés**lehetőséget válassza.

    ![Az Azure virtuális hálózati címterülete](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Ezután válassza **az Analytics > a Databricks**lehetőséget.

    ![Hozzon létre egy Azure Databricks-munkaterületet az Azure Portalon](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. Az **Azure Databricks Service alatt**alkalmazza a következő beállításokat:

    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Munkaterület neve|databricks-rövidútmutató|Válasszon nevet az Azure Databricks-munkaterületének.|
    |Előfizetés|\<Az Ön előfizetése\>|Válassza ki a használni kívánt Azure-előfizetést.|
    |Erőforráscsoport|databricks-rövidútmutató|Válassza ki ugyanazt az erőforráscsoportot, amelyet a virtuális hálózathoz használt.|
    |Hely|\<Válassza ki a felhasználóihoz legközelebb eső régiót\>|Válassza ki ugyanazt a helyet, mint a virtuális hálózat.|
    |Tarifacsomag|Válassza a Standard vagy a Prémium előfizetést.|A tarifacsomagokról a [Databricks díjszabási oldalon](https://azure.microsoft.com/pricing/details/databricks/)talál további információt.|

    ![Azure Databricks-munkaterület alapjainak létrehozása](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Miután befejezte a beállítások **megadását** az Alapok lapon, válassza a **Tovább: Hálózat: Hálózat >** lehetőséget, és alkalmazza a következő beállításokat:

    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Az Azure Databricks-munkaterület üzembe helyezése a virtuális hálózaton (VNet)|Igen|Ez a beállítás lehetővé teszi egy Azure Databricks-munkaterület üzembe helyezését a virtuális hálózatban.|
    |Virtual Network|databricks-rövidútmutató|Jelölje ki az előző szakaszban létrehozott virtuális hálózatot.|
    |Nyilvános alhálózat neve|nyilvános alhálózat|Használja az alapértelmezett nyilvános alhálózat nevet.|
    |Nyilvános alhálózati CIDR-tartomány|10.179.64.0/18|Legfeljebb /26-os CIDR-tartományt használjon.|
    |Privát alhálózat neve|magán-alhálózat|Használja az alapértelmezett személyes alhálózati nevet.|
    |Privát alhálózati CIDR-tartomány|10.179.0.0/18|Legfeljebb /26-os CIDR-tartományt használjon.|

    ![Virtuális hálózati adatok hozzáadása az Azure Databricks-munkaterülethez az Azure Portalon](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. A központi telepítés befejezése után keresse meg az Azure Databricks erőforrást. Figyelje meg, hogy a virtuális hálózati társviszony-létesítés le van tiltva. Figyelje meg az erőforráscsoportot és a felügyelt erőforráscsoportot is az áttekintő lapon. 

    ![Az Azure Databricks áttekintése az Azure Portalon](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    A felügyelt erőforráscsoport nem módosítható, és nem használható virtuális gépek létrehozásához. Csak a kezelt erőforráscsoportban hozhat létre virtuális gépeket.

    ![Azure Databricks felügyelt erőforráscsoport](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    Ha egy munkaterület telepítése sikertelen, a munkaterület továbbra is hibás állapotban jön létre. Törölje a sikertelen munkaterületet, és hozzon létre egy új munkaterületet, amely megoldja a telepítési hibákat. A sikertelen munkaterület törlésekor a felügyelt erőforráscsoport és a sikeresen üzembe helyezett erőforrások is törlődnek.

## <a name="create-a-cluster"></a>Fürt létrehozása

> [!NOTE]
> Ha egy ingyenes fiókot használna az Azure Databricks-fürt létrehozásához, a fürt létrehozása előtt nyissa meg a saját profilját, és módosítsa az előfizetését **használatalapú fizetésre**. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket.

1. Térjen vissza az Azure Databricks szolgáltatásához, és válassza a **Munkaterület indítása** lehetőséget az **Áttekintés** lapon.

2. Válassza **a Fürtök** > **+ Fürt létrehozása**lehetőséget. Ezután hozzon létre egy fürtnevet, például *databricks-quickstart-cluster*, és fogadja el a fennmaradó alapértelmezett beállításokat. Válassza **a Fürt létrehozása**lehetőséget.

    ![Azure Databricks-fürt létrehozása](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. A fürt futása után térjen vissza a felügyelt erőforráscsoport az Azure Portalon. Figyelje meg az új virtuális gépeket, lemezeket, IP-címet és hálózati adaptereket. A hálózati adapter jön létre az egyes nyilvános és magán alhálózatok IP-címekkel.  

    ![Az Azure Databricks által kezelt erőforráscsoport a fürt létrehozása után](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Térjen vissza az Azure Databricks-munkaterületére, és válassza ki a létrehozott fürtöt. Ezután keresse meg a **Végrehajtók** lapot a **Spark felhasználói felületén.** Figyelje meg, hogy az illesztőprogram és a végrehajtók címei a privát alhálózati tartományban vannak. Ebben a példában az illesztőprogram 10.179.0.6, a végrehajtók pedig 10.179.0.4 és 10.179.0.5. Az IP-címek eltérőek lehetnek.

    ![Az Azure Databricks Spark felhasználói felületének végrehajtói](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a cikkel, leállíthatja a fürtöt. Ehhez az Azure Databricks-munkaterület bal oldali panelén kattintson a **Fürtök** elemre. A leállítani kívánt fürtnél vigye az egérmutatót a **Műveletek** oszlopban található három pont fölé, majd kattintson a **Leállítás** ikonra. Ez leállítja a fürtöt.

Ha nem szakítja meg manuálisan a fürtöt, az automatikusan leáll, feltéve, hogy a fürt létrehozásakor bejelölte a **Percek \_ \_ utáni inaktivitás** után jelölőnégyzetet. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

Ha nem szeretné újra felhasználni a fürtöt, törölheti az Azure Portalon létrehozott erőforráscsoportot.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy Spark-fürtaz Azure Databricks, amely egy virtuális hálózatra telepített. A következő cikkből megtudhatja, hogyan kérdezhet le egy SQL Server Linux Docker-tárolót a virtuális hálózatban a JDBC használatával egy Azure Databricks-jegyzetfüzetből.  

> [!div class="nextstepaction"]
>[Virtuális hálózaton belüli SQL Server Linux Docker-tároló lekérdezése Azure Databricks-jegyzetfüzetből](vnet-injection-sql-server.md)
