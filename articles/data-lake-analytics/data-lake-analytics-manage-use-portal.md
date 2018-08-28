---
title: Az Azure Data Lake Analytics kezelése az Azure portal használatával
description: Ez a cikk ismerteti az Azure portal használata Data Lake Analytics-fiókok, adatforrások, felhasználók & feladatok kezelésére.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 1d49403ec04b2ec35291869385c316cb5ab3b0da
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044909"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Az Azure Portalon az Azure Data Lake Analytics kezelése
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk ismerteti az Azure Data Lake Analytics-fiókok, adatforrások, felhasználók és feladatok kezelése az Azure portal használatával.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Data Lake Analytics-fiókok kezelése

### <a name="create-an-account"></a>Fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **erőforrás létrehozása** > **intelligencia és elemzés** > **Data Lake Analytics**.
3. Adja meg az alábbi elemek értékeit: 
   1. **Név**: a Data Lake Analytics-fiók nevét.
   2. **Előfizetés**: a fiók az Azure-előfizetés.
   3. **Erőforráscsoport**: az Azure-erőforráscsoportot, amelyben a fiók létrehozásához. 
   4. **Hely**: a Data Lake Analytics-fiók az Azure-adatközpontban. 
   5. **Data Lake Store**: az alapértelmezett tároló a Data Lake Analytics-fiók használandó. Az Azure Data Lake Store-fiók és a Data Lake Analytics-fiók ugyanazon a helyen kell lennie.
4. Kattintson a **Create** (Létrehozás) gombra. 

### <a name="delete-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók törlése

Data Lake Analytics-fiók törlése, előtt törölje az alapértelmezett Data Lake Store-fiók.

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. Kattintson a **Törlés** gombra.
3. Írja be a fiók nevét.
4. Kattintson a **Törlés** gombra.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Adatforrások kezelése

A Data Lake Analytics támogatja a következő adatforrásokat:

* Data Lake Store
* Azure Storage

Az adatkezelő segítségével keresse meg az adatforrásokat, és alapvető felügyeleti műveletet végezzen. 

### <a name="add-a-data-source"></a>Adatforrás hozzáadása

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. Kattintson a **adatforrások**.
3. Kattintson a **adatforrás hozzáadása**.
    
   * Adjon hozzá egy Data Lake Store-fiókot, szüksége van a fiók neve és a fiók a lekérdezésére, a hozzáférést.
   * Adja hozzá az Azure Blob storage, szüksége van a tárfiók és a fiókkulcsot. Találja meg azokat, nyissa meg a tárfiók a portálon.

## <a name="set-up-firewall-rules"></a>Tűzfalszabályok beállítása

A Data Lake Analytics segítségével további zároljuk hozzáférést a Data Lake Analytics-fiók a hálózati szinten. Tűzfal engedélyezése, adja meg az IP-cím vagy IP-címtartomány megadása a megbízható ügyfelek számára. Miután engedélyezi ezeket a mértékeket, csak a definiált tartományon belüli IP-címek rendelkező ügyfelek csatlakozhat a tárolóban.

Ha más Azure-szolgáltatásokkal, például az Azure Data Factory vagy virtuális gépek, a Data Lake Analytics-fiókhoz kapcsolódik, győződjön meg arról, hogy **engedélyezése az Azure-szolgáltatások** be van kapcsolva **a**. 

### <a name="set-up-a-firewall-rule"></a>Tűzfalszabály beállítása

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. A bal oldali menüben kattintson **tűzfal**.

## <a name="add-a-new-user"></a>Új felhasználó hozzáadása

Használhatja a **felhasználó hozzáadása varázsló** , könnyedén üzembe helyezheti az új Data Lake-felhasználók.

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. A bal oldali alatt **bevezetés**, kattintson a **felhasználó hozzáadása varázsló**.
3. Válasszon ki egy felhasználót, és kattintson a **kiválasztása**.
4. Válasszon egy szerepkört, és kattintson **kiválasztása**. Az Azure Data Lake állíthat be egy új fejlesztő, válassza ki a **Data Lake Analytics-fejlesztő** szerepkör.
5. Válassza ki az U-SQL-adatbázisokra vonatkozó hozzáférés-vezérlési listák (ACL). Amikor már elégedett a kiválasztott beállításokat, kattintson az **kiválasztása**.
6. Válassza ki a hozzáférés-vezérlési fájlokat. Az alapértelmezett tároló nem a hozzáférés-vezérlési listákat a legfelső szintű mappa módosítása "/" és a vagy mappát. Kattintson a **Kiválasztás** gombra.
7. Tekintse át a kiválasztott módosításokat, és kattintson a **futtatása**.
8. Ha a varázsló végzett, kattintson a **kész**.

## <a name="manage-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés kezelése

