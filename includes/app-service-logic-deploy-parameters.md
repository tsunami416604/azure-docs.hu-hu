Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon nevű paraméterek szakaszban, amely tartalmazza az összes paraméter értékét tartalmazza.
Meg kell határozni egy paramétert ezeket az értékeket, amelyek a projekt telepít vagy telepít, hogy a környezet alapján változhatnak. Az értékeket, amelyeket a rendszer mindig ugyanaz maradjon paraméterek nem határoznak meg. Minden paraméter értéke szolgál a sablon határozza meg az erőforrásokat, vannak-e telepíteni. 

Ha paraméterek megadása a **Storageaccount_accounttype** mezőben adja meg, mely felhasználó értékekkel biztosíthat a telepítés során. Használja a **defaultValue** mezőben rendelhet értéket a paraméternek, ha nincs érték megadva üzembe helyezése során.

Azt ismerteti, egyes paramétereket a sablonban.

### <a name="logicappname"></a>logicAppName
A logikai alkalmazás létrehozása neve.

    "logicAppName": {
        "type": "string"
    }