---
title: "Az Azure SQL-adatbázis adatainak felderítése és besorolása |} Microsoft Docs"
description: "Az Azure SQL-adatbázis adatainak felderítése és besorolása"
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: 18afebefaee9117244767e0a00e293a297bbf9af
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Az Azure SQL-adatbázis adatainak felderítése és besorolása
Az Azure SQL Database beépített speciális képességeket biztosít a felderítési adatok & besorolás (jelenleg előzetes verzió) **felderítéséhez**, **zárolásának**, **címkézés**  &  **védelme** az adatbázisokat a bizalmas adatokat.
Felderítésének, illetve a leginkább bizalmas adatok zárolásának (üzleti, pénzügyi, egészségügyi, személyazonosításra alkalmas adatok, stb.) is a döntő szerepet játszanak a szervezeti adatok védelmet stature. Az infrastruktúra lehetnek:
* Gondoskodik a megfelelő adatok adatvédelmi szabványok és az előírásoknak való megfelelés követelményeket, például GDPR.
* Különféle biztonsági forgatókönyvek, például a figyelési (naplózás), és riasztást küld rendellenes bizalmas adatokhoz való hozzáférést.
* Hozzáférés szabályozása, és a bizalmas adatokat tartalmazó adatbázisok biztonsági korlátozására.

## <a id="subheading-1"></a>– Áttekintés
Felderítési adatok & besorolás vezeti be a speciális szolgáltatások és új SQL-képességek, egy új SQL Information Protection-összeállítást az adatokat, nem csak az adatbázis védelmét célzó képező:
* **Felderítési & javaslatok** – a besorolási motor megvizsgálja az adatbázist, és azonosítja a potenciálisan bizalmas adatokat tartalmazó oszlopok. Majd biztosít, egyszerűen áttekintése és konfigurálása a megfelelő besorolási javaslatok az Azure-portálon.
* **Címkézés** – érzékenységi besorolási címkék tartósan címkézett oszlopokon új besorolási metaadat-attribútumok bevezetni az SQL-kezelő használatával. A metaadatok majd speciális sensitivity alapú naplózás és védelmi forgatókönyvek állítható be.
* **A lekérdezés eredményének beállítása érzékenységi** – a lekérdezés eredményhalmazából érzékenységének kiszámítása valós időben naplózási célokra.
* **Láthatóság** – az adatbázis besorolás állapotát tekintheti meg a részletes irányítópult a portálon. A jelentés (Excel formátumú) letöltése ezenkívül megfelelőségi és naplózási célokra, valamint más kell használni.

## <a id="subheading-2"></a>Felderítésével, zárolásának & bizalmas oszlopok címkézés
A következő szakasz felderítésével, zárolásának és az adatbázis, valamint az adatbázis jelenlegi besorolás állapotának megtekintése és jelentések exportálása lévő bizalmas adatokat tartalmazó oszlopok címkézés lépéseit ismerteti.

A besorolási két metaadat-attribútumok tartalmazza:
* Feliratok – a fő besorolás attribútumok az érzékenységi szint az oszlopban tárolt adatok meghatározásához használt.  
* Adattípusok – adja meg az oszlopban tárolt adatok típusú további granularitási.

<br>
**Az SQL-adatbázis besorolására:**

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. Keresse meg a **adatok felderítési & besorolás (előzetes verzió)** az SQL-adatbázis-ban történő beállítását.

    ![Navigációs ablaktábla][1]

3. A **áttekintése** lapján található összes osztályozott oszlop, amely csak a megadott séma részei, adattípusok és címkék megtekintéséhez is végezhet részletes listáját az adatbázis jelenlegi besorolás állapotának összegzését. Ha még meg még nem besorolt egyéb oszlopok [ugorjon az 5](#step-5).

    ![Navigációs ablaktábla][2]

4. Excel formátumú jelentést letöltéséhez kattintson a a **exportálása** az ablak a felső menüben lehetőség.

    ![Navigációs ablaktábla][3]

5.  <a id="step-5"></a>Az adatok osztályozásához megkezdéséhez kattintson a a **osztályozás lapon** az ablak tetején.

    ![Navigációs ablaktábla][4]

6. A besorolási motor megvizsgálja a potenciálisan bizalmas adatokat tartalmazó oszlopok az adatbázisba, és listája **oszlop besorolások ajánlott**. Megtekintéséhez és besorolási ajánlások érvényesek:

    * Ajánlott oszlopában besorolások listájának megtekintéséhez kattintson az ablak alján a javaslatok panelen:

        ![Navigációs ablaktábla][5]

    * Tekintse át a javaslatok – egy adott oszlop ajánlást elfogadja, jelölje be a jelölőnégyzetet, a bal oldali oszlopban, a megfelelő sor listáját. Megjelölheti a *ajánlások* , fogadja el a javaslatok táblázat fejlécében jelölőnégyzet ellenőrzése.

        ![Navigációs ablaktábla][6]

    * A kijelölt javaslatok alkalmazásához kattintson a kék **fogadja el a kiválasztott javaslatok** gombra.

        ![Navigációs ablaktábla][7]

7. Emellett **manuális besorolását** oszlopok helyett, vagy emellett javaslat alapú osztályozására:

    * Kattintson a **vegye fel a besorolási** az ablak a felső menüben.

        ![Navigációs ablaktábla][8]

    * A környezet megjelenő ablakban válassza ki a séma > tábla > oszlopot, amely szeretné besorolni, valamint információkat és érzékenységi feliratot. Kattintson a kék a **vegye fel a besorolási** gomb a környezet ablak alján.

        ![Navigációs ablaktábla][9]

8. Végezze el a besorolás, és tartósan label (címke) az adatbázis oszlopok az új besorolás metaadatokkal, kattintson a **mentése** az ablak a felső menüben.

    ![Navigációs ablaktábla][10]

## <a id="subheading-3"></a>A bizalmas adatokhoz való hozzáférés naplózása

Az információk védelme paradigma fontos eleme azt a képességet figyelje a bizalmas adatokhoz való hozzáférést.

[Az Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) új mező szerepeljen a biztonsági naplóba nevű továbbfejlesztett *data_sensitivity_information*, amely naplózza a tényleges által visszaadott adatok érzékenysége osztályozását (címke) a lekérdezés.

![Navigációs ablaktábla][11]

## <a id="subheading-4"></a>Következő lépések
Érdemes úgy konfigurálni [Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) a figyelés és naplózás a minősített bizalmas adatokhoz való hozzáférést.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4

<!--Image references-->
[1]: ./media/sql-data-discovery-and-classification/1_data_classification_settings_menu.png
[2]: ./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png
[3]: ./media/sql-data-discovery-and-classification/3_data_classification_export_report.png
[4]: ./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png
[5]: ./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png
[6]: ./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png
[7]: ./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png
[8]: ./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png
[9]: ./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png
[10]: ./media/sql-data-discovery-and-classification/10_data_classification_save.png
[11]: ./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png
