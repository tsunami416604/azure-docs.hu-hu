---
title: Adatfelderítés és besorolás
description: Adatfelderítési & besorolása Azure SQL Database és az Azure szinapszis Analyticshez
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
ms.date: 04/21/2020
tags: azure-synapse
ms.openlocfilehash: f05b4d4fec99aaa2fb79da46e2167d883d1f15ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81766991"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Adatfelderítési & besorolása Azure SQL Database és az Azure szinapszis Analyticshez

Az adatfelderítési & besorolása Azure SQL Databasebe van építve. Fejlett képességeket biztosít az adatbázisaiban található bizalmas adatok felfedéséhez, besorolásához, címkézéséhez és jelentéséhez.

A legérzékenyebb adatok lehetnek például az üzleti, pénzügyi, egészségügyi vagy személyes adatok. Az adatok felfedezése és besorolása kulcsfontosságú szerepet játszik a szervezet Information Protection-megközelítésében. Infrastruktúraként alkalmas lehet az alábbiakra:

- Az adatvédelemre vonatkozó szabványok és a szabályozásoknak való megfelelőség követelményeinek való megfelelés elősegítése.
- Különböző biztonsági forgatókönyvek, például a figyelés (naplózás) és a bizalmas adatok rendellenes hozzáférésének riasztása.
- A szigorúan bizalmas adatokat tartalmazó adatbázisok biztonságának szabályozása és a hozzáférés megerősítése.

Az adatfelderítési & besorolása a [fejlett adatbiztonsági](sql-database-advanced-data-security.md) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. Az adatfelderítési & besorolását a Azure Portal központi **SQL Advanced adatbiztonság** szakasza segítségével érheti el és kezelheti.

