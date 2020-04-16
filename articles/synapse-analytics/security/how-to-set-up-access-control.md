---
title: A Szinapsze-munkaterület védelme (előzetes verzió)
description: Ez a cikk bemutatja, hogyan használhatja a szerepköröket és a hozzáférés-vezérlést a tevékenységek és az adatokhoz való hozzáférés szabályozásához a Synapse-munkaterületen.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 94699f2153fa8d1df3ab85c184f32792c7ae0b59
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428874"
---
# <a name="secure-your-synapse-workspace-preview"></a>A Szinapsze-munkaterület védelme (előzetes verzió)

Ez a cikk bemutatja, hogyan használhatja a szerepköröket és a hozzáférés-vezérlést a tevékenységek és az adatokhoz való hozzáférés vezérléséhez. Az alábbi utasításokat követve az Azure Synapse Analytics hozzáférés-vezérlése egyszerűsödik. Csak felhasználókat kell hozzáadnia és eltávolítania a három biztonsági csoport egyikéhez.

## <a name="overview"></a>Áttekintés

A Synapse-munkaterület (előzetes verzió) védelméhez a következő elemek konfigurálásának mintáját kell követnie:

- Azure-szerepkörök (például a beépített szerepkörök, például tulajdonos, közreműködő stb.)
- Szinapszis szerepkörök – ezek a szerepkörök egyediek a Szinapsz, és nem alapulnak Azure-szerepkörök. A következő szerepek közül három létezik:
  - Szinapszis munkaterület-rendszergazda
  - Szinapszis SQL-rendszergazda
  - Synapse Spark-rendszergazda
- Hozzáférés-vezérlés az adatok az Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Hozzáférés-vezérlés a Synapse SQL és Spark-adatbázisokhoz

## <a name="steps-to-secure-a-synapse-workspace"></a>A Szinapsze-munkaterület védelmének lépései

Ez a dokumentum szabványos neveket használ az utasítások egyszerűsítése érdekében. Cserélje le őket bármilyen nevet, amelyet választott.

|Beállítás | Példaérték | Leírás |
| :------ | :-------------- | :---------- |
| **Szinapszis munkaterület** | WS1 |  A Synapse-munkaterület neve. |
| **ADLSGEN2-fiók** | STG1 (1. sz.) | A munkaterülettel használandó ADLS-fiók. |
| **Konténer** | CNT1 | Az STG1 tárolója, amelyet a munkaterület alapértelmezés szerint használni fog. |
| **Active directory bérlője** | contoso | az active directory bérlői nevét.|
||||

## <a name="step-1-set-up-security-groups"></a>1. LÉPÉS: Biztonsági csoportok beállítása

Három biztonsági csoport létrehozása és feltöltése a munkaterülethez:

- **WS1\_WSAdmins** – azoknak a felhasználóknak, akiknek teljes ellenőrzésre van szükségük a munkaterület felett
- **WS1\_SparkAdmins** – azoknak a felhasználóknak, akiknek teljes körű ellenőrzésre van szükségük a munkaterület Spark-aspektusai felett
- **WS1\_SQLAdmins** – azoknak a felhasználóknak, akiknek teljes ellenőrzésre van szükségük a munkaterület SQL-aspektusai felett
- **WS1-rendszergazdák\_** hozzáadása a **\_WS1 SQLAdmins szolgáltatáshoz**
- **WS1-rendszergazdák\_** hozzáadása a **\_WS1 SparkAdmins szolgáltatáshoz**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>2. lépés: A Data Lake Storage Gen2 fiók előkészítése

Azonosítsa a tárhelyre vonatkozó adatokat:

- A munkaterülethez használandó ADLSGEN2 fiók. Ez a dokumentum STG1-nek hívja.  Az STG1 a munkaterület "elsődleges" tárfiókának számít.
- A WS1-en belüli tároló, amelyet a Szinapsze-munkaterület alapértelmezés szerint használni fog. Ez a dokumentum CNT1-nek hívja.  Ez a tároló a következőkre szolgál:
  - A háttéradatfájlok tárolása Spark-táblákhoz
  - Végrehajtási naplók a Spark-feladatokhoz

