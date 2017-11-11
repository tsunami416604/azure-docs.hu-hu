```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|maxPollingInterval|60000|A maximális ezredmásodpercben várólista lekérdezések között.| 
|visibilityTimeout|0|Egy üzenet feldolgozása közben az újrapróbálkozások közötti időközt sikertelen lesz.| 
|batchSize|16|Az üzenetsor-üzeneteket beolvasni és párhuzamos feldolgozását száma. A maximális érték a 32.| 
|maxDequeueCount|5|A rendszer hányszor próbálkozzon, egy üzenet feldolgozása előtt helyezze át a poison várólista száma.| 
|newBatchThreshold|batchSize/2|A küszöbérték, ahol az üzenetek új kötegelt beolvasott.| 
