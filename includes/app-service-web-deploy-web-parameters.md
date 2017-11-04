Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon nevű paraméterek szakaszban, amely tartalmazza az összes paraméter értékét tartalmazza.
Meg kell határozni egy paramétert ezeket az értékeket, amelyek a projekt telepít vagy telepít, hogy a környezet alapján változhatnak. Az értékeket, amelyeket a rendszer mindig ugyanaz maradjon paraméterek nem határoznak meg. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva. 

Ha paraméterek megadása a **Storageaccount_accounttype** mezőben adja meg, mely felhasználó értékekkel biztosíthat a telepítés során. Használja a **defaultValue** mezőben rendelhet értéket a paraméternek, ha nincs érték megadva üzembe helyezése során.

Azt ismerteti, egyes paramétereket a sablonban.

### <a name="sitename"></a>SiteName
A létrehozni kívánt webes alkalmazás neve.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
A webalkalmazás üzemeltetéséhez használt App Service-csomag neve.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>Termékváltozat
Ez a tarifacsomag a üzemeltetési terv.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

A sablon határozza meg az értékeket, amelyeket ez a paraméter engedélyezett, és hozzárendeli az alapértelmezett értéket (S1), ha nincs érték megadva.

### <a name="workersize"></a>workerSize
A példány mérete a üzemeltetési terv (kis, közepes vagy nagy).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

A sablon határozza meg az értékeket, amelyeknél engedélyezve van ez a paraméter (0, 1 vagy 2), és hozzárendeli az alapértelmezett értéket (0), ha nincs érték megadva. Az értékek megegyeznek kis, közepes és nagy méretű.