> [!NOTE]
> Ez a cikk a Azure SQL Database és az Azure szinapszis Analytics szolgáltatásra vonatkozik. Az egyszerűség kedvéért a *SQL Database* itt tekintjük át a SQL Database és az Azure szinapszis szolgáltatásra is. További információ a SQL Serverról (helyszíni): SQL- [adatok felderítése és besorolása](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Mi az az adatfelderítési & besorolás?

Az adatfelderítési & besorolása fejlett szolgáltatások és új SQL Database képességek készletét mutatja be. A SQL Database új adatvédelmi paradigmát képezi, amely az adatok védelmét célozza, és nem csak az adatbázist. A paradigma a következőket foglalja magában:

- **Felderítés és javaslatok:** A besorolási motor megvizsgálja az adatbázist, és azonosítja a potenciálisan bizalmas adatokat tartalmazó oszlopokat. Ezután egyszerűen áttekintheti és alkalmazhatja az ajánlott besorolást a Azure Portal használatával.

- **Címkézés:** Az adatbesorolási címkéket tartósan alkalmazhatja az oszlopokra az SQL Database motorhoz hozzáadott új metaadat-attribútumok használatával. Ezt a metaadatokat ezután speciális, érzékenységen alapuló naplózási és védelmi forgatókönyvekhez lehet használni.

- **Lekérdezési eredmény – beállított érzékenység:** A lekérdezési eredményhalmaz érzékenységét valós időben számítjuk ki naplózási célokra.

- **Láthatóság:** Az adatbázis-besorolási állapotot a Azure Portal részletes irányítópultján tekintheti meg. Emellett a jelentés Excel-formátumban is letölthető a megfelelőségi és naplózási célokra, valamint más igényekhez.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Bizalmas oszlopok felderítése, besorolása és címkézése

Ez a szakasz a következő lépéseit ismerteti:

- Bizalmas adatokat tartalmazó oszlopok felfedése, osztályozása és címkézése az adatbázisban.
- Az adatbázis aktuális besorolási állapotának és a jelentések exportálásának megtekintése.

A besorolás két metaadat-attribútumot tartalmaz:

- **Címkék**: a fő besorolási attribútumok, amelyek az oszlopban tárolt adatmennyiség érzékenységi szintjének meghatározására szolgálnak.  
- **Adattípusok**: az oszlopban tárolt adatok típusával kapcsolatos részletesebb információkat biztosító attribútumok.

### <a name="define-and-customize-your-classification-taxonomy"></a>Besorolási besorolás meghatározása és testreszabása

Az adatfelderítési & besorolása tartalmaz egy beépített érzékenységi címkét és egy beépített adattípust és felderítési logikát. Mostantól testre szabhatja ezt a taxonómiát, és kifejezetten a környezetre szabott rangsorolási és besorolási konstrukciókat definiálhat.

A besorolási besorolást a teljes Azure-szervezet egyik központi helyén definiálhatja és testreszabhatja. Ez a hely [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), a biztonsági szabályzat részeként. Ezt a feladatot csak a szervezet legfelső szintű felügyeleti csoportjának rendszergazdai jogosultsággal rendelkező személye teheti meg.

Az SQL Information Protection házirend-kezelésének részeként egyéni címkéket adhat meg, rangsorolhatja őket, és társíthatja őket egy kiválasztott adattípussal. Saját egyéni adattípusokat is hozzáadhat, és karakterlánc-mintázatokkal konfigurálhatja őket. A rendszer hozzáadja a mintákat a felderítési logikához az ilyen típusú adattípusok azonosításához az adatbázisokban.

További információ a szabályzatok testreszabásáról és kezeléséről az [SQL Information Protection szabályzat útmutatójában](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Az egész szervezetre kiterjedő házirend meghatározása után folytathatja az egyes adatbázisok besorolását a testreszabott házirend használatával.

### <a name="classify-your-sql-database"></a>Az SQL-adatbázis besorolása

1. Lépjen a [Azure Portal](https://portal.azure.com).

2. A Azure SQL Database panel **biztonsági** fejléce alatt lépjen a **speciális adatbiztonság** elemre. Válassza a **speciális adatbiztonság**lehetőséget, majd válassza ki az **adatfelderítési & besorolási** kártyát.

   ![Speciális adatbiztonság ablaktábla Azure Portal](./media/sql-data-discovery-and-classification/data_classification.png)

3. Az **adatfelderítési & besorolása** lapon az **Áttekintés** lapon szerepel az adatbázis aktuális besorolási állapotának összegzése. Az összefoglalás tartalmazza az összes besorolt oszlop részletes listáját, amelyeket szűrheti is, ha csak bizonyos sémákat, adattípusokat és címkéket szeretne megjeleníteni. Ha még nem sorolt be oszlopokat, [ugorjon az 5. lépésre](#step-5).

   ![Aktuális besorolási állapot összegzése](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Egy jelentés Excel-formátumban való letöltéséhez válassza az **Exportálás** lehetőséget a panel felső menüjében.

5. <a id="step-5"></a>Az adatbesorolás megkezdéséhez válassza a **besorolás** lapot az **adatfelderítési & besorolása** lapon.

    A besorolási motor megvizsgálja az adatbázist a potenciálisan bizalmas adatokat tartalmazó oszlopokra vonatkozóan, és felsorolja a javasolt oszlopok besorolásait.

6. Besorolási javaslatok megtekintése és alkalmazása:

   - A javasolt oszlop besorolások listájának megtekintéséhez kattintson a panel alján található javaslatok panelre.

   - Egy adott oszlopra vonatkozó javaslat elfogadásához jelölje be a megfelelő sor bal oldali oszlopában található jelölőnégyzetet. Az összes javaslat elfogadottként való megjelöléséhez jelölje be a bal szélső jelölőnégyzetet a javaslatok táblázat fejlécében.

       ![Besorolási javaslatok áttekintése és kiválasztása](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - A kiválasztott javaslatok alkalmazásához válassza a **kiválasztott javaslatok elfogadása**lehetőséget.

7. Az oszlopokat manuálisan is osztályozhatja, Alternatív megoldásként vagy a javaslaton alapuló besorolás mellett:

   1. A panel felső menüjében válassza a **besorolás hozzáadása** elemet.

   1. A megnyíló környezet ablakban válassza ki a minősíteni kívánt sémát, táblázatot és oszlopot, valamint az információ típusát és az érzékenység címkéjét.

   1. A környezet ablakának alján válassza a **besorolás hozzáadása** elemet.

      ![Jelölje ki a minősíteni kívánt oszlopot](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Ha az adatbázis oszlopait az új besorolási metaadatokkal szeretné elvégezni, a besorolást és a tartósan címkézett címkét (címke) az ablak felső menüjében válassza a **Mentés** lehetőséget.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Bizalmas adatokhoz való hozzáférés naplózása

Az Information-Protection paradigma fontos aspektusa a bizalmas adatokhoz való hozzáférés figyelése. A [Azure SQL Database naplózása](sql-database-auditing.md) ki lett bővítve, hogy egy új mezőt tartalmazzon a `data_sensitivity_information`naplóban. Ez a mező a lekérdezés által visszaadott adatérzékeny besorolásokat (címkéket) naplózza. Például:

![Napló](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Engedélyek

Ezek a beépített szerepkörök egy Azure SQL Database-adatbázis adatbesorolását tudják olvasni:

- Tulajdonos
- Olvasó
- Közreműködő
- SQL Security Manager
- Felhasználói hozzáférés rendszergazdája

Ezek a beépített szerepkörök módosíthatják egy Azure SQL Database-adatbázis adatbesorolását:

- Tulajdonos
- Közreműködő
- SQL Security Manager

További információ a [RBAC Azure-erőforrások](https://docs.microsoft.com/azure/role-based-access-control/overview)szerepkör-alapú engedélyeiről.

## <a name="manage-classifications"></a><a id="manage-classification"></a>Besorolások kezelése

A besorolások kezeléséhez a T-SQL, a REST API vagy a PowerShell használható.

### <a name="use-t-sql"></a>A T-SQL használata

A T-SQL használatával oszlop besorolásokat adhat hozzá vagy távolíthat el, valamint beolvashatja az összes besorolást a teljes adatbázisra vonatkozóan.

> [!NOTE]
> Ha T-SQL-T használ a címkék kezeléséhez, nem kell érvényesíteni, hogy egy oszlophoz hozzáadott címkék megtalálhatók-e a szervezet Information Protection-szabályzatában (a portál ajánlásaiban megjelenő címkék halmaza). Így Ön is ellenőrizheti ezt.

A T-SQL osztályozási szolgáltatással való használatáról a következő hivatkozásokban talál további információt:

- Egy vagy több oszlop besorolásának hozzáadása vagy frissítése: [érzékenységi besorolás hozzáadása](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- A besorolás eltávolítása egy vagy több oszlopból: az [érzékenység besorolásának eldobása](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Az adatbázis összes besorolásának megtekintése: [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>A REST API használata

A REST API használatával programozott módon kezelheti a besorolásokat és a javaslatokat. A közzétett REST API a következő műveleteket támogatja:

- [Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): a megadott oszlop érzékenységi címkéjét hozza létre vagy frissíti.
- [Delete (Törlés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete)): törli a megadott oszlop érzékenységi címkéjét.
- [Javaslat letiltása](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): a megadott oszlop érzékenységi javaslatainak letiltása.
- [Javaslat engedélyezése](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): a megadott oszlopra vonatkozó érzékenységi javaslatokat tesz lehetővé. (A javaslatok alapértelmezés szerint engedélyezve vannak az összes oszlopban.)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): lekéri a megadott oszlop érzékenységi címkéjét.
- [Aktuális adatbázis listázása](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): lekéri a megadott adatbázis aktuális érzékenységi címkéit.
- Az [adatbázis által ajánlott lista](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): lekéri a megadott adatbázis javasolt érzékenységi címkéit.

### <a name="use-powershell-cmdlets"></a>PowerShell-parancsmagok használata
A PowerShell használatával kezelheti Azure SQL Database és felügyelt példányok besorolásait és javaslatait.

#### <a name="powershell-cmdlets-for-sql-database"></a>PowerShell-parancsmagok a SQL Databasehoz

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [AzSqlDatabaSesensitivityRecommendation engedélyezése](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [AzSqlDatabaseSensitivityRecommendation letiltása](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>A felügyelt példányok PowerShell-parancsmagjai

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [AzSqlInstanceDatabaseSensitivityRecommendation engedélyezése](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [AzSqlInstanceDatabaseSensitivityRecommendation letiltása](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>További lépések

- További információ a [speciális adatbiztonságról](sql-database-advanced-data-security.md).
- Érdemes lehet beállítani [Azure SQL Database naplózást](sql-database-auditing.md) a minősített bizalmas adatokhoz való hozzáférés figyelésére és naplózására.
- Az adatfelderítést és-besorolást tartalmazó bemutatókért lásd: az [SQL-adatok védelmének észlelése, osztályozása, címkézése & | Az elérhető adatvédelem](https://www.youtube.com/watch?v=itVi9bkJUNc).
