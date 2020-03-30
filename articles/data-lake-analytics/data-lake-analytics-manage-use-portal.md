---
title: Az Azure Data Lake Analytics kezelése az Azure Portal használatával
description: Ez a cikk bemutatja, hogyan használhatja az Azure Portalon a Data Lake Analytics-fiókok, adatforrások, felhasználók, & feladatok kezelésére.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8a6b7cb3fd45e17b84519efcaa826b569083e156
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265700"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Az Azure Data Lake Analytics kezelése az Azure Portal használatával
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk ismerteti, hogyan kezelheti az Azure Data Lake Analytics-fiókok, adatforrások, felhasználók és feladatok az Azure Portal használatával.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Data Lake Analytics-fiókok kezelése

### <a name="create-an-account"></a>Fiók létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Kattintson az >  **Erőforrás-intelligencia** > **létrehozása + elemzés,****a Data Lake Analytics**elemre.
3. Adja meg az alábbi elemek értékeit: 
   1. **Név**: A Data Lake Analytics-fiók neve.
   2. **Előfizetés**: A fiókhoz használt Azure-előfizetés.
   3. **Erőforráscsoport:** Az Azure erőforráscsoport, amelyben a fiók létrehozásához. 
   4. **Hely:** Az Azure-adatközpont a Data Lake Analytics-fiókhoz. 
   5. **Data Lake Store**: A Data Lake Analytics-fiók alapértelmezett tárolója. Az Azure Data Lake Store-fiók és a Data Lake Analytics-fiók ugyanazon a helyen kell lennie.
4. Kattintson **a Létrehozás gombra.** 

### <a name="delete-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók törlése

A Data Lake Analytics-fiók törlése előtt törölje az alapértelmezett Data Lake Store-fiókot.

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. Kattintson a **Törlés** gombra.
3. Írja be a fiók nevét.
4. Kattintson a **Törlés** gombra.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Adatforrások kezelése

A Data Lake Analytics a következő adatforrásokat támogatja:

* Data Lake Store
* Azure Storage

Az Adatkezelő segítségével tallózhat az adatforrások között, és alapvető fájlkezelési műveleteket hajthat végre. 

### <a name="add-a-data-source"></a>Adatforrások felvétele

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. Kattintson **az Adatforrások gombra.**
3. Kattintson **az Adatforrás hozzáadása gombra.**
    
   * Data Lake Áruházbeli fiók hozzáadásához a fiók nevére és a fiókhoz való hozzáférésre van szükség ahhoz, hogy lekérdezhesse azt.
   * Az Azure Blob-tárhozzáadásaa szükséges a tárfiók és a fiókkulcs hozzáadása. A keresésükhöz nyissa meg a portálon lévő tárfiókot.

## <a name="set-up-firewall-rules"></a>Tűzfalszabályok beállítása

A Data Lake Analytics segítségével hálózati szinten további zárolást biztosíthat a Data Lake Analytics-fiókjához. Engedélyezheti a tűzfalat, ip-címet adhat meg, vagy ip-címtartományt adhat meg a megbízható ügyfelek számára. Miután engedélyezte ezeket a mértékeket, csak azok az ügyfelek csatlakozhatnak az üzlethez, amelyek a megadott tartományon belül rendelkeznek az IP-címekkel.

Ha más Azure-szolgáltatások, például az Azure Data Factory vagy a virtuális gépek is csatlakoznak a Data Lake Analytics-fiókhoz, győződjön meg arról, hogy az **Azure-szolgáltatások engedélyezése** be van **kapcsolva.** 

### <a name="set-up-a-firewall-rule"></a>Tűzfalszabály beállítása

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. A bal oldali menüben kattintson a **Tűzfal parancsra.**

## <a name="add-a-new-user"></a>Új felhasználó hozzáadása

A Felhasználó **hozzáadása varázslóval** egyszerűen hozhat létre új Data Lake-felhasználókat.

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. A bal oldalon az **Első lépések**csoportban kattintson a Felhasználó hozzáadása **varázsló gombra.**
3. Jelöljön ki egy felhasználót, majd kattintson **a Kijelölés gombra.**
4. Jelöljön ki egy szerepkört, majd kattintson **a Kijelölés gombra.** Ha új fejlesztőt szeretne beállítani az Azure Data Lake használatára, válassza a **Data Lake Analytics fejlesztői** szerepkört.
5. Jelölje ki az U-SQL adatbázisok hozzáférés-vezérlési listáit (Hozzáférés-vezérlési listáit). Ha elégedett a választásokkal, kattintson a **Kijelölés gombra.**
6. Jelölje ki a fájlok acl-jait. Az alapértelmezett tároló esetében ne módosítsa az ACL-eket a gyökérmappához "/" és a /system mappához. Kattintson a **Kiválasztás** gombra.
7. Tekintse át az összes kijelölt módosítást, majd kattintson a **Futtatás gombra.**
8. Amikor a varázsló elkészült, kattintson a **Kész gombra.**

## <a name="manage-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés kezelése

Más Azure-szolgáltatásokhoz hasonlóan a szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozhatja, hogy a felhasználók hogyan használják a szolgáltatást.

