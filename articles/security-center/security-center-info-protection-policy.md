---
title: Az SQL Information Protection testreszabása – Azure Security Center
description: Megtudhatja, hogyan szabhatja testre az Information Protection-szabályzatokat a Azure Security Centerban.
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
ms.openlocfilehash: d37333c0ca3f8acab7a35c23bbab67beef056b72
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664393"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Az SQL Information Protection-szabályzat testreszabása Azure Security Centerban (előzetes verzió)
 
Megadhatja és testreszabhatja a teljes Azure-bérlőhöz tartozó SQL Information Protection-szabályzatot Azure Security Centerban.

Az adatvédelem egy fejlett biztonsági képesség az Azure-beli adatforrásokban lévő bizalmas adatok felfedéséhez, besorolásához, címkézéséhez és védelméhez. A bizalmas adatok (üzleti, pénzügyi, egészségügyi, személyes adatok stb.) felfedése és besorolása kulcsfontosságú szerepet játszik a szervezeti adatok védelmében. A következő infrastruktúrát nyújtja:
- Az adatvédelmi szabványoknak és a szabályozási megfelelőségi követelményeknek való megfelelés elősegítése
- Biztonsági helyzetek, mint például a figyelés (naplózás) és a bizalmas adatok rendellenes hozzáférésének riasztása
- A fokozottan bizalmas adatokat tartalmazó adattárak biztonságának szabályozása és a hozzáférés megerősítése
 
