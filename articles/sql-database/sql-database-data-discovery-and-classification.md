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
ms.openlocfilehash: eb4e7907c3dcffed035307c2084160ce6051be13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409949"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Az Azure SQL Database és az Azure Synapse Analytics adatfelderítési besorolása &

A Data Discovery & Classification az Azure SQL Database-be épített speciális képességeket biztosít az adatbázisokban lévő bizalmas adatok **felderítéséhez,** **osztályozásához,** & **címkézéséhez.** **labeling**

A legérzékenyebb adatok (üzleti, pénzügyi, egészségügyi, személyazonosításra alkalmas adatok) és így tovább) felderítése és besorolása kulcsfontosságú szerepet játszhat a szervezeti információvédelmi termetben. Infrastruktúraként alkalmas lehet az alábbiakra:

- Segíthet megfelelni az adatvédelmi szabványoknak és a szabályozási megfelelőség követelményeinek.
- Különböző biztonsági forgatókönyvek, például figyelése (naplózása) és riasztása a rendellenes hozzáférést a bizalmas adatokhoz.
- Vezérelhető és megerősíthető vele a bizalmas adatokat tartalmazó adatbázisok biztonsága.

A Data Discovery & Classification része a [speciális adatbiztonság](sql-database-advanced-data-security.md) (ADS) ajánlatnak, amely egy egységes csomag a fejlett SQL biztonsági képességekhez. adatfelderítési & besorolás a központi SQL ADS portálon keresztül érhető el és kezelhető.

> [!NOTE]
> Ez a dokumentum az Azure SQL Database és az Azure Synapse. Az egyszerűség kedvéért az SQL Database az SQL Database és az Azure Synapse hivatkozva használatos. Az SQL Server (helyszíni) esetén lásd: [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="subheading-1"></a>Mi az adatfelderítés & besorolása

A Data Discovery & Classification fejlett szolgáltatások és új SQL-képességek készletét vezeti be, és egy új SQL Information Protection paradigmát alkot, amelynek célja az adatok védelme, nem csak az adatbázis:

- **Felfedezés& javaslatok**

  Az osztályozási motor ellenőrzi az adatbázist, és azonosítja a potenciálisan bizalmas adatokat tartalmazó oszlopokat. Ezt követően egy egyszerű módszert kínál a megfelelő besorolási javaslatok áttekintésére és alkalmazására az Azure Portalon keresztül.

- **Címkézés**

  Az érzékenységi besorolási címkék az SQL Engine-be bevezetett új besorolási metaadat-attribútumok használatával folyamatosan címkézhetők az oszlopokon. Ezeket a metaadatokat aztán speciális bizalmasságalapú naplózáshoz és védelmi helyzetekben lehet hasznosítani.

- **Lekérdezés eredménykészletének érzékenysége**

  A lekérdezés eredménykészletének érzékenysége valós időben kerül kiszámításra naplózási célokra.

- **Láthatóság**

  Az adatbázis besorolási állapota megtekinthető egy részletes irányítópulton a portálon. Ezenkívül letölthet egy jelentést (Excel-formátumban), amelyet egyebek mellett megfelelőségi és naplózási célokra használhat.

## <a name="discover-classify--label-sensitive-columns"></a><a id="subheading-2"></a>Címkeérzékeny oszlopok felderítése, & osztályozása

A következő szakasz ismerteti az adatbázisban bizalmas adatokat tartalmazó oszlopok felderítésének, besorolásának és címkézésének lépéseit, valamint az adatbázis aktuális besorolási állapotának megtekintését és a jelentések exportálását.

A besorolás két metaadat-attribútumot tartalmaz:

- Címkék – Az oszlopban tárolt adatok érzékenységi szintjének meghatározására használt fő besorolási attribútumok.  
- Információtípusok – További részletesség biztosítása az oszlopban tárolt adatok típusához.

## <a name="define-and-customize-your-classification-taxonomy"></a>A besorolási besorolás meghatározása és testreszabása

Data Discovery & Besorolás jön egy beépített sor érzékenységi címkék és beépített információtípusok és felderítési logika beépített készletével. Most már képes testreszabni ezt a besorolást, és meghatározza a besorolási konstrukciók készletét és rangsorolását kifejezetten a környezetéhez.

A besorolási besorolás meghatározása és testreszabása egy központi helyen történik a teljes Azure-bérlő számára. Ez a hely az [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)található, a biztonsági szabályzat részeként. Ezt a feladatot csak a bérlői gyökérfelügyeleti csoport rendszergazdai jogosultságaival rendelkező személy hajthatja végre.

Az Információvédelem házirend-kezelés részeként egyéni címkéket határozhat meg, rangsorolhatja őket, és társíthatja őket egy kiválasztott adattípus-készlethez. Saját egyéni információtípusokat is felvehet, és olyan sztringmintákkal konfigurálhatja őket, amelyek a felderítési logika részévé válva biztosítják az adattípus azonosíthatóságát.
A házirend testreszabásáról és kezeléséről az [Információvédelem útmutatójában olvashat bővebben.](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)

Miután a bérlői szintű szabályzat definiálva lett, folytathatja az egyes adatbázisok besorolását a testreszabott házirend használatával.

## <a name="classify-your-sql-database"></a>Az SQL-adatbázis osztályozása

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)

