---
title: Azure Data Lake Tools for Visual Studio használatával U-SQL-adatbázis exportálása
description: Útmutató U-SQL-adatbázisok exportálását, és automatikusan importálni kell egy helyi fiók az Azure Data Lake Tools for Visual Studio használatával.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 7a4aae87cd704cf94c89c308c0c056ae5b9a8b4d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251002"
---
# <a name="export-a-u-sql-database"></a>U-SQL-adatbázis exportálása

Ebből a cikkből megtudhatja, hogyan használható [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) U-SQL-adatbázisok exportálását egy U-SQL parancsfájl és a letöltött erőforrásokhoz. Importálhatja az exportált adatbázis egy helyi fiók ugyanabban a folyamatban.

Ügyfelek általában több fejlesztési, tesztelési és éles környezetek karbantartása. Ezekben a környezetekben egyaránt egy helyi fiók, egy fejlesztői helyi számítógépen, és a egy Azure Data Lake Analytics-fiókban, az Azure-ban üzemel. 

Fejlesztésekor és fejlesztési-tesztelési környezetet az U-SQL-lekérdezések hangolása, fejlesztőknek gyakran kell újra létre kell hoznia egy éles adatbázist a munkájukat. Adatbázis exportálása varázsló segítségével felgyorsíthatja az ezt a folyamatot. A varázsló segítségével a fejlesztők számára a meglévő adatbázis-környezet és más Data Lake Analytics-fiókokba mintaadatok klónozhat.

## <a name="export-steps"></a>Az exportálási lépéseket

### <a name="step-1-export-the-database-in-server-explorer"></a>1. lépés: A Server Explorerben adatbázis exportálása

Minden Data Lake Analytics-fiók, amely rendelkezik engedélyekkel a Server Explorer szerepelnek. Az adatbázis exportálása:

1. A Server Explorerben bontsa ki a fiókot, amely tartalmazza az exportálni kívánt adatbázist.
2. Kattintson a jobb gombbal az adatbázist, és válassza **exportálása**. 
   
    ![Server Explorer - adatbázis exportálása](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Ha a **exportálása** menüpont nem érhető el, újra kell [frissítse az eszközt a legfrissebb kiadás](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>2. lépés: Konfigurálja az exportálandó objektumokat

Ha nagy méretű adatbázisok csak kis részét kell, konfigurálhatja úgy, hogy az exportálási varázslóban exportálandó objektumok egy részét. 

Az exportálási művelet befejeződött, egy U-SQL-feladat futtatásával. Ezért az Azure-fiók exportálása költségekkel jár.

![Adatbázis exportálása varázsló – objektumok exportálása kiválasztása](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>3. lépés: Ellenőrizze az objektumok és más konfigurációk

Ebben a lépésben ellenőrizheti a kiválasztott objektumok a **exportálási objektumlistához** mezőbe. Ha nincsenek hibák, válassza ki a **előző** visszaléphet, és megfelelően konfigurálja az exportálandó objektumokat.

Az Exportálás cél egyéb beállításai is konfigurálhatók. Konfiguráció leírása az alábbi táblázatban láthatók:

|Konfiguráció|Leírás|
|-------------|-----------|
|Cél neve|Ez a név azt jelzi, ahol az exportált adatbázis-erőforrások menteni szeretné. Példák szerelvények, további fájlok és a mintaadatokat. Egy ilyen nevű mappa a helyi adatok legfelső szintű gyermekmappát hoz létre.|
|Projektkönyvtár|Ezt az elérési utat határozza meg, ahová az exportált U-SQL parancsfájl menteni szeretné. Az összes adatbázis objektumdefiníciók ezen a helyen lesznek mentve.|
|Csak a séma|Ha ezt a lehetőséget választja, csak adatbázis-definíciók és erőforrások (például a szerelvények és további fájlokat) exportálja.|
|Séma és adatok|Ezt a beállítást, ha az adatbázis-definíciók, erőforrások és adatok exportálva. A felső N sorát a táblák exportálja.|
|A helyi adatbázis automatikus importálása|Ezt a beállítást, ha az exportált adatbázist a rendszer automatikusan importálja a helyi adatbázis exportálásakor befejeződött.|

![Adatbázis exportálása varázsló – objektumok lista exportálása és egyéb konfigurációk](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>4. lépés: Az exportálási eredmények ellenőrzése

Ha exportálása befejeződött, megtekintheti az exportált eredmények a napló ablak a varázslóban. Az alábbi példa bemutatja, hogyan találhatja meg az exportált U-SQL parancsfájl és adatbázis-erőforrásokat, szerelvényeket, további fájlok és a mintaadatok többek között:

![Adatbázis exportálása varázslóban - exportálási eredmények](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importálja az exportált adatbázist egy helyi fiók

Importálja az exportált adatbázist a legmegfelelőbb módszer az, hogy válassza ki a **importálása a helyi adatbázis automatikusan** jelölőnégyzetet a 3. lépésben a exportálása során. Ha Ön nem ezt a jelölőnégyzetet, először az exportált U-SQL-szkriptet a exportálási naplóban található. Ezután futtassa helyileg a U-SQL parancsfájl importálja az adatbázist a helyi fiók.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importálja az exportált adatbázist Data Lake Analytics-fiók

Az adatbázis importálása másik Data Lake Analytics-fiókhoz:

1. Töltse fel az exportált erőforrások, beleértve a szerelvényeket, további fájlok és a mintaadatokat, a Data Lake Analytics-fiók, amely az importálni kívánt alapértelmezett Azure Data Lake Store-fiókba. Az exportált resource mappát a helyi adatok gyökérmappájában található. Töltse fel a teljes mappát az alapértelmezett Data Lake Store-fiók gyökérmappájában.
2. Ha befejeződött a feltöltés, küldje el az exportált U-SQL parancsfájl importálja az adatbázist, hogy a Data Lake Analytics-fiókhoz.

## <a name="known-limitations"></a>Ismert korlátozások

Jelenleg Ha a **séma és adatok** futtat egy U-SQL-feladatot, amely a táblákban tárolt adatok exportálása a 3. lépés: az eszköz. Emiatt előfordulhat, hogy az adatok exportálása folyamat lassú, és, előfordulhat, hogy a személy költségei. 

## <a name="next-steps"></a>További lépések

* [További információ a U-SQL-adatbázisok](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [U-SQL feladatok tesztelése és hibakeresése helyi futtatással és az Azure Data Lake U-SQL SDK használatával](data-lake-analytics-data-lake-tools-local-run.md)


