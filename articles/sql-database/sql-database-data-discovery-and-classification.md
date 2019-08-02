---
title: Adatfelderítési & besorolásának Azure SQL Database és SQL Data Warehousee | Microsoft Docs
description: Azure SQL Database és az adatfelderítési & besorolása
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: a606364503172d5cb1ddcf00262eb81b9423b55b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569053"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Adatfelderítési & besorolása Azure SQL Database és SQL Data Warehouse

Az adatfelderítési & besorolása (jelenleg előzetes verzióban érhető el) olyan speciális képességeket biztosít, amelyek a Azure SQL Database beépítettek a bizalmas adatok **felderítéséhez**, **besorolásához**  & , **címkézéséhez** adatbázisok.
A legérzékenyebb adatok (üzleti, pénzügyi, egészségügyi, személyazonosításra alkalmas adatok) felfedése és besorolása kulcsfontosságú szerepet játszik a szervezeti adatok védelmében. A következő infrastruktúrát nyújtja:

- Az adatvédelmi szabványoknak és a szabályozási megfelelőségi követelményeknek való megfelelés elősegítése.
- Különböző biztonsági forgatókönyvek, például a figyelés (naplózás) és a bizalmas adatok rendellenes hozzáférésének riasztása.
- A szigorúan bizalmas adatokat tartalmazó adatbázisok biztonságának szabályozása és a hozzáférés megerősítése.

Az adatfelderítési & besorolása a [speciális adatbiztonsági](sql-database-advanced-data-security.md) (ADS) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. az adatfelderítési & besorolása a központi SQL ADS portálon keresztül érhető el és kezelhető.

