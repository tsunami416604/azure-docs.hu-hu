---
title: Az SQL information protection-szabályzat az Azure Security Centerben testreszabása |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre az adatvédelmi szabályzatok az Azure Security Centerben.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 9b63fb963408b8f22453c7ea78e36a49402273a7
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105722"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Testre szabhatja az SQL information protection-szabályzat az Azure Security Centerben (előzetes verzió)
 
Egy SQL information protection-szabályzat definiálhatók és testre szabott, teljes Azure-bérlője számára, az Azure Security Centerben.

Information protection egy speciális biztonsági funkció felderítése, a besorolása, a címkézés és a bizalmas adatok az Azure-beli adat-erőforrások védelmét. Felderítése és besorolása a leginkább bizalmas adatok (üzleti, pénzügyi, egészségügyi, személyazonosításra alkalmas adatok, stb.) is kulcsszerepet játszik a szervezeti adatok védelme helyzetében. Az infrastruktúra szolgálhasson:
- Útmutatás nyújtása a felel meg az adatok adatvédelmi szabványok és az előírt megfelelőségi követelmények
- Különféle biztonsági forgatókönyvek, például a (naplózás) figyelési és riasztási a bizalmas adatokhoz való rendellenes hozzáférést
- Való hozzáférés szabályozásának és adatok biztonságának megerősítése tárolja a szigorúan bizalmas adatokat tartalmazó
 
[SQL-információvédelem](../sql-database/sql-database-data-discovery-and-classification.md) valósítja meg a számára az SQL Azure SQL Database jelenleg támogatott adattárak költségei. SQL Information Protection automatikusan felderíti és osztályozza a potenciálisan bizalmas adatokat, lehetővé teszi a címkézési tartósan címkézése a bizalmas adatok besorolása attribútumokkal rendelkező és a egy részletes irányítópult megjelenítő biztosít a besorolás az adatbázis állapotát. Emellett számítja ki az eredmény az SQL-lekérdezések érzékenységi állíthat be, így a lekérdezéseket, amelyek bizalmas adatokat nyerhet ki az explicit módon naplózhatóvá válik, és az adatok védelme biztosítható. Az SQL-információvédelem további részletekért lásd: [Azure SQL Database az Adatfelderítés és besorolás](../sql-database/sql-database-data-discovery-and-classification.md).
 
A besorolási mechanizmus alapul, amely a besorolási besorolás - alkotó két elsődleges szerkezetek **címkék** és **információtípusok**.
- **Címkék** – a fő besorolási attribútumok, segítségével határozhatók meg az oszlopban tárolt adatok az érzékenységi szint. 
- **Adatok típusai** – nyújt további granularitási be az oszlopban tárolt adatok típusát.
 
Information Protection tartalmaz egy beépített címkék és adattípusok, alapértelmezés szerint használt. Ezek testreszabásához testre szabhatja az information protection-szabályzat az Azure Security Centerben.
 
## <a name="customize-the-information-protection-policy"></a>Az Information Protection-szabályzat testreszabása
Az information protection-házirend testreszabása az Azure-bérlőhöz, szüksége lesz [rendszergazdai jogosultságokkal azon a bérlő gyökérszintű felügyeleti csoport](security-center-management-groups.md). 
 
1. A Security Center főmenüjében válassza **biztonsági házirend**.
2. Válassza ki **hierarchikus megjelenítése (előzetes verzió)**, majd a **bérlői legfelső szintű csoport**, kattintson **beállításainak szerkesztése**.
 
   ![Information protection-szabályzat konfigurálása](./media/security-center-info-protection-policy/security-policy.png) 
 
3. A **szabályzat összetevői**, kattintson a **Information protection**. Az a **Information protection-beállítások** lapon megtekintheti az aktuális készletét címkéket. Ezek azok a fő besorolási attribútumok, amelyek az érzékenységi szint, az adatok kategorizálását. Itt konfigurálhatja a **Information protection-címkék** és **információtípusok** a bérlő számára. 
 
### <a name="customizing-labels"></a>A címkék testreszabása
 
