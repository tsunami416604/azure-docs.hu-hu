---
title: A szinapszis munkaterület biztonságossá tétele (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan használhatja a szerepköröket és a hozzáférés-vezérlést a tevékenységek vezérléséhez és a szinapszis munkaterület adataihoz való hozzáféréshez.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 1a8023a700e83892fae9a16bb2bfbe2a2eb0b307
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85193413"
---
# <a name="secure-your-synapse-workspace-preview"></a>A szinapszis munkaterület biztonságossá tétele (előzetes verzió)

Ez a cikk bemutatja, hogyan használhatja a szerepköröket és a hozzáférés-vezérlést a tevékenységek vezérléséhez és az adateléréshez. Ezeket az utasításokat követve az Azure szinapszis Analytics hozzáférés-vezérlése egyszerűsített. A felhasználókat csak három biztonsági csoport egyikére kell felvennie és eltávolítania.

## <a name="overview"></a>Áttekintés

A szinapszis-munkaterület (előzetes verzió) biztonságossá tételéhez kövesse az alábbi elemek konfigurálásának mintáját:

- Azure-szerepkörök (például a beépítettek, mint a tulajdonos, közreműködő stb.)
- Szinapszis-szerepkörök – ezek a szerepkörök egyediek a szinapszisok számára, és nem az Azure szerepkörein alapulnak. A szerepkörök közül három:
  - Szinapszis-munkaterület rendszergazdája
  - Szinapszis SQL-rendszergazda
  - Az Azure szinapszis Analytics-rendszergazda Apache Spark
- Azure Data Lake Storage Gen 2 (ADLSGEN2) adathozzáférés-vezérlése.
- A szinapszis SQL és a Spark adatbázisok hozzáférés-vezérlése

## <a name="steps-to-secure-a-synapse-workspace"></a>A szinapszis-munkaterület biztonságossá tételének lépései

Ez a dokumentum szabványos neveket használ az utasítások egyszerűsítéséhez. Cserélje le azokat bármely tetszőleges névvel.

|Beállítás | Példaérték | Description |
| :------ | :-------------- | :---------- |
| **Szinapszis-munkaterület** | WS1 |  A szinapszis munkaterület neve. |
| **ADLSGEN2-fiók** | STG1 | A munkaterülethez használni kívánt ADLS-fiók. |
| **Tároló** | CNT1 | A STG1 azon tárolója, amelyet a munkaterület alapértelmezés szerint fog használni. |
| **Active Directory-bérlő** | contoso | az Active Directory-bérlő neve.|
||||

## <a name="step-1-set-up-security-groups"></a>1. lépés: biztonsági csoportok beállítása

Hozza létre és töltse fel a munkaterület három biztonsági csoportját:

- **WS1 \_ WSAdmins** – azoknak a felhasználóknak, akiknek teljes körű felügyeletet kell végezniük a munkaterület felett
- **WS1 \_ SparkAdmins** – azoknak a felhasználóknak, akik a munkaterület Spark-szempontjainak teljes körű felügyeletét szeretnék
- **WS1 \_ SQLAdmins** – a munkaterület SQL-szempontjait teljes körű vezérlést igénylő felhasználók számára
- **WS1- \_ WSAdmins** hozzáadása a **WS1 \_ SQLAdmins**
- **WS1- \_ WSAdmins** hozzáadása a **WS1 \_ SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>2. lépés: a Data Lake Storage Gen2-fiók előkészítése

A tárterülettel kapcsolatos információk azonosítása:

- A munkaterülethez használni kívánt ADLSGEN2-fiók. Ez a dokumentum meghívja a STG1.  A STG1 a munkaterület "elsődleges" Storage-fiókjának minősül.
- A WS1 belüli tároló, amelyet a szinapszis-munkaterület alapértelmezés szerint fog használni. Ez a dokumentum meghívja a CNT1.  Ez a tároló a következőhöz használható:
  - A Spark-táblák biztonsági mentésére szolgáló adatfájlok tárolása
  - Végrehajtási naplók a Spark-feladatokhoz

- A Azure Portal használatával rendelje hozzá a biztonsági csoportokat a következő szerepkörökhöz a CNT1

  - **WS1- \_ WSAdmins** társítása a **tárolási blob adatközreműködői** szerepkörhöz
  - **WS1- \_ SparkAdmins** társítása a **tárolási blob adatközreműködői** szerepkörhöz
  - **WS1- \_ SQLAdmins** társítása a **tárolási blob adatközreműködői** szerepkörhöz

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>3. lépés: a szinapszis-munkaterület létrehozása és konfigurálása

A Azure Portal hozzon létre egy szinapszis-munkaterületet:

- A munkaterület WS1 neve
- STG1 kiválasztása a Storage-fiókhoz
- Válassza a CNT1 lehetőséget a tárolóhoz, amelyet "fájlrendszerként" használ.
- WS1 megnyitása a szinapszis Studióban
- Válassza a **kezelés**  >  **Access Control** a biztonsági csoportok társítása a következő szinapszis-szerepkörökhöz lehetőséget.
  - **WS1- \_ WSAdmins** társítása a szinapszis-munkaterület rendszergazdái számára
  - **WS1- \_ SparkAdmins** kiosztása az szinapszis Spark-rendszergazdáknak
  - **WS1- \_ SQLAdmins** kiosztása a szinapszis SQL-rendszergazdáknak

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>4. lépés: a Data Lake Storage Gen2 konfigurálása a szinapszis munkaterület általi használatra

A szinapszis munkaterületnek hozzá kell férnie a STG1 és a CNT1, hogy képes legyen a folyamatok futtatására és rendszerfeladatok végrehajtására.

- Nyissa meg az Azure Portalt
- STG1 megkeresése
- Navigáljon a CNT1
- Győződjön meg arról, hogy a WS1 MSI-fájlja (Managed Service Identity) hozzá van rendelve a **Storage blob adatközreműködői** szerepkörhöz a CNT1
  - Ha nem látja a hozzárendelést, rendelje hozzá.
  - Az MSI neve megegyezik a munkaterülettel. Ebben az esetben a &quot; WS1 lenne &quot; .

## <a name="step-5-configure-admin-access-for-sql-pools"></a>5. lépés: rendszergazdai hozzáférés konfigurálása SQL-készletekhez

- Nyissa meg az Azure Portalt
- Navigáljon a WS1
- A **Beállítások**területen kattintson az **SQL Active Directory-rendszergazda** lehetőségre.
- Kattintson a **rendszergazda beállítása** elemre, és válassza a WS1 \_ SQLAdmins

## <a name="step-6-maintaining-access-control"></a>6. lépés: hozzáférés-vezérlés fenntartása

A konfigurálás befejeződött.

A felhasználók hozzáférésének kezeléséhez mostantól felhasználókat adhat hozzá és távolíthat el a három biztonsági csoporthoz.

Bár a felhasználók manuálisan is hozzárendelhetők a szinapszis-szerepkörökhöz, ha igen, az nem konfigurálja a dolgokat folyamatosan. Ehelyett csak felhasználókat adhat hozzá vagy távolíthat el a biztonsági csoportokhoz.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>7. lépés: a szerepkörökhöz tartozó felhasználók hozzáférésének ellenőrzése

Az egyes szerepkörökben lévő felhasználóknak a következő lépéseket kell végrehajtaniuk:

|   | Lépés | Munkaterület-rendszergazdák | Spark-rendszergazdák | SQL-rendszergazdák |
| --- | --- | --- | --- | --- |
| 1 | Parquet-fájl feltöltése a CNT1-be | IGEN | IGEN | IGEN |
| 2 | A Parquet-fájl beolvasása az SQL igény szerinti használatával | IGEN | NO | IGEN |
| 3 | Spark-készlet létrehozása | IGEN [1] | IGEN [1] | NO  |
| 4 | A Parquet-fájl beolvasása jegyzetfüzettel | IGEN | IGEN | NO |
| 5 | Hozzon létre egy folyamatot a jegyzetfüzetből, és indítsa el a folyamatot most | IGEN | NO | NO |
| 6 | Hozzon létre egy SQL-készletet, és futtasson egy SQL-parancsfájlt, például &quot; válassza az 1 elemet&quot; | IGEN [1] | NO | IGEN [1] |

> [!NOTE]
> [1] SQL-vagy Spark-készletek létrehozásához a felhasználónak legalább közreműködő szerepkörrel kell rendelkeznie a szinapszis munkaterületen.
> [!TIP]
>
> - A szerepkörtől függően bizonyos lépések szándékosan nem engedélyezettek.
> - Ne feledje, hogy bizonyos feladatok sikertelenek lehetnek, ha a biztonság nem volt teljesen konfigurálva. Ezeket a feladatokat a táblázatban kell feltüntetni.

## <a name="step-8-network-security"></a>8. lépés: hálózati biztonság

A munkaterület tűzfalának, a virtuális hálózatnak és a [privát kapcsolatnak](../../azure-sql/database/private-endpoint-overview.md)a konfigurálásához.

## <a name="step-9-completion"></a>9. lépés: Befejezés

A munkaterület mostantól teljesen konfigurálva van és biztonságos.

## <a name="how-roles-interact-with-synapse-studio"></a>A szerepkörök interakciója a szinapszis Studióval

A szinapszis Studio a felhasználói szerepkörök alapján eltérően fog működni. Előfordulhat, hogy egyes elemek el lesznek rejtve vagy le vannak tiltva, ha a felhasználó nincs olyan szerepkörhöz rendelve, amely megfelelő hozzáférést biztosít. A következő táblázat összefoglalja a szinapszis Studióra gyakorolt hatást.

| Tevékenység | Munkaterület-rendszergazdák | Spark-rendszergazdák | SQL-rendszergazdák |
| --- | --- | --- | --- |
| A szinapszis Studio megnyitása | IGEN | IGEN | IGEN |
| Kezdőlap hub megtekintése | IGEN | IGEN | IGEN |
| Adatközpont megtekintése | IGEN | IGEN | IGEN |
| Adatközpont/lásd a csatolt ADLS Gen2 fiókokat és tárolókat | IGEN [1] | IGEN [1] | IGEN [1] |
| Adatközpont/adatbázis-információk | IGEN | IGEN | IGEN |
| Adatközpont/objektumok megjelenítése az adatbázisokban | IGEN | IGEN | IGEN |
| Adatközpont/hozzáférési információ az SQL-készlet adatbázisaiban | IGEN   | NO   | IGEN   |
| Adatközpont/hozzáférés az SQL igény szerinti adatbázisaihoz | IGEN [2]  | NO  | IGEN [2]  |
| Adatközpont/hozzáférési információ a Spark-adatbázisokban | IGEN [2] | IGEN [2] | IGEN [2] |
| A fejlesztés központ használata | IGEN | IGEN | IGEN |
| Hub/Author SQL-parancsfájlok fejlesztése | IGEN | NO | IGEN |
| Hub/Author Spark-feladatdefiníciók fejlesztése | IGEN | IGEN | NO |
| Hub/Author notebookok fejlesztése | IGEN | IGEN | NO |
| Hub/Author adatfolyamok fejlesztése | IGEN | NO | NO |
| A hangszerelő központ használata | IGEN | IGEN | IGEN |
| Hubok összehangolása/folyamatok használata | IGEN | NO | NO |
| A Manage hub használata | IGEN | IGEN | IGEN |
| Hub/SQL-készletek kezelése | IGEN | NO | IGEN |
| Hub/Spark-készletek kezelése | IGEN | IGEN | NO |
| Hub/triggerek kezelése | IGEN | NO | NO |
| Hub/társított szolgáltatások kezelése | IGEN | IGEN | IGEN |
| Hub/Access Control kezelése (felhasználók társítása a szinapszis-munkaterület szerepköreihez) | IGEN | NO | NO |
| Hub/Integration Runtimes kezelése | IGEN | IGEN | IGEN |
| A figyelő központ használata | IGEN | IGEN | IGEN |
| Központ/előkészítés/folyamat futtatásának figyelése  | IGEN | NO | NO |
| Központ/előkészítés/trigger-futtatások figyelése  | IGEN | NO | NO |
| Hub/előkészítési/integrációs modulok figyelése  | IGEN | IGEN | IGEN |
| Hub/tevékenységek/Spark-alkalmazások figyelése | IGEN | IGEN | NO  |
| Központ/tevékenységek/SQL-kérelmek figyelése | IGEN | NO | IGEN |
| Hub/tevékenységek/Spark-készletek figyelése | IGEN | IGEN | NO  |
| Hub/triggerek figyelése | IGEN | NO | NO |
| Hub/társított szolgáltatások kezelése | IGEN | IGEN | IGEN |
| Hub/Access Control kezelése (felhasználók társítása a szinapszis-munkaterület szerepköreihez) | IGEN | NO | NO |
| Hub/Integration Runtimes kezelése | IGEN | IGEN | IGEN |


> [!NOTE]
> [1] a tárolókban lévő adatokhoz való hozzáférés a ADLS Gen2 hozzáférés-vezérléstől függ. </br>
> [2] az SQL OD-táblák és a Spark-táblázatok tárolja az ADLS Gen2 és a hozzáféréshez szükséges megfelelő engedélyeket a ADLS Gen2.

## <a name="next-steps"></a>További lépések

Szinapszis- [munkaterület](../quickstart-create-workspace.md) létrehozása
