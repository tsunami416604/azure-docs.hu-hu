---
title: U-SQL adatbázis exportálása – Azure Data Lake-eszközök a Visual Studio számára
description: Megtudhatja, hogy az Azure Data Lake Tools for Visual Studio használatával hogyan exportálhat egy U-SQL-adatbázist, és hogyan importálhatja automatikusan egy helyi fiókba.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: e5a52de0342e864cb108d8d590583fe64f72e3b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315793"
---
# <a name="export-a-u-sql-database"></a>U-SQL adatbázis exportálása

Ebből a cikkből megtudhatja, hogy [miként](https://aka.ms/adltoolsvs) exportálhat u-SQL-adatbázist egyetlen U-SQL-parancsfájlként és letöltött erőforrásként az Azure Data Lake Tools for Visual Studio használatával az U-SQL-adatbázisok egyetlen U-SQL-parancsfájlként és letöltött erőforrásokként történő exportálásához. Az exportált adatbázist ugyanebben a folyamatban importálhatja egy helyi fiókba.

Az ügyfelek általában több környezetben fejlesztési, tesztelési és éles környezetben. Ezek a környezetek egy helyi fiókban, egy fejlesztő helyi számítógépén és egy Azure Data Lake Analytics-fiókban is vannak tárolva az Azure-ban. 

Amikor fejlesztői és tesztelési környezetekben fejleszt és hangol be U-SQL-lekérdezéseket, a fejlesztőknek gyakran újra létre kell hozniuk a munkájukat egy éles adatbázisban. Az Adatbázis-exportálás varázsló segít felgyorsítani ezt a folyamatot. A varázsló használatával a fejlesztők klónozhatják a meglévő adatbázis-környezetet és a mintaadatokat más Data Lake Analytics-fiókokba.

## <a name="export-steps"></a>Lépések exportálása

### <a name="step-1-export-the-database-in-server-explorer"></a>1. lépés: Az adatbázis exportálása a Kiszolgálókezelőben

Minden Data Lake Analytics-fiók, amelyhez engedéllyel rendelkezik, megjelenik a Kiszolgálókezelőben. Az adatbázis exportálása:

1. A Kiszolgálókezelőben bontsa ki az exportálni kívánt adatbázist tartalmazó fiókot.
2. Kattintson a jobb gombbal az adatbázisra, és válassza **az Exportálás parancsot.** 
   
    ![Server Explorer – Adatbázis exportálása](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Ha az **Exportálás** menü nem érhető el, frissítenie kell [az eszközt az utolsó kiadásra.](https://aka.ms/adltoolsvs)

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>2. lépés: Az exportálni kívánt objektumok konfigurálása

Ha egy nagy adatbázisnak csak egy kis része szükséges, az exportálási varázslóban konfigurálhatja az exportálandó objektumok egy részét. 

Az exportálási művelet egy U-SQL feladat futtatásával fejeződik be. Ezért az Azure-fiókból történő exportálás bizonyos költségekkel jár.

![Adatbázis-exportálás varázsló – Objektumok exportálásának kijelölése](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>3. lépés: Ellenőrizze az objektumok listáját és más konfigurációkat

Ebben a lépésben ellenőrizheti a kijelölt objektumokat az **Objektum exportálása listában.** Ha bármilyen hiba történt, válassza **az Előző** lehetőséget a visszalépéshez és az exportálni kívánt objektumok helyes konfigurálásához.

Az exportálási cél egyéb beállításait is beállíthatja. A konfigurációs leírások az alábbi táblázatban találhatók:

|Konfiguráció|Leírás|
|-------------|-----------|
|Cél neve|Ez a név jelzi, hogy hová szeretné menteni az exportált adatbázis-erőforrásokat. Ilyenek például az összeállítások, a további fájlok és a mintaadatok. Egy ilyen nevű mappa jön létre a helyi adatgyökér mappában.|
|Projekt könyvtára|Ez az elérési út határozza meg, hogy hová szeretné menteni az exportált U-SQL parancsfájlt. Az adatbázis-objektum összes definíciója ezen a helyen kerül mentésre.|
|Csak séma|Ha ezt a beállítást választja, csak az adatbázis-definíciók és erőforrások (például az összeállítások és a további fájlok) lesznek exportálva.|
|Séma és adatok|Ha ezt a beállítást választja, a program exportálja az adatbázis-definíciókat, az erőforrásokat és az adatokat. A program a táblák felső N sorait exportálja.|
|Importálás a helyi adatbázisba automatikusan|Ha ezt a beállítást választja, az exportált adatbázis automatikusan importálódik a helyi adatbázisba, amikor az exportálás befejeződött.|

![Adatbázis-exportálás varázsló – Objektumok listájának és egyéb konfigurációknak exportálása](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>4. lépés: Az exportálási eredmények ellenőrzése

Ha az exportálás befejeződött, megtekintheti az exportált eredményeket a varázsló naplóablakában. A következő példa bemutatja, hogyan keresheti meg az exportált U-SQL parancsfájl- és adatbázis-erőforrásokat, beleértve az összeállításokat, a további fájlokat és a mintaadatokat:

![Adatbázis-exportálás varázsló – eredmények exportálása](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Az exportált adatbázis importálása helyi fiókba

Az exportált adatbázis importálásának legkényelmesebb módja, ha bejelöli az **Importálás a helyi adatbázisba automatikusan** jelölőnégyzetet a 3. Ha nem jelölje be ezt a jelölőnégyzetet, először keresse meg az exportált U-SQL parancsfájlt az exportálási naplóban. Ezután futtassa az U-SQL parancsfájlt helyileg az adatbázis helyi fiókba való importálásához.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Az exportált adatbázis importálása Data Lake Analytics-fiókba

Az adatbázis importálása különböző Data Lake Analytics-fiókba:

1. Töltse fel az exportált erőforrásokat, beleértve az összeállításokat, a további fájlokat és a mintaadatokat a Data Lake Analytics-fiók alapértelmezett Azure Data Lake Store-fiókjába, amelybe importálni szeretne. Az exportált erőforrásmappa a helyi adatgyökér mappában található. Töltse fel a teljes mappát az alapértelmezett Data Lake Store-fiók gyökerére.
2. Amikor a feltöltés befejeződött, küldje el az exportált U-SQL parancsfájlt a Data Lake Analytics-fiókba, amelybe importálni szeretné az adatbázist.

## <a name="known-limitations"></a>Ismert korlátozások

Jelenleg, ha **a** 3. Emiatt az adatexportálási folyamat lassú lehet, és költségek merülhetnek fel. 

## <a name="next-steps"></a>További lépések

* [Tudnivalók az U-SQL adatbázisokról](/u-sql/data-definition-language-ddl-statements) 
* [U-SQL feladatok tesztelése és hibakeresése helyi futtatással és az Azure Data Lake U-SQL SDK használatával](data-lake-analytics-data-lake-tools-local-run.md)


