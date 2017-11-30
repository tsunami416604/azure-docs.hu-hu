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
|maxOutstandingRequests|-1|Egy adott időpontban (-1 érték azt jelenti, hogy unbounded) vissza lesz tartva függőben lévő kérelmek maximális száma. A korlát magában foglalja a kérelmek várólistára kerülnek, de nincs elindítva, valamint minden folyamatban lévő végrehajtások végrehajtása. Ezt a határt keresztül bejövő minden kérelmet elutasították 429-es jelű "Túl elfoglalt" választ. Hívóknak adott válasz segítségével időalapú újrapróbálkozási stratégiát alkalmaz. Ez a beállítás vezérli csak queuing, amely akkor fordul elő, a feladat állomás végrehajtási elérési útban. Más várólisták, például az ASP.NET-kérelmek várólistájának nem érinti ez a beállítás. |
|maxConcurrentRequests|-1|A maximális száma párhuzamos (-1 érték azt jelenti, hogy unbounded) végrehajtott HTTP-funkciókat. Beállíthat például korlátozni, ha a HTTP-funkciók túl sok rendszererőforrásokat használhat, ha nagy a feldolgozási. Vagy ha a funkciók kimenő kéréseket a külső szolgáltatáshoz, e hívások kell sebessége korlátozott lehet.|
|dynamicThrottlesEnabled|hamis|A kérelem feldolgozása folyamat rendszeres időnként ellenőrzik a rendszerteljesítmény-számlálók okoz. Kapcsolatok száma, a szálak, a folyamatok, a memória és a cpu tartalmazzák. Ha a számlálók bármelyike (80 %-át) beépített küszöböt, kérelmek azért lettek elutasítva 429-es jelű "Túl elfoglalt" választ mindaddig, amíg a counter(s) normál szintek való visszatéréshez.|
