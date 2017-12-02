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
|routePrefix|api-t|Az útvonal előtagja, amely az összes útvonal vonatkozik. Üres karakterlánc segítségével távolítsa el az alapértelmezett előtag. |
|maxOutstandingRequests|-1|Egy adott időpontban a tranzakció kimenetelére várva függőben lévő kérelmek maximális száma. Ezt a határt magában foglalja a kérelmek várólistára kerülnek, de nincs elindítva, valamint bármely, a folyamatban lévő végrehajtások végrehajtása. Ezt a határt keresztül bejövő minden kérelmet elutasították 429-es jelű "Túl elfoglalt" választ. Amely hívók időalapú újrapróbálkozási stratégiát alkalmaz, és segítséget nyújt annak vezérlésére kérések maximális késések fordulnak elő. Ez csak szabályozza, a parancsfájl állomás végrehajtási elérési útján belül előforduló queuing. Például az ASP.NET-kérelmek várólistájának más várólisták marad érvényben, és ez a beállítás nincs hatással. Az alapértelmezett érték unbounded.|
|maxConcurrentRequests|-1|A http-funkciók párhuzamosan végrehajtott maximális száma. Ez lehetővé teszi a vezérlő egyidejűségi, amelyek segíthetnek az erőforrás-használat kezelése. Például lehetséges, hogy egy http-függvény, amely nagy mennyiségű rendszer-erőforrások (memória / / processzorfoglalatok) használ, úgy, hogy ha túl nagy a feldolgozási problémákat okoz. Előfordulhat, hogy egy függvény harmadik fél szolgáltatás kimenő kérelmek, illetve ezek hívások kell sebessége korlátozott. Ezekben az esetekben a késleltetési itt alkalmazása segítségével. Az alapértelmezett érték unbounded.|
|dynamicThrottlesEnabled|hamis|Ha engedélyezve van, ez a beállítás megadja a kérelemfeldolgozási folyamatba, hogy rendszeres időközönként ellenőrizze a rendszerteljesítményt például kapcsolatok szálak/folyamatok/memória/cpu/stb teljesítményszámlálók, és ha ezek a számlálók bármelyike (80 %-át), beépített magas küszöböt igényel lesz-e. elutasított 429-es jelű "Túl elfoglalt" választ, amíg a counter(s) térjen vissza a normál szintek.|
