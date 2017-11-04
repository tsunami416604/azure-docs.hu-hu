---
title: "Teljesítmény ajánlásokkal – az Azure SQL Database alkalmazása |} Microsoft Docs"
description: "Az Azure-portálon is az Azure SQL adatbázis teljesítményének optimalizálása teljesítmény javaslatokat kereséséhez használja."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 3c621fc557ed466ddf2b514136a32d98be454325
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="find-and-apply-performance-recommendations"></a>Keresse meg és teljesítmény javaslatok alkalmazása

Használhatja az Azure portálon található teljesítmény javaslatokat is optimalizálhatja az Azure SQL adatbázis teljesítményét, vagy a számítási feladatok valamilyen probléma megoldására. **Teljesítmény ajánlás** Azure-portálon lapon áttekintheti a célgyűjtemény meg a felső ajánlásainkat található. 

## <a name="viewing-recommendations"></a>Javaslatok megtekintése

Megtekintheti és teljesítmény ajánlások érvényesek, kell a megfelelő [szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-what-is.md) engedélyek az Azure-ban. **Olvasó**, **SQL DB Contributor** engedélyekre van szükség a javaslatok, megtekintése és **tulajdonos**, **SQL DB Contributor** végre semmilyen műveletet; hozzon létre vagy dobjon el indexeket és indexlétrehozás megszakítását jogosultságokra van szükség.

Azure-portál teljesítménye javaslatok kereséséhez tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg a **további szolgáltatások** > **SQL-adatbázisok**, és válassza ki az adatbázist.
3. Navigáljon a **teljesítmény ajánlás** megtekintése a kijelölt adatbázishoz rendelkezésre álló ajánlott.

Teljesítmény javaslatok hasonlít az alábbi ábrán látható egy táblázatban láthatók:

![Javaslatok](./media/sql-database-advisor-portal/recommendations.png)

Javaslatok a potenciális gyakorolt hatása az alábbi kategóriákba vannak rendezve:

| Gyakorolt hatás | Leírás |
|:--- |:--- |
| Magas |Nagy jelentőségű ajánlásokat kell biztosítania a legjelentősebb teljesítményre gyakorolt hatást. |
| Közepes |Közepes hatás ajánlásokat kell teljesítmény javítása érdekében, de nem jelentősen. |
| Alacsony |Alacsony hatás ajánlásokat kell biztosítania nélkül jobb teljesítményt, de fejlesztései nem mindig jelentős. |


> [!NOTE]
> Az Azure SQL-adatbázist vissza kell tevékenységek figyelését, legalább egy napig néhány megismerése érdekében. Az Azure SQL Database könnyebben optimalizálható lekérdezés konzisztens mintára, mint a véletlenszerű spotty felszakadásáig tevékenység használhatja. Javaslatok még nem állnak rendelkezésre, ha a **teljesítmény ajánlás** lap egy üzenet, amely ismerteti, hogy miért biztosít.
> 

A múltbeli működési állapotát is megtekintheti. További részletek megtekintéséhez javaslat vagy állapot kiválasztása.

Íme egy példa a ajánlás "Create index" az Azure portálon.