- Az Azure Portal használatával rendelje hozzá a biztonsági csoportok a következő szerepkörök cnt1

  - **WS1\_WSAdminok hozzárendelése** a **Storage Blob Data Contributor** szerepkörhöz
  - **WS1\_SparkAdminok hozzárendelése** a **Storage Blob Data Contributor** szerepkörhöz
  - **WS1\_SQLAdminok hozzárendelése** a **Storage Blob Data Contributor** szerepkörhöz

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>3. LÉPÉS: A Szinapszis-munkaterület létrehozása és konfigurálása

Az Azure Portalon hozzon létre egy Synapse-munkaterületet:

- A munkaterület elnevezése WS1
- Válassza az STG1 lehetőséget a Storage-fiókhoz
- Válassza a CNT1 lehetőséget a "fájlrendszerként" használt tárolóhoz.
- Ws1 megnyitása a Synapse Stúdióban
- Válassza a**Hozzáférés-vezérlés** **kezelése** > lehetőséget, és rendelje hozzá a biztonsági csoportokat a következő Szinapsze szerepkörökhöz.
  - **WS1\_WSAdminok hozzárendelése** a Synapse-munkaterület rendszergazdáihoz
  - **WS1\_SparkAdmins hozzárendelése** a Synapse Spark-rendszergazdákhoz
  - **WS1\_SQLAdminok hozzárendelése** a Synapse SQL-rendszergazdákhoz

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>4. LÉPÉS: A Data Lake Storage Gen2 konfigurálása a Synapse munkaterület számára

A Synapse munkaterületnek hozzá kell férnie az STG1-hez és a CNT1-hez, hogy futófolyamatokat futtathasson és rendszerfeladatokat hajthat végre.

- Az Azure-portál megnyitása
- STG1 megkeresése
- Navigálás a CNT1-re
- Győződjön meg arról, hogy a WS1-hez tartozó MSI hozzá van rendelve az **Azure Blob data contributor** szerepkörhöz a CNT1-en
  - Ha nem látja hozzárendelve, rendelje hozzá.
  - Az MSI neve megegyezik a munkaterület nevével. Ebben az esetben lenne &quot;WS1&quot;.

## <a name="step-5-configure-admin-access-for-sql-pools"></a>5. LÉPÉS: Rendszergazdai hozzáférés konfigurálása SQL-készletekhez

- Az Azure-portál megnyitása
- Navigálás a WS1-re
- A **Beállítások csoportban**kattintson az **SQL Active Directory rendszergazdája** elemre.
- Kattintson **a Rendszergazda** \_beállítása gombra, és válassza a WS1 SQLAdmins lehetőséget.

## <a name="step-6-maintaining-access-control"></a>6. LÉPÉS: Hozzáférés-vezérlés fenntartása

A konfiguráció befejeződött.

Most a felhasználók hozzáférésének kezeléséhez hozzáadhat és eltávolíthat felhasználókat a három biztonsági csoporthoz.

Bár manuálisan rendelhet felhasználókat a Szinapszszerepkörökhöz, ha igen, akkor nem fogja következetesen konfigurálni a dolgokat. Ehelyett csak felhasználókat vegyen fel vagy távolítson el a biztonsági csoportokhoz.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>7. LÉPÉS: Hozzáférés ellenőrzése a szerepkörökben lévő felhasználók számára

Az egyes szerepkörekben lévő felhasználóknak a következő lépéseket kell végrehajtaniuk:

|   | Lépés | Munkaterület-rendszergazdák | Spark-rendszergazdák | SQL-rendszergazdák |
| --- | --- | --- | --- | --- |
| 1 | Parkettafájl feltöltése a CNT1 programba | IGEN | IGEN | IGEN |
| 2 | A parkettafájl olvasása igény szerinti SQL használatával | IGEN | NO | IGEN |
| 3 | Spark-készlet létrehozása | IGEN [1] | IGEN [1] | NO  |
| 4 | A füzettel rendelkező parkettafájl beolvasása | IGEN | IGEN | NO |
| 5 | Folyamat létrehozása a jegyzetfüzetből, és a folyamat futtatása most | IGEN | NO | NO |
| 6 | SQL-készlet létrehozása és SQL-parancsfájl, például &quot;SELECT 1 futtatása&quot; | IGEN [1] | NO | IGEN[1] |

