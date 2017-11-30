---
title: "U-SQL-adatbázisok Azure Data Lake Tools for Visual Studio használatával exportálása |} Microsoft Docs"
description: "Útmutató a U-SQL-adatbázis exportálása és importálása egy időben helyi fiók az Azure Data Lake Tools for Visual Studio használatával."
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
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>U-SQL-adatbázis exportálása

Ebből a dokumentumból megtudhatja, hogyan használandó [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) U-SQL adatbázis-exportálási egy U-SQL parancsfájl és a letöltött erőforrásokhoz. Ugyanazt a folyamatot is támogatja a helyi fiók az exportált adatbázist importálását.

Az ügyfelek általában több környezet fejlesztési, tesztelési és éles karbantartása. Ezekben a környezetekben helyi fiók a helyi számítógépen fejlesztő, mind az Azure Data Lake Analytics-fiókot az Azure üzemelnek. Fejlesztéséhez és a U-SQL lekérdezések fejlesztési és tesztkörnyezetek hangolása esetén gyakori, hogy a fejlesztők létre kell hoznia a termelési adatbázis tartalmát. **Adatbázis exportálása varázsló** segít egyre gyorsabban jelennek meg ezt a folyamatot. A varázsló segítségével a fejlesztők számára a meglévő adatbázis-környezet és a mintaadatok más Azure Data Lake Analytics-fiókokba tud klónozni.

## <a name="export-steps"></a>Exportálási lépések

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>1. lépés: Kattintson a jobb gombbal a Server Explorer adatbázis, és kattintson a "Exportálás..."

Az összes Azure Data Lake Analytics-fiókok Ön jogosult a Server Explorer találhatók. Bontsa ki azt az adatbázist kívánja exportálni, és kattintson a jobb gombbal az adatbázis kiválasztása tartalmaz **exportálása...** . Nem található a helyi menüt, ha szeretné [az eszköz frissítése a legfrissebb verzióra](http://aka.ms/adltoolsvs).

![A Data Lake Analytics eszközök exportálási adatbázis](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>2. lépés: Az exportálni kívánt objektumok konfigurálása

Néha egy adatbázis mérete nagy, de csak egy kis részét kell, majd beállíthatja, hogy az objektumok exportálása varázslóban az exportálni kívánt részhalmazát. Vegye figyelembe, hogy befejeződött-e az exportálási művelet a U-SQL-feladat futtatásával, és ezért az Azure-fiók exportálása költségekkel jár.

![Data Lake Analytics eszközök exportálási Database varázsló](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>3. lépés: Ellenőrizze az objektumok listáját és a további konfigurációk

Ebben a lépésben kettős ellenőrizheti a kijelölt objektumokat a párbeszédpanel tetején. Ha nincsenek hibák, lépjen vissza, és exportálja újra a kívánt objektumok konfigurálása előző is kattinthat.

Más konfigurációk kapcsolatos exportálási cél is el lehet végezni, ezek a konfigurációk leírását vannak a táblázat alatti listában felsorolt:

|Konfiguráció|Leírás|
|-------------|-----------|
|Cél neve|Ez a név azt jelenti, ahol az exportált adatbázist erőforrások, például a szerelvények, további fájlok és mintaadatokat mentéséhez. Egy ilyen nevű mappa alatt a helyi adatok legfelső szintű mappa jön létre.|
|Projekt könyvtárában|Ezt az elérési utat határozza meg, ahol az exportált U-SQL parancsfájlt, amely tartalmazza az összes adatbázis Objektumdefiníció menteni szeretné.|
|Csak a sémát|Ezzel a beállítással csak adatbázis-definíciók és erőforrások (például a szerelvények és további fájlok) eredményez exportálás alatt álló.|
|Séma- és adatok|Ezzel a beállítással, adatbázis-definíciók, erőforrások és exportálandó adatokat eredményez. A legfelső N sort táblák exportálása.|
|Automatikusan importálja a helyi adatbázis|Ez a konfiguráció azt jelenti, hogy az exportált adatbázist ellenőrzése importálja a helyi adatbázis automatikusan exportálása befejeződött.|

![Data Lake Analytics eszközök exportálási adatbázis varázsló konfiguráció](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>4. lépés: Ellenőrizze az exportált adatok

Után minden ezen beállítások és exportálása folyamatban van a varázsló az exportált eredmények a napló ablakból is található. Keresztül ezen bAlacsony képernyőfelvételen vörös téglalap jelölte meg a naplóban találja az exportált U-SQL parancsfájl és adatbázis-erőforrásokat szerelvényeket, további fájlok és mintaadatokat helyét.

![Data Lake Analytics eszközök exportálási adatbázis varázsló befejeződött](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>Hogyan importálja az exportált adatbázist helyi fiók

A legtöbb kényelmes módja a importálása ellenőrzi **importálás automatikusan helyi adatbázis** exportáló a folyamat állapotát, a 3. lépés során. Ha elfelejtette ehhez, az exportált U-SQL parancsfájl keresztül az exportáló napló található, és futtassa a U-SQL helyi importálja az adatbázist a helyi fiók.

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>Az exportált adatbázist importálása az Azure Data Lake Analytics-fiókhoz

Egyéb Azure Data Lake Analytics-fiók importálja az adatbázist, két lépésben kell:

1. Töltse fel az exportált erőforrások, például szerelvények, további fájlok és az alapértelmezett Azure Data Lake Store-fiókba az Azure Data Lake Analytics-fiók az importálni kívánt mintaadatok. Az exportált resource mappát a helyi adatok gyökere alatt, és töltse fel a teljes mappát az alapértelmezett store-fiók gyökérkönyvtárában.
2. Küldje el az exportált U-SQL parancsfájl adatbázis feltöltése befejezése után importálja az Azure Data Lake Analytics-fiókhoz.

## <a name="known-limitation"></a>Ismert korlátozása

Jelenleg Ha a kiválasztott **séma- és** a varázslóban az eszköz futtatásához egy U-SQL-feladatot, amely a táblákban tárolt adatok exportálása. Ezért az adatok exportálása folyamat is lassú és költség fel Önnek. 

## <a name="next-steps"></a>Következő lépések

* [U-SQL-adatbázis ismertetése](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [Hogyan tesztelése és hibakeresése a U-SQL feladatok helyi futtatáskor és az Azure Data Lake U-SQL SDK használatával](data-lake-analytics-data-lake-tools-local-run.md)


