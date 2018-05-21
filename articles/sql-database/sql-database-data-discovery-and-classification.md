---
title: Az Azure SQL-adatbázis adatainak felderítése és besorolása |} Microsoft Docs
description: Az Azure SQL-adatbázis adatainak felderítése és besorolása
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/18/2018
ms.author: giladm
ms.openlocfilehash: b43b010a88f313930217289549448de30a82a070
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Az Azure SQL-adatbázis adatainak felderítése és besorolása
Az Azure SQL Database beépített speciális képességeket biztosít a felderítési adatok & besorolás (jelenleg előzetes verzió) **felderítéséhez**, **zárolásának**, **címkézés**  &  **védelme** az adatbázisokat a bizalmas adatokat.
Felderítésének, illetve a leginkább bizalmas adatok zárolásának (üzleti, pénzügyi, egészségügyi, személyazonosításra alkalmas adatok, stb.) is a döntő szerepet játszanak a szervezeti adatok védelmet stature. Az infrastruktúra lehetnek:
* Gondoskodik a megfelelő adatok adatvédelmi szabványok és az előírásoknak való megfelelés követelményeket, például GDPR.
* Különféle biztonsági forgatókönyvek, például a figyelési (naplózás), és riasztást küld rendellenes bizalmas adatokhoz való hozzáférést.
* Hozzáférés szabályozása, és a bizalmas adatokat tartalmazó adatbázisok biztonsági korlátozására.

Felderítési adatok & besorolás része a [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (ATP) ajánlat, amely a speciális SQL biztonsági képességei egyesített csomagok. Felderítési adatok & besorolás elérése és felügyelt központi SQL ATP portálról.

> [!NOTE]
> Ez a dokumentum csak az Azure SQL Database vonatkozik. Az SQL Server (helyszíni), lásd: [SQL adatelérési felderítése és besorolása](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Mi az az adatok felderítése és besorolása?
Felderítési adatok & besorolás vezeti be a speciális szolgáltatások és új SQL-képességek, egy új SQL Information Protection-összeállítást az adatokat, nem csak az adatbázis védelmét célzó képező:
* **Felderítési & javaslatok** – a besorolási motor megvizsgálja az adatbázist, és azonosítja a potenciálisan bizalmas adatokat tartalmazó oszlopok. Majd biztosít, egyszerűen áttekintése és konfigurálása a megfelelő besorolási javaslatok az Azure-portálon.
* **Címkézés** – érzékenységi besorolási címkék tartósan címkézett oszlopokon új besorolási metaadat-attribútumok bevezetni az SQL-kezelő használatával. A metaadatok majd speciális sensitivity alapú naplózás és védelmi forgatókönyvek állítható be.
* **A lekérdezés eredményének beállítása érzékenységi** – a lekérdezés eredményhalmazából érzékenységének kiszámítása valós időben naplózási célokra.
* **Láthatóság** – az adatbázis besorolás állapotát tekintheti meg a részletes irányítópult a portálon. A jelentés (Excel formátumú) letöltése ezenkívül megfelelőségi és naplózási célokra, valamint más kell használni.

## <a id="subheading-2"></a>Felderítésére, besorolására & bizalmas oszlopok címke
A következő szakasz felderítésével, zárolásának és az adatbázis, valamint az adatbázis jelenlegi besorolás állapotának megtekintése és jelentések exportálása lévő bizalmas adatokat tartalmazó oszlopok címkézés lépéseit ismerteti.

A besorolási két metaadat-attribútumok tartalmazza:
* Feliratok – a fő besorolás attribútumok az érzékenységi szint az oszlopban tárolt adatok meghatározásához használt.  
* Adattípusok – adja meg az oszlopban tárolt adatok típusú további granularitási.

## <a name="classify-your-sql-database"></a>Az SQL-adatbázis besorolása

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. Navigáljon a **Advanced Threat Protection** az Azure SQL Database ablaktáblán biztonsági fejléc alatt. Kattintson Advanced Threat Protection engedélyezéséhez, majd a a **adatok felderítési & besorolás (előzetes verzió)** kártya.

   ![Egy adatbázis vizsgálata](./media/sql-data-discovery-and-classification/data_classification.png) 

3. A **áttekintése** lapján található összes osztályozott oszlop, amely csak a megadott séma részei, adattípusok és címkék megtekintéséhez is végezhet részletes listáját az adatbázis jelenlegi besorolás állapotának összegzését. Ha még meg még nem besorolt egyéb oszlopok [ugorjon az 5](#step-5).

   ![Aktuális besorolás állapotát](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png) 

4. Excel formátumú jelentést letöltéséhez kattintson a a **exportálása** az ablak a felső menüben lehetőség.

   ![Exportálás Excelbe](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png) 

5.  <a id="step-5"></a>Az adatok osztályozásához megkezdéséhez kattintson a a **osztályozás lapon** az ablak tetején.

    ![Az adatok osztályozására](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png) 

6. A besorolási motor megvizsgálja a potenciálisan bizalmas adatokat tartalmazó oszlopok az adatbázisba, és listája **oszlop besorolások ajánlott**. Megtekintéséhez és besorolási ajánlások érvényesek:

    * Ajánlott oszlopában besorolások listájának megtekintéséhez kattintson az ablak alján a javaslatok panelen:
    
      ![Az adatok besorolása](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png) 

    * Tekintse át a javaslatok – egy adott oszlop ajánlást elfogadja, jelölje be a jelölőnégyzetet, a bal oldali oszlopban, a megfelelő sor listáját. Megjelölheti a *ajánlások* , fogadja el a javaslatok táblázat fejlécében jelölőnégyzet ellenőrzése.

       ![Tekintse át a javaslat listája](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png) 

    * A kijelölt javaslatok alkalmazásához kattintson a kék **fogadja el a kiválasztott javaslatok** gombra.

      ![Javaslatok alkalmazása](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png) 

7. Emellett **manuális besorolását** oszlopok helyett, vagy emellett javaslat alapú osztályozására:

    * Kattintson a **vegye fel a besorolási** az ablak a felső menüben.
  
      ![Adja hozzá manuálisan a besorolás](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png) 

    * A környezet megjelenő ablakban válassza ki a séma > tábla > oszlopot, amely szeretné besorolni, valamint információkat és érzékenységi feliratot. Kattintson a kék a **vegye fel a besorolási** gomb a környezet ablak alján.

      ![Válassza ki az oszlop besorolása](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png) 

8. Végezze el a besorolás, és tartósan label (címke) az adatbázis oszlopok az új besorolás metaadatokkal, kattintson a **mentése** az ablak a felső menüben.

   ![Mentés](./media/sql-data-discovery-and-classification/10_data_classification_save.png) 

## <a id="subheading-3"></a>A bizalmas adatokhoz való hozzáférés naplózása

Az információk védelme paradigma fontos eleme azt a képességet figyelje a bizalmas adatokhoz való hozzáférést. [Az Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) új mező szerepeljen a biztonsági naplóba nevű továbbfejlesztett *data_sensitivity_information*, amely naplózza a tényleges által visszaadott adatok érzékenysége osztályozását (címke) a lekérdezés.

![Napló](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png) 

## <a id="subheading-4"></a>Következő lépések

- További információ [SQL Advanced Threat Protection](sql-advanced-threat-protection.md).
- Érdemes úgy konfigurálni [Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) a figyelés és naplózás a minősített bizalmas adatokhoz való hozzáférést.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4