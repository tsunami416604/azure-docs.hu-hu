---
title: Teljesítménnyel kapcsolatos javaslatok alkalmazása
description: Az Azure Portal használatával olyan teljesítményjavaslatokat találhat, amelyek optimalizálhatják az Azure SQL-adatbázis teljesítményét.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: b0452d51dc472e100ef52536d8e3814ff395292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214179"
---
# <a name="find-and-apply-performance-recommendations"></a>Teljesítményjavaslatok keresése és alkalmazása

Az Azure Portal segítségével olyan teljesítményjavaslatokat találhat, amelyek optimalizálhatják az Azure SQL-adatbázis teljesítményét, vagy kijavíthatja a számítási feladatokban azonosított problémákat. **Teljesítményjavaslat** lap az Azure Portalon lehetővé teszi, hogy megtalálja a legnépszerűbb javaslatokat a lehetséges hatásuk alapján.

## <a name="viewing-recommendations"></a>Javaslatok megtekintése

Teljesítményjavaslatok megtekintéséhez és alkalmazásához a megfelelő [szerepköralapú hozzáférés-vezérlési](../role-based-access-control/overview.md) engedélyekre van szüksége az Azure-ban. **A Reader**, **az SQL DB Contributor** engedélyei szükségesek a javaslatok megtekintéséhez, a **Tulajdonos**, SQL **DB Közreműködő** engedélyei pedig a műveletek végrehajtásához; indexek létrehozása vagy eldobása, valamint az indexlétrehozás megszakítása.

Az alábbi lépésekkel teljesítményjavaslatokat találhat az Azure Portalon:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Nyissa meg a **Minden szolgáltatás** > **SQL-adatbázisát,** és jelölje ki az adatbázist.
3. A **Teljesítményjavaslat** elemre navigálva megtekintheti a kijelölt adatbázishoz elérhető javaslatokat.

A teljesítményre vonatkozó javaslatok a táblázatban az alábbiábrához hasonló táblázathoz hasonlóak:

![Javaslatok](./media/sql-database-advisor-portal/recommendations.png)

Az ajánlások a teljesítményre gyakorolt lehetséges hatásuk szerint vannak rendezve a következő kategóriákba:

| Hatás | Leírás |
|:--- |:--- |
| Magasság |A nagy hatású ajánlásoknak kell biztosítaniuk a teljesítményre gyakorolt legjelentősebb hatást. |
| Közepes |A közepes hatásra vonatkozó ajánlásoknak javítaniuk kell a teljesítményt, de nem lényegesen. |
| Alacsony |Az alacsony hatásra vonatkozó ajánlásoknak jobb teljesítményt kell nyújtaniuk, mint a nélkülük, de a fejlesztések nem feltétlenül jelentősek. |

> [!NOTE]
> Az Azure SQL Database-nek legalább egy napig figyelnie kell a tevékenységeket, hogy azonosíthassa az egyes javaslatokat. Az Azure SQL Database könnyebben optimalizálhatja a konzisztens lekérdezési mintákat, mint a véletlenszerű foltos sorozatok tevékenység. Ha a javaslatok jelenleg nem érhetők el, a **Teljesítményjavaslat** lap egy üzenetet tartalmaz, amely elmagyarázza, hogy miért.

Megtekintheti az előzményműveletek állapotát is. Válasszon ki egy javaslatot vagy állapotot a további információk megtekintéséhez.

Íme egy példa a "Index létrehozása" javaslat az Azure Portalon.

