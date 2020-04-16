---
title: Rövid útmutató – munkaterület létrehozása
description: Hozzon létre egy Azure Synapse Analytics munkaterületet az útmutató ban leírt lépéseket követve.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b6bf4c6a54e2f187a5751d267720f356e0c30ff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424397"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Rövid útmutató: Hozzon létre egy Azure Synapse Analytics munkaterületet (előzetes verzió)

Ez a rövid útmutató ismerteti az Azure Synapse-munkaterület az Azure Portal használatával történő létrehozásának lépéseit.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot a kezdés előtt.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Előfeltételek

- [Azure Data Lake Storage Gen2 tárfiók](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Bejelentkezés az [Azure Portalra](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Hozzon létre egy Azure Synapse-munkaterületet az Azure Portalhasználatával

1. A Microsoft Azure keresési ablaktábláján adja meg a **Synapse-munkaterületet,** majd válassza ki ezt a szolgáltatást.
![Az Azure Portal keresősávja az Azure Synapse beírt munkaterületekkel.](media/quickstart-create-synapse-workspace/workspace-search.png). .
2. A **Szinapszis-munkaterületek** lapon kattintson a **+ Hozzáadás gombra.**
![Parancs új Azure Synapse munkaterület kiemelve. ](media/quickstart-create-synapse-workspace/create-workspace-02.png).
3. Töltse ki az **Azure Synapse** munkaterület-űrlapot a következő információkkal:

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **Előfizetés** | *Az Ön előfizetése* | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
    | **Erőforráscsoport** | *Bármely erőforráscsoport* | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ismertető cikket. |
    | **Munkaterület neve** | mysamplemunkaterület | Megadja a munkaterület nevét, amelyet a rendszer a kapcsolatvégpontokhoz is használ.|
    | **Régió** | USA 2. keleti régiója | A munkaterület helyét adja meg.|
    | **2. generációs Data Lake Storage** | Számla:`storage account name` </br> Fájlrendszer:`root file system to use` | Megadja az elsődleges tárolóként használandó ADLS Gen2 tárfiók nevét és a használandó fájlrendszert.|
    ||||

    ![Munkaterület-létesítési folyamat – Alapjai lap.](media/quickstart-create-synapse-workspace/create-workspace-03.png).

    A tárfiók a következő helyközül választható ki:
    - Az előfizetésben elérhető ADLS Gen2-fiókok listája
    - Manuálisan a dedikáthasznál kell megadni

    > [!IMPORTANT]
    > Az Azure Synapse munkaterületet kell tudni olvasni és írni a kiválasztott ADLS Gen2 fiók.
    >
    > Az ADLS Gen2 kiválasztási mezők alatt van egy megjegyzés, amely szerint a munkaterület felügyelt identitása a **Storaqe Blob Data Contributor** szerepkört rendeli hozzá a kiválasztott Data Lake Storage Gen2 fájlrendszeren, amely teljes hozzáférést biztosít számára.

4. (Nem kötelező) A Biztonság **+ hálózat beállításainak** módosítása:
5. (Nem kötelező) Adja hozzá a **címkéket** a Címkék lapon.
6. Az **Összegzés** lapon futtatják a szükséges ellenőrzéseket a munkaterület sikeres létrehozásának biztosításához. Ha az ellenőrzés sikeres, nyomja le a **Munkaterület** ![](media/quickstart-create-synapse-workspace/create-workspace-05.png)létesítési folyamat létrehozása – megerősítés lapot.
7. Miután az erőforrás-létesítési folyamat sikeresen befejeződött, megjelenik egy bejegyzés a létrehozott munkaterület a szinapszid munkaterületek listájában. ![Az újonnan kiépített munkaterületet megjelenítő Synapse-munkaterületek listája.](media/quickstart-create-synapse-workspace/create-workspace-07.png).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Kövesse az alábbi lépéseket az Azure Synapse munkaterület törléséhez.
> [!WARNING]
> Az Azure Synapse-munkaterület törlésével eltávolítja az elemzési motorokat és a tartalmazott SQL-készletek és munkaterületi metaadatok adatbázisában tárolt adatokat. A továbbiakban nem lehet csatlakozni az SQL-végpontokhoz, az Apache Spark-végpontokhoz. Minden kódelem törlődik (lekérdezések, jegyzetfüzetek, feladatdefiníciók és folyamatok).
>
> A munkaterület törlése **nincs** hatással a munkaterülethez kapcsolódó Data Lake Store Gen2 adataira.

Ha törölni szeretné az Azure Synapse munkaterületet, hajtsa végre az alábbi lépéseket:

1. Keresse meg a törölni kívánt Azure Synapse munkaterületet.
1. Nyomja le a **delete** billentyűt a parancssávon.
 ![Azure Synapse munkaterület áttekintése – törlés parancs kiemelve.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Erősítse meg a törlést, és nyomja meg a **Törlés** gombot.
 ![Az Azure Synapse munkaterület áttekintése – munkaterület-megerősítő párbeszédpanel törlése.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. Ha a folyamat sikeresen befejeződik, az Azure Synapse munkaterület már nem jelenik meg a munkaterületek listájában.

## <a name="next-steps"></a>További lépések

Ezután [sql-készleteket hozhat létre,](quickstart-create-sql-pool.md) vagy [Apache Spark-készleteket hozhat létre](quickstart-create-apache-spark-pool.md) az adatok elemzésének megkezdéséhez és feltárásához.
