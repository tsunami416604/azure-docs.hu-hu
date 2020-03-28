---
title: 'Oktatóanyag: Hozzon létre egy jegyzetfüzetet az Azure Cosmos DB-ben az adatok elemzéséhez és megjelenítéséhez'
description: 'Oktatóanyag: Ismerje meg, hogyan használhatja a beépített Jupyter-jegyzetfüzetek adatok importálását az Azure Cosmos DB-be, elemezheti az adatokat, és vizualizálhatja a kimenetet.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 45dd4e8dcfd74cdb5d96b935e239b9f4b5094a7c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73720929"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Oktatóanyag: Hozzon létre egy jegyzetfüzetet az Azure Cosmos DB-ben az adatok elemzéséhez és megjelenítéséhez

Ez a cikk ismerteti, hogyan használhatja a beépített Jupyter notebookok minta kiskereskedelmi adatok importálásához az Azure Cosmos DB.This article describes how to use built-in Jupyter notebooks to import sample retail data to Azure Cosmos DB. Látni fogja, hogyan használhatja az SQL és az Azure Cosmos DB magic parancsokat lekérdezések futtatásához, az adatok elemzéséhez és az eredmények megjelenítéséhez.

## <a name="prerequisites"></a>Előfeltételek

* [Jegyzetfüzetek támogatásának engedélyezése az Azure Cosmos-fiók létrehozása közben](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Az erőforrások létrehozása és adatok importálása
 
Ebben a szakaszban létre fogja hozni az Azure Cosmos-adatbázist, tárolót, és importálja a kiskereskedelmi adatokat a tárolóba.

1. Keresse meg az Azure Cosmos-fiókot, és nyissa meg az **Adatkezelőt.**

1. Nyissa meg a **Jegyzetfüzetek** lapot, jelölje be `…` a Saját jegyzetfüzetek elemet, és hozzon létre egy új **jegyzetfüzetet** . **New Notebook** Válassza ki a **Python 3-at** alapértelmezett kernelként.

   ![Új jegyzetfüzet létrehozása](./media/create-notebook-visualize-data/create-new-notebook.png)

1. Az új jegyzetfüzet létrehozása után átnevezheti a **VisualizeRetailData.ipynb-re.**

1. Ezután létrehoz egy "RetailDemo" nevű adatbázist és egy "WebsiteData" nevű tárolót a kiskereskedelmi adatok tárolására. A /CardID kapcsolót partíciókulcsként használhatja. Másolja és illessze be a következő kódot a jegyzetfüzet egy új cellájába, és futtassa azt:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Cella futtatásához `Shift + Enter` jelölje be a Vagy jelölje ki a cellát, és válassza az **Aktív cella futtatása** jelölőnégyzetet az adatkezelő navigációs sávján.

   ![Az aktív cella futtatása](./media/create-notebook-visualize-data/run-active-cell.png)

   Az adatbázis és a tároló a jelenlegi Azure Cosmos-fiókban jön létre. A tartály 400 RU/s-os kiépítésű. Az adatbázis és a tároló létrehozása után a következő kimenet jelenik meg. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Az **Adatok** lapot is frissítheti, és megtekintheti az újonnan létrehozott erőforrásokat:

   ![Az adatok lap frissítése az új tároló megtekintéséhez](media/create-notebook-visualize-data/refresh-data-tab.png)

1. Ezután importálja a kiskereskedelmi minta adatokat az Azure Cosmos tárolóba. A kiskereskedelmi adatokból származó cikk formátuma a következő:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Az oktatóanyag céljából a minta kiskereskedelmi adatok az Azure blob storage tárolja. Importálhatja az Azure Cosmos-tárolóba a következő kód beillesztésével egy új cellába. Az adatok sikeres importálását egy lekérdezés sel ellenőrizheti az elemek számának kiválasztásához.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Az előző lekérdezés futtatásakor a következő kimenetet adja vissza:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Adatok bekerülése dataframe-be

Mielőtt lekérdezéseket futtatna az adatok elemzéséhez, elolvashatja az adatokat a tárolóból a [Pandas DataFrame-be](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) elemzésre. Az adatok dataframe-be való beolvasásához használja a következő sql magic parancsot:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

További információkért tekintse meg a [beépített jegyzetfüzet parancsok és funkciók az Azure Cosmos DB cikkben.](use-notebook-features-and-commands.md) Futtatja a lekérdezést. `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c` Az eredményeket a rendszer egy Pandas DataFrame-be menti, amelynek neve df_cosmos. Illessze be a következő parancsot egy új jegyzetfüzetcellába, és futtassa azt:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

Egy új jegyzetfüzetcellában futtassa a következő kódot a kimenet első 10 elemének beolvasásához:

```python
# See a sample of the result
df_cosmos.head(10)
```

![Lekérdezés futtatása a 10 elem bekéséhez](./media/create-notebook-visualize-data/run-query-get-top10-items.png)

## <a name="run-queries-and-analyze-your-data"></a>Lekérdezések futtatása és az adatok elemzése

Ebben a szakaszban néhány lekérdezést futtat a beolvasott adatokon.

* **Lekérdezés1:** Futtasson egy csoportot lekérdezés szerint a DataFrame-en, hogy megkapja az egyes országok összes értékesítési bevételének összegét, és 5 cikket jelenítsen meg az eredményekből. Egy új jegyzetfüzetcellában futtassa a következő kódot:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   ![Összes értékesítési bevétel kibocsátása](./media/create-notebook-visualize-data/total-sales-revenue-output.png)

* **Lekérdezés2:** Az öt beszerzett elem listájának lehívásához nyisson meg egy új jegyzetfüzetcellát, és futtassa a következő kódot:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   ![Az öt beszerzett elem első](./media/create-notebook-visualize-data/top5-purchased-items.png)

## <a name="visualize-your-data"></a>Adatok vizualizációja  

1. Most, hogy rendelkezünk az Azure Cosmos-tárolóból származó bevételre vonatkozó adatainkkal, az Ön által kiválasztott vizualizációs könyvtárral vizualizálhatja az adatokat. Ebben az oktatóanyagban a Bokeh könyvtárat használjuk. Nyisson meg egy új jegyzetfüzetcellát, és futtassa a következő kódot a Bokeh könyvtár telepítéséhez. Miután az összes követelmény teljesült, a könyvtár lesz telepítve.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Ezután készítse elő az adatok térképen való ábrázolására. Csatlakozzon az Azure Cosmos DB-ben tárolt adatokhoz az Azure Blob storage-ban található országadatokkal, és alakítsa át az eredményt GeoJSON formátumra. Másolja a következő kódot egy új jegyzetfüzetcellába, és futtassa azt.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries dataframe with our data in Azure Cosmos DB, joining on country code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. A különböző országok értékesítési bevételeinek megjelenítése a világtérképen a következő kód futtatásával egy új jegyzetfüzetcellában:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   A kimenet különböző színekkel jeleníti meg a világtérképet. A világosabbra sötétebb színek a legmagasabb bevételt, a legalacsonyabb bevételt eredményező országokat jelölik.

   ![Országok bevételi térképe imitomegjelenítése](./media/create-notebook-visualize-data/countries-revenue-map-visualization.png)

1. Lássunk egy másik adatvizualizációs esetet. A WebsiteData tároló ban vannak olyan felhasználók, akik megtekintettek egy elemet, hozzáadták őket a kosárhoz, és megvásárolták az elemet. Ábrázoljuk a megvásárolt cikkek konverziós arányát. Futtassa a következő kódot egy új cellában az egyes elemek konverziós arányának megjelenítéséhez:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   ![Beszerzési konverziós arány megjelenítése](./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png)

## <a name="next-steps"></a>További lépések

* A jegyzetfüzetparancsokról az [Azure Cosmos DB-cikk beépített jegyzetfüzetparancsainak és -szolgáltatásainak használata](use-notebook-features-and-commands.md) című témakörben olvashat bővebben.
