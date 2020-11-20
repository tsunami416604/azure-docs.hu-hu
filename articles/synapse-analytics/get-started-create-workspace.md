---
title: 'Gyors útmutató: az első lépések – szinapszis-munkaterület létrehozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet, egy dedikált SQL-készletet és egy kiszolgáló nélküli Apache Spark készletet.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: c15750a7a63293994ff7c6b68cb85a5066acee90
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981136"
---
# <a name="creating-a-synapse-workspace"></a>Szinapszis-munkaterület létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet, egy dedikált SQL-készletet és egy kiszolgáló nélküli Apache Spark készletet. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek elvégzéséhez hozzáféréssel kell rendelkeznie egy olyan erőforráscsoporthoz, amelyhez hozzá van rendelve a **tulajdonosi** szerepkör. Hozza létre a szinapszis munkaterületet ebben az erőforráscsoportban.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Szinapszis-munkaterület létrehozása a Azure Portalban

1. Nyissa meg a [Azure Portal](https://portal.azure.com), és a legfelső szintű keresés a **szinapszisban**.
1. A keresési eredmények között, a **szolgáltatások** területen válassza az **Azure szinapszis Analytics (munkaterületek előzetes verzió)** lehetőséget.
1. Munkaterület létrehozásához válassza a **Hozzáadás** lehetőséget.
1. Az **alapvető beállítások** területen adja meg a kívánt **előfizetést**, **erőforráscsoportot**, **régiót**, majd válassza ki a munkaterület nevét. Ebben az oktatóanyagban a **sajátmunkaterület**-t fogjuk használni.
1. Navigáljon a **2. generációs Data Lake Storage kiválasztásához**. 
1. Kattintson az **új létrehozása** elemre, és nevezze el **contosolake**.
1. Kattintson a **fájlrendszer** elemre, és nevezze el a **felhasználókat**. Ekkor létrejön egy **felhasználó** nevű tároló.
1. A munkaterület ezt a Storage-fiókot fogja használni az "elsődleges" Storage-fiók a Spark-táblák és a Spark-alkalmazások naplói számára.
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A munkaterület pár percen belül elkészül.

## <a name="open-synapse-studio"></a>A szinapszis Studio megnyitása

Az Azure szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:

* Nyissa meg a szinapszis munkaterületet a [Azure Portal](https://portal.azure.com). Az **Áttekintés** szakasz tetején válassza a **szinapszis Studio elindítása** lehetőséget.
* Lépjen a `https://web.azuresynapse.net` munkaterületre, és jelentkezzen be.

## <a name="create-a-dedicated-sql-pool"></a>Dedikált SQL-készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza az **Manage**  >  **SQL-készletek** kezelése lehetőséget.
1. **Új** kiválasztása
1. Az **SQL-készlet neve** területen válassza a **SQLPOOL1** lehetőséget.
1. A **teljesítmény szintjén** válassza a **DW100C** lehetőséget.
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A dedikált SQL-készlet néhány percen belül elkészül. A dedikált SQL-készlet egy dedikált SQL Pool-adatbázishoz van társítva, amely más néven **SQLPOOL1**.

A dedikált SQL-készletek számlázható erőforrásokat használnak, amíg aktívak. A készletet később is szüneteltetheti a költségek csökkentése érdekében.

## <a name="create-a-serverless-apache-spark-pool"></a>Kiszolgáló nélküli Apache Spark készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza a **Manage**  >  **Apache Spark készletek** kezelése lehetőséget.
1. **Új** kiválasztása 
1. **Apache Spark a készlet neve** mezőbe írja be a következőt: **Spark1**.
1. A **csomópont mérete** mezőben adja meg a **kis** értéket.
1. Ha a **csomópontok száma** a minimum 3 és a maximum 3 értéket állítja be
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A Apache Spark-készlet néhány másodpercen belül elkészül.

A Spark-készlet azt mutatja be, hogy az Azure szinapszis hány Spark-erőforrást használ. Ön csak a felhasznált erőforrásokért fizet. Ha aktívan abbahagyja a készlet használatát, az erőforrások automatikusan időtúllépést és újrahasznosítást végeznek.

## <a name="the-built-in-serverless-sql-pool"></a>A beépített kiszolgáló nélküli SQL-készlet

Minden munkaterülethez **beépített, beépített** készlet tartozik. Ez a készlet nem törölhető. A kiszolgáló nélküli SQL-készlet lehetővé teszi az SQL használatát anélkül, hogy létre kellene hoznia egy kiszolgáló nélküli SQL-készletet az Azure Szinapszisban. A dedikált SQL-készletektől eltérően a kiszolgáló nélküli SQL-készlet számlázása a lekérdezés futtatásához beolvasott adatmennyiségen alapul, nem a lekérdezés végrehajtásához használt erőforrások számától.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Elemzés dedikált SQL-készlet használatával](get-started-analyze-sql-pool.md)