A szabványos RBAC-szerepkörök a következő képességekkel rendelkeznek:
* **Tulajdonos:** Elküldheti a feladatokat, figyelheti a feladatokat, megszüntetheti a feladatokat bármely felhasználótól, és konfigurálhatja a fiókot.
* **Közreműködő**: Feladatok küldése, feladatok figyelése, bármely felhasználó feladatának megszakítása és a fiók konfigurálása.
* **Olvasó**: Nyomon követheti a feladatokat.

A Data Lake Analytics fejlesztői szerepkörrel engedélyezheti, hogy az U-SQL-fejlesztők használhassák a Data Lake Analytics szolgáltatást. A Data Lake Analytics fejlesztői szerepköre a következőkre használható:
* Feladatok beküldése.
* A feladat állapotának és a bármely felhasználó által beküldött feladatok előrehaladásának figyelése.
* Tekintse meg az U-SQL-parancsfájlokat bármely felhasználó által beküldött feladatokból.
* Csak a saját feladatait mondja le.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Felhasználók vagy biztonsági csoportok hozzáadása Data Lake Analytics-fiókhoz

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. Kattintson **a Hozzáférés-vezérlés (IAM)** > **Szerepkör-hozzárendelés hozzáadása parancsra.**
3. Jelöljön ki egy szerepkört.
4. Adjon hozzá egy felhasználót.
5. Kattintson az **OK** gombra.

>[!NOTE]
>Ha egy felhasználónak vagy egy biztonsági csoportnak feladatokat kell benyújtania, akkor az áruházi fiókhoz is engedélyt kell kérnie. További információ: [Biztonságos adatok a Data Lake Store-ban.](../data-lake-store/data-lake-store-secure-data.md)
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Feladatok kezelése

### <a name="submit-a-job"></a>Feladat beküldése

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.

2. Kattintson **az Új feladat gombra.** Minden feladathoz konfigurálja a következőket:

    1. **Feladat neve**: A feladat neve.
    2. **Prioritás**: Az alacsonyabb számok elsőbbséget élveznek. Ha két feladat várakozik, az alacsonyabb prioritású értékkel rendelkező feladat fut először.
    3. **Párhuzamosság:** A feladathoz lefoglalandó számítási folyamatok maximális száma.

3. Kattintson a **Feladat elküldése** elemre.

### <a name="monitor-jobs"></a>Feladatok figyelése

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. Kattintson **az Összes feladat megtekintése gombra.** Megjelenik a fiókban lévő összes aktív és nemrég befejezett feladat listája.
3. Szükség esetén a **Szűrő** gombra kattintva megkeresheti a feladatokat **az Időtartomány,** **a Feladat neve**és a **Szerző** értéke szerint. 

### <a name="monitoring-pipeline-jobs"></a>Folyamatfeladatok figyelése
A folyamat részét használó feladatok általában egymás után dolgoznak egy adott forgatókönyv megvalósításához. Például lehet egy folyamat, amely tisztítja, kinyeri, átalakítja, összesíti a használatot az ügyfél-elemzésekhez. A folyamatfeladatok a "Pipeline" tulajdonság használatával kerülnek azonosításra a feladat elküldésekor. Az ADF V2 használatával ütemezett feladatok automatikusan feltöltik ezt a tulajdonságot. 

A folyamatok részét tartalmazó U-SQL-feladatok listájának megtekintése: 

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókokat.
2. Kattintson **a Feladatelemzések gombra.** A "Minden feladat" lap alapértelmezett lesz, és a futó, várólistára helyezett és a leállított feladatok listáját jeleníti meg.
3. Kattintson a **Folyamatfolyamatok** fülre. A folyamatfeladatok listája jelenik meg az egyes folyamatok összesített statisztikáival együtt.

### <a name="monitoring-recurring-jobs"></a>Ismétlődő feladatok figyelése
Az ismétlődő feladat az, amely ugyanazt az üzleti logikát, de különböző bemeneti adatokat használ minden egyes futtatásakor. Ideális esetben az ismétlődő feladatoknak mindig sikeresnek kell lenniük, és viszonylag stabil végrehajtási idővel kell rendelkezniük; ezeknek a viselkedéseknek a figyelése segít biztosítani, hogy a feladat kifogástalan legyen. Az ismétlődő feladatok azonosítása az "Ismétlődés" tulajdonság használatával lesz azonosítva. Az ADF V2 használatával ütemezett feladatok automatikusan feltöltik ezt a tulajdonságot.

Ismétlődő U-SQL-feladatok listájának megtekintése: 

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókokat.
2. Kattintson **a Feladatelemzések gombra.** A "Minden feladat" lap alapértelmezett lesz, és a futó, várólistára helyezett és a leállított feladatok listáját jeleníti meg.
3. Kattintson az **Ismétlődő feladatok** fülre. Megjelenik az ismétlődő feladatok listája az egyes ismétlődő feladatok összesített statisztikáival együtt.

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Az Azure Data Lake Analytics kezelése az Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)
* [Az Azure Data Lake Analytics kezelése szabályzatokkal](data-lake-analytics-account-policies.md)
