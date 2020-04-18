---
title: Adatfelderítés és besorolás
description: Az Azure SQL Database és az Azure Synapse Analytics adatfelderítési besorolása &
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 599b2a280e386e49eb114f448f55b17ed7e823d7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616760"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Az Azure SQL Database és az Azure Synapse Analytics adatfelderítési besorolása &

Az Azure SQL Database be van építve az adatokfelderítés& besorolás. Speciális lehetőségeket biztosít az adatbázisokban lévő bizalmas adatok felderítéséhez, besorolásához, címkézéséhez és jelentéséhez.

A legérzékenyebb adatok közé tartozhatnak az üzleti, pénzügyi, egészségügyi vagy személyes adatok. Ezekaz adatok felderítése és besorolása döntő szerepet játszhat a szervezet információvédelmi megközelítésében. Infrastruktúraként alkalmas lehet az alábbiakra:

- Az adatvédelemre vonatkozó szabványok és a jogszabályi megfelelőség követelményeinek való megfelelés elősegítése.
- Különböző biztonsági forgatókönyvek, például figyelése (naplózása) és riasztása a rendellenes hozzáférést a bizalmas adatokhoz.
- A rendkívül bizalmas adatokat tartalmazó adatbázisokhoz való hozzáférés szabályozása és biztonságának szigorítása.

Az adatfelderítési & besorolás a [speciális adatbiztonsági](sql-database-advanced-data-security.md) ajánlat része, amely egy egyesített csomag a fejlett SQL biztonsági képességekhez. Az Azure Portal központi SQL Advanced **Data Security (SQL Advanced Data Security)** szakaszában elérheti és kezelheti az adatfelderítési & besorolását.

> [!NOTE]
> Ez a cikk az Azure SQL Database és az Azure Synapse Analytics vonatkozik. Az egyszerűség kedvéért itt az *SQL Database-t* használjuk az SQL Database és az Azure Synapse hivatkozásához. Az SQL Server (helyszíni) kiszolgálóról az [SQL Data Discovery and Classification című](https://go.microsoft.com/fwlink/?linkid=866999)témakörben talál további információt.

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Mi az adatfelderítési & besorolás?

Az adatfelderítési & besorolás speciális szolgáltatások és új SQL Database-képességek készletét vezeti be. Új információvédelmi paradigmát alkot az SQL Database számára, amelynek célja az adatok védelme, és nem csak az adatbázis. A paradigma tartalmazza:

- **Felfedezés és ajánlások:** A besorolási motor ellenőrzi az adatbázist, és azonosítja a potenciálisan bizalmas adatokat tartalmazó oszlopokat. Ezután egyszerű módot biztosít az ajánlott besorolás áttekintésére és alkalmazására az Azure Portalon keresztül.

- **Címkézés:** Az SQL-adatbázismotorhoz hozzáadott új metaadat-attribútumok használatával folyamatosan alkalmazhat érzékenységi besorolási címkéket az oszlopokra. Ezek a metaadatok ezután speciális, érzékenységalapú naplózási és védelmi forgatókönyvekhez használhatók.

- **Lekérdezés eredménykészletének érzékenysége:** A lekérdezés eredményhalmazának érzékenységét a rendszer valós időben számítja ki naplózási célokra.

- **Láthatóság:** Az adatbázis-besorolási állapot az Azure Portal részletes irányítópultján tekintheti meg. Emellett letöltheti a jelentést Excel formátumban, hogy megfelelőségi és naplózási célokra és egyéb igényekre használhassa.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Bizalmas oszlopok felderítése, osztályozása és címkézése

Ez a szakasz a következő lépéseket ismerteti:

- Bizalmas adatokat tartalmazó oszlopok felderítése, osztályozása és címkézése.
- Az adatbázis aktuális besorolási állapotának megtekintése és jelentések exportálása.

A besorolás két metaadat-attribútumot tartalmaz:

- **Címkék:** Az oszlopban tárolt adatok érzékenységi szintjének meghatározására használt fő besorolási attribútumok.  
- **Információtípusok:** Olyan attribútumok, amelyek részletesebb információt nyújtanak az oszlopban tárolt adatok típusáról.

### <a name="define-and-customize-your-classification-taxonomy"></a>A besorolási besorolás meghatározása és testreszabása

Data Discovery & Besorolás jön egy beépített sor érzékenységi címkék és beépített információtípusok és felderítési logika beépített készletével. Mostantól testre szabhatja ezt a taxonómiát, és kifejezetten a környezetre szabott rangsorolási és besorolási konstrukciókat definiálhat.

A besorolási besorolást egyetlen központi helyen definiálhatja és szabhatja testre a teljes Azure-szervezet számára. Ez a hely az [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)található, a biztonsági szabályzat részeként. Ezt a feladatot csak a szervezet gyökérfelügyeleti csoportjának rendszergazdai jogosultságaival rendelkező személy végezheti el.

Az SQL-információvédelem házirend-kezelésének részeként egyéni címkéket határozhat meg, rangsorolhatja őket, és társíthatja őket egy kiválasztott adattípuskészlethez. Saját egyéni adattípusokat is hozzáadhat, és karakterláncmintákkal konfigurálhatja őket. A minták hozzáadódnak a felderítési logikához az ilyen típusú adatok azonosításához az adatbázisokban.

További információ a házirend testreszabásáról és kezeléséről az [SQL Információvédelem házirend útmutatójában.](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)

A szervezeti szintű házirend definiálása után folytathatja az egyes adatbázisok osztályozását a testreszabott házirend használatával.

### <a name="classify-your-sql-database"></a>Az SQL-adatbázis osztályozása

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)