![Index létrehozása](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Javaslatok alkalmazása
Az Azure SQL adatbázis teljes szabályozhatják, hogyan a javaslatok még lehetővé teszi a következő három beállítások segítségével engedélyezve: 

* Az egyes javaslatok egy alkalmazza egyszerre.
* Engedélyezze az automatikus hangolással javaslatok automatikus alkalmazását.
* A javaslat manuális alkalmazásához futtassa az ajánlott T-SQL parancsfájlt az adatbázis.

Válassza ki a javaslat a részletek megtekintéséhez, majd **parancsfájl megtekintése** áttekintheti a pontos részleteit a javaslat létrehozását.

Az adatbázis online marad, amíg a javaslat alkalmazása--teljesítmény javaslat használatával, vagy automatikus hangolása soha nem fogad egy adatbázis kapcsolat nélküli.

### <a name="apply-an-individual-recommendation"></a>Az egyes javaslat alkalmazása
Tekintse át, és fogadja el a javaslatok egy egyszerre.

1. Az a **javaslatok** lapon, válassza ki egy javaslatot.
2. Az a **részletek** kattintson **alkalmaz** gombra.
   
    ![A javaslat alkalmazása](./media/sql-database-advisor-portal/apply.png)

Az adatbázis kijelölt javaslat alkalmazzák.

### <a name="removing-recommendations-from-the-list"></a>Javaslatok eltávolítása a listáról
Ha a javaslatok listája az eltávolítani kívánt elemeket tartalmaz, elvetheti a javaslat:

1. Jelölje ki az ajánlás listája **javaslatok** részleteit megnyitásához.
2. Kattintson a **elvetése** a a **részletek** lap.

Ha szükséges, hozzáadhat, biztonsági elvetett elemek a **javaslatok** listája:

1. Az a **javaslatok** kattintson **elvetett nézet**.
2. Válasszon egy eldobott elemet a listából, a részletek megtekintéséhez.
3. Ha úgy gondolja, a **visszavonása elvetése** vissza a fő listáját az index hozzáadása **javaslatok**.


### <a name="enable-automatic-tuning"></a>Automatikus hangolás engedélyezése
Beállíthatja, hogy az Azure SQL Database javaslatok automatikus végrehajtása érdekében. Amint elérhetővé válnak a javaslatok, akkor automatikusan alkalmazni. Csakúgy, mint a teljesítményre gyakorolt hatás negatív esetén a szolgáltatás által kezelt összes javaslatot a javaslat tért vissza.

1. Az a **javaslatok** kattintson **automatizálás**:
   
    ![Az Advisor-beállítások](./media/sql-database-advisor-portal/settings.png)
2. Válassza ki az automatikus műveleteket:
   
    ![Ajánlott indexek](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>Az ajánlott T-SQL parancsfájl manuális futtatása
Válassza ki a javaslat, és kattintson a **parancsfájl megtekintése**. Futtassa a parancsfájlt az adatbázis a javaslat manuális alkalmazásához.

*Manuálisan végrehajtott indexek nem figyeli, és a szolgáltatás által a teljesítményre gyakorolt hatás érvényesíteni* , javasolt, ezek az indexek figyelheti azok biztosít teljesítménynövekedést érhet el és módosítsa vagy törölje azokat, ha szükséges ellenőrzése létrehozása után. Indexek létrehozásával kapcsolatos részletekért lásd: [a CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Javaslatok megszakítása
A javaslatok egy **függőben lévő**, **ellenőrzése**, vagy **sikeres** állapota lehet megszakítani. Javaslatok állapotú **Executing** nem szakítható meg.

1. Válassza ki az ajánlás a **hangolása előzmények** megnyitásához a **ajánlások részleteit** lap.
2. Kattintson a **Mégse** megszakítja a folyamatot a javaslat alkalmazását.

## <a name="monitoring-operations"></a>Figyelési műveletek
A javaslat alkalmazása nem fordulhat elő, azonnal. A portál részletesen ajánlás állapotával kapcsolatban. Az index lehet a lehetséges állapotok a következők:

| status | Leírás |
|:--- |:--- |
| Függőben |A javaslat parancs érkezett, és a végrehajtásra ütemezett alkalmazni. |
| Végrehajtása |A javaslat vonatkozik. |
| Ellenőrzése |A javaslat alkalmazása sikeresen, és a szolgáltatás a következő előnyöket mérés. |
| Sikeres |A javaslat alkalmazása sikeresen és előnyök mért lett. |
| Hiba |Hiba történt az ajánlás alkalmazásának folyamatában. Ez lehet átmeneti jellegű probléma, vagy esetleg a séma módosítsa a táblázat, és a parancsfájl már nem érvényes. |
| Visszaállítás |A javaslat alkalmazta, de nem performant megállapítása, és automatikusan vissza. |
| Vissza |A javaslat vissza lett állítva. |

Kattintson egy folyamaton belüli ajánlott megoldás a listából a további részletek:

![Ajánlott indexek](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Azt ajánljuk, akkor a gyermekrekordokra
Ha a teljesítmény ajánlások a javaslat alkalmazása (azaz manuális futtatása a T-SQL parancsfájl), akkor automatikusan helyreállítja a módosítás Ha úgy találja, a teljesítményre gyakorolt hatás negatív. Ha bármilyen okból egyszerűen vissza szeretné azt ajánljuk, a következőket teheti:

1. Válassza ki a sikeresen elvégzett ajánlást a **előzmények hangolása** területen.
2. Kattintson a **visszaállítás** a a **javaslat részletei** lap.

![Ajánlott indexek](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Ajánlott index teljesítményre gyakorolt hatásának figyelése
Javaslatok sikeres végrehajtása után (jelenleg, műveletek index, és csak a lekérdezések javaslatok parametrizálja), kattintson **lekérdezési** ajánlásával javasolt Részletek lap megnyitásához [lekérdezés Teljesítménybe](sql-database-query-performance.md) és tekintse meg a leggyakoribb lekérdezések a teljesítményre gyakorolt hatást.

![A figyelő teljesítményre gyakorolt hatás](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Összefoglalás
Az Azure SQL-adatbázis SQL-adatbázis teljesítményének javítására vonatkozó javaslatokkal szolgál. T-SQL-parancsfájlok megadásával, kérhet segítséget az adatbázis optimalizálása és végső soron a lekérdezési teljesítmény javítása.

## <a name="next-steps"></a>Következő lépések
A javaslatok figyelése, és továbbra is alkalmazandó, pontosítsa a teljesítményt. Adatbázis-terhelések dinamikusak, és folyamatosan módosítása. Az Azure SQL-adatbázis továbbra is fennáll, figyeléséhez, és adja meg a javaslatok, javíthatja az adatbázis teljesítménye. 

* Lásd: [automatikus hangolása](sql-database-automatic-tuning.md) tudhat meg többet az Azure SQL-adatbázis automatikus hangolása.
* Lásd: [teljesítmény javaslatok](sql-database-advisor.md) teljesítmény javaslatok az Azure SQL Database áttekintését.
* Lásd: [lekérdezési teljesítmény Insights](sql-database-query-performance.md) a teljesítményre gyakorolt hatását a leggyakoribb lekérdezések megtekintésének megismerése.

## <a name="additional-resources"></a>További források
* [A Lekérdezéstár](https://msdn.microsoft.com/library/dn817826.aspx)
* [INDEX LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms188783.aspx)
* [Szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-what-is.md)

