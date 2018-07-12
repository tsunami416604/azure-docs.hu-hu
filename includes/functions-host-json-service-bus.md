```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|maxConcurrentCalls|16|Egyidejű hívás, amely az üzenet szivattyú kell kezdeményezni a visszahívás maximális számát. Alapértelmezés szerint a Functions futtatókörnyezete dolgozza fel a több üzenetet egy időben. A közvetlen egyszerre csak egy üzenetsor vagy témakör üzenetet feldolgozni a futtatókörnyezetet, állítsa `maxConcurrentCalls` 1-re. | 
|prefetchCount|n/a|Az alapértelmezett PrefetchCount, amely az alapul szolgáló MessageReceiver használni fog.| 
|autoRenewTimeout|00:05:00|A maximális időtartamot, amelyen belül az üzenet zárolási újul meg automatikusan.| 
