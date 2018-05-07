---
title: Azure Data Lake Analytics kezelése az Azure portál használatával |} Microsoft Docs
description: Megtudhatja, hogyan kezelheti a Data Lake Analytics könyvvitelének, adatforrások, felhasználók és feladatok.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: kfile
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 99b638279b38ce3b583208ec0b80617f9046a6d7
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="manage-azure-data-lake-analytics-by-using-the-azure-portal"></a>Azure Data Lake Analytics kezelése az Azure portál használatával
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Útmutató: Azure Data Lake Analytics fiókok, fiók adatforrások, felhasználók és feladatok kezelése az Azure portál használatával. Más eszközök használatával kapcsolatos kapcsolatos témakörök megtekintéséhez kattintson egy fülre az oldal tetején.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Data Lake Analytics-fiókok kezelése

### <a name="create-an-account"></a>Fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **hozzon létre egy erőforrást** > **Eszközintelligencia + analitika** > **Data Lake Analytics**.
3. Adja meg az alábbi elemek értékeit: 
   1. **Név**: a Data Lake Analytics-fiók nevét.
   2. **Előfizetés**: a fiók használható az Azure-előfizetés.
   3. **Erőforráscsoport**: az Azure-erőforráscsoportot a fiók létrehozásához. 
   4. **Hely**: az Azure-adatközpontban és a Data Lake Analytics-fiók. 
   5. **Data Lake Store**: az alapértelmezett tárolójába. a Data Lake Analytics-fiók használandó. Az Azure Data Lake Store-fiók és a Data Lake Analytics-fiók ugyanazon a helyen kell lennie.
4. Kattintson a **Create** (Létrehozás) gombra. 

### <a name="delete-a-data-lake-analytics-account"></a>A Data Lake Analytics-fiók törlése

Data Lake Analytics-fiók törlése előtt törölje az alapértelmezett Data Lake Store-fiók.

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Törlés** gombra.
3. Írja be azt a fióknevet.
4. Kattintson a **Törlés** gombra.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Adatforrások kezelése

A Data Lake Analytics a következő adatforrásokat támogatja:

* Data Lake Store
* Azure Storage

Adatok Intézővel keresse meg az adatforrások és alapvető felügyeleti műveletet végrehajtani. 

### <a name="add-a-data-source"></a>Egy adatforrás hozzáadása

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **adatforrások**.
3. Kattintson a **adja hozzá az adatforrást**.
    
   * Data Lake Store-fiók hozzáadásához van szüksége a fiók nevét és a fiók tud lekérdezni az elérésére.
   * Adja hozzá az Azure Blob Storage tárolóban, szüksége van a tárfiók és a fiókkulcsot. Őket megkereséséhez nyissa meg a tárfiók a portálon.

## <a name="set-up-firewall-rules"></a>Tűzfalszabályok beállítása

A Data Lake Analytics segítségével hozzáférés további zárolása a Data Lake Analytics-fiók a hálózati szintű. Tűzfal engedélyezése, IP-cím vagy IP-címtartomány megadása a megbízható ügyfeleket. Ezeket a mértékeket engedélyezését követően a tároló csak a definiált tartományon belüli IP-címmel rendelkező ügyfelek is elérheti.

Ha más Azure-szolgáltatásokkal, például az Azure Data Factory vagy a virtuális gépek, a Data Lake Analytics-fiók, győződjön meg arról, hogy **Azure-szolgáltatások engedélyezése** van kapcsolva **a**. 

### <a name="set-up-a-firewall-rule"></a>Tűzfalszabály beállítása

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. A bal oldali menüben kattintson **tűzfal**.

## <a name="add-a-new-user"></a>Új felhasználó hozzáadása

Használhatja a **varázslót** új Data Lake-felhasználók egyszerűen kiépítéséhez.

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. A bal oldali alatt **bevezetés**, kattintson a **varázslót**.
3. Válasszon ki egy felhasználót, és kattintson **válasszon**.
4. Válassza ki a szerepkört, és kattintson **válasszon**. Hozzon létre egy új fejlesztői használja az Azure Data Lake, válassza ki a **Data Lake Analytics fejlesztői** szerepkör.
5. Válassza ki a U-SQL-adatbázisokhoz tartozó hozzáférés-vezérlési listák (ACL). Ha elégedett a kiválasztott beállításokat, kattintson **válasszon**.
6. Válassza ki a hozzáférés-vezérlési fájlok. Az alapértelmezett tároló ne módosítsa a hozzáférés-vezérlési listákat a gyökérmappához "/" és a vagy mappához. Kattintson a **Kiválasztás** gombra.
7. Tekintse át a kiválasztott módosításokat, és kattintson **futtatása**.
8. Ha a varázsló végzett, kattintson a **végzett**.

## <a name="manage-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés kezelése

Más Azure-szolgáltatásokkal, például a szerepköralapú hozzáférés-vezérlést (RBAC) segítségével szabályozhatja, hogy a felhasználók hogyan használják a szolgáltatással.

