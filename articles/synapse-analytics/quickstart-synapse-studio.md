---
title: A Synapse Studio használata (előzetes verzió)
description: Ebben a rövid útmutatóban megtudhatja, hogy milyen egyszerű lekérdezni a különböző típusú fájlokat a Synapse Studio használatával.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 294f53fe929343708bdbb9564b23c46865cf02ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423865"
---
# <a name="quickstart-using-synapse-studio-preview"></a>Rövid útmutató: A Synapse Studio használata (előzetes verzió)

Ebben a rövid útmutatóban megtudhatja, hogyan kérdezheti le a fájlokat a Synapse Studio használatával.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Azure Synapse munkaterületet és a társított tárfiókot.](quickstart-create-workspace.md)

## <a name="launch-synapse-studio"></a>Indítsd el a Synapse Stúdiót

Az Azure Synapse-munkaterületen az Azure Portalon kattintson a **Synapse Studio indítása**elemre.

![Indítsd el a Synapse Stúdiót](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Másik lehetőségként elindíthatja a Synapse Studio-t az [Azure Synapse Analytics](https://web.azuresynapse.net) elemre kattintva, és megadva a megfelelő bérlői, előfizetési és munkaterületi értékeket.

## <a name="browse-storage-accounts"></a>Tárfiókok böngészése

Miután megnyitotta a Synapse Studio-t, keresse fel az **Adatok,** majd **bontsa ki a Storage-fiókokat** a tárfiók megtekintéséhez a munkaterületen.

![Fájlok tallózása a tárhelyen](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Új mappákat hozhat létre, és fájlokat tölthet fel az eszköztár hivatkozásai segítségével a fájlok rendszerezéséhez.

## <a name="query-files-on-storage-account"></a>Fájlok lekérdezése a tárfiókban

> [!IMPORTANT]
> A fájlok lekérdezéséhez az `Storage Blob Reader` alapul szolgáló tároló szerepkör tagjának kell lennie. Megtudhatja, hogyan [rendelhet hozzá Storage Blob Data **Reader** vagy Storage Blob **Data Contributor** RBAC engedélyeket az Azure Storage-hoz.](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)

1. Töltsön `PARQUET` fel néhány fájlt.
2. Jelöljön ki egy vagy több fájlt, majd hozzon létre egy új SQL-parancsfájlt vagy egy Spark-jegyzetfüzetet a fájlok tartalmának megtekintéséhez. Ha jegyzetfüzetet szeretne létrehozni, létre kell hoznia [az Apache Spark-készletet a munkaterületen.](spark/apache-spark-notebook-create-spark-use-sql.md)

   ![Fájlok lekérdezése a tárolóban](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Futtassa a létrehozott lekérdezést vagy jegyzetfüzetet a fájl tartalmának megtekintéséhez:

   ![A fájl tartalmának megtekintése](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. A lekérdezés módosíthatja az eredmények szűrését és rendezését. Keresse meg az SQL-szolgáltatások ban igény szerint elérhető nyelvi szolgáltatásokat az [SQL-szolgáltatások áttekintésében.](sql/overview-features.md)

## <a name="next-steps"></a>További lépések

- Az Azure AD-felhasználók lekérdezése a [storage Blob Data Reader vagy storage **Blob** **Data Contributor** RBAC engedélyek hozzárendelésével az Azure Storage-hoz](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Fájlok lekérdezése az Azure Storage-ban az SQL On-Demand használatával](sql/on-demand-workspace-overview.md)
- [Apache Spark-készlet létrehozása](spark/apache-spark-notebook-create-spark-use-sql.md)
- [Power BI-jelentés létrehozása az Azure Storage-ban tárolt fájlokon](sql/tutorial-connect-power-bi-desktop.md)
