---
title: Az Azure SQL-adatbázis adatainak felderítés és besorolás |} A Microsoft Docs
description: Az Azure SQL-adatbázis adatainak felderítése és besorolása
services: sql-database
author: giladmit
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: giladm
ms.openlocfilehash: 6ef9a701f3a228e4c40da94f83310ef2884a3f59
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055729"
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Az Azure SQL-adatbázis adatainak felderítése és besorolása
Adatfelderítés és besorolás (jelenleg előzetes verzióban érhető el) biztosít az Azure SQL Database-be épített fejlettebb képességek **felderítése**, **Írisz**, **címkézés**  &  **védelme** a bizalmas adatokat az adatbázisban.
Felderítése és besorolása a leginkább bizalmas adatok (üzleti, pénzügyi, egészségügyi, személyazonosításra alkalmas adatok, stb.) is kulcsszerepet játszik a szervezeti adatok védelme helyzetében. Az infrastruktúra szolgálhasson:
* Lehetővé teszi, hogy megfeleljen az adatok adatvédelmi szabványok és az előírt megfelelőségi követelmények teljesítését.
* Különféle biztonsági forgatókönyvek, például a (naplózás) figyelési és riasztási a bizalmas adatokhoz való rendellenes hozzáférést.
* Való hozzáférés szabályozása és a bizalmas adatokat tartalmazó adatbázisok biztonságának megerősítése.

Adatfelderítés és besorolás részét képezi a [SQL komplex veszélyforrások elleni védelem](sql-advanced-threat-protection.md) (ATP) ajánlat, amely egységes csomag egy tapasztalt SQL biztonsági funkciók. Adatfelderítés és besorolás érheti el és a központi SQL ATP portálján felügyelt.

