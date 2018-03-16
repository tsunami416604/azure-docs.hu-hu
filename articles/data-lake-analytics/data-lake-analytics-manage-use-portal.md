---
title: "Azure Data Lake Analytics kezelése az Azure portál használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a Data Lake Analytics könyvvitelének, adatforrások, felhasználók és feladatok."
services: data-lake-analytics
documentationcenter: 
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
ms.openlocfilehash: 93815904e7e21e1ba8283d7a522297c7e3466702
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
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

## <a name="manage-policies"></a>Házirendek kezelése

### <a name="account-level-policies"></a>Fiók szintű szabályzatok

Ezek a házirendek a Data Lake Analytics-fiók levő összes feladatnak vonatkozik.

#### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>A Data Lake Analytics-fiók ausztráliai maximális száma
A szabályzatok vezérlik az Analytics-egységek (ausztráliai) használható a Data Lake Analytics-fiók teljes száma. Alapértelmezés szerint a értéke 250. Például, ha a változó értéke 250 ausztráliai, 250 fut egy feladat lehet ausztráliai vagy 25 futtató 10 feladatok rendelt minden ausztráliai. További feladatok tartalmazó várólistára kerülnek, a futó feladatok befejezéséig. Futó feladat befejeződése után ausztráliai felszabadítását feliratkozott az aszinkron feladatok futtatását.

A Data Lake Analytics-fiók ausztráliai számának módosításához:

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Tulajdonságok** elemre.
3. A **maximális ausztráliai**, a csúszkával kiválaszthat egy értéket, vagy a mezőben adja meg az értéket. 
4. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Ha több, mint az alapértelmezett (250) kell ausztráliai, a portálon kattintson **súgó + támogatás** támogatási kérelmet küldeni. A Data Lake Analytics-fiókja elérhető ausztráliai száma növelhető.
>

#### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Az egyidejűleg futtatható feladatok maximális számát
A házirend szabályozza, hogy hány feladatok futtathatók egyszerre. Ez az érték alapértelmezés szerint 20 értéke. Ha a Data Lake Analytics ausztráliai elérhető, új feladatok ütemezésére addig, amíg a futó feladatok teljes száma eléri ezt a házirendet az érték. Elérte az egyidejűleg futtatható feladatok maximális száma, amikor további feladat várólistára prioritási sorrendben mindaddig, amíg legalább egy futó feladat befejeződik (attól függően, hogy AU rendelkezésre állás).

Az egyidejűleg futtatható feladatok számát módosítása:

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Tulajdonságok** elemre.
3. A **maximális számát a futó feladatok**, a csúszkával kiválaszthat egy értéket, vagy a mezőben adja meg az értéket. 
4. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Ha a több alapértelmezett (20) feladatok, a portál futtatásához szükséges, kattintson a **súgó + támogatás** támogatási kérelmet küldeni. A Data Lake Analytics-fiók egyidejűleg futtatható feladatok száma növelhető.
>

#### <a name="how-long-to-keep-job-metadata-and-resources"></a>Mennyi ideig tartsa feladat metaadatok és az erőforrásokhoz való 
Amikor a felhasználók a U-SQL feladatok futtatása, a Data Lake Analytics szolgáltatás megőrzi az összes kapcsolódó fájlt. Kapcsolódó fájlok közé tartoznak a U-SQL parancsfájl, a DLL-fájlok a U-SQL-parancsfájl, a lefordított erőforrások és a statisztika hivatkozik. A fájlok az alapértelmezett Azure Data Lake-tárfiókra /system/ mappájában. Ez az irányelv szabályozza, hogy mennyi ideig tárolja ezeket az erőforrásokat automatikusan törlés előtt (az alapértelmezett érték 30 nap). Ezeket a fájlokat is használhatja, a hibakeresés, valamint a teljesítményének hangolása, amely a későbbiekben lesz Újrafuttatja feladatok.

Mennyi ideig átállítása feladat metaadatok és erőforrások:

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Tulajdonságok** elemre.
3. A **Feladatlekérdezéseit megőrizni kívánt napok**, a csúszkával kiválaszthat egy értéket, vagy a mezőben adja meg az értéket.  
4. Kattintson a **Save** (Mentés) gombra.

### <a name="job-level-policies"></a>Feladat szintű szabályzatok
A feladat szintű házirendek szabályozhatja a maximális ausztráliai és a legnagyobb prioritású virtuális gép, amely az egyes felhasználók (vagy a meghatározott biztonsági csoportok tagjai) feladatok, ugyanúgy lehet beállítani. Ez lehetővé teszi a felhasználók felmerült kapcsolatos költségek szabályozását. Ezenkívül lehetővé teszi a hatást, amelyet az ütemezett feladatok lehet magas prioritású termelési feladatokhoz ugyanazt a Data Lake Analytics-fiókot a futó vezérlő.

A Data Lake Analytics van két házirend, amely a feladat szinten állíthatja be:

* **AU felső határ az egyes feladatok**: csak a küldje el, hogy ez a szám ausztráliai kell feladatok. Alapértelmezés szerint ez a korlátozás megegyezik a AU maximális száma a fiókot.
* **Prioritás**: csak a küldje el, amely kisebb vagy egyenlő az érték a prioritású feladatok. Vegye figyelembe, hogy a nagyobb szám azt jelenti, hogy a kisebb prioritással. Alapértelmezés szerint ennek értéke 1, amely a lehető legmagasabb prioritású.

Nincs minden fiókhoz beállított alapértelmezett házirend. Az alapértelmezett házirendet a fiók minden felhasználójára vonatkoznak. További házirendjeinek beállítása meghatározott felhasználókhoz és csoportokhoz. 

> [!NOTE]
> Fiók szintű és a feladat szintű házirendeket alkalmazza egyszerre.
>

#### <a name="add-a-policy-for-a-specific-user-or-group"></a>Egy szabályzat egy adott felhasználó vagy csoport hozzáadása

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Tulajdonságok** elemre.
3. A **feladat elküldése korlátok**, kattintson a **házirend hozzáadása** gombra. Ezután válassza ki vagy adja meg a következő beállításokat:
    1. **Házirend neve számítási**: Adja meg a házirend nevét, a házirend céljának arra az esetre.
    2. **Válassza ki a felhasználó vagy csoport**: válassza ki a felhasználó vagy csoport a házirend vonatkozik.
    3. **A feladat AU-korlátjának beállítása**: állítsa be a kijelölt felhasználót vagy csoportot a AU korlátozás.
    4. **A prioritás-korlátjának beállítása**: állítsa be a kijelölt felhasználót vagy csoportot a prioritás korlátozás.

4. Kattintson az **OK** gombra.

5. Az új házirend megjelenik a **alapértelmezett** házirend a tábla **feladat elküldése korlátok**. 

#### <a name="delete-or-edit-an-existing-policy"></a>Törölje vagy egy meglévő házirend szerkesztése

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Tulajdonságok** elemre.
3. A **feladat elküldése korlátok**, keresse meg a szerkeszteni kívánt házirendet.
4.  Hogy a **törlése** és **szerkesztése** beállítások, a tábla a jobb oldali oszlopban kattintson **...** .

### <a name="additional-resources-for-job-policies"></a>További erőforrásokat a feladat házirendek
* [Házirend által írt blogbejegyzés áttekintése](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Fiók szintű szabályzatok által írt blogbejegyzés](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Feladat szintű szabályzatok által írt blogbejegyzés](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Ismerkedés a Data Lake Analytics az Azure portál használatával](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics kezelése az Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)

