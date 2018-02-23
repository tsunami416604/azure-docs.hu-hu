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
|batchSize|16|A Functions futtatókörnyezete egy időben kéri le, és párhuzamosan dolgozza fel a várólista-üzenetek száma. Ha a szám feldolgozott le a lekérdezi a `newBatchThreshold`, a futtatókörnyezet egy másik köteg lekérdezi, és megkezdi az üzenetek feldolgozását. Így a maximális száma párhuzamos éppen feldolgozott üzeneteinek másodpercenkénti függvény `batchSize` plus `newBatchThreshold`. Ezt a határt külön-külön minden várólista-eseményindítóval aktivált függvény vonatkozik. <br><br>Ha el szeretné kerülni a párhuzamos végrehajtás egy olyan sort a fogadott üzenetet, akkor megadhatja `batchSize` 1. Azonban ez a beállítás nem egyidejűségi, csak, feltéve, hogy a függvény app futó egyetlen virtuális gép (VM). A függvény alkalmazást több virtuális gépek méretezze át, ha egyes virtuális gépek futtathat egy példányát minden egyes várólista-eseményindítóval aktivált függvény.<br><br>A maximális `batchSize` 32. | 
|maxDequeueCount|5|A rendszer hányszor próbálkozzon, egy üzenet feldolgozása előtt helyezze át a poison várólista száma.| 
|newBatchThreshold|batchSize/2|Amikor egy időben feldolgozott üzenetek száma Ez a szám, a futtatókörnyezet egy másik köteg kéri le.| 



