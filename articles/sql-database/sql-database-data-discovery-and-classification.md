---
title: Az Azure SQL Database és az SQL Data Warehouse adatfelderítés és besorolás |} A Microsoft Docs
description: Az Azure SQL Database és az adatok felderítés és besorolás
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: 74bd3af3e1ffd126f8cb4f2347e4566cc4708e25
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58495986"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Az Azure SQL Database és az SQL Data Warehouse adatfelderítés és besorolás

Adatfelderítés és besorolás (jelenleg előzetes verzióban érhető el) biztosít az Azure SQL Database-be épített fejlettebb képességek **felderítése**, **Írisz**, **címkézés**  &  **védelme** a bizalmas adatokat az adatbázisban.
Felderítése és besorolása a leginkább bizalmas adatok (üzleti, pénzügyi, egészségügyi, személyazonosításra alkalmas adatok (PII), és így tovább.) a szervezeti adatok védelme helyzetében kulcsfontosságú szerepet játszhatnak. Az infrastruktúra szolgálhasson:

- Lehetővé teszi, hogy megfeleljen az adatok adatvédelmi szabványok és az előírt megfelelőségi követelmények teljesítését.
- Különféle biztonsági forgatókönyvek, például a (naplózás) figyelési és riasztási a bizalmas adatokhoz való rendellenes hozzáférést.
- Való hozzáférés szabályozása és a bizalmas adatokat tartalmazó adatbázisok biztonságának megerősítése.

Adatfelderítés és besorolás részét képezi a [speciális adatbiztonság](sql-database-advanced-data-security.md) (ADS) ajánlat, amely egységes csomag egy tapasztalt SQL biztonsági funkciók. adatfelderítés és besorolás érheti el és a központi SQL ADS portálon keresztül kezelhetők.