> [!NOTE]
> Ez a dokumentum csak az Azure SQL Database vonatkozik. Az SQL Server (helyszíni), lásd: [SQL Adatfelderítés és besorolás](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Mi az Adatfelderítés és besorolás?
Adatfelderítés és besorolás vezet be a speciális szolgáltatások és funkciók az SQL, egy új SQL Information Protection-összeállítást az adatokat, nem csak az adatbázis védelmét célzó képező:
* **Felderítés & javaslatok** – a modul megkeresi az adatbázishoz, és azonosítja a potenciálisan bizalmas adatokat tartalmazó oszlopok. Ezután, egy egyszerűbb megoldást kínál tekintse át, és alkalmazza a megfelelő besorolási javaslatok az Azure Portalon keresztül.
* **Címkézés** – érzékenységi besorolási címkék tartósan címkézett oszlopokon új besorolási metaadatok attribútumok bevezetni az SQL-kezelő használatával. Ezeket a metaadatokat a speciális érzékenységi-alapú naplózás és a védelmi forgatókönyvek alkalmazhatja.
* **Lekérdezés eredményének érzékenység beállítása** – lekérdezés eredményhalmazában érzékenysége kiszámítása a valós idejű a naplózási célokra.
* **Láthatóság** – egy részletes irányítópultján a portálon az adatbázis-besorolás állapota lehet megtekinteni. Emellett letöltheti a jelentés (az Excel-formátumú) megfelelőségi és naplózási célokra, valamint egyéb használandó.

## <a id="subheading-2"></a>Fedezze fel, a besorolás és a bizalmas oszlopok címkézése
A következő szakasz a felderítése, besorolását és címkézését az adatbázisban, valamint az adatbázis aktuális besorolási állapotának megtekintése és jelentések exportálása bizalmas adatokat tartalmazó oszlopok lépéseit ismerteti.

A besorolás két metaadat-attribútum tartalmazza:
* Feliratok – a fő besorolási attribútumok segítségével határozhatók meg az oszlopban tárolt adatok az érzékenységi szint.  
* Adatok típusai – adja meg az oszlopban tárolt adatok be további granularitási.

## <a name="classify-your-sql-database"></a>Az SQL Database besorolása

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. Navigáljon a **komplex veszélyforrások elleni védelem** az Azure SQL Database panelen biztonsági fejléc alatt. Komplex veszélyforrások elleni védelem engedélyezéséhez kattintson, és kattintson a a **adatfelderítés és besorolás (előzetes verzió)** kártya.

   ![Egy adatbázis beolvasása](./media/sql-data-discovery-and-classification/data_classification.png)

3. A **áttekintése** lap az adatbázist, beleértve a részletes listát az összes besorolt oszlopok, amely szűrheti is csak az adott séma részei, adattípusok és címkék megtekintéséhez aktuális besorolási állapotának összegzését tartalmazza. Ha még nem besorolása oszlopokat, [ugorjon az 5](#step-5).

   ![Jelenlegi besorolási állapot összegzése](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Excel-formátumban a jelentés letöltéséhez kattintson a a **exportálása** lehetőséget a felső menüben az ablak.

   ![Exportálás Excelbe](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5.  <a id="step-5"></a>Az adatok osztályozásához megkezdéséhez kattintson a a **osztályozás lapon** az ablak tetején.

    ![Adatok osztályozása](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. A modul megvizsgálja az adatbázis a potenciálisan bizalmas adatokat tartalmazó oszlopok, és listája **oszloposztályozás ajánlott**. Megtekintheti, és besorolási javaslatok alkalmazása:

    * Ajánlott oszloposztályozás listájának megtekintéséhez kattintson az ablak alján a javaslatok panel:

      ![Az adatok besorolása](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

    * Tekintse át a javaslatok – egy adott oszlopra vonatkozó elfogadja, jelölje be a jelölőnégyzetet, a bal oldali oszlopban, a megfelelő sor. Is megjelölheti *összes ajánlás* , a javaslatokat táblázat fejlécében jelölőnégyzet bejelölésével elfogadja.

       ![Tekintse át a javaslatok listája](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

    * A kiválasztott javaslatok a alkalmazni, kattintson a kék **kijelölt javaslatok elfogadása** gombra.

      ![Javaslatok alkalmazása](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Emellett **manuálisan osztályozhatja a** alternatívájaként vagy emellett javaslat-alapú besorolású oszlopok:

    * Kattintson a **besorolás hozzáadása** az ablak felső menüjében.

      ![Manuális besorolás hozzáadása](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

    * A környezet megjelenő ablakban válassza ki a sémát > táblázat > oszlopot, amely szeretné besorolni, és az adatokat és érzékenységi címkét. Kattintson a kék a **besorolás hozzáadása** gombot a környezeti ablak alján.

      ![Osztályozásra oszlop kiválasztása](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Végezze el a besorolást, és tartósan címke (tag) az adatbázis oszlopok az új besorolást metaadatokkal, kattintson a **mentése** az ablak felső menüjében.

   ![Mentés](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Bizalmas adatokhoz történő hozzáférés naplózása

Fontos szempont az information protection paradigmát a rendszer azon képessége, bizalmas adatokhoz való hozzáférés figyelésére. [Az Azure SQL Database naplózási](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) v auditovacím protokolu nevű új mező tartalmazza a továbbfejlesztett *data_sensitivity_information*, amelyek az érzékenységi besorolások (címkék), a tényleges adatok által visszaadott naplók a lekérdezés.

![Napló](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Automatikus/Programmatic besorolás

T-SQL használatával oszloposztályozás hozzáadása/eltávolítása, valamint a teljes adatbázis minden besorolás beolvasása.

> [!NOTE]
> Amikor T-SQL használatával felügyeli a címkéket, nem történik ellenőrzés a szervezeti information protection-szabályzat (a portál javaslatokat megjelenő címkék készletét) létező címkék egy oszlophoz érték hozzáadva. Esetén a ezekhez, hogy ennek ellenőrzése.

* Egy vagy több oszlop osztályozása hozzáadása/frissítése: [tartalmi OSZTÁLYOZÁSHOZ hozzáadása](https://docs.microsoft.com/en-us/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
* Távolítsa el a besoroláshoz egy vagy több oszlop: [dobja el a tartalmi osztályozás](https://docs.microsoft.com/en-us/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
* Minden besorolás megtekintése az adatbázison: [sys.sensitivity_classifications](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

REST API-k használatával programozott módon kezelheti a besorolásokat. A közzétett REST API-k támogatják a következő műveleteket:
* [Hozzon létre vagy frissítsen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) – létrehozza vagy frissíti a érzékenységi címkét egy adott oszlop
* [Törlés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) – a érzékenységi címkét egy adott oszlop törlése
* [Első](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) – lekérdezi a érzékenységi címkét egy adott oszlop
* [Lista által adatbázis](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listbydatabase) – lekérdezi a megadott adatbázis érzékenységi címkék


## <a id="subheading-5"></a>Következő lépések

- Tudjon meg többet [SQL komplex veszélyforrások elleni védelem](sql-advanced-threat-protection.md).
- Érdemes úgy konfigurálni [Azure SQL Database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) a figyelés és naplózás, a minősített bizalmas adatokhoz való hozzáférést.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
