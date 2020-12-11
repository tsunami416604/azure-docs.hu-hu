---
title: Érzékenységi címkék automatikus alkalmazása az adataira
description: Megtudhatja, hogyan hozhat létre érzékenységi címkéket, és hogyan alkalmazhatja azokat automatikusan az adataira a vizsgálat során.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: c824e0abea7320a20441e51caa2a05d534ff61b3
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092686"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Az Azure hatáskörébe tartozó adatfeliratok automatikus címkézése

Ez a cikk bemutatja, hogyan hozhat létre a Microsoft Information Protection (MIP) érzékenységi címkéket, és hogyan alkalmazhatja azokat automatikusan az Azure-beli eszközökre az Azure hatáskörébe.

## <a name="what-are-sensitivity-labels"></a>Mik azok az érzékenységi Címkék? 

A munka elvégzéséhez a szervezeten belüli és a szervezeten kívüli személyek is együttműködnek másokkal. Az adatai nem mindig a felhőben maradnak, és gyakran barangolnak mindenhol, az eszközökön, az alkalmazásokon és a szolgáltatásokon keresztül. 

Ha az adatai barangolnak, azt biztonságos, védett módon kell végrehajtania, amely megfelel a szervezet üzleti és megfelelőségi szabályzatának.

Az érzékenységi címkék alkalmazása lehetővé teszi, hogy megossza az egyes adatok bizalmas adatait a szervezetében. Előfordulhat például, hogy egy adott projekt neve nagyon bizalmas a szervezeten belül, míg ugyanez a kifejezés nem bizalmas más szervezeteknek. 

### <a name="sensitivity-labels-in-azure-purview"></a>Érzékenységi címkék az Azure-beli hatáskörébe

A hatáskörébe tartoznak a besorolások a tulajdonosi címkékhez hasonlók, és az adatkészletben található, a vizsgálat során talált adott típusú adattípusok azonosítására szolgálnak.

A hatáskörébe ugyanazokat a besorolásokat (más néven bizalmas adattípusokat) használja, mint a Microsoft 365.  A MIP-érzékenységi címkék a Microsoft 365 biztonsági és megfelelőségi központban (SCC) jönnek létre. Ez lehetővé teszi a meglévő érzékenységi címkék kiterjesztését az Azure hatáskörébe tartozó eszközökre.

> [!NOTE]
> Míg a besorolások közvetlenül vannak összehasonlítva (a társadalombiztosítási szám besorolása **társadalombiztosítási számot** tartalmaz), az érzékenységi címkék akkor lesznek alkalmazva, ha egy vagy több besorolás és forgatókönyv együtt található. 
> 

Az Azure hatáskörébe tartozó érzékenységi címkék használatával automatikusan alkalmazhat címkéket a fájlokra és az adatbázis-oszlopokra.

További információ:

