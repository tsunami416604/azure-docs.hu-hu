---
title: Rövid útmutató – munkaterület létrehozása
description: Hozzon létre egy Azure szinapszis Analytics-munkaterületet az útmutató lépéseit követve.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0bf58348d91c23db9bcc016ff6d71ce2b3804ced
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201053"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Rövid útmutató: Azure szinapszis Analytics-munkaterület létrehozása (előzetes verzió)

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Azure szinapszis-munkaterületet a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- [Azure Data Lake Storage Gen2 Storage-fiók](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [Azure Portalba](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Azure szinapszis-munkaterület létrehozása a Azure Portal használatával

1. A Microsoft Azure keresési ablaktáblán adja meg a **szinapszis munkaterületet** , majd válassza ki ezt a szolgáltatást.
![Azure Portal keresési sáv az Azure szinapszis-munkaterületek beírásával.](media/quickstart-create-synapse-workspace/workspace-search.png)
2. A **szinapszis munkaterületek** lapon kattintson a **+ Hozzáadás**gombra.
![Parancs az új Azure szinapszis-munkaterület kiemeléséhez.](media/quickstart-create-synapse-workspace/create-workspace-02.png)
3. Töltse ki az **Azure szinapszis-munkaterület** űrlapot a következő információkkal:

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **Előfizetés** | *Az előfizetése* | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
    | **Erőforráscsoport** | *Bármely erőforráscsoport* | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ismertető cikket. |
    | **Munkaterület neve** | mysampleworkspace | Megadja a munkaterület nevét, amelyet a rendszer a kapcsolatok végpontjai esetében is használni fog.|
    | **Régió** | USA 2. keleti régiója | Meghatározza a munkaterület helyét.|
    | **2. generációs Data Lake Storage** | Fiók`storage account name` </br> Fájlrendszer:`root file system to use` | Megadja az elsődleges tárolóként használandó ADLS Gen2 Storage-fiók nevét és a használni kívánt fájlrendszert.|
    ||||

    ![Munkaterület-kiépítési folyamat – alapismeretek lap.](media/quickstart-create-synapse-workspace/create-workspace-03.png)

    A Storage-fiók az alábbiak közül választható ki:
    - Az előfizetésben elérhető ADLS Gen2 fiókok listája
    - Manuálisan lett megadva a fiók neve alapján

    > [!IMPORTANT]
    > Az Azure szinapszis-munkaterületnek képesnek kell lennie olvasni és írni a kiválasztott ADLS Gen2 fiókot. Továbbá minden olyan Storage-fiókhoz, amelyet elsődleges Storage-fiókként csatol, engedélyezni kell a **hierarchikus névteret** a Storage-fiók létrehozásakor.
    >
    > A ADLS Gen2 kiválasztási mezők alatt látható, hogy a munkaterület felügyelt identitása hozzá lesz rendelve a **Storaqe blob adatközreműködői** szerepkörhöz a kiválasztott Data Lake Storage Gen2-fájlrendszerben, amely teljes hozzáférést biztosít.

4. Választható Módosítsa a **Biztonság + hálózatkezelés alapértelmezett beállítások** lapját:
5. Választható Adjon hozzá címkéket a **címkék** lapon.
6. Az **Összefoglalás** lapon a szükséges érvényesítések futnak, hogy a munkaterület sikeresen létrehozható legyen. Ha az ellenőrzés sikeres, nyomja meg **Create** a ![ munkaterület kiépítési folyamatának létrehozása – megerősítés lapot.](media/quickstart-create-synapse-workspace/create-workspace-05.png)
7. Miután az erőforrás-létesítési folyamat sikeresen befejeződik, megjelenik egy bejegyzés a létrehozott munkaterülethez a szinapszis-munkaterületek listájában. ![Az újonnan kiosztott munkaterületet megjelenítő szinapszis-munkaterületek listázása.](media/quickstart-create-synapse-workspace/create-workspace-07.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure szinapszis-munkaterület törléséhez kövesse az alábbi lépéseket.
> [!WARNING]
> Az Azure szinapszis-munkaterület törlésével a rendszer eltávolítja az elemzési motorokat és a tárolt SQL-készletek és munkaterület-metaadatok adatbázisában tárolt adatokat. Többé nem lehet csatlakozni az SQL-végpontokhoz, Apache Spark-végpontokhoz. Minden kódrészlet törölve lesz (lekérdezések, jegyzetfüzetek, feladatdefiníciók és folyamatok).
>
> A munkaterület törlése **nem** befolyásolja a munkaterülethez csatolt Data Lake Store Gen2 lévő adatműveleteket.

Ha törölni szeretné az Azure szinapszis munkaterületet, hajtsa végre a következő lépéseket:

1. Navigáljon a törölni kívánt Azure szinapszis-munkaterületre.
1. Nyomja le a **delete** billentyűt a parancssorban.
 ![Az Azure szinapszis munkaterület áttekintése – a parancs kiemelése kiemelve.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Erősítse meg a törlést, majd nyomja le a **delete (Törlés** ) gombot.
 ![Az Azure szinapszis munkaterületének áttekintése – munkaterület megerősítő párbeszédpanelének törlése.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. Ha a folyamat sikeresen befejeződik, az Azure szinapszis munkaterület többé nem jelenik meg a munkaterületek listájában.

## <a name="next-steps"></a>Következő lépések

Ezután [LÉTREHOZHAT SQL-készleteket](quickstart-create-sql-pool.md) , vagy [létrehozhat Apache Spark készleteket](quickstart-create-apache-spark-pool.md) az adatok elemzésének és vizsgálatának megkezdéséhez.
