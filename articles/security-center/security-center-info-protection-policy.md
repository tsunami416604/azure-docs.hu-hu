---
title: SQL-információvédelem testreszabása – Azure Security Center
description: Ismerje meg, hogyan szabhatja testre az információvédelmi szabályzatokat az Azure Security Centerben.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: 9c776a32b4a35c72fc40a16afb87db9896a763cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75611066"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Az SQL-információvédelmi házirend testreszabása az Azure Security Centerben (előzetes verzió)
 
Az Azure Security Centerben definiálhat és testreszabhat egy SQL-információvédelmi szabályzatot a teljes Azure-bérlőhöz.

Az információvédelem egy speciális biztonsági képesség a bizalmas adatok felderítésére, osztályozására, címkézésére és jelentésére az Azure-adaterőforrásokban. A legérzékenyebb adatok (üzleti, pénzügyi, egészségügyi, személyes adatok stb.) felfedezése és osztályozása döntő szerepet játszhat a szervezeti információvédelmi termetben. Infrastruktúraként alkalmas lehet az alábbiakra:
- Az adatvédelmi szabványok és a jogszabályi megfelelőségi követelmények teljesítésének segítése
- Biztonsági forgatókönyvek, például figyelés (naplózás) és riasztás a bizalmas adatokhoz való rendellenes hozzáférésről
- A rendkívül érzékeny adatokat tartalmazó adattárakhoz való hozzáférés szabályozása és biztonságának szigorítása
 
[Az SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) ezt a paradigmát valósítja meg az SQL-adattárak, jelenleg támogatott Azure SQL Database. Az SQL Information Protection automatikusan felderíti és osztályolja a potenciálisan bizalmas adatokat, címkézési mechanizmust biztosít a bizalmas adatok osztályozási attribútumokkal való tartós címkézéséhez, és részletes irányítópultot biztosít a az adatbázis besorolási állapota. Emellett kiszámítja az SQL-lekérdezések eredménykészlet-érzékenységét, így a bizalmas adatokat kibontó lekérdezések explicit módon naplózhatók, és az adatok védhetők. Az SQL Information Protection szolgáltatásról az [Azure SQL Database Data Discovery and Classification című](../sql-database/sql-database-data-discovery-and-classification.md)témakörben talál további információt.
 
Az osztályozási mechanizmus két elsődleges konstrukción alapul, amelyek a besorolási besorolást alkotják - **Címkék** és **információtípusok**.
- **Címkék** – Az oszlopban tárolt adatok érzékenységi szintjének meghatározására használt fő besorolási attribútumok. 
- **Információtípusok** – További részletességet biztosít az oszlopban tárolt adatok típusához.
 
Az Információvédelem beépített címkéket és információtípusokat tartalmaz, amelyek alapértelmezés szerint használatosak. A címkék és típusok testreszabásához testreszabhatja a Biztonsági központ információvédelmi házirendjét.
 
## <a name="customize-the-information-protection-policy"></a>Az Information Protection-szabályzat testreszabása
Az Azure-bérlő adatvédelmi szabályzatának testreszabásához [rendszergazdai jogosultságokkal kell rendelkeznie a bérlő gyökérfelügyeleti csoportjában.](security-center-management-groups.md) 
 
