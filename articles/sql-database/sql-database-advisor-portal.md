---
title: Teljesítménnyel kapcsolatos javaslatok – Azure SQL Database alkalmazása |} A Microsoft Docs
description: Az Azure portal használatával kereshet a teljesítménnyel kapcsolatos javaslatok, optimalizálhatja az Azure SQL Database teljesítményét.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: d06d29fb43ea8d8971c3f013c1e66406a30798d3
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064256"
---
# <a name="find-and-apply-performance-recommendations"></a>Keresse meg és teljesítménnyel kapcsolatos javaslatok alkalmazása

Az Azure Portalon is használhatja, teljesítménnyel kapcsolatos javaslatok, optimalizálhatja az Azure SQL Database teljesítményét, vagy valamilyen probléma a számítási feladatok alapján. **Teljesítmény javaslat** lap az Azure Portalon lehetővé teszi, hogy keresse meg a leggyakoribb ajánlásokat potenciális hatásuk alapján. 

## <a name="viewing-recommendations"></a>Javaslatok megtekintése

Megtekintheti és a teljesítménnyel kapcsolatos javaslatok alkalmazása van szüksége a megfelelő [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) engedélyek az Azure-ban. **Olvasó**, **SQL DB Contributor** engedélyekre van szükség, javaslatok, megtekintése és **tulajdonosa**, **SQL-Adatbázisok Közreműködője** engedélyek szükségesek hajtsa végre a műveleteket; Hozzon létre vagy dobjon el indexeket, és megszakítja a index létrehozását.

A következő lépések segítségével keresse meg a teljesítménnyel kapcsolatos javaslatok az Azure Portalon:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Lépjen a **minden szolgáltatás** > **SQL-adatbázisok**, és válassza ki az adatbázist.
3. Navigáljon a **teljesítmény javaslat** a kiválasztott adatbázishoz elérhető javaslatok megtekintése.

Teljesítménnyel kapcsolatos javaslatok láthatók a hasonló az alábbi ábrán látható a táblázatban:

![Javaslatok](./media/sql-database-advisor-portal/recommendations.png)

Javaslatok a potenciális hatása a teljesítményre, a következő kategóriákba vannak rendezve:

| Hatás | Leírás |
|:--- |:--- |
| Magas |Nagy hatású javaslatok kell biztosítania a legjelentősebb teljesítményre gyakorolt hatás. |
| Közepes |Közepes hatású javaslatok kell javítható a teljesítmény, de a rendszer lényegében nem. |
| Alacsony |Kis hatású javaslatok nélkül jobb teljesítményt kell biztosítania, de fejlesztései nem mindig jelentős. |


> [!NOTE]
> Az Azure SQL Database kell tevékenységek figyelését, legalább egy napig ajánlásokat azonosítása érdekében. Az Azure SQL Database könnyebben optimalizálhatja a konzisztens lekérdezési minták, mint amennyit fel tud tevékenység véletlenszerű jól csúcsforgalomra. Ha a javaslatok nem érhető el, a **teljesítmény javaslat** oldal nyújt egy üzenet tájékoztatja, hogy miért.
> 

A korábbi műveletek állapotát is megtekintheti. Válasszon ki egy javaslat vagy állapota további információkat.

Íme egy példa, ajánlás "Create index" az Azure Portalon.

