---
title: Adatok elemzése az Azure Data Explorerben egy Jupyter-jegyzetfüzet használatával
description: Ez a témakör bemutatja, hogyan elemezheti az adatokat az Azure Data Explorer egy Jupyter notebook és a Kqlmagic bővítmény használatával.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 83902ea5a3e73603311a0c469126ed603d0ebd16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064869"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Jupyter-jegyzetfüzet és Kqlmagic-bővítmény használata az Azure Data Explorer adatainak elemzéséhez

A Jupyter Notebook egy nyílt forráskódú webes alkalmazás, amely lehetővé teszi az élő kódot, egyenleteket, vizualizációkat és narratív szöveget tartalmazó dokumentumok létrehozását és megosztását. A használat magában foglalja az adatok tisztítását és átalakítását, a numerikus szimulációt, a statisztikai modellezést, az adatmegjelenítést és a gépi tanulást.
[A Jupyter Notebook](https://jupyter.org/) támogatja a mágikus funkciókat, amelyek további parancsok támogatásával bővítik a kernel képességeit. A KQL mágia egy olyan parancs, amely kiterjeszti a Python kernel képességeit a Jupyter Notebook-ban, így a Kusto nyelvi lekérdezéseket natív módon futtathatja. Könnyedén kombinálhatja a Python és a Kusto lekérdezési nyelvét az `render` adatok lekérdezéséhez és megjelenítéséhez a parancsokba integrált gazdag Plot.ly könyvtár használatával. A lekérdezések futtatásához az adatforrások támogatottak. Ezek az adatforrások közé tartozik az Azure Data Explorer, egy gyors és jól méretezhető adatfeltárási szolgáltatás napló- és telemetriai adatok, valamint az Azure Monitor naplók és az Application Insights. A KQL magic az Azure Notebooks, a Jupyter Lab és a Visual Studio Code Jupyter-bővítményekkel is működik.

## <a name="prerequisites"></a>Előfeltételek

- Szervezeti e-mail fiók, amely az Azure Active Directory (AAD) tagja.
- A Jupyter notebook telepítve van a helyi számítógépen, vagy használja az Azure Notebookokat, és klónozza a minta [Azure Notebookot](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>A KQL varázskönyvtárának telepítése

1. Telepítse a KQL magic-et:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Az Azure Notebooks használata esetén ez a lépés nem szükséges.

1. KQL mágia betöltése:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Módosítsa a Kernel verziót Python 3.6-ra a Kernel > Change Kernel > Python 3.6 elemre kattintva
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Csatlakozás az Azure Data Explorer súgófürtjéhez

A következő paranccsal csatlakozhat a *súgófürtön* található *Minták* adatbázishoz. A nem Microsoft AAD-felhasználók számára `Microsoft.com` cserélje le a bérlő nevét az AAD-bérlőre.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Lekérdezés és megjelenítés

Adatok lekérdezése a [renderelési operátor](/azure/kusto/query/renderoperator) használatával, és az adatok megjelenítése a ploy.ly függvénytár használatával. Ez a lekérdezés és vizualizáció natív KQL-t használó integrált élményt nyújt. A Kqlmagic a legtöbb `timepivot` `pivotchart`diagramot `ladderchart`támogatja, kivéve a , és a. A renderelés a `kind` `ysplit`( `accumulate`a ) és a kivételével az összes attribútummal támogatott. 

### <a name="query-and-render-piechart"></a>Kördiagram lekérdezése és renderelése

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Timechart lekérdezése és renderelése

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Ezek a diagramok interaktívak. Jelöljön ki egy időtartományt egy adott időpontra való nagyításhoz.

### <a name="customize-the-chart-colors"></a>A diagram színeinek testreszabása

Ha nem tetszik az alapértelmezett színpaletta, szabja testre a diagramokat a palettabeállítások kal. A rendelkezésre álló paletták itt találhatók: [Válasszon színpalettát a KQL mágikus lekérdezési diagram eredményéhez](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Paletták listájához:

    ```python
    %kql --palettes -popup_window
    ```

1. Jelölje `cool` ki a színpalettát, és jelenítse meg újra a lekérdezést:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Lekérdezés paraméterezése pythonnal

A KQL mágia lehetővé teszi a Kusto lekérdezési nyelv és a Python közötti egyszerű cserét. További információ: [A KQL-mágikus lekérdezés paraméterezése pythonnal](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Python-változó használata a KQL-lekérdezésben

A lekérdezésben egy Python változó értékét használhatja az adatok szűrésére:

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

### <a name="convert-query-results-to-pandas-dataframe"></a>Lekérdezéseredmények konvertálása Pandas DataFrame rendszerré

A KQL-lekérdezés eredményeit a Pandas DataFrame-ben érheti el. Érje el az utolsó `_kql_raw_result_` végrehajtott lekérdezési eredményeket változók szerint, és egyszerűen konvertálja az eredményeket Pandas DataFrame-re az alábbiak szerint:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Példa

Számos elemzési forgatókönyv esetén érdemes lehet újrafelhasználható jegyzetfüzeteket létrehozni, amelyek sok lekérdezést tartalmaznak, és egy lekérdezés eredményeit egy későbbi lekérdezésekbe táplálják. Az alábbi példa a `statefilter` Python változó t használja az adatok szűréséhez.

1. Lekérdezés futtatása a legfeljebb `DamageProperty`10 állapot megtekintéséhez:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Futtasson egy lekérdezést a legfelső állapot kibontásához, és állítsa be egy Python-változóba:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Futtasson lekérdezést az utasítás és a `let` Python-változó használatával:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Futtassa a súgóparancsot:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Az összes rendelkezésre álló `%config Kqlmagic`konfigurációval kapcsolatos információ fogadásához használja a használatát. A Kusto-hibák, például a csatlakozási problémák és a helytelen lekérdezések elhárításához és rögzítéséhez használja a`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>További lépések

A súgóparancs futtatásával fedezze fel a következő mintajegyzetfüzeteket, amelyek tartalmazzák az összes támogatott szolgáltatást:
- [Ismerkedés a KQL-varázslattal az Azure Data Explorerben](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Ismerkedés a KQL magic for Application Insights alkalmazáselemzési adataival](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Ismerkedés a KQL-varázslattal az Azure Monitor-naplókhoz](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize a KQL mágikus lekérdezés Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Válasszon színpalettát a KQL mágikus lekérdezési diagram eredményéhez](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