![Index létrehozása](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Ajánlások alkalmazása

Az Azure SQL Database teljes körű encikbe juttatja a javaslatok engedélyezését az alábbi három lehetőség valamelyikével:

* Egyéni ajánlásokat alkalmazhat egyenként.
* Engedélyezze az Automatikus hangolás automatikus alkalmazását a javaslatok alkalmazásához.
* Javaslat manuális megvalósítása érdekében futtassa az ajánlott T-SQL-parancsfájlt az adatbázison.

Válassza ki a részleteket megtekintő javaslatot, majd kattintson a **Parancsfájl megtekintése** gombra a javaslat létrehozásának pontos részleteinek áttekintéséhez.

Az adatbázis online marad, amíg a javaslat alkalmazása – teljesítményjavaslat vagy automatikus hangolás soha nem vesz egy adatbázis offline állapotba.

### <a name="apply-an-individual-recommendation"></a>Egyéni ajánlás alkalmazása

A javaslatokat egyenként tekintheti meg és fogadhatja el.

1. A **Javaslatok** lapon válasszon egy javaslatot.
2. A **Részletek** lapon kattintson az **Alkalmaz** gombra.

   ![Javaslat alkalmazása](./media/sql-database-advisor-portal/apply.png)

A kiválasztott javaslat az adatbázisra vonatkozik.

### <a name="removing-recommendations-from-the-list"></a>Ajánlások eltávolítása a listáról

Ha a javaslatok listája olyan elemeket tartalmaz, amelyeket el szeretne távolítani a listából, elvetheti a javaslatot:

1. A részletek megnyitásához válasszon egy **javaslatot** a Javaslatok listájában.
2. Kattintson az **Elvetés** **gombra** a Részletek lapon.

Ha szükséges, hozzáadhatja az elvetett elemeket a **Javaslatok** listához:

1. A **Javaslatok** lapon kattintson az **Elvetett nézet**elemre.
2. A részletek megtekintéséhez jelöljön ki egy elvetett elemet a listából.
3. Ha azt is szeretné, hogy az **Elvetés visszavonása gombra** kattintva adja vissza az indexet a **javaslatok**fő listájához.

> [!NOTE]
> Kérjük, vegye figyelembe, hogy ha az SQL Database [Automatikus hangolás](sql-database-automatic-tuning.md) engedélyezve van, és manuálisan elvetett egy javaslatot a listából, az ilyen javaslat soha nem lesz automatikusan alkalmazva. Egy javaslat elvetése egy praktikus módja annak, hogy a felhasználók automatikus hangolás engedélyezve vannak azokban az esetekben, amikor egy adott javaslat nem kell alkalmazni.
> Ezt a viselkedést úgy állíthatja vissza, hogy visszaadja az elvetett javaslatokat a Javaslatok listához az Elvetés visszavonása beállítás kiválasztásával.

### <a name="enable-automatic-tuning"></a>Automatikus hangolás engedélyezése

Beállíthatja, hogy az Azure SQL Database automatikusan valósítsa meg a javaslatokat. Amint az ajánlások elérhetővé válnak, a rendszer automatikusan alkalmazza azokat. Mint a szolgáltatás által kezelt összes javaslathoz, ha a teljesítményre gyakorolt hatás negatív, a javaslat visszaáll.

1. A **Javaslatok** lapon kattintson az **Automatizálás gombra:**

   ![Tanácsadó beállításai](./media/sql-database-advisor-portal/settings.png)
2. Válassza ki az automatizálandó műveleteket:

   ![Ajánlott indexek](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Kérjük, vegye figyelembe, hogy **DROP_INDEX** beállítás jelenleg nem kompatibilis a partíciókapcsolást és indextippeket használó alkalmazásokkal.

Miután kiválasztotta a kívánt konfigurációt, kattintson az Alkalmaz gombra.

### <a name="manually-apply-recommendations-through-t-sql"></a>Ajánlások manuális alkalmazása a T-SQL-en keresztül

Jelöljön ki egy javaslatot, majd kattintson **a Parancsfájl megtekintése**gombra. Futtassa ezt a parancsfájlt az adatbázison, hogy manuálisan alkalmazza a javaslatot.

*A manuálisan végrehajtott indexeket a szolgáltatás nem figyeli és érvényesíti a teljesítményre gyakorolt hatás érdekében,* ezért azt javasoljuk, hogy a létrehozás után figyelje ezeket az indexeket, hogy ellenőrizze, hogy teljesítménynövekedést biztosítanak-e, és szükség esetén módosítsa vagy törölje őket. Az indexek létrehozásáról az [INDEX LÉTREHOZÁSA (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)című témakörben talál részleteket. Ezenkívül a manuálisan alkalmazott ajánlások aktívak maradnak, és megjelennek a 24-48 órás ajánlások listájában. mielőtt a rendszer automatikusan visszavonja azokat. Ha hamarabb szeretne eltávolítani egy javaslatot, manuálisan elvetheti azt.

### <a name="canceling-recommendations"></a>Ajánlások visszavonása

A **függőben lévő,** **érvényesítési**vagy **sikeres** állapotú javaslatok visszavonhatók. A **végrehajtó** állapotú javaslatok nem vonhatók vissza.

1. Válasszon egy javaslatot a **Hangolási előzmények** területen a **javaslatok részletei** lap megnyitásához.
2. **A Mégse gombra** kattintva szakítsa meg a javaslat alkalmazásának folyamatát.

## <a name="monitoring-operations"></a>Ellenőrzési műveletek

Előfordulhat, hogy egy ajánlás alkalmazása nem történik meg azonnal. A portál az ajánlás állapotával kapcsolatos részleteket tartalmaz. A következőkben lehetséges állapotok, amelyek ben lehet egy index:

| status | Leírás |
|:--- |:--- |
| Függőben |Az Alkalmazás javaslat parancs beérkezett, és végrehajtásra van ütemezve. |
| Végrehajtó |Az ajánlás alkalmazása folyamatban van. |
| Érvényesítése |Az ajánlás alkalmazása sikeresen megtörtént, és a szolgáltatás méri az előnyöket. |
| Sikeres |Az ajánlást sikeresen alkalmazták, és az előnyöket mérték. |
| Hiba |Hiba történt a javaslat alkalmazása során. Ez lehet átmeneti probléma, vagy esetleg a tábla sémája, és a parancsfájl már nem érvényes. |
| Visszatérnek |Az ajánlást alkalmazták, de nem teljesítettnek ítélték, és a rendszer automatikusan visszaállítja. |
| Megtért |Az ajánlást visszaállították. |

További információk megtekintéséhez kattintson a listából egy folyamaton átnyúló javaslatra:

![Ajánlott indexek](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Ajánlás visszaállítása

Ha a teljesítményjavaslatokat használta a javaslat alkalmazásához (ami azt jelenti, hogy nem futtatta manuálisan a T-SQL-parancsfájlt), akkor automatikusan visszaállítja a módosítást, ha a teljesítményre gyakorolt hatást negatívnak találja. Ha bármilyen okból egyszerűen vissza szeretne állítani egy ajánlást, a következőket teheti:

1. Válasszon ki egy sikeresen alkalmazott javaslatot a **Hangolási előzmények** területen.
2. Kattintson a **Visszaállítás gombra** a **javaslat részletei** lapon.

![Ajánlott indexek](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Az indexajánlások teljesítményre gyakorolt hatásának figyelése

A javaslatok sikeres megvalósítása után (jelenleg az indexműveletek és a lekérdezések csak paraméterezése), a **Query Insights** elemre kattintva a javaslat részletei lapon megnyithatja a Query [Performance Insights-ot,](sql-database-query-performance.md) és megtekintheti a leggyakoribb lekérdezések teljesítményhatását.

![A teljesítményre gyakorolt hatás figyelése](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Összefoglalás

Az Azure SQL Database javaslatokat tartalmaz az SQL-adatbázis teljesítményének javítására. A T-SQL-parancsfájlok biztosításával segítséget kap az adatbázis optimalizálásához és végső soron a lekérdezési teljesítmény javításához.

## <a name="next-steps"></a>További lépések

Figyelje a javaslatokat, és továbbra is alkalmazza őket a teljesítmény finomításához. Az adatbázis-munkaterhelések dinamikusak és folyamatosan változnak. Az Azure SQL Database továbbra is figyeli és javaslatokat nyújt, amelyek potenciálisan javíthatják az adatbázis teljesítményét.

* Az [Automatikus hangolás](sql-database-automatic-tuning.md) című témakörben további információ az Azure SQL Database automatikus hangolásáról.
* Az Azure SQL Database teljesítményre vonatkozó javaslatainak áttekintését [a Teljesítményre vonatkozó javaslatok](sql-database-advisor.md) című témakörben találja.
* A [Query Performance Insights](sql-database-query-performance.md) című témakörben olvashat a leggyakoribb lekérdezések teljesítményre gyakorolt hatásának megtekintéséről.

## <a name="additional-resources"></a>További források

* [Lekérdezéstár](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)
