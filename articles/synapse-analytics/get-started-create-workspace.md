---
title: 'Oktatóanyag: a szinapszis-munkaterület létrehozásának első lépései'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet, egy SQL-készletet és egy Apache Spark készletet.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 08/27/2020
ms.openlocfilehash: 56292d3e8ba4c9ec89d73f10640264c178f8a9a7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255018"
---
# <a name="create-a-synapse-workspace"></a>Synapse-munkaterület létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet, egy SQL-készletet és egy Apache Spark készletet. 

## <a name="create-a-synapse-workspace"></a>Synapse-munkaterület létrehozása

1. Nyissa meg a [Azure Portal](https://portal.azure.com), és a legfelső szintű keresés a **szinapszisban**.
1. A keresési eredmények között, a **szolgáltatások**területen válassza az **Azure szinapszis Analytics (munkaterületek előzetes verzió)** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget a munkaterület létrehozásához a következő beállítások használatával:

    |Tab|Beállítás | Ajánlott érték | Leírás |
    |---|---|---|---|
    |Alapvető beállítások|**Munkaterület neve**|Bármilyen nevet megadhat.| Ebben a dokumentumban a **sajátmunkaterület**-t fogjuk használni.|
    |Alapvető beállítások|**Régió**|A Storage-fiók régiójának egyeztetése.|

1. Munkaterület létrehozásához ADLSGEN2-fiókra van szükség. A legegyszerűbb lehetőség, hogy újat hozzon létre. Ha újra szeretné használni a meglévőket, néhány további konfigurálást is végre kell hajtania. 
1. 1. lehetőség új ADLSGEN2-fiók létrehozása 
    1. A **2. generációs Data Lake Storage kiválasztása**területen kattintson az **új létrehozása** elemre, és nevezze el **contosolake**.
    1. A **2. generációs Data Lake Storage kiválasztása**területen kattintson a **fájlrendszer** elemre, és nevezze el a **felhasználók**nevet.
1. 2. lehetőség: a **Storage-fiók előkészítési** utasításai a dokumentum alján találhatók.
1. Az Azure szinapszis-munkaterülete ezt a Storage-fiókot fogja használni az "elsődleges" Storage-fiók és a munkaterület-adattárolási tároló számára. A munkaterület Apache Spark táblákban tárolja az adattárakat. Egy **/Synapse/workspacename**nevű mappában tárolja a Spark-alkalmazás naplóit.
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A munkaterület pár percen belül elkészül.


## <a name="open-synapse-studio"></a>A szinapszis Studio megnyitása

Az Azure szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:

* Nyissa meg a szinapszis munkaterületet a [Azure Portal](https://portal.azure.com). Az **Áttekintés** szakasz tetején válassza a **szinapszis Studio elindítása**lehetőséget.
* Lépjen a `https://web.azuresynapse.net` munkaterületre, és jelentkezzen be.

## <a name="create-a-sql-pool"></a>SQL-készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza az **Manage**  >  **SQL-készletek**kezelése lehetőséget.
1. Válassza az **új** lehetőséget, és adja meg a következő beállításokat:

    |Beállítás | Ajánlott érték | 
    |---|---|---|
    |**SQL-készlet neve**| **SQLDB1**|
    |**Teljesítményszint**|**DW100C**|
    |||

1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. Az SQL-készlet néhány percen belül elkészül. Az SQL-készlet társítva van egy **SQLDB1**néven is ismert SQL Pool-adatbázishoz.

Az SQL-készlet számlázható erőforrásokat használ, amíg az aktív. A készletet később is szüneteltetheti a költségek csökkentése érdekében.

## <a name="create-an-apache-spark-pool"></a>Apache Spark-készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza a **Manage**  >  **Apache Spark készletek**kezelése lehetőséget.
1. Válassza az **új** lehetőséget, és adja meg a következő beállításokat:

    |Beállítás | Ajánlott érték | 
    |---|---|---|
    |**Apache Spark készlet neve**|**Spark1**
    |**Csomópont mérete**| **Kicsi**|
    |**Csomópontok száma**| Állítsa be a minimumot 3 értékre, a maximumot 3 értékre|

1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A Apache Spark-készlet néhány másodpercen belül elkészül.

> [!NOTE]
> A név ellenére egy Apache Spark-készlet nem olyan, mint egy SQL-készlet. Ez csak néhány alapvető metaadat, amelyet az Azure szinapszis-munkaterület használatának elvégzéséhez használ a Sparktal való kommunikációhoz.

Mivel ezek a metaadatok, a Spark-készletek nem indíthatók el és nem állíthatók le.

Amikor Spark-tevékenységet hajt végre az Azure Szinapszisban, meg kell adnia a használni kívánt Spark-készletet. A készlet azt jelzi, hogy az Azure szinapszis hány Spark-erőforrást használ. Ön csak a felhasznált erőforrásokért fizet. Ha aktívan abbahagyja a készlet használatát, az erőforrások automatikusan időtúllépést és újrahasznosítást végeznek.

> [!NOTE]
> A Spark-adatbázisok a Spark-készletektől függetlenül jönnek létre. A munkaterületnek mindig van egy **alapértelmezett**nevű Spark-adatbázisa. További Spark-adatbázisokat is létrehozhat.

## <a name="the-sql-on-demand-pool"></a>Az igény szerinti SQL-készlet

Minden munkaterülethez egy előre elkészített, **SQL on-demand**nevű készlet tartozik. Ez a készlet nem törölhető. Az igény szerinti SQL-készlet lehetővé teszi az SQL használatát anélkül, hogy létre kellene hoznia egy SQL-készletet az Azure Szinapszisban.

A más típusú készletektől eltérően az SQL igény szerinti számlázása a lekérdezés futtatásához beolvasott adatmennyiségen alapul, nem a lekérdezés végrehajtásához használt erőforrások számától.

* Az SQL on-demand saját SQL igény szerinti adatbázisokat tartalmaz, amelyek egymástól függetlenül léteznek bármely SQL igény szerinti készletből.
* A munkaterületnek mindig van egy SQL igény szerinti, **igény**szerinti SQL on-demand nevű készlete.

## <a name="prepare-a-storage-account"></a>Storage-fiók előkészítése

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Hozzon létre egy új Storage-fiókot, amely a következő beállításokkal rendelkezik:

    |Tab|Beállítás | Ajánlott érték | Leírás |
    |---|---|---|---|
    |Alapvető beállítások|**Tárfiók neve**| Válassza ki a kívánt nevet.| Ebben a dokumentumban a **contosolake**nevet fogjuk használni.|
    |Alapvető beállítások|**Fiók altípusa**| **StorageV2** ||
    |Alapvető beállítások|**Hely**|Válassza ki a kívánt helyet.| Javasoljuk, hogy az Azure szinapszis Analytics-munkaterülete és Azure Data Lake Storage Gen2 fiókja ugyanabban a régióban legyen.|
    |Felsőfokú|**Data Lake Storage Gen2**|**Engedélyezve**| Az Azure szinapszis csak olyan Storage-fiókokkal működik, amelyeken engedélyezve van ez a beállítás.|
    |||||

1. A Storage-fiók létrehozása után a bal oldali panelen válassza a **hozzáférés-vezérlés (iam)** lehetőséget. Ezután rendelje hozzá a következő szerepköröket, vagy győződjön meg arról, hogy már hozzá van rendelve:
    * Rendelje hozzá magát a **tulajdonosi** szerepkörhöz.
    * Rendeljen hozzá saját magát a **Storage blob-adat tulajdonosi** szerepköréhez.
1. A bal oldali ablaktáblán válassza a **tárolók** lehetőséget, és hozzon létre egy tárolót.
1. Megadhatja a tároló nevét. Ebben a dokumentumban a Container **Users**nevet fogjuk megkeresni.
1. Fogadja el az alapértelmezett **nyilvános hozzáférési szint**beállítást, majd válassza a **Létrehozás**lehetőséget.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>A Storage-fiókhoz való hozzáférés konfigurálása a munkaterületről

Előfordulhat, hogy az Azure szinapszis-munkaterülethez tartozó felügyelt identitások már hozzáférnek a Storage-fiókhoz. Az alábbi lépéseket követve győződjön meg arról, hogy:

1. Nyissa meg a munkaterülethez kiválasztott [Azure Portal](https://portal.azure.com) és elsődleges Storage-fiókot.
1. A bal oldali panelen válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. Rendelje hozzá a következő szerepköröket, vagy győződjön meg arról, hogy már hozzá van rendelve. Ugyanazt a nevet használjuk a munkaterület-identitáshoz és a munkaterület nevéhez.
    * A Storage- **blob adatközreműködői** szerepköréhez a Storage-fiókban rendeljen **sajátmunkaterület** a munkaterület-identitáshoz.
    * Rendelje hozzá a **sajátmunkaterület** a munkaterület neveként.

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Elemzés SQL-készlet használatával](get-started-analyze-sql-pool.md)
