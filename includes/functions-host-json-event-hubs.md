```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|maxBatchSize|64|A receive hurok érkezett események maximális száma.|
|prefetchCount|n/a|Az alapértelmezett PrefetchCount, amelyet az alapul szolgáló EventProcessorHost fog használni.| 
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpont létrehozása előtt feldolgozható kötegek esemény száma.| 
