---
title: Adatfelderítés és besorolás
description: Azure SQL Database és az adatfelderítési & besorolása
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: 7161c5554fe2d4993f3bd30ad82d675a71927f23
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279204"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Adatfelderítési & besorolása Azure SQL Database és SQL Data Warehouse

Az adatfelderítési & besorolása olyan speciális képességeket **biztosít, amelyek**a Azure SQL Databasebe vannak építve, így a bizalmas adatoknak az adatbázisokban való felderítéséhez, **besorolásához**, **címkézéséhez** ** & .**

A legérzékenyebb adatok (üzleti, pénzügyi, egészségügyi, személyazonosításra alkalmas adatok) felfedése és besorolása kulcsfontosságú szerepet játszik a szervezeti adatok védelmében. Infrastruktúraként alkalmas lehet az alábbiakra:

- Segíthet megfelelni az adatvédelmi szabványoknak és a szabályozási megfelelőség követelményeinek.
- Különböző biztonsági forgatókönyvek, például a figyelés (naplózás) és a bizalmas adatok rendellenes hozzáférésének riasztása.
- Vezérelhető és megerősíthető vele a bizalmas adatokat tartalmazó adatbázisok biztonsága.

Az adatfelderítési & besorolása a [speciális adatbiztonsági](sql-database-advanced-data-security.md) (ADS) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. az adatfelderítési & besorolása a központi SQL ADS portálon keresztül érhető el és kezelhető.