> [!NOTE]
> [1] SQL- vagy Spark-készletek létrehozásához a felhasználónak legalább közreműködői szerepkörrel kell rendelkeznie a Szinapsze-munkaterületen.
> [!TIP]
>
> - A szerepkörtől függően egyes lépések szándékosan nem engedélyezettek.
> - Ne feledje, hogy egyes feladatok sikertelenek lehetnek, ha a biztonság nincs teljesen konfigurálva. Ezeket a feladatokat a táblázat ban feljegyezzük.

## <a name="step-8-network-security"></a>8. LÉPÉS: Hálózati biztonság

A munkaterülettűzfal, a virtuális hálózat és a [Privát kapcsolat konfigurálása.](../../sql-database/sql-database-private-endpoint-overview.md)

## <a name="step-9-completion"></a>9. LÉPÉS: Befejezés

A munkaterület most már teljesen konfigurálva és biztonságos.

## <a name="how-roles-interact-with-synapse-studio"></a>Hogyan hatnak a szerepkörök a Synapse Studio-ra?

A Synapse Studio a felhasználói szerepkörök alapján eltérően fog játszani. Egyes elemek rejtettek vagy le vannak tiltva, ha a felhasználó nincs hozzárendelve a megfelelő hozzáférést adó szerepkörökhöz. Az alábbi táblázat a Synapse Studio-ra gyakorolt hatást foglalja össze.

| Tevékenység | Munkaterület-rendszergazdák | Spark-rendszergazdák | SQL-rendszergazdák |
| --- | --- | --- | --- |
| Nyitott Synapse Studio | IGEN | IGEN | IGEN |
| Otthoni központ megtekintése | IGEN | IGEN | IGEN |
| Adatközpont megtekintése | IGEN | IGEN | IGEN |
| Data Hub / Lásd csatolt ADLSGen2 fiókok és tárolók | IGEN [1] | IGEN[1] | IGEN[1] |
| Data Hub / Lásd adatbázisok | IGEN | IGEN | IGEN |
| Data Hub / Objektumok megtekintése adatbázisokban | IGEN | IGEN | IGEN |
| Data Hub / Access-adatok SQL-készlet-adatbázisokban | IGEN   | NO   | IGEN   |
| Data Hub / Access adatok az SQL igény szerinti adatbázisaiban | IGEN [2]  | NO  | IGEN [2]  |
| Data Hub / Access-adatok a Spark-adatbázisokban | IGEN [2] | IGEN [2] | IGEN [2] |
| A Fejlesztés központ használata | IGEN | IGEN | IGEN |
| Hub/ author SQL-parancsfájlok fejlesztése | IGEN | NO | IGEN |
| Hub/ author Spark-feladatdefiníciók fejlesztése | IGEN | IGEN | NO |
| Hub / author notebookok fejlesztése | IGEN | IGEN | NO |
| Hub/ author dataflows fejlesztése | IGEN | NO | NO |
| Az Orchestrate hub használata | IGEN | IGEN | IGEN |
| Orchestrate hub / folyamatok használata | IGEN | NO | NO |
| A Kezelő központ használata | IGEN | IGEN | IGEN |
| Hub/ SQL-készletek kezelése | IGEN | NO | IGEN |
| Hub/ Spark-készletek kezelése | IGEN | IGEN | NO |
| Hub kezelése / Eseményindítók | IGEN | NO | NO |
| Hub/ Csatolt szolgáltatások kezelése | IGEN | IGEN | IGEN |
| Hub/Access Control kezelése (felhasználók hozzárendelése a Szinapszis munkaterületi szerepkörökhöz) | IGEN | NO | NO |
| Hub/ Integrációs runtimes kezelése | IGEN | IGEN | IGEN |

> [!NOTE]
> [1] A tárolókban lévő adatokhoz való hozzáférés az ADLSGen2 [2] SQL OD-tábláinak hozzáférés-vezérlésétől függ, és a Spark-táblák az ADLSGen2-ben tárolják az adataikat, és a hozzáféréshez megfelelő engedélyek szükségesek az ADLSGen2-hez.

## <a name="next-steps"></a>További lépések

[Szinapszis-munkaterület](../quickstart-create-workspace.md) létrehozása
