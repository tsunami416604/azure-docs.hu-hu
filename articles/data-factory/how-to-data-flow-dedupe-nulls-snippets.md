---
title: Sorok kihagyása és a null értékek keresése adatfolyam-kódrészletek használatával
description: Megtudhatja, hogyan lehet egyszerűen dedupe sorokat használni, és nullát keresni a kódrészletek használatával az adatfolyamatokban
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 1c630cdd66fa4f8e609524feb9c3f0bcad9711a0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458166"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>Sorok kihagyása és a null értékek keresése adatfolyam-kódrészletek használatával

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha kódrészleteket használ a leképezési folyamatokban, egyszerűen végrehajthat olyan gyakori feladatokat, mint például az adatok deduplikálása és a null szűrés. Ez a cikk azt ismerteti, hogyan lehet egyszerűen hozzáadni ezeket a függvényeket a folyamatokhoz az adatfolyam-parancsfájlok használatával.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Válassza a **New pipeline** (Új folyamat) lehetőséget.

1. Adjon hozzá egy adatfolyam-tevékenységet.

1. Válassza ki a **forrás beállításai** lapot, adjon hozzá egy forrás-átalakítást, majd kapcsolja össze az adatkészletek egyikével.

    ![Képernyőkép a "forrásoldali beállítások" panelről, amely a forrás típusát adja meg.](media/data-flow/snippet-adf-2.png)

    A dedupe és null ellenőrzési kódrészlet olyan általános mintákat használ, amelyek kihasználják az adatfolyam-séma eltolódását. A kódrészletek az adatkészlet bármely sémájával vagy olyan adatkészletekkel működnek, amelyek nem rendelkeznek előre definiált sémával.

1. Az [adatáramlási parancsfájl (DFS)](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)"az összes oszlop használata" szakasza alapján másolja a DistinctRows kódrészletét.

1. Az adatfolyam-tervező felhasználói felületén kattintson a jobb felső sarokban található **parancsfájl** gombra, és nyissa meg a parancsfájl-szerkesztőt az adatáramlási gráf mögött.

    ![Képernyőfelvétel a forrás részleteiről.](media/data-flow/snippet-adf-3.png)

1. A parancsfájlban a definíciója után `source1` nyomja meg az ENTER billentyűt, majd illessze be a kódrészletbe.

1. A következő lehetőségek közül választhat:

   * Kapcsolja össze ezt a beillesztett kódrészletet a gráfban korábban létrehozott forrás-átalakításhoz, és írja be a **source1** szöveget a beillesztett kód elé.

   * Azt is megteheti, hogy az új transzformációt a tervezőben a gráf új transzformációs csomópontjának bejövő stream elemére kattintva kapcsolja össze.

     ![Képernyőkép a "feltételes felosztás beállításai" panelről.](media/data-flow/snippet-adf-4.png)

   Most, hogy az adatfolyam el fogja távolítani az ismétlődő sorokat a forrásból az összesített átalakítás használatával, amely az összes sor szerint csoportosítja az összes oszlop értékét.
    
1. Adjon hozzá egy kódrészletet az adatai egyetlen streambe való felosztásához, amely NULL értékkel rendelkező sorokat és egy másik, Null érték nélküli streamet tartalmaz. Ehhez tegye a következőket:

   a. Térjen vissza a [kódrészlet-könyvtárhoz](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns), és másolja a null értékű ellenőrzésekhez tartozó kódot.

   b. Az adatfolyam-tervezőben válassza újra a **parancsfájlt** , majd illessze be az új átalakítási kódot az aljára. Ez a művelet összekapcsolja a szkriptet az előző átalakításhoz, ha az átalakítás nevét a beillesztett kódrészlet elé helyezi.

   Az adatfolyam-gráfnak ekkor a következőhöz hasonlóan kell kinéznie:

    ![Képernyőfelvétel az adatfolyam gráfról.](media/data-flow/snippet-adf-1.png)

  Ezzel létrehozott egy működő adatfolyamatot általános deduping és null ellenőrzésekkel azáltal, hogy a meglévő kódrészleteket az adatfolyam parancsfájl-könyvtárából helyezi, és hozzáadja őket a meglévő tervhez.

## <a name="next-steps"></a>További lépések

* Hozza létre a többi adatáramlási logikát az adatforgalom- [átalakítások](concepts-data-flow-overview.md)leképezése használatával.
