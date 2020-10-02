---
title: Dedupe sorok és null értékek keresése adatfolyam-kódrészletek használatával
description: Megtudhatja, hogyan lehet egyszerűen dedupe sorokat használni, és nullát keresni a kódrészletek használatával az adatfolyamatokban
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 683d7ffe5549b86a587cd2dc3c9a86a36aee1bba
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637247"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>Dedupe sorok és null értékek keresése adatfolyam-kódrészletek használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha kódrészleteket használ a leképezési adatforgalomban, egyszerűen végezhet olyan gyakori feladatokat, mint például az adatok deduplikálása és a null szűrés. Ez a útmutató ismerteti, hogyan adhatja hozzá ezeket a függvényeket a folyamatokhoz az adatfolyam-parancsfájlok használatával.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Új folyamat létrehozásához válassza az **+ új folyamat** elemet.

2. Adjon hozzá egy adatfolyam-tevékenységet.

3. Forrás-átalakítás hozzáadása és csatlakoztatása egy adatkészlethez

    ![2. forrás](media/data-flow/snippet-adf-2.png)

4. A dedupe és NULL ellenőrzési kódrészlet olyan általános mintákat használ, amelyek kihasználják az adatfolyam-sémák eltolódását, hogy az adatkészletből származó összes sémával működjenek, vagy olyan adatkészletekkel, amelyek nem rendelkeznek előre definiált sémával.

5. [Lépjen az adatfolyam-parancsfájl dokumentációs oldalára, és másolja a kódrészletet a különböző sorokhoz.](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. Az adatfolyam-tervező felhasználói felületén kattintson a jobb felső sarokban található script (szkript) gombra, és nyissa meg a parancsfájl-szerkesztőt az adatáramlási gráf mögött.

    ![3. forrás](media/data-flow/snippet-adf-3.png)

7. Miután a parancsfájlban definiálta a definícióját ```source1``` , nyomja meg az ENTER billentyűt, majd illessze be a kódrészletbe.

8. Ehhez a beillesztett kódrészletet a gráfban létrehozott korábbi forrás-átalakításhoz kell kapcsolni, a beillesztett kód elé írja be a "source1" kifejezést.

9. Azt is megteheti, hogy az új transzformációt a tervezőben a gráf új transzformációs csomópontjának bejövő stream elemére kattintva kapcsolja össze.

    ![4. forrás](media/data-flow/snippet-adf-4.png)

10. Most, hogy az adatfolyam el fogja távolítani az ismétlődő sorokat a forrásból az összes sor által az összes oszlop értékeit használó összesített átalakítással.
    
11. Ezután hozzáadunk egy kódrészletet az adatai egy olyan adatfolyamba való felosztásához, amely NULLával rendelkező sorokat és egy olyan streamet tartalmaz, amely nem rendelkezik NULLával.

12. [Térjen vissza a kódrészlet-könyvtárhoz, és ezúttal másolja a NULL ellenőrzések kódját.](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. Az adatfolyam-tervezőben ismét kattintson a parancsfájl elemre, és illessze be az új átalakítási kódot az aljára, és csatlakoztassa az előző átalakításhoz úgy, hogy beírja az átalakítás nevét a beillesztett kódrészlet elé.

14. Az adatfolyam-gráfnak ekkor a következőhöz hasonlóan kell kinéznie:

    ![1. forrás](media/data-flow/snippet-adf-1.png)

  Az általános deduping és NULL ellenőrzésekkel rendelkező munkafolyamatok mostantól a meglévő kódrészletek az adatfolyam-parancsfájl könyvtárából való hozzáadásával és a meglévő tervbe való felvételével rendelkeznek.

## <a name="next-steps"></a>További lépések

* Hozza létre a többi adatáramlási logikát az adatforgalom- [átalakítások](concepts-data-flow-overview.md)leképezése használatával.
