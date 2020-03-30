---
title: 'Adatok importálása: modul hivatkozási'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Adatok importálása modult az Azure Machine Learningben az adatok gépi tanulási folyamatba való betöltéséhez a meglévő felhőalapú adatszolgáltatásokból.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d124fdc15bd34743b237985a66cc35625f5d9a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456199"
---
# <a name="import-data-module"></a>Adatok importálása modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal adatokat tölthet be egy gépi tanulási folyamatba a meglévő felhőalapú adatszolgáltatásokból. 

> [!Note]
> A modul által biztosított összes funkciót **az adattár** és az **adatkészletek** végezhetik el a worksapce céloldalon. Azt javasoljuk, hogy **használja az adattároló** és **az adatkészlet,** amely további funkciókat, például az adatfigyelés. További információ: [Az adatok elérése](../how-to-access-data.md) és [az adatkészletek regisztrálása](../how-to-create-register-datasets.md) című cikkben.
> Miután regisztrált egy adatkészletet, megtalálhatja azt a tervezői felület **Adatkészletek** -> **csoportjában.** Ez a modul a Studio(classic) felhasználók számára van fenntartva egy ismerős élmény érdekében. 
>

**Az Adatok importálása** modul támogatása a következő forrásokból olvassa be az adatokat:

- URL HTTP-n keresztül
- Az Azure felhőalapú tárhelyei az [**adatáruházakon**](../how-to-access-data.md)keresztül)
    - Azure Blob-tároló
    - Azure-fájlmegosztás
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure PostgreSQL    

A felhőalapú tárhely használata előtt először regisztrálnia kell egy adattáradataz Azure Machine Learning-munkaterületen. További információt az Adatok elérése című témakörben [talál.](../how-to-access-data.md) 

Miután definiálta a kívánt adatokat, és csatlakozott a forráshoz, az **[Adatok importálása](./import-data.md)** a benne lévő értékek alapján következtet az egyes oszlopok adattípusából, és betölti az adatokat a tervezői folyamatba. **Az Adatok importálása** egy adatkészlet, amely bármely tervezői folyamattal használható.

Ha a forrásadatok megváltoznak, az [Adatok importálása](./import-data.md)című fájl újbóli futtatásával frissítheti az adatkészletet, és új adatokat adhat hozzá.

## <a name="how-to-configure-import-data"></a>Az adatok importálásának beállítása

1. Adja hozzá az **Adatok importálása** modult a folyamathoz. Ezt a modult a tervező **Adatbevitel és kimenet** kategóriában találja.

1. Válassza ki a modult a jobb oldali ablaktábla megnyitásához.

1. Válassza **az Adatforrás**lehetőséget, és válassza ki az adatforrás típusát. Ez lehet HTTP vagy adattár.

    Ha az adattár, kiválaszthatja a meglévő adattárak, amelyek már regisztrált az Azure Machine Learning-munkaterületre, vagy hozzon létre egy új adattárban. Ezután adja meg az adattárban importálandó adatok elérési útját. Könnyedén böngészhet az útvonalon, kattintson **a Tallózás útvonal** ![importálási-adatelérési út](media/module/import-data-path.png)

1. Jelölje ki az előnézeti sémát a felvenni kívánt oszlopok szűréséhez. Speciális beállításokat is megadhat, például határolóbeállítást az elemzési beállításokban.

    ![import-data-preview](media/module/import-data.png)

1. A jelölőnégyzet, **Újragenerálása kimenet**, dönti el, hogy végre a modult, hogy újragenerálja a kimenetet a működési idő. 

    Alapértelmezés szerint nincs bejelölve, ami azt jelenti, hogy ha a modult korábban ugyanazokat a paramétereket hajtották végre, a rendszer újra felhasználja az utolsó futtatás kimenetét a futási idő csökkentése érdekében. 

    Ha be van jelölve, a rendszer újra végrehajtja a modult a kimenet újragenerálásához. Így válassza ezt a lehetőséget, ha az alapul szolgáló adatok a tárolóban frissül, segíthet a legfrissebb adatok betöltésében.


1. Küldje el a folyamatot.

    Amikor az Adatok importálása betölti az adatokat a tervezőbe, az egyes oszlopok adattípusát a benne lévő értékek alapján következteti be, akár numerikus, akár kategorikus.

    Ha van fejléc, a fejléc a kimeneti adatkészlet oszlopainak elnevezésére szolgál.

    Ha az adatokban nincsenek meglévő oszlopfejlécek, a col1 (col2) formátumban új oszlopnevek jönnek létre,... , coln*.

## <a name="results"></a>Results (Eredmények)

Az importálás befejezéseután kattintson a kimeneti adatkészletre, és válassza a **Visualize** lehetőséget, hogy lássa, sikeresen importálta-e az adatokat.

Ha az adatokat újrafelhasználásra szeretné menteni, ahelyett, hogy a folyamat minden egyes futtatásakor új adathalmazt importálna, válassza az **Adatkészlet regisztrálása** ikont a modul jobb oldali paneljének **Kimenetek** lapján. Válassza ki az adatkészlet nevét. A mentett adatkészlet megőrzi az adatokat a mentés időpontjában, az adatkészlet nem frissül a folyamat újrafuttatásakor, még akkor sem, ha a folyamat adatkészlete megváltozik. Ez az adatok pillanatképeinek készítéséhez lehet hasznos.

Az adatok importálása után további előkészületekre lehet szükség a modellezéshez és elemzéshez:

- [A Metaadatok szerkesztése](./edit-metadata.md) segítségével módosíthatja az oszlopneveket, más adattípusként kezelheti az oszlopokat, illetve jelezheti, hogy egyes oszlopok címkék vagy szolgáltatások.

- Az [Adatkészlet Oszlopainak kijelölése](./select-columns-in-dataset.md) segítségével kijelölhet egy oszlopot, amelyet át alakítana vagy használhat modellezésben. Az átalakított vagy eltávolított oszlopok könnyen újra csatlakoztathatók az eredeti adatkészlethez az [Oszlopok hozzáadása](./add-columns.md) modul segítségével.  

- [A Partition és a Sample](./partition-and-sample.md) segítségével feloszthatja az adatkészletet, mintavételezést végezhet, vagy beszerezheti a felső n sorokat.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
