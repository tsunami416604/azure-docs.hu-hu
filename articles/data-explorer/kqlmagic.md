---
title: Jupyter Notebook és Kqlmagic használata az adatok elemzése
description: Ez a témakör bemutatja, hogyan elemezhet Jupyter Notebook és KQLmagic használata az adatok
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 7152b1d09a11d5860d52b5f73ae601422bd0f722
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654491"
---
# <a name="analyze-data-using-jupyter-notebook-and-kqlmagic"></a>Jupyter Notebook és Kqlmagic használata az adatok elemzése
Jupyter Notebook egy nyílt forráskódú webes alkalmazás, amely lehetővé teszi, hogy hozzon létre és megoszthatja a dokumentumokat, amely tartalmazza az élő kód, a egyenletek, a Vizualizációk és a leíró szöveg. Használati adatok tisztítása és átalakítása numerikus szimuláció, statisztikai modellezési, adatvizualizáció és machine learning tartalmazza.
[Jupyter Notebook](https://jupyter.org/) magic függvények támogatásával, további parancsok a kernel képességeit kibővítő támogatja. Kqlmagic egy függvényt, amely bővíti a Python-kernel Jupyter Notebookot a natív módon Kusto-lekérdezés nyelvi lekérdezések futtatásához. A Python és Kusto lekérdezési nyelv lekérdezése és gazdag és integrált Plot.ly könyvtár használatával adatok vizualizációja révén könnyedén egyesítheti `render` parancsokat. A futó lekérdezések adatforrások támogatottak. Ezeknek az adatforrásoknak az Azure az adatkezelőt, egy gyors és hatékonyan méretezhető exploration szolgáltatás napló és a telemetriai adatokat, valamint a Log Analytics és az Application Insights tartalmazza.

## <a name="prerequisites"></a>Előfeltételek
- Munkahelyi e-mail-fiók, amely tagja az Azure Active Directory (AAD).
- A Jupyter Notebook telepítve van a helyi gépén vagy Azure notebookok használata, és klónozza a minta [Azure Notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)


## <a name="install-kqlmagic-library"></a>Kqlmagic erőforrástár telepítése

1. Kqlmagic telepítése:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```

2. Betöltés Kqlmagic:

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Csatlakozás az Azure Data Explorer súgója fürthöz

A következő paranccsal csatlakozhat a *minták* adatbázisban lévő üzemeltetett a *súgó* fürt. A Microsoft AAD-felhasználók számára, cserélje le a bérlő neve `Microsoft.com` az AAD-bérlőben.


```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Lekérdezése és megjelenítése

Lekérdezési adatok használatával a [operátor render](/azure/kusto/query/renderoperator) és az adatok megjelenítése a ploy.ly kódtár használatával. Ehhez a lekérdezéshez és vizualizációs választékát kínálja, amely natív KQL integrált megoldást. Kqlmagic támogatja kivételével a legtöbb diagramok `timepivot`, `pivotchart`, és `ladderchart`. Renderelési támogatott, kivéve az összes attribútum `kind`, `ysplit`, és `accumulate`. 

### <a name="query-and-render-piechart"></a>Lekérdezés és a renderelési piechart

```python
%%kql 
StormEvents 
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Lekérdezés és a renderelési idődiagramját

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Ezek a diagramok használata interaktív. Jelöljön ki egy időtartományt, egy adott időpont nagyíthatja.

### <a name="customize-the-chart-colors"></a>A diagram színeinek testreszabásához
Ha nem szeretné az alapértelmezett színpalettát, testre szabhatja a diagramok paletta beállítások használatával. A rendelkezésre álló palettára itt található: [A Kqlmagic lekérdezési diagram eredményhez színpaletta kiválasztása](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Színpaletták listáját:

    ```python
    %kql --palettes -popup_window
    ```

1. Válassza ki a `cool` paletta szín és jelennek meg újra a lekérdezést:

    ```python
    %%kql -palette_name "cool"
    StormEvents 
    | summarize statecount=count() by State
    | sort by statecount 
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parametrize-a-query-with-python"></a>A lekérdezés Parametrize Python használatával

Lehetővé teszi a Kqlmagic egyszerű adatcsere Kusto lekérdezési nyelvet és a Python között. További tudnivalók: [A Kqlmagic lekérdezés Parametrize Python használatával](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 

### <a name="use-a-python-variable-in-your-kql-query"></a>Használhat egy Python-változót a KQL lekérdezés

Egy Python-változó értékét használhatja a lekérdezésben az adatok szűrése:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Lekérdezési eredmények átalakítása Pandas DataFrame 

Pandas DataFrame KQL lekérdezések eredményeit is elérheti. Az utolsó végrehajtott lekérdezési eredmények eléréséhez a változó által `_kql_raw_result_` és egyszerűen alakíthatja az eredmények Pandas DataFrame módon:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Példa 

Számos elemzési forgatókönyvek érdemes létrehozni, amely számos lekérdezéseket tartalmaz, és az eredmények hírcsatorna egy lekérdezés által az ezt követő lekérdezéseket újrafelhasználható notebookok. Az alábbi példa Python változót használja `statefilter` az adatok szűréséhez.

1. Az első 10 állapotot, legfeljebb egy lekérdezés futtatásával `DamageProperty`:

    ```python
    %%kql
    StormEvents 
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Bontsa ki a felső állapotot, és állítsa be egy Python-változóba egy lekérdezés futtatásával:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Egy lekérdezés segítségével futtassa a `let` utasítás és a Python-változó:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state) 
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. A Súgó parancs futtatásához: 

    ```python
    %kql --help "help"
    ```

## <a name="next-steps"></a>További lépések
    
A help paranccsal Fedezze fel a következő mintafüzetek, amelyek tartalmazzák a támogatott szolgáltatások:
- [Azure Data Explorer Kqlmagic használatának első lépései](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Az Application Insights Kqlmagic használatának első lépései](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [A Log Analytics Kqlmagic használatának első lépései](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [A Kqlmagic lekérdezés Parametrize Python használatával](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [A Kqlmagic lekérdezési diagram eredményhez színpaletta kiválasztása](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)