2. Keresse meg a **Speciális adatbiztonság** elemet az Azure SQL-adatbázis ablaktáblájának Biztonság fejlécében. Ide kattintva engedélyezheti a speciális adatbiztonságot, majd kattintson az **Adatfelderítés & besorolási** kártyára.

   ![Adatbázis bevizsgálata](./media/sql-data-discovery-and-classification/data_classification.png)

3. Az **Áttekintés** lap tartalmazza az adatbázis aktuális besorolási állapotának összegzését, beleértve az összes minősített oszlop részletes listáját, amelyet szűrhet, hogy csak bizonyos sémarészeket, információtípusokat és címkéket jelenítsen meg. Ha még nem osztályozta az oszlopokat, [ugorjon az 5.](#step-5)

   ![Az aktuális besorolási állapot összefoglalása](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Ha Excel formátumban szeretne jelentést letölteni, kattintson az ablak felső menüjének **Exportálás** parancsára.

   ![Exportálás Excelbe](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Az adatok besorolásának megkezdéséhez kattintson az ablak tetején lévő **Besorolás fülre.**

    ![Adatok osztályozása](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Az osztályozási motor megvizsgálja az adatbázisban a potenciálisan bizalmas adatokat tartalmazó oszlopokat, és tartalmazza az **ajánlott oszlopbesorolások**listáját. Osztályozási javaslatok megtekintése és alkalmazása:

   - Az ajánlott oszlopbesorolások listájának megtekintéséhez kattintson az ablak alján található ajánlások panelre:

      ![Az adatok besorolása](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Tekintse át a javaslatok listáját – egy adott oszlopra vonatkozó javaslat elfogadásához jelölje be a jelölőnégyzetet a megfelelő sor bal oldali oszlopában. Az összes *javaslatot* elfogadottként is megjelölheti, ha bejelöli a javaslatok táblázat fejlécében lévő jelölőnégyzetet.

       ![Ajánláslista áttekintése](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - A kiválasztott javaslatok alkalmazásához kattintson a kék **A kiválasztott javaslatok elfogadása** gombra.

      ![Ajánlások alkalmazása](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Az oszlopokat manuálisan is **osztályozhatja** alternatívaként, vagy a javaslatalapú besoroláshoz képest:

   - Kattintson a **Besorolás hozzáadása** gombra az ablak felső menüjében.

      ![Besorolás manuális hozzáadása](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - A megnyíló környezeti ablakban jelölje ki a sémát, > a minősíteni kívánt táblát > oszlopot, valamint az információtípust és az érzékenységi címkét. Ezután kattintson a kék **Besorolás hozzáadása** gombra a környezet ablakának alján.

      ![Jelölje ki az osztályozandó oszlopot](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. A besorolás befejezéséhez és az adatbázis-oszlopok új osztályozási metaadatokkal való tartós címkéjéhez kattintson a **Mentés** gombra az ablak felső menüjében.

   ![Mentés](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a name="auditing-access-to-sensitive-data"></a><a id="subheading-3"></a>Bizalmas adatokhoz való hozzáférés naplózása

Az információvédelmi paradigma egyik fontos szempontja a bizalmas adatokhoz való hozzáférés nyomon követése. [Az Azure SQL Database Auditing](sql-database-auditing.md) lett kijavítva, hogy egy új mezőt tartalmazzon a data_sensitivity_information *nevű*naplóban, amely naplózza a lekérdezés által visszaadott tényleges adatok érzékenységi besorolásait (címkéit).

![Napló](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="subheading-4"></a>Engedélyek

A következő beépített szerepkörök olvashatják az Azure SQL-adatbázis `Contributor` `SQL Security Manager` adatbesorolását: `Owner`, `Reader`, , és `User Access Administrator`.

A következő beépített szerepkörök módosíthatják az Azure SQL-adatbázis adatbesorolását: `Owner`, `Contributor`. `SQL Security Manager`

További információ [az RBAC for Azure-erőforrásokról](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="subheading-5"></a>Besorolások kezelése

# <a name="t-sql"></a>[T-SQL](#tab/azure-t-sql)
A T-SQL segítségével oszlopbesorolásokat adhat hozzá/távolíthat el, valamint a teljes adatbázis összes besorolását lekérheti.

> [!NOTE]
> Ha a T-SQL használatával kezeli a címkéket, nincs érvényesítés, hogy a szervezeti információvédelmi házirendben (a portálajánlásaiban megjelenő címkék) léteznek egy oszlophoz hozzáadott címkék. Ezért önön múlik, hogy ezt érvényesítse.

- Egy vagy több oszlop besorolásának hozzáadása/frissítése: [ÉRZÉKENYSÉGI OSZTÁLYOZÁS HOZZÁADÁSA](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Az osztályozás eltávolítása egy vagy több oszlopból: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Az adatbázis összes besorolásának megtekintése: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apis"></a>[Rest API-k](#tab/azure-rest-api)
A REST API-k segítségével programozott módon kezelheti a besorolásokat és a javaslatokat. A közzétett REST API-k a következő műveleteket támogatják:

- [Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) – Egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése
- [Törlés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) – Egy adott oszlop érzékenységi címkéjének törlése
- [Javaslat letiltása](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) - Letiltja az érzékenységi javaslatokat egy adott oszlopban
- [Javaslat engedélyezése](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) – Érzékenységi javaslatok engedélyezése egy adott oszlopon (a javaslatok alapértelmezés szerint engedélyezve vannak az összes oszlopban)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) - Egy adott oszlop érzékenységi címkéjének bekerülése
- [Aktuális adatbázis szerint listázása](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) – Egy adott adatbázis aktuális érzékenységi címkéinek beírása
- [Adatbázis által ajánlott lista](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) - Egy adott adatbázis ajánlott érzékenységi címkéinek beírása

# <a name="powershell-cmdlet"></a>[PowerShell-parancsmag](#tab/azure-powelshell)
A PowerShell segítségével kezelheti az Azure SQL Database és a felügyelt példány besorolásait és javaslatait.

### <a name="powershell-cmdlet-for-azure-sql-database"></a>PowerShell-parancsmag az Azure SQL-adatbázishoz
- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>PowerShell-parancsmagok felügyelt példányhoz
- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a name="next-steps"></a><a id="subheading-6"></a>További lépések

- További információ a [speciális adatbiztonságról](sql-database-advanced-data-security.md).
- Fontolja meg az [Azure SQL Database Auditing](sql-database-auditing.md) konfigurálását a minősített bizalmas adatokhoz való hozzáférés figyelésére és naplózására.
- Adatfelderítési & besorolást tartalmazó YouTube-bemutató esetén [lásd: SQL-adatok felderítése, osztályozása, címkézése & védelme | Adatkitett](https://www.youtube.com/watch?v=itVi9bkJUNc).

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