![Index létrehozása](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Javaslatok alkalmazása
Az Azure SQL Database teljes körűen javaslatok láthatók, hogyan biztosítja a következő három módon bármelyikével engedélyezve: 

* Az egyes javaslatok egy alkalmazása egyszerre.
* Engedélyezze az automatikus hangolás automatikusan alkalmazhatja a javaslatokat.
* Egy javaslat manuálisan implementálásához futtassa a javasolt a T-SQL parancsfájl az adatbázison.

Válassza ki a részletek megtekintéséhez, majd bármely javaslatra **parancsfájl megjelenítése** áttekintheti, hogyan hozza létre a javaslatot a pontos részleteiért.

Az adatbázis online állapotban marad, amíg a javaslat alkalmazása--teljesítmény javaslat használatával, vagy az automatikus hangolás soha nem vesz igénybe egy adatbázis kapcsolat nélküli.

### <a name="apply-an-individual-recommendation"></a>Az egyes javaslatok alkalmazása
Tekintse át, és egyszerre ajánlásait egy fogadni.

1. Az a **javaslatok** lap, válasszon ki egy javaslatot.
2. Az a **részletek** kattintson **alkalmaz** gombra.
   
    ![Az ajánlás alkalmazása](./media/sql-database-advisor-portal/apply.png)

Kiválasztott ajánlás érvénybe lépnek az adatbázison.

### <a name="removing-recommendations-from-the-list"></a>Javaslatok eltávolítása a listából
Ha a javaslatok listája, amelyek a el kívánja távolítani a listában több elemet tartalmaz, elvetheti a javaslat:

1. Válasszon ki egy javaslatot listájában **javaslatok** , nyissa meg a részleteket.
2. Kattintson a **elveti** a a **részletek** lapot.

Ha szükséges, hozzáadhat, az elvetett elemek biztonsági a **javaslatok** lista:

1. Az a **javaslatok** kattintson **elvetett javaslatok megtekintése**.
2. Az elvetett elemet kijelölhet a listában a részletek megtekintéséhez.
3. Rákattinthat **visszavonása elveti** hozzáadása az index vissza a fő listájának **javaslatok**.

> [!NOTE]
> Vegye figyelembe, hogy ha az SQL Database [az automatikus hangolás](sql-database-automatic-tuning.md) engedélyezve van, és ha manuálisan elvetett egy javaslatot a listából az ilyen javaslat soha nem automatikusan lesznek érvényesek. Egy javaslat elvetése a felhasználók számára, hogy egy adott javaslatot nem alkalmazható igénylő esetekben engedélyezve az automatikus hangolás sablonszolgáltatása segítségével kényelmesen.
> Ez a viselkedés térhet vissza a javaslatok listája a elvetett javaslatok visszavonása elveti beállítás kiválasztásával adja hozzá.
> 

### <a name="enable-automatic-tuning"></a>Automatikus hangolás engedélyezése
Az Azure SQL Database automatikusan ajánlások megvalósítása állíthatja be. Amint elérhetővé válnak a javaslatok, azokat automatikusan alkalmazza a rendszer. Mivel az összes ajánlást, a szolgáltatás által kezelt, ha a teljesítményre gyakorolt negatív, az ajánlás vissza lett vonva.

1. Az a **javaslatok** kattintson **automatizálása**:
   
    ![Az Advisor-beállítások](./media/sql-database-advisor-portal/settings.png)
2. Műveletek automatizálása kiválasztása:
   
    ![Ajánlott indexek](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Vegye figyelembe, hogy **DROP_INDEX** lehetőség jelenleg nem kompatibilis alkalmazások használatával a partíció közötti váltás és index mutatókat, és ezekben az esetekben nem szabad engedélyezni.
>

Miután kiválasztotta a kívánt beállításait, kattintson az Alkalmaz gombra.

### <a name="manually-run-the-recommended-t-sql-script"></a>A javasolt a T-SQL parancsfájl manuális futtatása
Jelölje ki bármely javaslatra, és kattintson a **parancsfájl megjelenítése**. Futtassa ezt a szkriptet az adatbázison a manuálisan alkalmazhatja a javaslatot.

*Manuálisan végrehajtott indexek nem monitorozott és a teljesítményt a a szolgáltatás által ellenőrzött* ezért javasoljuk, hogy nyomon, hogy ezek az indexek ellenőrizheti azok teljesítménynövekedést biztosít, és módosítsa vagy törölje őket, ha szükséges a létrehozása után. Az indexek létrehozásával kapcsolatos részletekért lásd: [a CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Javaslatok visszavonása
Az ajánlások egy **függőben lévő**, **érvényesítés**, vagy **sikeres** állapot szakadhatnak meg. Javaslatok állapottal **Executing** nem szakítható meg.

1. Válasszon ki egy javaslatot a a **hangolási előzmények** területen nyissa meg a **ajánlások részleteit** lapot.
2. Kattintson a **Mégse** megszakítja a folyamatot a javaslat alkalmazása.

## <a name="monitoring-operations"></a>Figyelési műveletek
A javaslat alkalmazása nem fordulhat elő, azonnal. A portálon részletesen ajánlás állapotával kapcsolatban. Lehetséges, hogy az index az állapotok a következők:

| status | Leírás |
|:--- |:--- |
| Függőben |A alkalmazni javaslat érkezett parancsot, és ütemezve van végrehajtásra. |
| Végrehajtás |Az ajánlás alkalmazása folyamatban van. |
| Érvényesítés |Javaslatok sikeresen alkalmazva lett, és a szolgáltatás mérés előnyeit. |
| Sikeres |Az ajánlás alkalmazása sikerült és előnyöket mért lett. |
| Hiba |Hiba történt az ajánlás alkalmazásának folyamatában. Ez lehet egy átmeneti probléma, vagy akár egy sémát módosítani a tábla, és a szkript már nem érvényes. |
| Visszaállítás |Az ajánlás lett alkalmazva, de nem nagy teljesítményű minősül, és automatikusan vissza. |
| Visszaállítva |Az ajánlás vissza lett állítva. |

További információkat a listából egy folyamaton belüli javaslatra kattintva:

![Ajánlott indexek](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Visszaállítás egy javaslat
Ha a javaslat alkalmazása a teljesítménnyel kapcsolatos javaslatok (azaz manuálisan nem futtatta a T-SQL parancsfájl), automatikusan visszaáll a módosítás Ha megtalálja a teljesítményre gyakorolt negatív. Ha bármilyen okból egyszerűen vissza kívánja egy javaslatot, a következőket teheti:

1. Válasszon ki egy javaslatot, amelyek sikeresen alkalmazva a a **hangolási előzmények** területen.
2. Kattintson a **visszaállítás** a a **javaslat részletei** lapot.

![Ajánlott indexek](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Figyelés teljesítményre gyakorolt hatásának ajánlott indexek
Javaslatok sikeres bevezetése után (jelenleg műveletek index és a lekérdezések javaslatok csak paraméterezni) elemre **betekintés a Lekérdezésekbe** ajánlására Részletek lap megnyitásához [lekérdezés Teljesítményelemzés](sql-database-query-performance.md) és a teljesítményre gyakorolt hatás, a lekérdezések megtekintéséhez.

![A figyelő teljesítményre gyakorolt hatás](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Összegzés
Az Azure SQL Database az SQL database szolgáltatás teljesítményének javítására vonatkozó javaslatokat biztosít. Azáltal, hogy a T-SQL-szkriptek, optimalizálása az adatbázishoz, és végső soron a lekérdezési teljesítmény javításához segítséget kap.

## <a name="next-steps"></a>További lépések
Figyelheti a javaslatokat, és továbbra is alkalmazhatja őket, amellyel pontosíthatja a teljesítményt. Adatbázis-munkaterhelés dinamikusak, és folyamatosan változik. Az Azure SQL Database továbbra is megfigyelheti és javíthatja az adatbázis teljesítményét javaslatokkal. 

* Lásd: [az automatikus hangolás](sql-database-automatic-tuning.md) tudhat meg többet az Azure SQL Database automatikus finomhangolása.
* Lásd: [teljesítménnyel kapcsolatos javaslatok](sql-database-advisor.md) áttekintheti az Azure SQL Database teljesítménnyel kapcsolatos javaslatok.
* Lásd: [lekérdezési teljesítmény Elemzéseihez](sql-database-query-performance.md) , a teljesítményre gyakorolt hatás, a lekérdezések megtekintésének megismerése.

## <a name="additional-resources"></a>További források
* [Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
* [INDEX LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms188783.aspx)
* [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)

