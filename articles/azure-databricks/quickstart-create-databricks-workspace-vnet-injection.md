---
title: Azure Databricks-munkaterület létrehozása egy virtuális hálózaton belül
description: Ez a cikk ismerteti, hogyan helyezhet üzembe az Azure Databricks a virtuális hálózathoz.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288201"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Gyors útmutató: Azure Databricks-munkaterület létrehozása egy virtuális hálózaton belül

Ez a rövid útmutató bemutatja, hogyan Azure Databricks-munkaterület létrehozása egy virtuális hálózaton belül. Apache Spark-fürt, azon belül is létrehozza.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Az Azure Portalon válassza ki a **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózati**.

2. A **virtuális hálózat létrehozása**, alkalmazza a következő beállításokat: 

    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Name (Név)|databricks-quickstart|Válassza ki a virtuális hálózat nevét.|
    |Címtér|10.1.0.0/16|A virtuális hálózat címtartománya CIDR formátumban.|
    |Előfizetés|\<Az Ön előfizetése\>|Válassza ki a használni kívánt Azure-előfizetést.|
    |Erőforráscsoport|databricks-quickstart|Válassza ki **hozzon létre új** , és adja meg egy új erőforráscsoport neve a fiókjához.|
    |Földrajzi egység|\<Válassza ki a felhasználóihoz legközelebb eső régiót\>|Válasszon egy földrajzi hely, ahol a virtuális hálózat is üzemeltethet. A felhasználókhoz legközelebb eső helyet használja.|
    |Alhálózat neve|alapértelmezett|Válassza ki az alapértelmezett alhálózat nevét, a virtuális hálózaton.|
    |Alhálózat címtartománya|10.1.0.0/24|Az alhálózat címtartománya a CIDR-jelölésrendszerben. A virtuális hálózat címteréhez kell tartoznia. Használatban lévő alhálózatok címtartománya nem szerkeszthető.|

    ![Virtuális hálózat létrehozása az Azure Portalon](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Az üzembe helyezés befejezése után lépjen a virtuális hálózathoz, és válassza **címtér** alatt **beállítások**. A szöveget *újabb címtartomány felvétele*, insert `10.179.0.0/16` válassza **mentése**.

    ![Az Azure virtuális hálózat címtere](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

1. Az Azure Portalon válassza ki a **erőforrás létrehozása** > **Analytics** > **Databricks**.

2. A **Azure Databricks szolgáltatás**, alkalmazza a következő beállításokat:

    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Munkaterület neve|databricks-quickstart|Válassza ki az Azure Databricks-munkaterület nevét.|
    |Előfizetés|\<Az Ön előfizetése\>|Válassza ki a használni kívánt Azure-előfizetést.|
    |Erőforráscsoport|databricks-quickstart|Válassza ki a virtuális hálózat esetében használt ugyanabban az erőforráscsoportban.|
    |Földrajzi egység|\<Válassza ki a felhasználóihoz legközelebb eső régiót\>|Válassza ki a virtuális hálózattal megegyező helyen.|
    |Tarifacsomag|Válassza a Standard vagy prémium szint között.|A tarifacsomagokkal kapcsolatos további információkért lásd: a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).|
    |A virtuális hálózat az Azure Databricks-munkaterület üzembe helyezése|Igen|Ezzel a beállítással telepítheti a virtuális hálózat az Azure Databricks-munkaterületet.|
    |Virtual Network|databricks-quickstart|Válassza ki a virtuális hálózat az előző szakaszban létrehozott.|
    |Nyilvános alhálózat neve|public-subnet|Használja az alapértelmezett nyilvános alhálózat neve.|
    |Nyilvános alhálózat CIDR-tartományt|10.179.64.0/18|Az alhálózat CIDR-tartományt /18 és/26-os között kell lennie.|
    |Privát alhálózat neve|private-subnet|Használja az alapértelmezett magánhálózati alhálózat neve.|
    |Privát alhálózat CIDR-tartományt|10.179.0.0/18|Az alhálózat CIDR-tartományt /18 és/26-os között kell lennie.|

    ![Az Azure Portalon az Azure Databricks-munkaterület létrehozása](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Az üzembe helyezés befejezése után lépjen az Azure Databricks-erőforrást. Figyelje meg, hogy a virtuális hálózati társviszony le van tiltva. Szintén figyelje meg az erőforráscsoportot és a felügyelt erőforráscsoporthoz az Áttekintés lapon. 

    ![Az Azure Portalon az Azure Databricks áttekintése](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    A kezelt erőforráscsoport használt fizikai helynek a tárfiók (dbfs-SZEL) worker-sg (hálózati biztonsági csoport) és feldolgozók közötti kapcsolat (virtuális hálózat) tartalmaz. Emellett akkor is a hely, ahol a virtuális gépek, a lemez, az IP-cím és a hálózati adapter létrejön. Ez az erőforráscsoport zárolta az alapértelmezett; azonban ha egy fürtöt a virtuális hálózatot, egy hálózati adapter jön létre a felügyelt erőforráscsoporthoz a feldolgozók-vnet és a "hub" virtuális hálózat között.

    ![Az Azure Databricks felügyelt erőforráscsoport](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Fürt létrehozása

> [!NOTE]
> Ha egy ingyenes fiókot használna az Azure Databricks-fürt létrehozásához, a fürt létrehozása előtt nyissa meg a saját profilját, és módosítsa az előfizetését **használatalapú fizetésre**. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket.

1. Térjen vissza az Azure Databricks szolgáltatást, és válassza ki **munkaterület indítása** a a **áttekintése** lapot.

2. Válassza ki **fürtök** > **+ a fürt létrehozása**. Majd hozzon létre a fürt nevét, például *a rövid útmutatóban a databricks-fürt*, és fogadja el a többi alapértelmezett beállítást. Válassza ki **-fürt létrehozása**.

    ![Az Azure Databricks-fürt létrehozása](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Ha a fürt már fut, térjen vissza a felügyelt erőforráscsoporthoz az Azure Portalon. Figyelje meg, hogy az új virtuális gépek, a lemezek, az IP-cím és a hálózati adapterek. Hálózati adapter egyes nyilvános és privát IP-címekkel rendelkező alhálózatok jön létre.  

    ![Az Azure Databricks felügyelt erőforráscsoport fürt létrehozása után](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Térjen vissza az Azure Databricks-munkaterület, és válassza ki a létrehozott fürtöt. Keresse meg a **végrehajtóval** lapján a **Spark felhasználói felület** lapot. Figyelje meg, hogy a címek az illesztőprogramot, és a végrehajtóval privát alhálózatra tartományban vannak-e. Ebben a példában az illesztőprogram 10.179.0.6 és végrehajtóval 10.179.0.4 és 10.179.0.5. Az IP-címek eltérő lehet.

    ![Az Azure Databricks Spark felhasználói felület végrehajtóval](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a cikkel, leállíthatja a fürtöt. Ehhez az Azure Databricks-munkaterület bal oldali panelén kattintson a **Fürtök** elemre. A leállítani kívánt fürtnél vigye az egérmutatót a **Műveletek** oszlopban található három pont fölé, majd kattintson a **Leállítás** ikonra. Ezzel leállítja a fürtöt.

Ön nem állítja le manuálisan a fürt automatikusan leáll, ha a kiválasztott megadott a **leállítása után \_ \_ ennyi perc inaktivitás** jelölőnégyzetet a fürt létrehozásakor. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

Ha nem szeretne újból felhasználhatja a fürt, törölheti az Azure Portalon létrehozott erőforráscsoportot.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy Spark-fürtöt az Azure Databricks üzembe helyezett virtuális hálózathoz. Folytassa a következő cikkben megtudhatja, hogyan kérdezhet le egy Azure Databricks-jegyzetfüzetnek a JDBC segítségével a virtuális hálózatban az SQL Server Linux Docker-tárolóban.  

> [!div class="nextstepaction"]
>[A lekérdezés egy SQL Server Linux Docker-tárolót egy virtuális hálózaton lévő az Azure Databricks-jegyzetfüzet](vnet-injection-sql-server.md)
