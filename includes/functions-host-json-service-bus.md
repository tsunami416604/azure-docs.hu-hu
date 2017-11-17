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
|maxConcurrentCalls|16|A visszahívás, amely az üzenet szivattyú kell kezdeményezni az egyidejű hívások maximális száma. Alapértelmezés szerint a Functions futtatókörnyezete dolgozza fel a több üzenetet párhuzamosan. Állítsa át tudja irányítani a futtatókörnyezet egyszerre csak egyetlen üzenetsor vagy témakör üzenet feldolgozásához, `maxConcurrentCalls` 1. | 
|prefetchCount|n/a|Az alapértelmezett PrefetchCount, amelyet az alapul szolgáló MessageReceiver fog használni.| 
|autoRenewTimeout|00:05:00|A maximális időtartamot, amelyen belül az üzenet zár megújításra kerül automatikusan.| 
