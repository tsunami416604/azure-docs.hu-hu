---
title: Jupyter Notebook használata az Azure-ban tárolt adatelemzéshez Adatkezelő
description: Ebből a témakörből megtudhatja, hogyan elemezheti az Azure Adatkezelőban tárolt adatelemzést egy Jupyter Notebook és a Kqlmagic bővítmény használatával.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: ba21ec6ba82ac4984d3c51fc46f88de2cd56b2b2
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933697"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Jupyter Notebook-és Kqlmagic-bővítmény használata az Azure-ban tárolt adatelemzéshez Adatkezelő

A Jupyter Notebook egy nyílt forráskódú webes alkalmazás, amellyel élő kódokat, egyenleteket, vizualizációkat és narratív szövegeket tartalmazó dokumentumokat hozhat létre és oszthat meg. A használat magában foglalja az adatok tisztítását és átalakítását, a numerikus szimulációt, a statisztikai modellezést, az adatvizualizációt és a gépi tanulást
[Jupyter notebook](https://jupyter.org/) támogatja a Magic functions szolgáltatást, amely további parancsok támogatásával bővíti a kernel képességeit. A KQL Magic egy olyan parancs, amely kibővíti a Python-kernel képességeit Jupyter Notebookban, így natív módon futtathatja a Kusto nyelvi lekérdezéseit. A Python és a Kusto lekérdezési nyelvét könnyedén kombinálhatja a `render` parancsokkal integrált, gazdag Plot.ly-kódtár használatával történő lekérdezéséhez és megjelenítéséhez. A lekérdezések futtatásához szükséges adatforrások támogatottak. Ezek az adatforrások közé tartoznak az Azure Adatkezelő, a gyors és rugalmasan méretezhető adatfelderítési szolgáltatás a napló-és telemetria, valamint Azure Monitor naplók és Application Insights. A KQL Magic a Azure Notebooks, a Jupyter Lab és a Visual Studio Code Jupyter bővítménnyel is működik.

## <a name="prerequisites"></a>Előfeltételek

- A szervezeti e-mail-fiók, amely Azure Active Directory (HRE) tagja.
- Jupyter Notebook telepítve a helyi gépen, vagy használja Azure Notebooks és klónozása a minta [Azure notebookot](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>A KQL Magic Library telepítése

1. A KQL Magic telepítése:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Azure Notebooks használatakor ez a lépés nem szükséges.

1. KQL Magic betöltése:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Módosítsa a kernel verzióját a Python 3,6-ra úgy, hogy a kernel > Change kernel > Python 3,6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Kapcsolódás az Azure Adatkezelő Súgó fürthöz

A következő parancs használatával csatlakozhat a *Súgó* fürtön tárolt mintaadatbázis-adatbázishoz. A nem a Microsofttól származó HRE felhasználók esetében cserélje le `Microsoft.com` a bérlő nevét a HRE-bérlőre.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Lekérdezés és megjelenítés

Az adatlekérdezés a [Render operátor](/azure/kusto/query/renderoperator) használatával és az adatmegjelenítés a Ploy.ly-könyvtár használatával. Ez a lekérdezés és vizualizáció olyan integrált felhasználói élményt nyújt, amely natív KQL használ. A Kqlmagic a legtöbb diagramot `pivotchart`támogatja, `ladderchart`a és a kivételével `timepivot`. A renderelés az összes attribútummal támogatott `kind`, `ysplit`kivéve: `accumulate`, és. 

### <a name="query-and-render-piechart"></a>Piechart lekérdezése és megjelenítése

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Idődiagramját lekérdezése és megjelenítése

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Ezek a diagramok interaktívak. Válasszon ki egy időtartományt, amely egy adott időpontra nagyít.

### <a name="customize-the-chart-colors"></a>A diagram színeinek testreszabása

Ha nem tetszik az alapértelmezett színpaletta, testreszabhatja a diagramokat a paletta beállításainak használatával. A rendelkezésre álló paletta itt található: [A KQL Magic Query diagram eredményének kiválasztása szín paletta](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. A paletták listája:

    ```python
    %kql --palettes -popup_window
    ```

1. Válassza ki `cool` a színpalettát, és jelenítse meg újra a lekérdezést:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parametrizálja egy lekérdezést a Pythonban

A KQL Magic lehetővé teszi a Kusto-lekérdezési nyelv és a Python közötti egyszerű váltást. További információ: [Parametrizálja a KQL Magic-lekérdezést a Pythonban](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Python-változó használata a KQL-lekérdezésben

Az adatszűréshez használhatja a lekérdezés Python-változó értékét:

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

### <a name="convert-query-results-to-pandas-dataframe"></a>Lekérdezési eredmények konvertálása pandák DataFrame

A KQL-lekérdezés eredményei a pandák DataFrame érhetők el. A legutóbbi lekérdezési eredmények változó `_kql_raw_result_` alapján érhetők el, és könnyen átalakíthatók a pandák DataFrame az eredmények a következőképpen:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Példa

Számos elemzési forgatókönyv esetén érdemes lehet olyan újrafelhasználható jegyzetfüzeteket létrehozni, amelyek sok lekérdezést tartalmaznak, és az eredményeket egy lekérdezésből az azt követő lekérdezésekben táplálják. Az alábbi példa a Python változót `statefilter` használja az adatszűréshez.

1. Futtasson egy lekérdezést, amely a legfelső 10 állapotot jeleníti meg maximálisan `DamageProperty`:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Futtasson egy lekérdezést a legfelső állapot kinyeréséhez és egy Python-változóra való beállításához:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Futtasson egy lekérdezést az `let` utasítás és a Python változó használatával:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Futtassa a Súgó parancsot:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Az összes elérhető konfigurációval `%config KQLmagic`kapcsolatos információ fogadása. A Kusto hibák, például a kapcsolódási problémák és a helytelen lekérdezések hibaelhárításához és rögzítéséhez használja a következőt`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>További lépések

A Súgó parancs futtatásával vizsgálja meg a következő minta-jegyzetfüzeteket, amelyek tartalmazzák az összes támogatott funkciót:
- [Ismerkedés a KQL Magic for Azure Adatkezelő](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Ismerkedés a KQL Magic szolgáltatással Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Ismerkedés a KQL Magic szolgáltatással Azure Monitor naplókhoz](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize a KQL Magic-lekérdezést a Pythonban](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [A KQL Magic Query diagram eredményének kiválasztása szín paletta](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