- Tudnivalók a Microsoft 365 dokumentációjában található [érzékenységi címkékről](/microsoft-365/compliance/sensitivity-labels)
- [Mik azok az autocímkézési szabályok?](#what-are-autolabeling-rules)
- [Támogatott adattípusok az érzékenységi címkékhez az Azure-beli hatáskörébe](#supported-data-types-for-sensitivity-labels-in-azure-purview)

#### <a name="what-are-autolabeling-rules"></a>Mik azok az autocímkézési szabályok?

Az adatai folyamatosan növekednek és változnak. A jelenleg címkézett és a címkék manuális alkalmazására szolgáló műveletek követése nem csak nehézkes, hanem szükségtelen fejfájást is okoz. 

Az autocímkézési szabályok az Ön által megadott feltételek, amelyekben egy adott címkét kell alkalmazni. Ha ezek a feltételek teljesülnek, a rendszer automatikusan hozzárendeli a címkét az adathoz, és megőrzi az adatain lévő konzisztens érzékenységi címkéket.

A címkék létrehozásakor ügyeljen arra, hogy az automatikus címkézési szabályokat definiálja mindkét [fájlhoz](#define-autolabeling-rules-for-files) és [adatbázis-oszlophoz](#define-autolabeling-rules-for-database-columns) , hogy automatikusan alkalmazza a címkéket az egyes adatvizsgálatok során. 

Miután ellenőrizte az adatait a hatáskörébe, megtekintheti a hatáskörébe tartozó katalógus és betekintés jelentésekben automatikusan alkalmazott címkéket.

#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Támogatott adattípusok az érzékenységi címkékhez az Azure-beli hatáskörébe

Az alábbi adattípusok esetében a rendszer az Azure hatáskörébe tartozó érzékenységi címkéket támogatja:

|Adattípus  |Források  |
|---------|---------|
|Automatikus címkézés a fájlokhoz     |     – Azure Blob Storage  </br>– Azure Data Lake Storage 1. és 2. generációs  |
|Az adatbázis oszlopainak automatikus címkézése     |  – SQL Server </br>– Azure SQL Database </br>-Azure SQL Database felügyelt példány   <br> – Azure szinapszis  <br>– Azure Cosmos DB   |
| | |

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Érzékenységi Címkék létrehozása a Microsoft 365ban

Ha még nem rendelkezik érzékenységi címkékkel, létre kell hoznia őket, és elérhetővé kell tennie őket az Azure hatáskörébe. A meglévő érzékenységi címkék is módosíthatók, hogy elérhetők legyenek az Azure hatáskörébe.

További információ:

- [Licenckövetelmények](#licensing-requirements)
- [Az érzékenységi címkék kiterjesztése az Azure hatáskörébe](#extending-sensitivity-labels-to-azure-purview)
- [Új érzékenységi Címkék létrehozása vagy meglévő címkék módosítása](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>Licenckövetelmények

A MIP-érzékeny címkéket a Microsoft 365 biztonsági és megfelelőségi központban kell létrehozni és felügyelni. Az Azure hatáskörébe tartozó érzékenységi címkék létrehozásához aktív Microsoft 365 E5-licenccel kell rendelkeznie.

Ha még nem rendelkezik a szükséges licenccel, regisztrálhat [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion)próbaverzióra.

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Az érzékenységi címkék kiterjesztése az Azure hatáskörébe

Alapértelmezés szerint a MIP-érzékenységi címkék csak a Microsoft 365 található eszközökön érhetők el, ahol a fájlokra és az e-mailekre is alkalmazhatók.

Ahhoz, hogy a MIP-érzékenységi címkéket az Azure-ban található Azure-eszközökre alkalmazza, explicit módon jóvá kell hagynia a címkék kiterjesztését, és ki kell választania azokat a címkéket, amelyeknek elérhetőnek kell lennie a hatáskörébe.

A MIP érzékenységi címkéjének az Azure hatáskörébe való kiterjesztésével a szervezetek mostantól felfedezhetik, beoszthatják és betekintést nyerhetnek az adatforrások szélesebb körébe, és minimalizálják a megfelelőségi kockázatot.

> [!NOTE]
> Mivel a Microsoft 365 és az Azure-beli hatáskörébe külön szolgáltatások tartoznak, lehetséges, hogy különböző régiókban lesznek telepítve. A címkék nevei és az egyéni bizalmas adatok típusának nevei a vásárlói adatoknak tekintendők, és alapértelmezés szerint ugyanabban a földrajzi helyen maradnak az adatok érzékenységének védelme és a GDPR-törvények elkerülése érdekében.
>
> Ebben az esetben a címkék és az egyéni bizalmas adattípusok nem az Azure-beli hatáskörébe vannak osztva, és az Ön beleegyezik, hogy az Azure hatáskörébe tartozik.

> [!IMPORTANT]
> Az Ön beleegyezik, hogy a Microsoft a címke nevét és az egyéni bizalmas adatok típusának nevét az Azure hatáskörébe és a Azure Security Center (ASC) *is* megosztja. A Microsoft az Azure hatáskörébe tartozó címke-információkat használja a javaslatok és riasztások ASC-ben való gyarapítása érdekében. 
>
> A Microsoft 365 megfelelőségi központban való hozzájárulás az ezen az adatszolgáltatásokkal való megosztásra vonatkozik. Jelenleg nincs lehetőség a címkézési információk megosztására az Azure hatáskörébe.

**Az érzékenységi címkék kiterjesztése a hatáskörébe:**

A Microsoft 365ban navigáljon a **Information Protection** lapra. A **címkézés kibővítése az eszközökre az Azure-ban**– válassza a **bekapcsolás** gombot, majd a megjelenő megerősítési párbeszédpanelen válassza az **Igen** lehetőséget.

Például:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="Válassza a * * bekapcsolás * * lehetőséget az érzékenységi címkék kiterjesztéséhez a hatáskörébe" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Ha kiterjeszti a címkézést az eszközökre az Azure hatáskörébe, kiválaszthatja azokat a címkéket, amelyeket elérhetővé szeretne tenni a hatáskörébe. További információ: [új érzékenységi Címkék létrehozása vagy meglévő címkék módosítása](#creating-new-sensitivity-labels-or-modifying-existing-labels).
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>Új érzékenységi Címkék létrehozása vagy meglévő címkék módosítása

1. Nyissa meg a [Microsoft 365 biztonsági és megfelelőségi központot](https://protection.office.com/homepage). 

1. A **megoldások** területen válassza az **Adatvédelem**, majd **a címke létrehozása** lehetőséget. 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Érzékenységi Címkék létrehozása a Microsoft 365 biztonsági és megfelelőségi központban" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. Nevezze el a címkét. Ezután **a címke hatókörének meghatározása** területen válassza a **fájlok és e-mailek** és az **Azure hatáskörébe tartozó eszközök** elemet.
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="A címke létrehozása a Microsoft 365 biztonsági és megfelelőségi központban" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. A felirat beállításainál kövesse a varázsló további részeit. 

    Pontosabban definiálja a fájlok és az adatbázis-oszlopok autocímkézési szabályait:

    - [A fájlokra vonatkozó autocímkézési szabályok definiálása](#define-autolabeling-rules-for-files)
    - [Az adatbázis-oszlopokra vonatkozó autocímkézési szabályok definiálása](#define-autolabeling-rules-for-database-columns)

    A varázsló beállításaival kapcsolatos további információkért tekintse meg a Microsoft 365 dokumentációjában a következő témakört: [Mi az érzékenységi címkék](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) .

1. További címkék létrehozásához ismételje meg a fent felsorolt lépéseket. 

    Alcímke létrehozásához válassza ki a szülő címkét > **..**  >  . **További műveletek**  >  **Alcímke hozzáadása**

1. Meglévő feliratok módosításához tallózással keresse meg **Information Protection**  >  **címkéket**, és válassza ki a címkét. 

    Ezután válassza a **címke szerkesztése** lehetőséget, és nyissa meg ismét az **érzékenységi címke szerkesztése** varázslót, és adja meg az összes olyan beállítást, amelyet a címke létrehozásakor adott meg.

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="Meglévő érzékenységi címke szerkesztése" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. Ha elkészült az összes címke létrehozásával, győződjön meg róla, hogy megtekinti a címke sorrendjét, és szükség szerint átrendezi őket. 

    A címkék sorrendjének módosításához válassza a.. **.** **> további műveletek**  >  **feljebb** vagy lejjebb lehetőséget **.** 

    További információkért lásd a Microsoft 365 dokumentációjának [címkék prioritása (rendelési kérdések)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) című témakörét.

> [!IMPORTANT]
> Ne törölje a címkét, hacsak nem érti a felhasználókra gyakorolt hatást. 
>
> További információ: [címkék eltávolítása és törlése](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) a Microsoft 365 dokumentációjában.

Folytassa az [adatai vizsgálatával, hogy automatikusan alkalmazza a címkéket](#scan-your-data-to-apply-labels-automatically), majd:

- [Eszközök címkéjének megtekintése](#view-labels-on-assets)
- [A besorolások és az érzékenységi címkék megtekintése a betekintési jelentésekben](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-autolabeling-rules-for-files"></a>A fájlokra vonatkozó autocímkézési szabályok definiálása

A címke létrehozásakor vagy szerkesztésekor a varázslóban található fájlokra vonatkozó autocímkézési szabályok definiálása. 

Az **Office-alkalmazások automatikus címkézése** lapon engedélyezze az **Office-alkalmazások automatikus címkézését,** majd adja meg azokat a feltételeket, amelyekben automatikusan alkalmazni szeretné a címkét az adataira.

Például:

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="A Microsoft 365 biztonsági és megfelelőségi központban található fájlokra vonatkozó autocímkézési szabályok definiálása" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
További információ: [érzékenységi címke alkalmazása automatikusan az adatokra](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) a Microsoft 365 dokumentációjában. 

#### <a name="define-autolabeling-rules-for-database-columns"></a>Az adatbázis-oszlopokra vonatkozó autocímkézési szabályok definiálása

A címke létrehozásakor vagy szerkesztésekor a varázslóban található adatbázis-oszlopokra vonatkozó autocímkézési szabályok definiálása. 

Az **Azure hatáskörébe tartozó eszközök (előzetes verzió)** lehetőségnél:

1. Válassza az **automatikus címkézés az adatbázis oszlopaihoz** csúszkát.

1. Jelölje be a **bizalmas adattípus-típusok jelölőnégyzetet** , és válassza ki a címkére alkalmazni kívánt bizalmas adatokat.

Például:
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Az SQL-oszlopokra vonatkozó autocímkézési szabályok definiálása a Microsoft 365 biztonsági és megfelelőségi központban" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>A feliratok automatikus alkalmazásának ellenőrzése az adataiban

Ellenőrizze az adatait az Azure hatáskörébe, hogy automatikusan alkalmazza a létrehozott címkéket a definiált automatikus címkézési szabályok alapján. 

További információ arról, hogyan állíthatja be a vizsgálatokat különböző eszközökön az Azure hatáskörébe, lásd:

|Forrás  |Referencia  |
|---------|---------|
|**Azure Blob Storage**     |[Azure-Blob Storage regisztrálása és vizsgálata](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake Storage**     |[Azure Data Lake Storage Gen1 regisztrálása és vizsgálata](register-scan-adls-gen1.md) </br>[Azure Data Lake Storage Gen2 regisztrálása és vizsgálata](register-scan-adls-gen2.md)         |
|**Azure SQL Databases**|[Azure SQL Database regisztrálása és vizsgálata](register-scan-azure-sql-database.md) </br>[Azure SQL Database felügyelt példány regisztrálása és bevizsgálása](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>Eszközök címkéjének megtekintése

Miután meghatározta a címkékre vonatkozó automatikus címkézési szabályokat Microsoft 365 és az adatok beolvasása az Azure-ban, a rendszer automatikusan felveszi a címkéket a saját eszközeire. 

**Az eszközökre alkalmazott címkék megtekintéséhez az Azure hatáskörébe katalógusában:**

Az Azure-beli hatáskörébe katalógusban használja a címkék szűrésére szolgáló beállításokat, hogy **csak a megadott** címkékkel rendelkező fájlokat jelenítse meg. Például: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="Eszközök keresése címke szerint" lightbox="media/create-sensitivity-label/filter-search-results.png":::

Például:

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Az Azure-Blob Storage egy fájlján megtekintheti az érzékenységi címkét" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>A besorolások és az érzékenységi címkék megtekintése a betekintési jelentésekben

A **besorolási** és az **érzékenységi címkézési** jelentések segítségével megtalálhatja az Azure-beli besorolási és címkézett adataival kapcsolatos elemzéseket.

> [!div class="nextstepaction"]
> [Besorolási ismeretek](./classification-insights.md)

> [!div class="nextstepaction"]
> [Érzékenység feliratának felismerése](sensitivity-insights.md)