> [!NOTE]
> Ez a dokumentum az Azure SQL Database és az Azure SQL Data Warehouse vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database. Az SQL Server (helyszíni), lásd: [SQL Adatfelderítés és besorolás](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Mi az adatfelderítés és besorolás

Adatfelderítés és besorolás bemutatja a speciális szolgáltatások és az új SQL-képességek, egy új SQL Information Protection-összeállítást az adatokat, nem csak az adatbázis védelmét célzó képező:

- **Felderítési és javaslatok**

  A modul megvizsgálja az adatbázishoz, és azonosítja a potenciálisan bizalmas adatokat tartalmazó oszlopok. Ezután, egy egyszerűbb megoldást kínál tekintse át, és alkalmazza a megfelelő besorolási javaslatok az Azure Portalon keresztül.

- **Címkézés**

  Érzékenységi besorolási címkék új besorolási metaadatok attribútumok bevezetni az SQL-kezelő használatával oszlopokon tartósan címkézett is. Ezeket a metaadatokat a speciális érzékenységi-alapú naplózás és a védelmi forgatókönyvek alkalmazhatja.

- **Lekérdezés eredménye set érzékenység**

  A naplózási célokra valós idejű lekérdezés eredményhalmazában érzékenysége kiszámítása.

- **Láthatóság**

  Az adatbázis besorolási állapotát egy részletes irányítópultján a portálon is megtekinthetők. Emellett letöltheti a jelentés (az Excel-formátumú) megfelelőségi és naplózási célokra, valamint egyéb használandó.

## <a id="subheading-2"></a>Fedezze fel, a besorolás és a bizalmas oszlopok címkézése

A következő szakasz a felderítése, besorolását és címkézését az adatbázisban, valamint az adatbázis aktuális besorolási állapotának megtekintése és jelentések exportálása bizalmas adatokat tartalmazó oszlopok lépéseit ismerteti.

A besorolás két metaadat-attribútum tartalmazza:

- Feliratok – a fő besorolási attribútumok segítségével határozhatók meg az oszlopban tárolt adatok az érzékenységi szint.  
- Adatok típusai – adja meg az oszlopban tárolt adatok be további granularitási.

## <a name="define-and-customize-your-classification-taxonomy"></a>Meghatározása és testre szabása a besorolási besorolás

Az SQL adatfelderítés és besorolás tartalmaz egy beépített érzékenységi címkék és a beépített adattípusok és a felderítési logikát. Most már testre szabhatja a besorolás és a egy készlet és a kifejezetten a környezet a besorolási szerkezeteket rangsorolását megadása.

Meghatározása és testre szabhatja a besorolási besorolás az egész Azure-bérlőhöz egy központi helyen történik. Hogy a hely található a [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), a biztonsági szabályzat részeként. Csak a személy a bérlő legfelső szintű felügyeleti csoport rendszergazdai jogosultságai a feladat végrehajtásához használhatja.

Az Information Protection-házirend felügyelet részeként egyéni címkék megadása, rangsor őket, és rendelje azokat a kiválasztott adatok típusai vannak beállítva. Adja hozzá a saját egyéni adatok típusai is, és konfigurálja őket karakterlánc mintákat, amelyek az ilyen típusú adatok az adatbázisokban azonosítására szolgáló felderítési logika kerülnek.
További tudnivalók a Testreszabás és kezelése a szabályzat a [Information Protection-házirend gyakorlati útmutató](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Miután az összes bérlőre vonatkozó házirend lett meghatározva, továbbra is az egyéni szabályzat használata az egyes adatbázisok besorolású.

## <a name="classify-your-sql-database"></a>Az SQL Database besorolása

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. Navigáljon a **adatok biztonságú** az Azure SQL Database panelen biztonsági fejléc alatt. Lehetővé teszi a speciális biztonsági kattintva, és kattintson a a **adatfelderítés és besorolás (előzetes verzió)** kártya.

   ![Egy adatbázis beolvasása](./media/sql-data-discovery-and-classification/data_classification.png)

3. A **áttekintése** lap az adatbázist, beleértve a részletes listát az összes besorolt oszlopok, amely szűrheti is csak az adott séma részei, adattípusok és címkék megtekintéséhez aktuális besorolási állapotának összegzését tartalmazza. Ha még nem besorolása oszlopokat, [ugorjon az 5](#step-5).

   ![Jelenlegi besorolási állapot összegzése](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Excel-formátumban a jelentés letöltéséhez kattintson a a **exportálása** lehetőséget a felső menüben az ablak.

   ![Exportálás Excelbe](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Az adatok osztályozásához megkezdéséhez kattintson a a **osztályozás lapon** az ablak tetején.

    ![Adatok osztályozása](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. A modul megvizsgálja az adatbázis a potenciálisan bizalmas adatokat tartalmazó oszlopok, és listája **oszloposztályozás ajánlott**. Megtekintheti, és besorolási javaslatok alkalmazása:

   - Ajánlott oszloposztályozás listájának megtekintéséhez kattintson az ablak alján a javaslatok panel:

      ![Az adatok besorolása](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Tekintse át a javaslatok – egy adott oszlopra vonatkozó elfogadja, jelölje be a jelölőnégyzetet, a bal oldali oszlopban, a megfelelő sor. Is megjelölheti *összes ajánlás* , a javaslatokat táblázat fejlécében jelölőnégyzet bejelölésével elfogadja.

       ![Tekintse át a javaslatok listája](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - A kiválasztott javaslatok a alkalmazni, kattintson a kék **kijelölt javaslatok elfogadása** gombra.

      ![Javaslatok alkalmazása](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Emellett **manuálisan osztályozhatja a** alternatívájaként vagy emellett javaslat-alapú besorolású oszlopok:

   - Kattintson a **besorolás hozzáadása** az ablak felső menüjében.

      ![Manuális besorolás hozzáadása](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - A környezet megjelenő ablakban válassza ki a sémát > táblázat > oszlopot, amely szeretné besorolni, és az adatokat és érzékenységi címkét. Kattintson a kék a **besorolás hozzáadása** gombot a környezeti ablak alján.

      ![Osztályozásra oszlop kiválasztása](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Végezze el a besorolást, és tartósan címke (tag) az adatbázis oszlopok az új besorolást metaadatokkal, kattintson a **mentése** az ablak felső menüjében.

   ![Mentés](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Bizalmas adatokhoz történő hozzáférés naplózása

Fontos szempont az information protection paradigmát a rendszer azon képessége, bizalmas adatokhoz való hozzáférés figyelésére. [Az Azure SQL Database naplózási](sql-database-auditing.md) v auditovacím protokolu nevű új mező tartalmazza a továbbfejlesztett *data_sensitivity_information*, amelyek az érzékenységi besorolások (címkék), a tényleges adatok által visszaadott naplók a lekérdezés.

![Napló](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Automatikus/Programmatic besorolás

T-SQL használatával oszloposztályozás hozzáadása/eltávolítása, valamint a teljes adatbázis minden besorolás beolvasása.

> [!NOTE]
> Amikor T-SQL használatával felügyeli a címkéket, nem történik ellenőrzés a szervezeti information protection-szabályzat (a portál javaslatokat megjelenő címkék készletét) létező címkék egy oszlophoz érték hozzáadva. Éppen ezért Ön ennek ellenőrzése.

- Egy vagy több oszlop osztályozása hozzáadása/frissítése: [ADJA HOZZÁ A TARTALMI OSZTÁLYOZÁS](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Távolítsa el a besoroláshoz egy vagy több oszlop: [DOBJA EL A TARTALMI OSZTÁLYOZÁS](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Minden besorolás megtekintése az adatbázison: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

REST API-k használatával programozott módon kezelheti a besorolásokat. A közzétett REST API-k támogatják a következő műveleteket:

- [Hozzon létre vagy frissítsen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) – létrehozza vagy frissíti a érzékenységi címkét egy adott oszlop
- [Törlés](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) – a érzékenységi címkét egy adott oszlop törlése
- [Első](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) – lekérdezi a érzékenységi címkét egy adott oszlop
- [Aktuális által adatbázis listában](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) -lekérdezi az aktuális érzékenységi címkék egy adott adatbázis
- [Adatbázis által ajánlott listában](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) – egy adott adatbázis a javasolt érzékenységi címkék beolvasása

## <a name="permissions"></a>Engedélyek
A következő beépített szerepkörök olvashatja az Azure SQL-adatbázis az adatok besorolását: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` és `User Access Administrator`.

A következő beépített szerepkörök módosíthatja az adatok besorolását, egy Azure SQL Database: `Owner`, `Contributor`, `SQL Security Manager`.

Tudjon meg többet [RBAC az Azure-erőforrásokhoz](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Következő lépések

- Tudjon meg többet [adatbiztonság speciális](sql-database-advanced-data-security.md).
- Érdemes úgy konfigurálni [Azure SQL Database naplózási szolgáltatásával](sql-database-auditing.md) a figyelés és naplózás, a minősített bizalmas adatokhoz való hozzáférést.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
