---
title: 'Gyors útmutató: Azure-Adatkezelő összekötése egy szinapszis-munkaterülettel'
description: Azure Adatkezelő-fürt összekötése egy szinapszis-munkaterülettel az Azure szinapszis használatával Apache Spark
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 99ccc2f4d7e3adbba704f784025abfdfa8b96f52
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090658"
---
# <a name="connect-to-azure-data-explorer-using-synapse-apache-spark"></a>Kapcsolódás az Azure Adatkezelőhoz a szinapszis használatával Apache Spark

Ez a cikk azt ismerteti, hogyan lehet hozzáférni egy Azure Adatkezelő-adatbázishoz a szinapszis Studio és a szinapszis Apache Spark használatával. 

## <a name="prerequisites"></a>Előfeltételek

* [Hozzon létre egy Azure adatkezelő-fürtöt és-adatbázist](/azure/data-explorer/create-cluster-database-portal).
* Meglévő szinapszis munkaterület, vagy hozzon létre egy új munkaterületet [a rövid](./quickstart-create-workspace.md) útmutató után 
* Meglévő szinapszis Apache Spark készlet vagy új készlet [létrehozása a rövid](./quickstart-create-apache-spark-pool-portal.md) útmutató után
* [Hozzon létre Azure AD-alkalmazást egy Azure AD-alkalmazás üzembe helyezésével.](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)
* Adja meg az Azure AD-alkalmazás hozzáférését az adatbázishoz az [azure adatkezelő Database-engedélyek kezelése](/azure/data-explorer/manage-database-permissions) után

## <a name="navigate-to-synapse-studio"></a>Navigáljon a szinapszis studióhoz

A szinapszis munkaterületen válassza a **szinapszis Studio elindítása**lehetőséget. A szinapszis Studio kezdőlapján **válassza az adatelemet**, amely végigvezeti az **adatObject Explorer**.

## <a name="connect-an-azure-data-explorer-database-to-a-synapse-workspace"></a>Azure Adatkezelő-adatbázis összekötése egy szinapszis-munkaterülettel

Az Azure Adatkezelő-adatbázisok munkaterülethez való csatlakoztatása a társított szolgáltatáson keresztül történik. Egy Azure-beli adat-összevonási szolgáltatás lehetővé teszi a felhasználók számára az adatok tallózását, olvasását és írását az Azure szinapszis Analytics Apache Spark, valamint az integrációs feladatok egy folyamaton belüli futtatását.

Az adatok Object Explorer egy Azure Adatkezelő-fürt közvetlen összekapcsolásához kövesse az alábbi lépéseket:

1. Válassza ki **+** az ikont az adatközpont közelében
2. Külső **adatkapcsolat kiválasztása**
3. **Azure-adatkezelő kiválasztása (Kusto)**
5. Válassza a **Folytatás** elemet
6. Nevezze el a társított szolgáltatást. A név megjelenik a Object Explorerban, és a szinapszis futtatási idejének használatával csatlakozik az adatbázishoz. A felhasználóbarát név használatát javasoljuk
7. Válassza ki az Azure-beli adatok feltárása fürtöt az előfizetésből, vagy adja meg az URI-t.
8. Adja meg a "szolgáltatás egyszerű azonosítója" és az "egyszerű szolgáltatásnév" kulcsot. (gondoskodjon arról, hogy ez az egyszerű szolgáltatásnév az olvasási művelethez és a betöltéshez szükséges adatfeldolgozáshoz a hozzáférés megtekintése az adatbázisban.)
9. Adja meg az Azure Adatkezelő-adatbázis nevét
10. A megfelelő engedélyek biztosításához kattintson a **Kapcsolódás tesztelése** lehetőségre.
11. Kattintson a **Létrehozás** elemre.

    ![Új társított szolgáltatás](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > Választható A kapcsolat tesztelése nem ellenőrzi az írási hozzáférést, győződjön meg arról, hogy a szolgáltatásnév azonosítójának írási hozzáférése van az Azure Adatkezelő-adatbázishoz.

12. Az Azure Adatkezelő-fürtök és-adatbázisok az Azure Adatkezelő szakasz  **csatolt** lapján láthatók. 

    ![Fürtök tallózása](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE] 
    > Az aktuális kiadásban az adatbázis-objektumok a HRE-fiók engedélyei alapján vannak feltöltve az Azure Adatkezelő-adatbázisokban. A Apache Spark notebookok vagy integrációs feladatok futtatásakor a rendszer a kapcsolati szolgáltatás hitelesítő adatait fogja használni (az egyszerű szolgáltatásnév).


## <a name="quickly-interact-with-code-generated-actions"></a>Gyors interakció a kód által generált műveletekkel

* Ha a jobb gombbal egy adatbázisra vagy táblára kattint, megjelenik a kézmozdulatok listája, amely egy minta Spark notebookot indít el az adatolvasáshoz, az adatíráshoz, az adatátvitelhez és az Azure-Adatkezelő. 
    [![Új minta notebookok](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

* Íme egy példa az adatolvasásra. Csatolja a notebookot a Spark-készlethez, és futtassa a cella [ ![ új olvasási jegyzetfüzetét](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE] 
   > Az első végrehajtás több mint három percet is igénybe vehet a Spark-munkamenet elindításához. A későbbi végrehajtások jelentősen gyorsabbak lesznek.  


## <a name="limitations"></a>Korlátozások
Az Azure Adatkezelő Connector jelenleg nem támogatott az Azure szinapszis által felügyelt VNET.


## <a name="next-steps"></a>Következő lépések

* [Mintakód speciális beállításokkal](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Azure Adatkezelő (Kusto) Spark-összekötő](https://github.com/Azure/azure-kusto-spark)