2. Nyissa meg a **Speciális adatbiztonság** lehetőséget az Azure SQL-adatbázis ablaktáblájának **Biztonság** fejlécében. Válassza **a Speciális adatbiztonság**lehetőséget, majd válassza az **Adatfelderítés & besorolás kartont.**

   ![Speciális adatbiztonság ablaktábla az Azure Portalon](./media/sql-data-discovery-and-classification/data_classification.png)

3. Az **Adatfelderítés & besorolás** lapon az **Áttekintés** lap tartalmazza az adatbázis aktuális besorolási állapotának összegzését. Az összefoglaló tartalmazza az összes minősített oszlop részletes listáját, amelyet szűrhet, hogy csak bizonyos sémarészeket, információtípusokat és címkéket jelenítsen meg. Ha még nem osztályozta az oszlopokat, [ugorjon az 5.](#step-5)

   ![Az aktuális besorolási állapot összefoglalása](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Ha Excel formátumú jelentést szeretne letölteni, válassza az **exportálás** lehetőséget az ablaktábla felső menüjében.

5. <a id="step-5"></a>Az adatok besorolásának megkezdéséhez válassza a **Besorolás** lapot az **Adatfelderítés & besorolási** lapon.

    A besorolási motor megvizsgálja az adatbázist a potenciálisan bizalmas adatokat tartalmazó oszlopokat, és az ajánlott oszlopbesorolások listáját tartalmazza.

6. Osztályozási javaslatok megtekintése és alkalmazása:

   - Az ajánlott oszlopbesorolások listájának megtekintéséhez jelölje ki az ablaktábla alján található javaslatok panelt.

   - Egy adott oszlopra vonatkozó javaslat elfogadásához jelölje be a megfelelő sor bal oldali oszlopában lévő jelölőnégyzetet. Ha az összes javaslatot elfogadottként szeretné megjelölni, jelölje be a bal szélső jelölőnégyzetet a javaslatok táblázat fejlécében.

       ![A besorolási ajánlások áttekintése és a listából való kijelölés](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - A kijelölt javaslatok alkalmazásához válassza a **Kijelölt javaslatok elfogadása**lehetőséget.

7. Az oszlopokat manuálisan is osztályozhatja, alternatív a javaslatalapú besoroláson kívül:

   1. Válassza a **Besorolás hozzáadása lehetőséget** az ablaktábla felső menüjében.

   1. A megnyíló környezeti ablakban jelölje ki a minősíteni kívánt sémát, táblát és oszlopot, valamint az információtípust és az érzékenységi címkét.

   1. Válassza a **Besorolás hozzáadása** lehetőséget a környezeti ablak alján.

      ![Az osztályozandó oszlop kiválasztása](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. A besorolás befejezéséhez és az adatbázis-oszlopok új osztályozási metaadatokkal való tartós címkéjéhez és címkézéséhez válassza a **Mentés** gombot az ablak felső menüjében.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Bizalmas adatokhoz való hozzáférés naplózása

Az információvédelmi paradigma egyik fontos szempontja a bizalmas adatokhoz való hozzáférés nyomon követése. [Az Azure SQL Database Auditing](sql-database-auditing.md) tovább lett fejlesztve, `data_sensitivity_information`hogy egy új mezőt vegyen fel a naplóba, amelynek neve . Ez a mező naplózza a lekérdezés által visszaadott adatok érzékenységi besorolásait (feliratait). Például:

![Napló](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Engedélyek

Ezek a beépített szerepkörök egy Azure SQL-adatbázis adatbesorolását olvashatják:

- Tulajdonos
- Olvasó
- Közreműködő
- SQL biztonsági kezelő
- Felhasználói hozzáférés rendszergazdája

Ezek a beépített szerepkörök módosíthatják az Azure SQL-adatbázis adatbesorolását:

- Tulajdonos
- Közreműködő
- SQL biztonsági kezelő

További információ a szerepköralapú engedélyekről az [RBAC for Azure-erőforrásokban.](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="manage-classification"></a>Besorolások kezelése

A T-SQL, a REST API vagy a PowerShell használatával kezelheti a besorolásokat.

### <a name="use-t-sql"></a>A T-SQL használata

A T-SQL segítségével oszlopbesorolásokat adhat hozzá vagy távolíthat el, és a teljes adatbázis összes besorolását lekérheti.

> [!NOTE]
> Ha a T-SQL használatával kezeli a címkéket, nincs érvényesítés, hogy a szervezet információvédelmi házirendjében (a portálajánlásaiban megjelenő címkék) tartalmaznak egy oszlophoz hozzáadott címkéket. Szóval, ez rajtad múlik, hogy érvényesítse ezt.

A T-SQL besorolásokhoz való használatáról az alábbi hivatkozásokban talál további információt:

- Egy vagy több oszlop besorolásának hozzáadása vagy frissítése: [ÉRZÉKENYSÉGI OSZTÁLYOZÁS HOZZÁADÁSA](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Az osztályozás eltávolítása egy vagy több oszlopból: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Az adatbázis összes besorolásának megtekintése: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>A Rest API használata

A REST API segítségével programozott módon kezelheti a besorolásokat és a javaslatokat. A közzétett REST API a következő műveleteket támogatja:

- [Létrehozás vagy frissítés:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate)A megadott oszlop érzékenységi címkéjének létrehozása vagy frissítése.
- [Törlés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): Törli a megadott oszlop érzékenységi címkéjét.
- [Javaslat letiltása](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): Letiltja a megadott oszlop érzékenységi javaslatait.
- [Javaslat engedélyezése](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): Érzékenységi javaslatok engedélyezése a megadott oszlopban. (A javaslatok alapértelmezés szerint minden oszlopban engedélyezve vannak.)
- [Bekés:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get)A megadott oszlop érzékenységi címkéjének bekésése.
- [Aktuális adatbázis szerint listázás:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase)A megadott adatbázis aktuális érzékenységi címkéinek beírása.
- [Adatbázis által ajánlott lista:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)A megadott adatbázis ajánlott érzékenységi címkéinek beírása.

### <a name="use-powershell-cmdlets"></a>PowerShell-parancsmagok használata
A PowerShell segítségével kezelheti az Azure SQL Database és a felügyelt példányok besorolásait és javaslatait.

#### <a name="powershell-cmdlets-for-sql-database"></a>PowerShell-parancsmagok az SQL Database-hez

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>PowerShell-parancsmagok felügyelt példányokhoz

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>További lépések

- További információ a [speciális adatbiztonságról](sql-database-advanced-data-security.md).
- Fontolja meg az [Azure SQL Database Auditing](sql-database-auditing.md) konfigurálását a minősített bizalmas adatokhoz való hozzáférés figyelésére és naplózására.
- Az adatok felderítését és besorolását tartalmazó bemutatókat [lásd: Sql-adatok felderítése, osztályozása, címkézése & védelme | Adatkitett](https://www.youtube.com/watch?v=itVi9bkJUNc).
