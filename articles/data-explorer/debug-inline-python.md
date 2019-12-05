---
title: Hibakeresés Kusto lekérdezési nyelv beágyazott Python használatával VS Code-Azure Adatkezelő
description: Ismerje meg, hogyan lehet hibakeresést végezni a Kusto lekérdezési nyelv (KQL) inline Python segítségével a VS Code használatával.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: f0bf81ce6392e1650d9986d81a55eac9c416c24d
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822902"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Hibakeresés Kusto lekérdezési nyelv beágyazott Python használatával VS Code

Az Azure Adatkezelő támogatja a [Python () beépülő modul](/azure/kusto/query/pythonplugin)használatával a Kusto-lekérdezési nyelven beágyazott Python-kód futtatását. A beépülő modul futtatókörnyezetét homokozóban, elkülönített és biztonságos Python-környezetben üzemeltetjük. A Python () beépülő modul képességei kiterjesztik a Kusto lekérdezési nyelvét natív funkciókkal az OSS Python-csomagok hatalmas archívumával. Ez a bővítmény lehetővé teszi speciális algoritmusok, például gépi tanulás, mesterséges intelligencia, statisztikai és idősorozatok futtatását a lekérdezés részeként.

A Kusto-lekérdezés nyelvi eszközei nem alkalmasak a Python-algoritmusok fejlesztésére és hibakeresésére. Ezért fejlesztheti az algoritmust a kedvenc Python-integrált fejlesztési környezetében, például a Jupyter, a Notebookshoz, a VS vagy a VS Code használatával. Ha az algoritmus elkészült, másolja és illessze be a KQL. A munkafolyamat fejlesztése és egyszerűsítése érdekében az Azure Adatkezelő támogatja a Kusto Explorer vagy webes felhasználói felületi ügyfelek, valamint a VS Code integrációját a KQL beágyazott Python-kódok készítéséhez és hibakereséséhez. 

> [!NOTE]
> Ezt a munkafolyamatot csak viszonylag kis bemeneti táblák hibakeresésére lehet használni (akár néhány MB-ig). Ezért előfordulhat, hogy korlátoznia kell a hibakereséshez szükséges adatokat.  Ha nagyméretű táblát kell feldolgoznia, korlátozza a hibakeresést `| take`, `| sample`vagy `where rand() < 0.x`használatával.

## <a name="prerequisites"></a>Előfeltételek

1. Telepítse a Python [anaconda-disztribúciót](https://www.anaconda.com/distribution/#download-section). A **Speciális beállítások**területen válassza az **anaconda hozzáadása a saját PATH környezeti változóhoz**lehetőséget.
2. A [Visual Studio Code](https://code.visualstudio.com/Download) telepítése
3. Telepítse [a Python-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)-hoz.

## <a name="run-your-query-in-your-client-application"></a>A lekérdezés futtatása az ügyfélalkalmazás

1. Az ügyfélalkalmazás egy, a beágyazott Pythont tartalmazó lekérdezést `set query_python_debug;`
1. Futtassa a lekérdezést.
    * Kusto Explorer: a VS Code automatikusan elindul a *debug_python. a.* a szkripttel.
    * Kusto webes felhasználói felület: 
        1. Töltse le és mentse a *debug_python.* , a *DF. txt*és a *kargs. txt fájlt*. Az ablakban válassza az **Engedélyezés**lehetőséget. Fájlok **mentése** a kiválasztott könyvtárba. 

            ![A webes felhasználói felület letölti a beágyazott Python-fájlokat](media/debug-inline-python/webui-inline-python.png)

        1. Kattintson a jobb gombbal a *debug_python. a.* , majd a vs Code elemre. 
        A *debug_python.* másolási parancsfájl tartalmazza a beágyazott Python-kódot a KQL-lekérdezésből, amelyet a sablon kód előre rögzített, hogy a *DF. txt* fájlból és a *kargs. txt*fájlból származó paraméterek szótárában inicializálja a bemeneti dataframe.    
            
1. A VS Code-ban indítsa el a VS Code debuggert: **hibakeresés** > **Indítás hibakeresése (F5)** , válassza a **Python** -konfiguráció lehetőséget. A hibakereső elindítja és automatikusan töréspontot küld a beágyazott kód hibakereséséhez.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Hogyan működik a Deline Python-hibakeresés a VS Code-ban?

1. A lekérdezés elemzése és végrehajtása a kiszolgálón, amíg el nem éri a szükséges `| evaluate python()` záradékot.
1. A rendszer meghívja a Python Sandboxot, de nem futtatja a kódot, hanem a bemeneti táblázatot, a paraméterek szótárát és a kódot, majd visszaküldi őket az ügyfélnek.
1. Ez a három objektum a következő három fájlban van mentve: *DF. txt*, *kargs. txt*és *debug_python.* a kiválasztott könyvtár (webes felület) vagy a (z)% Temp% Directory (Kusto Explorer) ügyfél.
1. A VS Code elindult, előre be van töltve a *debug_python.* másolási fájllal, amely tartalmaz egy előtag-kódot a DF és a kargs a megfelelő fájlokból való inicializálásához, majd a KQL-lekérdezésbe ágyazott Python-szkriptet.

## <a name="query-example"></a>Példa lekérdezésre

1. Futtassa az alábbi KQL-lekérdezést az ügyfélalkalmazás számára:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Tekintse meg az eredményül kapott táblázatot:

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Futtassa ugyanazt a KQL-lekérdezést az ügyfélalkalmazás `set query_python_debug;`használatával:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. A VS Code elindítása:

    ![a VS Code elindítása](media/debug-inline-python/launch-vs-code.png)

1. A VS Code debugs és a "result" dataframe a hibakeresési konzolon:

    ![VS Code-hibakeresés](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Előfordulhat, hogy a Python-homokozó rendszerképe és a helyi telepítése között különbségek vannak. A [beépülő modul lekérdezésével keresse meg a megadott csomagok sandbox-képét](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