Az [sql Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementálja ezt a paradigmát az SQL-adattárak számára, jelenleg a Azure SQL Database támogatja. Az SQL Information Protection automatikusan felkeresi és osztályozza a potenciálisan bizalmas adatokat, címkéző mechanizmust biztosít a bizalmas adatoknak a besorolási attribútumokkal való tartós címkézéséhez, és részletes irányítópultot jelenít meg a az adatbázis besorolási állapota. Emellett kiszámítja az SQL-lekérdezések eredmény-beállítását is, így a bizalmas adatokat kinyerő lekérdezések explicit módon naplózhatók, és az adatok védetté is menthetők. További információ az SQL Information Protectionről: [Azure SQL Database adatfelderítés és besorolás](../sql-database/sql-database-data-discovery-and-classification.md).
 
A besorolási mechanizmus két elsődleges összeállításon alapul, amelyek a besorolási besorolást – **címkéket** és **adattípusokat**alkotnak.
- **Labels (címkék** ) – a fő besorolási attribútumok, amelyek az oszlopban tárolt adatmennyiség érzékenységi szintjének meghatározására szolgálnak. 
- **Adattípusok** – további részletességi adatokat biztosít az oszlopban tárolt adatok típusához.
 
A Information Protection a címkék és az adattípusok beépített készletét tartalmazza, amelyeket alapértelmezés szerint használ a rendszer. A címkék és típusok testreszabásához Security Centerban testreszabhatja az Information Protection-házirendet.
 
## <a name="customize-the-information-protection-policy"></a>Az Information Protection-szabályzat testreszabása
Az Azure-bérlőhöz tartozó Information Protection-házirend testreszabásához [rendszergazdai jogosultságokkal kell rendelkeznie a bérlő legfelső szintű felügyeleti csoportjában](security-center-management-groups.md). 
 
1. A Security Center főmenüjének erőforrás- **biztonsági higiénia** területén válassza az **adat & Storage** lehetőséget, majd kattintson az **SQL Information Protection** gombra.

   ![Information Protection-házirend konfigurálása](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Az **SQL Information Protection** lapon megtekintheti a címkék aktuális készletét. Ezek a fő besorolási attribútumok, amelyek az adatérzékenység szintjének kategorizálására szolgálnak. Itt konfigurálhatja a bérlő **Information Protection-címkéit** és **adattípusait** . 
 
### <a name="customizing-labels"></a>Címkék testreszabása
 
1. Szerkesztheti vagy törölheti a meglévő címkéket, vagy hozzáadhat egy új címkét is. Meglévő címke szerkesztéséhez válassza ki a címkét, majd kattintson a **Konfigurálás**elemre a jobb oldalon vagy a helyi menüben. Új címke hozzáadásához kattintson a felső menüsorban vagy a címkék tábla alján található **címke létrehozása** elemre.
2. Az **érzékenységi címke beállítása** képernyőn létrehozhat vagy megváltoztathatja a címke nevét és leírását. Azt is beállíthatja, hogy a felirat aktív-e vagy le van-e tiltva az **engedélyezve** kapcsoló be-vagy kikapcsolása esetén. Végül hozzáadhat vagy eltávolíthat a címkéhez társított adattípusokat is. Az ehhez az adattípushoz tartozó összes felderített adat automatikusan tartalmazza a társított érzékenységi címkét a besorolási javaslatokban.
3. Kattintson az **OK** gombra.
 
   ![Érzékenységi címke konfigurálása](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. A feliratok növekvő érzékenységi sorrendben vannak felsorolva. A feliratok közötti rangsor módosításához húzza a címkéket úgy, hogy átrendezi őket a táblázatba, vagy használja a **feljebb és lejjebb gombokat a sorrend** megváltoztatásához. 
 
    ![Information Protection-házirend konfigurálása](./media/security-center-info-protection-policy/move-up.png)
 
5. Ha elkészült, kattintson a **Mentés** gombra a képernyő tetején.
 
 
## <a name="adding-and-customizing-information-types"></a>Adattípusok hozzáadása és testreszabása
 
1. Az adattípusok kezeléséhez és testreszabásához kattintson az **adattípusok kezelése**lehetőségre.
2. Új **adattípus**hozzáadásához válassza a felső menüben az **információ létrehozása típust** . Megadhatja az **adattípus**nevét, leírását és a keresési minta sztringjét. A keresési minta sztringek helyettesítő karakterekkel rendelkező kulcsszavakat is használhatnak (a (z) "%" karakterrel), amelyet az automatikus felderítési motor használ a bizalmas adatok azonosításához az adatbázisokban az oszlopok metaadatai alapján.
 
    ![Information Protection-házirend konfigurálása](./media/security-center-info-protection-policy/info-types.png)
 
3. A beépített **adattípusokat** további keresési minta karakterláncok hozzáadásával, a meglévő sztringek letiltásával vagy a Leírás módosításával is konfigurálhatja. A beépített **adattípusok** nem törölhetők, és nem szerkeszthetők a nevük. 
4. Az **adattípusok** növekvő felderítési rangsorolási sorrendben vannak felsorolva, ami azt jelenti, hogy a listában magasabb típusok először is megpróbálják egyeztetni az adatokat. Az adattípusok rangsorolásának módosításához húzza a típusokat a táblázat jobb oldali helyére, vagy használja a **feljebb és lejjebb gombokat a sorrend** módosításához. 
5. Ha elkészült, kattintson **az OK gombra** .
6. Az adattípusok kezelésének befejezése után ügyeljen arra, hogy a megfelelő címkéket társítsa a megfelelő címkékhez, ehhez kattintson a **Konfigurálás** egy adott címkére lehetőségre, és adja hozzá vagy törölje az adattípusokat.
7. Ügyeljen rá, hogy a fő **címkék** panelen a **Mentés** gombra kattintva alkalmazza az összes módosítást.
 
Miután az adatvédelmi szabályzatot teljesen meghatározta és mentette, a bérlőben lévő összes Azure SQL-adatbázis adatainak besorolására vonatkozik.
 
 
## <a name="next-steps"></a>További lépések
 
Ebben a cikkben megtanulta, hogyan határozhat meg egy SQL Information Protection szabályzatot a Azure Security Centerban. Ha többet szeretne megtudni az SQL-Information Protection használatáról az SQL-adatbázisokban található bizalmas adatok osztályozásához és védelméhez, olvassa el az [adatfelderítés és-besorolás Azure SQL Database](../sql-database/sql-database-data-discovery-and-classification.md). 

A Azure Security Center biztonsági házirendjeivel és adatvédelmével kapcsolatos további információkért tekintse meg a következő cikkeket:
 
- [Biztonsági szabályzatok beállítása Azure Security Centerban](tutorial-security-policy.md): Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és-erőforráscsoportok biztonsági szabályzatait
- [Azure Security Center adatbiztonság](security-center-data-security.md): megtudhatja, hogyan kezeli és védi az adatSecurity Center