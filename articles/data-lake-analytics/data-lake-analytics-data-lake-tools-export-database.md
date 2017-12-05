---
title: "Egy Azure Data Lake Tools for Visual Studio használatával U-SQL-adatbázis-exportálási |} Microsoft Docs"
description: "Útmutató: Azure Data Lake Tools for Visual Studio segítségével exportálja a U-SQL-adatbázis, és automatikusan importálja a helyi fiók."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 441606258f9541c9552925e7c0cbc9b3a9effb4d
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="export-a-u-sql-database"></a>U-SQL-adatbázis exportálása

Ebből a cikkből megtudhatja, hogyan használható [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) U-SQL-adatbázis exportálása egy U-SQL parancsfájl és a letöltött erőforrásokhoz. Az exportált adatbázist importálhatja a helyi fiók ugyanabban a folyamatban.

Az ügyfelek általában karbantartása több fejlesztői, tesztelési és éles környezeteket. Ezekben a környezetekben egyaránt helyi fiók, a fejlesztő helyi számítógépen, és az Azure Data Lake Analytics-fiók az Azure-ban van közzétéve. 

Fejlesztésekor és fejlesztési és tesztelési környezetben U-SQL lekérdezések finomhangolása, fejlesztők gyakran kell hozza létre az éles adatbázis munkájukat. Az adatbázis exportálása varázslóval egyre gyorsabban jelennek meg ezt a folyamatot. A varázsló segítségével a fejlesztők számára a meglévő adatbázis-környezet és a mintaadatok más Data Lake Analytics-fiókokba tud klónozni.

## <a name="export-steps"></a>Exportálási lépések

### <a name="step-1-export-the-database-in-server-explorer"></a>1. lépés: Az adatbázis, a Server Explorer exportálása

Összes Data Lake Analytics-fiók, amely engedélyekkel rendelkezik a Server Explorer láthatók. Az adatbázis-exportálási:

1. A Server Explorer eszközben bontsa ki a fiókot, amely tartalmazza az exportálni kívánt adatbázist.
2. Kattintson a jobb gombbal az adatbázist, majd válassza ki **exportálása**. 
   
    ![Server Explorer - adatbázis exportálása](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Ha a **exportálása** menüpont nem érhető el, újra kell [az eszköz frissítése a legfrissebb verzióra](http://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>2. lépés: Az exportálni kívánt objektumok konfigurálása

Ha nagy adatbázist csak kis részét, beállíthatja a exportálása varázsló exportálni kívánt objektumok egy részét. 

Az exportálási művelet befejeződött egy U-SQL-feladat futtatásával. Ezért az Azure-fiók exportálása költségekkel jár.

![Adatbázis exportálása varázslóban - válassza objektumok exportálása](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>3. lépés: Ellenőrizze a objektumlistához és egyéb beállításokra

Ebben a lépésben ellenőrizheti a kijelölt objektumokat a **exportálás objektumlistához** mezőbe. Ha hiba történik, válassza ki a **előző** lépjen vissza, és megfelelően konfigurálja az exportálandó objektumokat.

Az exportálási cél számára más beállításokat is konfigurálhatja. Konfigurációs leírása az alábbi táblázatban láthatók:

|Konfiguráció|Leírás|
|-------------|-----------|
|Cél neve|Ez a név azt jelenti, ahol az exportált adatbázist erőforrások mentéséhez. Többek között a szerelvények, további fájlok és mintaadatokat. Egy ilyen nevű mappa a helyi adatok gyökérmappájában jön létre.|
|Projekt könyvtárában|Ezt az elérési utat határozza meg, ahol az exportált U-SQL parancsfájl menteni szeretné. Összes adatbázis Objektumdefiníció ezen a helyen vannak mentve.|
|Csak a sémát|Ha ezt a lehetőséget választja, exportálva csak adatbázis-definíciók és erőforrások (például a szerelvények és további fájlok).|
|Séma- és adatok|Ezt a beállítást, ha az adatbázis-definíciók, az erőforrások és az adatok exportálva. A legfelső N sort táblák exportálása.|
|Automatikusan importálja a helyi adatbázis|Ezt a beállítást, ha az exportált adatbázist a rendszer automatikusan importálja a helyi adatbázis exportálásakor befejeződött.|

![Adatbázis exportálása varázslóban - exportálási objektumlistához és egyéb beállításokra](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>4. lépés: Ellenőrizze az exportált adatok

Amikor befejeződött az exportálási, megtekintheti az exportált eredmények a napló ablakban, a varázslóban. A következő példa bemutatja, hogyan exportált U-SQL parancsfájl és az adatbázis erőforrások keresésére, beleértve a szerelvények, további fájlok és mintaadatokat:

![Adatbázis exportálása varázslóban - eredmények exportálása](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importálja az exportált adatbázist egy helyi fiók

A legalkalmasabb importálja az exportált adatbázist módja válassza ki a **importálása a helyi adatbázis automatikusan** jelölőnégyzetet a 3. lépésben a exportálása során. Ha nem ezt a jelölőnégyzetet, először az exportált U-SQL-parancsfájlt a Exportálás naplóban található. Ezután futtassa helyileg a U-SQL parancsfájl importálja az adatbázist a helyi fiók.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importálja az exportált adatbázist Data Lake Analytics-fiók

Az adatbázis importálására különböző Data Lake Analytics-fiókhoz:

1. Töltse fel az exportált erőforrások, például szerelvényeket, további fájlok és mintaadatokat, a Data Lake Analytics-fiók, amely az importálni kívánt alapértelmezett Azure Data Lake Store-fiókba. Az exportált resource mappa a helyi adatok gyökere alatt található. Töltse fel a teljes mappát az alapértelmezett Data Lake Store-fiók gyökérkönyvtárában.
2. Ha befejeződött a feltöltés, küldje el az exportált U-SQL parancsfájl a Data Lake Analytics-fiókhoz, amely importálja az adatbázist.

## <a name="known-limitations"></a>Ismert korlátozásai

Jelenleg Ha a **séma- és** lehetőséget a 3. lépésben, az eszköz fut egy U-SQL-feladatot, amely a táblákban tárolt adatok exportálása. Ezért az adatok exportálási folyamata lassú lehet, és előfordulhat, hogy fel Önnek a költségeket. 

## <a name="next-steps"></a>Következő lépések

* [További információk a U-SQL-adatbázisok](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [U-SQL feladatok tesztelése és hibakeresése helyi futtatással és az Azure Data Lake U-SQL SDK használatával](data-lake-analytics-data-lake-tools-local-run.md)