> [!NOTE]
> Ez a dokumentum Azure SQL Database és Azure SQL Data Warehousera vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database. SQL Server (helyszíni) esetében lásd: [SQL-adatfelderítés és-besorolás](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Mi az adatfelderítési & besorolása

Az adatfelderítési & besorolása fejlett szolgáltatásokat és új SQL-képességeket vezet be, amelyek egy új SQL Information Protection paradigmát képeznek, amely az adatok védelmére szolgál, nem csupán az adatbázisra:

- **Felderítési & javaslatok**

  A besorolási motor megvizsgálja az adatbázist, és azonosítja a potenciálisan bizalmas adatokat tartalmazó oszlopokat. Ezt követően egyszerű módszert biztosít a megfelelő besorolási javaslatok áttekintésére és alkalmazására a Azure Portal használatával.

- **Címkézés**

  Az érzékenységi besorolási címkék az SQL Engine-be bevezetett új besorolási metaadatokat használó oszlopokban tartósan címkézve lehetnek. Ezt a metaadatokat ezután speciális, érzékenységen alapuló naplózási és védelmi forgatókönyvekhez lehet használni.

- **Lekérdezési eredményhalmaz érzékenysége**

  A lekérdezési eredményhalmaz érzékenységét valós időben számítjuk ki naplózási célokra.

- **Láthatóság**

  Az adatbázis besorolási állapota a portálon található részletes irányítópulton tekinthető meg. Emellett letölthet egy jelentést (Excel-formátumban), amely megfelelőségi & naplózási célokra, valamint más igényekre is használható.

## <a id="subheading-2"></a>Felderítés, besorolás & címke bizalmas oszlopai

A következő szakasz ismerteti a bizalmas adatokat tartalmazó oszlopok felfedésének, besorolásának és címkézésének lépéseit az adatbázisban, valamint megtekintheti az adatbázis aktuális besorolási állapotát és a jelentések exportálását.

A besorolás két metaadat-attribútumot tartalmaz:

- Labels (címkék) – a fő besorolási attribútumok, amelyek az oszlopban tárolt adatmennyiség érzékenységi szintjének meghatározására szolgálnak.  
- Adattípusok – további részletességi adatokat adhat meg az oszlopban tárolt adatok típusához.

## <a name="define-and-customize-your-classification-taxonomy"></a>Besorolási besorolás meghatározása és testreszabása

Az SQL adatfelderítési & besorolása tartalmaz egy beépített érzékenységi címkét és egy beépített adattípust és felderítési logikát. Most már testre szabhatja ezt a besorolást, és meghatározhatja a besorolási szerkezetek készletét és rangsorolását kifejezetten a környezetéhez.

A besorolási besorolás meghatározása és testreszabása a teljes Azure-bérlő egyik központi helyén történik. Ez a hely [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), a biztonsági szabályzat részeként. Ezt a feladatot csak azok a felhasználók hajthatják végre, akik rendszergazdai jogokkal rendelkeznek a bérlő gyökérszintű felügyeleti csoportjában.

A Information Protection házirend-kezelés részeként egyéni címkéket adhat meg, rangsorolhatja őket, és társíthatja őket egy kiválasztott adattípussal. Hozzáadhat saját egyéni adattípusokat is, és a karakterlánc-mintázatokkal konfigurálhatja azokat, amelyeket a rendszer az adatbázisokban az ilyen típusú adatok azonosításához ad hozzá a felderítési logikához.
További információ a szabályzatok testreszabásáról és kezeléséről a [Information Protection szabályzat útmutatójában](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Miután meghatározta a bérlőre kiterjedő házirendet, folytathatja az egyéni adatbázisok besorolását a testreszabott szabályzat használatával.

## <a name="classify-your-sql-database"></a>A SQL Database besorolása

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. A Azure SQL Database panel biztonsági fejléce alatt navigáljon a **speciális adatbiztonság** elemre. Kattintson ide a speciális adatbiztonság engedélyezéséhez, majd kattintson az **adatfelderítési & besorolás (előzetes verzió)** kártyára.

   ![Adatbázis vizsgálata](./media/sql-data-discovery-and-classification/data_classification.png)

3. Az **Áttekintés** lap az adatbázis aktuális besorolási állapotának összegzését tartalmazza, beleértve az összes besorolt oszlop részletes listáját is, amelyet szűrheti úgy is, hogy csak bizonyos sémákat, adattípusokat és címkéket tekintse meg. Ha még nem sorolt be oszlopokat, [ugorjon az 5](#step-5). lépésre.

   ![Aktuális besorolási állapot összegzése](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Egy jelentés Excel-formátumban való letöltéséhez kattintson az **Exportálás** lehetőségre az ablak felső menüjében.

   ![Exportálás Excelbe](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Az adatbesorolás megkezdéséhez kattintson a **besorolás fülre** az ablak tetején.

    ![Az adatbesorolás](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. A besorolási motor megvizsgálja az adatbázist a potenciálisan bizalmas adatokat tartalmazó oszlopokra vonatkozóan, és felsorolja a **javasolt oszlopok besorolásait**. Besorolási javaslatok megtekintése és alkalmazása:

   - A javasolt oszlop besorolások listájának megtekintéséhez kattintson az ablak alján található javaslatok panelre:

      ![Az adatai osztályozása](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Tekintse át a javaslatok listáját – egy adott oszlopra vonatkozó javaslat elfogadásához jelölje be a megfelelő sor bal oldali oszlopában található jelölőnégyzetet. A javaslatok tábla fejlécében a jelölőnégyzet bejelölésével megadhatja az elfogadásra váró *összes javaslatot* is.

       ![Javaslati lista áttekintése](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - A kiválasztott javaslatok alkalmazásához kattintson a kék **elfogadás kiválasztott javaslatok** gombra.

      ![Javaslatok alkalmazása](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Az oszlopokat **manuálisan** is kiminősítheti alternatívként, illetve a javaslaton alapuló besoroláshoz:

   - Kattintson a **besorolás hozzáadása** lehetőségre az ablak felső menüjében.

      ![Besorolás manuális hozzáadása](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - A megnyíló környezet ablakban válassza ki a sémát, > a táblázat > a minősíteni kívánt oszlopot, valamint az információ típusát és az érzékenységi címkét. Ezután kattintson a kék **Hozzáadás besorolás** gombra a helyi ablak alján.

      ![Osztályozni kívánt oszlop kiválasztása](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Ha az adatbázis oszlopait az új besorolási metaadatokkal szeretné elvégezni, a besorolást és a tartósan címkézett címkét (címke) az ablak felső menüjében kattintson a **Mentés** gombra.

   ![Mentés](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Bizalmas adatokhoz való hozzáférés naplózása

Az Information Protection paradigmájának fontos aspektusa a bizalmas adatokhoz való hozzáférés figyelése. A [Azure SQL Database naplózása](sql-database-auditing.md) ki lett bővítve, hogy szerepeljen egy új mező a *data_sensitivity_information*nevű naplóban, amely naplózza a lekérdezés által visszaadott tényleges adatok érzékenységi besorolásait (feliratait).

![Napló](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Az adatbesorolás kezelése a T-SQL használatával

A T-SQL használatával oszlop besorolásokat adhat hozzá vagy távolíthat el, valamint beolvashatja az összes besorolást a teljes adatbázisra vonatkozóan.

> [!NOTE]
> Ha T-SQL-T használ a címkék kezeléséhez, nincs olyan érvényesítés, amely egy oszlophoz hozzáadott címkék léteznek a szervezeti Information Protection-házirendben (a portál ajánlásaiban megjelenő címkék halmaza). Ezért ennek ellenőrzése.

- Egy vagy több oszlop besorolásának hozzáadása/frissítése: [ÉRZÉKENYSÉGI BESOROLÁS HOZZÁADÁSA](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Távolítsa el a besorolást egy vagy több oszlopból: [ELDOBÁSI ÉRZÉKENYSÉG BESOROLÁSA](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Az adatbázis összes besorolásának megtekintése: [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

Használhatja a REST API-kat is a besorolások programozott kezeléséhez. A közzétett REST API-k a következő műveleteket támogatják:

- [Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) – egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése
- [Delete (Törlés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) ) – egy adott oszlop érzékenységi címkéjét törli
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) -lekéri egy adott oszlop érzékenységi címkéjét
- [Aktuális adatbázis listázása](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) – egy adott adatbázis aktuális érzékenységi címkéit kapja meg
- [Adatbázis által ajánlott lista](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) – egy adott adatbázis javasolt érzékenységi címkéit kéri le

## <a name="manage-data-discovery-and-classification-using-azure-powershell"></a>Adatfelderítés és besorolás kezelése az Azure PowerShell használatával

Az Azure SQL Database-adatbázisok és a felügyelt példányok összes javasolt oszlopának beolvasásához a PowerShellt használhatja.

### <a name="powershell-cmdlets-for-azure-sql-database"></a>PowerShell-parancsmagok az Azure SQL Database-hez

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>A felügyelt példány PowerShell-parancsmagjai

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)

## <a name="permissions"></a>Engedélyek

A következő beépített szerepkörök beolvashatják az Azure SQL Database-adatbázisok adatbesorolását: `Owner` `SQL Security Manager` `Reader` `Contributor`,, és `User Access Administrator`.

A következő beépített szerepkörök módosíthatják egy Azure SQL Database-adatbázis adatbesorolását: `Owner`, `Contributor`, `SQL Security Manager`.

További információ az [Azure-erőforrások RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Következő lépések

- További információ a [speciális](sql-database-advanced-data-security.md)adatbiztonságról.
- Érdemes lehet beállítani [Azure SQL Database naplózást](sql-database-auditing.md) a minősített bizalmas adatokhoz való hozzáférés figyelésére és naplózására.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
