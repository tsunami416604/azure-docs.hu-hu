A következő táblázat felsorolja a korlátokat, amely a alkalmazni az IoT Hub Device Provisioning Service-erőforrások:

| Erőforrás | Korlát |
| --- | --- |
| Azure-előfizetésenként legfeljebb Device Provisioning Service | 10 |
| Regisztrációk maximális száma | 500,000 |
| A regisztrációk maximális száma | 500,000 |
| Regisztrációs csoportok maximális száma | 100 |
| Hitelesítésszolgáltatók maximális száma | 25 |

> [!NOTE]
> Felveheti a kapcsolatot [Support](https://azure.microsoft.com/support/options/) az előfizetésében a példányok számának növeléséhez.

> [!NOTE]
> Felveheti a kapcsolatot [Support](https://azure.microsoft.com/support/options/) növelni a beléptetések és az eszközkiépítési szolgáltatás a regisztrációk számát.

A Device Provisioning Service szabályozza a kéréseket, amikor az alábbi kvóták átlépése:

| Szabályozás | Egységenkénti érték |
| --- | --- |
| Műveletek | 200-as/perc/szolgáltatás |
| Eszközregisztrációk | 200-as/perc/szolgáltatás |
| Eszköz lekérdezési művelet | 5/10 másodperc/eszköz |