1. Szerkeszteni vagy törölni minden olyan meglévő címkét, vagy új címke hozzáadása. Meglévő címke szerkesztéséhez válassza ki a címkét, és kattintson a **konfigurálása**, az oldal tetején, vagy a helyi menüből a jobb oldalon. Új címke hozzáadásához kattintson **címke létrehozása** a felső menüsávon vagy a címkék tábla alján.
2. Az a **konfigurálása jellemzésére** képernyőn hoz létre, vagy módosíthatja a címke neve és leírása. Beállíthat-e a címke átállításával aktív vagy le van tiltva a **engedélyezve** váltson be- és kikapcsolása. Végül adja hozzá, vagy távolítsa el a címke társított adatok típusai. Adatok felderítése, amely megfelel az, hogy az adattípust automatikusan tartalmazza a társított bizalmassági címkére a besorolási javaslatok.
3. Kattintson az **OK** gombra.
 
   ![Érzékenységi címke konfigurálása](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Címkék az érzékenységi növekvő sorrendben vannak felsorolva. Címkék között alapján a rangsorolás, húzza a címkék sorrendjét húzással módosíthatja a tábla, vagy használja a **feljebb** és **mozgatása lefelé** gombok a sorrendjének módosításához. 
 
    ![Information protection-szabályzat konfigurálása](./media/security-center-info-protection-policy/move-up.png)
 
5. Ügyeljen arra, hogy kattintson **mentése** , ha befejezte a képernyő tetején.
 
 
## <a name="adding-and-customizing-information-types"></a>Hozzáadása és testreszabása az adatok típusai
 
1. Kezelheti és adattípusok testreszabása kattintva **információtípusok kezelése**.
2. Egy új **információ típusa**válassza **információ típusa létrehozása** a felső menüben. Egy név, leírás, konfigurálhatja és minta karakterláncok keresése a **információ típusa**. A minta keresőkifejezések kulcsszavakat a helyettesítő karaktereket (a (%) karakter használatával), amely az automatikus felderítő motor használatával azonosítsa a bizalmas adatokat az adatbázisokban, az oszlopok metaadatai alapján használhatnak.
 
    ![Information protection-szabályzat konfigurálása](./media/security-center-info-protection-policy/info-types.png)
 
3. Beállíthatja a beépített **információtípusok** további minta keresőkifejezések, letiltása, a meglévő karakterláncok némelyike hozzáadásával vagy módosításával a leírást. Nem lehet törölni a beépített **információtípusok** vagy szerkesztheti a nevüket. 
4. **Adatok típusai** felderítési rangsoroló, ami azt jelenti, hogy a listában előbb szereplő típusok megfelelő először megpróbálja növekvő sorrendben szerepelnek. Adatok típusai közötti alapján a rangsorolás módosításához a típusok húzza a megfelelő helyszíni a tábla, vagy használja a **feljebb** és **lejjebb** gombok a sorrendjének módosításához. 
5. Kattintson a **OK** Ha elkészült.
6. Miután elvégezte az adatok típusai kezelése, ügyeljen arra, hogy a megfelelő címkéket, kattintson a megfelelő típusú társítása **konfigurálása** egy adott címkét, és a megfelelő adatok típusai hozzáadása vagy törlése.
7. Ügyeljen arra, hogy kattintson **mentése** fő **címkék** panel az összes módosítást a alkalmazni.
 
Miután az Information protection-szabályzat teljesen definiálva, és mentette, a besorolás minden Azure SQL-adatbázisok a bérlőben lévő adatok érvényesek.
 
 
## <a name="next-steps"></a>További lépések
 
Ebben a cikkben megismerkedett az Azure Security Centerben egy SQL Information Protection-szabályzat meghatározása. Az osztályozás és védelem a bizalmas adatokat az SQL-adatbázisok SQL Information Protection használatával kapcsolatos további tudnivalókért lásd: [Azure SQL Database az Adatfelderítés és besorolás](../sql-database/sql-database-data-discovery-and-classification.md). 

Biztonsági szabályzatok és az Azure Security Center által nyújtott adatbiztonság további információkért lásd a következő cikkeket:
 
- [Biztonsági szabályzatok áttekintése](security-center-policies-overview.md): A Security Center biztonsági szabályzatainak áttekintést kaphat
- [Biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md): Ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok
- [Az Azure Security Center által nyújtott adatbiztonság](security-center-data-security.md): Útmutató a Security Center felügyeli, és gondoskodik az adatok