A szabványos RBAC-szerepkörök a következő képességekkel rendelkeznek:
* **Tulajdonos**: feladatok küldéséhez, feladatok figyelése, szakítsa bármely felhasználó, és konfigurálja a fiókot.
* **A közreműködői**: feladatok küldéséhez, feladatok figyelése, szakítsa bármely felhasználó, és konfigurálja a fiókot.
* **Olvasó**: figyelheti a feladat.

A Data Lake Analytics fejlesztői szerepkör használatával a Data Lake Analytics szolgáltatással a fejlesztők U-SQL. A Data Lake Analytics fejlesztői szerepkört is használhatja:
* Feladatok elküldéséhez.
* Feladat állapota és a felhasználó által küldött feladatok előrehaladásának figyeléséhez.
* Tekintse meg a U-SQL-parancsfájlokat, a feladatok bármely felhasználó által küldött.
* Csak a saját feladatok megszakítása

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Felhasználók vagy biztonsági csoportok hozzáadása a Data Lake Analytics-fiók

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **hozzáférés-vezérlés (IAM)** > **Hozzáadás**.
3. Szerepkör kiválasztása.
4. Felhasználó hozzáadása.
5. Kattintson az **OK** gombra.

>[!NOTE]
>Ha egy felhasználó vagy egy biztonsági csoportot kell feladatokat küldhet, is van szükségük a store-fiók engedélyt. További információkért lásd: [adatok a védelméről a Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Feladatok kezelése

### <a name="submit-a-job"></a>Feladat elküldése

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.

2. Kattintson a **új feladat**. Az egyes feladatokhoz konfigurálása:

    1. **Feladat neve**: a feladat nevét.
    2. **Prioritás**: alacsonyabb számok magasabb prioritással rendelkezik. Ha a két feladat várólistára kerülnek, alacsonyabb prioritású virtuális gép értékű első futtatja.
    3. **Párhuzamossági**: Ez a feladat foglalása számítási folyamatok maximális számának.

3. Kattintson a **Feladat elküldése** elemre.

### <a name="monitor-jobs"></a>Feladatok figyelése

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **összes feladat megtekintéséhez**. Az aktív és a közelmúltban befejezett levő összes feladatnak a fiók listája látható.
3. Ha úgy gondolja, a **szűrő** nyújtanak szerint a feladatokat **időtartomány**, **feladat neve**, és **Szerző** értékeket. 

### <a name="monitoring-pipeline-jobs"></a>Feldolgozási sor feladatok figyelése
Feladatok, amelyek egy folyamat részeként működik együtt, általában sorrendben történik, egy adott forgatókönyv megvalósításához. Például hogy egy folyamatot, amely megtisztítja, kiolvassa, átalakítja, összesíti az ügyfél insights használata. Feldolgozási sor feladatok azonosítja a "Futószalag" tulajdonsággal, ha a feladat el lett küldve. ADF V2 használatával ütemezett feladatok automatikusan lesz feltöltve ezt a tulajdonságot. 

A U-SQL feladatok folyamatok részét képező listájának megtekintéséhez: 

1. Az Azure portálon lépjen a Data Lake Analytics-fiókok.
2. Kattintson a **Insights feladat**. Az "Összes feladat" lapon lesz alapértelmezésnek megfelelően, futó, listáját tartalmazó sorba, és befejeződött feladatok.
3. Kattintson a **csővezeték feladatok** fülre. Feldolgozási sor feladatokra minden adatcsatorna összesített statisztikák együtt jelenik meg.

### <a name="monitoring-recurring-jobs"></a>Ismétlődő feladatok figyelése
Ismétlődő feladat egyike, amelyek azonos üzleti logikát tartalmaz, de különböző bemeneti adatokat használja, minden egyes futásakor. Ideális esetben ismétlődő feladatok mindig sikeres legyen, és viszonylag állandó végrehajtási idővel; Ezek közül a viselkedésmódok a figyelés segítségével ellenőrizze, hogy a feladat kifogástalan. Ismétlődő feladatok azonosítják az "Ismétlődési" tulajdonság használatával. ADF V2 használatával ütemezett feladatok automatikusan lesz feltöltve ezt a tulajdonságot.

Ismétlődő U-SQL-feladatok listájának megtekintéséhez: 

1. Az Azure portálon lépjen a Data Lake Analytics-fiókok.
2. Kattintson a **Insights feladat**. Az "Összes feladat" lapon lesz alapértelmezésnek megfelelően, futó, listáját tartalmazó sorba, és befejeződött feladatok.
3. Kattintson a **ismétlődő feladatok** fülre. Ismétlődő feladatok listája jelenik meg minden ismétlődő feladat összesített statisztikák együtt.

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Azure Data Lake Analytics kezelése az Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)
* [Azure Data Lake Analytics-szabályzatok kezelése](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-policies)
