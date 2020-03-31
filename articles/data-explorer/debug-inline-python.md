---
title: Debug Kusto lekérdezési nyelv inline Python segítségével VS Code - Azure Data Explorer
description: Ismerje meg, hogyan debug Kusto lekérdezési nyelv (KQL) inline Python vs kód használatával.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444473"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Debug Kusto lekérdezési nyelv inline Python segítségével VS-kód

Az Azure Data Explorer támogatja a Kusto lekérdezési nyelvébe ágyazott Python-kód futtatását a [python() beépülő modul](/azure/kusto/query/pythonplugin)használatával. A plugin runtime üzemelteti a sandbox, egy elszigetelt és biztonságos Python környezetben. A python() plugin képesség kiterjeszti a Kusto lekérdezési nyelv natív funkcióit az OSS Python-csomagok hatalmas archívumával. Ez a bővítmény lehetővé teszi, hogy a lekérdezés részeként speciális algoritmusokat, például gépi tanulást, mesterséges intelligenciát, statisztikai és idősorozatokat futtasson.

A Kusto lekérdezési nyelvi eszközei nem kényelmesek a Python-algoritmusok fejlesztéséhez és hibakereséséhez. Ezért fejlessze az algoritmust a kedvenc Python-integrált fejlesztői környezetben, például a Jupyter, a PyCharm, a VS vagy a VS Code. Ha az algoritmus befejeződött, másolja és illessze be a KQL-be. A munkafolyamat javítása és egyszerűsítése érdekében az Azure Data Explorer támogatja a Kusto Explorer vagy a Web UI ügyfelek és a VS Code közötti integrációt a KQL inline Python-kód létrehozásához és hibakereséséhez. 

> [!NOTE]
> Ez a munkafolyamat csak viszonylag kis bemeneti táblák (legfeljebb néhány MB) hibakeresésére használható. Ezért előfordulhat, hogy korlátoznia kell a hibakeresés bemenetét.  Ha nagy méretű táblát kell feldolgoznia, korlátozza `| take` `| sample`a `where rand() < 0.x`hibakereséshez a használatával, vagy .

## <a name="prerequisites"></a>Előfeltételek

1. Telepítse a Python [Anaconda Distribution](https://www.anaconda.com/distribution/#download-section)alkalmazást. A **Speciális beállítások csoportban**válassza **az Anaconda hozzáadása a PATH környezeti változóhoz**lehetőséget.
2. Visual [Studio-kód](https://code.visualstudio.com/Download) telepítése
3. Telepítse [a Python-bővítményt a Visual Studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>A lekérdezés futtatása az ügyfélalkalmazásban

1. Az ügyfélalkalmazásban előtagként előtagként egy inline Pythont tartalmazó lekérdezést`set query_python_debug;`
1. Futtassa a lekérdezést.
    * Kusto Explorer: VS Kód automatikusan elindul a *debug_python.py* script.
    * Kusto Web felhasználói felület: 
        1. Töltse le és mentse *a debug_python.py*, *df.txt*és *kargs.txt*fájlt. Az ablakban válassza az **Engedélyezés**lehetőséget. **Fájlok mentése** a kijelölt könyvtárba. 

            ![A webes felhasználói felület letölti a beépített python-fájlokat](media/debug-inline-python/webui-inline-python.png)

        1. Kattintson a jobb gombbal *a debug_python.py-ra,* és nyissa meg a VS-kóddal. 
        A *debug_python.py* parancsfájl tartalmazza a szövegközi Python-kódot a KQL lekérdezésből, amelyet a sablonkód előtaggal rögzít a *df.txt* bemeneti adatkeret inicializálásához, valamint a *kargs.txt*paraméterszótárának inicializálásához.    
            
1. A VS-kódban indítsa el a VS-kód hibakereső: **Debug** > **Start Debugging (F5)** lehetőséget **a Python-konfiguráció.** A hibakereső elindul, és automatikusan töréspont hibakeresés a beépített kódot.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Hogyan működik a soros Python hibakeresés a VS Code-ban?

1. A lekérdezés elemzésre kerül, és a `| evaluate python()` kiszolgálón a szükséges záradék eléréséig kerül végrehajtásra.
1. A Python sandbox meghívása, de ahelyett, hogy a kód futtatása, szerializálja a bemeneti tábla, a paraméterek szótára, és a kódot, és elküldi azokat az ügyfélnek.
1. Ez a három objektum három fájlba kerül: *df.txt*, *kargs.txt*és *debug_python.py* a kijelölt könyvtárba (web felhasználói felület) vagy az ügyfél %TEMP% könyvtárába (Kusto Explorer).
1. Vs kód indul, előre betöltve a *debug_python.py* fájlt, amely tartalmaz egy előtag kódot inicializálására df és kargs a megfelelő fájlokat, majd a Python script ágyazott a KQL lekérdezést.

## <a name="query-example"></a>Példa lekérdezésre

1. Futtassa a következő KQL-lekérdezést az ügyfélalkalmazásban:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Lásd az eredményül kapott táblázatot:

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Futtassa ugyanazt a KQL-lekérdezést az ügyfélalkalmazásban a következő használatával: `set query_python_debug;`

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. Vs Kód van indított:

    ![vs kód elindítása](media/debug-inline-python/launch-vs-code.png)

1. VS Code debugs és nyomatok "eredmény" dataframe a hibakeresési konzol:

    ![VS-kód hibakeresés](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Előfordulhat, hogy különbségek vannak a Python sandbox-lemezkép és a helyi telepítés között. [Ellenőrizze a sandbox képet az egyes csomagok lekérdezésével a plugin](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