1. A Security Center főmenüjében, **az ERŐFORRÁS-biztonság higiéniai** lapon nyissa meg **a Data & storage** menüt, és kattintson az SQL Information **Protection** gombra.

   ![Információvédelmi házirend konfigurálása](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Az **SQL Information Protection** lapon megtekintheti az aktuális címkéket. Ezek azok a fő besorolási attribútumok, amelyek az adatok érzékenységi szintjének kategorizálására szolgálnak. Itt konfigurálhatja az **információvédelmi címkéket** és a bérlő **információtípusait.** 
 
### <a name="customizing-labels"></a>Címkék testreszabása
 
1. Bármely meglévő címkét szerkeszthet vagy törölhet, vagy új címkét adhat hozzá. Meglévő címke szerkesztéséhez jelölje ki a feliratot, majd kattintson a **Beállítás**gombra, legyen az felül vagy a jobb oldali helyi menüben. Új címke hozzáadásához kattintson a **Címke létrehozása gombra** a felső menüsorban vagy a címkék táblázatának alján.
2. Az **Érzékenységi címke konfigurálása** képernyőn létrehozhatja vagy módosíthatja a címke nevét és leírását. Az **Engedélyezve** kapcsoló be- vagy kikapcsolásával azt is beállíthatja, hogy a címke aktív vagy le legyen tiltva. Végül hozzáadhat vagy eltávolíthat a címkéhez társított adattípusokat. Minden olyan felderített adat, amely megfelel az adott információtípusnak, automatikusan tartalmazza a társított érzékenységi címkét a besorolási javaslatokban.
3. Kattintson az **OK** gombra.
 
   ![Érzékenységi címke konfigurálása](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. A címkék növekvő érzékenység szerint vannak felsorolva. A címkék közötti rangsorolás módosításához húzza a címkéket a táblázatban való átrendezésükhöz, vagy a **Fel** és **a Le** gombbal módosítsa a sorrendet. 
 
    ![Információvédelmi házirend konfigurálása](./media/security-center-info-protection-policy/move-up.png)
 
5. Ha végzett, kattintson a **Mentés** gombra a képernyő tetején.
 
 
## <a name="adding-and-customizing-information-types"></a>Információtípusok hozzáadása és testreszabása
 
1. Az információtípusok kezelése és testreszabása az **Információtípusok kezelése**gombra kattintva.
2. Új **információtípus**hozzáadásához válassza az **Információtípus létrehozása lehetőséget** a felső menüben. Az **Információ típushoz**név-, leírás- és keresési mintakarakterláncokat állíthat be. A keresési mintakarakterláncok tetszés szerint használhatnak helyettesítő karaktereket tartalmazó kulcsszavakat (a'%' karakter használatával), amelyeket az automatikus felderítési motor az oszlopok metaadatai alapján az adatbázisok bizalmas adatainak azonosítására használ.
 
    ![Információvédelmi házirend konfigurálása](./media/security-center-info-protection-policy/info-types.png)
 
3. A beépített **információtípusokat** további keresési mintakarakterláncok hozzáadásával, a meglévő karakterláncok letiltásával vagy a leírás módosításával is konfigurálhatja. A beépített **adattípusok** nem törölhetők, és nem szerkeszthetők a nevükkel. 
4. **Az információtípusok** növekvő felderítési rangsor szerint vannak felsorolva, ami azt jelenti, hogy a listában feljebb lévő típusok kísérlik meg először a mérkőzéseket. Az adattípusok közötti rangsorolás módosításához húzza a típusokat a táblázat megfelelő helyére, vagy a **Fel** és **a Le** gombbal módosítsa a sorrendet. 
5. Ha végzett, kattintson **az OK** gombra.
6. Miután befejezte az adattípusok kezelését, mindenképpen társítsa a megfelelő típusokat a megfelelő címkékhez, kattintson a **Beállítás** egy adott címkéhez elemre, és szükség szerint adja hozzá vagy törölje az adattípusokat.
7. Ügyeljen arra, hogy az összes módosítás alkalmazásához kattintson a **Mentés** **gombra** a címkék fő panelén.
 
Miután az információvédelmi szabályzat teljes mértékben definiált és mentett, a bérlő összes Azure SQL-adatbázisának adatbesorolására vonatkozik.
 
 
## <a name="next-steps"></a>További lépések
 
Ebben a cikkben az Azure Security Center SQL Information Protection szabályzatának definiálásáról olvashat. Ha többet szeretne tudni arról, hogy miként szolgálhat az SQL Information Protection használatával az SQL-adatbázisokban lévő bizalmas adatok osztályozására és védelmére, olvassa el az [Azure SQL Database Data Discovery and Classification című témakört.](../sql-database/sql-database-data-discovery-and-classification.md) 

Az Azure Security Center biztonsági szabályzatairól és adatbiztonságáról az alábbi cikkekben talál további információt:
 
- [Biztonsági szabályzatok beállítása az Azure Security Centerben:](tutorial-security-policy.md)Ismerje meg, hogyan konfigurálhatja a biztonsági szabályzatokat az Azure-előfizetésekhez és az erőforráscsoportokhoz
- [Az Azure Security Center adatbiztonsága:](security-center-data-security.md)Ismerje meg, hogyan kezeli és védi a Security Center az adatokat
