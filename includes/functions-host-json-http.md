```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|routePrefix|api-t|Az útválasztási előtagot, amely az összes útvonal vonatkozik. Üres karakterlánc segítségével távolítsa el az alapértelmezett előtag. |
|maxOutstandingRequests|-1|Egy adott időpontban tartott szálankénti függőben lévő kérelmek maximális száma. Ez a korlátozás kérelmek várólistára kerülnek, de nem kezdték végrehajtása, valamint bármely, a folyamat-végrehajtást tartalmaz. Minden olyan bejövő kérések át a korlátot 429 "Túlságosan elfoglalt" választ a rendszer elutasítja. Amely lehetővé teszi, hogy a hívók időalapú újrapróbálkozási stratégiák használatát tervezni, és vezérelheti a kérések maximális késéseket is segít. Ez csak vezérli, üzenetsor-kezelési, amely a gazdagép szkript végrehajtási útvonalát belül történik. Más várólisták, például az ASP.NET-kérelmek várólistája továbbra is lesz érvényben, és ez a beállítás nem befolyásolja. Az alapértelmezett érték a korlátlan streameken működő.|
|maxConcurrentRequests|-1|Http-függvények, amelyek végrehajtható párhuzamosan maximális számát. Ez lehetővé teszi a vezérlő egyidejűséget, amelyek segíthetnek az erőforrás-felhasználás kezelése. Előfordulhat például, hogy egy http-függvény, amely sok rendszererőforrást (memória / / processzorfoglalatok) használja úgy, hogy ha egyidejűség túl magas problémákat okoz. Vagy előfordulhat, hogy egy függvényt, amely lehetővé teszi a kimenő kérelmeket egy külső szolgáltatás, és ezeket a hívások sebessége korlátozott kell. Ezekben az esetekben alkalmazása egy késleltetési ide segítségével. Az alapértelmezett érték a korlátlan streameken működő.|
|dynamicThrottlesEnabled|false|Ha engedélyezve van, ez a beállítás megadja a kérelemfeldolgozási folyamatba, rendszeres időközönként ellenőrizze a rendszerteljesítményt számlálók, például a kapcsolatokat/szálak/folyamatok/memória/cpu/stb., és ha ezek a számlálók bármelyike egy beépített magas meghaladja a küszöbértéket (80 %), a kérelmek lesz-e. elutasított 429 "Túlságosan elfoglalt" választ, mindaddig, amíg a számláló térjen vissza a normális üzemi szint.|