> [!NOTE]
> Ez a dokumentum Azure SQL Database és Azure SQL Data Warehousera vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database. SQL Server (helyszíni) esetében lásd: [SQL-adatfelderítés és-besorolás](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Mi az adatfelderítési & besorolása

Az adatfelderítési & besorolása fejlett szolgáltatásokat és új SQL-képességeket vezet be, amelyek egy új SQL Information Protection paradigmát képeznek, amely az adatok védelmére szolgál, nem csupán az adatbázisra:

- **Felderítési & javaslatok**

  A besorolási motor megvizsgálja az adatbázist, és azonosítja a potenciálisan bizalmas adatokat tartalmazó oszlopokat. Ezt követően egy egyszerű módszert kínál a megfelelő besorolási javaslatok áttekintésére és alkalmazására az Azure Portalon keresztül.

- **Címkézés**

  Az érzékenységi besorolási címkék az SQL Engine-be bevezetett új besorolási metaadatokat használó oszlopokban tartósan címkézve lehetnek. Ezeket a metaadatokat aztán speciális bizalmasságalapú naplózáshoz és védelmi helyzetekben lehet hasznosítani.

- **Lekérdezési eredményhalmaz érzékenysége**

  A lekérdezési eredményhalmaz érzékenységét valós időben számítjuk ki naplózási célokra.

- **Láthatóság**

  Az adatbázis besorolási állapota a portálon található részletes irányítópulton tekinthető meg. Ezenkívül letölthet egy jelentést (Excel-formátumban), amelyet egyebek mellett megfelelőségi és naplózási célokra használhat.

## <a id="subheading-2"></a>Felderítés, besorolás & címke bizalmas oszlopai

A következő szakasz ismerteti a bizalmas adatokat tartalmazó oszlopok felfedésének, besorolásának és címkézésének lépéseit az adatbázisban, valamint megtekintheti az adatbázis aktuális besorolási állapotát és a jelentések exportálását.

A besorolás két metaadat-attribútumot tartalmaz:

- Labels (címkék) – a fő besorolási attribútumok, amelyek az oszlopban tárolt adatmennyiség érzékenységi szintjének meghatározására szolgálnak.  
- Adattípusok – további részletességi adatokat adhat meg az oszlopban tárolt adatok típusához.

## <a name="define-and-customize-your-classification-taxonomy"></a>Besorolási besorolás meghatározása és testreszabása

Az SQL adatfelderítési & besorolása tartalmaz egy beépített érzékenységi címkét és egy beépített adattípust és felderítési logikát. Most már testre szabhatja ezt a besorolást, és meghatározhatja a besorolási szerkezetek készletét és rangsorolását kifejezetten a környezetéhez.

A besorolási besorolás meghatározása és testreszabása a teljes Azure-bérlő egyik központi helyén történik. Ez a hely [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), a biztonsági szabályzat részeként. Ezt a feladatot csak azok a felhasználók hajthatják végre, akik rendszergazdai jogokkal rendelkeznek a bérlő gyökérszintű felügyeleti csoportjában.

A Information Protection házirend-kezelés részeként egyéni címkéket adhat meg, rangsorolhatja őket, és társíthatja őket egy kiválasztott adattípussal. Saját egyéni információtípusokat is felvehet, és olyan sztringmintákkal konfigurálhatja őket, amelyek a felderítési logika részévé válva biztosítják az adattípus azonosíthatóságát.
További információ a szabályzatok testreszabásáról és kezeléséről a [Information Protection szabályzat útmutatójában](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Miután meghatározta a bérlőre kiterjedő házirendet, folytathatja az egyéni adatbázisok besorolását a testreszabott szabályzat használatával.

## <a name="classify-your-sql-database"></a>A SQL Database besorolása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

2. A Azure SQL Database panel biztonsági fejléce alatt navigáljon a **speciális adatbiztonság** elemre. Kattintson ide a speciális adatbiztonság engedélyezéséhez, majd kattintson az **adatfelderítési & besorolási** kártyára.

   ![Adatbázis vizsgálata](./media/sql-data-discovery-and-classification/data_classification.png)

3. Az **Áttekintés** lap az adatbázis aktuális besorolási állapotának összegzését tartalmazza, beleértve az összes besorolt oszlop részletes listáját is, amelyet szűrheti úgy is, hogy csak bizonyos sémákat, adattípusokat és címkéket tekintse meg. Ha még nem sorolt be oszlopokat, [ugorjon az 5. lépésre](#step-5).

   ![Aktuális besorolási állapot összegzése](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Egy jelentés Excel-formátumban való letöltéséhez kattintson az **Exportálás** lehetőségre az ablak felső menüjében.

   ![Exportálás Excelbe](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Az adatbesorolás megkezdéséhez kattintson a **besorolás fülre** az ablak tetején.

    ![Az adatbesorolás](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. A besorolási motor megvizsgálja az adatbázist a potenciálisan bizalmas adatokat tartalmazó oszlopokra vonatkozóan, és felsorolja a **javasolt oszlopok besorolásait**. Besorolási javaslatok megtekintése és alkalmazása:

   - A javasolt oszlop besorolások listájának megtekintéséhez kattintson az ablak alján található javaslatok panelre:

      ![Az adatok besorolása](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Tekintse át a javaslatok listáját – egy adott oszlopra vonatkozó javaslat elfogadásához jelölje be a megfelelő sor bal oldali oszlopában található jelölőnégyzetet. A javaslatok tábla fejlécében a jelölőnégyzet bejelölésével megadhatja az elfogadásra váró *összes javaslatot* is.

       ![Javaslati lista áttekintése](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - A kiválasztott javaslatok alkalmazásához kattintson a kék **elfogadás kiválasztott javaslatok** gombra.

      ![Javaslatok alkalmazása](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Az oszlopokat **manuálisan is kiminősítheti** alternatívként, illetve a javaslaton alapuló besoroláshoz:

   - Kattintson a **besorolás hozzáadása** lehetőségre az ablak felső menüjében.

      ![Besorolás manuális hozzáadása](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - A megnyíló környezet ablakban válassza ki a sémát, > a táblázat > a minősíteni kívánt oszlopot, valamint az információ típusát és az érzékenységi címkét. Ezután kattintson a kék **Hozzáadás besorolás** gombra a helyi ablak alján.

      ![Osztályozni kívánt oszlop kiválasztása](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Ha az adatbázis oszlopait az új besorolási metaadatokkal szeretné elvégezni, a besorolást és a tartósan címkézett címkét (címke) az ablak felső menüjében kattintson a **Mentés** gombra.

   ![Mentés](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Bizalmas adatokhoz való hozzáférés naplózása

Az Information Protection paradigmájának fontos aspektusa a bizalmas adatokhoz való hozzáférés figyelése. A [Azure SQL Database naplózása](sql-database-auditing.md) ki lett bővítve, hogy egy új mezőt tartalmazzon *data_sensitivity_information*nevű naplóban, amely a lekérdezés által visszaadott tényleges adatok érzékenységi besorolásait (feliratait) naplózza.

![Napló](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Engedélyek

A következő beépített szerepkörök beolvashatják az Azure SQL Database-adatbázisok adatbesorolását: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` és `User Access Administrator`.

A következő beépített szerepkörök módosíthatják egy Azure SQL Database-adatbázis adatbesorolását: `Owner`, `Contributor`, `SQL Security Manager`.

További információ az [Azure-erőforrások RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Besorolások kezelése

# <a name="t-sqltabazure-t-sql"></a>[T-SQL](#tab/azure-t-sql)
A T-SQL használatával oszlop besorolásokat adhat hozzá vagy távolíthat el, valamint beolvashatja az összes besorolást a teljes adatbázisra vonatkozóan.

> [!NOTE]
> Ha T-SQL-T használ a címkék kezeléséhez, nincs olyan érvényesítés, amely egy oszlophoz hozzáadott címkék léteznek a szervezeti Information Protection-házirendben (a portál ajánlásaiban megjelenő címkék halmaza). Ezért ennek ellenőrzése.

- Egy vagy több oszlop besorolásának hozzáadása/frissítése: [érzékenységi besorolás hozzáadása](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Távolítsa el a besorolást egy vagy több oszlopból: [eldobási érzékenység besorolása](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Az adatbázis összes besorolásának megtekintése: [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apistabazure-rest-api"></a>[REST API-k](#tab/azure-rest-api)
A REST API-k használatával programozott módon kezelheti a besorolásokat és a javaslatokat. A közzétett REST API-k a következő műveleteket támogatják:

- [Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) – egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése
- [Delete (Törlés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) ) – egy adott oszlop érzékenységi címkéjét törli
- [Javaslat letiltása](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) – az érzékenységi javaslatok letiltása egy adott oszlopban
- [Javaslat engedélyezése](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) – az érzékenységi javaslatok engedélyezése egy adott oszlopban (a javaslatok alapértelmezés szerint engedélyezve vannak az összes oszlopban)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) -lekéri egy adott oszlop érzékenységi címkéjét
- [Aktuális adatbázis listázása](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) – egy adott adatbázis aktuális érzékenységi címkéit kapja meg
- [Adatbázis által ajánlott lista](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) – egy adott adatbázis javasolt érzékenységi címkéit kéri le

# <a name="powershell-cmdlettabazure-powelshell"></a>[PowerShell-parancsmag](#tab/azure-powelshell)
A PowerShell használatával kezelheti Azure SQL Database és felügyelt példány besorolásait és javaslatait.

### <a name="powershell-cmdlet-for-azure-sql-database"></a>PowerShell-parancsmag a Azure SQL Databasehoz
- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [AzSqlDatabaSesensitivityRecommendation engedélyezése](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [AzSqlDatabaseSensitivityRecommendation letiltása](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>A felügyelt példány PowerShell-parancsmagjai
- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [AzSqlInstanceDatabaseSensitivityRecommendation engedélyezése](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [AzSqlInstanceDatabaseSensitivityRecommendation letiltása](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a id="subheading-6"></a>Következő lépések

- További információ a [speciális adatbiztonságról](sql-database-advanced-data-security.md).
- Érdemes lehet beállítani [Azure SQL Database naplózást](sql-database-auditing.md) a minősített bizalmas adatokhoz való hozzáférés figyelésére és naplózására.
- Az adatfelderítési & besorolást tartalmazó YouTube-bemutatóhoz lásd: az SQL-adatok észlelése [, osztályozása, címkézése & védelme | Az elérhető adatvédelem](https://www.youtube.com/watch?v=itVi9bkJUNc).

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