Más Azure-szolgáltatásokhoz hasonlóan a szerepköralapú hozzáférés-vezérlés (RBAC) segítségével szabályozhatja, hogy a felhasználók hogyan használják a szolgáltatással.

A standard szintű RBAC-szerepkör az alábbi képességekkel rendelkezik:
* **Tulajdonos**: feladatok elküldéséhez, figyelheti a feladatokat, bármely felhasználó a feladatok megszakítása, és konfigurálja a fiókot.
* **Közreműködői**: feladatok elküldéséhez, figyelheti a feladatokat, bármely felhasználó a feladatok megszakítása, és konfigurálja a fiókot.
* **Olvasó**: figyelheti a feladatokat.

A Data Lake Analytics-fejlesztő szerepkör használatával a Data Lake Analytics szolgáltatással a U-SQL fejlesztők. Használhatja a Data Lake Analytics-fejlesztő szerepkört a következőre:
* Feladatok elküldéséhez.
* Feladat állapotát és minden olyan felhasználó által elküldött feladatok előrehaladásának figyelése.
* Tekintse meg a U-SQL-parancsfájlok bármely felhasználó által elküldött feladatok.
* Csak a saját feladatok megszakítása.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Felhasználók vagy biztonsági csoportok hozzáadása a Data Lake Analytics-fiók

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. Kattintson a **hozzáférés-vezérlés (IAM)** > **Hozzáadás**.
3. Válasszon ki egy szerepkört.
4. Felhasználó hozzáadása.
5. Kattintson az **OK** gombra.

>[!NOTE]
>Ha egy felhasználó vagy egy biztonsági csoportot kell feladatok elküldéséhez, azok a store-fiók engedéllyel is kell. További információkért lásd: [Secure Data Lake Store-ban tárolt adatok](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Feladatok kezelése

### <a name="submit-a-job"></a>Feladat elküldése

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.

2. Kattintson a **új feladat**. Konfigurálja az egyes feladatokhoz:

    1. **Feladat neve**: a feladat nevét.
    2. **Prioritás**: alacsonyabb sorszámok magasabb prioritással. Ha két feladatok várólistára kerülnek, az egy alacsonyabb prioritási értékkel rendelkező első futtatja.
    3. **Párhuzamosság**: lefoglalni a feladat a számítási feladatok maximális számát.

3. Kattintson a **Feladat elküldése** elemre.

### <a name="monitor-jobs"></a>Feladatok figyelése

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. Kattintson a **az összes feladat megtekintése**. Összes aktív és a nemrégiben befejezett feladatot a fiókban lévő listája látható.
3. Másik lehetőségként kattinthat **szűrő** nyújtanak a feladatok által **időtartomány**, **feladat neve**, és **Szerző** értékeket. 

### <a name="monitoring-pipeline-jobs"></a>Folyamat-feladatok figyelése
Folyamat részét képező feladatok működik együtt, egymás után általában egy adott forgatókönyv megvalósításához. Rendelkezhet például egy folyamatot, amely törli a, kinyeri, alakítja át, a customer insights használati összesíti. Folyamatfeladatok azonosított "Folyamat" tulajdonság használatával, ha a feladat el lett küldve. Az ADF V2 használatával ütemezett feladatok automatikusan lesz kitölti ezt a tulajdonságot. 

U-SQL feladatok, amelyek részei folyamatok listájának megtekintése: 

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókok.
2. Kattintson a **Insights feladat**. Az "Összes feladat" lapon lesz az alapértelmezett, futó, listáját tartalmazó várólistára helyezve, és befejeződött feladatok.
3. Kattintson a **Folyamatfeladatok** fülre. Folyamat-feladatok listája mellett minden egyes folyamat esetében összesített statisztikák jelennek meg.

### <a name="monitoring-recurring-jobs"></a>Ismétlődő feladatok figyelése
Ismétlődő feladat, amely ugyanazt a üzleti logikát tartalmaz, de különböző bemeneti adatokat használ, során minden alkalommal. Ideális esetben az ismétlődő feladatok mindig sikeres legyen, és viszonylag stabil végrehajtási ideje; Ezen viselkedés figyelése biztosítja a feladat állapota kifogástalan. Ismétlődő feladatok azonosítása a "Recurrence" tulajdonság használatával. Az ADF V2 használatával ütemezett feladatok automatikusan lesz kitölti ezt a tulajdonságot.

Ismétlődő U-SQL-feladatok listájának megtekintéséhez: 

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókok.
2. Kattintson a **Insights feladat**. Az "Összes feladat" lapon lesz az alapértelmezett, futó, listáját tartalmazó várólistára helyezve, és befejeződött feladatok.
3. Kattintson a **ismétlődő feladatok** fülre. Ismétlődő feladatok listája jelenik meg minden ismétlődő feladat összesített statisztikák együtt.

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Az Azure Data Lake Analytics kezelése az Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)
* [Házirendek használata Azure Data Lake Analytics kezelése](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-policies)
